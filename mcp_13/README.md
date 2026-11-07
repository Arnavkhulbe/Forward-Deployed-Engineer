# Model Context Protocol (MCP)

## 📌 Overview

**MCP** stands for **Model Context Protocol**.

It is a standardized protocol that allows AI applications to communicate with external **tools, resources, and prompts**.

The main problem MCP solves is the difficulty of connecting multiple AI applications with multiple external tools without creating a large number of custom integrations.

---

# 1. Why Do We Need Tools?

An LLM can understand language and generate text, but it cannot automatically perform every external operation.

For example, an LLM may not have access to:

- Current weather information
- External APIs
- Calculators
- Code execution systems
- Databases
- Calendar systems

To give an LLM these abilities, we can provide **tools**.

### Example

```text
User
  ↓
Application
  ↓
LLM
  ↓
Tool
  ↓
Result
  ↓
LLM
  ↓
User
```

The LLM decides which tool is needed, while the tool performs the actual operation.

---

# 2. What Are Tools?

A **tool** is an external capability that an AI can use to perform a specific task.

Examples:

```text
Calculator Tool
Weather Tool
Compiler Tool
Calendar Tool
Database Tool
```

### Calculator

Used for calculations.

### Weather Tool

Can obtain weather information from a weather API.

### Compiler

Can execute or compile generated code.

The important idea is:

> **The LLM decides what needs to be done, while the tool performs the actual operation.**

---

# 3. How Does the LLM Know Which Tool to Use?

The LLM needs information about the available tools.

Each tool can provide:

- Tool name
- Tool description
- Input parameters
- Parameter types
- Parameter descriptions

For example:

```text
Create Task
Description:
Creates a new task.

Input:
title → string
```

The LLM can then understand what the tool does and what information it needs.

---

# 4. Structured Output

When an LLM communicates with a tool, it needs to provide information in a structured format.

For example:

```text
Tool: create_task

title: "Go to Gym"
```

The tool understands the structure and can execute the requested operation.

This is why **structured output** is important in tool calling.

---

# 5. The Problem With Multiple Applications

Suppose we have several applications:

```text
Application 1
Application 2
Application 3
Application 4
Application 5
```

And all of them need the same tools:

```text
Calculator
Weather
Compiler
```

Without a common system, every application would need its own integration with every tool.

This creates a large number of integrations.

---

# 6. The M × N Problem

Suppose we have:

- `5` applications
- `10` tools

Then we potentially have:

```text
5 × 10 = 50 integrations
```

As the number of applications and tools increases, maintaining these integrations becomes difficult.

This is one of the main problems MCP is designed to solve.

---

# 7. What Is MCP?

## Model Context Protocol

**MCP = Model Context Protocol**

The word **Protocol** is important.

A protocol is a standardized set of rules that defines how systems communicate.

Examples include:

```text
HTTP
SMTP
```

Similarly, MCP provides standardized rules for communication between AI applications and external capabilities.

### Simple Definition

> **MCP is a standard protocol that allows AI applications to connect with external tools and data.**

---

# 8. MCP Architecture

The basic MCP architecture contains three important concepts:

```text
Host
MCP Client
MCP Server
```

---

# 9. MCP Server

The **MCP Server** is where external AI capabilities are exposed.

For example:

```text
MCP Server
│
├── Create Task
├── List Tasks
├── Complete Task
├── Calculator
├── Weather
└── Calendar
```

An MCP Server can expose:

- Tools
- Resources
- Prompts

The MCP Server does not necessarily need to contain an LLM.

---

# 10. MCP Client

The **MCP Client** is responsible for communicating with the MCP Server.

```text
MCP Client
     ↕
MCP Server
```

The MCP Client can ask the server:

> "What tools do you have?"

The server can respond with information about its available tools.

The client can also request the execution of a specific tool.

---

# 11. Host

The **Host** is the overall AI application.

It can contain:

```text
Host
│
├── Application Server
├── LLM
└── MCP Client
```

The MCP Client is therefore a part of the Host application.

---

# 12. Important Difference

Do not confuse:

### User Client

The interface through which the user interacts with the application.

Examples:

```text
Frontend
Chat Interface
Postman
```

### MCP Client

The component responsible for communicating with an MCP Server.

They are different components.

---

# 13. Service Discovery

One of the important MCP concepts is **Service Discovery**.

When an MCP Client connects to an MCP Server, it needs to discover what capabilities are available.

Conceptually:

```text
MCP Client
    │
    │ "What tools do you have?"
    ↓
MCP Server
    │
    │ Tool definitions
    ↓
MCP Client
```

The server provides information such as:

- Tool name
- Description
- Input schema
- Parameters
- Parameter types

The LLM can then use this information to decide which tool is appropriate.

---

# 14. Example: Todo Application

Suppose we have an AI-powered Todo application.

The MCP Server provides three tools:

```text
Create Task
List Task
Complete Task
```

### Create Task

Creates a new task.

### List Task

Returns the list of tasks.

### Complete Task

Marks a task as completed.

---

# 15. Complete MCP Flow

Suppose the user says:

> "Add Go to Gym as my task."

The process is:

```text
User
 ↓
Application
 ↓
LLM
 ↓
Select Create Task
 ↓
MCP Client
 ↓
MCP Server
 ↓
Create Task Tool
 ↓
Task Created
 ↓
MCP Client
 ↓
LLM
 ↓
User
```

---

# 16. Step-by-Step Flow

### Step 1 — User sends a request

```text
"Add Go to Gym as my task."
```

### Step 2 — Application sends the request to the LLM

The LLM also has information about the available MCP tools.

### Step 3 — LLM chooses the appropriate tool

The LLM sees:

```text
Create Task → Creates a task
List Task → Lists tasks
Complete Task → Completes a task
```

It chooses:

```text
Create Task
```

### Step 4 — LLM creates a structured tool call

Conceptually:

```text
create_task
title = "Go to Gym"
```

### Step 5 — MCP Client sends the request

```text
MCP Client
    ↓
MCP Server
```

### Step 6 — MCP Server executes the tool

The task is created.

Result:

```text
Task Created
```

### Step 7 — Result goes back to the LLM

```text
MCP Server
 ↓
MCP Client
 ↓
LLM
```

### Step 8 — LLM creates the final response

The LLM can turn:

```text
Task Created
```

into a natural-language response.

---

# 17. MCP vs REST API

MCP does **not** replace REST APIs.

REST APIs and MCP have different purposes.

### REST API

A REST API usually exposes specific endpoints.

For example:

```text
POST /create
GET  /list
POST /complete
```

The application needs to know these endpoints and how to interact with them.

### MCP

MCP provides a standardized approach for AI applications to:

- Discover available tools
- Understand tool descriptions
- Understand input schemas
- Call tools
- Receive tool results

Therefore, MCP helps standardize the interaction between AI applications and external capabilities.

---

# 18. Why MCP Helps

Without MCP:

```text
Application 1 ─── Tool 1
Application 1 ─── Tool 2
Application 1 ─── Tool 3

Application 2 ─── Tool 1
Application 2 ─── Tool 2
Application 2 ─── Tool 3
```

Each application needs its own integrations.

With MCP:

```text
                 MCP Server
              /      |      \
           Tool 1  Tool 2  Tool 3
              ↑
              │
         MCP Client
              ↑
              │
        AI Application
```

Another application can connect using an MCP Client without implementing every tool integration from scratch.

---

# 19. MCP Server Capabilities

An MCP Server can expose three important types of information:

```text
Tools
Resources
Prompts
```

---

# 20. Tools

Tools answer:

> **What can the AI DO?**

Examples:

```text
Create Task
Calculate
Get Weather
Send Email
Query Database
Create Calendar Event
```

Tools perform actions.

---

# 21. Resources

Resources answer:

> **What information can the AI ACCESS?**

Examples:

```text
Company Documentation
Refund Policy
Return Policy
Payment Documents
PDFs
Other Data
```

Resources provide information to the AI.

---

# 22. MCP Resources and RAG

MCP Resources can be related to the concept of **RAG**.

Suppose an MCP Server has many documents:

```text
Refund Policy
Return Policy
Payment Policy
Company Documentation
```

Instead of sending every document to the LLM, we can retrieve only the relevant information.

Conceptually:

```text
Documents
    ↓
Retrieve Relevant Information
    ↓
Relevant Chunks
    ↓
LLM
```

This helps avoid unnecessarily filling the LLM's context with irrelevant information.

---

# 23. Prompts

Prompts answer:

> **What reusable instructions can we provide?**

Sometimes we have instructions that are used repeatedly.

Instead of asking the user to write the same instructions every time, reusable prompts can be stored and made available.

Conceptually:

```text
MCP Server
    ↓
Reusable Prompt
    ↓
LLM
```

---

# 24. Easy Way to Remember MCP

Remember these three:

| MCP Component | Meaning |
|---|---|
| **Tools** | What AI can **DO** |
| **Resources** | What AI can **KNOW / ACCESS** |
| **Prompts** | Reusable **INSTRUCTIONS** |

---

# 25. Important MCP Terms

| Term | Meaning |
|---|---|
| **LLM** | Understands requests and decides what should happen |
| **Tool** | Performs a specific action |
| **MCP Server** | Exposes tools, resources and prompts |
| **MCP Client** | Communicates with MCP Servers |
| **Host** | Overall AI application containing the LLM and MCP Client |
| **Protocol** | Standard rules for communication |
| **Service Discovery** | Discovering available tools and their definitions |
| **Structured Output** | Structured information used for reliable communication |

---

# 26. Complete Architecture

```text
                    HOST
        ┌─────────────────────────┐
        │                         │
        │   Application Server    │
        │           ↓             │
        │          LLM            │
        │           ↓             │
        │      MCP Client         │
        │           │             │
        └───────────┼─────────────┘
                    │
               MCP Protocol
                    │
                    ↓
        ┌─────────────────────────┐
        │       MCP SERVER        │
        │                         │
        │        Tools            │
        │        Resources        │
        │        Prompts          │
        └─────────────────────────┘
```

---

# 27. Most Important Flow to Remember

```text
User
 ↓
Host
 ↓
LLM
 ↓
MCP Client
 ↓
MCP Server
 ↓
Tool
 ↓
MCP Server
 ↓
MCP Client
 ↓
LLM
 ↓
User
```

---

# 28. Key Takeaways

- **MCP = Model Context Protocol**
- MCP provides a standardized way for AI applications to communicate with external capabilities.
- **MCP Server** exposes tools, resources and prompts.
- **MCP Client** communicates with the MCP Server.
- **Host** is the overall AI application.
- Tools allow the AI to perform actions.
- Resources provide information to the AI.
- Prompts provide reusable instructions.
- **Service Discovery** allows the client to discover available tools and their schemas.
- MCP helps reduce the need for many custom integrations.
- MCP does not make REST APIs useless; REST APIs can still be used inside MCP-based systems.
- MCP can work together with **RAG** and other AI architecture components.

---

## 🧠 One-Line Definition

> **MCP is a standardized protocol that allows AI applications to discover and use external tools, resources, and prompts through an MCP Client and MCP Server.**
