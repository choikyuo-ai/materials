---
theme: ../theme
title: 'L20: How Our Hand-Drawn Lectures Are Built'
info: |
  EECS 498 AASE, Lecture 20
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# How Our Hand-Drawn Lectures Are Built

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

This is Lecture 20, How Our Hand-Drawn Lectures Are Built. Like the last lecture, this one is a case study. We take a tool the course staff use every day, open it up, and look at how it works, why we use it the way we do, and what you could build from it. The tools today are the ones behind every deck and every lecture video in this course: Excalidraw, a drawing app; a canvas server that lets a coding agent draw in Excalidraw and look at what it drew; and excalive, which turns those drawings and one text file into the slides you are looking at and a narrated video of the same lecture. By the end you should be able to explain how an agent checks work that no test can run, because that is the problem these tools keep solving.
-->

---
layout: none
class: story
---

# The running example

<img class="story-still" src="./assets/slides/l20-hook-0.png" />


<!--
Direction: About 2 minutes. No clicks.

Every slide today was built by the tool it describes. The drawings on these slides were written by a coding agent as a file, drawn on a canvas it could look at, checked, and turned into stills and a PowerPoint by excalive. The words you are hearing are the presenter notes in that same file. The video made from these slides has a voice read those same notes while the same drawings build in, click for click. So whenever we describe a step of the pipeline, you can look at the slide in front of you and see the output of that step. Hold on to one question for the whole hour: when the work is a picture or a voice, something you look at or listen to, how does an agent check it? A test can tell you that a function returns the right number. No test tells you that a label is sitting on top of an arrow, or that a voice said a name wrong.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: About 1 minute. No clicks.

Here is the plan. We start with how we got here, from slides nobody liked to videos. Then Excalidraw and how a coding agent draws in it and checks its drawing. After the break, how excalive turns drawings and words into a deck and a video. Then why we use it the way we do, including what the checks still miss. We finish with what you could build on the same pieces, and then questions.
-->

---
layout: none
class: story
---

# Part 1: How we got here

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Part one. How we got here.
-->

---
layout: none
class: story
---

# The decks we started with

<img class="story-still" src="./assets/slides/l20-before-0.png" />


<!--
Direction: About 2 minutes. No clicks. Rafe built these tools and tells this part best; credit him by name.

This is a slide from the deck we gave in Lecture 5, before any of this existed. The term opened on decks like this one, built with Slidev, a tool that turns Markdown into slides, and a custom theme: a monospace title, a small label above it, cards with icons, and a line of small gray text underneath. Most of the words on these slides were written with a model's help, and they read like it. Look at the title: four primitives, not a vibe. That pattern, a thing and then "not" some other thing, is one of the most common tells of model-written text, and these decks were full of it. Five rungs, not a ranking. Repair is an insertion, not a stage. The cards hold definitions in sentences too small to read from the back of the room, and the line at the bottom explains the slide instead of the speaker doing it. The staff member who built today's tools put it plainly: the old lectures looked bad, and it was time for a change. Keep this slide in mind. At the end of this part you will see the same lecture's slide redrawn.
-->

---
layout: none
class: story
---

# Podcasts for prep and review

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-podcast-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-podcast-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-podcast-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-podcast-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-podcast-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

The course's first review tool was audio.

Click. A Brief plays before class. It is a teaser of about ninety seconds, generated from the lecture's overview with Gemini Notebook's audio overview feature, two generated voices talking about what is coming. The idea was that you would arrive knowing the question the lecture answers.

Click. A Deep Dive plays after class. It is the longer companion, made from the lecture's own materials and the cleaned-up transcript of the lecture as it was actually given, so it covers what was said in the room, not only what was planned.

Click. You listen on a walk or a drive. That was the whole appeal. A podcast is review you can do while doing something else: walking to class, on the bus, driving home. You never have to sit down with the slides to go over a lecture again.

Click. Audio has nothing to look at. That is where the idea runs out. These lectures are built around diagrams: the loop, a request with its messages, a timeline of who talks to whom. Take the diagrams away and a lot of what the lecture says goes with them. And a generated podcast gives you almost no control. You hand it sources and a prompt, and it decides the length and the voices. A Brief comes out at about ninety seconds whatever the prompt asks for.

So the podcasts did one part of the job well, review away from a screen, and could not do the part that mattered most for a course built on pictures. Keep both halves of that in mind, because the video keeps the first and fixes the second.

Ask: Who here used the podcasts this term, and what for? Take two or three answers. Expect "on the way to class" and "I tried one and stopped".
-->

---
layout: none
class: story
---

# A new style guide and Excalidraw

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-excalidraw-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-excalidraw-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-excalidraw-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-excalidraw-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-excalidraw-4.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 4 clicks.

So the slides had to change, and the change started in the style guide, the document that says what every slide in this course looks like.

Click. Diagrams carry each slide. In early October the style guide turned diagram-first: if an idea has a shape, a loop, a flow, a set of actors passing messages, the slide draws that shape, and the words become labels on it. A slide that needs a paragraph is two slides, or a picture.

Click. Excalidraw draws them by hand. Excalidraw is a free, open-source drawing app that runs in the browser. It draws like a whiteboard: boxes with slightly wobbly edges, arrows that look sketched, a handwriting font. The staff found it, liked how it looked, and started drawing the course's diagrams in it and dropping them into the Slidev decks. The deck for Lecture 14 still shows that mix, hand-drawn diagrams inside the old Slidev frame.

Click. A drawing is a file in the repo. An Excalidraw drawing saves as a plain text file, which means it lives next to the slides in git. You can see who changed which drawing and when, and you can roll one back. A picture pasted into a slide tool is a blob that nobody reviews.

There were other ways to draw. A model can write Mermaid, a small language for flowcharts, and a tool lays it out for you. That is quick, but you give up control of where things go, and every Mermaid diagram looks like every other one. In Excalidraw every element has a position you choose, so a diagram can follow the course's own rules: the model always on the left, the harness in the middle, time running down the page.

Click. An agent can write that file. This is the part that mattered most for us. The file is JSON, and a model writes JSON well. So a coding agent can draw a diagram the same way it writes code: by writing the file. Part 2 opens that file up and shows how an agent draws in it and then checks what it drew.

Say you want a slide showing one request going from the harness to the model and back. You describe it in a sentence, the agent writes the boxes, arrows and labels as JSON, and the file lands in the lecture's folder next to the notes. If the arrow points the wrong way, the fix is one number in that file, and the diff shows exactly that.
-->

---
layout: none
class: story
---

# A side project in animation

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-animate-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-animate-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-animate-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-animate-3.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 3 clicks. This is Rafe's side project; tell it as his.

Then came a side project that had nothing to do with the course. One of the staff started animating Excalidraw drawings, just because it was fun. The question was simple: what if a drawing did not appear all at once, but drew itself in the way you would draw it on a whiteboard, one piece at a time?

Click. Shapes trace their own outlines. A box does not pop in. Its outline is drawn around, the way a pen would go, and then its fill comes in. An arrow grows from its start to its head, so you see which way the message travels.

Click. Labels write themselves on. Hand-drawn text appears letter by letter, as if someone were writing it. That small effect makes a diagram feel explained rather than shown.

Click. Matching shapes slide into place. When a drawing changes from one step to the next, a shape that is in both steps does not vanish and reappear. It moves, resizes or changes color to where it is in the next step. That is how you show a pointer moving along an array, or a message moving from one lane to another, without redrawing the whole picture. Those three effects, plus a way to step through a drawing, became the renderer at the heart of excalive.

Notice that nothing here was planned as course infrastructure. It was a toy. A lot of useful tools start that way: someone builds a thing because it is fun, and later it turns out to solve a problem they already had. If you have a side project you keep coming back to, it is worth asking what problem in your own work it might quietly solve.
-->

---
layout: none
class: story
---

# From podcasts to videos

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-origin-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-origin-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-origin-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-origin-3.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 3 clicks.

Here is how the two threads came together. On the left, from the podcasts, the idea that a voice reads the lecture, so you can review it without the slides in front of you.

Click. And from the side project, drawings that build step by step, the way a lecturer draws on a board while talking.

Click. excalive joins the two. The idea was: instead of a podcast, make a video. A voice reads the lecture, and while it talks about a step, that step draws in on screen. The voice is the podcast's job and the drawings are the side project's.

Click. Out of one source come two things: a narrated video, and a deck with notes, the deck you are looking at. The first narrated lecture video was Lecture 14, on subagents, on October 15. It looked cleaner than the Slidev deck it was drawn from, so the decks moved to the same format. One thing changed on the way: fully hand-drawn text looked odd on a slide, so the words on the page became a plain rounded font, and only the drawings stayed hand-drawn. Thirteen decks were rebuilt that same day, and eighteen by October 17. There was a lot of iteration after that, and the rest of the lecture is mostly about what that iteration taught us.
-->

---
layout: none
class: story
---

# The rungs slide before and after

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-after-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-after-1.png" /></template>
</v-switch>


<!--
Direction: About 1 and a half minutes. 1 click.

Before. Here is one more slide from the old Lecture 5 deck: five rungs, not a ranking. It is a chart, so it was trying to be a picture, but the labels are tiny, the title is the same "not" pattern, and the gray sentence under it does the explaining.

Click. After. The same idea, redrawn for the hand-drawn deck. Five boxes of rising height, a dashed line where the model starts choosing, and a short plain title. The speaker explains it, and the slide gives you something to look at while they do. That is the whole change in one picture: fewer words, bigger shapes, and a drawing that is the explanation instead of a decoration next to it.
-->

---
layout: none
class: story
---

# Part 2: Excalidraw and how agents use it

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Part two. Excalidraw and how agents use it.
-->

---
layout: none
class: story
---

# A scene as JSON

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-json-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-json-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-json-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-json-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Start with what a drawing actually is. Excalidraw calls a drawing a scene, and a scene file is a JSON document: a few fields saying it is an Excalidraw file, an array of elements, some settings, and any embedded images. This is one element, the first list item on the podcasts slide from a few minutes ago, exactly as it sits in the file. Every element is a flat object like this: what kind of thing it is, where it is, how it looks.

Click. The id is the name steps use. Every element has an id, and here it is i0, item zero. Excalidraw itself barely cares about ids. excalive cares a lot. A slide's clicks are written as lists of ids: click one shows the frame and the title, click two adds i0. And when two steps share an id, excalive treats them as the same thing and moves it rather than redrawing it. Remember this. It comes back in Part 3.

Click. Position as numbers. The x and the y are plain numbers on a page that is sixteen hundred wide and nine hundred tall. That sounds boring, and it is the reason agents can do this work. A program can measure whether text runs past the right margin. A diff in git shows that a label moved from 290 to 310. Compare a slide in PowerPoint, which is a zip of XML nobody reads, or a slide exported as an image, which nobody can edit at all. Text and numbers are what models and programs are good at.

Here is a concrete case. A list item at size 48 that runs past the right edge of the page is a number problem: start position plus measured width is more than fourteen hundred and eighty. The check that catches it never looks at a picture. It reads the JSON, measures the text with the real font, and compares two numbers.

Click. And the seed fixes the wobble. That long number at the bottom is a random seed. To see why it is there, we need to look at where the hand-drawn look comes from.

Ask: What else in this element could a program check without looking at a picture? Take two or three answers. Expect font size against a minimum, the color against the course palette, and text width against the margin.
-->

---
layout: none
class: story
---

# Where the wobble comes from

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-rough-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-rough-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-rough-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-rough-3.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 4 clicks.

Excalidraw draws every shape through a library called Rough.js, which turns a clean geometric shape into strokes that look drawn by hand. Two fields on each element control it. Here is the same rectangle three times. Roughness 0 is clean, almost ruled.

Click. Roughness 1 is the default, a little wobble on every line. Roughness 2 is looser, like a quick sketch. The course uses roughness 1 for drawings and roughness 0 for structure: the list markers and table rules on our slides are clean on purpose, so the page looks ruled and the diagrams look drawn.

Click. The second field is the seed. The wobble is random, and the seed fixes the random numbers. Here are two rectangles that both have seed 7, at roughness 2. They wobble the same way, because the same seed draws the same lines every time. That is why a drawing does not shimmer each time you open it.

Click. Change the seed to 8 and you get a different wobble. excalive uses this on purpose. Its renderer has an option called boil, which gives every element a new seed several times a second, so the lines tremble the way hand-drawn animation does. And it is why an element in a deck has to keep its seed from one step to the next: change the seed and the shape looks redrawn when it should look like it stayed put.
-->

---
layout: none
class: story
---

# One canvas with three ways in

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-canvas-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-canvas-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-canvas-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-canvas-3.png" /></template>
</v-switch>


<!--
Direction: About 3 and a half minutes. 4 clicks.

A model can write the JSON, but it cannot see it, and drawings need looking at. So our agents draw through a canvas server, an open-source project called mcp-excalidraw-server. Everything on this slide runs on your machine. The canvas server is the Excalidraw app plus a small web server. It keeps the current drawing, and a browser tab can open it to show the same canvas live, so a person can watch the agent draw or drag a shape by hand.

Click. There are three ways in, and all three drive the same canvas. The CLI is a command you run in a shell: add these elements, export the file, take a screenshot. An MCP server exposes the same operations as tools, twenty-six of them, for any harness that speaks the protocol from Lecture 13. And plain HTTP is there for any program that wants to call it directly.

Click. The harness uses the CLI. That is a choice we made, and it is worth saying why. The CLI needs no configuration: the agent runs a command from a skill, the canvas server starts itself if it is not running, and the results come back as JSON. It works the same in Claude Code and in Codex, because both can run a shell command. An MCP server would need setting up in every harness separately, and its twenty-six tool definitions would sit in every request whether the session draws anything or not. That is the cost you saw when we compared skills and tools in Lecture 10.

Click. Two things come out. A file, the scene saved as JSON into the repository. And a PNG, a screenshot of the canvas. That PNG is what makes this setup different from Excalidraw's own official MCP server, which is prompt in, diagram out: the model sends a drawing and never sees it. Here the agent can look. One warning from experience: the canvas is shared by everything on its port, so two agents on one canvas clear each other's drawings. Give each agent its own port.
-->

---
layout: none
class: story
---

# How an agent checks its own drawing

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-look-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-look-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-look-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-look-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Here is that loop on a timeline, with the three actors we always draw: the model, the harness, and the canvas server it drives.

Click. The model writes the elements as JSON and says add these shapes. The harness runs the add command, and the canvas server puts them on the canvas. So far this is exactly how the model writes code: it produces text, and the harness acts on it.

Click. Now the part code does not have. The model says show me the canvas. The harness runs screenshot, the canvas server renders a PNG and sends it back, and the harness puts the image in the next request to the model. The model now looks at its drawing. It is good at this. It spots a label overlapping an arrow, text running out of a box, two boxes that should line up and don't. It is the same move as the browser extension in Lecture 13, which takes a screenshot after a click to see what the click did, and the same idea as Lecture 11: check the result of an action instead of trusting that it worked.

Click. So the model says move the label up, the harness sends update, and the model looks again. It does not get tired of the fifth look, which is more than you can say for a person nudging boxes at midnight. The catch is that the loop is only as good as the picture. If the screenshot does not match what the final slide will look like, the model fixes the wrong thing very carefully. And that happened to us.

Ask: If the model can see the drawing, what would you still not trust it to judge? Take two answers before moving on. Expect "whether it is clear to a student" and "whether the diagram is correct as well as tidy".
-->

---
layout: none
class: story
---

# A contact sheet of stills

<img class="story-still" src="./assets/slides/l20-sheet-0.png" />


<!--
Direction: About 1 and a half minutes. No clicks.

The canvas's screenshot reads a centered label's position as its left edge, so it draws every centered label half its width to the right of where the slide will put it. A model looking at that screenshot would carefully fix a problem that is not there. So our agents do not judge a drawing by the canvas screenshot. They judge it by the stills excalive renders for the deck itself, the exact images that become the PDF. This is what they look at: a contact sheet, twenty clicks on one page, from a previous deck. An agent reads a sheet like this the way you would, scanning for a title out of line, a label sitting on an arrow, a click that adds nothing. The rule underneath is general: when an agent checks its work by looking, it has to look at the real output, not a preview of it.
-->

---
layout: none
class: story
---

# Drawing without a browser

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-headless-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-headless-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-headless-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 3 clicks.

One more piece makes all of this run on a machine with no screen. Older versions of the canvas server took a screenshot by asking an open browser tab to draw. Now it renders headless. The scene goes in, and Excalidraw's own export code draws it, running inside jsdom, a browser written in JavaScript. Because it is Excalidraw's own code and not a reimplementation, a rendered frame looks exactly like the app.

Click. Out comes SVG, a vector picture, which is turned into a PNG. No browser window, no display, nobody logged in. That is why the whole pipeline can run on the Mac mini from the last lecture at three in the morning.

Before this existed, rendering meant keeping a browser open somewhere, and a sleeping laptop or a closed tab broke the build. Moving rendering into the same process as everything else is what turned a tool you run while watching into one an agent runs while you are away.

Click. The font files have to be there too, because a headless renderer has no fonts of its own. The canvas server ships its fonts as files. It did not ship Nunito, the rounded font our page text uses, so without help every slide title would come out in a plain substitute. The course's drawing skill carries a Nunito font file and hands it to the renderer. Small detail, and it is the kind of thing you only find by looking at the output.
-->

---
layout: none
class: story
---

# Chat break

<img class="story-still" src="./assets/slides/deck-chat-0.png" />


<!--
Direction: About 5 minutes. No clicks. Have students talk with a neighbor for three minutes, then take two or three answers.

We have seen an agent check a drawing by looking at it. Turn to a neighbor and think about your own harness. What does your harness produce that no test can check? It might be a web page, a summary, an email, a chart, a spoken reply. How could it look at that work itself? Think about what the equivalent of the screenshot would be, and whether it would be the real output or only a preview of it.
-->

---
layout: none
class: story
---

# Part 3: How excalive works

<img class="story-still" src="./assets/slides/card-part3-0.png" />


<!--
Part three. How excalive works.
-->

---
layout: none
class: story
---

# One story file for deck and video

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-story-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-story-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-story-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-story-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

excalive has one input you write by hand, besides the drawings: a story file. This is a piece of the real one behind these slides, the start of the podcasts slide from Part 1.

Click. The heading names the scene, the click and the slide title. The scene is the drawing to use, the number is which build step, and the title after the bar goes on the slide. Every click of every slide is one heading like this, so the file reads top to bottom in the order you will present it.

Click. Under the heading are the presenter notes. When excalive builds the deck, this text becomes the speaker notes for that click, the words the presenter reads from. The lines that start with Direction are for the presenter only: timing, what to click, a story to tell.

Click. And they are the video's words too. When excalive builds the video, the same text, minus the Direction lines, is what the voice reads while that step is on screen. One text, two jobs. So if the notes are good, the video is good, and if the video sounds wrong, the fix goes in the one place both come from. A whole lecture is one of these files, and building its deck takes seconds.

Here is what that means in practice. Say a student points out that a slide says the wrong lecture number. You fix the sentence in the story file, rebuild, and the slide notes, the delivery script and the video's narration all change together. Nobody has to remember that there were three copies of that sentence, because there was only ever one.
-->

---
layout: none
class: story
---

# Build steps and morphs

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-morph-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-morph-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-morph-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-morph-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

The other half of a scene is its build steps. Beside every drawing sits a small file that lists, for each click, the ids of the elements to show. Here is a scene at step 1 with two boxes, a and b.

Click. A step can also name a whole new version of the drawing. excalive compares it with the step before, id by id. Box a is in both, so a slides into place: it moves to its new position instead of vanishing and reappearing.

Click. Box c is new, so c draws in: its outline traces and its label writes on, the effects from the side project.

Click. And box b is gone from the new version, so b fades out. That is the whole animation model: same id moves, new id draws in, missing id fades out. It is why ids and seeds matter so much in the JSON. It is also why the deck's clicks and the video's beats are the same list: a click is a step, and a step is a moment in the video.

Ask: Where else have you seen "match things by id, then work out what changed"? Take an answer or two. Expect React's keys and git's diff.
-->

---
layout: none
class: story
---

# From scene to frame

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-frame-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-frame-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-frame-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-frame-3.png" /></template>
</v-switch>


<!--
Direction: About 3 and a half minutes. 4 clicks.

For the video, excalive has to turn two steps into motion. It starts from two steps, before and after.

Click. It works out every frame between them, thirty per second: where each moving box is at this instant, how much of each new outline is traced, how faded each leaving element is. Each frame goes through the headless renderer from Part 2, the same code the canvas uses, so the video looks exactly like the slides. Out comes a PNG per frame.

To get a sense of scale: a step that takes three seconds to draw in is ninety frames, each one a full picture at the size of an HD screen. An eighty-minute lecture is well over a hundred thousand frames. Rendering every one from scratch would take hours.

Click. The PNGs stream into ffmpeg, the standard open-source tool for encoding video, and out comes the video. One shortcut matters a lot: when nothing is moving, during a hold while the voice talks, excalive renders one frame and reuses it. Most of a lecture video is holds, so a sixteen-minute video renders in a few minutes on the Mac mini.

Click. The voice clips go into ffmpeg too, one per section of the story, laid against the frames so each step's sentence plays while that step is on screen. Which raises the question of where the voice comes from.
-->

---
layout: none
class: story
---

# A voice on your own machine

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-voice-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-voice-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-voice-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-voice-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-voice-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

The voice is not a service. It runs on the same machine as everything else.

Click. Kokoro speaks with no network or fee. Kokoro is a small open-weight text-to-speech model. excalive runs it locally, downloading it once. No account, no per-minute charge, no rate limit, so the pipeline can re-voice a whole lecture at any hour. The trade-off is that Kokoro reads at one even pace and pitch. It sounds clear and a little flat, and no setting fixes that.

Click. A lexicon fixes names it misreads. A text-to-speech model guesses how to say a word it has never seen. A lexicon is a small file that says, for a given word, how to pronounce it, either as a respelling or in the phonetic alphabet. Model names with version numbers are the usual trouble: a name like K2.5 comes out wrong unless the lexicon spells it out as "K two point five".

Click. Each step holds until its sentence ends. This is what keeps picture and voice together. excalive knows how long each section's clip is, so it holds that step on screen until the sentence finishes, then moves on. The picture cannot drift from the voice, because the voice sets the clock.

Click. Only a changed section is voiced again. Each clip is cached by its exact words. Edit one sentence and rebuild, and excalive re-voices only that section. This matters more than it sounds: a lecture goes through many rounds of edits, and re-voicing eighty minutes for every typo would make nobody want to fix typos.
-->

---
layout: none
class: story
---

# Checks before any render

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-checks-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-checks-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-checks-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-checks-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-checks-4.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 4 clicks.

Before excalive renders anything, it runs a check. It takes about a second and needs no rendering, and the video will not render until the first item is clean.

Click. Every word on screen gets said. For each click, the check compares the words on screen with the words in the notes, and lists any line whose words the notes never say. Why is that a rule? Because people read along while they listen. If a box says "canvas server" and the voice never mentions it, the viewer stops listening to work out what the box means. That is also why every list item on these slides is said word for word as its click comes up.

Click. No text runs off the page. The check measures each line with the real font the frames are drawn in and reports anything past the right margin or below the bottom. A model laying out a slide guesses widths. The check measures them.

Click. No text trips the voice. A list of patterns the voice gets wrong: an abbreviated date, a number written with a letter for million, four-digit counts that come out as years. Also words that go stale once the video exists, like a weekday or "the next slide", and a sentence that repeats one from twenty-five seconds earlier.

Click. Spoken counts match the screen. If the voice says two servers over a drawing with three, the check flags it. Notice what all four have in common. Each one takes "does this look and sound right", a question you would answer by watching, and turns it into something a program can answer from text and numbers. That is the main trick of this whole pipeline. The agent building a video cannot watch it and cannot hear it, so every check we could make mechanical, we did.

Ask: Which of these four would catch a problem in your own platform's output? Take one or two answers. Expect the counts check, for a bot that summarizes numbers.
-->

---
layout: none
class: story
---

# Part 4: Why we use it this way

<img class="story-still" src="./assets/slides/card-part4-0.png" />


<!--
Part four. Why we use it this way.
-->

---
layout: none
class: story
---

# Narration taken from the notes

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-derive-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-derive-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-derive-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-derive-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

At first the video had its own script, written separately from the deck's notes. Two things went wrong. A narration written as a summary of the lecture ran nine minutes for an eighty-minute talk, so the video was a trailer, not a lecture. And the deck changed under the video, twice: one session edited the slides while another was building the video, and nothing noticed the narration was now out of date. So now the words live in one place, the deck's notes.

Click. From the deck's notes, excalive builds the slides and exports, the PDF and PowerPoint you read.

Click. A course script builds the delivery script, the timed document the presenter rehearses from, from the same notes.

Click. And another builds the video's narration, which renders into the video. It drops the slides that only work in the room, like the chat break and the sources, and drops the Direction and Ask lines. Nobody edits the narration by hand. At the same time, the notes were rewritten to the length the presenter actually speaks, about a hundred and fifty words per planned minute, after recordings showed the lecturer saying three or four times what the old notes held. So the video is now the lecture, not a summary of it. When three outputs have to agree, generate all three from one source.
-->

---
layout: none
class: story
---

# Checking a voice nobody has heard

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-hear-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-hear-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-hear-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-hear-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-hear-4.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 4 clicks.

The agent that builds a video cannot hear it. So four checks stand in for listening, in order, from cheap to expensive.

Click. Phonemes show how a word will sound. Before voicing, the agent can ask the voice's own phonemizer how it will say a suspect word, and read the result. If a name comes out with the stress in the wrong place, it goes in the lexicon.

Click. Whisper writes down what the voice said. Whisper is a speech-to-text model. Transcribe every clip and diff the transcript against the script. Most differences are harmless, like digits against number words. The ones that matter are words that fused: "a round" heard as "around". One lesson: use the large model. The small one invents errors of its own, and once heard "everything" as "nothing".

Click. A fresh model hears it once. Give a model that has never seen the lecture the whole narration, and ask it to read it as a listener who cannot rewind: where did you lose the thread, what showed up that was never set up, which sentence was hardest by ear. It always finds something, so you stop when what is left is nits.

Click. A person listens before it ships. Every one of the first three is a translation of sound into text. None of them hears a flat stress or a name said wrong the way a person does. So the last check is a human with headphones. The pattern is the same as the screenshot: when the output is a picture or a sound, the agent needs a translation of it into something it can read, and a person still has the final listen.
-->

---
layout: none
class: story
---

# A skill that carries its command

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-skill-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-skill-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-skill-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-skill-3.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 3 clicks.

excalive is packaged as a skill, the same idea from Lecture 10: instructions a harness loads when a task needs them. This is the whole folder. One choice is worth noticing: it started as a Claude Code plugin and became a plain skill on its first day.

Click. SKILL.md is what the agent reads: what excalive does, how to draw a card, which checks to run, and where the rest of the instructions live.

Click. The file excalive inside bin is the command itself, shipped inside the skill folder with the scripts it runs beside it. A skill usually tells the agent how to use a tool that is installed somewhere else. This one ships the tool. Install the skill and the agent can run the command with nothing else set up, and an update updates the instructions and the tool together, so they cannot disagree. Because it is a plain skill folder, the same install works for Claude Code, Codex, Cursor and other harnesses that read skills.

Click. The references folder holds the house style, the long document of what a good slide and good notes look like. The agent reads it only when it is drawing a slide or writing notes, which is the three levels of loading from Lecture 10 in practice: the description always, SKILL.md when the task matches, and a reference only when that part of the task comes up.
-->

---
layout: none
class: story
---

# Agents one deck at a time

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-batch-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-batch-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-batch-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-batch-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-batch-4.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 4 clicks.

Rebuilding eighteen decks in three days was agent work: one agent per deck, one at a time, each working from the written guide alone. Here is what made that work.

Click. The guide holds every rule. An agent told to copy what the last deck did copies its accidents too. So every rule an agent needs, with the coordinates and the JSON for every kind of slide, lives in the style guide, and no rule points at another deck's files.

Click. The guide gets fixed between agents. Each agent writes a notes file listing every place the guide was unclear, wrong or silent, and what it decided. The guide is fixed from that file before the next agent starts. Most new kinds of gap show up in the first few decks. One thing never to do is change the guide under a running agent: two early agents started on one version, finished under another, and redid their work.

Click. Each agent draws on its own canvas. Remember the shared canvas: two agents on one port erase each other. Each gets its own port. In the same spirit, each agent commits only its own files by name, because a plain commit sweeps up whatever another session had staged.

Click. A person reviews every contact sheet before a deck is committed. The checks find unspoken words and bad speech. They do not find a slide that is tidy and wrong.

That review is fast, because it is twenty clicks to a page, and it is the step that caught the most important problems. An agent can produce a deck in an afternoon. A deck nobody looked at is still a draft.
-->

---
layout: none
class: story
---

# What the checks miss

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-miss-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-miss-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-miss-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-miss-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-miss-4.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 4 clicks.

So here is what still gets through.

Click. A label sitting on an arrow. Nothing in the check knows what an arrow is for. Text can be inside the frame, said in the notes, and still sit on top of a line so you cannot read it.

Click. Centered text drawn off-center. This was the worst bug in the first video. The deck stored a centered label's position at its middle, the way the browser draws it. The headless renderer read that number as the label's left edge, so every centered label in thirteen of the fourteen diagrams was shifted right by half its width. Every check passed. A contact sheet, rendered before the video, is what caught it, because somebody looked.

Click. A wrong stress on a name. Whisper hears the right word even when the stress is odd, so no text check notices.

Click. A person still looks and listens. The checks make the person's job small: instead of watching eighty minutes cold, they look at a few sheets and listen to sections the checks flagged. They do not make it zero.

This is the honest shape of almost every agent workflow you will build. The agent does the work and checks what can be checked mechanically. A person checks what cannot, and the more you move into the first group, the less the person has to do.
-->

---
layout: none
class: story
---

# Why it works for us

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-why-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-why-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-why-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-why-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l20-why-4.png" /></template>
</v-switch>


<!--
Direction: About 1 and a half minutes. 4 clicks.

To close this part, why this setup works for us, which is the real reason to tell you about it.

Click. Diagrams carry the explanation. The slides are mostly drawings, and drawings are easier to explain from and easier to follow.

Click. The slides look good to teach from. The staff like them, which matters more than it sounds when you stand in front of them twice a week.

Click. One source makes deck, script and video. They cannot drift apart, and a fix lands in all three.

Click. A video reviews a lecture quickly. That was the podcasts' job from the start, and the video does it with the pictures. And none of it costs anything to run: the drawing app, the canvas server, excalive and the voice are all free and run on our own machine.

There is a cost, and it is time. Building a lecture this way means writing every word the presenter says and drawing every diagram, and the first decks took many rounds to get right. What we got back is that each later deck is easier, because every lesson went into the guide and the checks instead of into somebody's memory.
-->

---
layout: none
class: story
---

# Part 5: Building on it

<img class="story-still" src="./assets/slides/card-part5-0.png" />


<!--
Part five. Building on it.
-->

---
layout: none
class: story
---

# The same scenes running live

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-handwave-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-handwave-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-handwave-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-handwave-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks. No demo today; describe it, or show the GIFs in handwave's README if there is time.

Once drawings are data, other programs can read them, run them and change them. handwave, another project by the same staff member, runs Excalidraw scenes live in a browser and moves them in response to input.

Click. Phones vote on a slide. In its live deck, the room scans a code on the title slide, and then a slide can give every phone something to do. A vote makes hand-drawn bars grow on the big screen as answers arrive. The presenter can drag a shape or draw on a slide, then press a key and everything morphs back to the script, using the same id matching you saw in Part 3.

Click. A drawing runs as a program. Shapes are parts, arrows join them, and labels say what they do. Change a label from three workers to one and the queue in the drawing backs up. The drawing is the program, and a misspelled word gets a message on the drawing.

Click. A dashboard is drawn as the system. Draw the boxes of your system, and each box shows live numbers from a data source named in its label. The layout of the dashboard is the layout of the system, and since the binding is label text in a plain file, a change to the dashboard is a diff you can review.
-->

---
layout: none
class: story
---

# What you could build

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l20-build-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l20-build-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l20-build-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l20-build-3.png" /></template>
</v-switch>


<!--
Direction: About 2 and a half minutes. 3 clicks. Nothing here is assigned.

Here is what you could build for your own platform from these pieces. All of them are free and open source, so none needs a subscription.

Click. Diagrams your harness draws and checks. Give your Hackathon 2 platform the canvas CLI and a screenshot loop, and it can answer "how does this work" with a diagram it drew, looked at and fixed.

Click. A video that explains your platform. A story file and a handful of scenes is a narrated video of how your system works, and the check will tell you before you render which words you forgot to say.

Click. A skill that ships its own tool. The excalive layout, a SKILL.md beside a bin folder with the command in it, works for any tool you want an agent to run. If your platform has a script you keep explaining to your agent, package the explanation and the script together. And whatever you build, keep the lesson these tools taught us: when your harness produces something no test can check, find the real output, turn it into something the model can read, and keep a person on the last look.

If you want somewhere to start, take the hello example in excalive's repository. It is one story file, three scenes and a short video, and you can change its words and rebuild it in a few minutes on your own laptop.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks. Leave this up during questions.

The sources for today: excalive, the canvas server mcp-excalidraw-server, Excalidraw itself, the Kokoro voice model, and handwave. All are on GitHub, and every claim in this lecture is sourced in the lecture's content file.
-->

---
layout: none
class: story
---

# Questions

<img class="story-still" src="./assets/slides/deck-questions-0.png" />


<!--
Direction: The rest of the time is questions.

Questions.
-->
