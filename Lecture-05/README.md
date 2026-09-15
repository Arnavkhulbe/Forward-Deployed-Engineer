# 🤖 AI Agents — LLMs, Tools, Loops & Sandboxing

## 📌 Overview

An **AI Agent** is more than just an LLM that generates text.

A raw LLM can understand a request and generate a response, but it generally cannot directly perform actions such as:

* Creating files
* Modifying files
* Running specific operations
* Calling external APIs
* Checking the result of an action

An AI Agent solves this problem by combining:

> **LLM + Tools + Loop**

The LLM acts as the **decision maker**, while tools allow it to perform deterministic actions.

---

# 1. Chatbots vs AI Agents

### Traditional LLM

A user might ask:

```text
Create a portfolio website for me.
```

The LLM can generate:

```text
index.html
style.css
script.js
```

But the LLM itself does not necessarily have access to the user's filesystem to create these files.

### AI Agent

An AI Agent can be given tools such as:

```text
createFile()
writeFile()
readFile()
createDirectory()
```

Now the LLM can decide which tool to use and in what order.

Therefore:

```text
User Request
     ↓
    LLM
     ↓
Decision
     ↓
Tool
     ↓
Action
     ↓
Observation
     ↓
LLM
     ↓
Next Decision
```

---

# 2. Deterministic vs Non-Deterministic Systems

Understanding this difference is important for understanding agents.

## 🧠 LLM — Non-Deterministic

An LLM predicts tokens based on probabilities.

For example:

```text
"What is 94291195 × 34576?"
```

An LLM may produce an incorrect answer because mathematical calculation is not its primary mechanism.

LLMs are excellent at:

* Understanding language
* Reasoning
* Generating text
* Making decisions
* Selecting appropriate tools

But they can sometimes make mistakes.

---

## 🛠️ Tools — Deterministic

A normal programming function behaves predictably.

For example:

```java
public long multiply(long a, long b) {
    return a * b;
}
```

For the same input:

```text
94291195 × 34576
```

the function produces the same result.

Tools can therefore handle tasks that require reliable execution.

---

# 3. The Agent Architecture

The basic architecture is:

```text
                 ┌──────────────┐
                 │     User     │
                 └──────┬───────┘
                        ↓
                 ┌──────────────┐
                 │     LLM      │
                 │ Decision Maker│
                 └──────┬───────┘
                        ↓
                  Select Tool
                        ↓
                 ┌──────────────┐
                 │     Tool     │
                 └──────┬───────┘
                        ↓
                    Execute
                        ↓
                   Observation
                        ↓
                       LLM
                        ↓
                 Next Decision
```

The LLM does not necessarily perform every operation itself.

Instead, it determines:

> **What should happen next?**

The tool performs the actual operation.

---

# 4. Calculator Tool

A simple calculator is a good example of tool calling.

The LLM might determine that multiplication is required and produce structured arguments:

```json
{
  "operation": "multiply",
  "a": 94291195,
  "b": 34576
}
```

The application then executes the corresponding Java function.

The result is returned to the LLM.

The LLM can then convert the result into a natural-language response.

### Flow

```text
User
 ↓
LLM
 ↓
"Need multiplication"
 ↓
Calculator Tool
 ↓
Exact calculation
 ↓
Result
 ↓
LLM
 ↓
Final response
```

---

# 5. Tool Descriptions

When using Spring AI, tools can be described using annotations such as:

```java
@Tool(description = "Performs arithmetic operations")
```

Parameters can be described using:

```java
@ToolParam(description = "First number")
```

These descriptions are important because the LLM uses them to understand:

* What the tool does
* When it should be used
* What parameters it requires
* What values those parameters should contain

### Important

Tool descriptions should be **precise**.

For example, if the application expects:

```text
ADD
SUBTRACT
MULTIPLY
DIVIDE
```

the instructions should clearly communicate these allowed values.

If the LLM sends an unexpected value such as:

```text
"plus"
```

while the code expects:

```text
"ADD"
```

the tool may fail.

---

# 6. Weather Tool

LLMs may not have access to current weather information.

Instead, an agent can use a weather API.

### Flow

```text
User
 ↓
"What is the weather in Delhi?"
 ↓
LLM
 ↓
Weather Tool
 ↓
Weather API
 ↓
Current weather data
 ↓
LLM
 ↓
Natural-language response
```

The tool can use an HTTP client such as Spring's `RestClient` to communicate with the external API.

Example input:

```text
city = "Delhi"
```

The tool then retrieves the weather information and returns it to the LLM.

---

# 7. Currency Conversion Tool

The same concept can be applied to currency conversion.

For example:

```text
from = EUR
to = GBP
```

The tool calls a currency API and obtains the current exchange rate.

The LLM then receives the result.

---

# 8. Multi-Step Tool Calling

Agents become more interesting when a task requires multiple tools.

Consider:

```text
I have 5000 Euros. How many Pounds will I get?
```

The agent may need both:

* Currency Tool
* Calculator Tool

### Step 1 — Get Exchange Rate

The LLM determines:

```text
EUR → GBP
```

It calls the currency tool.

The tool might return:

```text
1 EUR = 0.85 GBP
```

### Step 2 — Perform Calculation

The LLM now determines:

```text
5000 × 0.85
```

It calls the calculator tool.

Result:

```text
4250
```

### Step 3 — Generate Final Response

The LLM uses the result to generate the final response.

```text
5000 EUR ≈ 4250 GBP
```

### Complete Flow

```text
User
 ↓
LLM
 ↓
Currency Tool
 ↓
Exchange Rate
 ↓
LLM
 ↓
Calculator Tool
 ↓
Calculation Result
 ↓
LLM
 ↓
Final Answer
```

This demonstrates the **iterative nature of an AI Agent**.

---

# 9. What Defines an AI Agent?

An AI Agent can be understood through six major components.

## 1. Goal

The objective that needs to be achieved.

Example:

```text
Create a portfolio website.
```

## 2. Decision Maker

Usually an LLM.

It decides what action should happen next.

## 3. Action

The actual tool execution.

Example:

```text
createFile()
```

## 4. Environment

The system where the action occurs.

For example:

```text
Local filesystem
Server
Database
External API
```

## 5. Observation

The result of the action.

Example:

```text
File successfully created.
```

## 6. Loop

The agent repeatedly performs:

```text
Decision → Action → Observation
```

until the goal is completed.

---

# 10. Website Builder Agent

The main project in this lecture is a **Website Builder Agent**.

The goal is:

```text
Build a static website based on the user's request.
```

The agent needs access to filesystem operations.

However, giving an LLM unrestricted access to the terminal or operating system is dangerous.

---

# 11. Why Sandboxing Is Important

An LLM can make mistakes or generate an unintended command.

Giving it unrestricted terminal access could allow destructive operations.

Therefore, instead of allowing:

```text
Run Any Command
```

we provide a limited set of controlled tools.

The agent is also restricted to a specific directory.

For example:

```text
generated_sites/
```

The agent can work inside this directory but cannot access arbitrary locations on the computer.

---

# 12. Website Tool Set

Instead of providing unrestricted terminal access, four controlled tools are created.

### 1. Create Directory

```java
createDirectory(path)
```

Creates a directory inside the allowed workspace.

---

### 2. Create File

```java
createFile(path)
```

Creates a new file.

Example:

```text
portfolio/index.html
```

---

### 3. Write File

```java
writeFile(path, content)
```

Writes HTML, CSS, JavaScript, or other content into a file.

---

### 4. Read File

```java
readFile(path)
```

Reads an existing file.

This can be used for verification and observation.

---

# 13. Safe Path Validation

Every filesystem operation should validate the requested path.

A helper such as:

```java
safePath(path)
```

can be used before performing the operation.

The purpose is to ensure that the requested file remains inside the permitted directory.

For example:

```text
generated_sites/portfolio/index.html
```

is allowed.

But something attempting to escape the directory, such as:

```text
../../etc/passwd
```

should be rejected.

Similarly, paths targeting unrelated system directories should not be allowed.

### Concept

```text
LLM requests path
       ↓
   safePath()
       ↓
 ┌─────┴─────┐
 ↓           ↓
Valid      Invalid
 ↓           ↓
Execute    Reject
```

This provides an important security boundary between the agent and the operating system.

---

# 14. Website Creation Workflow

Suppose the user says:

```text
Create a portfolio website.
```

The agent can work through several steps.

### Step 1 — Understand the Goal

The LLM determines:

```text
I need to create a portfolio website.
```

---

### Step 2 — Create Directory

The LLM calls:

```text
createDirectory("portfolio")
```

---

### Step 3 — Create HTML File

The LLM calls:

```text
createFile("portfolio/index.html")
```

---

### Step 4 — Write HTML

The LLM generates the HTML and calls:

```text
writeFile(
    "portfolio/index.html",
    "<html>...</html>"
)
```

---

### Step 5 — Verify

The agent can call:

```text
readFile("portfolio/index.html")
```

to inspect the created content.

---

### Step 6 — Create CSS

```text
createFile("portfolio/style.css")
```

Then:

```text
writeFile("portfolio/style.css", "...")
```

---

### Step 7 — Create JavaScript

```text
createFile("portfolio/script.js")
```

Then:

```text
writeFile("portfolio/script.js", "...")
```

---

### Step 8 — Continue the Loop

The LLM continues deciding what needs to happen next.

```text
Decision
   ↓
Tool
   ↓
Action
   ↓
Observation
   ↓
Decision
   ↓
Tool
   ↓
Action
```

The loop continues until the website is completed.

---

# 15. Complete Agent Workflow

```text
                USER
                  │
                  ▼
            ┌───────────┐
            │    LLM    │
            └─────┬─────┘
                  │
            Decide Action
                  │
                  ▼
        ┌──────────────────┐
        │   Available Tools │
        ├──────────────────┤
        │ createDirectory() │
        │ createFile()      │
        │ writeFile()       │
        │ readFile()        │
        └────────┬─────────┘
                 │
                 ▼
             Execute
                 │
                 ▼
            Observation
                 │
                 ▼
                LLM
                 │
          Goal Completed?
            /          \
          No            Yes
          │              │
          ▼              ▼
      Next Action      Response
```

---

# 16. Technologies Used

## Spring AI

Used to connect the Java application with the LLM and expose tools to the model.

## Spring `@Tool`

Used to describe functions that the LLM can call.

```java
@Tool(description = "...")
```

## `@ToolParam`

Used to describe tool parameters.

```java
@ToolParam(description = "...")
```

## RestClient

Used to communicate with external APIs such as:

* Weather APIs
* Currency APIs

## Java File I/O

Used to perform controlled filesystem operations:

* Creating directories
* Creating files
* Writing files
* Reading files

---

# 17. Key Concepts Learned

### LLM

The **brain** of the agent.

```text
Understands → Decides → Selects Tool
```

### Tools

The **actions** available to the agent.

```text
Calculate
Fetch data
Create files
Read files
Write files
```

### Agent Loop

The process that connects everything:

```text
Decision → Action → Observation → Decision
```

### Sandboxing

The security mechanism that limits what the agent is allowed to access.

---

# 18. Final Mental Model

The simplest way to remember the entire lecture is:

```text
             AI AGENT
                 │
       ┌─────────┼─────────┐
       │         │         │
       ▼         ▼         ▼
      LLM      TOOLS      LOOP
    Decision    Actions   Process
      Maker
```

Or simply:

> **AI Agent = LLM (Brain) + Tools (Hands) + Loop (Process)**

The LLM decides **what to do**, the tools perform **the actual operations**, and the loop allows the agent to continue working until the **goal is achieved**.

---

## 🎯 Main Takeaway

A chatbot primarily **generates responses**.

An AI Agent can **decide, act, observe, and continue**.

The important transition is:

```text
LLM
 ↓
LLM + Tools
 ↓
Tool Calling
 ↓
Decision → Action → Observation
 ↓
AI Agent
```

When building agents that interact with the filesystem or operating system, **tool restrictions and sandboxing are essential** to prevent unintended or unsafe operations.
