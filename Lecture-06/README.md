# 🚀 Streaming Responses in AI Applications

Streaming is an important technique used in AI chat applications to make responses feel faster and more interactive.

Instead of waiting for the complete LLM response, the application receives and displays the generated content **chunk by chunk** in real time.

---

## 📌 1. Generation vs Delivery

A common misconception is that streaming makes the AI generate responses faster.

It doesn't.

### Generation

An LLM generates text **token by token** internally.

For example:

```text
Prompt: Capital of India is

New → Delhi → .
```

### Traditional Delivery

Without streaming:

```text
LLM
 ↓
Generates complete response
 ↓
Backend receives complete response
 ↓
Frontend receives complete response
 ↓
User sees response
```

The user may have to wait several seconds before seeing anything.

### Streaming Delivery

With streaming:

```text
LLM
 ↓
Generates token/chunk
 ↓
Backend receives chunk
 ↓
Frontend displays chunk
 ↓
Next chunk...
```

The user starts seeing the response immediately.

> **Important:** Streaming does not reduce the total generation time. It reduces the **perceived latency** by reducing the **Time to First Token (TTFT)**.

---

# 🏗️ 2. Streaming Architecture

A complete streaming architecture requires **two streaming layers**.

```text
              ┌──────────────┐
              │  AI Provider │
              └──────┬───────┘
                     │
                Streaming
                     │
                     ▼
              ┌──────────────┐
              │   Backend    │
              └──────┬───────┘
                     │
                Streaming
                     │
                     ▼
              ┌──────────────┐
              │  Frontend    │
              └──────────────┘
```

### Layer 1 — AI Provider → Backend

The AI provider sends generated content to the backend in chunks.

### Layer 2 — Backend → Frontend

The backend immediately forwards those chunks to the frontend.

### ⚠️ Important

If the backend receives the stream but waits for the **entire response** before sending it to the frontend, the user will still experience the same waiting time.

---

# 🌐 3. SSE vs WebSockets

There are two common technologies for streaming data.

## Server-Sent Events (SSE)

SSE uses a long-lived HTTP connection where the server continuously sends events to the client.

### Advantages

* Lightweight
* Simple to implement
* Suitable for server → client streaming
* Works well for AI chat applications

```text
Client ───── Request ─────> Server

Client <──── Chunk 1 ───── Server
Client <──── Chunk 2 ───── Server
Client <──── Chunk 3 ───── Server
Client <──── Chunk 4 ───── Server
```

---

## WebSockets

WebSockets provide a **full-duplex connection**.

Both client and server can send data independently at any time.

```text
Client  ←────────────→  Server
        Full Duplex
```

For a typical AI chat request-response workflow, WebSockets can be unnecessary because the primary requirement is server → client streaming.

> **For basic AI chat streaming, SSE / Streaming HTTP is generally simpler and lightweight.**

---

# ☕ 4. Backend Implementation — Spring Boot

The example uses **Spring Boot + Spring AI**.

## Non-Streaming Approach

The traditional implementation returns a complete `String`.

```java
public String chat(Message message) {

    String response = chatClient.prompt()
        .systemMessage("You are a funny AI chatbot...")
        .userMessage(message.getMessage())
        .call()
        .content();

    return response;
}
```

Here:

```text
LLM → Complete String → Backend → Frontend
```

The frontend receives the response only after the complete generation finishes.

---

# 🌊 5. Streaming with `Flux<String>`

For streaming, the return type changes from:

```java
String
```

to:

```java
Flux<String>
```

And instead of:

```java
.call()
```

we use:

```java
.stream()
```

### Streaming Implementation

```java
public Flux<String> chat(Message message) {

    Flux<String> responseStream = chatClient.prompt()
        .systemMessage(
            "You are a funny AI chatbot. Reply everything sarcastically."
        )
        .userMessage(message.getMessage())
        .stream()
        .content();

    return responseStream;
}
```

The important change is:

```java
.stream()
```

which returns a reactive stream of generated content.

---

# 🧠 6. Handling Conversation History

Streaming creates an additional problem.

Previously, we had:

```java
String response
```

Now we have:

```java
Flux<String> response
```

A conversation history normally expects a complete string, not a stream.

Therefore, we need to **accumulate the incoming chunks**.

### Concept

```text
Chunk 1 → "Hello"
Chunk 2 → " how"
Chunk 3 → " are"
Chunk 4 → " you?"

        ↓

Full Response

"Hello how are you?"
```

A buffer such as `StringBuilder` can be used to accumulate the chunks.

### Conceptual Logic

```java
StringBuilder fullResponse = new StringBuilder();

stream
    .doOnNext(chunk -> {
        fullResponse.append(chunk);
    })
    .doOnComplete(() -> {

        history.add(
            new AssistantMessage(fullResponse.toString())
        );

    });
```

The important idea is:

```text
Stream chunks
     ↓
Append chunks to buffer
     ↓
Stream completes
     ↓
Convert buffer to String
     ↓
Save complete response to history
```

---

# 🎮 7. Backend Controller

The controller also needs to return the stream.

```java
@RestController
@RequestMapping("/api")
public class ChatController {

    @Autowired
    private ChatService chatService;

    @PostMapping("/chat")
    public Flux<String> chat(
        @RequestBody Message message
    ) {
        return chatService.chat(message);
    }
}
```

Instead of returning:

```java
String
```

the controller returns:

```java
Flux<String>
```

This allows Spring's reactive system to stream the response to the client.

---

# 💻 8. Frontend Implementation — JavaScript

The frontend must also change.

## ❌ Non-Streaming

A normal `fetch()` implementation may use:

```javascript
const text = await response.text();

displayMessage(text);
```

The problem is that:

```javascript
response.text()
```

waits for the complete response.

```text
Backend
   ↓
Complete response
   ↓
response.text()
   ↓
Display
```

---

# ✅ 9. Streaming with ReadableStream

For streaming, JavaScript provides the **ReadableStream API**.

The important APIs are:

* `response.body`
* `getReader()`
* `reader.read()`
* `TextDecoder`

### Basic Implementation

```javascript
async function sendMessage() {

    const response = await fetch('/api/chat', {
        method: 'POST',
        body: JSON.stringify({
            message: userInput
        })
    });

    if (!response.ok) {
        throw new Error('Request failed');
    }

    const reader = response.body.getReader();
    const decoder = new TextDecoder();

    let aiResponseBuffer = "";

    try {

        while (true) {

            const { value, done } = await reader.read();

            if (done) {
                break;
            }

            const chunk = decoder.decode(
                value,
                { stream: true }
            );

            aiResponseBuffer += chunk;

            updateAssistantMessage(
                aiResponseBuffer
            );

            scrollToBottom();
        }

    } finally {

        reader.releaseLock();

    }
}
```

---

# 🔍 10. Understanding `reader.read()`

The following line is important:

```javascript
const { value, done } = await reader.read();
```

It returns two important values:

### `value`

Contains the current chunk of data.

```javascript
value
```

### `done`

Indicates whether the stream has finished.

```javascript
done === true
```

means:

```text
Stream finished
```

---

# 🔤 11. Understanding `TextDecoder`

Network data arrives as binary data.

Therefore, we use:

```javascript
const decoder = new TextDecoder();
```

Then:

```javascript
const chunk = decoder.decode(
    value,
    { stream: true }
);
```

This converts the received binary chunk into readable text.

---

# 🔄 12. Incremental UI Updates

Instead of waiting for the complete response, we continuously update the AI message.

```javascript
aiResponseBuffer += chunk;

updateAssistantMessage(
    aiResponseBuffer
);
```

So the UI gradually changes:

```text
Chunk 1:
Hello

Chunk 2:
Hello, how

Chunk 3:
Hello, how are

Chunk 4:
Hello, how are you?
```

This creates the familiar real-time AI typing experience.

---

# 🔁 13. Complete Streaming Workflow

The complete architecture looks like this:

```text
User
 │
 │ Prompt
 ▼
Frontend
 │
 │ HTTP Request
 ▼
Backend
 │
 │ .stream()
 ▼
AI Provider
 │
 │ Generated chunks
 ▼
Backend
 │
 │ Streaming HTTP / SSE
 ▼
Frontend
 │
 │ reader.read()
 ▼
TextDecoder
 │
 │
 ▼
UI Update
```

At the same time, the backend can accumulate the chunks:

```text
Chunks
  ↓
Buffer
  ↓
Complete response
  ↓
Conversation History
```

---

# 🧩 14. Key Concepts

| Concept                  | Purpose                                     |
| ------------------------ | ------------------------------------------- |
| `stream()`               | Requests streaming output from the AI model |
| `Flux<String>`           | Represents a reactive stream of strings     |
| `reader.read()`          | Reads the next incoming chunk               |
| `TextDecoder`            | Converts binary chunks into text            |
| `StringBuilder` / Buffer | Reconstructs the complete response          |
| `doOnNext()`             | Executes logic for each incoming chunk      |
| `doOnComplete()`         | Executes logic when streaming finishes      |
| SSE                      | Lightweight server-to-client streaming      |
| WebSockets               | Full-duplex real-time communication         |

---

# 🎯 15. Key Takeaways

1. **Streaming improves user experience** by showing generated content immediately.
2. Streaming does **not** make the LLM generate faster.
3. The major improvement is reducing the **perceived latency / Time to First Token**.
4. True streaming requires both:

   * AI Provider → Backend
   * Backend → Frontend
5. `Flux<String>` can represent the streaming response in Spring.
6. `.stream()` is used instead of `.call()` for streaming.
7. The frontend can use `ReadableStream`, `reader.read()`, and `TextDecoder`.
8. Response chunks can be accumulated to save the complete conversation history.
9. SSE is lightweight and well suited to typical AI chat streaming.
10. Both backend and frontend need to support streaming for a real-time experience.

---

## 📚 Technologies Covered

```text
Java
Spring Boot
Spring AI
Reactive Streams
Flux
HTTP Streaming
SSE
JavaScript
ReadableStream API
TextDecoder
LLM Streaming
```

---

## 🧠 Core Idea

The most important concept from this lesson is:

> **Don't wait for the complete LLM response. Stream each generated chunk from the model → backend → frontend and update the UI incrementally.**

This makes an AI application feel significantly more responsive without changing the model's actual generation speed.
