---
theme: ../theme
title: 'L18: Many Models, One Harness'
info: |
  EECS 498 AASE, Lecture 18
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# Many Models, One Harness

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

This is Lecture 18, Many Models, One Harness. Lab 5 drove one harness from another program. Today is the other side: one harness, and the many models it may have to talk to. The last few lectures each added a part to the harness: subagents, compaction and the cache, memory, and a knowledge base. Every one of those parts sends requests to a model, and so far nothing has said which model, or how the harness talks to it. By the end you should be able to say what changes when a harness talks to a second provider, what a harness needs to know about each model, and how to choose a model for each job without paying more than you need to.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: About 1 minute. No clicks. Point out the break between the parts, and questions at the end.

Here is today's plan, in two parts. The first part is speaking to many models: how one harness talks to providers that each expect a different shape. Then a break. The second part is choosing a model for each job in a session, and what that choice costs. Then questions.
-->

---
layout: none
class: story
---

# A harness built for one model

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-problem-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-problem-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-problem-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-problem-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Let's start with a harness built the simple way. Say you built your harness against one provider. Every request it sends is in that one provider's format, and today's model answers. That is how most harnesses start, and it is a reasonable way to start. You read one provider's documentation, build the request it describes, parse the reply it sends back, and the loop works. The system prompt goes where that provider wants it, tool calls come back in that provider's shape, and the code that reads them assumes that shape everywhere. Nothing about that is wrong until a second model shows up.

Click. Then more models show up. A newer model arrives every few months, and it is often better at exactly the work your harness does. Another provider's model is cheaper, or it is the one your school pays for, so the people using your harness want to point it there. A model on your own machine costs nothing per token, and some people will not send their code anywhere else at all. Each one expects requests in its own shape. A harness that speaks only the first provider's format cannot use any of them. And a harness with that format written into every part of its loop has a lot of code to change before it can.

Direction: Your story, if you have one: a new model you moved to when it came out, and what you had to change to use it.

Click. And inside one session, the jobs differ. To fix a bug nobody understands, you want the strongest model you have. It has to read a lot of code, keep a lot in mind at once, and reason about what is going wrong, and a weaker model goes around in circles. To write a commit message, you do not. The diff is right there, and the job is to describe it in a sentence. The strongest model can do that, but you wait longer for it and pay more for it, and a session sends many small jobs like that one.

Click. So two questions. How does one harness speak to many models, and which model does each job? Part 1 takes the first. That is translation: the work a harness does so that one conversation can go to any provider, and what that translation cannot carry. Part 2 takes the second. That is routing: choosing a model for each job in a session, and what the choice costs. The two depend on each other. A harness can only send a job to another model if it can speak to that model, and once it can, every job becomes a choice.
-->

---
layout: none
class: story
---

# Part 1: Speaking to many models

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Part one. Speaking to many models.
-->

---
layout: none
class: story
---

# One conversation in two shapes

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-shapes-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-shapes-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-shapes-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-shapes-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

Let's build one short conversation twice, for one provider on the left and another provider on the right. We start where every request starts, with the system prompt. On one provider, the system prompt goes in as the first message, a message like any other, with a role that marks it as the system's. On the other, the system prompt goes in its own field, next to the messages, and there is no system message at all. Send that second provider a system message and the request is rejected. Then your message looks the same on both: a message from you, with your text in it. So far the difference is small, and an adapter could handle it in one line. So what happens when the model uses a tool? That is where the two shapes really part.

Click. The model asks for a tool. Say it wants to read the file where the bug is. One provider sends a tool call with its arguments as a string of JSON text, with the file's path written inside it. The harness has to parse that text before it can use it, and the model does not always write valid JSON, so the harness also has to handle text that does not parse. The other provider sends a tool call block with its arguments as an object, already parsed, with the path as one of its fields. Both mean the same thing: read this file. Even the word for why the model stopped is different. One provider says it stopped for tool calls, and the other says it stopped for tool use. The loop checks exactly that word to decide whether to run a tool, so a loop written for one word misses the other.

Click. Then the harness sends back the result. It has run the read and has the file's contents. One provider wants the result as a message of its own, with a role just for tool results and the id of the call it answers. The other wants the result inside your next message, as a block at the very start of it, paired with its call by the same kind of id. Put the result after some text instead of first, and that second provider rejects the request. Even the ids follow different rules. Each provider has its own limit on how long an id can be and which characters it may hold, so an adapter sometimes rewrites the ids on the way out.

Click. The conversation is the same, but the harness must build each shape exactly, or the provider rejects the request. None of this is a difference in what the model can do. Both models read the same file and get the same contents. It is all bookkeeping: where things go and what they are called. But the bookkeeping is strict. A provider does not guess what you meant. It returns an error, and the session stops until the harness sends a request in the right shape. So if you ever see a harness fail on its very first tool call with a new provider, look here first. The result is probably in the wrong place, or the arguments are in the wrong form.
-->

---
layout: none
class: story
---

# One format and an adapter per provider

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-adapter-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-adapter-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-adapter-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-adapter-3.png" /></template>
</v-switch>


<!--
Direction: About 6 minutes. 3 clicks.

So how does one harness build both shapes? The tempting way is to check the provider everywhere. Wherever the loop reads a tool call, it asks which provider it is talking to and reads the call that provider's way. Wherever it adds a result, it asks again. That works for two providers. With a third, every one of those checks changes, and they are spread through the whole loop, so one is always missed.

The usual answer is to keep the harness's own conversation in a format of its own, and put an adapter at the edge. Let's follow one request out. The loop works on the harness's own conversation, and so do compaction from Lecture 15 and memory from Lecture 16. None of them ever sees a provider's shape. Only at the last step, when the request is about to go out, an adapter for one provider turns that conversation into one provider's request. It moves the system prompt to where that provider wants it, writes the tool calls in its form, and puts each result where it belongs. Then one provider's models answer. Often the harness's own format is a copy of one popular provider's format, so that provider's adapter has almost nothing to do, and the others do the converting.

Click. Now we add more. Add an adapter for another provider and its models, and an adapter for a local server, which runs a model on your machine. Many local servers copy a popular provider's format, so their adapter is small. The rest of the harness does not change. That is the reason to put the adapter at the edge: the code that decides what to send stays the same, and only the code that writes it out knows about providers.

Some harnesses do not write their adapters at all. They use a translation library that already has an adapter for every provider, and they call one function in one format. Others skip adapters a different way: they speak one provider's format only, and work with any provider that offers a copy of it. Either way the translation still happens. It moves outside the harness.

Click. It works on the way back too. Replies and stream events come back in each provider's shape. Let's follow a reply back. It streams in pieces, so you see the text as it is written, and each provider cuts the reply up differently. One sends small chunks, each with a bit of text or a bit of a tool call's arguments. Another sends a typed event for each part of the reply: a block starts, pieces of it arrive, and the block stops. The adapter collects those pieces and turns them into the harness's own shape, so the loop sees one kind of tool call and one kind of stop. Whatever word a provider uses for stopping to call a tool, the loop sees the same word. Usage counts come back in each provider's shape too, and we come back to those in a few minutes, because they are easy to get wrong.

Click. So a new provider is a new adapter, not a new harness. When a provider you have never used appears, the work is one new piece of code at the edge, and every part of the harness above it works with the new provider the first time it runs. That is also a test of a harness's design. Say you are adding a provider to a harness and find yourself editing the loop or the compaction code. Then the boundary is in the wrong place. Keep everything a provider needs inside its adapter, and keep the adapter thin.
-->

---
layout: none
class: story
---

# Reasoning only one provider can read

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-sealed-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-sealed-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-sealed-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-sealed-3.png" /></template>
</v-switch>


<!--
Direction: About 6 minutes. 3 clicks.

If translation were only bookkeeping, the adapter would be the end of the story. But some things do not translate. The first is reasoning. Many models now think before they answer, and a reply from a reasoning model can carry sealed reasoning, its thinking in a form only that provider can read, next to a tool call. Sealed means encrypted. The provider hands the harness its model's reasoning as a block of scrambled text. The harness can store it and send it back, and that is all it can do with it. You may see a short summary of the thinking on screen, but the real thing stays sealed. Say the model spent 3,000 tokens of reasoning working out which file holds the bug, and it ends by asking to read that file. That reasoning is in the reply, sealed, right before the tool call.

Click. Why would a harness keep something it cannot read? Let's follow it into the next request. On the next request to the same provider, in the middle of a tool loop, the harness sends the sealed reasoning back unchanged, after the system prompt and your message, before the tool call and the result. The provider opens it. It reads its own reasoning and carries on. The model remembers why it asked for that file and what it was looking for, so it does not have to work it all out again. Change it or drop it here, and some providers reject the request. One provider rejects a modified block outright. Another fails the request when the sealed part is missing from a step that made a tool call. So the harness's own format needs a place to keep something it cannot read. That is harder than it sounds when the format was copied from a provider that has no field for it. One translation library ends up tucking the sealed part inside the tool call's id, because the id was the one field it could carry through.

Now here is a question to think about before the next click. What should the adapter do with that sealed part when the next request goes to a different provider?

Ask: What should the adapter do with the sealed reasoning when the next request goes to another provider? Take two or three answers before the click.

Click. Now we send the same request to another provider. The system prompt, your message, the tool call and the result all go, translated the usual way. It cannot read the sealed part, so the adapter leaves it out. Passing it along would not help, because the other provider has no way to open it. Turning it into plain text is not possible either, since nobody outside the first provider can read it. And this happens even inside one provider. A different model from the same provider may not be able to read the block, and then the provider drops it on its own.

Click. The new model reasons again from what it can read. Nothing breaks, but that thinking is paid for twice. Reasoning is billed like output. So the 3,000 tokens the first model spent finding the file are paid for once, and then the new model spends its own few thousand to get back to the same understanding. It sees that it asked for the file and what came back, but not why it asked. Usually it works out the why again. Sometimes it reaches a different conclusion, and the task changes direction halfway. So if your harness moves a conversation to another provider in the middle of a tool loop, expect a slower and costlier next step, and now and then a change of plan. Part 2 comes back to this with switching models.
-->

---
layout: none
class: story
---

# What else differs by provider

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-lost-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-lost-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-lost-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-lost-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l18-lost-4.png" /></template>
</v-switch>


<!--
Direction: About 7 minutes. 4 clicks.

More things differ between one provider and another provider, and the adapter handles each. None of these break a request the way a misplaced tool result does. They fail quietly, which makes them worse. Let's take them one at a time, starting with caching. Lecture 15 showed that a long session reads most of its input from the cache, at a tenth of the price or less. One provider caches the start of a request by itself. The harness does nothing, and when the start of a request matches an earlier one, it is read from the cache. Another caches only when asked, so the adapter adds the marks that say where: a few points in the request, each one saying cache everything up to here.

Now say the adapter forgets the marks. What goes wrong? Nothing fails, and every reply comes back fine. Let's put numbers on it. Say each request carries 100,000 tokens, and 90,000 of them are the same start as the last request. With the marks, the harness pays full price for 10,000 tokens and a tenth of it for the other 90,000, the same as paying full price for 19,000. Without them, it pays full price for all 100,000. So forgetting the marks makes every request about five times as expensive, and the only place it shows is the bill.

Click. Second, tool schemas. Every tool the harness offers comes with a schema, the description of its arguments that the model fills in. One provider takes most schemas. Another rejects some schema parts, so the adapter rewrites the schema before it goes out. Say your harness has a tool that reads part of a file, and its schema says the number of lines must be at least 1. One provider accepts that limit. Another, in its strict mode, rejects the whole request because of it. A third takes only a subset of the schema language, so its adapter strips out whatever that subset lacks. Removing a limit is safe only if something else enforces it, so the harness checks the value itself when the call comes back. This matters most for tools you did not write. A tool from an MCP server arrives with whatever schema its author wrote, and the adapter has to make it acceptable to whichever provider is on the other end.

Click. Third, built-in tools. One provider has a built-in tool, like a web search that runs on its own side. The harness does not run that search. It asks the provider to make the tool available, and the search happens inside the provider before the reply comes back. Another has nothing like it, so the adapter leaves it out. That is a real loss. Say you rely on that search to look up a library's current documentation. On the other provider the model has no search, so it answers from what it already knows, which may be out of date. When a capability matters to your work, the harness can offer its own version as an ordinary tool. That works the same on every provider, because the harness runs it.

Click. Fourth, token counts. Every reply says how many tokens it used, and the harness adds them up to show you a total and a cost. One provider's input count includes cached input. Another lists cached input apart. Take the same request as before: 100,000 tokens of input, 90,000 of them from the cache. The first provider reports 100,000 input tokens, and inside that, 90,000 cached. The second reports 10,000 input tokens, and next to that, 90,000 read from the cache. Which one is right? Both are. Now say the harness reads the second provider's count the first provider's way. It thinks only 10,000 tokens went in. Or it reads the first provider's count the second way, adds the cached tokens on top, and thinks 190,000 went in. Reasoning tokens have the same problem: one provider counts them inside the output, and another counts them separately. The adapter counts one way, or every total the harness shows is off. The fix is to turn every provider's numbers into the harness's own meaning inside the adapter, so the rest of the harness never has to know.

Click. Translation carries the conversation but not every feature. Whatever a provider does on its own side stays there: its cache, its built-in tools and its sealed reasoning. The messages move from one provider to the next. What the provider keeps or runs for you does not. That gives you a practical rule. Before you move a harness to a new provider, list what the old provider was doing for you, and check each item on the new side. The conversation will arrive. The rest depends on the adapter, and some of it cannot come along.
-->

---
layout: none
class: story
---

# A profile for each model

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-profile-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-profile-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-profile-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-profile-3.png" /></template>
</v-switch>


<!--
Direction: About 6 minutes. 3 clicks.

Adapters handle providers. But models differ even on the same provider, so the harness keeps a profile for each model. Its profile starts with limits and money: the context window, the longest reply, and the price per token. Each of these is used somewhere we have already seen. The context window decides when compaction runs, from Lecture 15. Say the profile says 1 million tokens, but the model holds 200,000. The harness keeps adding to the conversation, never compacts, and the provider rejects the first request past 200,000. The other way around, a profile that says 200,000 for a model that holds a million compacts five times too early and throws away context the model could have kept. The longest reply matters when the model rewrites a whole file: if the limit is shorter than the file, the reply is cut off partway through. And the price per token, for input, cached input and output, is how the harness turns token counts into a cost.

Limits and money are the easy part. Before the next click, think about what else the harness would need to know about how a model behaves.

Ask: What else does the harness need to know about how a model behaves, beyond its limits and its price? Take a few answers before the click.

Click. Then behavior. The best edit format, since some models do better rewriting a whole file and others sending only the changed lines. Say you change three lines in a 600-line file. Rewriting the whole file costs about 600 lines of output, several thousand tokens. Sending only the changed lines costs a few dozen. So the changed lines are far cheaper, but only if the model writes them correctly. A model that garbles that format produces edits the harness cannot apply, and the retries cost more than the whole file would have. So the profile picks the format for each model, from what has worked. Next, prompt changes the model needs: some harnesses add a line to the system prompt for one model, and some keep a whole system prompt written for each model. And reasoning and image support: whether the harness may send an effort setting, which Part 2 covers, and whether it may send a screenshot or has to leave it out.

Click. The harness reads the profile before it builds each request. That step connects the profile to everything else. Let's follow one request. The harness looks up the model and reads its window to decide whether to compact first. It picks the model's edit format and its prompt, leaves out the image if the model cannot take one, and only then hands the conversation to the adapter. Profiles come from a few places. The harness ships a list of them, some harnesses update that list from the internet, and you can add your own in a settings file. When you point a harness at a model it has no profile for, it falls back to guesses, and the good ones say so. One harness prints a warning that it is using fallback settings for an unknown model. If you see a warning like that, take it seriously, because the window, the price and the edit format are all guesses now.

Click. So a new model needs a profile, and a new provider also needs an adapter. That is what keeping up with new models means inside a harness. When a new model comes out, most of the work is its profile. The window and the price are quick to fill in. The slower part is finding out which edit format the model is good at and what its prompt needs, and that takes trying it on real tasks. A new provider adds an adapter on top, written once, and every model from that provider uses it after that. So when a harness supports a new model on the day it comes out, someone wrote a profile for it, and if the provider was new, an adapter too.
-->

---
layout: none
class: story
---

# Chat break

<img class="story-still" src="./assets/slides/deck-chat-0.png" />


<!--
Direction: Five minutes. The question is open; the instructor decides it.

Chat break. Back in five minutes.
-->

---
layout: none
class: story
---

# Part 2: Choosing a model for each job

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Part two. Choosing a model for each job.
-->

---
layout: none
class: story
---

# One model for every job

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-one-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-one-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-one-2.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 2 clicks.

Part 1 made every model reachable. Now, which one should do each job? Start with the simplest plan: the strongest model for every job. To fix a bug nobody understands, it works. To name the session or write a commit message, it is slow and costly. Let's put prices on it. Say the strongest model costs five dollars per million input tokens and twenty-five dollars per million output tokens, and a small model costs a fifth of that, one dollar in and five out. These are round numbers, and the real gap between a provider's strongest and smallest models runs from a few times to more than twenty times. A commit message reads a diff of about 3,000 tokens and writes one sentence. On the strongest model, that is about a cent and a half. On the small one, about a third of a cent. One commit costs little either way. But side jobs run all session long. Say the harness checks every command before it runs, at 2,000 tokens a check, a hundred times in a session. That is 200,000 tokens: a dollar on the strongest model and twenty cents on the small one. And you wait for each check before the command runs, and the strongest model is the slowest to answer.

Click. The opposite plan is the cheapest model for every job. Asked to name the session or write a commit message, it works. Asked to fix a bug nobody understands, it fails. And why is failing expensive? The model reads files, tries a fix, runs the tests, and tries another fix, and every one of those round trips is paid for. Say it spends forty round trips going in circles before you give up. You have paid for all forty, and then you start again on the strong model, which now has to undo the wrong fixes as well. The cheap model was cheaper per token and more expensive per bug.

Click. So give each job the cheapest model that does it well. Both halves count. Cheapest, because most jobs in a session are small, and a small model does them fine. Does it well, because a model that fails costs more than a model that costs more per token. The rest of this part is how harnesses apply that rule: which jobs go where, who decides, and what it costs to change your mind halfway through a task.
-->

---
layout: none
class: story
---

# The jobs inside one session

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-jobs-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-jobs-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-jobs-2.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 2 clicks.

A session holds more jobs than it seems. From where you sit, there is one conversation: you type, and the model answers. The main loop, where the model reads code and makes changes, goes to a strong model. That is the job you see. But the harness sends other requests too, and you have met many of them already in this course. So before the next click, think back over the last few lectures. Besides the main loop, what other requests does a harness send to a model?

Ask: Besides the main loop, what other requests have you seen a harness send to a model in this course? Take answers until the room has named three or four, then click.

Click. Around it are side jobs. Naming the session, so it has a title in your list of sessions. The commit message. A summary for compaction, from Lecture 15. A memory note, from Lecture 16. Checking a command before it runs, from Lecture 11. A subagent's search, from Lecture 14. Each of these can go to a smaller model, and harnesses send many of them there. The title and the commit message almost always go to a small model. Memory is often split: one harness pulls notes out of each session with a small model, and merges them with a bigger one. The command check goes to a fast model, because you wait on it every time.

Some of these are less automatic than you might expect. In at least one harness, the built-in search subagent runs on the main model, and if you want it on a cheaper one, you define that subagent yourself. And the compaction summary has a catch. It reads the whole conversation, and the cache belongs to the model that built it. Say the conversation is 150,000 tokens and the summary is 5,000. On the main model, reading the conversation from the cache costs about 8 cents, and writing the summary about 12, so 20 cents in all. On a small model with no cache, the reading costs 15 cents and the writing about 3, so 18. The two come out close. So check what your harness does before assuming the small model is the cheap path.

Click. That works because each side job is its own request, with its own prompt and its own model, and it never touches the main conversation. Let's follow one. The harness writes the commit message request from scratch: a short system prompt that says to write a commit message, and the diff. It sends that to the small model and takes the sentence that comes back. The main conversation never sees the request or the reply. Lecture 14 made the same move for subagents: a separate conversation that returns only its result. Because it is separate, a side job can also fail safely. One harness tries the small model first, and if the diff is too big for its window or nothing comes back, it tries the main model instead. The main loop never knows.
-->

---
layout: none
class: story
---

# A planner and an editor

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-roles-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-roles-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-roles-2.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 2 clicks.

Routing can also split one job in two. Some models reason well about code but write edits in the harness's format unreliably. Others follow the format well but plan poorly. So why not use both? From your request, a strong model plans the change in words. It reads the code and says what to change and why: in this function, check for an empty list before the loop, and update the two places that call it. It does not have to get any edit format right. It only has to be right about the change.

Click. Then a second model writes the edits in the harness's format, and the harness applies them. The second model gets the plan and the files, and its whole job is to turn the plan into edits the harness can apply. In one harness, the editor sees only the plan, not the conversation that led to it, which keeps its request small and its job narrow. The second model can be a smaller one, or the same strong model with a different prompt and a different format.

Click. Each model does the part it is best at. One reasons about the change, and the other follows the edit format, reliably and for less. There is a cost: two requests instead of one, so each change takes longer, and if the editor is not cheaper, it costs more too. So the split pays off when the strong model's edits keep failing to apply, or when the plan is short and the edits are long. Another harness uses the same split across a whole task. It plans on its strongest model, and once you approve the plan, it carries the plan out on a cheaper one. If you have approved a plan and then watched a harness carry it out, you have seen a planner and an editor at a larger scale.
-->

---
layout: none
class: story
---

# Effort as a dial

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-effort-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-effort-1.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 1 click.

There is also a dial on one model. Many reasoning models take an effort setting, which says how long to think before answering. Low effort for a quick rename: a little reasoning, then the answer. Medium effort for an ordinary change. High effort for a bug nobody understands. Let's put numbers on it, with the same prices as before. Say low effort reasons for a few hundred tokens, and high effort for 20,000. At twenty-five dollars per million output tokens, the quick rename's reasoning costs a small fraction of a cent. The hard bug's reasoning costs 50 cents for one reply, and a tool loop has many replies. Effort costs time as well: 20,000 tokens of thinking is a long wait before anything happens on your screen.

Direction: Your story, if you have one: a task where high effort was worth the wait, or one where it was wasted.

Click. Reasoning is billed like output, so effort costs time and money. The harness can set it per job, the same way it picks a model. A subagent can have its own effort, and so can a side job. So there are two dials: which model, and how hard it thinks. Is turning up the effort the same as picking a stronger model? Not quite. A small model at high effort still knows less than a strong one, and a strong model at low effort answers quickly on easy work. The levels differ between models, and when a harness asks for a level a model does not have, one harness drops to the nearest level below it. For your own work, a reasonable habit is to leave the default for ordinary changes, turn it up when the model keeps missing something, and turn it down for quick edits where you wait on every answer.
-->

---
layout: none
class: story
---

# Who picks the model

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-who-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-who-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-who-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-who-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Who makes the choice? There are four answers, and most harnesses use more than one. First, you. You switch models with a command. You know the task better than anyone, so you are the best judge at the start: you can see that a bug is hard before the model has read a line. But you have to remember to do it, and you are usually thinking about the bug, not about the model.

Click. Then the harness. A rule sends each job to a model, the way side jobs go to a smaller one. Commit messages go to the small model, and the main loop goes to the model you picked. Rules are predictable: you can read the settings and know where every job goes. But a rule does not look at the request, so a hard commit message and an easy one go to the same model.

Click. Then the main model. When it hands work to a subagent, it picks the subagent's model. The main model has read the task, so it can judge that a search through the code is simple and send it to a small model, or that a review needs a strong one. In one harness, the model named at the handoff comes first, then the subagent's own settings, and when neither names one, the subagent runs on the main model.

Click. Last, a router. A small model reads the request and picks which model answers it. One research project trained routers to choose between a strong model and a weak one for each request, and cut costs by more than half on some tests while keeping the answers about as good. One chat product puts a router in front of its models for every conversation. The risk is the router's mistakes. A hard request sent to the weak model fails, and you may not know a router was involved. The coding harnesses in this course do not route each request this way. They route by job, with rules, and leave the main model to you.
-->

---
layout: none
class: story
---

# Switching models mid-task

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-switch-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-switch-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-switch-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l18-switch-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

The main model changes in the middle of a task in two ways. You switch models, because the current one is stuck, or because you want a cheaper one for the easy part. Or the harness falls back when one is overloaded: the provider says it cannot take the request right now, and the harness sends it to a backup model instead. In one harness the fallback lasts only for the current turn, and your next message tries the main model again. It does not fall back on a rate limit or a billing error, only when the model itself is unavailable.

Direction: Your story, if you have one: a time you switched models halfway through a task, and what happened next.

Click. Either way, the conversation so far goes to the new model: the system prompt, your messages, replies, results and sealed reasoning. Because the harness keeps its own format, this part is easy. The adapter for the new model builds the request the same way it builds any other. One harness also adds a short note telling the new model that a different model was working before, followed by the new model's own instructions. So the new model sees the whole conversation. The question is what it does not get. Before the next click, think back to Part 1. Even with the whole conversation, what is lost?

Ask: What does the new model lose, even with the whole conversation? Take a few answers. Part 1 gave the room everything it needs to answer.

Click. Three things are lost. The cache belonged to the old model, so all of it is full price. Let's price it. Say the conversation is 150,000 tokens, and the new model costs the same per token as the old one, five dollars per million. On the old model, the next request would have read those 150,000 tokens from the cache at a tenth of the price, about 8 cents. On the new model, the first request pays full price for all of them, plus a quarter more to write the new cache, about 94 cents. One switch costs about twelve ordinary requests. After that, the new model's cache is warm and the cost drops back.

The sealed reasoning is left out, so the new model reasons again from what it can read, as Part 1 showed. And a smaller window may force compaction first. Say you switch from a model that holds a million tokens to one that holds 200,000, with 300,000 in the conversation. It does not fit, so the harness has to compact before the new model can answer. In one harness, the old model writes that summary, because it is the one that can still read everything.

Click. So keep the main model steady and send side jobs to other models. Side jobs are separate requests, so their model never touches the main conversation's cache or its reasoning. Switch on purpose, between tasks. When a task is done, the next one usually needs little of the old conversation, and if you start fresh, the switch costs nothing. Switching in the middle of a hard task is sometimes right, when the current model is stuck. Just know that you pay for a cold cache and a fresh start on the reasoning, and expect the new model to take a few steps to catch up.
-->

---
layout: none
class: story
---

# What a session costs

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l18-cost-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l18-cost-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l18-cost-2.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 2 clicks.

Last, money. Each reply's usage counts the tokens, already counted one way by the adapter: input, input read from the cache, and output and reasoning. The harness multiplies each count times its price from the profile, and keeps a total. Let's work through one session. Say it is 60 round trips. The requests grow as the session goes, and they average 80,000 tokens of input, 90 percent of it read from the cache. Each reply writes 1,000 tokens of output and reasoning. With the prices from before, the fresh input is 480,000 tokens, two dollars forty. The cached input is 4.3 million tokens at a tenth of the price, about two dollars twenty. The output is 60,000 tokens, a dollar fifty. That is about six dollars for the session. A harness shows a figure like that as you go, and treat it as an estimate. The harness computes it from list prices, and your bill can differ.

Direction: Your story, if you have one: what a long session of yours cost, if you have looked.

Click. Where a long session's money goes: mostly input, sent again on every round trip, as Lecture 15 showed. Output is small next to it. In our session, input is about four and a half dollars of the six, three quarters of the cost, even with the cache doing its work. Without the cache, the same 4.8 million tokens of input at full price would be twenty-four dollars, and the output would be a dollar fifty out of more than twenty-five. The one exception to watch is reasoning. At high effort, output can grow until it matters.

Click. So there are two savings. Caching makes the repeated input cheaper, and a smaller model makes side jobs cheaper. Let's add side jobs to our session. Say it sent 30 of them, titles, command checks and a commit message, at 5,000 tokens in and 200 out each. On the strongest model, they cost about 90 cents. On the small one, about 18. So in our session the cache saved about twenty dollars, and moving side jobs saved less than a dollar. Moving side jobs still matters, because it makes each one faster, and you feel that on every command check. So when a session costs more than you expected, where should you look first? Check that the cache is working, and then look at where the side jobs go.
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
Direction: About 4 minutes. 4 clicks.

Keep one format and translate at the edge. The loop, compaction and memory all work on the harness's own conversation, and only the adapter knows a provider's shape. So a new provider is a new adapter. If you find yourself checking the provider in the middle of the loop, move that check to the edge.

Click. Some things do not survive translation. Sealed reasoning stays with the provider that made it, the cache belongs to the model that built it, and a built-in tool stays where it runs. When you move work to another provider or another model, check those first, because they fail without an error.

Click. A profile says how each model works: its window, its longest reply, its price, its edit format and its prompt. The harness reads it before every request. When a harness warns that it does not know a model, believe the warning, and give the model a profile.

Click. Give each job the cheapest model that does it well. Keep the main model steady, send side jobs to smaller models, and switch on purpose between tasks. And look at where the money goes. In a long session it is the repeated input, so the cache saves more than any choice of model.
-->

---
layout: none
class: story
---

# This week

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/deck-week-1.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/deck-week-2.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/deck-week-3.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 3 clicks.

Aider reaches every provider through LiteLLM, a translation library. Every request goes out through one call in one format, and LiteLLM is the adapter for every provider. Aider also tells LiteLLM to drop any setting a provider does not accept, instead of failing. Aider's edits are plain text, not tool calls, so it has less to translate than most harnesses: no tool calls to reshape, and almost no sealed reasoning to carry.

Click. Its model settings are the profile: for each model, the edit format, whether to cache, and which models to pair it with. The settings ship as one long file with hundreds of models in it, and you can add your own in a settings file. For models that need cache marks, Aider adds them only when you turn caching on with a flag, so check that first. Find the entry for the model you use and read it.

Click. Its weak model writes commit messages and summaries of the chat history. Its architect mode is the planner and the editor from Part 2. The weak model is tried first, and the main model if that fails. One thing to know if you work on Aider's code: the weak model's calls are not added to the session's cost, so the total on screen leaves them out.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks. Leave it up while questions start.

Anthropic's Messages API, OpenAI on prompt caching, the RouteLLM paper on routing between models, and the Aider source.
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
