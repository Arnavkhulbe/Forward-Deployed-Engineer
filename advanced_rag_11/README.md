# Advanced RAG (Retrieval-Augmented Generation)

This README explains the main concepts covered in the **Advanced RAG** lecture in simple terms.

---

## 📌 What is RAG?

**RAG = Retrieval-Augmented Generation**

RAG combines:

1. **Retrieval** → Find relevant information from a knowledge base.
2. **Augmentation** → Give that information to the LLM as context.
3. **Generation** → The LLM uses that context to generate an answer.

### Basic RAG Pipeline

```text
Documents
    ↓
Chunking
    ↓
Embeddings
    ↓
Vector Database
    ↓
User Query
    ↓
Query Embedding
    ↓
Similarity Search
    ↓
Relevant Chunks
    ↓
LLM
    ↓
Answer
```

The main goal of RAG is to make the LLM answer using **relevant external information** instead of relying only on what it learned during training.

---

# ❌ Problems with Basic RAG

A RAG system can fail mainly in two ways.

## 1. Retrieval Failure

The system retrieves the **wrong information**.

```text
User Question
      ↓
Poor Retrieval
      ↓
Wrong Context
      ↓
LLM
      ↓
Wrong Answer
```

Even if the LLM is capable, it cannot give a good answer if the retrieved context is incorrect.

---

## 2. LLM Reasoning Failure

Sometimes retrieval works correctly, but the LLM does not properly understand or use the retrieved information.

```text
User Question
      ↓
Correct Retrieval
      ↓
Correct Context
      ↓
LLM misunderstands context
      ↓
Wrong Answer
```

Therefore, improving RAG requires improving both:

- **Retrieval**
- **LLM reasoning**

---

# ✂️ Chunking

Large documents cannot always be directly stored and searched effectively as a single piece.

Therefore, documents are divided into smaller pieces called **chunks**.

```text
Large Document
      ↓
 ┌────┬────┬────┬────┐
 │ C1 │ C2 │ C3 │ C4 │
 └────┴────┴────┴────┘
```

Each chunk can then be converted into an embedding and stored in a vector database.

---

# 1. Fixed-Size Chunking

The document is divided according to a fixed number of characters or tokens.

Example:

```text
Document
↓
Chunk 1 → 500 tokens
Chunk 2 → 500 tokens
Chunk 3 → 500 tokens
Chunk 4 → 500 tokens
```

### Advantage

Simple to implement.

### Problem

A chunk may cut a meaningful sentence or paragraph in the middle.

---

# 2. Chunk Overlap

To reduce the problem of losing information between chunks, some content can be repeated between neighboring chunks.

Example:

```text
Chunk 1:
A B C D E

Chunk 2:
        D E F G H

Chunk 3:
                G H I J K
```

Here:

- `D E` overlaps between Chunk 1 and Chunk 2.
- `G H` overlaps between Chunk 2 and Chunk 3.

This helps preserve context between chunks.

---

# 3. Structural / Document-Aware Chunking

Instead of blindly splitting text based on size, the document's structure can be considered.

For example:

```text
Document
│
├── Introduction
├── Chapter 1
│   ├── Section 1
│   └── Section 2
├── Chapter 2
│   ├── Section 1
│   └── Section 2
└── Conclusion
```

The chunks can be created according to meaningful sections.

This can preserve more of the original document's meaning.

---

# 4. Semantic Chunking

Semantic chunking tries to divide text according to **meaning** rather than simply using a fixed size.

The goal is to keep related information together.

```text
Related Information
        ↓
   Same Chunk
```

This can produce more meaningful chunks for retrieval.

---

# 🔎 Retrieval

After documents have been converted into embeddings and stored, the system needs to retrieve relevant chunks when the user asks a question.

A common approach is **similarity search**.

```text
User Query
    ↓
Query Embedding
    ↓
Vector Database
    ↓
Similarity Search
    ↓
Relevant Chunks
```

---

# 🔢 Top-K Retrieval

`K` represents how many results we want to retrieve.

For example:

```text
Top-K = 5
```

means:

> Retrieve the 5 most relevant chunks.

Example:

```text
Query
 ↓
Vector Search
 ↓
D1
D2
D3
D4
D5
```

These documents are then passed to the next stage.

---

# 🎯 Similarity Threshold

Instead of only asking for the top K results, we can also define a minimum similarity score.

For example:

```text
Threshold = 0.70
```

Only results with a similarity score above the threshold may be considered relevant.

```text
D1 → 0.92 ✅
D2 → 0.87 ✅
D3 → 0.81 ✅
D4 → 0.55 ❌
D5 → 0.42 ❌
```

This helps prevent obviously irrelevant information from reaching the LLM.

---

# 🏷️ Metadata Filtering

Documents can also contain metadata.

Example:

```text
Document:
"Return policy for electronics"

Metadata:
category = electronics
year = 2026
language = English
```

A query can use metadata to narrow the search.

```text
User Query
    ↓
Metadata Filter
    ↓
Relevant Category
    ↓
Similarity Search
```

This can reduce unnecessary search results.

---

# 🔀 Hybrid Search

One important concept in Advanced RAG is **Hybrid Search**.

Hybrid Search combines different retrieval methods.

The two important approaches are:

### 1. Semantic / Vector Search

Looks for information based on **meaning**.

```text
Query
 ↓
Embedding
 ↓
Vector Search
 ↓
Meaning-based results
```

### 2. Keyword Search

Looks for exact or important words.

```text
Query
 ↓
Keywords
 ↓
Keyword Search
 ↓
Exact-word matches
```

---

## Why Combine Them?

Vector search is good at understanding **meaning**.

Keyword search is good at finding **exact terms**.

For example, a query may contain:

```text
"What is the return policy for product XJ-9281?"
```

Vector search may find documents about return policies.

Keyword search can specifically find documents containing:

```text
XJ-9281
```

Hybrid Search combines both types of results.

```text
                 User Query
                     ↓
            ┌────────┴────────┐
            ↓                 ↓
     Vector Search       Keyword Search
            ↓                 ↓
     Semantic Results    Exact Matches
            └────────┬────────┘
                     ↓
              Combined Results
```

This can improve retrieval quality.

---

# 🔄 Reranking

Reranking is another important Advanced RAG technique.

### Simple Definition

> **Reranking means taking the results that were already retrieved and checking which ones are actually the most relevant.**

It does **not** mean searching the entire database again.

---

## Why Do We Need Reranking?

Suppose the database contains:

```text
1,000,000 chunks
```

Searching all of them with an expensive model would be slow.

Instead, we can use a two-stage process.

### Stage 1 — Fast Retrieval

```text
1,000,000 chunks
        ↓
   Fast Search
        ↓
   20 candidates
```

### Stage 2 — Reranking

```text
20 candidates
      ↓
  Reranker
      ↓
Best 5 results
```

Then only the best results are sent to the LLM.

---

# 🧠 Reranking Example

Suppose the user asks:

```text
Can I return my damaged headphones after 10 days?
```

Initial retrieval might produce:

```text
D1 → General Return Policy
D2 → Electronics Return Policy
D3 → Damaged Product Policy
D4 → Shipping Policy
D5 → Payment Policy
```

The initial search may not perfectly understand which documents are most useful.

The reranker checks them again.

```text
Reranker
   ↓

D2 → Highly relevant
D3 → Highly relevant
D1 → Somewhat relevant
D4 → Not relevant
D5 → Not relevant
```

The system can then send:

```text
D2
D3
D1
```

to the LLM.

---

# 🔁 Retrieval vs Reranking

These two concepts are different.

| Retrieval | Reranking |
|---|---|
| Finds candidate documents | Reorders candidate documents |
| Usually optimized for speed | Focuses more on relevance |
| Searches a large collection | Works on a smaller candidate set |
| First stage | Second stage |
| Retrieves possible answers | Selects the best answers |

### Easy way to remember

```text
Retrieval
= Find candidates

Reranking
= Check candidates again and put the best ones first
```

---

# 🤖 Cross-Encoder Reranking

A reranker can use a **cross-encoder** model.

Instead of independently creating embeddings for the query and document, the cross-encoder considers them together.

```text
Query + Document
       ↓
 Cross-Encoder
       ↓
Relevance Score
```

For example:

```text
Query:
"What is the return policy for damaged headphones?"

Document:
"Damaged electronic products can be returned within..."
       ↓
Cross-Encoder
       ↓
Score = 0.96
```

A higher score indicates stronger relevance.

The lecture also mentions that reranking can be performed using an **LLM or a specialized cross-encoder model**.

---

# 🏗️ Advanced RAG Pipeline

Putting the major concepts together:

```text
                    Documents
                       ↓
                    Chunking
                       ↓
                   Embeddings
                       ↓
                Vector Database
                       ↓
                    User Query
                       ↓
              ┌────────┴────────┐
              ↓                 ↓
       Vector Search       Keyword Search
              ↓                 ↓
              └────────┬────────┘
                       ↓
                  Hybrid Search
                       ↓
                Candidate Results
                       ↓
                   Reranking
                       ↓
                 Top Results
                       ↓
                      LLM
                       ↓
                    Answer
```

---

# 🚀 Simple RAG vs Advanced RAG

## Basic RAG

```text
Documents
   ↓
Chunks
   ↓
Embeddings
   ↓
Vector DB
   ↓
Query
   ↓
Similarity Search
   ↓
Top-K
   ↓
LLM
```

## Advanced RAG

```text
Documents
   ↓
Better Chunking
   ↓
Embeddings + Metadata
   ↓
Vector DB
   ↓
User Query
   ↓
Hybrid Search
   ↓
Filtering
   ↓
Candidate Results
   ↓
Reranking
   ↓
Top Relevant Context
   ↓
LLM
   ↓
Answer
```

---

# 🧩 Key Concepts to Remember

### RAG

Retrieves external information and gives it to an LLM before generating an answer.

### Chunking

Breaking large documents into smaller meaningful pieces.

### Chunk Overlap

Repeating some content between neighboring chunks to preserve context.

### Top-K

Number of results retrieved from the search.

### Similarity Threshold

Minimum relevance score required for a result to be considered.

### Metadata Filtering

Using information such as category, date, or language to narrow the search.

### Hybrid Search

Combining **semantic/vector search** with **keyword search**.

### Reranking

Taking retrieved candidates and **reordering them based on deeper relevance checking**.

### Cross-Encoder

A model that takes the **query and document together** and produces a relevance score.

---

# 🧠 Final Mental Model

Remember Advanced RAG as:

```text
1. BREAK
   ↓
Break documents into useful chunks

2. SEARCH
   ↓
Find potentially relevant chunks

3. COMBINE
   ↓
Use semantic + keyword search

4. FILTER
   ↓
Remove irrelevant results

5. RERANK
   ↓
Put the most relevant results first

6. GENERATE
   ↓
Give the best context to the LLM

7. ANSWER
   ↓
Generate the final response
```

The central idea of **Advanced RAG** is not simply:

> "Search → LLM"

It is:

> **Improve the quality of the information reaching the LLM before asking it to generate the answer.**
