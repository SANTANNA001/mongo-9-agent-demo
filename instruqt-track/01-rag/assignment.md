---
slug: rag
id: xp70krymdree
type: challenge
title: Part 1 — RAG over MongoDB 9.0 Release Notes
teaser: Give an LLM access to documents it was never trained on.
notes:
- type: text
  contents: |
    # Part 1 — RAG (6 min)

    Welcome everyone. Today we'll build three AI systems — from simple to complex — that investigate a real MongoDB 9.0 support case.

    **The scenario:** A customer upgraded to MongoDB 9.0. Now they report slow aggregations, memory errors, and change-stream lag.

    **RAG = Retrieve, then Generate.** The LLM doesn't know about MongoDB 9.0 — its training data cut off before 9.0 existed. We'll give it the release notes and it will answer from those documents.

    Steps:
    1. Open `1_rag.ipynb` in the Code editor
    2. Run cells top to bottom
    3. Watch: model fails without context → succeeds with RAG

    Key moment: Step 3 shows the model admitting it doesn't know about 9.0. Step 6 shows the same model answering correctly from our retrieved context.
tabs:
- id: 5gjxtixzxi0d
  title: Terminal
  type: terminal
  hostname: host01
  workdir: /root/mongo-9-agent-demo
- id: 3u20qj8porek
  title: Code editor
  type: service
  hostname: host01
  port: 8080
difficulty: basic
timelimit: 600
enhanced_loading: null
---
# Part 1 — RAG: Ask MongoDB 9.0 Release Notes

**Time:** ~6 minutes

## The Scenario

A customer upgraded their sharded Atlas cluster to MongoDB 9.0. They report:
- Slow aggregations
- Memory errors on write operations
- Change-stream lag

We need to figure out which 9.0 changes could cause these symptoms.

## What is RAG?

**RAG = Retrieval-Augmented Generation.** The LLM doesn't know about MongoDB 9.0 — its training data cut off before 9.0 existed. RAG fixes this by:
1. **Retrieving** the most relevant sections from the 9.0 release notes
2. **Augmenting** the prompt with that context
3. **Generating** an answer grounded in evidence

## Steps

1. Open the **Code editor** tab on the right
2. Click `1_rag.ipynb`
3. Run each cell top to bottom with `Shift+Enter`
4. Watch what happens at Step 3 — the model is asked a 9.0 question **without** any documents
5. Continue to Step 6 — the same question, but now with RAG: the model answers from the release notes

## Check Your Understanding

After running the notebook, you should be able to answer:

- Why does the model fail without context but succeed with RAG?
- What 9.0 changes explain the customer's memory errors?
- How do embeddings (nomic-embed-text) help us find relevant documents?

---
**Once you've run all cells in `1_rag.ipynb`, click Check to continue.**
