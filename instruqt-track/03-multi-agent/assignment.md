---
slug: multi-agent
id: mn3bzywmbzsd
type: challenge
title: Part 3 — Two-Agent Workflow
teaser: Split investigation and review into two specialized roles.
notes:
- type: text
  contents: |
    # Part 3 — Two-Agent Workflow (4 min)

    The single agent did well. But one model doing both investigation and review can miss things.

    In real TS work, we have different roles. One person gathers evidence. Another reviews it.

    **We split the work across two agents:**
    - Investigator — runs diagnostic tools, gathers raw evidence
    - TS Reviewer — cross-references findings with release notes, rejects unsafe advice, produces a structured report

    The handoff is Python code passing notes from one agent to the other. This is role separation, not an autonomous swarm.

    Key moment: Watch the reviewer reject unsafe recommendations. If the investigator had suggested "just increase the memory limit", the reviewer would flag it.
tabs:
- id: 575tcjqdqb7x
  title: Terminal
  type: terminal
  hostname: host01
  workdir: /root/mongo-9-agent-demo
- id: 2wdh4dfppha9
  title: Code editor
  type: service
  hostname: host01
  port: 8080
difficulty: basic
timelimit: 600
enhanced_loading: null
---
# Part 3 — Two-Agent Workflow

**Time:** ~4 minutes

## Why two agents?

The single agent in Part 2 investigated and reported in one pass. But in real TS work, investigation and review are different roles:
- **Investigator** — gathers facts, runs diagnostics
- **Reviewer** — validates findings, checks for unsafe advice, produces the final report

## How it works

1. The **Investigator** runs all diagnostic tools, collects evidence, produces raw findings
2. The **TS Reviewer** receives only those findings — not the raw tool output
3. The reviewer cross-references claims against the release notes
4. It rejects unsafe advice (like "just increase the memory limit")
5. It produces a structured report: symptom → evidence → risk → mitigation → escalation

## Steps

1. Open the **Code editor** tab on the right
2. Click `3_multi_agent.ipynb`
3. Run cells top to bottom
4. Watch the **Investigator** section gather evidence and produce preliminary findings
5. See the **Handoff** — only the findings text passes between agents
6. Watch the **TS Reviewer** validate and produce the final structured report

## What to watch for

- The reviewer has strict rules: never recommend disabling guardrails
- It distinguishes fact vs inference vs recommendation
- It flags when Engineering escalation is needed
- The output is a support-ready report you could put in a ticket

## Check Your Understanding

- How does role separation make the output safer?
- What does the reviewer check that the investigator might miss?
- Where would Voyage AI embeddings fit in production?

---
**Once you've run all cells in `3_multi_agent.ipynb`, click Check to continue.**
