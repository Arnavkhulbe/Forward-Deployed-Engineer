# Vector Database — ANN, IVF & HNSW

## 📌 Overview

A **Vector Database** is designed to store and search high-dimensional vectors efficiently.

The main problem is that when we have **millions of vectors**, searching through every vector becomes extremely expensive.

This lecture focuses mainly on:

- Why brute-force vector search is slow
- KNN (K-Nearest Neighbors)
- ANN (Approximate Nearest Neighbors)
- Recall
- IVF (Inverted File Index)
- Centroids and clustering
- HNSW (Hierarchical Navigable Small World)
- Vector storage problems
- Product Quantization (PQ)

---

# 1. What is a Vector?

A vector is a collection of numerical values that represents some information.

For example, a movie can be converted into an embedding:

```text
Interstellar → [0.21, 0.87, 0.43, ...]
Batman       → [0.76, 0.12, 0.54, ...]
Titanic      → [0.11, 0.23, 0.91, ...]
```

The vector represents the information contained in the movie.

An **embedding model** can convert text or other data into these vectors.

---

# 2. Similar Vectors Are Close Together

One important property of embeddings is:

> Similar objects tend to have similar vectors.

For example:

```text
Interstellar
      ↓
The Martian
      ↓
Gravity
```

These movies are related to space, so their vectors may be located close to each other.

While:

```text
Interstellar ---------------- Titanic
```

may be much farther apart.

This allows us to perform **similarity search**.

---

# 3. Vector Similarity Search

Suppose a user asks:

```text
"I want to watch a space movie."
```

We convert the query into an embedding:

```text
Query → Q = [0.32, 0.71, 0.45, ...]
```

Now we calculate the similarity between `Q` and our stored movie vectors.

For example:

```text
Interstellar → 0.97
Gravity      → 0.94
Martian      → 0.92
Titanic      → 0.11
Batman       → 0.08
```

Then we return the highest-scoring results.

```text
Top 3:

1. Interstellar
2. Gravity
3. The Martian
```

---

# 4. The Problem With Brute-Force Search

Suppose our database contains:

```text
100 million vectors
```

and every vector has:

```text
1536 dimensions
```

To compare a query vector with another vector, we have to perform calculations across all of its dimensions.

So searching through all vectors becomes extremely expensive.

Conceptually:

```python
for vector in all_vectors:
    similarity(query, vector)
```

The query is compared with:

```text
Vector 1
Vector 2
Vector 3
Vector 4
...
Vector 100,000,000
```

This is called **brute-force / exact search**.

The lecture estimates the operation count at a very large scale (around billions of dimension-level calculations), showing why this approach becomes expensive. 

---

# 5. KNN — K-Nearest Neighbors

The brute-force approach is essentially used to find the:

## K Nearest Neighbors

If:

```text
K = 3
```

we want the three vectors most similar to our query.

The process is:

```text
Query
  ↓
Compare with every vector
  ↓
Calculate similarity
  ↓
Sort results
  ↓
Return top K
```

### Advantage

The result is exact.

### Problem

It requires a huge number of comparisons when the database contains millions of vectors.

---

# 6. ANN — Approximate Nearest Neighbors

To solve the performance problem, we use:

## ANN

**ANN = Approximate Nearest Neighbors**

Instead of comparing the query with every vector:

```text
100 million vectors
        ↓
compare with all
        ↓
very expensive
```

ANN tries to identify a smaller region where the likely nearest vectors are located:

```text
100 million vectors
        ↓
identify relevant region
        ↓
search smaller set
        ↓
return top K
```

For example:

```text
100 million vectors
        ↓
     ANN
        ↓
   5,000 vectors
        ↓
     Top K
```

This can drastically reduce the number of comparisons.

---

# 7. Why is ANN "Approximate"?

ANN does not always guarantee the exact same answer as KNN.

Suppose exact KNN returns:

```text
A
B
C
```

ANN might return:

```text
A
B
D
```

If `D` is still very similar to the query, the result can still be useful.

Therefore:

```text
KNN
→ Exact
→ More computation

ANN
→ Approximately correct
→ Much faster
```

---

# 8. Recall

To measure how well ANN performs compared with exact KNN, we can use **Recall**.

Suppose the correct results are:

```text
A B C D E F G H I J
```

ANN returns:

```text
A B C D X F G H I Y
```

Correct results:

```text
A B C D F G H I
```

There are:

```text
8 correct results
```

out of:

```text
10 expected results
```

Therefore:

```text
Recall = Correct Results / Total Expected Results

Recall = 8 / 10

Recall = 0.8

Recall = 80%
```

### Higher Recall

Higher recall means ANN is finding more of the results that exact KNN would have found.

---

# 9. Speed vs Recall Trade-off

ANN introduces an important trade-off.

Suppose we have:

```text
100 million vectors
```

### Search 1,000 vectors

```text
Very fast
↓
Lower recall
```

### Search 5,000 vectors

```text
Slightly slower
↓
Higher recall
```

### Search 20,000 vectors

```text
More computation
↓
Even higher recall
```

Therefore:

```text
More vectors searched
        ↓
More computation
        ↓
Higher recall
```

The goal is to find a good balance between:

```text
Speed ↔ Recall
```

---

# 10. IVF — Inverted File Index

One important ANN algorithm is:

## IVF

**IVF = Inverted File Index**

The basic idea is:

> Divide the huge vector space into smaller clusters.

Suppose we have:

```text
1,000,000 vectors
```

We can divide them into approximately:

```text
1,000 clusters
```

with around:

```text
1,000 vectors per cluster
```

Conceptually:

```text
1,000,000 vectors
        ↓
     Clustering
        ↓
 ┌──────┼──────┐
 ↓      ↓      ↓
C1     C2     C3
...    ...    ...
```

---

# 11. What is a Cluster?

A cluster is simply a group of vectors that are relatively close to each other.

Conceptually:

```text
Cluster A

   • • •
 • • • •
   • •
```

Another cluster:

```text
Cluster B

              • •
             • • •
              • •
```

Similar vectors tend to appear in the same general region.

---

# 12. What is a Centroid?

Each cluster has a central point called a:

## Centroid

Conceptually:

```text
      •
   •  X  •
      •
```

`X` represents the centroid.

The centroid acts as a representative point for the cluster.

Clustering algorithms such as **K-Means** can be used to obtain centroids.

---

# 13. How IVF Search Works

Suppose we have:

```text
1,000 clusters
```

and a query vector:

```text
Q
```

### Step 1 — Compare Query With Centroids

Instead of comparing `Q` with all 1 million vectors:

```text
Q → Centroid 1
Q → Centroid 2
Q → Centroid 3
...
Q → Centroid 1000
```

We only compare against the centroids.

Then we find the closest centroid.

For example:

```text
Closest centroid
       ↓
Cluster 42
```

---

# 14. Step 2 — Search Inside the Selected Cluster

Suppose Cluster 42 contains:

```text
1,000 vectors
```

Now we compare:

```text
Q
↓
Vector 1
Vector 2
Vector 3
...
Vector 1000
```

Then we select the top K vectors.

Instead of searching:

```text
1,000,000 vectors
```

we searched approximately:

```text
1,000 vectors
```

This is the main advantage of IVF.

---

# 15. IVF Search Flow

```text
             1,000,000 Vectors
                     │
                     ↓
                IVF Index
                     │
                     ↓
                1,000 Clusters
                     │
                     ↓
                Query Q
                     │
                     ↓
        Compare with centroids
                     │
                     ↓
          Find closest cluster
                     │
                     ↓
      Search vectors in cluster
                     │
                     ↓
                  Top K
```

---

# 16. Problem With Searching Only One Cluster

There is an important limitation.

Suppose:

```text
Cluster A
   • • •
    • Q •

Cluster B
          •
```

The centroid of Cluster A may be closer to `Q`.

So IVF selects Cluster A.

But one vector inside Cluster B might actually be closer to `Q`.

Because Cluster B was ignored:

```text
Potentially correct vector
        ↓
Never searched
```

This is one reason IVF is approximate.

---

# 17. Searching Multiple Clusters

Instead of searching only one cluster, we can search multiple nearby clusters.

For example:

```text
Top 5 closest clusters
```

Suppose every cluster has approximately 1,000 vectors.

Then:

```text
5 × 1,000

= 5,000 vectors
```

So instead of:

```text
1,000,000 vectors
```

we search:

```text
5,000 vectors
```

This improves recall while still reducing the search space dramatically.

---

# 18. IVF Trade-off

```text
Search 1 cluster
      ↓
Very fast
      ↓
Lower recall
```

```text
Search 5 clusters
      ↓
More computation
      ↓
Higher recall
```

```text
Search more clusters
      ↓
More computation
      ↓
Higher recall
```

Therefore, IVF provides a trade-off between:

```text
Search Speed ↔ Recall
```

---

# 19. HNSW

The second major ANN algorithm discussed is:

## HNSW

**HNSW = Hierarchical Navigable Small World**

Instead of dividing vectors into clusters, HNSW represents vectors as a **graph**.

---

# 20. Vectors as Graph Nodes

Suppose we have:

```text
A
B
C
D
E
F
```

Each vector becomes a graph node.

Nearby/similar vectors are connected.

Conceptually:

```text
A ----- B
| \     |
|  \    |
C ----- D
 \     /
   E
   |
   F
```

Here:

```text
Node  = Vector
Edge  = Connection
```

The graph allows us to navigate from one vector toward vectors that are increasingly closer to the query.

---

# 21. Why Does HNSW Use Layers?

HNSW uses multiple graph layers.

Conceptually:

```text
Layer 2:
        A -------- F
         \        /

Layer 1:
      A --- C --- F --- H

Layer 0:
   A-B-C-D-E-F-G-H-I-J-K
```

The layers have different densities.

```text
Top layer
   ↓
Very sparse

Middle layer
   ↓
More nodes

Bottom layer
   ↓
Many nodes
```

Some vectors are selected probabilistically to appear in higher layers.

---

# 22. Why Are Multiple Layers Useful?

The higher layers allow us to move quickly across the vector space.

Instead of checking many nodes:

```text
A → B → C → D → E → F → G
```

we can make larger jumps:

```text
A ─────────→ F
              ↓
              ↓
              G
```

Then we move down to denser layers for more detailed searching.

---

# 23. HNSW Search

The basic search process is:

```text
Query Q
   ↓
Start at upper layer
   ↓
Move toward closer nodes
   ↓
Go down one layer
   ↓
Move toward closer nodes
   ↓
Go down again
   ↓
Detailed search
   ↓
Nearest neighbors
```

So HNSW is essentially:

> **Navigate through a graph toward the region containing the nearest vectors.**

---

# 24. IVF vs HNSW

| IVF | HNSW |
|---|---|
| Uses clusters | Uses graphs |
| Uses centroids | Uses connected nodes |
| Selects relevant clusters | Navigates through nodes |
| Cluster-based search | Graph-based search |
| Searches selected clusters | Searches through graph layers |
| Reduces search space | Reduces unnecessary traversal |

Both are trying to solve the same fundamental problem:

> **Avoid comparing the query against every vector.**

---

# 25. Another Problem — Storage

Search speed isn't the only problem.

Vectors can also require a lot of storage.

Suppose:

```text
100 million vectors
1536 dimensions
```

If every dimension uses `float32`:

```text
float32 = 4 bytes
```

Then:

```text
1536 × 4
= 6144 bytes
```

So one vector requires approximately:

```text
6 KB
```

For 100 million vectors:

```text
≈ 614 GB
```

And this is only the vector data.

Metadata and other database information require additional storage.

---

# 26. Vector Compression

Because vectors can consume a lot of storage, vector databases can use compression techniques.

One technique discussed in the lecture is:

## Product Quantization (PQ)

The basic purpose is:

```text
Large vector
     ↓
Compression
     ↓
Smaller representation
     ↓
Less storage
```

So we can think of the two major optimization problems as:

```text
                Vector Database
                       │
          ┌────────────┴────────────┐
          ↓                         ↓
    Search Problem             Storage Problem
          ↓                         ↓
         ANN                    Compression
       /     \                     ↓
     IVF     HNSW                  PQ
```

---

# 27. Complete Picture

The overall architecture discussed in the lecture can be remembered as:

```text
                         VECTOR DATABASE
                                │
                 ┌──────────────┴──────────────┐
                 │                             │
                 ↓                             ↓
          Efficient Search              Efficient Storage
                 │                             │
                 ↓                             ↓
                ANN                     Compression
             /       \                        │
            ↓         ↓                       ↓
          IVF        HNSW                     PQ
           │           │
           ↓           ↓
       Clusters      Graph
           │           │
       Centroids     Layers
           │           │
           └─────┬─────┘
                 ↓
          Fewer Comparisons
                 ↓
             Faster Search
```

---

# 28. Important Terms — Quick Revision

| Term | Meaning |
|---|---|
| **Vector** | Numerical representation of information |
| **Embedding** | Representation of data as a vector |
| **Similarity** | Measure of how close two vectors are |
| **KNN** | Finds exact K nearest vectors |
| **ANN** | Finds approximately nearest vectors faster |
| **Recall** | How many correct nearest results ANN retrieves |
| **IVF** | Divides vectors into clusters |
| **Centroid** | Center/representative point of a cluster |
| **HNSW** | Graph-based ANN algorithm |
| **Node** | A vector in the HNSW graph |
| **Edge** | Connection between vectors |
| **PQ** | Vector compression technique |

---

# 29. Most Important Concepts to Remember

### 1. Why not compare with everything?

Because millions of high-dimensional vectors require too many calculations.

### 2. What does ANN do?

It reduces the number of vectors that need to be searched.

### 3. Why approximate?

Because reducing the search space means we might miss the mathematically exact nearest vector.

### 4. What is Recall?

It measures how many of the correct nearest results were successfully retrieved.

### 5. What does IVF do?

```text
Vectors
  ↓
Clusters
  ↓
Find closest centroids
  ↓
Search selected clusters
```

### 6. What does HNSW do?

```text
Vectors
  ↓
Graph
  ↓
Navigate through graph
  ↓
Use multiple layers
  ↓
Find nearby vectors
```

### 7. What does PQ solve?

The **storage problem**, by compressing vectors.

---

# 30. Final Mental Model

The entire lecture can be reduced to this:

```text
                    MILLIONS OF VECTORS
                           │
                           ↓
                  Brute-force KNN
                           │
                           ↓
                    TOO EXPENSIVE
                           │
                           ↓
                         ANN
                    /            \
                   /              \
                 IVF              HNSW
                  │                │
              Clusters            Graph
                  │                │
              Centroids          Layers
                  │                │
                  └───────┬────────┘
                          ↓
                 FEWER COMPARISONS
                          ↓
                    FASTER SEARCH


              STORAGE PROBLEM
                     │
                     ↓
              Vector Compression
                     │
                     ↓
                     PQ
                     │
                     ↓
              LESS STORAGE
```

## 🎯 One sentence to remember

> **Vector databases use algorithms such as IVF and HNSW to avoid comparing a query with every vector, while techniques such as PQ help reduce the storage required for those vectors.**
