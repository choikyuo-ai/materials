---
theme: ../../lectures/theme
title: 'Lab05: Building on Codex'
info: |
  EECS 498 AASE, Lab 05
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# Building on Codex

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

Lab 5, Building on Codex.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: No clicks.

The problem, the app-server, custom tools and a toy example.
-->

---
layout: none
class: story
---

# A chat bot in Slack

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/card-chatbot-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/card-chatbot-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/card-chatbot-2.png" /></template>
</v-switch>


<!--
Direction: About 1 minute. 2 clicks. After the last click, show Claude's Slack app live.

Here are three systems you might build, one at a time. The first is a chat bot.

Click. Your team mentions the bot in Slack, to ask for a fix or a question about the code.

Click. The bot replies in the thread and keeps working there.
-->

---
layout: none
class: story
---

# The chat bot and its harness

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-chatbot-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-chatbot-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-chatbot-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

Here is how it works inside. On the left is Slack, where your team talks. On the right is your program, and the harness is part of it. Inside the harness is the loop you know: the model calls tools and the shell, the harness runs them and sends back the results, until the job is done. The arrows are everything that passes between Slack and your program.

Click. A mention in a thread arrives from Slack. Your program hands it to the harness as a prompt, with who sent it and the thread around it. That is the channel from L13, with Slack in place of iMessage. Each thread is its own session, so a reply in the thread picks up where it left off.

Click. Then the loop runs. When the model wants to answer, it calls a tool your program gave the harness, send a message, and your program posts a reply in the thread. Claude's Slack app uses the same idea to keep a checklist in the thread and edit it as the work moves along. That tool is your program's own, the same idea as the MCP tools from Lab04. The part you write never decides how many times to call the model, which files to read or when to compact. It turns Slack messages into prompts, and tool calls back into Slack.
-->

---
layout: none
class: story
---

# A review bot on every pull request

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/card-reviewbot-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/card-reviewbot-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/card-reviewbot-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/card-reviewbot-3.png" /></template>
</v-switch>


<!--
Direction: About 1 minute. 3 clicks. After the last click, show CodeRabbit live.

The second system is a review bot.

Click. Every new pull request gets a review: a summary of the change and comments on single lines, without anyone asking.

Click. Each new push gets reviewed again, looking only at what changed.

Click. People ask the bot questions in comments, and it answers in that thread.
-->

---
layout: none
class: story
---

# The review bot and its harness

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-reviewbot-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-reviewbot-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-reviewbot-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-reviewbot-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Now GitHub is on the left. The same harness sits inside your program, running the same loop.

Click. A new pull request arrives as a webhook, a trigger like the ones in L13. Your program starts a session for that pull request alone, so every review starts with a clean context. When more commits are pushed, it goes back to that same session, so the review takes its own earlier comments into account.

Click. A question in a comment, like why is this line a bug, goes into the same session as a prompt. That is the chat bot again, with the pull request as the channel.

Click. Review comments go back out to GitHub. While the loop runs, the model reads the code and runs its checks. Each finding is a call to a tool, post a review comment, and your program turns it into a comment on the right line. Nobody watches this one, so nobody can approve. The limits are set before the run starts: the harness runs the review in a sandbox with the repository cloned, the choice from L09.
-->

---
layout: none
class: story
---

# A website builder in the browser

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/card-builder-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/card-builder-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/card-builder-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/card-builder-3.png" /></template>
</v-switch>


<!--
Direction: About 1 minute. 3 clicks. After the last click, show Lovable live.

The third system is a website builder.

Click. You describe a site in a chat, say a landing page for a club.

Click. You watch the site take shape, in a preview next to the chat that updates as it builds.

Click. The builder publishes when you approve, and the site is live on the web.
-->

---
layout: none
class: story
---

# The website builder and its harness

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-builder-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-builder-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-builder-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-builder-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-builder-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

On the left is the web page, a chat panel on one side and your site on the other. Your program and the harness inside it, with the same loop, are the same shape as before.

Click. A request for a site comes in from the chat, and your program sends it to the harness as a prompt.

Click. While the loop runs, each step as it happens goes back out to the page: a file edit, a command, a screenshot of the preview. That is the stream from L07, shown to a person. Since the person is watching, they can stop it or send a correction.

Click. Some steps wait for a person. Before it publishes, the harness asks, and your program puts the question on the page: may I publish? That is the approval layer from L09. Lovable also asks before it buys a domain, and before a database change that could lose data.

Click. The person says yes, and the work goes on. Publishing is a tool too, along with connecting a domain, changing the database and storing an API key. The model calls them, and your program does the work. A website builder needs more of these tools than a chat bot. Three systems, three different programs, and the part you write in each never needs to know how the loop works.
-->

---
layout: none
class: story
---

# Building a harness or using one

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-build-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-build-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-build-2.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 2 clicks.

Look at the harness in those three diagrams. It was the same every time. So the chat bot, the review bot and the website builder all need the same thing: a harness. One way is building the harness yourself. Then the loop is yours. Tools and the shell. Approvals and the sandbox. Edits. Compaction. Prompt caching. Skills. MCP. Sessions and resume. And keeping up with new models, which arrive every few months and each want their own settings.

Click. That is the problem. All of it is yours to write, test and keep up to date, and none of it is the product you set out to build.

Click. The other way is using a harness that already does it. Codex's harness has every piece on this list, and OpenAI keeps it current. So if it fits your use case, use it, and spend your time on what makes your system different.
-->

---
layout: none
class: story
---

# Part 1: The app-server

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Part one. The app-server.
-->

---
layout: none
class: story
---

# What the app-server is

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-what-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-what-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-what-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-what-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Start with the Codex you already use. When you run codex in a terminal, two parts run. The terminal UI reads what you type and draws the screen. The other part is the harness, and Codex runs it as a server of its own, called the app-server.

Click. The two parts only pass messages. The terminal UI sends your message, and the app-server sends back what happened, piece by piece, and the UI draws it.

Click. Everything else happens in the app-server. It runs the loop, it runs tools in a sandbox, and it saves each thread so you can come back to it. It is also the only part that talks to the model.

Click. So the terminal UI is one client of the app-server. Nothing in it knows how the loop works. It sends messages and draws the answers.
-->

---
layout: none
class: story
---

# Clients of the app-server

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-cli-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-cli-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-cli-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

Here is the terminal UI again, as a client of the app-server, the harness, which talks to the model. In the terminal the app-server can run inside the same process, but the messages are the same.

Click. Codex's other apps are clients the same way. The editor extension, which the app-server was first built for. The desktop app, which runs many tasks side by side. And a container per cloud task, with your repository checked out and the app-server running inside it.

Click. Every Codex app is a client, and yours can be one too. Your program sends the same messages and gets the whole harness behind them.
-->

---
layout: none
class: story
---

# How your program talks to the app-server

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-pipe-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-pipe-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-pipe-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-pipe-3.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 3 clicks.

So how does your program talk to the app-server? Your program starts it as a child process, the way a shell starts a command, and keeps it running for as long as it needs Codex.

Click. Your program writes each request to the app-server's standard input. Here is a real one, trimmed: start a turn on a thread, with your message as the input.

Click. Everything the app-server has to say comes back on its standard output: answers to your requests, and events like this one, an item that just completed.

Click. One line of JSON per message, in both directions. That is the whole wire format. Next, a whole conversation.
-->

---
layout: none
class: story
---

# One conversation over the app-server

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-session-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-session-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-session-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-session-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-session-4.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 4 clicks.

Here is one conversation between your program and the app-server, one line at a time. First your program sets up the connection, and the app-server answers with its name and version.

Click. Your program says: start a thread. The answer is the thread, with its model and its settings.

Click. Then: start a turn with your message, here run the script, then change its greeting.

Click. Now the app-server talks. Pieces of the model's reply stream in as it writes them. Then a command it ran, with its output. Then a file it changed, with the diff.

Click. Finally, the turn is done. That last message says whether it completed, was interrupted, or failed.
-->

---
layout: none
class: story
---

# Threads, turns and items

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-nouns-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-nouns-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-nouns-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-nouns-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-nouns-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

Three nouns run the whole protocol. A thread is one conversation you can resume. It is saved to disk, so your program can come back to it later, or fork it.

Click. A turn runs one request until it is done. One turn can hold several calls to the model.

Click. Inside a turn are items: your message, the model's message, a command, a file change, the final answer.

Click. The next turn on the same thread holds its own items.

Click. And each item starts, streams in pieces, then completes. A program that only waits for the final answer misses the commands and edits, so it collects items as they arrive.
-->

---
layout: none
class: story
---

# An approval in the other direction

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-approval-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-approval-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-approval-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-approval-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-approval-4.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 4 clicks.

Approvals reverse the direction. Your program is on the left again. First the app-server announces the item: a command it wants to run.

Click. Then it sends your program a request of its own: may I run it? And it waits, for as long as your program takes.

Click. Your answer is one of four. Accept. Accept for the session. Decline and keep the turn going, so the model hears no and tries something else. Or cancel and stop the turn.

Click. Then the command ran, and its item completes with the output.

Click. A file change asks the same way, but its diff came in the earlier item, so your program matches the two by the item's id. Whether Codex asks at all is a setting on the thread: never, when the model decides it should, or for anything not known to be safe.
-->

---
layout: none
class: story
---

# Steering and stopping a turn

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-steer-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-steer-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-steer-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

On the app-server, a turn in progress can still be changed.

Click. Your program adds input mid-turn: also update the README. Codex folds it into the running turn. The request has to name the turn it expects, so a message never lands in the wrong one.

Click. Or it says stop, and the turn in progress ends as interrupted.
-->

---
layout: none
class: story
---

# What each side keeps

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-split-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-split-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-split-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

So who keeps what? Codex keeps the loop, tools and the sandbox, edits, compaction and caching, skills and MCP, and sessions.

Click. Your program keeps where people talk to it, your own tools, who approves, memory and schedules. Those are the parts that changed from the chat bot to the review bot to the website builder.

Click. The protocol is the line between them. OpenAI's own summary: your application owns the product context, the business rules and the tools, and the app-server provides the loop and the sandboxed execution.
-->

---
layout: none
class: story
---

# Part 2: Custom tools

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Part two. Custom tools.
-->

---
layout: none
class: story
---

# A custom tool call

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-tools-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-tools-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-tools-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-tools-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Your program can give the app-server tools of its own. You start a thread with your tools, and each tool has a name, a description and an input schema, the shape of the arguments it takes.

Click. When the model wants one, the app-server sends your program a request: call this tool with these arguments.

Click. Your program runs it, in its own code, and sends back the result.

Click. The model reads the result and keeps going, with your tools next to Codex's own shell and edits.
-->

---
layout: none
class: story
---

# Custom tools and MCP

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-mcp-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-mcp-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-mcp-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-mcp-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-mcp-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

A custom tool works the same way as an MCP tool from Lab 4. Start with the tool list. The harness has tools of its own, like run a command and edit a file, and it runs those itself.

Click. An MCP server is a program of its own. When the harness connects to it, the server sends its tools, and the harness adds them to the list, here search the docs. The harness did not change. Its list grew while it was running.

Click. Your program does the same thing. When it starts a thread, it sends your tools, and look up a ticket joins the same list.

Click. Now the model picks one of the new tools. The harness cannot run it, because the code is somewhere else. So it sends a call to whoever has the tool, waits, and passes the result back to the model. To the model, all four tools look alike.

Click. So both run outside the harness, and the harness only calls them. The one difference is who runs the code. An MCP server is its own program, so any harness can use it. A custom tool lives in your program, so it can reach your program's state, like the chat it was asked in.
-->

---
layout: none
class: story
---

# What you cannot change

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-cost-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-cost-1.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 1 click.

Custom tools add to Codex. They do not change it. From its side of the protocol, your program cannot rewrite a tool call, edit the history, change how it compacts, or see the final request to the model. Codex builds that request inside.

Click. When you need that control, build your own loop, the way Aider does.
-->

---
layout: none
class: story
---

# Part 3: A toy example

<img class="story-still" src="./assets/slides/card-part3-0.png" />


<!--
Part three. A toy example.
-->

---
layout: none
class: story
---

# A toy chat bot on Codex

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-toy-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-toy-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-toy-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks.

The toy is fixbot, the chat bot from the start. A teammate writes in team chat. Fixbot is your program. It runs the app-server, and the app-server talks to the model.

Click. Fixbot keeps one thread per chat, so each chat is its own conversation. It adds a custom tool named get_ticket, which reads a ticket from the team's tracker. And approvals are asked in the chat.

Click. Codex does the rest, and fixbot is one short Python file. It lets commands run inside the sandbox, and it asks a person before it changes code.
-->

---
layout: none
class: story
---

# One request through fixbot

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-toyrun-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-toyrun-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/lab05-toyrun-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/lab05-toyrun-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/lab05-toyrun-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

One request, start to finish, between team chat, fixbot and the app-server. A teammate writes: fix ticket 7. Fixbot starts a turn with the message.

Click. The model wants the ticket, so the app-server asks: call get_ticket with id 7. Fixbot looks it up and answers with the ticket's text.

Click. The model edits the file. Before the change lands, the app-server asks: may I make this change? Fixbot posts the question and the diff in the chat.

Click. The teammate says yes, and fixbot answers accept.

Click. Then the turn is done, and fixbot posts the final message in the chat. The teammate never saw Codex. They talked to fixbot, and Codex did the work.
-->

---
layout: none
class: story
---

# The code behind fixbot

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/lab05-toycode-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/lab05-toycode-1.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 1 click. Do not read the code aloud.

Here is the custom tool in code. A name, a description and an input schema, passed in the dynamic tools field when fixbot starts the thread. The same request sets the approval policy and the sandbox.

Click. And here is the loop that answers Codex, trimmed a little. It reads one message at a time. A tool call: look up the ticket and send the result. A command approval: accept, since the command runs inside the sandbox. A file change approval: ask the chat, then accept or decline. A finished message: keep it. And when the turn is done, post it to the chat. That loop is the whole integration.
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
Direction: About 2 minutes. 4 clicks.

Most systems can use an existing harness. The chat bot, the review bot and the website builder each needed the same harness underneath, so if one fits, use it and spend your time on what makes your system different.

Click. Codex's harness runs as the app-server. The terminal, the editor, the desktop app and your own program are all clients of it, talking to it in lines of JSON.

Click. Your program never needs to know the loop. It starts threads and turns, reads the items as they come back, and answers when the app-server asks for an approval or a tool call.

Click. Custom tools plug in like MCP tools. They join the harness's tool list, they run outside the harness in your program, and the harness calls them when the model picks one.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks.

OpenAI's posts on unlocking the Codex harness and on Codex as a platform, the app-server docs, and the Codex source.
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
