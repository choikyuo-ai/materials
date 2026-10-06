---
theme: ../theme
title: "L11: Guardrails: Errors, Retries and Approval Gates"
info: |
  EECS 498 AASE — Lecture 11
  Applied Agentic Software Engineering, UMich Fall 2026
layout: title
class: text-left
transition: fade
mdc: true
highlighter: shiki
---

# Guardrails: Errors, Retries and Approval Gates

## Lecture 11 · Oct 6, 2026

<!--
Direction: About 1 minute.

Good afternoon. The Apply build is due tonight at 11:59.

Today is about guardrails. A guardrail is the code around the model that decides two things: what happens when something fails, and what happens when the model asks to do something risky.
-->

---
layout: default
---

<div class="label">One turn, three places to check</div>

# Where guardrails go

<img src="./assets/diagrams/d1-loop.svg" class="mx-auto mt-4" style="max-height:19rem" />

<div class="caption mt-2">A guardrail is a check in your code. The model cannot talk its way past it.</div>

<!--
Direction: About 3 minutes.

This is the whole lecture on one slide. One turn of the loop you built. The model call. It asks for a tool. Someone approves. The tool runs. The result goes back to the model.

A guardrail is a check in your code at one of those points. It runs on every turn, whether or not the model cooperates. A line in the system prompt is not a guardrail, because the model can ignore it.

Three parts. Part 1 is failure: the model call fails, or the tool fails, and those two need opposite handling. Part 2 is approval when you are the one answering. Part 3 is approval gates, when something else answers for you: rules in code, a second model, or a small typed classifier called Jev.
-->

---
layout: section
---

# Part 1: When something fails

## Errors and retries

<!--
Direction: About 25 minutes for Part 1.

Part 1. Something fails. First we sort the failure, then we watch what Codex does against a dead endpoint, measured this morning, then we write a retry loop you can put in your own agent.
-->

---
layout: default
---

<div class="label">Sort it before you handle it</div>

# Three kinds of failure

<img src="./assets/diagrams/d3-triage.svg" class="mx-auto mt-2" style="max-height:21rem" />

<!--
Direction: About 4 minutes.

Something failed. Before anything else, ask which of three things failed, because each one gets a different answer.

The model call. A 429 rate limit, a 502 from the gateway, a timeout. No reply came back, so nothing happened, and sending the same request again is safe. That is the only place an automatic retry belongs.

Your request. A 400, a 401 for a bad key, a conversation longer than the context window. The endpoint understood you and said no. Send the same bytes again and you get the same no. Retrying only burns time. Stop and tell the user what to fix.

A tool. Exit status 1, no such file. That is not your harness failing. It is information for the model, so it goes back as the tool's result, and the model decides what to do next. L09's consecutive-errors limit is the backstop if it never recovers.

Ask: The Toolkit's occasional 502 at Hackathon 1. Which box was that? Take one answer.

The model call. Wait, and send it again.
-->

---
layout: default
---

<div class="label">Measured Oct 6 · a model endpoint that answers every request with 502</div>

# One dead endpoint, 30 requests

<img src="./assets/diagrams/d2-retries.svg" class="mx-auto mt-2" style="max-height:19rem" />

<div class="text-xl mt-2 text-center">Codex 0.160.0 · <b>6 tries × 5 sends = 30</b> · then exit 1</div>

<!--
Direction: About 5 minutes. Measured on Oct 6 with Codex 0.160.0 pointed at a local server that answers every request with 502 Bad Gateway. The raw logs are in assets/live/captures. The inner five is read from the timing, not printed by Codex. The settings are request_max_retries and stream_max_retries, per model provider in config.toml. The 401 behavior is from the rust-v0.160.0 source: codex-api/src/api_bridge.rs maps a 401 to UnexpectedStatus, and protocol/src/error.rs retry_delay treats that as retryable. The inner loop, codex-client/src/retry.rs, retries only 429, 5xx and network errors, so a 401 is six single requests.

This morning we pointed Codex at a model endpoint that is down, and logged every request that reached it. Each blue tick is one request, and the numbers under the bursts are Codex's tries.

Codex sent 30 requests in 24 seconds, then gave up and exited with status 1.

Nobody configured 30. It is two retry loops, one inside the other. Codex prints the outer loop: Reconnecting, 1 of 5, up to 5 of 5. That is six tries. Inside each try, the request is sent up to five times, and the waits double: 0.2, 0.4, 0.9, 1.5 seconds. The waits between tries double too. Six times five is thirty.

That is the thing to take from this slide. Every layer that retries multiplies the layer under it. Your agent probably has the same two layers without knowing it, which is the slide after next.

The exit status matters as much as the count. It gave up with status 1, so a script or CI job running codex exec can tell a failed run from a good one. When your agent gives up, it exits non-zero and says why.

We also sent it a 400. It sent that exactly once and stopped. That is the sorting from the last slide, done right.

A 401, a bad key, goes the other way. Codex runs it through the same five reconnects, although nothing changes between tries. By the last slide's sort that is your request, and retrying it only burns time. A shipped harness gets one of the two wrong.
-->

---
layout: default
---

<div class="label">Live · two terminals</div>

# Watch Codex give up

<div class="grid grid-cols-2 gap-8 mt-6">
<div>

**Left: a model that always fails**

```text
$ python3 fake-endpoint.py 8799 502
```

- Bursts of five, the gaps growing

</div>
<div>

**Right: real Codex, pointed at it**

```text
$ ./codex-502.sh 8799
```

- `Reconnecting... 1/5` up to `5/5`
- Last line: `codex exit status: 1`

</div>
</div>

<div class="caption mt-6">About 25 seconds. Both scripts are in assets/live.</div>

<!--
Direction: About 3 minutes. Run from lectures/L11/assets/live. Start the left pane first. It prints "listening on 127.0.0.1:8799, answering 502" and then waits. Nothing more appears until Codex runs in the right pane. codex-502.sh needs codex on PATH and uses a throwaway CODEX_HOME, so the presenter's config and login are not touched. Run it once before class. It took 25 seconds on Oct 6. Fallback: assets/live/captures/codex-502-endpoint.txt and codex-502-codex.txt.

Two panes. On the left, a fake model endpoint that answers every request with 502 and prints when each one arrived. On the right, real Codex, configured to use it as its model provider.

Watch the left pane. Requests come in bursts of five, and the gaps inside a burst grow. Watch the right pane count its reconnects up to five. About 25 seconds in it gives up, and the last line is the exit status.
-->

---
layout: default
---

<div class="label">For your agent</div>

# A retry loop you own

```python
RETRY = {429, 500, 502, 503, 504}

def call_model(send, request, deadline_s=60):
    start, delay = time.monotonic(), 0.5
    while True:
        try:
            return send(request)               # client built with max_retries=0
        except ModelHTTPError as e:
            if e.status not in RETRY:
                raise                          # 400, 401, too long: stop
            wait = e.retry_after or delay * random.uniform(0.5, 1.5)
            if time.monotonic() - start + wait > deadline_s:
                raise                          # give up, and exit non-zero
            time.sleep(wait)
            delay = min(delay * 2, 16)
```

<!--
Direction: About 4 minutes. A sketch, not a library. ModelHTTPError stands for whatever your client raises, carrying the status and any Retry-After value.

Five decisions in fifteen lines.

Retry only the statuses where nothing happened. Everything else raises at once.

Back off by doubling, with jitter. The jitter is the random factor. If thirty agents in this room hit the same 429 at the same moment and all wait exactly one second, they all come back at the same moment and get another 429.

Use Retry-After when the endpoint sends it. On a 429 the server is telling you when to come back.

A deadline for the whole call, not a count of tries. Five tries means something very different at a quarter second and at 16 seconds, and your user feels seconds, not tries.

And one loop. That is the comment on the send line. The OpenAI Python SDK retries twice by default, so wrap its client in your own loop and every one of your tries is three requests. Build the client with max_retries set to zero and own the loop, or count both.

When it finally raises, the conversation is untouched. That is L07's failed-stream rule from the other side: nothing half-finished goes into the history, and the user can try again.
-->

---
layout: default
---

<div class="label">The tool side</div>

# A timeout is not a no

```text
step 6   run_command   taskr add "buy milk"
         killed by the harness after 30 s
```

- Did it save? The harness cannot know
- Retry it, and there may be two
- Return: "timed out after 30 s. It may have run."

<div class="caption mt-6">Retry a call only if running it twice is the same as running it once.</div>

<!--
Direction: About 3 minutes.

Now the tool side. Your harness kills a command after 30 seconds. Did taskr add save the task before it was killed? You do not know. It may have written ~/.taskr.json and then hung.

If your harness retries tool calls on its own, the user may now have buy milk twice. A model call that failed did nothing, so it is safe to send again. A tool call that timed out may have done all of it.

The word is idempotent: running it twice is the same as running it once. A read is idempotent. taskr list is. taskr add is not, and neither is git push, sending an email or charging a card.

So the harness never retries a tool by itself. It returns the timeout as the result, says the command may have run, and the model can check with taskr list before doing it again.

Ask: Name one command your agent ran this week that is not safe to run twice.
-->

---
layout: section
---

# Part 2: What you approve

## When the agent asks you

<!--
Direction: About 10 minutes for Part 2.

Part 2. When the agent asks you. What the person at the approval prompt can and cannot judge, and how Codex and Aider ask.
-->

---
layout: two-col
---

## An edit

```text
write_file(path, content)
```

The change is in the call. You read a diff, and git can put it back.

::right::

## A command

```text
run_command(command)
```

The call is a program. You see what runs, not what it touches.

<div class="caption mt-10">The same y/n prompt, two different jobs.</div>

<!--
Direction: About 2 minutes.

An approval prompt asks you one of two things.

Approve an edit, and the call carries the change. A diff is something you can read and judge, and git can put it back.

Approve a command, and the call carries a program. The text tells you what runs, not what it touches. git does not cover a shell command. rm on a file in your home directory, pip install, curl piped into sh: nothing in your repository records any of it.

Everything Codex asked you this week was one of these two.
-->

---
layout: default
---

<div class="label">Three things a command prompt hides</div>

# What the prompt does not show

| You see | You do not see |
|---|---|
| "Clean up the test state" | It was written by the model that wrote the command |
| `python -c "...main(['add', ...])"` | It saves to the user's `~/.taskr.json` |
| `pytest`, at step 12 | `tests/conftest.py`, written at step 11 |

<div class="caption mt-6">Approve the command in the state it runs in. Never the explanation.</div>

<!--
Direction: About 4 minutes. Rows 2 and 3 are from L09's rehearsal runs 2 and 3, Sep 29.

Three things you cannot judge from the prompt.

The model's stated reason. Codex usually writes a line before it asks. That line came from the same model that wrote the command. It is not evidence that the command does what the line says.

A test with a side effect, from L09's rehearsal runs. It looks like a test, and it is one. It also runs the real add, which saves to the user's task list. Both runs left two junk tasks in it, and the rehearsal approved every one of those commands, because nothing in their text mentions a home directory.

A command that points at a file. pytest runs every conftest.py in the repository. If step 11 wrote one, approving pytest at step 12 approves whatever that file says. make and npm test are the same.

So the unit you approve is a command in a state: the text, plus everything written since you last read the repository.

Ask: Hands up if you approved a python -c or a test run this week without knowing what it would write.
-->

---
layout: default
---

<div class="label">Codex 0.160.0 · Aider 5dc9490b</div>

# Codex and Aider ask differently

<div class="grid grid-cols-2 gap-8 mt-6">
<div>

**Codex: a sandbox, then a prompt**

- `--sandbox`: `read-only`, `workspace-write`, `danger-full-access`
- `--ask-for-approval`: `on-request` or `never`
- Only what leaves the sandbox asks

</div>
<div>

**Aider: a prompt only**

- `(Y)es/(N)o/(D)on't ask again [Yes]`
- Enter means yes
- `--yes-always`: shell commands get *no*
- "Don't ask again" means no

</div>
</div>

<!--
Direction: About 4 minutes. Codex flags from codex --help on 0.160.0, the version Lab03 installed. Aider line numbers at 5dc9490b: confirm_ask at aider/io.py:807, the --yes-always answer at :867, never_prompts checked at :823 and added at :903.

Codex splits the question in two. The sandbox limits what a command can touch: read only, writes inside the workspace, or everything. The approval policy says who is asked when a command needs to leave the sandbox. With on-request, you are. With never, nobody is, and the failure goes straight back to the model. codex doctor with an empty config reports approval on request, with the filesystem and network restricted. Lab03's profile sets nothing about approval, so that default is what you have been running.

Aider has no sandbox, only the prompt, and three details most people get wrong. The default is yes, so Enter runs the command. --yes-always does not mean yes to everything. Shell commands are asked with an explicit yes required, and with --yes-always on, they get no. That is line 867: someone decided that unattended Aider should not run commands. And "Don't ask again" is not an allow rule. It stores that exact command and answers no, now and every later time in the session. It is a deny rule written by a tired person, and it is gone when Aider exits.

Codex has one more flag, --approve-for-me. It sends these prompts to a reviewer agent instead of you. That is Part 3.
-->

---
layout: default
---

<div class="label">Chat break</div>

# What you approved without reading

Pick one thing Codex or Claude Code asked you to approve this week that you approved without reading.

What would a program have needed to know to answer it for you?

<!--
OK, Zoom, we're on break.

Direction: About 5 minutes, on paper or with a neighbor. If you have Codex's session history, you can look. Walk the room and collect two or three to read back, no name attached. Listen for answers that name something outside the command text: what a file contains, what the user asked for, whether it can be undone.
-->

---
layout: section
---

# Part 3: Approval gates

## When something else answers

<!--
OK, Zoom, we're back.

Direction: About 27 minutes for Part 3.

One word first. The phase gate at the end of Analyze is a graded test. An approval gate is code that answers the approval prompt so a person does not have to. Today is only the second.
-->

---
layout: default
---

<div class="label">Why approval gates exist</div>

# Most prompts get a yes

<div class="grid grid-cols-3 gap-8 mt-10">
  <div><div class="font-sans font-semibold" style="font-size:3.2rem;line-height:1;color:#1A1A1A">93%</div>
       <div class="text-lg mt-2">of Claude Code prompts, approved</div></div>
  <div><div class="font-sans font-semibold" style="font-size:3.2rem;line-height:1;color:#1A1A1A">720</div>
       <div class="text-lg mt-2">of 10,000 Codex actions left the sandbox</div></div>
  <div><div class="font-sans font-semibold" style="font-size:3.2rem;line-height:1;color:#1A1A1A">7</div>
       <div class="text-lg mt-2">of those 720, denied by a reviewer</div></div>
</div>

<div class="caption mt-10">A prompt that is nearly always yes stops being read.</div>

<!--
Direction: About 3 minutes. Read back two or three break answers first, no names.

Anthropic measured that Claude Code users approve 93 percent of permission prompts. That is from their engineering post on auto mode, March 25, 2026.

OpenAI's post on Codex auto-review, April 30, 2026, gives an internal snapshot of 10,000 actions. 9,280 ran inside the sandbox and never asked anyone. 720 needed approval. Their reviewer approved 713 and denied 7. Of the 7, four went on by a safer route and three stopped to ask the user.

And this is what that does to people. OpenAI found a sizable minority of internal users had an allow rule for every command starting with python. By step 30 of a session the prompt is a keypress, not a decision. Something else has to answer most of these.
-->

---
layout: default
---

<div class="label">Three things can answer</div>

# Three kinds of approval gate

| | Rules in code | Reviewer model | Typed classifier |
|---|---|---|---|
| Judges | The text | Intent and effect | Intent and effect |
| Returns | Match or not | A decision, a reason | Probabilities |
| Per call | Free | A model call | 70 to 500 ms, vendor |
| Example | Prefix rules | Codex auto-review | Jev |

<!--
Direction: About 2 minutes.

Rules in code are what L09 promised: match the requested call, deny wins, anything unmatched asks. Deterministic, free, and you can read them. They match the text, so an allow rule for pytest fires whatever conftest.py says.

A reviewer model is a second LLM call that reads the conversation and the proposed action and decides. It can judge intent and effect, it gives the agent a reason, and it costs a model call on every action it sees.

A typed classifier is the newest option. Jev, from TypeSafe. You ask it typed questions and it returns probabilities, not text.

No shipped system uses one of these alone. Code goes first, and a model sees only what code could not decide.
-->

---
layout: default
---

<div class="label">Two shipped reviewer models</div>

# Auto mode and auto-review

| | Claude Code auto mode | Codex auto-review |
|---|---|---|
| What reaches it | Past the allowlist and in-repo edits | Only what leaves the sandbox |
| Reviewer | Sonnet 4.6, two stages | GPT-5.4 Thinking, low effort |
| A denial | A tool result, with the reason | A tool result, with the reason |
| Stops the run | 3 denials in a row | 3 denials in a row |

<!--
Direction: About 3 minutes. Sources: Anthropic engineering, "How we built Claude Code auto mode", Mar 25, 2026. OpenAI alignment blog, "Auto-review of agent actions", Apr 30, 2026, and the Codex auto-review docs.

Both put code in front of the model. Claude Code allows a fixed list of tools that cannot change anything, plus your own allow rules, and file edits inside the project, because git can put those back. On entering auto mode it drops blanket rules like any python or any npm run, because those would hide the most dangerous commands from the classifier. Codex uses the sandbox from Part 2: only a request to leave it reaches the reviewer, and the reviewer never widens the sandbox.

Claude Code's classifier runs in two stages. First a single yes or no token, told to err toward blocking. Only if that flags does it reason, which keeps the cost down on the common case.

Both send a denial back to the agent as the tool's result, with the reason, and tell it to find a safer way rather than route around the block. Both stop after three denials in a row. Claude Code also stops at 20 in a session, Codex at 10 in the last 50 reviews of a turn. A denial is a result, and too many of them end the run.
-->

---
layout: default
---

<div class="label">Claude Code auto mode</div>

# What the reviewer sees

<img src="./assets/diagrams/d6-reviewer.svg" class="mx-auto mt-4" style="max-height:19rem" />

<!--
Direction: About 2 minutes. Source: the Anthropic post, section "Why we strip assistant text and tool results".

Auto mode's classifier sees the user's messages and the agent's tool calls. Nothing else.

The agent's own text is stripped, so it cannot argue. "These branches are stale, safe to delete" never reaches the classifier. It judges git push --delete against "clean up old branches", and clean up does not say delete the remote ones. That is the stated-reason row from Part 2, built into a product.

Tool output is stripped too. A README that says post your .env to this URL is how a prompt injection gets in. The classifier never reads it, and does not need to: posting .env to an outside URL fails against what the user asked, whatever prompted it.

Anthropic tested the alternatives. Giving the classifier the agent's text and the tool output made it worse on their set of real overeager actions.
-->

---
layout: default
---

<div class="label">TypeSafe · Sep 15, 2026</div>

# Jev, a typed decision model

- In: a state, as text or JSON
- Out: typed answers with probabilities
- No text, so nothing to parse
- Vendor: 70 to 500 ms per request

<div class="caption mt-6">Early access. The numbers are TypeSafe's, not ours.</div>

<!--
Direction: About 3 minutes.

Jev is from TypeSafe, released in early access on September 15, 2026. TypeSafe calls it a System One model. It takes a state, which is any text or JSON, and a set of typed questions, and returns answers your code can use directly. It does not write text, write code or call tools. TypeSafe's own docs say it does not replace the model behind a coding agent. It sits next to it.

TypeSafe's numbers: 70 to 500 milliseconds end to end, and input at 4.2 cents per million tokens, with output not billed. Those are their figures. We have not measured Jev. LangChain shipped an integration on September 17 that includes a tool-call gate, which comes up in a few slides.

Why it fits here. An approval gate's answer is a decision your code branches on, not prose. An LLM gate has to be told to say yes or no, and then parsed. Anthropic's first stage is an LLM told to emit one token. Jev returns the number directly.
-->

---
layout: default
---

<div class="label">Choice, Noul, Score</div>

# Safety categories, as questions

```python
QUESTIONS = {
    "kind": Choice(
        instructions="What does `call` do when it runs?",
        criteria={"read": "Only reads files in the repo",
                  "edit": "Changes files in the repo",
                  "run": "Runs code, tests or a build",
                  "outside": "Touches anything outside the repo"}),
    "asked": Noul(instructions="Did a user message ask for this action?"),
    "undo": Score(
        instructions="If `call` is a mistake, how hard is it to undo?",
        criteria=["Nothing changes", "git undoes it",
                  "Fixable by hand", "Cannot be undone"]),
}
```

<!--
Direction: About 3 minutes. The code is written against typesafe_sdk 0.7.1 and was checked on Oct 6 against a local stand-in for the API, which checks the request shape and nothing else. No Jev output is shown because none was measured.

Three question types, one of each, all in one request.

A Choice picks one option from a set, with a probability for each option and a confidence. A Noul is a yes or no question, answered as the probability of yes. A Score places the state on levels you write, numbered from zero, and the answer can land between two levels.

These are the questions you ask yourself at the prompt without saying them aloud. What kind of action is this? Did anyone ask for it? How bad is it if it is wrong? Written down, they are safety categories.

And they are plain English that you wrote and can read. The policy lives where you can review it and change it.
-->

---
layout: default
---

<div class="label">Code first, then Jev, then a person</div>

# An approval gate in code and Jev

<img src="./assets/diagrams/d4-gate.svg" class="mx-auto mt-2" style="max-height:20rem" />

<!--
Direction: About 3 minutes.

Here is the combination as a picture.

Code goes first, for everything you can decide from the text and must never get wrong. A path outside the repository is denied. A read inside it is allowed. Nobody, person or model, is asked about either.

The rest goes to Jev with the three questions. Jev answers with probabilities, and your thresholds, in code, turn them into one of three outcomes. The middle outcome is a person.

Why both. Code alone can only match text, so it cannot tell whether the user asked for something. Jev alone is a model, and sometimes it will be wrong about something code could have decided for certain. Rules take the certain cases, Jev the judgment calls, and the person the ones nobody is sure about.
-->

---
layout: default
---

<div class="label">The same gate, in Python</div>

# Thresholds are code

```python
def gate(call, user_msgs):
    if outside_repo(call) or denied_by_rule(call):
        return "deny"                      # code: nobody is asked
    a = jev.system_one(state={"user": user_msgs, "call": call},
                       questions=QUESTIONS).answers
    undo, asked, kind = a["undo"].score, a["asked"].noul, a["kind"]
    if undo >= 2 and asked < 0.5:
        return "deny"                      # hard to undo, not asked for
    if kind.choice == "read" and kind.confidence > 0.9:
        return "allow"
    if undo < 1 and asked > 0.9:
        return "allow"
    return "ask"                           # the middle band
```

<!--
Direction: About 3 minutes. A teaching sketch, checked on Oct 6 against typesafe_sdk 0.7.1 and a local stand-in for the API. Whether Jev is allowed in the Analyze build is the spec's call, not this lecture's. Do not promise it.

The picture as code. Rules first. Then one Jev request with the three questions. Then thresholds: deny what is hard to undo and nobody asked for, allow confident reads and cheap actions the user asked for, and send everything else to a person.

Walk one call from Part 2 through it. The python -c test that writes to the user's task list. Nothing in its text leaves the repository, so no rule fires. Jev can catch it only if the state says what main with add writes. Jev sees what you put in the state, like any reviewer.

Two things this does not give you. A reason: Jev returns numbers, so the message the agent gets back is yours to write, from whichever threshold fired, and every tool call still gets exactly one result. And a guarantee. That is two slides from now.
-->

---
layout: default
---

<div class="label">Where to draw the lines</div>

# One score, two policies

<div class="text-lg mt-1">Two lines turn one probability into three answers: run it, ask a person, block it.</div>

<div class="mt-12 text-base" style="display:grid;grid-template-columns:17rem 1fr 10rem;column-gap:1.5rem;row-gap:3.4rem;align-items:center">
  <div><b>TypeSafe cookbook, strict</b></div>
  <div style="position:relative;height:1.6rem;background:var(--c-bg-2);border-radius:4px">
    <div style="position:absolute;top:0;bottom:0;display:flex;align-items:center;justify-content:center;color:#fff;font-size:.75rem;font-weight:600;left:70%;right:0;background:var(--c-rose);border-radius:0 4px 4px 0">blocked</div>
    <div style="position:absolute;top:1.85rem;transform:translateX(-50%);font-size:.8rem;color:var(--c-ink-muted);white-space:nowrap;left:70%">block at 0.70</div>
    <div style="position:absolute;left:74%;top:-.4rem;bottom:-.4rem;width:3px;background:var(--c-ink)"></div>
    <div style="position:absolute;left:74%;top:-1.6rem;transform:translateX(-50%);font-size:.85rem;font-weight:700;color:var(--c-ink)">0.74</div>
  </div>
  <div>Blocked</div>
  <div><b>TypeSafe cookbook, permissive</b></div>
  <div style="position:relative;height:1.6rem;background:var(--c-bg-2);border-radius:4px">
    <div style="position:absolute;top:0;bottom:0;display:flex;align-items:center;justify-content:center;color:#fff;font-size:.75rem;font-weight:600;left:85%;right:0;background:var(--c-rose);border-radius:0 4px 4px 0">blocked</div>
    <div style="position:absolute;top:1.85rem;transform:translateX(-50%);font-size:.8rem;color:var(--c-ink-muted);white-space:nowrap;left:85%">block at 0.85</div>
    <div style="position:absolute;left:74%;top:-.4rem;bottom:-.4rem;width:3px;background:var(--c-ink)"></div>
    <div style="position:absolute;left:74%;top:-1.6rem;transform:translateX(-50%);font-size:.85rem;font-weight:700;color:var(--c-ink)">0.74</div>
  </div>
  <div>A person decides</div>
  <div><b>LangChain middleware</b></div>
  <div style="position:relative;height:1.6rem;background:var(--c-bg-2);border-radius:4px">
    <div style="position:absolute;top:0;bottom:0;display:flex;align-items:center;justify-content:center;color:#fff;font-size:.75rem;font-weight:600;left:0;width:50%;background:var(--c-emerald);border-radius:4px 0 0 4px">runs</div>
    <div style="position:absolute;top:0;bottom:0;display:flex;align-items:center;justify-content:center;color:#fff;font-size:.75rem;font-weight:600;left:50%;right:0;background:var(--c-rose);border-radius:0 4px 4px 0">blocked</div>
    <div style="position:absolute;top:1.85rem;transform:translateX(-50%);font-size:.8rem;color:var(--c-ink-muted);white-space:nowrap;left:50%">block at 0.5</div>
  </div>
  <div>Never asks</div>
</div>

<div class="caption" style="margin-top:3rem">The probability did not move. The policy did.</div>

<!--
Direction: About 2 minutes. The bars are to scale. The cookbook gives each policy's block line but not its review line, only that 0.74 is above the permissive one, so the bars draw no review line. LangChain's 0.5 applies to its own question, risky or insufficiently authorized, so its bar has no 0.74 mark.

A rule gives you match or no match. A reviewer gives you yes or no. A probability gives you a third answer for free: not sure. That is the band you send to a person.

TypeSafe's guardrail cookbook writes it as two thresholds: a review line, and above it a block line. Their example: Jev gave a jailbreak message a probability of 0.74. That is the black mark on the top two bars. Under their strict policy the block line is 0.70, so it is blocked. Under permissive it is 0.85, so it goes to a person. The probability did not move. The policy did, and the policy is two numbers in your code. In their words, TypeSafe supplies the assessment and your application owns the decision.

LangChain's AutoModeMiddleware, in langchain-typesafe 0.0.1a3, has one line at 0.5 and no person. Its docstring says it blocks risky calls and does not ask for approval. One choice in it is worth copying: if the classifier call fails, the tool does not run. That is Part 1 again, from the gate's side.
-->

---
layout: default
---

<div class="label">What an approval gate cannot promise</div>

# Measured, not trusted

| Claude Code auto mode | Safe actions blocked | Overeager let through |
|---|---|---|
| Both stages | 0.4% | 17% |
| First stage alone | 8.5% | 6.6% |

| Codex auto-review | Denied |
|---|---|
| Synthetic overeager actions | 90.3% |
| Synthetic prompt injections | 99.3% |

<div class="caption mt-6">An approval gate is a classifier. Classifiers get evaluated.</div>

<!--
Direction: About 3 minutes.

Anthropic's numbers for the full auto mode pipeline. On 10,000 real tool calls, it blocked 0.4 percent of safe actions. On 52 real overeager actions from their incident log, it let 17 percent through. The first stage alone let 6.6 percent through, but blocked 8.5 percent of safe traffic. That trade is the whole design problem in two rows.

OpenAI's numbers for auto-review: it denied 90.3 percent of synthetic overeager actions and 99.3 percent of synthetic prompt injections. In their words, it should not be treated as a guarantee of security, and red-teaming found cases where it could be talked into approving.

Their advice, when there are too many reviews, is to fix the boundary first: narrow rules like cargo test, never python. A gate is not a reason to leave the sandbox loose.

Every number on this slide came from an eval: a labelled set of actions, a rate of safe actions blocked, a rate of dangerous ones let through. Your approval gate is one of the first things in your agent worth measuring that way.
-->

---
layout: default
---

<div class="label">This week</div>

# Dates this week

| When | What |
|---|---|
| Tonight, 11:59 PM | Apply build due |
| Thursday | Analyze spec, after L12 |
| Before your Lab04 | Skills design doc drafted |
| Oct 12 or 13 | Lab04 |

<!--
Direction: About 2 minutes.

Tonight first. The Apply build is due at 11:59.

Thursday you get the Analyze spec, at the end of L12.

Before your Lab04 section, have a skills design doc drafted and committed. You started it with Elephant in Lab03. Lab04 takes it through a Goldfish round and into code.
-->

---
layout: default
---

<div class="label">Take these with you</div>

# Three questions

1. What does your agent retry, and for how long?
2. Which approvals can code answer?
3. Who answers the middle band?

<!--
Direction: About 2 minutes. Do not answer them.

Three for your design doc.

What does your agent retry, and for how long? Count the layers, including the SDK's.

Which of the approvals you gave this week could a rule in code have answered, with no model at all?

And when your agent runs with nobody at the prompt, who answers the middle band?
-->

---
layout: end
---

<!--
Questions.

Before Thursday, keep a note of what your agent asks you to approve and which ones you approve without reading. That list is your first rules file.

See you Thursday.
-->
