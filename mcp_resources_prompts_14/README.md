# MCP Resources and Prompts

This lecture explains **MCP Resources and Prompts**, how they work, and how an MCP client communicates with an MCP server to access them.

# 1. MCP Architecture

An AI application generally contains three main components:

- **AI Application**
- **LLM (Large Language Model)**
- **MCP Client**

The MCP server exposes three main capabilities:

- Tools
- Resources
- Prompts

```text
AI Application
├── Application Logic
├── LLM
└── MCP Client
       |
       v
    MCP Server
    ├── Tools
    ├── Resources
    └── Prompts
```

# 2. MCP Resources

A **Resource** provides information or context to an AI application.

Examples:

- Company policies
- Coding guidelines
- Documentation
- User information
- JSON data
- HTML documents
- Images

### Difference Between Tools, Resources and Prompts

| Component | Purpose |
|---|---|
| Tools | Perform actions |
| Resources | Provide information |
| Prompts | Provide instructions |

## 2.1 Resource Operations

There are two important operations used to access resources.

### `resources/list`

The MCP client asks the MCP server:

> What resources are available?

The server returns information about available resources, including:

- Resource name
- Description
- URI
- MIME type

### `resources/read`

The MCP client requests a particular resource using its URI.

The MCP server returns the content of that resource.

### Resource Flow

```text
resources/list
       |
       v
Get available resources
       |
       v
Select a resource
       |
       v
resources/read
       |
       v
Get resource content
```

## 2.2 Resource Anatomy

A resource contains five main things:

1. **Name** – Name of the resource.
2. **Description** – Explains what the resource contains.
3. **URI** – Identifies the resource.
4. **MIME Type** – Defines the type of content.
5. **Content** – Actual information stored in the resource.

### Example

```text
Name: Coding Guidelines

Description: Guidelines for writing code.

URI: company/coding-guidelines

MIME Type: text/plain

Content: Rules and guidelines for writing code.
```

## 2.3 MIME Types

MIME type tells us what kind of content a resource contains.

| MIME Type | Meaning |
|---|---|
| `text/plain` | Plain text document |
| `application/json` | JSON document |
| `text/html` | HTML document |
| `image/png` | PNG image |

## 2.4 URI vs URL

**URI (Uniform Resource Identifier)** identifies a particular resource.

Example:

```text
company/coding-guidelines
```

A URI does not necessarily have to be a URL.

- A URL is an address that can be used to locate a resource.
- A URI is an identifier understood by the MCP server.
- The MCP server uses the URI to identify and locate the requested resource.

**Remember:** Every URL is a URI, but not every URI is a URL.

## 2.5 Static vs Dynamic Resources

### Static Resource

A static resource contains predefined content.

```text
resources/read
       |
       v
Static Document
       |
       v
Return Content
```

### Dynamic Resource

A dynamic resource can fetch information from a database.

Example URI:

```text
user/123/profile
```

Here:

- `user` represents the resource category.
- `123` represents the user ID.
- `profile` represents the requested information.

The MCP server can use this ID to fetch the user's profile from a database such as MySQL.

```text
resources/read
       |
       v
user/123/profile
       |
       v
MCP Server
       |
       v
MySQL Database
       |
       v
Fetch User Information
       |
       v
Return Resource Content
```

# 3. Resources and RAG

Resources can also be integrated with a RAG system.

The MCP server can contain or connect to a system that performs:

1. Document loading
2. Chunking
3. Embedding generation
4. Vector database storage
5. Relevant information retrieval

### RAG Architecture

```text
Documents
    |
    v
Chunking
    |
    v
Embeddings
    |
    v
Vector Database
    |
    v
MCP Server
    |
    v
AI Application / LLM
```

The advantage is that multiple AI applications can access the same centralized resources instead of storing the same documents separately.

# 4. MCP Prompts

A **Prompt** is a predefined instruction stored on the MCP server.

Instead of asking users to write long and complicated prompts, an application can provide predefined prompt options through its UI.

### Example

```text
Available Prompts:

1. Plan My Day
2. Review My Workload
3. Replan My Day
```

The user selects one option, and the application fetches the actual predefined prompt from the MCP server.

## 4.1 Prompt Operations

There are two important operations.

### `prompts/list`

- Fetches the list of available prompts.
- Returns prompt names and descriptions.
- The application can display these options in its UI.

### `prompts/get`

- Fetches a particular prompt.
- Returns the actual predefined instructions.
- The application provides those instructions to the LLM.

### Prompt Flow

```text
prompts/list
      |
      v
Get Available Prompts
      |
      v
Display Prompts in UI
      |
      v
User Selects a Prompt
      |
      v
prompts/get
      |
      v
Fetch Actual Prompt
      |
      v
Send Prompt to LLM
      |
      v
Generate Response
```

## 4.2 Prompt Arguments

Prompts can also accept arguments from users.

Example:

```text
Prompt: Plan My Day

Argument:
available_hours = 4
```

The application can pass the available hours as an argument to the prompt.

The predefined prompt can use this value when generating instructions for the LLM.

Multiple arguments can also be passed if required.

# 5. Why Store Tools, Resources and Prompts on an MCP Server?

We can technically store tools, resources, and prompts directly inside our AI application.

However, MCP provides a **centralized location**.

Suppose we have multiple AI applications:

```text
                 MCP Server
                     |
          +----------+----------+
          |          |          |
          v          v          v
       App 1       App 2       App 3
```

All applications can access the same:

- Tools
- Resources
- Prompts

This avoids duplicating the same capabilities across multiple AI applications.

The main goal is to make the system more reusable and centralized.

# 6. Control Model

The lecture describes three different control patterns:

| Component | Purpose | Controlled By |
|---|---|---|
| Tools | Perform actions | LLM-controlled |
| Resources | Provide information | AI application-controlled |
| Prompts | Provide predefined instructions | User-controlled |

### 1. Tools – LLM Controlled

The LLM decides which tool should be called based on the user's request.

Operations:

```text
tools/list
tools/call
```

### 2. Resources – Application Controlled

The AI application generally decides which resource should be fetched and provided as context to the LLM.

Operations:

```text
resources/list
resources/read
```

### 3. Prompts – User Controlled

The user selects a predefined prompt from the application's UI.

Operations:

```text
prompts/list
prompts/get
```

# 7. Complete MCP Structure

```text
                     MCP SERVER
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
        TOOLS         RESOURCES       PROMPTS
          |              |              |
          v              v              v
       Actions       Information    Instructions
          |              |              |
          v              v              v
     tools/list     resources/list  prompts/list
     tools/call     resources/read  prompts/get
```

# 8. Key Takeaways

Remember these three concepts:

- **Tool = Action**
- **Resource = Information**
- **Prompt = Instruction**

MCP provides a standardized way for AI applications to discover and use tools, access information, and retrieve predefined prompts from an MCP server.
