#### Critical Note: this code still require further troubelshooting, didnt work as expected, but the readme is here for reference and future work.
##################################################################################################################################################
# Azure AI Multi-Agent Sequential Workflow

A Python script that uses the **Agent Framework** to orchestrate multiple AI agents in a sequential pipeline — each one processing customer feedback and passing its output to the next.

---

## What It Does

This script runs a **three-stage agent pipeline** that automatically summarizes, classifies, and recommends an action for a piece of customer feedback. Each agent builds on the previous one's output, creating a structured, automated triage workflow.

| Agent | Role | Example Output |
|---|---|---|
| **Summarizer** | Condenses feedback into one neutral sentence | *"User requests a dark mode feature."* |
| **Classifier** | Labels it as Positive, Negative, or Feature Request | *"Feature request"* |
| **Action** | Suggests next step based on summary + classification | *"Log as enhancement request for product backlog."* |

---

## How It Works

```
Customer feedback (hardcoded string)
        ↓
Summarizer agent — condenses into one sentence
        ↓
Classifier agent — assigns a category
        ↓
Action agent — recommends what to do next
        ↓
All outputs displayed in order
```

This pattern is called **Sequential Orchestration** — agents run one after another, each receiving the full conversation history so far.

---

## Prerequisites

- Python 3.8+
- An [Azure AI Foundry](https://ai.azure.com/) project with an OpenAI model deployed
- **Azure CLI** installed and logged in (`az login`)
- The `agent_framework` package installed

---

## Installation

1. **Install dependencies:**
   ```bash
   pip install agent-framework azure-identity
   ```

2. **Log in to Azure CLI:**
   ```bash
   az login
   ```

---

## Usage

```bash
python 04-agents.py
```

### Example Output

```
------------------------------------------------------------
01 [summarizer]
User requests a dark mode option for the dashboard.

------------------------------------------------------------
02 [classifier]
Feature request

------------------------------------------------------------
03 [action]
Log as enhancement request for product backlog.
```

---

## How It Differs from Other Agent Scripts

| Feature | Support Agent (03) | Expenses Agent (04) | Multi-Agent (05) |
|---|---|---|---|
| Number of agents | 1 | 1 | 3 (pipeline) |
| Orchestration | Single thread loop | Single turn | Sequential workflow |
| Input | User typed | File + prompt | Hardcoded feedback |
| Use case | Helpdesk | Expenses | Feedback triage |

---

## Project Structure

```
.
└── 04-agents.py   # Main script (no additional files needed)
```

---

## Notes

- The feedback string is **hardcoded** in the script. To process different feedback, update the `feedback` variable in `main()`.
- Authentication uses **`AzureCliCredential`** — run `az login` before executing the script.
- All three agents share the same underlying model deployment configured in the `AzureAIAgentClient`.
- The `agent_framework` package is a higher-level abstraction — it handles agent creation, message passing, and streaming internally.
