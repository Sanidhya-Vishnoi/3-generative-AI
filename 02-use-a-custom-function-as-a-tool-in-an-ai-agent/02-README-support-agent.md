# Azure AI Technical Support Agent

A Python script that uses **Azure AI Agents** with **custom function calling** to create a technical support agent that collects user details and automatically generates support ticket files.

---

## What It Does

This script creates a conversational technical support agent. When a user describes a problem, the agent collects their email address and issue description, then calls a **custom Python function** to generate and save a support ticket file — all without the user needing to fill out any forms.

| Feature | Description |
|---|---|
| **Custom Function Tool** | Agent can call your own Python functions to take real-world actions |
| **Auto Function Calling** | Agent decides when to call functions automatically based on context |
| **Support Ticket Generation** | Creates a uniquely numbered `.txt` ticket file on disk |
| **Conversational Flow** | Agent naturally gathers required info through dialogue |
| **Conversation Log** | Prints the full chat history at the end of the session |
| **Auto Cleanup** | Deletes the agent from Azure after the session ends |

---

## How It Works

```
User reports a technical issue
        ↓
Agent asks for email address and issue description
        ↓
Agent calls submit_support_ticket(email, description)
        ↓
Function generates a unique ticket number and saves a .txt file
        ↓
Agent tells the user their ticket number and file name
```

---

## Files

### `02-agent.py` — Main Script
Sets up the Azure AI Agent with a `FunctionTool`, creates a conversation thread, and runs the interactive support chat loop.

### `02-user_functions.py` — Custom Functions
Defines the `submit_support_ticket()` function that the agent can call, and exports the `user_functions` set for the agent to use.

---

## Prerequisites

- Python 3.8+
- An [Azure AI Foundry](https://ai.azure.com/) project with an OpenAI model deployed (e.g. `gpt-4o`)
- Your project endpoint and model deployment name

---

## Installation

1. **Clone the repository** and navigate to the project folder.

2. **Install dependencies:**
   ```bash
   pip install python-dotenv azure-identity azure-ai-agents
   ```

3. **Set up your environment variables** by creating a `.env` file in the project root:
   ```env
   PROJECT_ENDPOINT=https://<your-project>.services.ai.azure.com/api/projects/<your-project-name>
   MODEL_DEPLOYMENT_NAME=your_model_deployment_name
   ```
 #Note: the environment variable names must match those used in the code, optionally you can store these in Azure Key Vault (AKV) & access  using managed identity.

4. Make sure `03-agent.py` and `03-user_functions.py` are in the **same directory**.

---

## Usage

```bash
python 02-agent.py
```

### Example Session

```
You're chatting with: support-agent (asst_abc123)

Enter a prompt (or type 'quit' to exit): My screen keeps going black randomly.
Last Message: I'm sorry to hear that! Could I get your email address to log a support ticket?

Enter a prompt (or type 'quit' to exit): john@example.com
Last Message: Thanks John! Just to confirm — the issue is your screen going black randomly. Shall I submit this ticket?

Enter a prompt (or type 'quit' to exit): Yes please.
Last Message: Done! Your support ticket has been submitted and saved as ticket-a3f91b.txt.

Enter a prompt (or type 'quit' to exit): quit

Conversation Log:

user: My screen keeps going black randomly.
agent: I'm sorry to hear that! Could I get your email address...
...

Deleted agent
```

### Example Ticket File (`ticket-a3f91b.txt`)

```
Support ticket: a3f91b
Submitted by: john@example.com
Description:
My screen keeps going black randomly.
```

---

## How `submit_support_ticket` Works

The function in `03-user_functions.py`:

1. Generates a **unique 6-character ticket number** using `uuid4()`
2. Creates a file named `ticket-<number>.txt` in the script's directory
3. Writes the ticket number, email, and description to the file
4. Returns a JSON message confirming the file name — which the agent reads and relays to the user

To add more functions for the agent to use, simply define them in `03-user_functions.py` and add them to the `user_functions` set.

---

## How It Differs from the Data Agent (02-agent.py)

| Feature | Data Agent (02) | Support Agent (03) |
|---|---|---|
| Tool used | Code Interpreter | Custom Function Tool |
| What it can do | Write & run Python code | Call your own defined functions |
| File input | Uploads `data.txt` for analysis | No file input needed |
| Output | Data insights & statistics | Support ticket `.txt` files |
| Use case | Data analysis | Helpdesk / support automation |

---

## Project Structure

```
.
├── 02-agent.py            # Main agent script
├── 02-user_functions.py   # Custom callable functions for the agent
├── .env                   # Your Azure credentials (do not commit!)
└── ticket-<id>.txt        # Generated support tickets (created at runtime)
```

---

## Notes

- Never commit your `.env` file to version control. Add it to `.gitignore`.
- The agent is automatically **deleted from Azure** at the end of each session.
- Ticket files are saved in the **same directory as the script** and persist after the session ends.
- Authentication uses `DefaultAzureCredential` — you may need to be logged in via the **Azure CLI** (`az login`) for this to work locally.
- To extend the agent's capabilities, add more functions to `03-user_functions.py` and include them in the `user_functions` set.
