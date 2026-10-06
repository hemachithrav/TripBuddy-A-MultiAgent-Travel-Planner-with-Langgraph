<!-- 🤖 HEADER BANNER -->
<p align="center">
  <img src="https://capsule-render.vercel.app/api?type=waving&color=0:00c6ff,100:0072ff&height=220&section=header&text=TripBuddy%20AI&fontSize=48&fontColor=ffffff&animation=fadeIn&fontAlignY=35&desc=Agentic%20AI%20%7C%20Multi-Agent%20Systems%20%7C%20LangGraph%20%7C%20MCP&descAlignY=58&descSize=18" />
</p>

# ✈️ TripBuddy AI — Multi-Agent Travel Planner


> **An Agentic AI travel-planning system built with LangGraph, MCP, Supervisor-based orchestration, AI Guardrails, Human-in-the-Loop approval, and persistent PostgreSQL-backed agent state.**

TripBuddy AI is an end-to-end **Multi-Agent AI application** that demonstrates how LLM-powered agents can collaborate to plan personalized trips while maintaining control, safety, persistence, and human approval.

The system uses **LangGraph for stateful agent orchestration** and **Model Context Protocol (MCP)** to decouple agents from external tools and services.

Users can submit a travel request, receive a dynamically generated itinerary, review the proposed plan, provide feedback or request revisions, and approve the final result.

---

## 🚀 What This Project Demonstrates

### 🤖 Agentic AI

* Multi-Agent Systems
* Specialized AI agents
* Supervisor Agent / Agent Orchestration
* Dynamic agent routing
* Tool calling
* Stateful agent workflows
* Conditional execution
* Cost-aware agent coordination

### 🧠 LangGraph

* Graph-based agent orchestration
* Shared state across agents
* Conditional routing
* Stateful workflows
* PostgreSQL checkpointing
* Workflow interruption and resumption
* Human-in-the-loop workflows

### 🔌 Model Context Protocol (MCP)

The project demonstrates multiple MCP integration patterns:

* MCP Client
* Remote MCP Servers
* Local MCP Servers
* Custom MCP Servers
* MCP tool discovery
* Dynamic MCP tool invocation
* External service abstraction

Integrated MCP-based tools include:

* **Tavily MCP** — web/search capabilities
* **AviationStack MCP** — flight information
* **Custom Weather MCP Server** — weather information

This architecture separates agent reasoning from external tools and services, making the system more modular and extensible.

### 🛡️ AI Guardrails

The application validates incoming requests before downstream agent execution.

The guardrail layer can:

* Validate user requests
* Detect unsafe or sensitive requests
* Prevent inappropriate requests from reaching agents
* Apply model-based validation before execution

### 👤 Human-in-the-Loop

Travel plans are not blindly finalized by the AI.

The workflow supports:

```text
Generate Plan
     ↓
Human Review
   ↙     ↘
Reject   Approve
  ↓         ↓
Revise    Continue
  ↓         ↓
Regenerate Final Plan
```

Users can approve the generated plan or provide feedback that sends the workflow back for revision.

### 💾 Persistent Agent Memory

LangGraph checkpointing with PostgreSQL provides:

* Persistent conversation state
* Thread-based execution
* Workflow recovery
* Resumable agent execution
* Long-running workflow support

### 🌐 Full-Stack AI Application

The project includes:

* FastAPI backend
* REST APIs
* Interactive web UI
* HTML/CSS/JavaScript frontend
* Async Python execution
* Docker containerization
* Cloud deployment
* CI/CD

---

## 🏗️ High-Level Architecture

```text
                         ┌───────────────────┐
                         │      User         │
                         └─────────┬─────────┘
                                   │
                                   ▼
                         ┌───────────────────┐
                         │  Input Guardrail  │
                         └─────────┬─────────┘
                                   │
                                   ▼
                    ┌──────────────────────────┐
                    │  Supervisor /           │
                    │  Orchestrator Agent     │
                    └────────────┬─────────────┘
                                 │
             ┌───────────────────┼───────────────────┐
             ▼                   ▼                   ▼
      ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
      │ Flight      │     │ Hotel       │     │ Weather     │
      │ Agent       │     │ Agent       │     │ Agent       │
      └──────┬──────┘     └──────┬──────┘     └──────┬──────┘
             │                   │                   │
             └───────────────────┼───────────────────┘
                                 ▼
                       ┌───────────────────┐
                       │ Itinerary Agent   │
                       └─────────┬─────────┘
                                 │
                                 ▼
                       ┌───────────────────┐
                       │ Human Approval    │
                       │      (HITL)       │
                       └─────────┬─────────┘
                            ┌────┴────┐
                            │         │
                         Approve    Revise
                            │         │
                            ▼         └──────► Agent Workflow
                    ┌───────────────┐
                    │ Final Travel  │
                    │     Plan      │
                    └───────────────┘

         ┌──────────────────────────────────────────┐
         │              MCP Layer                   │
         │                                          │
         │ Tavily MCP │ AviationStack │ Weather MCP│
         └──────────────────────────────────────────┘

                    ┌───────────────────┐
                    │    PostgreSQL     │
                    │ LangGraph State / │
                    │   Checkpoints     │
                    └───────────────────┘
```

---

## 🧩 MCP Architecture

TripBuddy separates AI reasoning from external capabilities through MCP.

```text
                    TripBuddy AI
                         │
                    MCP Client
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
    Remote MCP       Local MCP      Custom MCP
       Server          Server          Server
          │              │              │
       Tavily       Flight Search      Weather
```

### MCP Server Types Demonstrated

| MCP Type   | Example        | Purpose                      |
| ---------- | -------------- | ---------------------------- |
| Remote MCP | Tavily         | External search capabilities |
| Local MCP  | AviationStack  | Local tool integration       |
| Custom MCP | Weather Server | Application-specific tools   |

---

## 🧠 Agent Responsibilities

| Agent           | Responsibility                            |
| --------------- | ----------------------------------------- |
| Guardrail       | Validate incoming requests                |
| Supervisor      | Determine which agents/tools are required |
| Flight Agent    | Retrieve flight information               |
| Hotel Agent     | Search accommodation information          |
| Weather Agent   | Retrieve weather information              |
| Budget Agent    | Estimate/manage travel budget             |
| Itinerary Agent | Build the overall travel itinerary        |
| Final Agent     | Generate the approved final travel plan   |

The Supervisor avoids blindly executing every agent for every request, helping reduce unnecessary LLM and tool calls.

---

## 🔄 Human-in-the-Loop Workflow

TripBuddy uses LangGraph workflow interruption to pause execution before finalization.

```text
User Request
     ↓
Guardrail
     ↓
Supervisor
     ↓
Specialized Agents
     ↓
Itinerary Generation
     ↓
Human Review
     │
     ├── Approve ──────► Final Plan
     │
     └── Request Changes
              ↓
        Re-enter Workflow
              ↓
        Updated Itinerary
```

---

## 💾 Persistent State

TripBuddy uses PostgreSQL with LangGraph checkpointing.

Each workflow is associated with a `thread_id`, allowing the application to maintain state across requests.

This enables:

* Conversation continuity
* Persistent agent state
* Human approval workflows
* Workflow resumption
* Checkpoint-based recovery

---

## 🛠️ Technology Stack

### AI / Agentic AI
![Python](https://img.shields.io/badge/Python-3.11-blue)
![LangGraph](https://img.shields.io/badge/LangGraph-Agentic_AI-orange)
![MCP](https://img.shields.io/badge/MCP-Model_Context_Protocol-purple)
![FastAPI](https://img.shields.io/badge/FastAPI-API-green)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-Database-blue)
![Docker](https://img.shields.io/badge/Docker-Container-blue)

`Generative AI` `LLMs` `Agentic AI` `Multi-Agent Systems` `AI Agents` `Supervisor Agents` `Tool Calling` `AI Guardrails` `Human-in-the-Loop`

### AI Frameworks

`LangGraph` `LangChain` `MCP` `LangChain MCP Adapters`

### Backend

`Python` `FastAPI` `AsyncIO` `REST APIs` `Uvicorn`

### Persistence

`PostgreSQL` `LangGraph Checkpointing` `PostgresSaver`

### MCP / External Tools

`Tavily` `AviationStack` `Custom Weather MCP Server`

### Frontend

`HTML` `CSS` `JavaScript`

### Deployment

`Docker` `Render` `CI/CD` `Environment Variables`

---

## 📁 Project Structure

```text
TripBuddy/
│
├── app.py
├── backend.py
├── mcp_client.py
├── custom_weather_mcp_server.py
├── requirements.txt
│
├── tools/
│   └── ...
│
├── templates/
│   └── index.html
│
├── static/
│   ├── style.css
│   └── script.js
│
└── README.md
```

---

## 🔑 Key Engineering Concepts Demonstrated

This project demonstrates practical implementation of:

* Multi-Agent AI architecture
* Agent orchestration
* Supervisor-based routing
* Stateful LangGraph workflows
* MCP client/server architecture
* Remote, local, and custom MCP integrations
* Tool discovery and invocation
* AI safety guardrails
* Human-in-the-loop approval
* Persistent agent memory
* PostgreSQL checkpointing
* Async Python
* FastAPI REST APIs
* Docker containerization
* Cloud deployment
* CI/CD
* Modular tool architecture
* Environment-based configuration
* External API integration

---

## 🎯 Why This Architecture?

Traditional LLM applications often tightly couple the model with external APIs and application logic.

TripBuddy separates these concerns:

```text
Agent Reasoning
      │
      ▼
 LangGraph
      │
      ▼
 MCP Client
      │
      ▼
 External Tools / Services
```

This allows tools to evolve independently from the agent orchestration layer and provides a standardized interface for connecting AI agents with external capabilities.

---

## ▶️ Running Locally

### 1. Clone the repository

```bash
git clone <YOUR_REPOSITORY_URL>
cd TripBuddy
```

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Windows:

```bash
.venv\Scripts\activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

### 4. Configure environment variables

Create a `.env` file with the required API keys and database configuration.

Never commit API keys, passwords, or connection strings to GitHub.

### 5. Start the application

```bash
python app.py
```

or:

```bash
uvicorn app:app --reload --host 127.0.0.1 --port 8000
```

Open:

```text
http://127.0.0.1:8000
```

---

## 🔐 Security

Secrets are intentionally excluded from source control.

Configure credentials through environment variables such as:

```text
GROQ_API_KEY=
TAVILY_API_KEY=
DATABASE_URL=
OPENWEATHER_API_KEY=
```

Use a `.env` file locally and configure secrets through the deployment platform for cloud environments.

---

## 📌 Project Highlights

**TripBuddy demonstrates an end-to-end Agentic AI architecture combining:**

> **LangGraph + Multi-Agent Systems + MCP + Supervisor Agent + Guardrails + Human-in-the-Loop + Persistent Memory + FastAPI + PostgreSQL + Docker + Cloud Deployment**

The project is designed as a practical demonstration of how modern LLM applications can move from simple prompt-based applications toward **stateful, tool-using, controlled, and human-supervised AI agents**.
