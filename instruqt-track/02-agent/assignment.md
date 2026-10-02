---
slug: agent
id: jvmzf3db8elf
type: challenge
title: Part 2 — Single Agent with Diagnostic Tools
teaser: Give the model tools and let it investigate on its own.
notes:
- type: text
  contents: |
    # Part 2 — Single Agent (7 min)

    Now that we've seen RAG — where we pick the question and the model answers from documents — let's give the model tools and let it decide what to do.

    **An agent is a model that can choose which tools to use.** We give it 7 tools:
    - search_docs — find relevant 9.0 changes
    - parse_explain — read the customer's explain() output
    - check_queryStats — analyze write patterns
    - read_serverStatus — check memory guardrails
    - classify_error — explain error codes
    - get_weather — pull live weather data
    - save_report — write findings to a file

    The model chooses which tools to run, and in what order. We only run approved tools.

    Steps:
    1. Open `2_agent.ipynb`
    2. Run cells top to bottom
    3. Watch the agent loop — it calls tools, reads results, decides next steps

    Key moment: The agent loop shows the model autonomously choosing tools — search_docs, parse_explain, check_queryStats — without us scripting the order.
tabs:
- id: 6pkkho9w1rzg
  title: Terminal
  type: terminal
  hostname: host01
  workdir: /root/mongo-9-agent-demo
- id: me3d5ysl36bp
  title: Code editor
  type: service
  hostname: host01
  port: 8080
difficulty: basic
timelimit: 600
enhanced_loading: null
---
# Part 2 — One Agent with Diagnostic Tools

**Time:** ~7 minutes

## What is an agent?

An **agent** is a model that can choose which tools to use. Instead of us telling it "run this, then that", we give it a list of approved tools and a task. The model decides the order.

We give it 7 tools:
| Tool | What it does |
|---|---|
| `search_docs` | Searches the 9.0 release notes |
| `parse_explain` | Reads the customer's explain() output |
| `check_queryStats` | Analyzes write operation patterns |
| `read_serverStatus` | Checks memory guardrails and SBE usage |
| `classify_error` | Explains MongoDB error codes |
| `get_weather` | Pulls live weather data (shows external API) |
| `save_report` | Writes findings to a file |

## Steps

1. Open the **Code editor** tab on the right
2. Click `2_agent.ipynb`
3. Run cells top to bottom
4. The **Tool checks** cell verifies all tools work
5. The **Agent loop** cell shows the model investigating — watch it choose tools autonomously
6. The **See what the agent saved** cell shows the diagnostic report

## What to watch for

- The model calls tools in its own order — we didn't script this
- It classified error codes 146 and 292 from the release notes
- It identified the per-operation memory limit as the likely cause
- It checked live weather from an external API

## Check Your Understanding

- What makes this an "agent" vs just RAG?
- Why is the `TOOLS` dictionary a safety mechanism?
- What error codes signal a 9.0 memory guardrail issue?

---
**Once you've run all cells in `2_agent.ipynb`, click Check to continue.**
