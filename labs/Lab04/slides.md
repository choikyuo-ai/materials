---
theme: ../../lectures/theme
title: "Lab04: Reaching the Computer: Shell, MCP and the Browser"
info: |
  EECS 498 AASE, Lab04
  How a harness reaches a computer: the shell, MCP, the browser and prompt injection, Fall 2026
layout: title
class: text-left
transition: fade
mdc: true
highlighter: shiki
---

# Reaching the Computer: Shell, MCP and the Browser

## Lab04 · Oct 12–13, 2026

<!--
Direction: About 1 minute.

Good afternoon. In L11 we covered what happens when a tool call fails, and who says yes before one runs. Today is the tools themselves: how a harness actually reaches your computer.

There is no demo and no assignment today, just this, then the attendance form. Everything on the slides was captured on Oct 7 from the real tools: the requests Codex and Claude Code send their model, a real MCP server on the wire, and a real browser driven by a harness.
-->

---
layout: default
class: v2
---

# Today's schedule

<div class="fig"><div>

<table class="map">
<tbody>
<tr><td>Part 1: the shell</td></tr>
<tr><td>Part 2: MCP</td></tr>
<tr><td>Part 3: the browser</td></tr>
<tr><td>Part 4: prompt injection</td></tr>
<tr><td>Questions, then the attendance form</td></tr>
</tbody>
</table>

</div></div>

<div class="source">Slides only today: nothing to install and nothing to hand in.</div>

<!--
Direction: About 1 minute.

Here is today. Three parts, each one a way a harness reaches your computer, then prompt injection, which cuts across all three. No assignment and nothing to install. The attendance form is at the end.
-->

---
layout: section
---

# Part 1: The shell

## How it runs · what it costs · what it can reach

<!--
Direction: About 25 minutes for Part 1.

Part 1. The shell is the one tool every harness has, so it is where we start.
-->

---
layout: section
---

# Short commands

## Part 1 · the shell

<!--
Direction: About 8 minutes for short commands.

Two kinds of command, and they work differently. First the ordinary kind: a command that runs, finishes, and prints something. pytest, git status, grep.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · short commands</div>

# One command, end to end

<div class="lede">The model only asks. The harness runs the command on your computer and sends back what it printed.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d03-shell-loop-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d03-shell-loop-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d03-shell-loop-2.svg" /></template>
</v-switch>
</div>

<div class="source">The same loop in every harness, from the big ones to a 100-line one.</div>

<!--
Direction: About 2 minutes. Two clicks.

One command, start to finish. The model cannot run anything. It writes a tool call that says: run pytest -q.

Click. The harness takes that string and runs it, on your computer, as you. The same as if you had typed it into a terminal.

Click. Whatever the command printed, plus its exit code, goes back to the model as text. Exit code 1 means a test failed, and the model decides what to do next.

That is the whole tool for a short command: it runs, it finishes, its output comes back. Next, what that tool call actually looks like.
-->

---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · short commands</div>

# The shell tool's inputs

<div class="lede">The model writes more than the command: a description for you, and how long to wait.</div>

<div class="fig"><div class="grid grid-cols-2 gap-8 items-center">
<div>

```json
{"name": "Bash",
 "input": {
   "command": "pytest -q",
   "description": "Run the test suite",
   "timeout": 120000
 }}
```

</div>
<div>

<table class="map compact">
<tbody>
<tr><td><code>command</code></td><td>What to run</td></tr>
<tr><td><code>description</code></td><td>What you see when asked to approve</td></tr>
<tr><td><code>timeout</code></td><td>How long to wait, in ms</td></tr>
<tr><td><code>run_in_background</code></td><td>For commands that do not finish</td></tr>
</tbody>
</table>

</div>
</div></div>

<div class="source">Claude Code 2.1.293's Bash tool, captured Oct 7.</div>

<!--
Direction: About 2 minutes.

This is the tool call the model writes when it wants to run the tests, in a real harness's format.

The command is only one of the inputs. The model also writes a description, in plain words, and that is what you see when the harness asks you to approve the command. The tool's own instructions say the user often reads the description without the command, so the description is a claim the model makes about its own command. L11's approval rule applies: read the command, not the explanation.

The timeout is how long the harness waits, two minutes by default. And run_in_background is for commands that will not finish, which is the second half of this part.

So what happens when a task needs more than one command?
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · short commands</div>

# One tool call at a time

<div class="lede">Normally each command is its own tool call. Every result goes back to the model before it picks the next command.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d05a-one-call-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d05a-one-call-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d05a-one-call-2.svg" /></template>
</v-switch>
</div>

<div class="source">An illustrative example: shell() stands for the harness's shell tool.</div>

<!--
Direction: About 2 minutes. Two clicks.

You ask the model why the tests fail. It needs two commands: git status, to see what changed, and the test suite.

It asks for the first one. The harness runs git status on your computer, gets four lines back, and hands those four lines to the model.

Click. The blue bar is the model working: it reads the four lines and decides what to run next. Then it asks for the tests. The harness runs pytest, and all three hundred lines of test log go back to the model.

Click. So two commands cost two trips through the model, and every line either command printed is now in its context, whether the model needed it or not.

This is how every harness works by default. Code mode changes it.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · short commands</div>

# Code mode: tools called from code

<div class="lede">In code mode, the model's tool call is a program. The harness runs all of it, and one result comes back: whatever the program printed.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d05b-code-block-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d05b-code-block-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d05b-code-block-2.svg" /></template>
</v-switch>
</div>

<div class="source">An illustrative example: shell() stands for the harness's shell tool, called from Python. Anthropic's “Code execution with MCP” (Nov 4, 2025) and Cloudflare's “Code Mode” (Sep 26, 2025) describe the same idea.</div>

<!--
Direction: About 2 minutes. Two clicks.

Same task: why do the tests fail. In code mode the model does not ask for one command. Its tool call is a short Python program it wrote just now. shell is the harness's shell tool, made callable from code. The program runs git status, runs the tests, and prints the status and only the end of the test log.

Click. The harness runs the whole program. Each shell call runs on your computer, and its output goes back to the program, not to the model: four lines from git status, three hundred from pytest. The three hundred lines stay inside the program.

Click. When the program ends, whatever it printed comes back to the model as one result, about twenty-five lines.

One block of code in, one result out. Next, the same run in the lanes from the last slide.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · short commands</div>

# Code mode in the same lanes

<div class="lede">The model is called once. The commands run while it waits, and only the printed result reaches its context.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d05-code-mode-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d05-code-mode-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d05-code-mode-2.svg" /></template>
  <template #3><img src="./assets/diagrams/d05-code-mode-3.svg" /></template>
</v-switch>
</div>

<div class="source">The same illustrative example as the last two slides.</div>

<!--
Direction: About 2 minutes. Three clicks.

Same task, same three lanes as one call at a time. The model sends the program as one tool call.

Click. The harness runs the program. Each shell call runs on your computer and its output comes back to the program, not to the model. The model is not called at all while this happens.

Click. When the program ends, the model gets only what it printed: about twenty-five lines.

Click. Compare one call at a time, two slides back: one trip through the model instead of two, and twenty-five lines in its context instead of three hundred. The more commands a task needs, the more this saves.

That is short commands. Next, the other kind: commands that never finish.
-->
---
layout: section
---

# Long-running commands

## Part 1 · the shell

<!--
Direction: About 12 minutes for long-running commands.

So far every command finished in a second or two. Some take minutes, and some never finish. First, how the harness waits for one.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · long-running commands</div>

# Commands that do not finish

<div class="lede">From outside, a slow command and one that never ends look the same. The harness has to decide when to stop waiting.</div>

<div class="fig"><div class="grid grid-cols-2 gap-6">
<div class="card"><div class="col-head">Servers</div><p><code>npm run dev</code>, <code>python -m taskrweb</code></p></div>
<div class="card"><div class="col-head">Watchers</div><p><code>tsc --watch</code>, anything that reruns on save</p></div>
<div class="card"><div class="col-head">Waiting for input</div><p>A password prompt, a pager, a yes or no</p></div>
<div class="card"><div class="col-head">Just slow</div><p>A full build, a long test suite</p></div>
</div></div>

<div class="source">Every harness needs an answer for all four.</div>

<!--
Direction: About 2 minutes.

Four kinds of command do not come back quickly.

Servers: a dev server runs until you stop it. Your taskr web app from Apply is one.

Watchers: a compiler or test runner in watch mode, rerunning every time a file changes.

Waiting for input: a password prompt, a pager like the one git log opens, a question that wants yes or no. These are stuck, not slow.

And plain slow: a big build or the full test suite, which will finish, eventually.

The harness cannot tell these apart by waiting. After thirty seconds of silence, a slow build and a server look exactly the same. So every harness has to decide when to stop waiting, and what to do with a process that is still running.

First, the commands that stop to ask a person.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · long-running commands</div>

# Commands that wait for input

<div class="lede">By default nobody is at the keyboard, so the harness keeps programs from stopping to ask.</div>

<div class="fig"><div>

<table class="map compact">
<colgroup><col style="width:20rem" /><col /></colgroup>
<thead><tr><th>The program wants</th><th>What the harness does</th></tr></thead>
<tbody>
<tr><td>A pager (<code>git log</code>)</td><td>Sets <code>PAGER=cat</code>, so it prints and exits</td></tr>
<tr><td>Colors and progress bars</td><td>Sets <code>TERM=dumb</code> and <code>NO_COLOR=1</code></td></tr>
<tr><td>An editor or a prompt (<code>git rebase -i</code>)</td><td>Tells the model not to run them</td></tr>
</tbody>
</table>

</div></div>

<div class="source">Codex 0.161 source: every command gets TERM=dumb, NO_COLOR=1 and PAGER=cat · Claude Code 2.1.293 Bash tool description: interactive flags are not supported.</div>

<!--
Direction: About 2 minutes.

The sneakiest kind of command stops and waits for a person.

git log opens a pager and waits for you to press q. A harness can set PAGER to cat for every command, so git log just prints and exits.

Programs that draw colors and progress bars fill the output with escape codes the model has to read through. The harness tells them there is no fancy terminal.

And some open an editor or ask a question, like git rebase dash i. There is nobody at the keyboard, so the harness's tool description tells the model plainly not to run them.

If you build a shell tool, you need the same defenses, or your harness will hang on the first git log.

That handles commands that wait for a person. Next, commands that are just slow.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · long-running commands</div>

# A wait has a deadline

<div class="lede">The harness waits up to 2 minutes. Then it stops waiting, but the command keeps running, and the harness tells the model when it ends.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d04e-deadline-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d04e-deadline-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d04e-deadline-2.svg" /></template>
</v-switch>
</div>

<div class="source">Simplified from Claude Code 2.1.293's Bash tool (timeout 2 minutes by default, 10 at most), captured Oct 7, and its docs.</div>

<!--
Direction: About 2 minutes. Two clicks.

The ordinary case. The model asks for the test suite, and the harness runs it. Nobody told the model this suite takes three minutes.

Click. The harness waits, two minutes by default. The model can ask for up to ten when it makes the call. When the time is up and pytest is still going, the harness does not kill it. It stops waiting, tells the model the command is still running, and gives it a name: task 7.

Click. The model gets on with other work. When pytest finally exits, the harness wakes the model with the result, the same output it would have got by waiting.

So a slow command does not block the model forever. But the model still sat through two minutes for a command it could have guessed was slow. Next: saying so up front.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · long-running commands</div>

# Starting in the background

<div class="lede">When the model knows a command will not end, it says so up front. The harness answers at once with a task id.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d04b-long-command-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d04b-long-command-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d04b-long-command-2.svg" /></template>
</v-switch>
</div>

<div class="source">Simplified from Claude Code 2.1.293's Bash tool (run_in_background), captured Oct 7.</div>

<!--
Direction: About 1 minute. Two clicks.

A dev server never exits, so waiting two minutes for it is pointless. The model asks for it in the background. The harness starts the process: the green bar is how long it runs.

Click. The harness does not wait at all. It answers right away: started, task 7.

Click. The model keeps working, and the server keeps running until something stops it. From now on, the task id is how the model reaches it.

Next: what the model can do with a command that is still running.
-->
---
layout: default
class: v2
---

<div class="where"><b>Shell</b> · long-running commands</div>

# Four ways into a running command

<div class="lede">A running command has an output, an input, a port and a stop. The model reaches each one through the harness, by task id.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d04i-reach-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d04i-reach-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d04i-reach-2.svg" /></template>
  <template #3><img src="./assets/diagrams/d04i-reach-3.svg" /></template>
  <template #4><img src="./assets/diagrams/d04i-reach-4.svg" /></template>
</v-switch>
</div>

<div class="source">Simplified from Claude Code 2.1.293's Monitor and TaskStop and Codex 0.160.0's write_stdin, captured Oct 7.</div>

<!--
Direction: About 5 minutes. Four clicks.

Task 7 is running in the background. What can the model do with it? A running program has only a few ways in, the same for you as for the model: what it prints, what it reads, the network port it listens on if it is a server, and stopping it. The model never touches the process. It asks the harness, and names the task by its id.

Click. Its output. The model asks the harness to watch task 7. Every new line the program prints goes to the harness, and the harness passes it to the model as it comes. Each line is a message in the model's context, so a real watch filters, for example only the lines with Error.

Click. Its input. The model writes to the program's input, the same as typing at its keyboard: here y and Enter, to answer a yes or no question, and it gets back whatever the program prints next. A Python session or a debugger works the same way and keeps its state between writes. The harness has to start the program in a terminal for this. It is also a risk: whatever a prompt was asking a person, the model can now answer.

Click. Its port. A server listens on a port, here 5173. The model needs no special tool for this: it runs curl like any other command, curl sends the request, and the response comes back as curl's output. A browser is another way in, in Part 3.

Click. Stop. The model asks the harness to stop task 7, and the harness stops it. If the model forgets, the harness stops a background command after ten minutes by default. The model can ask for up to two hours.

That is the shell: it runs on your computer, it costs whatever output comes back, and the harness decides how long to wait and how the model reaches a running process. Next: tools that someone else wrote.
-->
---
layout: section
---

# Part 2: MCP

## Tools that someone else wrote

<!--
Direction: About 13 minutes before the break, 10 after.

Part 2. MCP is how a harness gets tools that someone else wrote and runs.
-->

---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · what it is</div>

# What MCP is

<div class="lede">MCP is how a harness gets more tools from other programs, and hands them to the model.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d02-mcp-tools-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d02-mcp-tools-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d02-mcp-tools-2.svg" /></template>
</v-switch>
</div>

<div class="source">MCP, the Model Context Protocol, is an open standard (modelcontextprotocol.io), announced by Anthropic Nov 2024 and now under the Linux Foundation.</div>

<!--
Direction: About 2 minutes. Two clicks.

Here is all MCP is about. The harness has a list of tools it gives the model: the shell, read a file, edit a file. Those are written into the harness.

Click. An MCP server is another program that has tools of its own. GitHub's server has list_issues, add_issue_comment and more. The harness connects to it, asks for its tools, and adds them to the list.

Click. The model sees one list. It cannot tell which tools came from the harness and which came from MCP, and it does not need to. When it calls list_issues, the harness passes the call to GitHub's server.

So MCP is not a model and not a harness. It is a standard way for a harness to plug in more tools.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · why a standard</div>

# Without MCP, every harness writes its own

<div class="lede">Codex, Claude Code and ChatGPT would each write their own GitHub code, and their own Slack code, and so on.</div>

<div class="fig"><img src="./assets/diagrams/d07-n-by-m-0.svg" /></div>

<div class="source">Four harnesses and four services: sixteen integrations, each written and kept up by a harness's makers.</div>

<!--
Direction: About 1 minute.

Why not have each harness call GitHub's API itself? It can. But someone has to write that code, and keep it working.

Without a standard, Codex writes a GitHub integration, Claude Code writes another, ChatGPT a third, and the same again for Slack, Postgres and everything else. Sixteen integrations for four and four, each slightly different, each waiting on that harness's next release.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · why a standard</div>

# With MCP, the service writes it once

<div class="lede">GitHub writes one MCP server. Every harness that speaks MCP can use it.</div>

<div class="fig"><img src="./assets/diagrams/d07-hub-0.svg" /></div>

<div class="source">Each harness speaks MCP once, each service writes one server: eight pieces instead of sixteen. The idea is borrowed from the Language Server Protocol.</div>

<!--
Direction: About 1 minute.

With MCP, each harness learns MCP once, and each service writes one server. Four plus four.

GitHub knows its own API best, so GitHub writes the server. When GitHub adds a feature, it adds a tool to its server, and every harness sees it the next time it connects. Nobody waits for a harness release.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · how it runs</div>

# MCP in two calls

<div class="lede">The harness asks the server for its tools, then calls one. The model never talks to it.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d09-mcp-calls-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d09-mcp-calls-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d09-mcp-calls-2.svg" /></template>
</v-switch>
</div>

<div class="source">The two calls are tools/list and tools/call in the MCP spec. Tool names from GitHub's MCP server; the call and its result are illustrative.</div>

<!--
Direction: About 3 minutes. Two clicks.

Here is all of MCP, with GitHub's server as the example. Three lanes: the model, the harness, and the server.

One. When the harness starts, it asks the server: what tools do you have? The server answers with a list: list_issues, add_issue_comment, and so on, each with a short description and the inputs it takes.

Click. Two. The harness puts those tools in the model's tool list, next to its own tools like the shell. From the model's side, an MCP tool looks exactly like any other tool. Notice that the descriptions the server wrote now go into the model's prompt, word for word. We come back to that after the break.

Click. Three. The model decides to call list_issues for the aider repo. The harness turns that into the second call: run list_issues with these arguments. The server does the work, maybe calling GitHub's own API, and sends back the result, which goes to the model as text.

That is it. Two calls: list the tools, call one. The model never talks to the server directly. The harness is always in the middle, which is where approval from L11 sits.

Next: what that list of tools actually contains.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · how it runs</div>

# What a server tells the model

<div class="lede">For each tool: a name, a description of when to use it, and the arguments it takes.</div>

<div class="fig"><div class="tight">

```json
{"name": "list_issues",
 "description": "List issues in a GitHub repository with optional
                 filtering by labels, states, and custom fields.",
 "inputSchema": {"type": "object",
   "properties": {
     "owner": {"type": "string", "description": "The owner of the repository"},
     "repo":  {"type": "string", "description": "The name of the repository"},
     "state": {"type": "string", "description": "Filter by issue state (open, closed, all)"}},
   "required": ["owner", "repo"]},
 "annotations": {"readOnlyHint": true}}
```

</div></div>

<div class="source">list_issues from GitHub's MCP server source (github/github-mcp-server), trimmed to three of its seven arguments. The harness copies this into the model's tool list.</div>

<!--
Direction: About 2 minutes.

This is what one tool looks like when GitHub's server lists it. Real, from the server's source, trimmed.

A name, which is what the model calls.

A description, in plain English. This is the only thing that tells the model when to use the tool, so it is a prompt, written by GitHub.

An input schema: the arguments, their types, a short description of each, and which ones are required. Owner and repo are required, state is optional. The model fills these in when it calls the tool, and the harness can check them before sending anything.

And an annotation saying the tool only reads. A harness can use that to skip asking you for approval, though the spec says to trust annotations only from servers you trust.

The harness copies all of this into the model's tool list, in the same format as its own tools.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · how it runs</div>

# Where MCP servers run

<div class="lede">A local server is a program the harness starts on your computer. A remote one runs on someone else's machines.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d08-mcp-parts-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d08-mcp-parts-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d08-mcp-parts-2.svg" /></template>
</v-switch>
</div>

<div class="source">MCP specification, 2026-07-28 · modelcontextprotocol.io</div>

<!--
Direction: About 2 minutes. Two clicks.

Where does an MCP server actually run? Two places, and the picture is the point.

Your computer, with the harness on it.

Click. A local server is just a program. When the harness starts, it starts the server too, on your computer, and they talk through the program's input and output. Playwright's browser server works this way, and so does the Excalidraw server that drew these diagrams. It runs as you, with your files.

Click. A remote server runs on someone else's machines. GitHub's official server is one: the harness connects to it over HTTPS, like any web API, and GitHub runs it. Nothing to install, and your login goes to GitHub, not to the harness.

Either way the harness is in the middle, and the model only sees tools.

Next: how the harness actually sends a server a call.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · how it runs</div>

# How the harness sends a call

<div class="lede">One line of JSON. A local server reads it from its input; a remote server gets it over HTTPS.</div>

<div class="fig"><div class="tight">

```text
the harness writes:
{"jsonrpc": "2.0", "id": 3, "method": "tools/call",
 "params": {"name": "list_issues",
            "arguments": {"owner": "Aider-AI", "repo": "aider"}}}

the server answers:
{"jsonrpc": "2.0", "id": 3,
 "result": {"content": [{"type": "text", "text": "[{\"number\": 4521, …}]"}]}}
```

</div></div>

<div class="source">MCP specification: JSON-RPC 2.0, one message per line on a local server's stdin and stdout. Trimmed; the result is illustrative.</div>

<!--
Direction: About 2 minutes.

So how does the harness actually send a command to an MCP server? It is less magic than it sounds.

For a local server, the harness started the program itself, so it holds the program's input and output, the same pipes the shell uses. To call a tool, it writes one line of JSON to the program's input: call list_issues, with these arguments. The server does the work and prints one line of JSON back. The id, 3, matches the answer to the question.

For a remote server, like GitHub's, it is the same JSON, sent in an HTTPS request instead of a pipe.

The answer's content is what the harness passes to the model as the tool result. Here, a list of issues as text.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · how it runs</div>

# More than tool calls

<div class="lede">A server can also handle login, share data, ask you questions, and add tools.</div>

<div class="fig"><div class="grid grid-cols-2 gap-6">
<div class="card"><div class="col-head">Login</div><p>Sign in once; the token stays in the server</p></div>
<div class="card"><div class="col-head">Data</div><p>Files and records, not just tools</p></div>
<div class="card"><div class="col-head">Questions</div><p>Asks you for something it is missing</p></div>
<div class="card"><div class="col-head">Updates</div><p>New tools without a harness release</p></div>
</div></div>

<div class="source">MCP specification: authorization (OAuth), resources, elicitation, and list-changed notifications.</div>

<!--
Direction: About 2 minutes.

Tool calls are most of what MCP is used for, but a server can do four more useful things.

Login. A server that needs your account handles the sign-in itself. You click through a normal sign-in page once, in Codex that is codex mcp login, and the server keeps the token. The model never sees it, which matters a lot by Part 4.

Data. Besides tools, a server can offer resources: files, records and documents the harness can pull into the conversation.

Questions. Mid-call, a server can stop and ask you for something it needs, like which repository you meant, instead of guessing.

Updates. A server can tell the harness its tool list changed, so new tools show up without anyone updating the harness.

Next: when MCP is the right choice at all.
-->
---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · what it costs</div>

# An MCP server or a CLI

<div class="lede">If the harness has a shell, a CLI the model already knows is usually cheaper.</div>

<div class="fig"><div class="grid grid-cols-2 gap-8">
<div class="card">
<div class="col-head">gh through the shell</div>
<p>The model already knows it</p>
<p>Nothing added to each request</p>
<p>Needs a shell and your login</p>
</div>
<div class="card">
<div class="col-head">The GitHub MCP server</div>
<p>Typed arguments, described tools</p>
<p>Works where there is no shell</p>
<p>Handles your login itself</p>
</div>
</div></div>

<div class="source">Playwright MCP's README now points coding tools at its CLI as “more token-efficient” · M. Zechner, Nov 2, 2025 · S. Willison on skills, Oct 16, 2025.</div>

<!--
Direction: About 3 minutes.

Remember the third option: the model running a CLI like gh through the shell.

gh through the shell. The model has seen gh and git thousands of times in training, so it needs no definition at all. It runs like every other command. It costs nothing until it runs. But it needs a shell and your GitHub login on that machine.

The MCP server. Typed inputs and outputs. It works in apps with no shell, like ChatGPT. It handles its own login, so the model never sees your token. And its tool definitions ride along in every request, unless the host hides them.

For a harness with a shell, the CLI usually wins, and the people who build these tools say so. Playwright, which we use in Part 3, now tells coding tools to use its CLI rather than its MCP server, because it is cheaper.

MCP wins when there is no shell, when the login has to stay away from the model, or when there is no CLI. Next: how much those definitions actually cost.
-->

---
layout: default
class: v2
---

<div class="where"><b>MCP</b> · what it costs</div>

# Loading tools on demand

<div class="lede">The harness sends the model only the tool names. When the model needs a tool, it searches, and the harness loads that one definition.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d11-deferred-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d11-deferred-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d11-deferred-2.svg" /></template>
  <template #3><img src="./assets/diagrams/d11-deferred-3.svg" /></template>
</v-switch>
</div>

<div class="source">Playwright MCP 0.0.83's 25 tools, captured Oct 7, o200k tokens · Anthropic measured 77K tokens dropping to 8.7K (Nov 24, 2025).</div>

<!--
Direction: About 2 minutes. Three clicks.

Every tool the model can call has a definition: its name, a description, and its arguments. Normally all of them go into every request. Loading on demand sends less. Here Playwright's MCP server is connected, with 25 tools.

The harness sends only the names: 94 tokens for all 25, plus one tool of its own, search.

Click. The model wants to click a button, so it searches for click. The harness loads the one matching definition, browser_click in full, 257 tokens.

Click. Now the model can call it. The harness passes the call to Playwright's server with tools/call, the same as any MCP call, and the result comes back.

Click. Without this, all 25 definitions, 5,250 tokens, go into every request, every turn, whether the model touches the browser or not.

It is the same idea as skills in L10: names first, details when needed. The Claude Code session I built these slides in had twelve MCP servers and 109 tools, all names only until needed. Anthropic measured the same move at about 77 thousand tokens of definitions down to under nine. Codex goes further: its MCP tools are not in the prompt at all, and the model finds them by searching a list in code.

That is MCP: two calls to a server, paid for in tokens on every request. Next: the channel that reaches the most, the browser.
-->

---
layout: section
---

# Part 3: The browser

## Pages, pixels and trees

<!--
Direction: About 15 minutes.

Part 3. Why does a harness need a browser at all? To check a web page it just built, to read pages that only appear with JavaScript, and to use web apps with no API. Every capture here is Playwright's MCP server driving a real browser on Aider's documentation, on Oct 7.
-->

---
layout: default
class: v2
---

<div class="where"><b>Browser</b> · how it runs</div>

# Three ways to see a page

<div class="lede">A screenshot shows the layout, the tree names what can be clicked, the DOM holds everything.</div>

<div class="fig"><div class="grid grid-cols-2 gap-6 items-center">
<div>
<div class="col-head">Pixels: a screenshot</div>
<img src="./assets/img/aider-docs.png" class="shot" />
</div>
<div class="tight">
<div class="col-head">The accessibility tree</div>

```yaml
- navigation "Main" [ref=e7]:
  - list [ref=e8]:
    - listitem [ref=e9]:
      - link "Installation" [ref=e13]
```

<div class="col-head mt-4">The DOM, through JavaScript</div>

```js
() => [...document.querySelectorAll('nav a')]
  .slice(1, 5).map(a => a.getAttribute('href'))
// ["/docs/install/optional.html", …]
```

</div>
</div></div>

<div class="source">Captured Oct 7 · Playwright MCP 0.0.83 on aider.chat/docs: browser_take_screenshot, browser_snapshot, browser_evaluate.</div>

<!--
Direction: About 3 minutes.

One page, three ways the model can see it. All three are real outputs from the same page.

A screenshot. The model reads it as an image, so it sees what you see: layout, colors, what overlaps what.

The accessibility tree, the structure browsers build for screen readers. Every element with its role and its name, and a short handle like e13 that the model can use to act on it. No pixels at all.

The DOM, through JavaScript the model writes and the harness runs in the page. This pulled four links out of the menu, and they are links you cannot see in the screenshot, because that menu is collapsed. The DOM has everything, hidden parts included.

Real harnesses mix them. Claude in Chrome reads the tree and takes screenshots. Next: how it acts on what it sees.
-->

---
layout: default
class: v2
---

<div class="where"><b>Browser</b> · how it runs</div>

# A click by reference

<div class="lede">The model clicks a name from the tree, not a pixel, and the reply says what it clicked.</div>

<div class="fig">
<v-switch>
<template #0>
<div class="grid grid-cols-2 gap-6 items-center w-full tight">
<div>

```yaml
- navigation "Main" [ref=e7]:
  - list [ref=e8]:
    - listitem [ref=e9]:
      - link "Installation" [ref=e13]
```

</div>
<img src="./assets/img/aider-docs.png" class="shot" />
</div>
</template>
<template #1>
<div class="grid grid-cols-2 gap-6 items-center w-full tight">
<div>

```yaml
      - link "Installation" [ref=e13]
```

```json
browser_click {"element": "Installation link",
               "target": "e13"}
```

</div>
<img src="./assets/img/aider-docs.png" class="shot" />
</div>
</template>
<template #2>
<div class="grid grid-cols-2 gap-6 items-center w-full tight">
<div>

```json
browser_click {"element": "Installation link",
               "target": "e13"}
```

```js
// Ran Playwright code
await page
  .getByRole('navigation', { name: 'Main' })
  .getByRole('link', { name: 'Installation' })
  .click();
```

</div>
<img src="./assets/img/aider-install.png" class="shot" />
</div>
</template>
</v-switch>
</div>

<div class="source">Captured Oct 7 · Playwright MCP 0.0.83: the snapshot, the call and the reply. Computer use clicks by screen coordinates instead.</div>

<!--
Direction: About 3 minutes. Two clicks.

One click, exactly as it happened.

The tree gives the Installation link a handle, e13. On the right, the page.

Click. The model calls browser_click with two things. target is the handle. element is a plain-English description, and it is what a person sees if they are asked to approve the click. The model writes both, so that description is the model's claim about what e13 is.

Click. The reply shows what really happened. It did not click a pixel. It found the navigation called Main, then the link called Installation, and clicked that. On the right, the page it landed on.

The other way to click is by screen coordinates, which is what computer use does. That works on any app, and it hits the wrong thing when the layout moves. A handle only works until the page changes, and then the model looks again.

So which way of looking is cheaper?
-->

---
layout: default
class: v2
---

<div class="where"><b>Browser</b> · how it runs</div>

# Browser tools, side by side

<div class="lede">Claude in Chrome works like Playwright: the same three views, and clicks by reference.</div>

<div class="fig"><div class="tight">

<table class="map compact">
<colgroup><col style="width:9rem" /><col style="width:31%" /><col /><col /></colgroup>
<thead><tr><th></th><th>Playwright MCP</th><th>Chrome DevTools MCP</th><th>Claude in Chrome</th></tr></thead>
<tbody>
<tr><td>Screenshot</td><td><code>browser_take_screenshot</code></td><td><code>take_screenshot</code></td><td><code>computer</code> screenshot</td></tr>
<tr><td>The tree</td><td><code>browser_snapshot</code></td><td><code>take_snapshot</code></td><td><code>read_page</code></td></tr>
<tr><td>The DOM</td><td><code>browser_evaluate</code></td><td><code>evaluate_script</code></td><td><code>javascript_tool</code></td></tr>
<tr><td>Click</td><td><code>browser_click</code> by ref</td><td><code>click</code> by uid</td><td><code>computer</code> by ref or x, y</td></tr>
<tr><td>Open a page</td><td><code>browser_navigate</code></td><td><code>navigate_page</code></td><td><code>navigate</code></td></tr>
</tbody>
</table>

</div></div>

<div class="source">Playwright MCP 0.0.83 tools/list, Oct 7 · Chrome DevTools MCP tool reference · Claude in Chrome's tool definitions as Claude Code receives them over MCP, Oct 8.</div>

<!--
Direction: About 2 minutes.

Everything you just saw with Playwright is how the other browser tools work too, including Claude in Chrome.

Claude in Chrome is the extension that lets Claude drive the Chrome you already use. When you connect it to Claude Code, its tools arrive over MCP, like any other server's. I read their definitions for this slide.

Read a row at a time. Every one of these has a screenshot. Every one has the accessibility tree: Playwright calls it a snapshot, Chrome DevTools a snapshot too, Claude in Chrome calls it read_page. Every one can run JavaScript in the page to reach the DOM. And every one clicks by a reference from the tree: Playwright's e13, DevTools' uid, Claude in Chrome's ref_1. Claude in Chrome can also click by screen coordinates, the computer-use way.

The names differ. The ideas are the same three views and the same click. Learn it once on any of them and you can read the rest.

The real difference between them is not the tools. It is whose browser they drive, which is two slides from now. First, cost.
-->

---
layout: default
class: v2
---

<div class="where"><b>Browser</b> · what it costs</div>

# One page, two costs

<div class="lede">On this page the tree cost almost six times the screenshot. Which works better depends on the page.</div>

<div class="nums">
<div>
<div class="num">1,196</div>
<div class="num-label">tokens for the 1280 × 720 screenshot</div>
</div>
<div>
<div class="num amber">6,802</div>
<div class="num-label">tokens for the same page's tree, 523 lines</div>
</div>
</div>

<div class="source">Screenshot: Anthropic's 28 × 28 pixel patches. Tree: o200k tokenizer. WebVoyager (ACL 2024): on Booking and Google Flights, screenshots beat the tree by 41 and 52 points.</div>

<!--
Direction: About 2 minutes.

The usual claim is that the tree is the cheap option and screenshots are expensive. On this page it is the other way round.

The screenshot: about twelve hundred tokens. Anthropic counts images in small squares of pixels, so the cost depends on the image size, not the content.

The tree: about sixty-eight hundred, because a documentation page has hundreds of links, and every one is a line.

Which one works better? It depends on the page. A 2024 study found screenshots far ahead on visual sites like Booking and Google Flights, and roughly even on text-heavy ones. So measure on the pages your harness actually uses.

Last question for the browser: what can it reach?
-->

---
layout: default
class: v2
---

<div class="where"><b>Browser</b> · what it can reach</div>

# Three ways into a browser

<div class="lede">Each step out reaches more of you: a fresh browser, your logged-in Chrome, your whole screen.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d15-reach-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d15-reach-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d15-reach-2.svg" /></template>
</v-switch>
</div>

<div class="source">Playwright MCP README · Chrome DevTools MCP (Google, Sep 2025) · Vibium (J. Huggins) · Claude in Chrome docs · Anthropic computer use docs.</div>

<!--
Direction: About 2 minutes. Two clicks.

Where the harness's browser comes from decides what it can reach.

A fresh browser. Playwright, Google's DevTools server, and Vibium from the creator of Selenium each start their own browser with no logins. This is the right tool for checking a page you just built.

Click. Your own Chrome. Claude in Chrome lives in the browser you already use, so it is logged in to everything you are: email, GitHub, Canvas, your bank. That is the point of it, and the risk.

Click. Your whole screen. Computer use is not a browser tool at all. It sees the screen and moves the mouse, so it reaches every app you have open.

The more it can reach, the more it matters what it reads, and that is Part 4.
-->

---
layout: section
---

# Part 4: Prompt injection

## When what the model reads gives orders

<!--
Direction: About 10 minutes for Part 4.

Part 4, and a change of subject. The first three parts were about what each channel can do. This one is about what can go wrong in all three at once.
-->
---
layout: default
class: v2
---

<div class="where"><b>Prompt injection</b> · what it is</div>

# What prompt injection is

<div class="fig"><div>

<div class="prompt" style="font-size:1.7em">

**Prompt injection** is text the model reads that it *follows as if you wrote it*.

</div>

<div class="grid grid-cols-3 gap-6 mt-5">
<div class="card"><div class="col-head">Through the shell</div><p>A README or a file in a repo you cloned</p></div>
<div class="card"><div class="col-head">Through MCP</div><p>A tool's description, or an issue it fetches</p></div>
<div class="card"><div class="col-head">Through the browser</div><p>Hidden text on any page it visits</p></div>
</div>

</div></div>

<div class="source">The model receives your request and everything its tools return as one stream of text. Nothing in that stream marks which words are yours.</div>

<!--
Direction: About 2 minutes.

A definition first. Prompt injection is text the model reads that it follows as if you wrote it.

Why it works: the model gets your request, its instructions, and everything its tools bring back, all as one stream of text. Nothing in that stream says which words came from you and which came from a web page. So an instruction hidden in what the model reads can look exactly like one from you.

And every channel from today brings in text someone else wrote. Through the shell: a README, or any file in a repository you cloned. Through MCP: a tool's description, or an issue the tool fetches. Through the browser: any page it visits, including text you cannot see on screen.

First, a real case through MCP.
-->
---
layout: default
class: v2
---

<div class="where"><b>Prompt injection</b> · through MCP</div>

# A server's words in your prompt

<div class="lede">A tool description is an instruction to your model. Install a server like code that runs as you.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d12-poison-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d12-poison-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d12-poison-2.svg" /></template>
</v-switch>
</div>

<div class="source">After Invariant Labs, “Tool Poisoning Attacks,” Apr 1, 2025. The MCP spec: tool descriptions are untrusted unless the server is trusted.</div>

<!--
Direction: About 3 minutes. Two clicks.

Back to step two of MCP in two calls: the server's tool descriptions go into the model's prompt, word for word.

This attack was published in April 2025. A server you installed has a tool called add. Its description says it adds two numbers, and then, in text you never see, tells the model to read your ssh key and pass it along as an extra argument.

Click. The harness does what it does with every description: puts it in every request. The model reads it as an instruction from the tool's author.

Click. The model calls add with 2 and 3, plus a note containing your private key. Your approval prompt may only show add 2 3. The server gets the key.

A server can also be honest the day you install it and change its description next week.

So an MCP server reaches whatever its own code can reach, plus your model, through its text.

So what makes an injection actually dangerous? Three things together.
-->

---
layout: default
class: v2
---

<div class="where"><b>Prompt injection</b> · all three channels</div>

# The lethal trifecta

<div class="lede">Private data, untrusted content and a way to send data out, in one harness, is how data gets stolen.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d16-trifecta-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d16-trifecta-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d16-trifecta-2.svg" /></template>
  <template #3><img src="./assets/diagrams/d16-trifecta-3.svg" /></template>
</v-switch>
</div>

<div class="source">Simon Willison, “The lethal trifecta for AI agents,” Jun 16, 2025.</div>

<!--
Direction: About 2 minutes. Three clicks.

Simon Willison named this in June 2025.

A harness that can read your private data is fine.

Click. A harness that reads untrusted content, like a web page or an issue anyone can file, is fine.

Click. A harness that can send data out is fine.

Click. A harness with all three can be told, by something it read, to send your data somewhere. That was the poisoned tool description. Take away any one leg and the attack fails.

So which tools give your harness which legs?
-->

---
layout: default
class: v2
---

<div class="where"><b>Prompt injection</b> · all three channels</div>

# The trifecta in each channel

<div class="lede">Each channel can supply all three legs on its own. Cut one leg and the attack fails.</div>

<div class="fig">
<v-switch>
  <template #0><img src="./assets/diagrams/d17-trifecta-grid-0.svg" /></template>
  <template #1><img src="./assets/diagrams/d17-trifecta-grid-1.svg" /></template>
  <template #2><img src="./assets/diagrams/d17-trifecta-grid-2.svg" /></template>
  <template #3><img src="./assets/diagrams/d17-trifecta-grid-3.svg" /></template>
</v-switch>
</div>

<div class="source">The MCP row happened: Invariant Labs, May 26, 2025. An issue on a public repo led a harness with GitHub's MCP server to copy private repo data into a public pull request.</div>

<!--
Direction: About 3 minutes. Three clicks.

Click. The shell. Private data: your ssh key, which every sandbox lets it read. Untrusted content: a README in a repo you cloned. A way out: curl or git push. The network block in the sandbox is what cuts this leg.

Click. MCP. This row really happened, in May 2025. Researchers filed an issue on a public repository. A harness with GitHub's MCP server, asked to look at open issues, read it, followed it, pulled data from the user's private repositories and posted it in a public pull request. Nothing in the server had a bug. The harness just had all three legs.

Click. The browser. Your logged-in sessions, every page it reads, any form it can submit.

When you add a tool to your harness, ask which leg it adds.

That is today. Before you go, the attendance form.
-->

---
layout: end
---
