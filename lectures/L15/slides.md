---
theme: ../theme
title: 'L15: Compaction and Prompt Caching'
info: |
  EECS 498 AASE, Lecture 15
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# Compaction and Prompt Caching

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

Today is about the conversation a harness sends the model: what happens when it no longer fits, and what makes sending it again affordable.
-->

---
layout: none
class: story
---

# What each request carries

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-resend-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-resend-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-resend-3.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

Start with what L07 showed. Every turn, the harness builds a request and sends it to the model. Request 1 holds the system prompt, which the harness wrote, then your message. The colors hold all lecture: amber is the harness, rose is you, blue is the model, green is your computer, and gray is a mix.

Click. The model keeps nothing between requests. So request 2 is request 1 again, plus the model's tool call and the result from your computer.

Click. Request 3 is request 2 again, plus another call and result. The harness resends the whole conversation every time, and that raises today's two questions. What happens when it no longer fits? That is Part 1. Who pays to process all of it every time? That is Part 2.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: About 30 seconds. No clicks.

Here is today's plan, in two parts. The first part is compaction: what the harness does when the conversation no longer fits in the window. Then a break. The second part is prompt caching: what makes resending the whole conversation every turn affordable. Then questions.
-->

---
layout: none
class: story
---

# Part 1: Compaction

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Direction: About 15 seconds. No clicks.

Part one. Compaction.
-->

---
layout: none
class: story
---

# A window that fills

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-fill-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-fill-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-fill-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-fill-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Here is a 1 million token window, empty. Frontier models read that much now, and it sounds like room for anything.

Click. Every request starts with the system prompt, then your task. Say the task is to fix a slow upload.

Click. Then the work: tool calls, results and replies. The model reads one file after another, and every result stays in the conversation, because the harness resends all of it.

Click. A long day of work fills even a million tokens, and then the next request does not fit. L14 showed what a long conversation costs well before that.
-->

---
layout: none
class: story
---

# Four ways to handle a full window

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-options-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-options-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-options-3.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-options-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

The harness has four ways to handle a full window. It can stop with an error. That is what the API does on its own: a request longer than the window is refused, and the session is over.

Click. It can drop the oldest messages. Simple, but the oldest message is your task, so the model forgets what it was asked to do.

Click. It can clear old tool results. Tool output is the bulk of a coding session, and most of it is stale once the model has used it, so the harness swaps each old result for a short placeholder.

Click. Or it can summarize and start over, keeping the system prompt, one summary and the latest turn. The first three are the harness alone. This one asks the model for help, and it is called compaction.
-->

---
layout: none
class: story
---

# How a compaction runs

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-compact-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-compact-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-compact-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-compact-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l15-compact-4.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 4 clicks.

Here is one compaction, step by step. On the left is the conversation as it stands: system prompt, your task, file reads, replies, your next message.

Click. The harness appends a summary instruction to the end of the conversation, instead of building a new request. It does this before the window is full, because this request needs room too. The choice to append matters again in Part 2.

Click. The model gets all of it.

Click. The model writes a summary, and the harness starts the new conversation with it.

Click. The harness fills in the rest: the same system prompt, the latest messages word for word, a note on files read before, and your next message, the same one that set off the compaction. The note tells the model which files it had open, so it can read them again. The old conversation is gone from every request after this.
-->

---
layout: none
class: story
---

# What a summary keeps

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-keeps-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-keeps-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-keeps-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-keeps-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l15-keeps-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

That summary is all the next request has of the work so far, so the harness's summary prompt asks for what the task needs to carry on. On the left, the conversation: your messages, the model's work, tool output, which is most of it, and the latest messages. On the right, the summary.

Click. Every message you sent, so a rule like "never touch the production config" still applies after compaction.

Click. What was decided, with the code that matters: the fix goes in the upload's retry loop.

Click. The next step, taken from the latest messages, so the new conversation picks up the same task and does not drift.

Click. Most of the tool output is dropped. The files are still on disk, and the model can read them again.
-->

---
layout: none
class: story
---

# What a summary loses

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-lost-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-lost-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-lost-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

So what does the model know afterward? Before compaction, the model knew the exact line to change.

Click. The summary has to be short, so it rounds that off: the fix is somewhere in the upload code.

Click. After compaction, the model knows the place but not which line, so it has to read the file again. Getting an exact detail back costs a read, and a detail nobody reads again is gone.
-->

---
layout: none
class: story
---

# What survives compaction

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-survives-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-survives-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-survives-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-survives-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

So what does survive? The new conversation again: instructions, the summary, recent messages. Beside it, your computer. Some things come back from outside the conversation.

Click. Instruction files, re-read from disk. The harness puts files like AGENTS.md back into every request, compaction or not, because they live outside the conversation.

Click. Files the model can read again. When it needs the exact line, the model asks for the file, and the harness reads it from disk.

Click. Rules typed in chat may not survive. A summary tries to keep your messages, but a rule that has to last belongs in a file. That is L10's four homes for instructions: AGENTS.md is read on every request, and a chat message lives only as long as the conversation.
-->

---
layout: none
class: story
---

# Compaction as a handoff

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-handoff-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-handoff-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-handoff-2.png" /></template>
</v-switch>


<!--
Direction: About 1 minute. 2 clicks.

This should look familiar. In L14, a subagent hands a report back to the main conversation.

Click. Compaction is the same handoff, from the model to itself: the earlier conversation hands a summary to the later conversation.

Click. Each one loses exact details, so read a summary the way you read a subagent's report.
-->

---
layout: none
class: story
---

# Chat break

<img class="story-still" src="./assets/slides/deck-chat-0.png" />


<!--
Direction: About 5 minutes. No clicks.

Chat break. Back in five minutes.
-->

---
layout: none
class: story
---

# Part 2: Prompt caching

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Direction: About 15 seconds. No clicks.

Part two. Prompt caching.
-->

---
layout: none
class: story
---

# The cost of resending

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-repeat-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-repeat-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-repeat-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

Part 1 kept the conversation inside the window. Part 2 is the bill. Here is request 1 again, the system prompt and your message, and each request after it adds a call and a result.

Click. Keep going to request 8. Each request is the last one plus a little more, and the provider runs the model over every token of it before a word comes back.

Click. So the system prompt and your message are processed again in every request, eight times by request 8. On a long session, that repeated work is most of the cost.
-->

---
layout: none
class: story
---

# What the cache keeps

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-cache-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-cache-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-cache-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-cache-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Here are three requests again: request 1 is the system prompt and your message, and requests 2 and 3 each add a tool call and a result.

Click. The fix is for the provider to keep its work. Request 1 is written to the cache: the provider stores the processed start for a few minutes. The harness marks how far to cache, and the model never knows.

Click. Request 2 starts with exactly request 1, so that part is read from the cache instead of processed again. Only the new end is processed.

Click. Request 3 reads all of request 2 from the cache the same way. That is the whole mechanism.
-->

---
layout: none
class: story
---

# How the cache matches

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-cache-match-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-cache-match-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 1 click.

The match has to be exact. The provider reads a request in order: tools, system prompt, messages. When the next request starts with an earlier request byte for byte, that exact prefix is read from the cache.

Click. With one byte changed in the tools, the tools and everything after them miss, even though the rest is identical.
-->

---
layout: none
class: story
---

# What the cache costs

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-cache-cost-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-cache-cost-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 1 click.

Against normal input at full price, writing to the cache costs a quarter more, and reading from it costs a tenth or less. A long session reads most of its input from the cache, so most of it is cheap.

Click. How long it lives: five minutes by default, and every read starts the clock again. That entry costs a quarter more to write. A harness can pay double to write an entry that lives an hour, which is worth it when you step away for longer than five minutes.
-->

---
layout: none
class: story
---

# Request order

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-order-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-order-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-order-3.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-order-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Since the match starts at the front, the harness puts what changes least first. The tools, then the system prompt: the same for every session, so nothing in them names your folder, your name or the date.

Click. Next, instruction files like AGENTS.md: the same within a project.

Click. Then skills and the date: the same within a session. The date changes every day, so it comes late.

Click. Then the conversation, which grows every turn. Claude Code orders its requests in these layers, and Anthropic says it built the whole harness around prompt caching.
-->

---
layout: none
class: story
---

# Adding to the end

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-appends-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-appends-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-appends-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-appends-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

The cache explains a habit you can now spot in any harness. The start of the request, the tools and system prompt and the conversation so far, is read from the cache, so new things go on the end.

Click. A reminder. When a file changes on disk or a mode switches, the harness adds a reminder to the next message instead of editing the system prompt.

Click. A new tool result. Even a tool the model finds partway through the session arrives as a result at the end, so the tool list at the start never changes.

Click. A loaded skill. L10's three levels: a skill's name and description are in every request, and its body arrives only when the model asks, as a tool result at the end.
-->

---
layout: none
class: story
---

# What resets the cache

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-resets-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-resets-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-resets-3.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l15-resets-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

So what resets the cache? A changed tool definition: nothing read. The tools come first, so one changed byte there misses everything.

Click. A different model: nothing read either. Each model has its own cache, so switching models mid-session pays for the whole conversation again.

Click. A fresh subagent starts cold. It has its own system prompt and an empty conversation, so nothing matches the parent's prefix.

Click. A fork reads the parent's cache, because it starts with the parent's exact system prompt, tools and history. That is why L14 found a fork cheaper to start than a fresh subagent.
-->

---
layout: none
class: story
---

# Compaction through the cache

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l15-compact-cache-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l15-compact-cache-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l15-compact-cache-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

Compaction is the last reset, and the one the harness plans for. Before it, the conversation starts with the tools and system prompt. The summary request is that same conversation with the summary instruction appended, so all of it is read from the cache. That is the payoff of appending.

Click. The next request keeps the tools and system prompt and starts the new conversation. The summary and recent messages are new, so they are written to the cache.

Click. The tools and system prompt have not changed, so they are still read from the cache. Compaction is a rewrite the harness plans for: it keeps the start the same and pays only for the new conversation.
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
Direction: About 3 minutes. 3 clicks.

The harness compacts before the window fills, because the summary needs room.

Click. A summary keeps decisions and loses details, so what must be exact belongs in files.

Click. The cache rewards a harness that only appends: stable parts first, new things at the end.

Click. Every edit to the past is paid again: a changed tool, a different model, a summary.
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

Aider summarizes only finished turns. The loop you build runs inside one turn, so its messages are never summarized, and they grow with every tool round.

Click. Each tool round should append. Add the call and its result to the end of the messages, and never rebuild what is already there, and the cache can do its job.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks. Leave it up while questions start.

The Claude Code docs on prompt caching and on what survives compaction, Lessons from building Claude Code, which is Anthropic's post on building it around the cache, and Anthropic's and OpenAI's prompt caching docs.
-->

---
layout: none
class: story
---

# More sources

<img class="story-still" src="./assets/slides/deck-sources-2-0.png" />


<!--
Direction: No clicks.

The Codex source for its compaction prompt, the Aider source for its chat summarizer, and Anthropic's post on effective context engineering.
-->

---
layout: none
class: story
---

# Questions

<img class="story-still" src="./assets/slides/deck-questions-0.png" />


<!--
Direction: The rest of the time. No clicks.

Questions.
-->
