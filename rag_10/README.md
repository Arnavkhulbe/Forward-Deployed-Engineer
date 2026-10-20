RAG — Retrieval-Augmented Generation
What is RAG?
RAG stands for Retrieval-Augmented Generation. It is an architecture that allows a Large Language Model (LLM) to answer questions using relevant information retrieved from an external knowledge source.

Instead of providing the LLM with an entire knowledge base, RAG retrieves only the information relevant to the user's question, adds it to the LLM's context, and lets the LLM generate an answer.

The core idea:
User Question
     |
     v
Retrieve Relevant Information
     |
     v
Add Information to the LLM Context
     |
     v
LLM Generates an Answer
1. How Does a Normal LLM Work?
A Large Language Model generates text by predicting tokens one after another.

For example:
Input: "The capital of India is"
                 |
                 v
             LLM predicts
                 |
                 v
               "Delhi"
The model continues generating tokens until it produces a complete response.

An LLM answers using the information available in its trained knowledge and the context provided to it. A normal LLM does not automatically have access to every previous conversation or private company document.
2. Conversation History as Memory
An application can store previous messages and send them to the LLM with a new question.

For example:
User: My name is Aditya.
AI: Hello, Aditya!

User: What is my name?
If the application includes the previous messages in the new request, the LLM can use that context to answer.

This is conversation-history memory. However, the history can become very long as more messages are added.
Problems with sending all history
- Large context: The amount of text sent to the model keeps growing.
- Higher cost: Many LLM services charge based on the number of input and output tokens.
- Limited context window: A model can process only a limited amount of context in one request.
This motivates retrieving only the information needed for a particular question.
3. Why Not Put All Knowledge in the System Prompt?
Imagine building an e-commerce chatbot that needs to answer questions about:
- Refund policies
- Return policies
- Payment policies
- Exchange policies
- Orders and accounts
Putting every company policy into the system prompt may make it extremely long. Yet a particular question may need only one small section of one document.

For example, a question about returning shoes needs relevant return-policy information—not every payment, shipping, and account document.

RAG addresses this problem by retrieving relevant information before the LLM generates its answer.
4. The Three Parts of RAG
RAG has three stages:
R — Retrieval
Find information relevant to the user's question.
A — Augmented
Add the retrieved information to the LLM's input as context.
G — Generation
The LLM uses the question and available context to generate an answer.
Retrieval  -> Find relevant information
     |
     v
Augmentation -> Add it to the prompt/context
     |
     v
Generation -> LLM produces an answer
5. What Is an Embedding?
An embedding is a numerical representation of text, created by an embedding model. It is represented as a vector—a list of numbers.

For example, this text:
"Customers can return clothing within 10 days."
might be converted into a vector similar in form to:
[0.12, -0.45, 0.87, ...]
The numbers above are illustrative, not an actual embedding.

Embedding models are designed so that texts with related meanings can have similar vector representations. This helps a system search for meaning, not just exact keyword matches.
6. What Is a Vector Database?
A vector database stores vectors and supports searches for vectors similar to a query vector. In RAG, it can store embeddings for chunks of documents, along with the original text and associated metadata.

Examples include:
- Pinecone
- pgvector
A simplified flow looks like this:
Document Chunk
      |
      v
Embedding Model
      |
      v
Numerical Vector
      |
      v
Vector Database
The original text is important: the LLM generally needs the retrieved text, not just the vector's numbers.
7. What Is Similarity Search?
When a user asks a question, the system converts the question into an embedding and searches the vector database for relevant chunks.

For example, the database might contain:
Vector A -> Refund policy
Vector B -> Payment policy
Vector C -> Return policy
Vector D -> Shipping policy
User question:
I bought shoes 7 days ago. Can I return them?

The system searches for vectors similar to the question and may retrieve return-policy chunks first.
Common similarity measures
- Cosine similarity
- Euclidean distance
- Dot product
Vector search can use efficient indexing algorithms, such as HNSW and IVF, to speed up retrieval in large collections.

Similarity search is not a guarantee that the retrieved text is correct or sufficient. Retrieval quality depends on the embeddings, chunking, search settings, and source documents.
8. Why Do We Need Chunking?
Documents can be very large. A PDF might contain hundreds of pages and cover several topics.

Chunking means splitting a document into smaller pieces before creating embeddings.
Large Document
      |
      v
  +--------+
  | Chunk 1|
  +--------+
  | Chunk 2|
  +--------+
  | Chunk 3|
  +--------+
  |   ...  |
  +--------+
      |
      v
Create an embedding for each chunk
For example, one chunk might contain return-policy information while another contains payment information. Smaller chunks help the system retrieve more specific content.

Chunk size matters: chunks that are too large may contain too many unrelated topics, while chunks that are too small may lose important context.
9. Chunk Overlap
A chunk boundary can split a sentence or an important piece of information.

For example:
Chunk 1: "The return period for clothing items is..."
Chunk 2: "...10 days after delivery."
If only the first chunk is retrieved, the policy is incomplete.

Chunk overlap addresses this by repeating some text between neighboring chunks.

For example:
Chunk 1 = words 1–1000
Chunk 2 = words 801–1800
Here, words 801–1000 appear in both chunks.

Overlap can preserve context across boundaries, although it also creates some repeated content and increases the number of stored chunks.
10. The Indexing Phase
Before users ask questions, the documents must be prepared for retrieval. This is commonly called the indexing phase.
Documents / PDFs
       |
       v
Extract Text
       |
       v
Split Text into Chunks
       |
       v
Create Embeddings
       |
       v
Store Vectors and Text
       |
       v
Vector Database
This phase prepares the knowledge base. It is separate from the process that handles each user's question.
11. The Query Phase
When a user asks a question, the system retrieves the most relevant information.
Step 1 — Receive the question
"I bought shoes 7 days ago. Can I return them?"
Step 2 — Create a query embedding
The embedding model converts the question into a vector.
Step 3 — Search the vector database
Similarity search finds relevant document chunks.
Step 4 — Build the LLM input
The application provides the LLM with:
- System instructions
- The user's question
- The retrieved text (context)
Step 5 — Generate the answer
The LLM uses the provided context to formulate a response.
User Question
      |
      v
Create Query Embedding
      |
      v
Search Vector Database
      |
      v
Retrieve Relevant Chunks
      |
      v
Combine Question + Context + Instructions
      |
      v
LLM
      |
      v
Final Answer
12. Complete RAG Architecture
RAG has two connected workflows: indexing and question answering.
Indexing
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
Question answering
User Question
    |
    v
Query Embedding
    |
    v
Similarity Search
    |
    v
Relevant Text Chunks
    |
    v
Question + Retrieved Context + Instructions
    |
    v
LLM
    |
    v
Generated Answer
The vector database supplies relevant external information; the LLM uses that information to generate a natural-language answer.
13. RAG vs. a Normal LLM
Normal LLM
User Question
      |
      v
     LLM
      |
      v
    Answer
LLM with RAG
User Question
      |
      v
Retrieve Relevant Information
      |
      v
Add Retrieved Context
      |
      v
     LLM
      |
      v
    Answer
RAG gives the LLM relevant external information at question time. It does not permanently train the model on the retrieved documents.
14. RAG vs. Conversation Memory
These are related but different concepts.
Conversation history	RAG
Stores or supplies previous chat messages	Retrieves information from an external knowledge source
Helps the model use earlier conversational context	Helps the model answer using documents and other stored knowledge
Can grow as the conversation continues	Retrieves selected pieces relevant to the current question
Often sent as part of the current prompt	Retrieved text is added to the current prompt


A system can use both conversation history and RAG together.
15. Important Terminology
Term	Meaning
LLM	A model that generates text
Embedding	Numerical representation of text
Vector	A list of numbers representing information
Vector database	Stores and searches vectors
Similarity search	Finds vectors close to a query according to a similarity measure
Chunk	A smaller piece of a document
Chunking	Splitting a document into smaller pieces
Chunk overlap	Repeating some text between neighboring chunks
Indexing	Preparing documents and storing them for retrieval
Retrieval	Finding relevant information
Context	Information provided to the LLM for the current response
RAG	Retrieval-Augmented Generation


16. Key Takeaways
- A normal LLM does not automatically know private or newly supplied company documents.
- Sending a complete conversation or a huge collection of documents in every request can be inefficient.
- Embeddings represent text as vectors, enabling semantic search.
- Chunking divides large documents into smaller, searchable pieces.
- Overlap helps preserve context across chunk boundaries.
- A vector database stores embeddings and supports similarity search.
- RAG retrieves relevant text and adds it to the LLM's context.
- The LLM then generates an answer using the question and retrieved information.
- RAG can reduce irrelevant context, but it does not guarantee a correct answer. The quality of the documents and retrieval process still matters.
Final Summary
RAG = Retrieve → Augment → Generate
Documents
   |
   v
Chunks
   |
   v
Embeddings
   |
   v
Vector Database

User Question
   |
   v
Retrieve Relevant Chunks
   |
   v
Add Context to the LLM
   |
   v
Generate Answer
The central idea is simple:
Instead of giving an LLM an entire knowledge base, retrieve the relevant information, add it to the LLM's context, and let the LLM generate an answer based on that information.
