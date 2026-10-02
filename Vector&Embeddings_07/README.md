# Vectors and Embeddings



# 1. Why Do We Need Vectors?

Computers work with **numbers**.

Suppose we want a computer to understand that two movies are similar.

Humans can say:

> "Interstellar and The Martian are similar because both are related to space."

But a computer cannot directly perform mathematical operations on the words:

```text
Interstellar
The Martian
```

So we need a way to represent information using **numbers**.

This is where **vectors** come in.

A vector allows us to represent something using an ordered collection of numbers.

---

# 2. What Is a Vector?

A vector is simply:

> **An ordered collection of numbers.**

For example:

```text
[10, 7]
```

is a vector.

Another example:

```text
[2, 10]
```

is also a vector.

The **order of the numbers matters**.

For example:

```text
[10, 7]
```

is different from:

```text
[7, 10]
```

because the positions have different meanings.

---

# 3. Vector Dimensions

Each position in a vector is called a **dimension**.

For example:

```text
[10, 7]
```

has:

```text
Dimension 1 → 10
Dimension 2 → 7
```

If we decide:

```text
Dimension 1 → Action
Dimension 2 → Comedy
```

then:

```text
[10, 7]
```

means:

```text
Action = 10
Comedy = 7
```

So this is a **2-dimensional vector**.

---

# 4. Movie Recommendation Example

Let's understand vectors using a movie recommendation system.

Suppose we want to build a simple recommendation engine.

Imagine a user watches:

```text
Avengers
```

We want to recommend movies that are similar to Avengers.

For example:

```text
Avengers → Action + Comedy
```

We can give every movie a numerical score.

---

# 5. First Attempt: One Dimension

Initially, suppose we use only one parameter:

```text
Action
```

We give every movie an action score.

For example:

```text
Avengers → 10
Batman   → 9
Hera Pheri → 2
```

Now we can compare movies based on their action score.

For example:

```text
Avengers = 10
Karate Kid = 10
```

The absolute difference is:

```text
|10 - 10| = 0
```

A smaller difference means the movies are more similar according to this parameter.

But there is a problem.

---

# 6. Problem With One Dimension

A single dimension cannot represent the complete nature of a movie.

For example:

```text
Avengers → Action = 10
Hera Pheri → Action = 2
```

They are far apart in terms of action.

But what if we want to consider:

* Action
* Comedy
* Romance
* Thriller
* Darkness
* etc.

One number is not enough.

So we add more dimensions.

---

# 7. Two-Dimensional Vectors

Now suppose we use two parameters:

```text
Dimension 1 → Action
Dimension 2 → Comedy
```

For example:

```text
Avengers → [10, 7]
Batman   → [9, 1]
```

Here:

```text
Avengers
Action = 10
Comedy = 7
```

and:

```text
Batman
Action = 9
Comedy = 1
```

Now every movie can be represented as a point in a **2-dimensional space**.

---

# 8. Visualizing Vectors

We can represent the two dimensions using an X-Y graph.

```text
              Comedy
                 ↑
                 |
           Avengers ●
                 |
                 |
                 |
                 |
                 |       ● Batman
                 |
                 +--------------------→ Action
```

Each movie gets a position based on its vector.

For example:

```text
Avengers → [10, 7]
Batman   → [9, 1]
```

---

# 9. Similarity Using Distance

Now we have a very useful idea:

> **If two points are close to each other, their vectors are similar.**

If two points are far apart:

> Their vectors are less similar.

For example:

```text
Movie A ●       ● Movie B
```

They are close.

But:

```text
Movie A ●


                         ● Movie B
```

They are far apart.

Therefore, we can use **distance** to measure similarity.

---

# 10. Euclidean Distance

The lecture uses the familiar **Euclidean distance** formula.

For two points:

```text
A = (x₁, y₁)
B = (x₂, y₂)
```

the distance is:

```text
distance = √((x₂ - x₁)² + (y₂ - y₁)²)
```

For example:

```text
Avengers → [10, 7]
Batman   → [9, 1]
```

The distance tells us how close the two movies are in the vector space.

### Important idea

```text
Smaller distance → More similar
Larger distance  → Less similar
```

---

# 11. More Dimensions

Two dimensions may still not be enough.

So we can add more:

```text
1 → Action
2 → Comedy
3 → Romance
4 → Thriller
5 → Darkness
6 → ...
```

Then a movie might be represented by:

```text
[10, 7, 2, 8, 4, 6]
```

This is a **6-dimensional vector**.

We can use the same mathematical idea of distance in higher dimensions.

We cannot visually draw more than three dimensions easily, but the mathematics still works.

---

# 12. Building a Recommendation Engine

Suppose we have decided that six dimensions are enough to represent a movie.

We can give every movie a 6-dimensional vector.

Then, when a user watches a movie:

```text
User watches → Avengers
```

we:

1. Get the vector of Avengers.
2. Compare it with the vectors of other movies.
3. Calculate their distances.
4. Find the movies with the smallest distances.
5. Recommend those movies.

Conceptually:

```text
Avengers
   ↓
Get vector
   ↓
Compare with all movie vectors
   ↓
Calculate distance
   ↓
Find closest movies
   ↓
Recommend them
```

This gives us a basic recommendation engine.

And importantly:

> We can build such a recommendation system without an LLM.

---

# 13. But There Is a Problem

Who created these vectors?

**We did.**

For example, we manually decided:

```text
Avengers → [10, 7]
Batman   → [9, 1]
```

We decided:

```text
Dimension 1 = Action
Dimension 2 = Comedy
```

This is called a **handcrafted vector**.

---

# 14. Handcrafted Vectors

A handcrafted vector is a vector whose features are manually designed by humans.

For example:

```text
Avengers → [10, 7]

10 → Action score
7  → Comedy score
```

The numbers themselves can also be subjective.

One person might give:

```text
Avengers → [10, 7]
```

while another person might give:

```text
Avengers → [9, 6]
```

The general direction may be similar, but the exact values can vary.

These are called:

> **Handcrafted vectors**

because humans manually created the representation.

---

# 15. Feature Engineering

This idea is related to **feature engineering** in traditional machine learning.

We manually decide which features are useful.

For movies, we might choose:

```text
Action
Comedy
Romance
Thriller
Darkness
...
```

Then we score every movie using those features.

The algorithm then uses those numerical features to calculate similarities.

Historically, many machine-learning systems worked this way:

```text
Human
  ↓
Choose useful features
  ↓
Create numerical representation
  ↓
Machine-learning algorithm
  ↓
Calculate similarity / make prediction
```

---

# 16. Choosing Bad Dimensions

The quality of a vector depends heavily on the dimensions we choose.

Suppose we choose:

```text
Dimension 1 → Number of characters in movie name
Dimension 2 → Release year
```

These are numerical features, but they are not necessarily useful for determining whether two movies are similar.

For example:

```text
Batman
Titanic
```

might have similar:

* title lengths
* release years

But that does not mean the movies are actually similar.

So:

> **Choosing bad dimensions can produce bad vectors and therefore bad similarity results.**

---

# 17. The Main Problem With Manual Vectors

Manual vectors work when we know exactly which features matter.

But consider **language**.

Natural language is complicated.

A word can depend on:

* Context
* Multiple meanings
* Emotions
* Sarcasm
* Sentence structure
* Surrounding words

For example, the same word can mean different things in different contexts.

So manually defining dimensions for language becomes extremely difficult.

We cannot realistically say:

```text
Dimension 1 = happiness
Dimension 2 = sarcasm
Dimension 3 = royalty
Dimension 4 = food
Dimension 5 = context
...
```

and manually assign values to every word.

Therefore, a better question is:

> **Why don't we let a model learn how to create useful vectors itself?**

---

# 18. Learned Vectors

Instead of manually creating vectors, we can give a model the task of **learning useful representations**.

The basic idea is:

```text
Human
  ↓
Give model data + objective
  ↓
Model learns useful numerical representations
```

The model starts with initial numerical values and adjusts them during training.

---

# 19. Example: King, Queen, Apple, Banana

Consider these words:

```text
King
Queen
Apple
Banana
```

Look at their contexts.

We commonly see:

```text
King → Queen
King → Royal
King → Palace
```

while:

```text
Apple → Banana
Apple → Fruit
Apple → Food
```

The contexts around `King` and `Queen` are often more related to each other.

Similarly:

```text
Apple ↔ Banana
```

often appear in related contexts.

So we would like the learned representation to reflect these relationships.

Conceptually:

```text
King      ●
Queen     ●


Apple             ●
Banana            ●
```

The exact dimensions are not manually designed by us.

---

# 20. Context Helps the Model Learn

The model sees many examples of language.

It observes which words appear in similar contexts.

For example:

```text
The king ruled the kingdom.
The queen ruled the kingdom.
```

and:

```text
I ate an apple.
I ate a banana.
```

From many such examples, the model can learn that:

```text
King ↔ Queen
```

are related in one way, while:

```text
Apple ↔ Banana
```

are related in another way.

The important idea is:

> **The model learns relationships from patterns in the data rather than us manually defining every feature.**

---

# 21. Initial Random Vectors

Now comes an important question:

> How does the model create the vectors in the first place?

Initially, we can give the model **random numerical vectors**.

For example:

```text
King   → [0.91, -0.60, 0.80, -0.77]
Queen  → [0.20, 0.70, -0.30, 0.10]
Apple  → [-0.40, 0.30, 0.60, -0.20]
Banana → [0.10, -0.50, 0.40, 0.70]
```

At this point, these numbers do not necessarily have useful semantic meaning.

They are simply initial numerical values.

---

# 22. Give the Model a Goal

Now we give the model an objective.

For example:

> Given a word, identify another related word / learn useful relationships between words.

The model uses the vectors to calculate similarities.

Initially, its results may be poor.

So we calculate an **error/loss**.

---

# 23. Training Process

The training process can be simplified as:

```text
Start with initial vectors
        ↓
Give input
        ↓
Make prediction
        ↓
Compare prediction with expected result
        ↓
Calculate error / loss
        ↓
Adjust parameters
        ↓
Try again
        ↓
Repeat many times
```

The model repeatedly adjusts its parameters.

This can happen:

```text
Millions
or
Billions
of times
```

depending on the training process.

---

# 24. What Happens to the Vectors?

Initially:

```text
King   → random vector
Queen  → random vector
Apple  → random vector
Banana → random vector
```

After repeated training:

```text
King   → learned vector
Queen  → learned vector
Apple  → learned vector
Banana → learned vector
```

The numerical values have been adjusted so that the representations become useful for the model's objective.

---

# 25. Important Point: Dimensions Don't Have Human-Readable Meanings

This is extremely important.

When we manually created movie vectors, we said:

```text
Dimension 1 → Action
Dimension 2 → Comedy
```

But with learned vectors, we generally **cannot say**:

```text
Dimension 1 = Royalty
Dimension 2 = Richness
Dimension 3 = Food
```

The dimensions are not manually assigned human concepts in this way.

The model learns numerical parameters that work together to represent information.

So if:

```text
King → [0.21, 0.73, -0.12, ...]
```

we should not assume:

```text
0.21 = royalty
0.73 = richness
-0.12 = gender
```

The individual dimensions may not have such simple human-interpretable meanings.

---

# 26. Why Must Vectors Have the Same Dimensions?

To compare two vectors, their dimensions need to correspond.

For example:

```text
King → [a, b, c]
Queen → [x, y, z]
```

Both have three dimensions.

We can compare:

```text
a ↔ x
b ↔ y
c ↔ z
```

But suppose:

```text
King → [Richness, Royalty]
Banana → [Food, Fruit]
```

The dimensions don't represent the same coordinate system.

Comparing them directly would not make meaningful sense.

Therefore, learned representations are created in a **common vector space**.

---

# 27. Vector Space

A **vector space** is the mathematical space in which vectors exist and can be compared.

In a 2-dimensional example:

```text
          Y
          ↑
          |
          |
          |
----------+----------→ X
          |
          |
```

A vector can be represented as a point in this space.

For high-dimensional embeddings, we cannot visually draw all dimensions, but mathematically the same concept applies.

---

# 28. Measuring Similarity

Once we have vectors, we need a way to determine how similar they are.

The lecture discusses three important approaches:

1. Euclidean Distance
2. Cosine Similarity
3. Dot Product

---

# 29. Euclidean Distance

Euclidean distance measures the straight-line distance between two vectors/points.

For two 2D points:

```text
A = (x₁, y₁)
B = (x₂, y₂)
```

the formula is:

```text
d(A,B) = √((x₂ - x₁)² + (y₂ - y₁)²)
```

Interpretation:

```text
Small distance → Similar
Large distance → Different
```

The same idea can be extended to higher-dimensional vectors.

---

# 30. Cosine Similarity

Cosine similarity focuses on the **direction/angle** between two vectors.

The formula is:

```text
cosine_similarity(A, B)
=
(A · B)
----------------
|A| × |B|
```

where:

```text
A · B = dot product of A and B

|A| = magnitude of A

|B| = magnitude of B
```

The lecture focuses more on the intuition than the mathematical derivation.

---

# 31. Cosine Similarity Intuition

Think about two vectors pointing in different directions.

### Same direction

```text
→
→
```

High similarity.

Cosine similarity:

```text
+1
```

### Unrelated / perpendicular direction

```text
↑
→
```

Similarity is around:

```text
0
```

### Opposite direction

```text
→
←
```

Cosine similarity:

```text
-1
```

Therefore:

```text
Cosine Similarity Range:

-1  ←────────── 0 ──────────→  +1

Opposite       Unrelated       Same
direction      direction       direction
```

---

# 32. Why Cosine Similarity Is Useful

Cosine similarity focuses on the **direction** of vectors.

This can be useful when we care about the pattern/relationship represented by a vector rather than simply its absolute size.

The lecture presents cosine similarity as one of the major ways of comparing vectors.

---

# 33. Dot Product

Another method is the **dot product**.

For two vectors:

```text
A = [a₁, a₂, a₃]
B = [b₁, b₂, b₃]
```

the dot product is:

```text
A · B
=
a₁b₁ + a₂b₂ + a₃b₃
```

The lecture's intuition is that dot product takes into account both:

* The angle/direction between vectors
* Their magnitude

So unlike cosine similarity, which normalizes by magnitude, dot product considers magnitude as well.

---

# 34. Three Ways to Compare Vectors

The lecture introduces:

| Method             | Main idea                         |
| ------------------ | --------------------------------- |
| Euclidean Distance | How far apart are the vectors?    |
| Cosine Similarity  | How similar are their directions? |
| Dot Product        | Considers direction and magnitude |

Each can have different advantages depending on the use case.

---

# 35. Why We Move From Handcrafted to Learned Vectors

Handcrafted vectors have a major limitation.

We have to decide:

```text
What dimensions should exist?
```

For movies, this may be manageable:

```text
Action
Comedy
Romance
Thriller
...
```

But for natural language, this becomes extremely difficult.

Language has:

* Context
* Ambiguity
* Sarcasm
* Emotions
* Multiple meanings
* Complex relationships

Therefore:

```text
Manual feature design
        ↓
Becomes extremely difficult
        ↓
Let the model learn the representation
```

---

# 36. From Learned Vectors to Embeddings

A learned vector representation is commonly referred to as an **embedding**.

The lecture describes an embedding model as a function:

```text
Input
  ↓
Embedding Model
  ↓
Vector
```

For example:

```text
"king"
   ↓
Embedding Model
   ↓
[0.21, 0.73, -0.12, ...]
```

---

# 37. What Is an Embedding Model?

An embedding model can be thought of as a function:

```text
Embedding Model(input) → vector
```

For example:

```text
Embedding Model("king")
        ↓
[0.21, 0.73, -0.12, ...]
```

It can take different types of input depending on the model.

For example:

```text
Word
Sentence
Document
Image
...
```

and produce a vector representation.

---

# 38. Embedding Dimensions

The embedding model has a fixed output dimensionality.

For example, suppose an embedding model produces:

```text
1024-dimensional vectors
```

Then:

```text
"king"
   ↓
Embedding Model
   ↓
1024-dimensional vector
```

Similarly:

```text
"King is a ruler."
   ↓
Embedding Model
   ↓
1024-dimensional vector
```

The exact number of dimensions depends on the embedding model.

---

# 39. Latent Vectors

The lecture also refers to these learned representations as **latent vectors**.

Here, **latent** means:

> **Hidden**

The dimensions are not necessarily directly understandable to humans.

The model learns internal numerical representations that are useful for measuring relationships.

---

# 40. Embeddings Are Not an LLM Invention

Vectors and embeddings existed before modern LLMs such as:

* ChatGPT
* Claude
* Gemini

They have been used in machine learning for a long time.

For example:

```text
Recommendation systems
Search systems
Similarity systems
Natural language processing
```

can use vector representations.

LLMs also make extensive use of vector representations.

---

# 41. How LLMs Use Vectors

An LLM has a vocabulary containing many tokens.

When processing text, tokens are represented numerically.

Conceptually:

```text
Text
 ↓
Tokens
 ↓
Numerical representations / vectors
 ↓
Model processing
 ↓
Prediction
```

The model uses learned representations as part of its processing.

---

# 42. Next-Token Prediction

One important job of an LLM is to predict the next token.

Suppose the input is:

```text
The capital of India is
```

The model considers possible next tokens.

Conceptually:

```text
Delhi
Mumbai
Kolkata
London
...
```

The model computes probabilities for possible next tokens.

The token with a higher probability is more likely to be selected.

---

# 43. Similarity and Prediction

The lecture gives the intuition that the model works with vector representations and compares representations as part of its prediction process.

Conceptually:

```text
Input context
      ↓
Vector representations
      ↓
Model processing
      ↓
Possible token representations
      ↓
Scores / probabilities
      ↓
Next token
```

The important point is that numerical/vector representations allow mathematical operations to be performed by the neural network.

---

# 44. Token ID vs Vector

It is important not to confuse a **token ID** with an **embedding vector**.

A token might have:

```text
Token:

"king"
```

and an ID:

```text
king → 1523
```

The ID is simply an identifier/index.

The model then uses its embedding representation:

```text
1523
 ↓
Embedding lookup
 ↓
[0.21, 0.73, -0.12, ...]
```

The numbers in the embedding are learned during training.

---

# 45. Training vs User Input

For a pretrained model, the learning process happens before normal user interaction.

### Training

```text
Initial parameters
      ↓
Training data
      ↓
Prediction
      ↓
Loss/Error
      ↓
Parameter updates
      ↓
Repeat many times
      ↓
Trained model
```

### User input / Inference

```text
User input
      ↓
Tokenization
      ↓
Token IDs
      ↓
Learned representations
      ↓
Trained model
      ↓
Prediction
```

The model normally does **not** start from random vectors and retrain itself every time a user sends a message.

---

# 46. Complete Learning Process

The overall concept can be summarized as:

```text
                 TRAINING

Training Data
     ↓
   Tokens
     ↓
 Token IDs
     ↓
Initial numerical representations
     ↓
   Model
     ↓
 Prediction
     ↓
 Loss / Error
     ↓
Parameter Updates
     ↓
Repeat millions/billions of times
     ↓
Learned Parameters
     ↓
Learned Embeddings
```

Then:

```text
                 INFERENCE

User Input
     ↓
Tokenization
     ↓
Token IDs
     ↓
Learned Embeddings / Representations
     ↓
Trained Model
     ↓
Prediction
     ↓
Output
```

---

# 47. Handcrafted vs Learned Vectors

| Handcrafted Vectors                   | Learned Vectors                                    |
| ------------------------------------- | -------------------------------------------------- |
| Humans choose the dimensions          | Model learns useful representations                |
| Humans assign values                  | Values are adjusted during training                |
| Dimensions can have explicit meanings | Individual dimensions may not have simple meanings |
| Requires manual feature engineering   | Learns from data                                   |
| Difficult for complex language        | Better suited to complex patterns                  |
| Example: Action, Comedy               | Learned numerical representation                   |

---

# 48. The Evolution of the Idea

The lecture can be understood as a progression:

```text
Manual numbers
      ↓
Vectors
      ↓
Vector space
      ↓
Distance / similarity
      ↓
Handcrafted vectors
      ↓
Problem with manual features
      ↓
Learned vectors
      ↓
Embeddings
      ↓
Embedding models
      ↓
LLMs use vector representations
```

---

# 49. The Most Important Concepts

## Vector

An ordered collection of numbers.

```text
[10, 7]
```

---

## Dimension

A position/coordinate in a vector.

```text
[10, 7]

10 → Dimension 1
7  → Dimension 2
```

---

## Vector Space

The mathematical space in which vectors are represented and compared.

---

## Similarity

A measurement of how related two vectors are.

Common methods:

```text
Euclidean Distance
Cosine Similarity
Dot Product
```

---

## Handcrafted Vector

A vector manually designed by humans.

```text
Movie → [Action, Comedy]
```

---

## Learned Vector

A vector whose values are learned/adjusted by a model during training.

---

## Embedding

A learned numerical/vector representation of an input.

---

## Embedding Model

A model/function that converts an input into a vector.

```text
Input
  ↓
Embedding Model
  ↓
Vector
```

---

## Latent Vector

A hidden learned vector representation.

---

# 50. Final Mental Model

Remember the entire lecture using this flow:

```text
                    INFORMATION
                         ↓
                 Represent as numbers
                         ↓
                      VECTOR
                         ↓
              Put vectors in a space
                         ↓
               Compare the vectors
                         ↓
        ┌────────────────┼────────────────┐
        ↓                ↓                ↓
   Euclidean          Cosine          Dot Product
    Distance         Similarity
        └────────────────┼────────────────┘
                         ↓
                  Measure similarity
                         ↓
              ┌──────────┴──────────┐
              ↓                     ↓
       Handcrafted              Learned
        Vectors                  Vectors
              ↓                     ↓
      Human chooses           Model learns
       dimensions             representation
                                    ↓
                               EMBEDDINGS
                                    ↓
                             Embedding Model
                                    ↓
                              Vector Output
                                    ↓
                               AI / LLM Apps
```

---

# 51. One-Sentence Summary

> **Vectors convert information into numbers so computers can mathematically compare it; handcrafted vectors are manually designed, while embeddings are learned vector representations produced through training, and modern AI/LLM systems use these numerical representations to process and compare information.**

---

# 52. Quick Revision

```text
Q: What is a vector?
A: An ordered collection of numbers.

Q: What is a dimension?
A: A position/coordinate inside a vector.

Q: Why do we use vectors?
A: To represent information numerically so mathematical operations can be performed.

Q: How can vectors be compared?
A: Using Euclidean distance, cosine similarity, or dot product.

Q: What is a handcrafted vector?
A: A vector manually designed using human-selected features.

Q: What is the problem with handcrafted vectors?
A: Humans must manually decide useful features, which becomes very difficult for complex data such as natural language.

Q: What is a learned vector?
A: A vector whose numerical values are learned/adjusted during model training.

Q: What is an embedding?
A: A learned vector representation of an input.

Q: What is an embedding model?
A: A model/function that converts an input into a vector.

Q: What does latent mean here?
A: Hidden.

Q: Do embedding dimensions necessarily have human-readable meanings?
A: No. Learned dimensions generally should not be interpreted as simple manually assigned concepts.

Q: Does the token ID itself contain meaning?
A: No. It is an identifier/index used to locate the token's representation.

Q: When are the embedding values learned?
A: During model training, through parameter updates based on the training objective.

Q: Does the model normally retrain its embeddings every time a user sends a message?
A: No. A pretrained model uses its already-learned parameters during normal inference.
```

---

# 53. Final Takeaway

The central idea of the lecture is:

```text
Human-readable information
          ↓
       Numbers
          ↓
       Vectors
          ↓
   Mathematical Space
          ↓
     Similarity
          ↓
   Useful Representation
          ↓
       Embeddings
          ↓
      AI Systems
```

The major shift is:

```text
OLD APPROACH

Human chooses features
        ↓
Human creates vectors
        ↓
Algorithm compares vectors


LEARNED APPROACH

Give data + objective to model
        ↓
Model starts with initial parameters
        ↓
Training adjusts parameters
        ↓
Model learns useful vector representations
        ↓
Embeddings
        ↓
Use them in AI applications
```

That is the fundamental idea behind **vectors and embeddings**.
