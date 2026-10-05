---
theme: ../../lectures/theme
title: "Lab03: Where we are, and starting on Aider"
info: |
  EECS 498 AASE, Lab03
  What students told us, where the course is going, then learning Aider with Codex, Fall 2026
layout: title
class: text-left
transition: fade
mdc: true
highlighter: shiki
---

# Where we are, and starting on Aider

## Lab03 · Oct 5–6, 2026

<!--
Rebuilt 2026-10-01, and again on 2026-10-05 on instructor direction. This
replaces the "Build the agent" lab.

Two halves. About 15 minutes of talk: what we are changing and why, from
what you told us you are interested in on the Lab02 form. Then the lab itself: Codex set up on Luna,
Aider mapped with peanuts, a discussion of what you found, and the first
design of a feature for Aider.

The Apply build is due tomorrow (Tuesday) at 11:59 PM. Say so once, here.
-->

---
layout: default
---

<div class="label">Lab03</div>

# Today's schedule

1. What we are changing
2. Setup: Codex and Aider
3. Learning Aider with Codex
4. Designing skills for Aider

<!--
About 30 seconds. Say the shape: twenty minutes of us talking, then the rest
is yours, on your laptop, in the Aider codebase.

The attendance form is at the end, and it asks which AI subscriptions you
have, so answer that one honestly.
-->

---
layout: section
---

# What we are changing

## You were interested. We want to see what we can do.

---
layout: default
---

<div class="label">What we are changing · Lab02 form</div>

# Frontier tools, used well

<div class="text-2xl mt-2" style="color:var(--c-ink)">The most common thing you want to learn: using the tools <em>well</em>.</div>

<div class="grid grid-cols-2 gap-10 mt-6 items-center">
  <div>
    <div class="font-sans font-semibold" style="font-size:3.5rem;line-height:1;color:#1A1A1A">About half</div>
    <div class="text-lg mt-2">of your answers on what to learn</div>
  </div>
  <div class="flex flex-col gap-3">
    <div class="card text-sm italic">"how to push the limits of frontier models and harnesses"</div>
    <div class="card text-sm italic">"more realistic (modern) coding agent workflows"</div>
    <div class="card text-sm italic">"the workflow of how actual engineers use it"</div>
  </div>
</div>

<!--
About a minute.

49 of you answered "If you had Codex for the rest of the course, what would
you want to learn?" We read every answer and sorted them by hand. About 23,
the biggest group by far, were about using the tools well: real workflows,
good habits, reliable results, getting the most out of them. The next most
common were design and specs (8), orchestration and skills (7), and managing
context and tokens (5).

Notice that almost nobody asked what to build with it. You asked how to use
it. Today's lab is our first answer: Codex teaching you a codebase you did
not write.

The three quotes are from three different students. Nobody is named.
-->

---
layout: default
---

<div class="label">What we are changing</div>

# More focus on the tools

<div class="text-2xl mt-2" style="color:var(--c-ink)">From today you work with <em>Codex</em>, and labs teach you to use it well.</div>

<div class="grid grid-cols-3 gap-6 mt-8 items-stretch">
  <div class="card">
    <ph-terminal-window-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">Everyone uses Codex</div>
    <div class="text-sm opacity-70 flex-1">On Luna, with your course key</div>
  </div>
  <div class="card">
    <ph-laptop-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">Labs are hands-on</div>
    <div class="text-sm opacity-70 flex-1">Your laptop, real code</div>
  </div>
  <div class="card">
    <ph-target-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">We teach using it well</div>
    <div class="text-sm opacity-70 flex-1">Learn the code, then design</div>
  </div>
</div>

<!--
About 90 seconds. Three concrete changes.

Everyone uses Codex. A frontier coding agent, on the U-M GPT Toolkit key
you already have, with a Luna model. Same tool and same model for everyone,
so what works for one of you works for the room.

Labs are hands-on. Labs now spend most of their time with Codex in your
hands, on real code, with staff walking the room. Today is the first one.

We teach using it well. The part you asked for most. A strong model can
write code for you. The skill is using it to understand code you did not
write, and then making the design decisions yourself. That is what today
practises.
-->

---
layout: default
---

<div class="label">What we are changing</div>

# Big codebases, starting with Aider

<div class="text-2xl mt-2" style="color:var(--c-ink)">Small models meant small repos. Better tools mean the <em>real</em> Aider.</div>

<div class="grid grid-cols-2 gap-8 mt-8 items-stretch">
  <div class="card">
    <div class="font-semibold mb-3">Until now</div>
    <div class="text-sm mb-2">Aider on a small model</div>
    <div class="text-sm mb-2">Repos you built from scratch</div>
    <div class="text-sm">Sized to what the model could handle</div>
  </div>
  <div class="card">
    <div class="font-semibold mb-3">From today</div>
    <div class="text-sm mb-2">Codex on Luna</div>
    <div class="text-sm mb-2">The real Aider: 20,000 lines of Python</div>
    <div class="text-sm">Plus 12,000 lines of tests</div>
  </div>
</div>

<div class="caption mt-6">Today you start adding Claude Code features to it. Two of you proposed exactly this.</div>

<!--
About 2 minutes. This is the direction.

Until now the projects were small repositories you built yourself, because
you were working in Aider on a small model, and the work had to fit what
that model could handle. That was the right size for those tools.

Now you have Codex on a Luna model, and the scope goes up a lot. So instead
of a small project from scratch, you work in the real Aider: the open-source
tool you have used since week 1. About 20,000 lines of Python with 12,000
lines of tests (checked Oct 4 at commit 5dc9490b). That is real
engineering: code that already exists, that someone else wrote, that you
have to understand before you touch it.

Today you start adding Claude Code features to it, beginning with skills.
How long the course stays in Aider, and what comes after it, is not
decided.

Two of you suggested exactly this on the Lab02 form, independently:
"use codex to take aider open source code and make it super sophisticated"
and "how to build my own agent like codex that is stronger than aider".
Nobody is named.

Say what is not decided: how this is split into projects, what each one
requires, and the dates. That comes later. Today is the start, not a graded
project.
-->

---
layout: default
---

<div class="label">What we are changing</div>

# Learn, then design, then build

<div class="grid grid-cols-3 gap-6 mt-8 items-stretch">
  <div class="card">
    <ph-book-open-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">1. Learn</div>
    <div class="text-sm opacity-70 flex-1">Codex teaches you the code</div>
  </div>
  <div class="card">
    <ph-pencil-ruler-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">2. Design</div>
    <div class="text-sm opacity-70 flex-1">You make the decisions</div>
  </div>
  <div class="card">
    <ph-hammer-bold class="text-3xl text-blue-600 mb-3" />
    <div class="font-semibold mb-1">3. Build</div>
    <div class="text-sm opacity-70 flex-1">Small steps, good prompts</div>
  </div>
</div>

<div class="caption mt-6">Skip step 1 and the model makes every decision in step 2 for you.</div>

<!--
About 90 seconds. This is how we want you to work in Aider, today and for
in whatever codebase comes next, and the order matters.

Learn first. Use Codex as a teacher: ask it how the code works, make it show
you file names and line numbers, and check what it tells you.

Then design. You cannot make a design decision about code you do not
understand. If you try, you will hand the decision to the model without
noticing, and it will design everything. That is the failure we are trying
to avoid.

Then build, in small steps, with prompts that carry your design. Not one
prompt that asks for the whole feature. A one-shot prompt gives you the
model's design, not yours.

The rest of today follows these three steps in order.
-->

---
layout: default
---

<div class="label">What we are changing</div>

# What we are still figuring out

<div class="text-2xl mt-2" style="color:var(--c-ink)">Most of getting good is <em>use</em>, and use is hard to teach.</div>

<div class="grid grid-cols-3 gap-6 mt-8 items-stretch">
  <div class="card">
    <ph-chalkboard-teacher-bold class="text-3xl text-amber-600 mb-3" />
    <div class="font-semibold mb-1">How to teach the tools</div>
    <div class="text-sm opacity-70 flex-1">Beyond "use them a lot"</div>
  </div>
  <div class="card">
    <ph-graduation-cap-bold class="text-3xl text-amber-600 mb-3" />
    <div class="font-semibold mb-1">What learning looks like</div>
    <div class="text-sm opacity-70 flex-1">When the model writes the code</div>
  </div>
  <div class="card">
    <ph-kanban-bold class="text-3xl text-amber-600 mb-3" />
    <div class="font-semibold mb-1">What the projects require</div>
    <div class="text-sm opacity-70 flex-1">Details later this term</div>
  </div>
</div>

<div class="caption mt-6">We will work these out with you. The form at the end is the first step.</div>

<!--
About 2 minutes. Be straight about this. It is the honest state of the
course.

How to teach the tools. Most of what makes someone good with Codex comes
from using it, a lot, on real work, and noticing where it goes wrong. That
is hard to put in a lecture. Today is our first try at a lab built around
it.

What learning looks like. If the model can write most of the code, working
code no longer shows what you understood. Our current bet: you learn the
code first, with the agent teaching you, and then you make the design
decisions. Today is built that way. We are not sure it is right.

What the projects require. We know the direction, which is the next slide.
What each project asks you to hand in, and when, comes later. We will tell
you before you need it.
-->

---
layout: section
---

# Setup: Codex and Aider

---
layout: default
---

<div class="label">Setup · Codex</div>

# Codex on the Toolkit

<div class="text-3xl mt-12 font-semibold"><a href="https://its.umich.edu/computing/ai/codex-classroom">its.umich.edu/computing/ai/codex-classroom</a></div>

<div class="text-xl mt-6">Follow the ITS page from the top. Your key already exists.</div>

<div class="caption mt-10">Model: <code>gpt-6-luna</code>.</div>

<!--
About a minute to say, then up to 15 minutes of setup in the room. Many of
you did this before lab. If you did, help a neighbor.

Open the ITS Codex for the Classroom page and follow it from the top. It
covers the key (every student on the Canvas roster already has one), the
install guides for Mac, Linux and Windows, and how to check what is left of
your $25.

Everyone uses Codex on gpt-6-luna, at medium effort. Not Claude Code on
your own plan, and not Cursor. gpt-6-luna was verified with Codex on the
Toolkit on Oct 1 and again on Oct 5.

For staff, if a student gets stuck: the direct guides are KB 15550 (Mac,
Linux) https://teamdynamix.umich.edu/TDClient/30/Portal/KB/Article/15550/Codex-Setup-for-Mac-Linux
and KB 15551 (Windows), and a config tested on Oct 1 is in notes.md.
-->

---
layout: default
---

<div class="label">Setup · Codex</div>

# Already using Codex

<div class="text-xl mt-2" style="color:var(--c-ink)">Keep your own setup. Add the course key as a <em>profile</em>.</div>

<div class="grid grid-cols-2 gap-6 mt-6">
<div>

```toml
# add to ~/.codex/config.toml
[model_providers.toolkit]
name = "UM GPT Toolkit"
base_url = "https://api.toolkit.umgpt.umich.edu/v1"
env_key = "UMGPT_TOOLKIT_API_KEY"
```

</div>
<div>

```toml
# new file ~/.codex/toolkit.config.toml
model = "gpt-6-luna"
model_provider = "toolkit"
model_reasoning_effort = "medium"
```

</div>
</div>

<div class="text-lg mt-4"><code>codex --profile toolkit</code> uses the course key. Plain <code>codex</code> stays on your own account.</div>

<div class="caption mt-4">Put your course key in UMGPT_TOOLKIT_API_KEY. Tested on Codex 0.160.</div>

<!--
About a minute. Only for students who already use Codex with their own
ChatGPT account. Everyone else follows the ITS page on the previous slide.

The ITS guide sets the Toolkit as your default provider, which would replace
the setup you already use. A profile avoids that. The provider block goes
in your existing ~/.codex/config.toml. The profile is its own file,
~/.codex/toolkit.config.toml, which Codex layers on top of config.toml when
you run codex --profile toolkit (or -p toolkit). Plain codex keeps using
your own account and model.

Profiles as separate files are how current Codex works. Older versions used
[profiles.toolkit] tables inside config.toml, and recent versions no longer
accept those. If --profile is not recognized, update Codex.

The key: export UMGPT_TOOLKIT_API_KEY in your shell profile (~/.zshrc or
~/.bashrc), with the key ITS gave you.

Use gpt-6-luna for the course work, whatever you use for your own
projects.

Tested Oct 5 on Codex 0.160.0: codex exec -p toolkit ran on gpt-6-luna
through the Toolkit provider.
-->

---
layout: default
---

<div class="label">Setup · Aider</div>

# Your copy of Aider

1. Sent out like your other course repos
2. The EGM skills are already inside
3. Open Codex at the repository root

<div class="caption mt-6">Elephant, Goldfish, peanuts and the rest, in <code>.agents/skills/</code>, where Codex looks.</div>

<!--
About a minute.

Each of you gets your own private copy of Aider from the course, the same
way you got the ai-assistant repository: aase-student/aider-<uniqname>,
generated from the template eecs498-aase/aider. The template is Aider at
upstream commit 5dc9490b, the one every line number in this deck cites. We
installed the EGM skill suite in it before sending it out: peanuts,
elephant, goldfish, egm-implement and mean-review, under .agents/skills/,
which is where Codex looks for a project's skills. You do not install
anything. Aider's own CI and release workflows were removed, so pushing
runs nothing.

Clone it, cd into it, and start codex there. Ask it "what skills do you
have?" and the five should come back.

If the course copy has not reached you yet: clone
https://github.com/Aider-AI/aider, then copy the skills in from the
materials repository, as materials/egm/README.md describes
(cp -R materials/egm/skills/* .agents/skills/ inside the clone).
-->

---
layout: section
---

# Learning Aider

## Skills first, then peanuts, then your own questions

---
layout: default
---

<div class="label">Learning Aider · 1 of 3</div>

# The five skills in your repo

| Skill | What it does | What it writes |
|---|---|---|
| **peanuts** | Maps a codebase, bottom up | A README per folder, `PEANUTS.md` |
| **elephant** | A design conversation, no code | A design doc, `ELEPHANT.md` |
| **goldfish** | Fresh reviewers test the doc | `GOLDFISH.md` |
| **egm-implement** | Builds from an approved doc | Code, `IMPLEMENT.md` |
| **mean-review** | A hostile code review | `MEAN-REVIEW.md` |

<div class="caption mt-4">Read each <code>SKILL.md</code> in <code>.agents/skills/</code> before you run it, with its walkthrough in <a href="https://github.com/eecs498-aase/materials/tree/main/egm/walkthroughs">materials/egm/walkthroughs/</a>.</div>

<!--
About 10 minutes. Students read, before running anything.

A skill is a set of instructions Codex follows. If you run one without
reading it, you do not know what it is about to do to your repository or
when it will stop and wait for you. So read all five first. Each SKILL.md
is 120 to 180 lines.

Read each one beside its walkthrough in the course materials repository:
https://github.com/eecs498-aase/materials/tree/main/egm/walkthroughs
There is one walkthrough per skill (peanuts.md, elephant.md, goldfish.md,
egm-implement.md, mean-review.md), each explaining what problem the skill
solves and why it is built the way it is. Read them in that pipeline order.
egm/anatomy-of-a-skill.md, one folder up, explains how a skill file is put
together, if you want that first.

What to look for in each one:
- The description at the top. It decides when Codex picks the skill.
- The hard rules. What it will never do.
- The steps, and where it stops and waits for you. Those stops are your
  review gates.
- The ledger it keeps (PEANUTS.md, ELEPHANT.md and so on). That file is how
  it remembers where it is, and how you see it too. Elephant and the rest
  keep theirs in docs/egm/<feature>/.

Then check Codex sees them: ask "what skills do you have?" and all five
should come back. If one is missing, the folder is not at
.agents/skills/<name>/SKILL.md.

Today you use peanuts and elephant. The other three are for later.
-->

---
layout: default
---

<div class="label">Learning Aider · 2 of 3</div>

# A map with peanuts

```text
Run the peanuts skill on aider/, excluding aider/website. Write one README at a time and stop after each.
```

1. Read the README peanuts wrote
2. Ask Codex to teach you that part
3. Iterate with Codex until the README is clear and right
4. Approve it, then the next one

<div class="grid grid-cols-2 gap-4 mt-4">
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">FOLLOW-UP</div><div class="text-sm italic">"What does base_coder.py do that the other coders inherit? Show me where."</div></div>
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">ITERATE</div><div class="text-sm italic">"Check aider/coders/README.md against the code. Rewrite anything unclear or wrong."</div></div>
</div>

<!--
About 25 minutes. This is where most of the learning happens.

The run: peanuts writes three READMEs, aider/coders/ and aider/resources/
first, then aider/ itself, built from the two below it plus the 40 or so
modules at the top level. Exclude aider/website: it is the documentation
site, and without the exclusion peanuts plans eleven directories instead of
three. Asking for one README at a time keeps each review small enough to
do properly.

Students do not read the code by hand. Codex reads it and teaches them.
The goal is that they know the structure of each folder at least as well as
the README does.

For each README, in this order:

1. Read the README peanuts wrote. It is short.

2. Ask Codex to teach you that part of Aider. What does this file do? What
   calls it? Why is it split this way? Keep asking follow-ups until you
   could explain the folder without the README. The left card is an
   example.

3. Iterate with Codex on the README until it is clear and right, in the
   same session: the right card is a starting prompt. Codex rereads the
   code and rewrites what was wrong, missing or vague. Use what you just
   learned in step 2: if the README would not have taught you that part,
   say so and have Codex fix it. If a change surprises you, ask why.

4. Approve it (tell Codex, or set the status in PEANUTS.md) and let peanuts
   write the next one.

Peanuts' own SKILL.md warns that about half of leaf descriptions are wrong,
because at the leaves the model has only the code. Finding those is the
point: it is how you learn the codebase. The checked READMEs are what every
later session, yours or Codex's, starts from.

The next slide is the same habit without the README: ask Codex about
anything in the code.

Not rehearsed on Luna. The plan (three directories) was checked on Oct 5.
base_coder.py is the base class the edit-format coders in aider/coders/
subclass, so the follow-up example has a real answer.
-->

---
layout: default
---

<div class="label">Learning Aider · 3 of 3</div>

# Codex as a teacher

<div class="text-2xl mt-2" style="color:var(--c-ink)">Ask about <em>anything</em>. Codex reads the code to answer.</div>

<div class="grid grid-cols-2 gap-4 mt-6">
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">THE CODE</div><div class="text-sm italic">"Where does Aider build the prompt it sends?"</div></div>
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">THE DESIGN</div><div class="text-sm italic">"Why edit blocks instead of tool calls?"</div></div>
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">THE TESTS</div><div class="text-sm italic">"How do the tests run without a real model?"</div></div>
  <div class="card"><div class="text-xs font-semibold opacity-60 mb-1">WHAT IF</div><div class="text-sm italic">"What breaks if I delete repomap.py?"</div></div>
</div>

<div class="caption mt-4">Keep asking until you could explain it without Codex.</div>

<!--
About 10 minutes, and a habit for any codebase after this.

This is the main way to learn a codebase this size. You do not need a good
question. Ask what you are curious about, and follow up on whatever comes
back. Codex searches and reads the code to answer, and with the peanuts
READMEs in place it starts from a map.

The four cards are starting points, not a worksheet:
- The code: where something happens, and what calls what.
- The design: why it was built this way, and what the alternative was.
- The tests: how Aider checks itself.
- What if: what depends on what.

Two habits make it work. Ask for file names and line numbers, so you know
where things live. And ask follow-ups at your own level: "explain that like I have never seen this code", "draw it as a
diagram", "ask me three questions about it and tell me what I got wrong".

Answers for staff, at commit 5dc9490b: the system prompt is built in
fmt_system_prompt (base_coder.py:1174) and the messages in
format_chat_chunks (:1226). Aider's model replies with edit blocks in text,
one format per model, rather than tool calls. The tests replace coder.send
with a fake (tests/basic/test_coder.py:462). base_coder imports RepoMap
from aider/repomap.py and builds it at :498 to give the model a map of the
repository. The trace reference in notes.md has the rest.
-->

---
layout: section
---

# Designing skills for Aider

---
layout: default
---

<div class="label">Designing skills for Aider</div>

# Skills in Aider

<div class="text-2xl mt-2" style="color:var(--c-ink)">Aider has no skill loader. Today you <em>design</em> one.</div>

<div class="grid grid-cols-2 gap-8 mt-8 items-stretch">
  <div class="card">
    <div class="font-semibold mb-3">Aider today</div>
    <div class="text-sm mb-2">You add SKILL.md by hand</div>
    <div class="text-sm mb-2">The whole body, every request</div>
    <div class="text-sm">Until you drop it</div>
  </div>
  <div class="card">
    <div class="font-semibold mb-3">Claude Code and Codex</div>
    <div class="text-sm mb-2">Only the description, every request</div>
    <div class="text-sm mb-2">The model asks for the body</div>
    <div class="text-sm">Loaded when it matches</div>
  </div>
</div>

<!--
About 2 minutes.

Skills are the first thing we add to Aider. You met them in L10, and they
were your second most wanted topic on the form.

Today in Aider, the only way to use a skill is to add its SKILL.md yourself
with /read-only. Then Aider sends the whole body in every request until you
drop it. That costs tokens and crowds the context.

Claude Code and Codex do it in levels. Every request carries only each
skill's name and one-line description. When the task matches, the model
asks for the body, and only then does it load. That is the "three levels of
loading" from L10.

So the feature: Aider finds skills, tells the model what is available, and
loads one when it is needed. How it does each of those is your design.
-->

---
layout: default
---

<div class="label">Designing skills for Aider</div>

# The rest of today

1. Learn where prompts get built
2. Elephant: decide how it should work
3. Build one small step, with your design

<div class="caption mt-6">No one-shot prompts. You will not finish today, and that is fine.</div>

<!--
The rest of the session, with staff walking the room.

1. Learn first. Ask Codex where Aider builds the prompt it sends and where
   read-only files go in. Check what it says. You cannot design a loader for
   code you have not read.

2. Run elephant in Codex and have the design conversation. You make the
   decisions: where skills live on disk, how Aider finds them, what goes in
   every request, how the model asks for a body, and what happens when two
   skills match. The model can suggest. You choose.

3. Then build one small step against your design. Not "add skills to
   Aider". One prompt per step, and each prompt carries a decision you made.

What we are not doing: one prompt that builds the whole feature. Luna can
probably produce something that runs. It would be Luna's design, and you
would not know why it works.

You will not finish today. That is fine. This is the start of the Aider
work, not a graded project, and what is required comes later.
-->

---
layout: end
---
