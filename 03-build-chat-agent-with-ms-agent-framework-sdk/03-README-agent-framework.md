#### Critical Note: this code still require further troubelshooting, didnt work as expected, but the readme is here for reference and future work.
##################################################################################################################################################
# Azure AI Expenses Agent (Agent Framework)

A Python script that uses a higher-level **Agent Framework** abstraction over Azure AI to create an async expenses processing assistant that reads expense data and automatically composes and "sends" a formatted email claim.

---

## What It Does

This script reads expense data from a file, asks what you'd like to do with it, and uses an AI agent to process your request. When asked to submit an expense claim, the agent formats the data into an itemized email and sends it to the finance team — all triggered through natural language.

| Feature | Description |
|---|---|
| **Agent Framework** | Uses a high-level `ChatAgent` abstraction instead of raw Azure SDK calls |
| **Async Architecture** | Built with `asyncio` for non-blocking, efficient execution |
| **Email Tool** | Custom `send_email` function the agent calls to dispatch the claim |
| **Expense Formatting** | Agent automatically itemizes expenses and calculates totals |
| **Single-turn Interaction** | Takes one prompt and data input, processes it, and returns a response |

---

## How It Works

```
data.txt is read and displayed to the user
        ↓
User types a prompt (e.g. "Submit this as an expense claim")
        ↓
Prompt + expenses data sent to the ChatAgent
        ↓
Agent decides to call send_email() with itemized expenses and total
        ↓
Email details printed to console
        ↓
Agent confirms to the user that the claim was submitted
```

---

## Prerequisites

- Python 3.8+
- An [Azure AI Foundry](https://ai.azure.com/) project with an OpenAI model deployed
- **Azure CLI** installed and logged in (`az login`)
- The `agent_framework` package installed (provides `ChatAgent`, `AgentThread`, `AzureAIAgentClient`)
- A `data.txt` file in the same directory containing the expenses data

---

## Installation

1. **Clone the repository** and navigate to the project folder.

2. **Install dependencies:**
   ```bash
   pip install python-dotenv azure-identity agent-framework pydantic
   ```

3. **Log in to Azure CLI:**
   ```bash
   az login
   ```


4. **Add your expenses data** in a file named `data.txt` in the same directory. Example:
   ```
   Flights: $450.00
   Hotel (3 nights): $320.00
   Meals: $85.50
   Transport: $40.00
   ```

---

## Usage

```bash
python 03-agent-framework.py
```

### Example Session

```
Here is the expenses data in your file:

Flights: $450.00
Hotel (3 nights): $320.00
Meals: $85.50
Transport: $40.00

What would you like me to do with it?

Submit this as an expense claim.

To: expenses@contoso.com
Subject: Expense Claim
Please find the itemized expense claim below:

- Flights: $450.00
- Hotel (3 nights): $320.00
- Meals: $85.50
- Transport: $40.00

Total: $895.50

# Agent:
I've submitted your expense claim to expenses@contoso.com with an itemized breakdown and a total of $895.50.
```

---

## How `send_email` Works

The `send_email` function is defined with **Pydantic-annotated parameters**, which allows the agent framework to understand what each argument means and when to use it:

```python
def send_email(
    to: Annotated[str, Field(description="Who to send the email to")],
    subject: Annotated[str, Field(description="The subject of the email.")],
    body: Annotated[str, Field(description="The text body of the email.")]
):
```

In this script the function prints the email to the console rather than sending it over a real mail server — but it can be swapped for a real email integration (e.g. SendGrid, SMTP, or Microsoft Graph) with minimal changes.

---

## How It Differs from Other Agent Scripts

| Feature | Support Agent (03) | Data Agent (02) | Expenses Agent (04) |
|---|---|---|---|
| Framework | Azure AI Agents SDK | Azure AI Agents SDK | Agent Framework (higher-level) |
| Async | no | no | yes (`asyncio`) |
| Auth method | DefaultAzureCredential | DefaultAzureCredential | AzureCliCredential |
| Interaction | Multi-turn chat | Multi-turn chat | Single-turn |
| Tool output | Saves `.txt` file | Runs Python code | Sends email |
| Use case | Helpdesk tickets | Data analysis | Expense claims |

---

## Project Structure

```
.
├── 03-agent-framework.py   # Main script
├── data.txt                # Expenses data file (required at runtime)
└── .env                    # Optional — not used here (auth is via Azure CLI)
```

---

## Notes

- This script uses **`AzureCliCredential`** instead of `DefaultAzureCredential` — you must be logged into the Azure CLI (`az login`) before running it.
- The `send_email` function **does not send a real email** — it prints to the console. Replace the function body with a real mail client for production use.
- Unlike the other agents, this is a **single-turn** interaction — it takes one input and produces one response, rather than looping.
- The `agent_framework` package is a higher-level abstraction that simplifies agent setup compared to using the raw `azure-ai-agents` SDK directly.
