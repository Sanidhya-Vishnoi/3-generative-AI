# Azure AI Data Analysis Agent

A Python script that uses **Azure AI Agents** to create an autonomous AI agent capable of analyzing a data file, writing and executing Python code, and answering questions about the data — all in a conversational interface.

---

## What It Does

This script spins up an AI agent equipped with a **Code Interpreter** tool. You upload a data file and ask questions about it in plain English. The agent figures out what calculations or analysis are needed, writes Python code to do it, executes that code, and returns the results — all automatically.

| Feature | Description |
|---|---|
| **Code Interpreter Tool** | Agent writes and runs Python code to answer data questions |
| **File Upload** | Uploads a local `data.txt` file for the agent to analyze |
| **Conversation Thread** | Maintains a persistent thread for the full session |
| **Conversation Log** | Prints the complete chat history at the end of the session |
| **Auto Cleanup** | Deletes the agent from Azure after the session ends |

---

## What is an AI Agent?

Unlike a basic chat model, an **AI Agent** can take actions — not just generate text. In this case the agent is given a **Code Interpreter** tool, which means it can:

```
User asks: "What is the average sales value?"
        ↓
Agent decides it needs to calculate a mean
        ↓
Agent writes Python code to compute it
        ↓
Agent executes the code
        ↓
Agent returns the result in plain English
```

This makes it far more powerful than a regular chatbot for data tasks — it can calculate statistics, find trends, generate summaries, and more.

---

## Prerequisites

- Python 3.8+
- An [Azure AI Foundry](https://ai.azure.com/) project with an OpenAI model deployed (e.g. `gpt-4o`)
- A `data.txt` file in the same directory as the script containing the data to analyze

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
4. **Add your data file** named `data.txt` in the same directory as the script.

---

## Usage

```bash
python 01-agent.py
```

### Example Session

```
[contents of data.txt printed here]

Uploaded data.txt
Using agent: data-agent

Enter a prompt (or type 'quit' to exit): What is the average monthly revenue?
Last Message: The average monthly revenue is $24,350.

Enter a prompt (or type 'quit' to exit): Which month had the highest sales?
Last Message: August had the highest sales at $31,200.

Enter a prompt (or type 'quit' to exit): quit

Conversation Log:

user: What is the average monthly revenue?
agent: The average monthly revenue is $24,350.

user: Which month had the highest sales?
agent: August had the highest sales at $31,200.
```

---

## How It Differs from Other Scripts

| Feature | Chat App (02) | RAG App (03) | Agent (02) |
|---|---|---|---|
| Knowledge source | General AI | Search index | Uploaded file |
| Can run code | no | no | yes |
| Data analysis | no | no | yes |
| Tool use | None | Azure Search | Code Interpreter |
| Session cleanup | None | None | Deletes agent after use |

---

## Project Structure

```
.
├── 01-agent.py   # Main script
├── data.txt      # Data file to be analyzed
└── .env          # Your Azure credentials (do not commit!)
```

---

## Notes

- Never commit your `.env` file to version control. Add it to `.gitignore`.
- The agent is automatically **deleted from Azure** at the end of each session via `delete_agent()` — this keeps your Azure project clean and avoids leaving unused resources behind.
- The agent creates a **new thread** each run, so conversation history does not persist between separate script executions.
- Authentication uses `DefaultAzureCredential` — you may need to be logged in via the **Azure CLI** (`az login`) for this to work locally.
- The Code Interpreter tool has access only to the uploaded `data.txt` file — it cannot access the internet or other files.
