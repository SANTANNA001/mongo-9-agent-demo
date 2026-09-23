# AI Agent Demo — Speaker Notes

> 20-minute presentation. Glance at these notes while presenting.
> Each section tells you: what to show, what to say, why it matters, what comes next.

---

## INTRODUCTION (2 min)

### Welcome

**SHOW ON SCREEN:** Just this notebook or a blank welcome slide.

> "Welcome everyone. Today I'm going to walk you through three parts of the same flow."
>
> "We're going to start simple and build up."
>
> "By the end, you'll see how an AI agent can investigate a real MongoDB upgrade case."

---

### The Scenario

> "Here's the situation."
>
> "A customer just upgraded their sharded Atlas cluster to MongoDB 9.0."
>
> "Now they're reporting three problems:"
>
> "- Slow aggregations"
>
> "- Memory errors on write operations"
>
> "- Change-stream lag"
>
> "This is a real support case. A TS engineer needs to figure out what changed in 9.0 that could cause these symptoms."

---

### How the 3 parts connect

> "We're going to solve this in three steps."
>
> "Part 1: We'll ask a question about 9.0. But first, we'll show you what happens when the model has no access to the docs."
>
> "Then we give it the docs — that's RAG. It finds the relevant sections and answers from only that."
>
> "Part 2: Now instead of us picking the question, we give the model tools. It decides which tools to use, in what order, to investigate the case."
>
> "Part 3: We split the work between two agents. One investigates. One reviews the findings. Two specialized roles."

---

### PRESENTER TIP

> Open these files now so the audience can see them:
> - `9.0 Upcoming.pdf` — the MongoDB 9.0 release notes
> - `Compatibility changes.pdf` — what breaks between versions
>
> Keep them open in separate tabs. You'll point to them during Part 1.

---

## PART 1 — RAG (6 min)

### What we're doing

> "Part 1 is about RAG: Retrieval-Augmented Generation."
>
> "The idea is simple."
>
> "An LLM only knows what was in its training data. It was trained before MongoDB 9.0 came out."
>
> "So if we ask it about 9.0, it won't know."
>
> "RAG fixes this. We retrieve the relevant docs, then give them to the model as context."
>
> "The model answers from that context — not from memory."

---

### SHOW ON SCREEN: `Setup` cell

Run the setup cell.

> "We're using two models locally through Ollama."
>
> "One for embeddings — that's how we find relevant documents."
>
> "One for chat — that's how we get answers."
>
> "everything runs locally."

---

### SHOW ON SCREEN: `Step 1 — Read the markdown file`

Run the cell. Point to the output.

> "Here we're loading the MongoDB 9.0 release notes."
>
> "It's a file with about seven thousand characters."
>

---

### SHOW ON SCREEN: `Step 2 — Split into chunks and embed them`

Run the cell. Let the embedding output scroll.

> "Now we split the document into chunks of about fifteen hundred characters each."
>
> "Then we embed each chunk — meaning we turn it into a vector."
>
> "A vector is just a list of numbers that captures what the text means."
>
> "Similar ideas get similar vectors. That's how we'll find relevant sections later."
>
> "This step takes a few seconds because we're running an embedding model locally."

---

### SHOW ON SCREEN: `Step 3 — First, ask the model WITHOUT any context`

Run the cell. Let the audience see the answer.

> "Before we use RAG, let's ask the model directly."
>
> "We ask: what is the per-operation memory limit in MongoDB 9.0?"
>
> "No context. No documents. Just the model."
>
> "Look at the answer. It probably says something like 'I don't have information about MongoDB 9.0' or it guesses wrong."
>
> "This is the training cutoff problem. The model's data stops before 9.0 existed."
>
> "This is exactly why we need RAG."

---

### SHOW ON SCREEN: `Step 4 — Embed the question + find the best chunks`

Run the cell. Show the top chunks and scores.

> "Now we embed our real question into the same vector space."
>
> "We use cosine similarity to compare it against every chunk in the document."
>
> "The chunks with the highest scores — up near 0.7 or 0.8 — are the ones most relevant."
>
> "You can see the top one is about the per-operation memory limit. The next ones mention SBE and change streams."
>
> "This is semantic search — it finds meaning, not just keywords."
>
> "Quick note: in production, you'd swap this local embedding model for MongoDB Voyage AI embeddings. Voyage models are purpose-built for RAG and integrate directly with Atlas Vector Search. Same code pattern — just a different model."
>
> "We take the top five chunks and combine them into one piece of context."

---

### SHOW ON SCREEN: `Step 5 — Show the exact context`

Run the cell. Scroll through the context.

> "This is exactly what the model will see."
>
> "Look — it has the per-operation memory limit section. Mentions error code 146 and 292."
>
> "It has the SBE metrics. The change stream targeting. All the relevant 9.0 changes."
>
> "The model hasn't seen this before. We're about to hand it over."

---

### SHOW ON SCREEN: `Step 6 — Ask the LLM`

Run the cell. Show the answer.

> "Now we send this context plus the question to the LLM."
>
> "We tell it: answer ONLY from the context we gave you."
>
> "Look at the answer. It identifies the per-operation memory limit. It mentions error codes 146 and 292. It talks about SBE changes."
>
> "Same model. Same question. But now it has the docs."
>
> "That's RAG. Retrieve the right information. Give it to the model. Let it answer from evidence."

---

### SHOW ON SCREEN: `Step 7 — Ask another question` (optional)

Run this only if you have time. Otherwise skip.

> "Let's try one more. What changed about change streams?"
>
> "Same process — embed, find chunks, generate answer."
>
> "Same model. Different question. Still grounded in the docs."

---

### Transition to Part 2

> "So Part 1 showed us RAG. We retrieve, then we generate."
>
> "But we had to pick the question. We had to decide which tools to run."
>
> "What if the model could decide for itself?"
>
> "That's what Part 2 is about — agents."

---

## PART 2 — Single Agent (7 min)

### What we're doing

> "An agent is a model that can choose which tools to use."
>
> "Instead of us saying 'run this, then this', we give the model a list of tools and a task."
>
> "The model decides: what tools do I need? What order should I run them in?"
>
> "We only run the tools we approve. The model can't run arbitrary code."

---

### SHOW ON SCREEN: `Setup` and `Load all docs`

Run both cells.

> "Same setup as before — Ollama, TF-IDF, our markdown files."
>
> "We're also loading the compatibility docs now. Two source files: release notes and compatibility changes."

---

### SHOW ON SCREEN: `Define the diagnostic tools` — scroll through

Run the cell to check the tools work, but don't read through every function.

> "Here we define seven tools."
>
> "search_docs — searches the 9.0 release notes."
>
> "parse_explain — reads the customer's explain output."
>
> "check_queryStats — looks at write operation patterns."
>
> "read_serverStatus — checks memory guardrails."
>
> "classify_error — explains error codes like 146 and 292."
>
> "get_weather — calls a live weather API, just to show external data."
>
> "save_report — writes the findings to a file."
>
> "The key thing: these are all just Python functions. The model can only call the ones in our approved list."

---

### SHOW ON SCREEN: `Tool checks` output

Scroll through the tool check results quickly.

> "Let's verify each tool works."
>
> "search_docs found the memory limit section — good."
>
> "parse_explain shows the query is running in SBE with a thirty-thousand-to-one scan ratio — that's very high."
>
> "queryStats shows fifty-three-to-one scan ratio on writes, with error codes 146 and 292."
>
> "serverStatus shows the one-gigabyte memory limit, 137 operations failed."
>
> "Weather pulled live from Dublin — eighteen degrees, no rain."
>
> "All tools working. Now we hand them to the agent."

---

### SHOW ON SCREEN: `The task`

Run the cell to print the task.

> "This is what we ask the agent to do."
>
> "Investigate the customer's 9.0 upgrade case."
>
> "Search the docs. Parse the explain output. Check query stats. Read server status. Classify errors."
>
> "Then save a structured report."
>
> "Notice we don't tell it the order. The model decides."

---

### SHOW ON SCREEN: `The agent loop`

Run the cell. Watch the agent work.

> "This loop is the heart of an agent."
>
> "One: send the conversation and tool list to the model."
>
> "Two: if the model asks for a tool, we run it and feed the result back."
>
> "Three: the model sees the result and decides what to do next."
>
> "Four: repeat until the model gives a final answer or we hit the step limit."
>
> "Watch — it's calling search_docs first, then parse_explain, then queryStats..."
>
> "Notice it chose the order itself. We didn't script this."
>
> "Now it's classifying the error codes — 146 and 292."
>
> "And it checked the weather — New York, nineteen degrees."

**If the model produces an empty final response:**

> "Small models sometimes need a nudge. We added a fallback — if it stops without finishing, we ask it to summarize, and we automatically save whatever it produced."

---

### SHOW ON SCREEN: `See what the agent saved`

Run the cell.

> "This is the report the agent wrote."
>
> "Symptom: slow aggregations, memory errors, change-stream lag."
>
> "Likely change: the new per-operation memory limit. The SBE engine selection changes."
>
> "Evidence: thirty-thousand-to-one scan ratio, error codes 146 and 292."
>
> "Recommendation: review queries, add indexes."
>
> "One model. Seven tools. No scripting. The agent investigated on its own."

---

### Transition to Part 3

> "The single agent did well. But one person doing both investigation and review can miss things."
>
> "In real TS work, we have different roles. One person gathers evidence. Another reviews it."
>
> "Part 3 shows how to split this into two specialized agents."

---

## PART 3 — Two-Agent Workflow (4 min)

### What we're doing

> "Two agents with different jobs."
>
> "The Investigator gathers evidence — runs diagnostics, searches docs, classifies errors."
>
> "The TS Reviewer validates the findings — cross-references with release notes, checks for unsafe advice."
>
> "The handoff is our Python code passing notes from one to the other."
>
> "This isn't a swarm of autonomous agents. It's a fixed workflow with role separation."

---

### SHOW ON SCREEN: `Setup` and `Load docs and define diagnostic functions`

Run both cells.

> "Same tools as Part 2. Same documents. The difference is how we use them."

---

### SHOW ON SCREEN: `Agent 1 — The Investigator`

Run the cell. It gathers evidence, then sends everything to the LLM for analysis.

> "The investigator runs all the diagnostic tools."
>
> "It searches the docs. Reads explain output. Checks query stats. Classifies the errors."
>
> "Then it sends all this evidence to the LLM with one prompt: analyze this and produce structured findings."
>
> "Look at the output. Symptom. Likely changes. Evidence summary. Risk level. Preliminary recommendation."
>
> "This is raw — the investigator's first pass. It might have mistakes."

---

### SHOW ON SCREEN: `Handoff` markdown

Just point to this.

> "Here's the handoff."
>
> "The reviewer gets only the investigator's findings."
>
> "Not the raw diagnostic data. Not the tools. Just the text."
>
> "Remember the context we retrieved in Part 1? Same principle. Pass only what's needed."

---

### SHOW ON SCREEN: `Agent 2 — The TS Reviewer`

Run the cell. Show the final report.

> "The reviewer has strict rules."
>
> "Cross-reference claims against the release notes."
>
> "Reject unsafe advice — never just increase the memory limit."
>
> "Flag when Engineering escalation is needed."
>
> "Note what's Atlas-only versus Enterprise versus Community."
>
> "Look at the final report. Structured. Safe. Ready for a customer."
>
> "Symptom, likely change, evidence, risk, mitigation, escalation criteria, sources."
>
> "This is the output you'd actually put in a support ticket."

---

## WRAP-UP (1 min)

### What we built

> "Let me bring it together."
>
> "We started with a real scenario: customer upgrades to MongoDB 9.0, things break."
>
> "Part 1: RAG. We showed the model doesn't know about 9.0. Then we gave it the docs and it answered from evidence."
>
> "Part 2: Agent. We gave the model tools. It chose what to use, investigated on its own, and saved a report."
>
> "Part 3: Multi-agent. We split investigation and review into two specialized roles. The investigator finds. The reviewer validates."
>
> "Three parts. One story. Each one builds on the last."

### Key takeaways

> "RAG bridges the gap between what a model knows and what your documents contain."
>
> "Agents turn models from question-answerers into investigators."
>
> "Role separation makes the output safer and more reliable."
>
> "And everything ran locally. No cloud. No API keys. Just Ollama and some Python."

### Where this goes next

> "In production, you'd swap the local embedding model for Voyage AI."
>
> "You'd connect to real MongoDB clusters instead of sample data."
>
> "You'd add more tools — log analysis, metrics dashboards, ticket creation."
>
> "But the pattern is the same. Retrieve. Investigate. Validate."

---

> **END OF PRESENTATION**
