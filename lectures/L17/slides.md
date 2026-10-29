---
theme: ../theme
title: 'L17: Knowledge Bases'
info: |
  EECS 498 AASE, Lecture 17
  Applied Agentic Software Engineering, UMich Fall 2026
transition: fade
mdc: true
layout: none
class: story
---

# Knowledge Bases

<img class="story-still" src="./assets/slides/card-title-0.png" />

<style>
:global(.story) { background: #fff; }
:global(.story h1) { position: absolute; width: 1px; height: 1px; overflow: hidden; clip: rect(0 0 0 0); white-space: nowrap; margin: 0; }
:global(.story img.story-still) { position: absolute; inset: 0; width: 100%; height: 100%; object-fit: contain; }
</style>

<!--
Direction: About 1 minute. No clicks.

This is Lecture 17, Knowledge Bases. Lecture 16 was memory, what the model learns from its sessions with you. Today the knowledge comes from documents instead: the design docs, meeting notes and issue threads a project piles up. The question is the same one memory asked. How does what the model works out once stay worked out, instead of being found all over again? By the end you should be able to say how a wiki the model keeps is built, how a question gets answered from it, how that compares to the usual alternative, retrieval, and how a reader knows whether to trust a page a model wrote.
-->

---
layout: none
class: story
---

# Answers rebuilt from scratch

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-problem-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-problem-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-problem-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-problem-3.png" /></template>
</v-switch>


<!--
Direction: About 3 minutes. 3 clicks.

Let's start with a project. Your team's project has design docs, meeting notes and issue threads, a few dozen documents by the middle of the term. The colors are the same as before: rose is you and your team, blue is the model. On Monday you ask: why this database? The model searches the documents, pulls out three pieces, and puts an answer together. One piece is from the design doc, one is from a meeting, and one is from an issue thread. Say each piece is a few hundred tokens and the search read through several thousand to find them. The answer is good, and it took real work: finding the pieces, reading them, and working out how they fit.

Click. A similar question on Thursday. Someone else on the team asks why the project uses that database and not another one. The same search puts the same pieces together again. It reads the same documents, finds the same three pieces, and works out the same connections from scratch. Monday's work left nothing behind, so the second answer starts from nothing too. If the team asks ten questions like this over the term, the model does that work ten times, and none of it adds up. Each answer is as good as the search that found its pieces, and no better.

Click. It gets worse. Look at two of the documents: the design doc picks one database, and a later meeting changed it. Nothing ever compares them. The search finds whatever pieces look most like the question, and the design doc talks about the database at length, while the meeting mentions the change in two lines. So the answer may use the older one, and nobody notices, because the answer reads just as confidently either way. If you have ever been told something by a harness that was true a month ago, this is one way it happens.

Click. So today's question: how does knowledge build up instead of being found again? Part one builds a knowledge base that the model keeps: a wiki, written from your sources, that already holds the answer to Monday's question and already notes that the meeting changed the decision. Part two is how you find things in it, the common alternative called retrieval, and how you know what to trust.
-->

---
layout: none
class: story
---

# Today's schedule

<img class="story-still" src="./assets/slides/deck-schedule-0.png" />


<!--
Direction: About 30 seconds. No clicks.

Here is today's plan, in two parts. The first part is building a knowledge base: a wiki the model keeps up from your sources, so an answer is not assembled from scratch every time. Then a break. The second part is finding and trusting it: how the model retrieves a page, and how you know where a page came from and who checked it. Then questions.
-->

---
layout: none
class: story
---

# Part 1: Building a knowledge base

<img class="story-still" src="./assets/slides/card-part1-0.png" />


<!--
Direction: About 15 seconds. No clicks.

Part one. Building a knowledge base.
-->

---
layout: none
class: story
---

# Who keeps the wiki

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-upkeep-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-upkeep-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-upkeep-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-upkeep-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

Writing knowledge down once is an old answer: a wiki. Here is a wiki people keep. Think of a wiki or a shared docs folder you have worked with. What happened to it after a few months?

Ask: Who has worked on a team wiki or a shared docs folder? What happened to it after a few months? Take two or three answers, then go on.

The first pages are good, because someone cared enough to start it. Then the project moves on, and page after page falls out of date. Say the wiki has forty pages, and every change to the project touches about five of them. The person who made the change updates the one page they remember, if any. After a few months, most pages are a little wrong, and a reader cannot tell which. The upkeep grows faster than the value, so people stop updating it, and the wiki dies. A wiki that is half wrong is worse than none, because people trust it.

Direction: Your story, if you have one: a wiki or a docs folder you watched go stale.

Click. In April, Andrej Karpathy wrote up a different split of the work, which he calls an LLM wiki. It is a short idea file, not a product, and the rest of Part one follows it. The split gives each side the part it is good at. You: pick the sources, ask the questions, decide what matters. Those are judgments only you can make. Which documents count, what you want to understand, and what the project is for are things the model cannot know unless you say so. Notice what is not on your list: writing pages.

Click. The model: summaries and links, the index and the log, keeping pages consistent. That is the bookkeeping people give up on. When the project changes, someone has to find every page the change touches, update each one, fix the links between them, and write down what changed. That is exactly the work in the forty-page example, the part nobody does. In a team wiki, nobody is assigned that work, so it falls to whoever happens to notice. Here it has an owner. The model does it for every source, every time, because the schema says to.

Click. The bookkeeping now costs the model almost nothing. It does not get bored, it does not forget a cross-reference, and it can update many pages in one pass. Updating five pages after a meeting takes the model a few minutes. That is why this design works now. The idea itself is old. Vannevar Bush described a personal, linked knowledge store in 1945, the Memex, and the part he could not solve was who does the maintenance. Now there is an answer to that part. The judgment stays with you, and the maintenance goes to the model.
-->

---
layout: none
class: story
---

# Three layers

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-layers-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-layers-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-layers-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-layers-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

Let's build it up. The design has three layers, and we start at the bottom. Your raw sources: design docs, meeting notes, issue threads. They are read-only. The model reads them and never edits them, so they stay the source of truth. That rule matters more than it looks. If the model could edit the sources, a mistake in a summary could spread back into the documents it came from, and there would be nothing left to check against. Because the sources never change, any page in the wiki can always be checked against what was actually written.

Click. The model reads them and writes the second layer, the wiki the model writes. Pages like the index, the login flow, the database and the decisions, linked to each other. Each page is about one topic, not one document. The database page draws on every source that says anything about the database. You read the wiki, and the model writes it. You can still fix a page by hand, but the normal path is to tell the model what is wrong and let it fix the page, so the bookkeeping stays in one place.

Click. The third layer is the schema: rules you and the model keep. How pages are named, what goes on a page, what to do when a source arrives. It is gray because you both write it. It is an instruction file, like the AGENTS.md files from Lecture 10, with one job: keeping the wiki. You start it with a few lines, and it grows as you find out what you want. Say the first pages come out too long. You add a rule: one topic per page, and a page over a screen gets split. Say the model keeps overwriting old decisions. You add a rule: when a source contradicts a page, note both and say which is newer. The schema is where you steer the bookkeeping without doing it.

Click. Compare Lecture 16. Memory learns from sessions and a wiki learns from sources. In memory, the notes come from what happened while you worked: a correction, a preference. In a wiki, the pages come from documents someone else wrote. But underneath, both are folders of notes the model writes for later, and both are read the same way, as asking a question will show: a short index first, and a page only when it matters.
-->

---
layout: none
class: story
---

# One page from many sources

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-page-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-page-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-page-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-page-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

What a page looks like. Here is a page on the database, and three sources: a design doc, meeting notes, an issue thread. These are the same three pieces Monday's search found. The difference is that here they are put together once, ahead of time, and kept. A wiki holds a few kinds of pages. Some sum up one source, like a page for each meeting. Some are about one thing in the project, like the database or the login flow. Some are about an idea that runs across the project, like how the team handles errors. And some are comparisons, or an overview of the whole project. The database page is the second kind, and that kind does the most work, because questions are usually about things in the project, not about documents.

Click. Each source gives the page a piece. Let's take them one at a time. What we use, from the design doc. Why we chose it, from the meeting notes. An open problem, from the issue thread. Say the issue thread is about slow queries on one table. The design doc never mentions that, and the meeting notes do not either, so the page is the only place where the choice, the reason and the problem sit side by side. A reader gets all three in one page of text instead of three documents.

Click. And links to the login flow and the decisions. A page names its neighbors, so the model can follow them. If your question is about why sign-in is slow, the model can start at the login flow page, follow its link to the database page, and find the open problem there. The links are the model's way around the wiki, the same way you click through pages. Links also catch what the index misses. If the index line for the database page says nothing about speed, a question about slow sign-in might not pick that page from the index. But the login flow page links to it, so the model gets there anyway. In a folder of documents, nothing connects one file to another except a search. In a wiki, the connections are written down by a model that read both pages.

Click. So the page sums up several sources instead of copying one. The pieces Monday's search had to find and assemble are already assembled, and the conflict between the design doc and the later meeting is already noted on the page. That is the real gain. When Thursday's question comes, the model reads one page that already says the meeting changed the decision, instead of hoping the search happens to find both documents.
-->

---
layout: none
class: story
---

# Adding a source

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-ingest-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-ingest-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-ingest-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-ingest-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

The wiki changes through three operations, and the first is adding a source. You drop today's meeting notes into the raw sources and tell the model to add them. Let's follow one in. The notes go into the raw sources folder as they are, and you give a short instruction, something like: add today's meeting. The schema says what adding means: which pages to look at, how to mark a change, and what to write in the log. In Karpathy's version, the model first tells you the main points it took from the notes, so you can correct a misreading before it touches any page. Here is the wiki, with its index and its log. Before the next click, think about what the model should change in the wiki when those notes come in.

Ask: When today's meeting notes come in, what should the model change in the wiki? Take a few answers before the click.

Click. The model reads the notes, updates the three pages the meeting touched, and writes a new page for a topic that is new. Let's say the meeting decided to add caching in front of the database, and planned a new test setup. The model updates the database page with the caching decision, updates the decisions page, and adds a line to the login flow page because caching changes how sign-in reads its data. The test setup has no page yet, so it gets a new page. When the notes contradict a page, it says so on that page instead of quietly overwriting it. The old claim stays, marked as replaced, with the date and the source that replaced it. That way a reader can see the history of a decision, not only its current state.

Click. Then the bookkeeping: one new line in the index for the new page, and one new entry in the log saying what was added and when. The index is the list of every page with one line about each. The log is the record of what happened, so you can look back and see that the meeting notes went in on a given day and touched four pages. The log records questions and checks too, not only sources, so it is the wiki's history. When the model starts work in a new session, reading the last few entries tells it what happened recently, the same way you read the recent commits when you come back to a repository.

Click. One source can touch many pages. Karpathy says a single source might touch ten to fifteen pages. That is the work people skip, and it is why each update is worth checking. Karpathy adds sources one at a time and reads what changed. You can also add a batch and check less closely. It is a trade between your time and your trust in the pages. For a project you care about, reading what one source changed is a few minutes, and it is how you catch the model misreading a meeting before the mistake spreads to other pages.
-->

---
layout: none
class: story
---

# Asking a question

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-query-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-query-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-query-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-query-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l17-query-4.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 4 clicks.

The second operation: asking a question. You ask again: why this database? It goes to the model, and the model has the wiki, its index and its pages. Compare Monday. Then, the question went to a search over raw documents, and the model assembled an answer from whatever came back. Now the wiki already holds the assembled answer, and the only work left is finding the right page. Let's follow the question through.

Click. The index is read first: one line per page, enough to see which pages matter. Let's put numbers on it. Say the wiki has 300 pages, and each line in the index is about 20 tokens. The whole index is 6,000 tokens, which is cheap to read on every question. Reading all 300 pages instead, at about 1,500 tokens each, would be 450,000 tokens for one question. It is the same move as a skill's description in Lecture 10 and the memory index in Lecture 16: a short line that is always read, and a body that is read only when the line matches. And like the memory index, the line decides everything. A page whose line says "database notes" is harder to find than one whose line says why the project chose its database and what changed.

Click. Then opened: only the two pages the index pointed to, not the whole wiki. Here that is the database page and the decisions page, about 3,000 tokens. So the question costs about 9,000 tokens of reading in all, and both pages already hold the assembled answer, including the meeting that changed the decision. If the index points nowhere useful, the model is not stuck. It can follow links from the pages it opened, and the raw sources are still there to search. An answer found that way is a good one to file back, because it shows a gap in the wiki.

Click. An answer with links to the pages it came from, so you can check it. Each page links back to its sources, so you can go from the answer to the page to the original meeting notes in two steps. That chain is what makes the answer checkable. An answer without links asks you to trust it.

Click. And the step that makes it build up: a good answer is filed back. The comparison or the explanation you asked for becomes a new page, so the next person who asks starts from it. Say you asked the model to compare the two databases the team considered, and the comparison is good. Filed back, it becomes a page called something like database comparison, with its own line in the index. Thursday's question now finds it in the index and reads one page. So questions add to the wiki the same way sources do, and the wiki gets better the more it is used.
-->

---
layout: none
class: story
---

# Checking the wiki

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-lint-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-lint-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-lint-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-lint-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l17-lint-4.png" /></template>
  <template #5><img class="story-still" src="./assets/slides/l17-lint-5.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 5 clicks.

The third operation: checking the wiki, like running a linter on code. Every so often you ask the model to look over the wiki, and we can go through what it looks for. Adding sources one at a time catches most problems, but some only show up when you look at the whole wiki at once. Before the next click, think about what could go wrong in a wiki the model keeps.

Ask: What could go wrong in a wiki the model keeps, after a few months of sources and questions? Take answers before the clicks; most of them will match one of the next four.

Click. Two pages disagree. Say the database page says one thing and the decisions page another. That happens when one source updated one page and missed another. A source can touch ten to fifteen pages, so missing one is easy. The raw sources are still there, so the model can check which one is right. This is why the sources are read-only: the check always has something fixed to go back to.

Click. A page replaced by a newer source: a claim that was true until a later document changed it. The page still says the old database, because the meeting that changed it went in before the page existed, or the update missed it. The model compares the dates on the sources and finds the newer one. This is Monday's problem from the start of the lecture, caught on purpose instead of by luck.

Click. Nothing links here: no other page points to this one, so a reader following links will never find it. It might still be in the index, but the model moving through the wiki by links will never reach it. The fix is usually a link from the page that should have mentioned it. Orphans are common when an update writes a page for a new topic and nothing else is changed to point at it.

Click. A topic with no page yet: it keeps coming up in other pages but has none of its own. Here two pages mention the test setup, and neither explains it. The model can write a page for it from what the sources say, and link both pages to it. A missing page shows where the wiki has not caught up with how the team talks about the project.

Click. Then it fixes the pages or asks you which source is right. When the sources settle the question, the model fixes it. When they do not, say two meetings disagree and neither is clearly later, it asks you, because that is a judgment, and judgments are yours. Karpathy's list has two more checks: links that should exist between pages but do not, and gaps the model could fill with a web search. Adding, asking and checking are the whole job: the model adds, answers and checks, and you choose what goes in. Lecture 16 ended with checking a note before trusting it. This is the same habit for a whole wiki, done on a schedule.
-->

---
layout: none
class: story
---

# Chat break

<img class="story-still" src="./assets/slides/deck-chat-0.png" />


<!--
Direction: Five minutes. The question is to be decided by the instructor.

Chat break. Back in five minutes.
-->

---
layout: none
class: story
---

# Part 2: Finding and trusting it

<img class="story-still" src="./assets/slides/card-part2-0.png" />


<!--
Direction: About 15 seconds. No clicks.

Part two. Finding and trusting it.
-->

---
layout: none
class: story
---

# Finding the relevant text

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-retrieve-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-retrieve-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-retrieve-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-retrieve-3.png" /></template>
  <template #4><img class="story-still" src="./assets/slides/l17-retrieve-4.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 4 clicks.

The common alternative to a wiki is retrieval, often called RAG, short for retrieval-augmented generation. The name comes from a 2020 paper, and it has come to mean a whole family of systems. Retrieval is a job, not one technology. Let's walk through it. Your question comes in, something has to find the relevant text, the harness puts it in the request, and the model answers from what it was handed. The model never sees the whole collection, only what the finding step hands it. So the answer can only be as good as what was found. How would you do the finding?

Ask: What ways do you know to find the text relevant to a question? Take two or three answers before the click.

Click. One way is keyword search. It matches the exact words in the question, the way grep or a search box does, so it is good at names and error codes. Say you ask about a specific error message. Keyword search finds every document with that exact message in it, and nothing else. Its weakness is the other side of the same coin. If you ask about login and the document says sign-in, keyword search does not find it.

Click. Another is vector search. An embedding model turns each chunk of text into numbers, so the search matches by meaning. Let's follow one document through. First the documents are cut into chunks of a few hundred tokens each. Then an embedding model, a second model the harness calls and not the one that answers, turns each chunk into a long list of numbers. Text with similar meaning gets similar numbers. Your question is turned into numbers the same way, and the search returns the chunks whose numbers are closest. It can find a page about signing in when you asked about login, with no word in common.

Click. The third way needs no search engine at all: the model finds it itself. It reads the index, opens files, follows links, runs grep, with the same tools it uses to edit code. Cline, a coding harness, builds no index of your code on purpose. Its team describes it reading code file by file and following the imports, the way a person would. You have watched this happen whenever a harness reads a file, searches for a function's name, and opens the file that defines it. It takes more round trips than one search, but each step is the model's own judgment about where to look next.

Direction: Your story, if you have one: a time a harness found the right code by searching on its own, or went looking in the wrong place.

Click. So retrieval is the job and vector search is one tool for it. When people say RAG, they often mean vector search alone, but the job is finding the relevant text, and any of the three can do it. Real systems often combine them: keyword and meaning search together, or a search that finds where to start and a model that reads on from there.
-->

---
layout: none
class: story
---

# Limits of vector search

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-vector-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-vector-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-vector-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-vector-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Vector search is what most people mean by RAG, so know where it falls short. First, it finds text that sounds similar but may not be relevant. Nearest in meaning is a guess at relevant, not the same thing. Say you ask why the project chose its database. The closest chunk in meaning might be a paragraph that describes the database at length but never says why it was chosen. It is about the right topic and does not answer the question.

Click. It cuts each chunk off from the text around it. A chunk that says revenue grew three percent does not say which company or which quarter. That information was in the heading two pages earlier, and the chunk lost it when the document was cut. In code, a function call can land in one chunk and its definition in another. One fix, from Anthropic's post on contextual retrieval, is to have a model write a short line of context for each chunk before it is indexed: this chunk is from one company's filing for the second quarter. They report that this, together with keyword search, cut failed retrievals by about half. But notice what that fix is. A model reads the whole document and writes context once, ahead of time, which is a small step toward a wiki.

Click. It can miss an exact name or error code. Numbers that capture meaning blur exact strings, which is exactly where keyword search is strong. Two error codes that differ by one digit can get nearly the same numbers. Say a user reports error code 412, and the documents also mention 421. A search by meaning may rank the wrong one first, and a keyword search for 412 finds only the right one. That is why real systems run keyword search next to vector search and merge the results.

Click. And it needs rebuilding whenever the content changes. Every edited document means its chunks are cut and embedded again. Until it is rebuilt, the index describes documents or code that no longer look that way. For code that changes every few minutes during a session, that is a real problem, and it is part of why some coding harnesses do not index at all. For a document store that changes slowly, rebuilding overnight is fine.
-->

---
layout: none
class: story
---

# Retrieve or compile

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-compare-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-compare-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-compare-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-compare-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

Let's set the two side by side. Retrieve from raw documents, or compile a wiki. The work happens at every question for retrieval, and when a source arrives for a wiki. Retrieval does its finding and assembling each time someone asks. A wiki does it once, when the source comes in, and every later question reads the result. So retrieval is cheap to set up and pays on every question, and a wiki pays up front and saves on every question after.

Click. What is kept: nothing, for retrieval, so each question starts over. That is Monday and Thursday again. Pages that build up, for a wiki, including the answers that were filed back. It is worth being clear about what this saves and what it does not. Take the numbers from Part one. A wiki question read about 9,000 tokens: the index and two pages. A retrieval question might read ten chunks of 400 tokens, about 4,000 tokens, plus the search. So per question, the wiki does not read less. What it reads is better: pages already assembled, with the conflict already noted. And the wiki paid earlier, when each source went in and touched ten or more pages. So the trade is not about tokens. It is whether the assembling is done once and kept, or done again each time and thrown away.

Click. What the model gets: pieces of the raw documents, found fresh for each question, or pages written with their context, by a model that read the whole source. A chunk is a few hundred tokens cut out of a document. A page was written by a model that read every source on the topic. And there is a quieter difference. Retrieval has no step that compares two documents, so a contradiction only shows up if both pieces happen to come back together. A wiki's adding and checking look for it.

So which would you use? Think about your team's project, and then about a company's whole support archive, before the next click.

Ask: Which would you use for your team's project, and which for a company's whole support archive? Take two or three answers before the click.

Click. When it fits. Retrieval fits a big collection nobody will curate, like a company's whole document store or a support archive. Nobody is going to pick sources and read updates for a million support tickets, and the collection changes every day. A wiki fits knowledge you work with over time, like a project or a research topic. You care about it, it grows slowly, and the same questions come back. And for a small collection, neither: put all of it in the request. Anthropic puts that line at about 200,000 tokens, roughly 500 pages. With the cache from Lecture 15, sending the same 200,000 tokens with every question costs a tenth of the full price after the first one, as long as the cache is warm. A team project with a few dozen documents often fits, and then the simplest answer is the right one.
-->

---
layout: none
class: story
---

# When the index is not enough

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-search-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-search-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-search-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-search-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Back to the wiki. With hundreds of pages, the index is enough: the model reads the index, then the model opens pages and follows links. That is the third way of retrieving, and on a wiki it often works best, because the pages already link to each other and the index says what each one holds. Karpathy reports this working well at around a hundred sources and hundreds of pages, with no search engine at all.

Click. With thousands of pages, the index grows too long to read every time. Take the numbers from before. At 20 tokens a line, 5,000 pages is an index of 100,000 tokens, read on every question before the model opens a single page. Even from the cache, that is a lot of reading to find two pages, and a long index is harder for the model to scan well. The index also grows with every source and every answer filed back, so a wiki that starts small crosses this line on its own.

Click. So your question goes to a search first, a search by keyword or meaning, which returns a starting page. Not the answer, a starting page. From there the model follows links. Say the search returns the database page. The model reads it, follows the link to the decisions page, and has its answer, the same way it would have after reading the index. Karpathy suggests a small local search over the wiki's pages, keyword and meaning together, once the wiki gets that big.

Click. Search finds a starting point and links do the rest. Search is for the pages navigation cannot reach, and plain keyword search is often enough. You do not need a vector database to search a wiki. The pages are already summaries in plain words, with good titles and one-line descriptions, so keyword search on them works far better than it does on raw chunks.
-->

---
layout: none
class: story
---

# An open format

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-format-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-format-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-format-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-format-3.png" /></template>
</v-switch>


<!--
Direction: About 4 minutes. 3 clicks.

Now, what format do the pages use? Every harness could invent its own layout, and then a wiki one harness built is a pile of files the next one cannot make sense of. Say your team builds a wiki with one harness, and next term a different harness takes over. If the layout was that harness's own, the new one has to guess what each file means. The Open Knowledge Format, OKF, published by Google Cloud this year, is a shared one. A folder of markdown files, and first, index.md lists one line per page. The format calls that progressive disclosure: you see what is there before you open anything, which is the index-first reading from Part one.

Click. Each page starts with a small header. Only one field is required: type, what kind of page it is. A page that says only its type is a valid page. A description is recommended, the one line the index shows, and so are a title and tags. Then the text links to other pages, with ordinary markdown links. Nothing about the format needs a special tool to read or write.

Click. And log.md lists the newest changes first. That is the log from the ingest slide, written the way the format expects. Newest first means anyone opening the log, a person or a model, sees what changed recently without reading the whole history. And any folder may have its own index and log, covering only that folder. That is one more answer to a wiki with thousands of pages: split it into sections, each read index first.

Click. Any harness that reads files can read it, with no database and no special tools. If a harness can open a file, it can read the wiki, and if you can clone a repository, you can share it. It is the same habit as the model endpoint earlier in the course: program against an open standard, not a layout one tool made up. For your own work, that means a wiki built with one harness can be read by another harness, or by a teammate in a text editor. Nothing is locked inside one tool.
-->

---
layout: none
class: story
---

# Trust and freshness

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-trust-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-trust-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-trust-2.png" /></template>
  <template #3><img class="story-still" src="./assets/slides/l17-trust-3.png" /></template>
</v-switch>


<!--
Direction: About 5 minutes. 3 clicks.

When a model writes most of the pages, a reader needs to know how far to trust each one, and the format puts the answers in a page's header. The format was written for exactly that case: knowledge that is not written once and read, but written and kept up by models, continuously. First, the sources it came from. That is provenance. A page lists what it was written from, and a claim on the page can point to the one source it came from. So let's fill it in for the database page. The line on that page that says the meeting changed the decision points to those meeting notes, and you can check it.

Click. Written by a model or a person, recorded by name. A model is recorded with its name and version, and a person by their id, along with when the page was written. Say a page was written by a model in September. If you later learn that model version had a habit of mixing up dates, you know which pages to look at first.

Click. Checked by no one, a program or a person. Those give three levels of trust: unverified, machine-checked, human-reviewed. A page no one checked is unverified. A page that only a program checked, say a script that confirmed every source link still works, is machine-checked. A page a person read and confirmed is human-reviewed. Who wrote a page and who checked it are kept apart on purpose. A model that wrote a page and also marked it checked would tell you nothing, so the format lets the writer and the checker be different, and shows both. In practice, you might read and confirm the ten pages the team relies on most, and leave the rest marked unverified, so anyone reading knows which is which.

Click. Stale after a set date, after which the page counts as out of date. That is freshness. Say a page describes the demo schedule. It is stale after the demo, and the format lets the page say so ahead of time, so nobody trusts it after the demo. A page can also be marked draft, stable or deprecated. These are Lecture 16's memory problems written into the format: who may write, and a note that goes wrong. A writer nobody watched shows up as unverified, and a note that will go wrong carries its own date.
-->

---
layout: none
class: story
---

# Where knowledge lives

<v-switch>
  <template #0><img class="story-still" src="./assets/slides/l17-where-0.png" /></template>
  <template #1><img class="story-still" src="./assets/slides/l17-where-1.png" /></template>
  <template #2><img class="story-still" src="./assets/slides/l17-where-2.png" /></template>
</v-switch>


<!--
Direction: About 2 minutes. 2 clicks. A callback to Lecture 16's last slide, not a re-teach.

This is Lecture 16's grid: written by you or written by the model, and a fact that holds every session or a method for one kind of task. Instruction files, skills, memory, and skills the model writes.

Click. What is new today is one cell. The model's facts are now memory and wikis: a wiki is also facts the model writes, but from documents instead of from sessions with you. So what made memory work in Lecture 16 applies here too: a good one-line description, checking a page before trusting it, and a clear rule for who may write.

Click. And here is what Lecture 16 did not say. A skill's description, the memory index and the wiki's index.md are one mechanism, so a harness needs one way to load them: a line that is read every time, and a body read only when the line matches. Not three separate features. If you build one, build index-first loading once and point it at all three. Then a skill, a memory note and a wiki page all cost one line until they are needed, and the same code decides when to open each.
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

A wiki compiles sources and keeps them current, so the work happens once per source instead of once per question. Monday's answer and Thursday's answer read the same page, and the page already says the meeting changed the decision.

Click. You pick sources and the model keeps the pages. The bookkeeping that killed wikis is now nearly free. Your part is the judgment: which sources go in, what you ask, and which source is right when two disagree. Read what each source changed while the wiki is small, and steer the rest through the schema.

Click. Retrieve when nobody will curate a big collection that keeps changing, and compile what you work with over time. If the whole collection fits in the request, skip both and send it. And remember that retrieval is a job, not vector search: a model reading an index and following links is retrieval too.

Click. A page records its source and its checker, so a reader knows how far to trust it. When a model writes the pages, that header is how you tell a page someone checked from one nobody has. Check the pages you rely on, and give the ones that will go stale a date.
-->

---
layout: none
class: story
---

# Sources

<img class="story-still" src="./assets/slides/deck-sources-0.png" />


<!--
Direction: No clicks. Leave it up while questions start.

Karpathy's LLM Wiki. The Open Knowledge Format version 0.2. Anthropic on contextual retrieval, which has the revenue example. And the RAG paper by Lewis and others, from 2020.
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
