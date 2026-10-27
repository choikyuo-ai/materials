---
theme: ../theme
title: 'L16: Agent Memory'
info: |
  EECS 498 AASE, Lecture 16
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# Agent Memory

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

This is Lecture 16, Agent Memory. Lecture 15 was about what happens inside one long session. Today is about what carries from one session to the next.
-->

---
layout: none
class: story
---

# A correction the model forgets

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-problem-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-problem-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-problem-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-problem-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Monday. The request starts with the system prompt. You say: run the tests. The model runs the full suite, which takes several minutes. You say: no, only the quick tests. From then on it runs the quick tests.

Click. Tuesday's new session brings the same mistake and the same correction. Nothing you taught it on Monday reached Tuesday.

Click. Here is why. Tuesday's first request holds the system prompt and your new message, and nothing from Monday. Lecture 15 showed that the model keeps nothing between requests. A new session goes further: the harness starts a fresh conversation, so Monday's is never sent.

Click. So today's question: how does the model keep what you taught it? The answer is notes. The model writes them, the harness stores them, and the next session reads them back.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: About 30 seconds. No clicks.

Here is today's plan, in two parts. The first part is creating and storing memories: what the model should keep from a session, and where the harness puts it. Then a break. The second part is loading and retrieving those memories in a later session. Then questions.
-->

---
layout: none
class: story
---

# Part 1: Creating and storing memories

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Part one. Creating and storing memories.
-->

---
layout: none
class: story
---

# What to keep from a session

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-keep-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-keep-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-keep-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

What could carry Monday into Tuesday? Each row starts with the system prompt, in yellow. Then your messages in red, marked U. The model's responses in blue, marked R. And files it read in green, marked F. The simplest answer is to resend every session. It grows every session, and it is mostly files and failed tries: file contents that are still on disk, and attempts that did not work.

Click. Next, the system prompt and a summary of each session, the way compaction summarizes in Lecture 15. It is smaller but still grows every session, one summary at a time. And a summary keeps what mattered that day, not what will matter next week.

Click. Or the system prompt and a note of what matters later. Monday's lesson is one sentence, and the note holds just that text: only the quick tests. That is what memory keeps.
-->

---
layout: none
class: story
---

# A note the model writes

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-write-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-write-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-write-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

A harness needs two things before the model can remember. The system prompt with memory rules says where notes go and what is worth saving. And a memory folder on disk.

Click. Now you say: no, only the quick tests. The model notices it was corrected, and its answer includes a tool call to write a note. The note lands in the folder: run only the quick tests. Often that tool is the same file tool the model edits code with. Some harnesses give it a tool just for memory.

Click. Or you ask it to remember. Either way the model is the writer, during the session, at the moment it learns something.
-->

---
layout: none
class: story
---

# What a note holds

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-note-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-note-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-note-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-note-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

The top of a note is one line to find it later: run only the quick tests. A later session sees this line before it opens the note, so the line has to say what the note is about.

Click. Then the fact. Run the quick tests, not the full suite.

Click. Then why: the full suite takes minutes. The why lets a later session handle a case the rule did not cover. If you ask for the full suite on purpose, the model can tell that is not the mistake the note is about.

Click. And how to apply it: after every edit.
-->

---
layout: none
class: story
---

# What is worth a note

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-worth-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-worth-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-worth-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

Monday's session held much more than one correction: the files it read, a bug it fixed, only the quick tests, no merges during demo week, and today's plan. Which deserve a note?

Click. Three are dropped. The files it read: drop it because it is still on disk. A bug it fixed: drop it because git has it. Today's plan: drop it because it only matters today.

Click. Two are kept. Only the quick tests: keep it. No merges during demo week: keep it because the code lacks it. The rule: keep what is true next week and not in the code.
-->

---
layout: none
class: story
---

# Four kinds of note

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-kinds-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-kinds-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-kinds-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-kinds-3.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 3 clicks.

What is kept falls into four kinds. The first is who you are: say, you prefer short replies.

Click. The second is how to work. Only the quick tests is this kind.

Click. The third is the project. No merges during demo week is a fact about the project, not about you.

Click. The fourth is where to look: the dashboard that tracks bugs, the channel where releases are announced. The note says where the thing is, not what is in it.
-->

---
layout: none
class: story
---

# Modifying notes

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-update-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-update-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-update-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-update-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Weeks later, the memory folder holds three notes. Run only the quick tests. No merges during demo week. Releases go out on Fridays. Then you say: and run them before every commit. The model has a new fact to save.

Click. It does not add a second test note. It changes the one it has, which now reads: run only the quick tests before every commit. The original note modified, not a new one beside it.

Click. Later you say: releases moved to Tuesdays. The Friday note is now wrong. It is deleted and replaced with a new note: releases on Tuesdays. Incorrect notes deleted and replaced, so the wrong day is gone.

Click. One note per fact, so two notes never disagree. Before every write, the model checks for a note that already covers it. That rule lives in the system prompt too.
-->

---
layout: none
class: story
---

# When notes get written

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-moments-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-moments-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-moments-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-moments-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l16-moments-4.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 4 clicks.

So far the model notices and writes, and on one session's timeline that can happen at any turn. It is also unreliable: a model deep in a task often does not stop to save. So the harness adds moments of its own.

Click. Each of the next three moments comes from a different harness. Every few turns, the harness gives a copy of the conversation one question: is anything here worth saving? That copy may only add notes. It can never delete one, since nobody reads what it writes.

Click. Before compaction, whenever the context fills, the harness runs one extra turn to save what the summary is about to lose. This harness keeps a log for each day beside its regular notes, and that turn may only add to today's note in the log. It cannot touch the regular notes.

Click. After the session, a second model reads the finished conversation and saves what is worth keeping. This one may rewrite notes, but only the memory folder, with no network and nobody approving.

Click. The pattern is less power when no one is watching. Not less power over the notes, since the writer after the session can rewrite them, but less reach outside memory. A bad note comes back in every later session, so a writer nobody watches can touch memory and nothing else.
-->

---
layout: none
class: story
---

# Notes about you and the project

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-scope-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-scope-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-scope-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

Where do notes live? Some are about you: prefers short answers, explain before editing. These are loaded in every project.

Click. Others are about this project: run only the quick tests, and no merges during demo week. These are loaded only here, so a fact about one repository never leaks into another.

Click. Both are plain files you can open, read and fix. The model should not be the only one who can correct a memory.
-->

---
layout: none
class: story
---

# Chat break

<img class="story-still" src="./assets/slides/deck-chat-0.png" />


<!--
Direction: Five minutes. The question is open; the instructor picks it.

Chat break. Back in five minutes.
-->

---
layout: none
class: story
---

# Part 2: Loading and retrieving memories

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Part two. Loading and retrieving memories.
-->

---
layout: none
class: story
---

# Loading notes back

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-load-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-load-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-load-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-load-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Tuesday's first request, with memory: the system prompt, your instruction files, memory, then run the tests. Memory sits next to the instructions you wrote, before your message.

Click. It comes from the memory folder. The harness reads the notes at session start, here run only the quick tests.

Click. So the model runs the quick tests the first time, and nobody repeats the correction.

Click. Memory loads once, so it never changes during the session, so it stays cached, which Lecture 15 showed is what makes resending cheap. The cost: a note saved today loads tomorrow, or after the next compaction.
-->

---
layout: none
class: story
---

# Too many notes to load

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-index-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-index-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-index-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

A request, months later: the system prompt, then every note, in every request, before your message. Every request pays for notes about other work.

Click. So the harness loads an index with one line per note, and your message follows it. The notes wait in the memory folder until needed. The index line is the first line of each note, the one written to find it later.

Click. Past that, older notes and past sessions are too many even for an index. They are found by search.
-->

---
layout: none
class: story
---

# Loading in three levels

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-like-skill-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-like-skill-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-like-skill-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-like-skill-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l16-like-skill-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

This is a skill in Lecture 10. Its name and description go in every request. The SKILL.md body comes when the task calls for it. References and scripts come only when looked for.

Click. Memory has the same first level: the index with one line per note.

Click. Then the note, read when its line matches the task.

Click. Then a search of older notes, the third level.

Click. So an index line is written like a skill description. It decides whether the rest ever gets read, and a vague line means the note never comes back.
-->

---
layout: none
class: story
---

# Reading one note on demand

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-recall-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-recall-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-recall-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-recall-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Request 1 of a new session: the system prompt, the instruction files, the index, and your question: why only the quick tests? The index line says what to do, not why.

Click. So the model's answer is a tool call: read the test's note.

Click. Request 2 resends everything, read from the cache, and adds the test's note at the end.

Click. Now the model can answer: the full suite takes minutes. One extra request, the same price Lecture 10 showed for loading a skill.
-->

---
layout: none
class: story
---

# Searching older notes

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-search-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-search-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-search-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-search-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l16-search-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

You ask: how do we ship a release? Nothing in the index matches, so the model calls a search tool to search for release. The best results come first.

Click. Release steps: the same word.

Click. Deploy checklist: the same idea in other words. A search by meaning finds it even though it never says release.

Click. Release notes from last year: older notes rank lower, because they are more likely to be out of date.

Click. The model reads the best one or two, the same way it reads a note from the index.
-->

---
layout: none
class: story
---

# A note that goes wrong

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-stale-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-stale-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-stale-3.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-stale-4.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l16-stale-5.png" /></template>
  <template #5><img class="story-still" src="./assets/slides/l16-stale-6.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 5 clicks.

In September the note is saved, with the command that runs the tests.

Click. In October the command changes, and nothing tells the note.

Click. Today the note is wrong, and it loads in every session.

Click. There are three fixes. Check it before acting on it: a note that names a file or a command is checked against the code first.

Click. Fix or delete the note when it turns out wrong.

Click. And the harness can drop notes nobody uses. A note the model has not used in weeks ages out.
-->

---
layout: none
class: story
---

# Facts and methods

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l16-memskill-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l16-memskill-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l16-memskill-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l16-memskill-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Two questions sort what a model knows: who wrote it, and what kind of thing it is. Written by you, a fact that holds every session goes in instruction files, and a method for one kind of task goes in skills.

Click. Written by the model, a fact is memory.

Click. And a method the model learned becomes skills the model writes. After a hard task, some harnesses save the steps as a skill, which then loads only when that kind of task comes up.

Click. All four are read index first, so whoever wrote it, a short line decides whether the rest is ever read. Lecture 17 adds knowledge built from documents.
-->

---
layout: none
class: story
---

# What to take away

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/deck-takeaways-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/deck-takeaways-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/deck-takeaways-3.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/deck-takeaways-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

Memory is notes the model writes for later.

Click. The harness also decides when the model writes.

Click. Load one line per note and read the rest on demand.

Click. Check a note before trusting it.
-->

---
layout: none
class: story
---

# This week

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/deck-week-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/deck-week-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 1 click.

Aider writes no memory of its own. Its chat history file is a transcript, and it comes back only if you ask Aider to restore chat history.

Click. Standing rules go in a --read file, a file of conventions you pass on the command line. That is instructions you write, not memory.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks. Leave it up while questions start.

The Claude Code docs on memory, the Claude API's memory tool, Anthropic's post on effective context engineering, and the MemGPT paper from 2023.
-->

---
layout: none
class: story
---

# More sources

<img class="story-still" src="./assets/slides/deck-sources-2-0.png" />


<!--
The Hermes Agent source, the OpenClaw source, and the Codex source for its memories.
-->

---
layout: none
class: story
---

# Questions

<img class="story-still" src="./assets/slides/deck-questions-0.png" />


<!--
Questions.
-->
