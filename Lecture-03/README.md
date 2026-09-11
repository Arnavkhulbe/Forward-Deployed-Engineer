# LLM API & GenAI Application Basics

This note explains the basic architecture of **LLMs, GenAI applications, APIs, tools, Postman, tokens, context, and building an LLM-powered application**.

---

## 1. LLM vs GenAI Application

A common misconception is:

```text
ChatGPT = LLM
```

This is not completely correct.

An **LLM (Large Language Model)** is the model that generates text.

A **GenAI application** is the complete software built around the LLM.

```text
Raw LLM
   +
Backend
   +
Tokenizer
   +
Tools
   +
Guardrails
   +
Other components
   ↓
GenAI Application
```

### Raw LLM

The LLM provides the core language-generation capability.

### GenAI Application

The application provides additional functionality around the LLM.

Therefore:

```text
LLM ≠ Complete AI Application
```

---

# 2. Basic ChatGPT Architecture

When a user sends a message, the request does not simply go directly from the user to the raw LLM.

A simplified architecture is:

```text
User
 ↓
Frontend
 ↓
Backend / Server
 ↓
Tokenizer
 ↓
LLM
 ↓
Response
 ↓
Backend
 ↓
Frontend
 ↓
User
```

The frontend is the interface.

The backend handles communication with the model and other components.

---

# 3. What is an LLM?

An LLM generates text by predicting the next token.

Conceptually:

```text
Input
 ↓
Tokenization
 ↓
Tokens / Token IDs
 ↓
LLM
 ↓
Next Token
 ↓
Next Token
 ↓
Next Token
 ↓
Final Response
```

For example:

```text
Input:
"Hello, my name is"

Model predicts:
"Aditya"
```

Then the model continues generating the response token by token.

---

# 4. What is Tokenization?

LLMs do not directly process normal human sentences.

Text is first converted into **tokens**.

Conceptually:

```text
Human Text
    ↓
Tokenizer
    ↓
Tokens
    ↓
Token IDs
    ↓
LLM
```

Tokens are the units that the model processes.

The exact way text is split into tokens depends on the tokenizer.

---

# 5. What are Tools?

A raw LLM mainly generates text.

However, an AI application can provide **tools** that allow the system to perform additional tasks.

Examples:

```text
Calculator
Web Search
Weather API
Code Execution
Database
External APIs
```

Conceptually:

```text
LLM
 |
 +---- Calculator
 |
 +---- Web Search
 |
 +---- Code Execution
 |
 +---- Other APIs
```

Tools extend the capabilities of an LLM-powered application.

---

# 6. Why are Tools Required?

Suppose the user asks:

```text
What is the weather right now?
```

The raw LLM may not have access to the current weather.

Instead, the application can use:

```text
User
 ↓
LLM
 ↓
Weather Tool
 ↓
Current Weather
 ↓
LLM
 ↓
Final Answer
```

Similarly, for an exact mathematical calculation:

```text
User
 ↓
LLM
 ↓
Calculator
 ↓
Result
 ↓
LLM
 ↓
Final Answer
```

---

# 7. Code Execution as a Tool

Suppose the user asks the system to count characters in a string.

Instead of relying completely on language generation, the LLM can generate code.

```text
User
 ↓
LLM
 ↓
Generate Code
 ↓
Code Execution Tool
 ↓
Execute Code
 ↓
Result
 ↓
LLM
 ↓
Final Response
```

For example:

```python
text = "********"
print(len(text))
```

The code execution tool can calculate the exact result.

---

# 8. How Does the LLM Choose a Tool?

Suppose the application provides:

```text
1. Calculator
2. Weather API
3. Code Execution
```

The LLM can determine which tool is appropriate.

Example:

```text
"What is 25 × 40?"
        ↓
   Calculator
```

```text
"What is the weather?"
        ↓
   Weather API
```

```text
"Count these characters."
        ↓
   Code Execution
```

The overall flow becomes:

```text
User Request
     ↓
    LLM
     ↓
Choose Tool
     ↓
Tool Execution
     ↓
Tool Result
     ↓
    LLM
     ↓
Final Response
```

---

# 9. Human Language vs Tool Input

Tools usually require structured input.

A user might say:

```text
Multiply 25 and 40.
```

But a calculator tool may require something like:

```json
{
  "a": 25,
  "b": 40,
  "operation": "multiply"
}
```

Therefore:

```text
Human Language
      ↓
     LLM
      ↓
Structured Tool Request
      ↓
     Tool
      ↓
Tool Result
      ↓
     LLM
      ↓
Natural Language Response
```

---

# 10. Knowledge Cutoff

An LLM is trained using data available during its training process.

The training data has a cutoff.

Conceptually:

```text
Training Data
     ↓
   Training
     ↓
Knowledge Cutoff
     ↓
Released Model
```

Information that appears after the cutoff is not automatically part of the model's training knowledge.

For current information, an application can use tools such as:

```text
Web Search
Weather API
News API
Database
External APIs
```

---

# 11. What is an API?

API stands for:

> **Application Programming Interface**

An API provides a defined way for one software application to communicate with another service.

For an AI application:

```text
Your Application
      ↓
      API
      ↓
AI Server
      ↓
     LLM
      ↓
   Response
      ↓
Your Application
```

This allows your own application to use an LLM programmatically.

---

# 12. API Key

An API provider usually requires authentication.

An **API key** is a credential used to identify and authorize API requests.

Conceptually:

```text
Your Application
      ↓
   API Key
      ↓
AI Provider
```

### Important

API keys are secrets.

Do **NOT** put them directly into public GitHub repositories.

Bad:

```python
api_key = "my-secret-api-key"
```

Instead, use mechanisms such as:

```text
.env
Environment Variables
Secret Managers
```

---

# 13. What is Postman?

**Postman** is a tool used to test APIs.

Instead of writing application code immediately, you can manually send an API request using Postman.

Conceptually:

```text
Postman
   ↓
HTTP Request
   ↓
AI API
   ↓
LLM
   ↓
HTTP Response
   ↓
Postman
```

Postman is useful for understanding and testing an API before integrating it into your application.

---

# 14. What Does an API Request Contain?

An API request can contain several important components:

```text
URL
HTTP Method
Headers
Authentication
Request Body
```

For example:

```text
POST /some-endpoint
```

The request body might contain:

```json
{
  "model": "some-model",
  "input": "Explain Docker in two lines"
}
```

The exact format depends on the API.

---

# 15. HTTP POST Request

A **POST** request is commonly used when sending data to a server.

Conceptually:

```text
POST /endpoint

Here is my request data.
```

The server processes the request and sends back a response.

---

# 16. Request Body

The **request body** contains the information being sent to the server.

For an LLM API, it may contain:

```text
Model
Input / Messages
Parameters
Other configuration
```

Example:

```json
{
  "model": "some-model",
  "input": "Explain Docker"
}
```

---

# 17. API Response

The server sends a response back.

The response may contain:

```text
Response ID
Status
Model Information
Usage Information
Generated Output
Other Metadata
```

Usually, your application is mainly interested in:

```text
Generated Output
```

So the application extracts the useful text from the larger response.

---

# 18. HTTP Status Codes

When working with APIs, you will see HTTP status codes.

### 200 OK

```text
200
```

Generally means the request was successfully handled.

### 404 Not Found

```text
404
```

Generally means the requested endpoint/resource was not found.

For example:

```text
Wrong Endpoint
     ↓
    404
```

Correct endpoint:

```text
Correct Endpoint
     ↓
    200
```

---

# 19. Input Tokens and Output Tokens

LLM APIs work with tokens.

Suppose:

```text
Input = 13 tokens
Output = 39 tokens
```

Then:

```text
Total = 13 + 39
      = 52 tokens
```

So:

```text
Input Tokens
+
Output Tokens
=
Total Token Usage
```

---

# 20. Why Do Tokens Matter?

Tokens are important because API usage can be based on the number of tokens processed.

Generally:

```text
More Input Tokens
       +
More Output Tokens
       ↓
More Usage
```

Therefore, when building LLM applications, prompt size and response size can matter.

---

# 21. Context

Another important concept is **context**.

Suppose you send:

```text
My name is Aditya.
```

Then make a completely separate request:

```text
What is my name?
```

The model may not know the answer if the previous message was not included in the new request.

Why?

Because the second request does not automatically contain the first request.

Conceptually:

```text
Request 1:

"My name is Aditya."
```

Then:

```text
Request 2:

"What is my name?"
```

If the application sends only Request 2, the model does not have the previous information.

---

# 22. How Conversation Context Works

An application can send previous messages along with the current message.

For example:

```text
Previous Context:
"My name is Aditya."

Current User Message:
"What is my name?"
```

The model now has the required context.

Conceptually:

```text
Conversation History
        +
Current Message
        ↓
       LLM
        ↓
     Response
```

Therefore, what looks like "memory" in a conversational application can involve providing previous conversation context to the model.

---

# 23. Building Your Own LLM Application

Instead of manually using ChatGPT, you can build your own application.

You can use languages such as:

```text
Python
JavaScript
Java
```

The basic architecture remains the same:

```text
Your Application
      ↓
    AI API
      ↓
      LLM
      ↓
   Response
      ↓
Your Application
```

---

# 24. Example Application: Customer Ticket Summarizer

Suppose a company receives long customer support tickets.

Example:

```text
Customer:

I am extremely disappointed with my recent order.
I ordered chicken biryani, butter chicken,
garlic naan and Coke.

I waited for 50 minutes.

However, I received completely different items.

I also tried contacting the restaurant.

I want an immediate refund.
```

The executive does not want to read the entire message.

The application can summarize it:

```text
Customer received incorrect food after a 50-minute wait
and is requesting an immediate refund.
```

The basic flow:

```text
Long Customer Ticket
        ↓
       LLM
        ↓
Short Summary
```

---

# 25. Controller and Service

In a backend application, the code can be separated into different components.

Two important components in this example are:

```text
Controller
Service
```

### Controller

The controller handles incoming API requests.

```text
Client
  ↓
Controller
```

### Service

The service contains the application's business logic.

```text
Controller
    ↓
Service
    ↓
LLM
```

Complete flow:

```text
Client
  ↓
Controller
  ↓
Service
  ↓
AI API
  ↓
LLM
  ↓
Service
  ↓
Controller
  ↓
Client
```

---

# 26. What is an Endpoint?

An endpoint is a specific URL through which an application can communicate.

For example:

```text
/api/summarize
```

A request could be:

```text
POST /api/summarize
```

This tells the backend:

> Process a POST request using the `/api/summarize` endpoint.

---

# 27. What Does the Summarize Service Do?

The service receives a customer ticket.

Then it creates an instruction:

```text
Summarize this support ticket in two lines.

<Customer Ticket>
```

Then:

```text
Ticket
  ↓
Prompt
  ↓
ChatClient
  ↓
AI API
  ↓
LLM
  ↓
Response
  ↓
Extract Text
  ↓
Return Summary
```

---

# 28. What is ChatClient?

In the Spring AI example, the application uses:

```text
ChatClient
```

ChatClient provides a higher-level way to communicate with an LLM.

Instead of manually handling every API detail, the library provides an abstraction for interacting with the model.

Conceptually:

```text
Java Application
      ↓
  ChatClient
      ↓
    AI API
      ↓
      LLM
```

---

# 29. What Does `.builder()` Mean?

The example uses a builder to create/configure the ChatClient.

Conceptually:

```text
ChatClient Builder
       ↓
Configure
       ↓
build()
       ↓
ChatClient Object
```

This is related to the **Builder Design Pattern**.

For this lesson, the important point is simply:

> `builder()` helps construct the ChatClient object.

---

# 30. What Does `.user()` Mean?

The `.user()` part represents the user message/instruction being sent to the model.

Conceptually:

```text
.user(
    "Summarize this support ticket in two lines..."
)
```

The prompt can contain both:

```text
Instruction
+
User's Ticket
```

---

# 31. What Does `.call()` Mean?

`.call()` is used to actually make the model request.

Conceptually:

```text
Create Request
      ↓
.call()
      ↓
Send Request
      ↓
LLM
      ↓
Response
```

After the response arrives, the application can extract the generated content.

---

# 32. Dependency Injection

In Spring Boot, dependencies can be provided automatically using **Dependency Injection**.

For example:

```text
Controller
    ↓
Needs SummarizeService
    ↓
Spring
    ↓
Provides SummarizeService
```

Instead of manually creating every required object, Spring can manage and provide these objects.

---

# 33. Configuration

The application needs configuration such as:

```text
Which model should be used?
What API key should be used?
```

In Spring Boot, configuration can be placed in:

```text
application.properties
```

Conceptually:

```text
application.properties
        ↓
Model Configuration
API Key Configuration
        ↓
Spring AI
        ↓
AI API
```

Other programming languages/frameworks have their own configuration mechanisms.

---

# 34. Complete LLM Application Architecture

Putting everything together:

```text
                         USER
                           │
                           ▼
                  YOUR APPLICATION
                           │
                           ▼
                       BACKEND
                           │
                           ▼
                      CONTROLLER
                           │
                           ▼
                        SERVICE
                           │
                           ▼
                      CHAT CLIENT
                           │
                           ▼
                        AI API
                           │
                           ▼
                          LLM
                           │
             ┌─────────────┼─────────────┐
             │             │             │
             ▼             ▼             ▼
        Calculator    Web Search     Code Execution
             │             │             │
             └─────────────┼─────────────┘
                           │
                           ▼
                      TOOL RESULT
                           │
                           ▼
                          LLM
                           │
                           ▼
                    FINAL RESPONSE
                           │
                           ▼
                       BACKEND
                           │
                           ▼
                         USER
```

---

# 35. Important Problem: User Prompt vs Application Instructions

Suppose we build:

```text
Food Delivery Support AI
```

Our application wants the AI to handle:

```text
My order hasn't arrived.
Where is my order?
I want a refund.
I want to cancel my order.
```

But the user can send:

```text
What is 2 + 2?
```

or:

```text
Explain Docker.
```

If the application simply sends everything to the LLM, the model may answer these unrelated questions.

For example:

```text
User:
What is 2 + 2?

AI:
4
```

But this is not what a food-delivery support AI should be designed to do.

---

# 36. Why This Problem Matters

An AI application should have clear rules about:

```text
What the AI should answer
What the AI should not answer
What tasks are allowed
What tasks are outside its scope
```

For a food-delivery application:

```text
Allowed:

Order tracking
Order cancellation
Refund questions
Delivery problems
Food-related support
```

Unrelated:

```text
Explain Docker
Write Python code
Solve unrelated questions
```

This leads to important concepts such as:

```text
System Prompt
User Prompt
Guardrails
Context
Prompt Structure
```

These concepts are explored further in later lessons.

---

# 37. Postman vs Your Application

There are two ways to communicate with an AI API.

### Using Postman

```text
Postman
   ↓
API
   ↓
LLM
   ↓
Response
```

### Using Your Application

```text
Your Application
   ↓
API
   ↓
LLM
   ↓
Response
```

The main difference is that Postman is a tool for manually testing the API, while your application performs these requests programmatically.

---

# 38. Complete Mental Model

The most important architecture to remember is:

```text
User
 ↓
Your Application
 ↓
Backend
 ↓
AI API
 ↓
LLM
 ↓
Tools (if required)
 ↓
LLM
 ↓
Response
 ↓
Backend
 ↓
Your Application
 ↓
User
```

---

# 39. Important Terms

| Term                  | Meaning                                                               |
| --------------------- | --------------------------------------------------------------------- |
| **LLM**               | Large Language Model that generates text                              |
| **Raw LLM**           | The model itself without the surrounding application                  |
| **GenAI Application** | Complete software built around an LLM                                 |
| **Frontend**          | User-facing interface                                                 |
| **Backend**           | Server-side application logic                                         |
| **Tokenizer**         | Converts text into tokens                                             |
| **Token**             | Unit processed by an LLM                                              |
| **Token ID**          | Numerical representation of a token                                   |
| **Tool**              | External capability used by the AI system                             |
| **API**               | Interface for software-to-software communication                      |
| **API Key**           | Credential used to authenticate API requests                          |
| **Endpoint**          | Specific URL used to access a service                                 |
| **POST**              | HTTP method commonly used to send data                                |
| **Request Body**      | Data sent to the server                                               |
| **Response**          | Data returned by the server                                           |
| **Input Tokens**      | Tokens sent to the model                                              |
| **Output Tokens**     | Tokens generated by the model                                         |
| **Knowledge Cutoff**  | Point after which training knowledge is not automatically included    |
| **Context**           | Information provided to the model about the current task/conversation |
| **Controller**        | Handles incoming backend requests                                     |
| **Service**           | Contains business logic                                               |
| **Dependency**        | External library/component used by an application                     |
| **ChatClient**        | Higher-level interface for communicating with an LLM                  |
| **Guardrails**        | Rules that restrict/control AI behavior                               |
| **Postman**           | Tool for testing APIs                                                 |

---

# 40. Key Takeaways

### 1. ChatGPT is more than an LLM

```text
LLM ≠ Complete AI Application
```

A GenAI application contains an LLM plus surrounding software and capabilities.

### 2. LLMs generate text through token prediction

```text
Text
 ↓
Tokens
 ↓
LLM
 ↓
Generated Tokens
```

### 3. Tools extend AI capabilities

```text
LLM + Calculator
LLM + Web Search
LLM + Code Execution
```

### 4. APIs allow your application to communicate with an LLM

```text
Your App
 ↓
API
 ↓
LLM
```

### 5. API keys must remain secret

Never expose API keys in public repositories.

### 6. Postman is useful for API testing

```text
Postman → API → LLM → Response
```

### 7. Context must be provided

A separate API request does not automatically contain previous conversation context.

### 8. Backend applications can separate responsibilities

```text
Controller
    ↓
Service
    ↓
LLM
```

### 9. AI applications need boundaries

A production AI application should define what the model is supposed to handle.

This is where concepts such as:

```text
System Prompts
User Prompts
Guardrails
Context
```

become important.

---

## Final Architecture

```text
                    USER
                      │
                      ▼
              YOUR APPLICATION
                      │
                      ▼
                   BACKEND
                      │
                      ▼
                 AI API
                      │
                      ▼
                    LLM
                      │
             ┌────────┴────────┐
             ▼                 ▼
          Tools             No Tool
             │                 │
             ▼                 │
        Tool Result             │
             │                 │
             └────────┬────────┘
                      ▼
                     LLM
                      │
                      ▼
                Final Response
                      │
                      ▼
                    USER
```

> **Core idea:** An LLM provides the language-generation capability, while the application around it provides APIs, context, tools, business logic, and rules that turn the model into a useful GenAI application.
