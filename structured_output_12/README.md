# Structured Output

## 📌 Introduction

Large Language Models (LLMs) are very good at understanding and generating **natural language**.

For example:

```text
Schedule a meeting at 4 PM today with Ravi.
```

An LLM can easily understand this.

However, software applications usually don't want a natural-language paragraph. They need information in a **structured and predictable format**, such as JSON.

For example:

```json
{
  "attendee": "Ravi",
  "date": "2026-10-04",
  "time": "16:00"
}
```

This concept is called **Structured Output**.

---

# 1. What is Structured Output?

**Structured output** means asking an LLM to return information in a predefined format that another program can easily understand and use.

Instead of:

```text
Sure! I'll schedule your meeting with Ravi at 4 PM today.
```

we want:

```json
{
  "attendee": "Ravi",
  "date": "2026-10-04",
  "time": "16:00"
}
```

The second format is much easier for software to process.

### Simple Definition

> **Structured Output = Converting an LLM's natural-language understanding into a predefined, machine-readable structure.**

---

# 2. Unstructured vs Structured Output

## Unstructured Output

```text
Sure! I'll schedule a meeting with Ravi at 4 PM today.
```

This is just a string.

The application has to figure out:

```text
Who is the attendee?
What is the date?
What is the time?
```

This can become difficult and unreliable.

---

## Structured Output

```json
{
  "attendee": "Ravi",
  "date": "2026-10-04",
  "time": "16:00"
}
```

Now the application already knows:

```text
attendee → Ravi
date     → 2026-10-04
time     → 16:00
```

---

# 3. Why Do We Need Structured Output?

Humans understand natural language very easily.

Applications generally work better with predefined data structures.

Consider:

```text
User
  ↓
Natural Language
  ↓
LLM
  ↓
Structured Output
  ↓
Application
```

The LLM acts as a bridge between **human language** and **machine-readable data**.

---

# 4. Example: Meeting Scheduler

Imagine we are building a meeting scheduling application.

Normally, the application could have a form:

```text
Meeting Title: __________

Attendees: __________

Date: __________

Time: __________

Duration: __________
```

The frontend could send JSON to the backend:

```json
{
  "title": "Meeting",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

The backend can then use this information.

---

# 5. Adding an LLM

Instead of forcing the user to fill out a form, we can allow natural language:

```text
Schedule my meeting today at 4 PM with Ravi.
```

The LLM understands this sentence and extracts the relevant information.

It can produce:

```json
{
  "title": "Meeting with Ravi",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

Now the backend can work with this structured information.

---

# 6. The LLM's Responsibility

In this architecture, the LLM is **not necessarily responsible for scheduling the meeting**.

Its job is:

```text
Natural Language
       ↓
      LLM
       ↓
Structured Data
```

The backend is responsible for the actual business logic:

```text
Structured Data
       ↓
    Backend
       ↓
 ┌─────┼─────────┐
 ↓     ↓         ↓
DB  Calendar  Notification
```

For example, the backend could:

- Store the meeting
- Create a calendar event
- Send notifications
- Trigger other backend operations

The LLM only extracts and structures the user's request.

---

# 7. What is a Schema?

Suppose we tell an LLM:

> Convert this sentence into JSON.

The LLM might return:

```json
{
  "meetingTime": "4 PM",
  "person": "Ravi"
}
```

Another time it might return:

```json
{
  "timeOfMeeting": "16:00",
  "visitor": "Ravi"
}
```

Another time:

```json
{
  "time": "16:00",
  "attendees": ["Ravi"],
  "meetingDate": "2026-10-04"
}
```

All of these are valid JSON.

But our application cannot reliably depend on them because the field names keep changing.

We therefore need a **schema**.

---

# 8. Schema

A schema defines the exact structure that we expect.

For example:

```text
MeetingDetails

- title
- attendees
- date
- time
- duration
```

The expected JSON becomes:

```json
{
  "title": "Meeting with Ravi",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

The schema tells the LLM:

> These are the fields I expect and this is the structure you should follow.

---

# 9. Schema as a Contract

A useful way to think about a schema is as a **contract** between the LLM and the application.

Without a schema:

```text
LLM
 ↓
Random JSON structure
 ↓
Application
```

The application doesn't know exactly what it will receive.

With a schema:

```text
LLM
 ↓
Predefined Schema
 ↓
Expected Structure
 ↓
Application
```

The application knows what each field means.

---

# 10. Data Types in a Schema

A schema can also define the expected data type.

For example:

```text
title      → string
attendees  → list of strings
date       → string
time       → string
duration   → integer
```

Example:

```json
{
  "title": "Meeting with Ravi",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

Here:

```text
"Meeting with Ravi" → string

["Ravi"] → list

"2026-10-04" → string

"16:00" → string

30 → integer
```

This gives the application a predictable structure.

---

# 11. Why Not Just Use a System Prompt?

We might think:

> Why do we need a schema? Can't we simply tell the LLM the required JSON format in the system prompt?

For example:

```text
Always return:

{
  "title": "...",
  "attendees": "...",
  "date": "...",
  "time": "...",
  "duration": "..."
}
```

This can work in simple situations.

However, the lecture points out that an LLM is a **probabilistic model**.

It may sometimes fail to follow the instructions exactly.

For a production application, relying only on a prompt for strict formatting can therefore be risky.

A predefined schema gives the application a much clearer expected structure.

---

# 12. Mapping LLM Output to a Schema

The basic idea is:

```text
User Message
     ↓
     LLM
     ↓
Schema
     ↓
Structured Object
```

Suppose our schema is:

```text
MeetingDetails

title
attendees
date
time
duration
```

The user says:

```text
Schedule a meeting today at 4 PM with Ravi.
```

The LLM extracts:

```json
{
  "title": "Meeting with Ravi",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

The framework can then map this response into the predefined `MeetingDetails` structure.

---

# 13. Missing Information

What happens when the user doesn't provide enough information?

For example:

```text
Schedule a meeting with Ravi.
```

We know:

```text
attendee = Ravi
```

But we don't know:

```text
date = ?
time = ?
```

The LLM should **not invent** this information.

For example, it should not randomly decide:

```json
{
  "attendee": "Ravi",
  "date": "tomorrow",
  "time": "10:00"
}
```

Instead, missing information can be handled in different ways.

---

# 14. Option 1: Leave Missing Fields Blank

The system can return:

```json
{
  "attendee": "Ravi",
  "date": null,
  "time": null
}
```

The backend can then recognize that information is missing.

---

# 15. Option 2: Ask the User

The application can continue the conversation.

```text
User:
Schedule a meeting with Ravi.

LLM:
What date should I schedule it for?

User:
Tomorrow.

LLM:
What time?

User:
4 PM.
```

Eventually:

```json
{
  "attendee": "Ravi",
  "date": "2026-10-05",
  "time": "16:00"
}
```

This requires the application to maintain **conversation history**.

---

# 16. Conversation History

To handle multiple messages, the system needs to remember previous messages.

For example:

```text
User → Schedule a meeting with Ravi.

LLM → What date?

User → Tomorrow.

LLM → What time?

User → 4 PM.
```

The LLM needs access to this history to understand what the user means.

Conceptually:

```text
Message History
      ↓
     LLM
      ↓
Understand current message
      ↓
Structured Output
```

---

# 17. Structured Output vs Tool Calling

These concepts are related but different.

## Structured Output

The LLM extracts information and returns it in a predefined structure.

```text
User
 ↓
LLM
 ↓
Structured JSON
```

Example:

```json
{
  "city": "Delhi",
  "date": "2026-10-04"
}
```

---

## Tool Calling

The LLM decides to use an external tool.

```text
User
 ↓
LLM
 ↓
Tool
 ↓
External System
 ↓
Result
```

For example:

```text
User:
What's the weather in Delhi?

LLM
 ↓
Weather Tool
 ↓
Weather API
 ↓
Weather Result
```

### Key Difference

**Structured Output:**

> "Give me the information in this structure."

**Tool Calling:**

> "Use this external function/tool to perform an action."

---

# 18. Complete Architecture

The complete idea from the lecture can be represented as:

```text
                  USER
                    │
                    │
                    ▼
        "Schedule meeting at
         4 PM with Ravi"
                    │
                    ▼
                   LLM
                    │
                    │
             Extract Information
                    │
                    ▼
           STRUCTURED OUTPUT
                    │
                    ▼
        ┌─────────────────────┐
        │ MeetingDetails      │
        │                     │
        │ title               │
        │ attendees           │
        │ date                │
        │ time                │
        │ duration            │
        └─────────────────────┘
                    │
                    ▼
                 SERVER
                    │
          ┌─────────┼─────────┐
          ▼         ▼         ▼
      Database   Calendar   Notification
```

---

# 19. What the Java Code Is Doing

The Java code in the lecture may look complicated, but the underlying idea is simple.

### Step 1 — Define the expected structure

Create something like:

```text
MeetingDetails
```

with:

```text
title
attendees
date
time
duration
```

---

### Step 2 — Give instructions to the LLM

For example:

```text
Extract meeting information.

Convert relative dates such as
today and tomorrow.

Convert time to 24-hour format.

Don't invent attendee, date, or time.

If duration is missing,
use 30 minutes.
```

---

### Step 3 — Give the user message

```text
Schedule my meeting today at 4 PM with Ravi.
```

---

### Step 4 — Map the response

Tell the framework to map the LLM response into:

```text
MeetingDetails
```

---

### Step 5 — Receive structured data

```json
{
  "title": "Meeting with Ravi",
  "attendees": ["Ravi"],
  "date": "2026-10-04",
  "time": "16:00",
  "duration": 30
}
```

---

# 20. Important: The Meeting Is Not Actually Scheduled

One important point from the lecture:

At this stage, the system is **not actually scheduling the meeting**.

It is only converting:

```text
Natural Language
```

into:

```text
Structured Data
```

So:

```text
User
 ↓
LLM
 ↓
Structured Output
 ↓
Return JSON
```

The actual calendar integration comes later.

---

# 21. Why This Is Important for MCP

Structured output is an important foundation for understanding later concepts such as **MCP**.

A simplified architecture can look like:

```text
User
 ↓
LLM
 ↓
Understand Request
 ↓
Structured Information
 ↓
Tool / MCP
 ↓
External System
```

For example:

```text
"Schedule a meeting with Ravi tomorrow at 4 PM"
                    ↓
                   LLM
                    ↓
{
  "attendee": "Ravi",
  "date": "2026-10-05",
  "time": "16:00"
}
                    ↓
             Calendar Tool
                    ↓
             Calendar Event
```

---

# 🧠 Key Takeaways

Remember these points:

### 1. LLMs naturally work with text

```text
LLM → Natural Language
```

### 2. Applications prefer structured data

```text
Application → JSON / Objects / Defined Structures
```

### 3. Structured output connects them

```text
Natural Language
       ↓
      LLM
       ↓
Structured JSON
```

### 4. Schema defines the expected structure

```text
MeetingDetails
 ├── title
 ├── attendees
 ├── date
 ├── time
 └── duration
```

### 5. Schema acts like a contract

It tells the LLM and application exactly what structure is expected.

### 6. Structured output doesn't perform the action

It only produces the structured information.

The backend, tool, or external system performs the actual operation.

---

# ⭐ One-Line Definition

> **Structured Output is the process of getting an LLM to return information in a predefined structure, such as a JSON schema, so that software can reliably understand and use that information.**

---

# 🔄 Final Mental Model

```text
                HUMAN
                  │
                  │
          Natural Language
                  │
                  ▼
                 LLM
                  │
          Understand + Extract
                  │
                  ▼
             SCHEMA
                  │
                  ▼
        STRUCTURED OUTPUT
                  │
                  ▼
             APPLICATION
                  │
                  ▼
          Business Logic
                  │
          ┌───────┼────────┐
          ▼       ▼        ▼
       Database Tool   Notification
```

**Natural language → LLM → Schema → Structured Output → Application**

That is the core concept of this entire lecture.
