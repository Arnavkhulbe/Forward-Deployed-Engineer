# Vector Databases — Problems with Vectors & Embeddings

## 📌 Overview

When working with **embeddings**, we represent data such as text, movies, documents, products, etc. as numerical vectors.

At a small scale, we can simply store these vectors in application memory and compare them with a user's query.

However, as the number of vectors and their dimensions increase, this approach becomes expensive.

This lecture explains:

- How vector search works
- Why storing vectors in application memory doesn't scale
- Why traditional SQL databases are not enough for vector similarity search
- What K-Nearest Neighbors (KNN) means
- Why brute-force vector search is expensive
- Why we need indexing
- How KD-Trees work conceptually
- What the **Curse of Dimensionality** means
- Why specialized **Vector Databases** are required

---

# 1. 🎬 Movie Recommendation Example

Suppose we have a collection of movies:

```text
Interstellar
Batman
Superman
Gravity
Titanic
...
```

We take the description of every movie and pass it through an **embedding model**.

For example:

```text
Interstellar
     ↓
Embedding Model
     ↓
[0.12, 0.45, 0.78, ...]
```

Similarly:

```text
Batman
     ↓
Embedding Model
     ↓
[0.31, 0.22, 0.11, ...]
```

Each movie now has an embedding/vector.

---

# 2. 🔎 Searching for a Movie

Suppose the user searches:

> "I want a space-related movie."

We also convert this query into an embedding:

```text
User Query
    ↓
Embedding Model
    ↓
[0.14, 0.43, 0.81, ...]
```

Now we compare this query vector with every movie vector.

For example:

```text
Query → Interstellar = 0.94
Query → Superman     = 0.72
Query → Batman       = 0.31
Query → Titanic      = 0.20
```

Then we sort according to similarity:

```text
1. Interstellar → 0.94
2. Superman     → 0.72
3. Batman       → 0.31
4. Titanic      → 0.20
```

If:

```text
K = 3
```

we return the top 3 movies.

---

# 3. 🔄 Complete Initial Flow

Our original recommendation system works like this:

```text
User Query
     ↓
Create Query Embedding
     ↓
Compare With All Movie Embeddings
     ↓
Calculate Similarity
     ↓
Sort Results
     ↓
Return Top K
```

This approach works well when we have a small number of vectors.

For example:

```text
30 movies
100 movies
1000 movies
```

But problems appear when the dataset becomes very large.

---

# 4. ❌ Problem 1 — Storing Vectors in Application Memory

Initially, we could store vectors directly inside our application.

For example:

```python
movies = [
    [0.12, 0.45, 0.78, ...],
    [0.31, 0.22, 0.11, ...],
    [0.51, 0.42, 0.72, ...]
]
```

This is fine for a small dataset.

But imagine having:

```text
Millions of vectors
```

Applications cannot efficiently keep such a large amount of vector data in their RAM.

Therefore:

> We need persistent storage for our vectors.

The natural solution is a **database**.

---

# 5. 🗄️ Can a Traditional SQL Database Store Vectors?

Yes.

We could create a table like:

| ID | Name | Description | Embedding |
|---|---|---|---|
| 1 | Interstellar | Space movie... | `[0.12,...]` |
| 2 | Batman | Superhero... | `[0.31,...]` |
| 3 | Superman | Superhero... | `[0.51,...]` |

There are different ways to store embeddings, such as:

- Serialized data
- JSON
- Binary representation
- Specialized vector types/extensions

So:

> **Traditional databases can store vectors.**

But storing vectors isn't the main problem.

The bigger question is:

> **Can traditional databases efficiently search vectors based on similarity?**

---

# 6. 🔍 How Traditional Databases Search

Traditional SQL databases are designed around conditions and matching.

For example:

```sql
SELECT *
FROM movies
WHERE id = 3;
```

This asks:

> Find the row where ID is exactly `3`.

Traditional databases can also perform operations such as:

```sql
WHERE year > 2010
```

```sql
WHERE id = 3
```

```sql
WHERE name = 'Batman'
```

These are based on conditions and matching.

---

# 7. ⚠️ Vector Search Is Different

With vectors, we usually don't ask:

```sql
WHERE embedding = [0.2, 0.4, 0.8]
```

Instead, we ask:

> Which vectors are most similar to my query vector?

For example:

```text
Query
  ↓
[0.2, 0.4, 0.8]
```

Compare:

```text
Movie A → 0.91 similarity
Movie B → 0.23 similarity
Movie C → 0.87 similarity
```

We want:

```text
Movie A
Movie C
...
```

The important difference is:

```text
Traditional DB
      ↓
Exact/conditional matching
      ↓
YES / NO

Vector DB
      ↓
Similarity matching
      ↓
Similarity Score
      ↓
Top K results
```

---

# 8. 🧠 K-Nearest Neighbors (KNN)

The approach we initially used is called:

> **K-Nearest Neighbors (KNN)**

Imagine vectors as points:

```text
              A

      B

                 Q

          C

                       D
```

`Q` represents our query.

We want to find the points closest to `Q`.

If:

```text
K = 3
```

we return the 3 closest vectors.

```text
Q
│
├── C
├── B
└── A
```

These are the **3 nearest neighbors**.

---

# 9. 📏 Measuring Similarity

To determine which vectors are closest, we need a distance/similarity measurement.

Two concepts discussed are:

## Euclidean Distance

Measures the distance between points.

```text
A ●
   \
    \
     ● B
```

Smaller distance means the vectors are closer.

---

## Cosine Similarity

Looks at the direction/angle between vectors.

Conceptually:

```text
Similar direction
      ↗
     ↗
    ↗
```

The lecture discusses both **Euclidean distance** and **Cosine Similarity** as ways of comparing vectors.

---

# 10. 💥 Problem With Brute-Force Search

Suppose we have:

```text
100 vectors
```

We perform:

```text
100 comparisons
```

No major problem.

Suppose:

```text
1,000 vectors
```

We perform:

```text
1,000 comparisons
```

Still manageable.

But suppose:

```text
100,000,000 vectors
```

Now we need:

```text
100,000,000 comparisons
```

for one query.

The basic search complexity becomes approximately:

```text
O(n × d)
```

Where:

- `n` = number of vectors
- `d` = dimensions of each vector

---

# 11. 🧮 Example: 10 Million Vectors

Suppose:

```text
Number of vectors = 10,000,000
Dimensions       = 1536
```

Then:

```text
10,000,000 × 1536
```

=

```text
15,360,000,000
```

Approximately:

```text
15.36 billion
```

dimension-level operations for the simplified brute-force calculation.

And this is for just **one query**.

---

# 12. 💾 Vector Storage Problem

Now consider storage.

Suppose each vector has:

```text
1536 dimensions
```

Assume each floating-point value requires:

```text
4 bytes
```

Then:

```text
1536 × 4
= 6144 bytes
```

Approximately:

```text
6 KB
```

So one vector requires roughly:

```text
6 KB
```

Now suppose we have:

```text
10 million vectors
```

Then approximately:

```text
10,000,000 × 6 KB
≈ 60 GB
```

That's around:

```text
60 GB
```

just for the vector embeddings.

This does **not** include metadata such as:

```text
Movie ID
Movie Name
Description
Genre
Year
etc.
```

Therefore, we have two major problems:

```text
1. Storage
2. Computation
```

---

# 13. ⚡ Why Do We Need Indexing?

Instead of comparing the query with every vector:

```text
Query
 ↓
Vector 1
Vector 2
Vector 3
Vector 4
...
Vector 10,000,000
```

we want to somehow eliminate irrelevant vectors.

This leads to the idea of:

> **Indexing**

Traditional databases already use indexing.

---

# 14. 📚 Simple Binary Search Example

Suppose we have sorted numbers:

```text
1 2 3 4 5 6 7 8 9
```

We want to find:

```text
6
```

### Linear Search

We check:

```text
1 → No
2 → No
3 → No
4 → No
5 → No
6 → Yes
```

Complexity:

```text
O(n)
```

---

### Binary Search

Instead of checking everything, we check the middle.

```text
1 2 3 4 | 5 | 6 7 8 9
            ↑
```

Since:

```text
5 < 6
```

we know the answer must be on the right.

We eliminate half the search space.

Binary search has approximately:

```text
O(log n)
```

complexity.

---

# 15. 🧩 Can We Do the Same Thing With Vectors?

This works nicely in **one dimension**.

Suppose we have:

```text
12
35
42
67
80
86
99
```

Our query is:

```text
38
```

We know:

```text
35 < 38 < 42
```

Therefore, the nearest vectors are likely around:

```text
35
42
```

We don't need to compare against:

```text
80
86
99
```

because they are clearly farther away.

So indexing can reduce the number of comparisons.

---

# 16. 📐 What Happens in Multiple Dimensions?

Now suppose vectors have two dimensions.

Example:

```text
A = (2,7)
B = (5,1)
```

Which one is smaller?

Looking at X:

```text
A → 2
B → 5

A is smaller
```

But looking at Y:

```text
A → 7
B → 1

B is smaller
```

There is no simple global ordering like:

```text
A < B
```

This makes traditional sorting/indexing much harder.

---

# 17. 🌳 KD-Trees

One approach for multidimensional data is a:

> **KD-Tree (K-Dimensional Tree)**

The basic idea is to repeatedly divide the space.

For example:

```text
First split → X
Second split → Y
Third split → X
Fourth split → Y
...
```

Conceptually:

```text
              X
             / \
            /   \
           Y     Y
          / \   / \
         X   X X   X
```

The goal is to divide the search space into smaller regions.

---

# 18. ✂️ Pruning

Suppose we search for a query and discover a very close vector.

If an entire region is clearly too far away, we can ignore it.

This is called:

> **Pruning**

Instead of:

```text
Search everything
████████████████████
```

we try to do:

```text
Search relevant region
██████░░░░░░░░░░░░░░
```

This can significantly reduce the amount of searching.

---

# 19. 🚨 Curse of Dimensionality

The major problem is that real embeddings can have hundreds or thousands of dimensions.

For example:

```text
384 dimensions
768 dimensions
1024 dimensions
1536 dimensions
3072 dimensions
```

As the number of dimensions increases, spatial indexing becomes increasingly difficult.

This is known as:

> **Curse of Dimensionality**

---

# 20. Why Does the Curse of Dimensionality Matter?

In low dimensions:

```text
2D → easier to divide space
3D → still manageable
```

But as dimensions increase:

```text
10D
100D
500D
1536D
```

there are many more possible regions where a nearest neighbor might exist.

Therefore, it becomes harder to safely eliminate large portions of the search space.

Eventually:

```text
High dimensions
      ↓
Harder pruning
      ↓
More regions need to be searched
      ↓
Search starts behaving more like brute force
```

So our indexing method loses much of its advantage.

---

# 21. 🔑 The Two Main Problems

The lecture ultimately identifies two major challenges.

## Problem 1 — Storage

Large numbers of high-dimensional vectors require significant storage.

```text
Millions of vectors
        ↓
Huge storage requirement
```

---

## Problem 2 — Searching

Comparing a query with every vector requires enormous computation.

```text
Millions of vectors
        ↓
Millions of comparisons
        ↓
High computational cost
```

---

# 22. ❌ Why Traditional Indexing Isn't Enough

We might think:

```text
Traditional DB
      ↓
Index
      ↓
Fast search
```

But vector embeddings are different.

They are:

```text
High-dimensional
        +
Similarity-based
        +
Not exact-match based
```

Traditional indexing techniques don't directly solve this problem efficiently.

Even KD-Trees become less effective as dimensionality increases because of the **Curse of Dimensionality**.

---

# 23. 🗄️ Why Do We Need Vector Databases?

Now we can understand why Vector Databases exist.

A Vector Database is designed specifically to work with:

```text
Vector embeddings
       ↓
Similarity search
       ↓
Nearest neighbors
       ↓
Top-K results
```

Instead of simply asking:

```text
"Does this value equal X?"
```

we want to ask:

```text
"Which vectors are most similar to this query?"
```

And we need to perform this efficiently across potentially millions or billions of vectors.

---

# 24. 🧠 Complete Concept

The evolution can be understood as:

```text
Small Dataset
     ↓
Store vectors in application memory
     ↓
Compare with every vector
     ↓
Works
```

Then:

```text
Large Dataset
     ↓
Application memory becomes impractical
     ↓
Move vectors to database
```

Then:

```text
Traditional Database
     ↓
Can store vectors
     ↓
But similarity search is difficult
```

Then:

```text
Brute Force KNN
     ↓
Compare with every vector
     ↓
Too much computation
```

Then:

```text
Try Indexing
     ↓
Binary Search / KD-Tree concepts
     ↓
Curse of Dimensionality
     ↓
Traditional indexing becomes less effective
```

Finally:

```text
Need Specialized Vector Search
             ↓
      VECTOR DATABASES
```

---

# 25. 📌 Key Takeaways

### 1. What is an embedding?

A numerical representation of data.

```text
Text
 ↓
Embedding Model
 ↓
Vector
```

---

### 2. What is vector search?

Finding vectors that are most similar to a query vector.

---

### 3. What is KNN?

**K-Nearest Neighbors** finds the `K` closest vectors to a query.

---

### 4. What is brute-force search?

Comparing the query with every vector.

```text
Query → Vector 1
Query → Vector 2
Query → Vector 3
...
Query → Vector N
```

---

### 5. What is the problem with brute force?

The cost grows approximately with:

```text
O(n × d)
```

where:

```text
n = number of vectors
d = vector dimensions
```

---

### 6. Why can't we simply use traditional database indexes?

Because vector search is based on **similarity**, not exact matching.

---

### 7. What is a KD-Tree?

A structure that divides multidimensional space to reduce the search area.

---

### 8. What is pruning?

Ignoring regions that cannot contain a useful/nearest result.

---

### 9. What is the Curse of Dimensionality?

As dimensions increase, it becomes harder for traditional spatial indexes to eliminate large portions of the search space.

---

### 10. Why do we need Vector Databases?

Because we need specialized systems capable of efficiently:

```text
Storing
   ↓
Indexing
   ↓
Searching
   ↓
High-dimensional vectors
```

using similarity rather than traditional exact matching.

---

# 🎯 One-Line Summary

> **Vector databases exist because storing and searching millions of high-dimensional embeddings using normal application memory, brute-force comparison, and traditional database indexing becomes too expensive and inefficient.**
