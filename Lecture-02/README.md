# How Large Language Models (LLMs) Work

## Overview

A Large Language Model (LLM) can be understood fundamentally as a system that:

> **Given the previous tokens, predicts the probability distribution of the next token.**

The complete simplified pipeline is:

```text
Text
 ↓
Tokenization
 ↓
Token IDs
 ↓
Embeddings
 ↓
Positional Information
 ↓
Transformer Layers
 ↓
Self-Attention (Q, K, V)
 ↓
Final Contextual Representation
 ↓
Logits
 ↓
Softmax
 ↓
Probability Distribution
 ↓
Sampling / Greedy Decoding
 ↓
Next Token
 ↓
Repeat
```

---

# 1. Next-Token Prediction

The fundamental task of an LLM is **next-token prediction**.

For example:

```text
The capital of India is ___
```

The model needs to determine what token is likely to come next.

Possible candidates could be:

```text
Delhi
Mumbai
Kolkata
...
```

The model doesn't simply memorize one answer. It calculates scores/probabilities for possible next tokens based on the context.

Therefore:

```text
Previous tokens
      ↓
Understand context
      ↓
Predict next token
```

After generating the next token, that token becomes part of the context for the next prediction.

```text
The capital of India is
             ↓
           Delhi
             ↓
The capital of India is Delhi
             ↓
            ...
```

This process continues token by token.

---

# 2. Why Does Text Need to Become Numbers?

Neural networks perform mathematical operations.

They cannot directly perform matrix multiplication, attention calculations, etc. on raw text such as:

```text
"India"
"Delhi"
"capital"
```

Therefore, text must first be converted into numerical representations.

The first step is **tokenization**.

---

# 3. Tokenization

A **tokenizer** converts text into smaller units called **tokens**.

For example:

```text
"I love programming"
```

could be tokenized into something like:

```text
"I"
" love"
" programming"
```

The exact tokenization depends on the tokenizer and model.

A token does **not necessarily equal a complete word**.

A word can be divided into multiple tokens:

```text
programming
    ↓
program + ming
```

The exact split is determined by the tokenizer.

---

# 4. Token vs Token ID

These are different concepts.

### Token

The actual piece of text:

```text
Delhi
```

### Token ID

The numerical identifier assigned to that token:

```text
Delhi → 15342
```

The actual number is model-specific.

The important point is:

> **A token ID is an identifier, not a representation of meaning.**

For example:

```text
Delhi → 15342
Mumbai → 8217
```

This does **not** mean that Mumbai is somehow mathematically closer to Delhi because their IDs are close.

The IDs are simply used to identify entries in the model's vocabulary.

---

# 5. Why Not Make Every Word a Token?

If every possible word were assigned its own token, the vocabulary could become extremely large.

Consider:

```text
automate
automatic
automation
automatically
```

Instead of storing every possible word separately, tokenizers can use reusable pieces.

Conceptually:

```text
auto
matic
ation
```

These pieces can be reused across many different words.

This gives us a balance between:

```text
Word-level tokenization
→ huge vocabulary
→ shorter sequences
```

and:

```text
Character-level tokenization
→ tiny vocabulary
→ extremely long sequences
```

Modern tokenizers generally use a middle-ground approach based on subword/token pieces.

---

# 6. Why Not Use Characters Directly?

Character-level tokenization could use:

```text
A
B
C
...
Z
```

This would produce a very small vocabulary.

However, a sentence would require many tokens.

For example:

```text
Hello world
```

would require approximately one token per character or character-like unit.

Long documents would therefore contain enormous numbers of tokens.

So tokenization attempts to maintain a reasonable balance between:

* Vocabulary size
* Sequence length

---

# 7. Embeddings

Token IDs themselves don't contain semantic meaning.

Therefore, the model maps token IDs into **vectors**.

For example:

```text
Delhi
  ↓
Token ID
  ↓
Embedding
  ↓
[0.23, -0.71, 0.42, ...]
```

This vector is called an **embedding**.

An embedding provides a numerical representation that the neural network can process.

Conceptually, related concepts can have related representations.

For example:

```text
King
Queen
Prince
```

may learn representations that capture relationships related to:

* royalty
* leadership
* gender
* etc.

The actual embedding dimensions and learned meanings are much more complex than this simplified representation.

---

# 8. Tokenization vs Embedding

These two steps solve different problems.

### Tokenization

Answers:

> **What pieces of text are we processing?**

```text
Text
 ↓
Tokens
```

### Embedding

Answers:

> **How do we represent those tokens numerically?**

```text
Token ID
 ↓
Vector
```

Therefore:

```text
Text
 ↓
Tokenizer
 ↓
Tokens
 ↓
Token IDs
 ↓
Embeddings
```

Tokenization and embedding are **not the same thing**.

---

# 9. Why Position Matters

Consider:

```text
Dog bites man.
```

and:

```text
Man bites dog.
```

Both contain the same words, but their order is different.

Therefore, the model needs information about where tokens occur in the sequence.

Conceptually:

```text
Token identity
+
Token position
=
Better understanding of the sequence
```

This is why Transformers need a mechanism for representing positional information.

---

# 10. Context Matters

Consider:

```text
I deposited money at the bank.
```

and:

```text
I sat beside the bank of the river.
```

The token:

```text
bank
```

is the same.

But its meaning depends on the surrounding context.

Therefore, an LLM needs to understand relationships between tokens.

This is one of the major reasons **self-attention** is important.

---

# 11. Self-Attention

Self-attention allows tokens in a sequence to examine relationships with other tokens in that same sequence.

Consider:

```text
The cat sat on the mat.
```

When processing:

```text
on
```

the model can determine which other tokens are relevant.

Conceptually:

```text
on → the     low relevance
on → cat     some relevance
on → sat     relevance
on → mat     high relevance
```

The model then uses these relationships to create a better contextual representation.

---

# 12. Query, Key, and Value

Self-attention uses three vectors for each token:

```text
Q = Query
K = Key
V = Value
```

Conceptually:

```text
Token
 ├── Query
 ├── Key
 └── Value
```

These are learned transformations of the token representation.

---

## Query

The Query represents:

> **What information am I looking for?**

A token's Query is compared against other tokens' Keys.

---

## Key

The Key represents:

> **What kind of information do I provide?**

Each token has its own Key.

The Query and Key are compared to determine relevance.

---

## Value

The Value represents:

> **What information should actually be contributed?**

Once the model determines which tokens are important, their Value vectors are combined according to their attention weights.

---

# 13. How Q, K, and V Work Together

Suppose we have:

```text
Q(on)
```

and compare it with:

```text
K(the)
K(cat)
K(sat)
K(mat)
```

The model obtains attention scores.

Conceptually:

```text
Q(on) · K(the) → low
Q(on) · K(cat) → medium
Q(on) · K(sat) → medium
Q(on) · K(mat) → high
```

The actual Transformer uses scaled dot-product attention.

The scores are converted into attention weights, which are then used to combine the Value vectors.

Conceptually:

```text
New representation of "on"

= weight₁ × V(the)
+ weight₂ × V(cat)
+ weight₃ × V(sat)
+ weight₄ × V(mat)
```

Therefore, the representation of `on` now contains information from its surrounding context.

---

# 14. Why Is It Called Self-Attention?

It is called **self-attention** because the tokens in a sequence attend to other tokens within the same sequence.

For example:

```text
The
cat
sat
on
the
mat
```

can all establish relationships with one another.

Conceptually:

```text
The ↔ cat
cat ↔ sat
sat ↔ on
on  ↔ mat
...
```

The sequence is essentially using itself to determine contextual relationships.

---

# 15. Why Q, K, and V Are Separate

Using three different representations allows the model to separate three roles:

```text
Query → What am I looking for?

Key   → What information do I offer?

Value → What information should I contribute?
```

This gives the attention mechanism flexibility to determine:

1. What a token needs.
2. Which tokens provide relevant information.
3. What information should actually be passed forward.

---

# 16. Transformer Layers

Self-attention is not the entire LLM.

It is an important component of the **Transformer architecture**.

A Transformer contains multiple layers.

Simplified:

```text
Input
 ↓
Transformer Layer 1
 ↓
Transformer Layer 2
 ↓
Transformer Layer 3
 ↓
...
 ↓
Final Transformer Layer
```

Each layer progressively transforms the representations.

The model can therefore build increasingly complex contextual relationships.

---

# 17. A Simplified View of a Transformer Layer

Conceptually:

```text
Input representations
        ↓
Self-Attention
        ↓
Contextual information
        ↓
Other neural-network operations
        ↓
New representations
```

Real Transformer blocks contain more components, including:

* Multi-head attention
* Residual connections
* Layer normalization
* Feed-forward networks

So a Transformer should not be thought of as only Q/K/V.

---

# 18. Why Multiple Layers?

One attention operation can capture relationships between tokens.

Multiple layers allow the model to build more complex representations.

Conceptually:

```text
Layer 1
↓
Basic relationships

Layer 2
↓
More complex relationships

Layer 3
↓
Higher-level contextual relationships

...

Final Layer
↓
Rich contextual representation
```

By the final layers, each token's representation can contain information influenced by the broader context.

---

# 19. Predicting the Next Token

Consider:

```text
The capital of India is
```

After processing the sequence through the Transformer layers, the model has a contextual representation for the current position.

The model then needs to determine:

> **Which token should come next?**

Suppose the vocabulary contains:

```text
Delhi
Mumbai
Kolkata
India
London
...
```

The model produces a score for each possible token.

---

# 20. Logits

These raw scores are called **logits**.

For example:

```text
Delhi    → 12.1
Kolkata  → 8.1
Mumbai   → 5.4
London   → 2.3
...
```

These are **not probabilities yet**.

They are raw numerical scores.

Generally:

```text
Higher logit
→ stronger preference

Lower logit
→ weaker preference
```

---

# 21. Softmax

The logits are converted into a probability distribution using **softmax**.

Conceptually:

```text
Logits
  ↓
Softmax
  ↓
Probabilities
```

For example:

```text
Delhi     → 90%
Kolkata   → 5%
Mumbai    → 3%
Others    → 2%
```

The probabilities sum to:

```text
100%
```

Softmax therefore allows the model to express how likely each possible next token is.

---

# 22. Where Do These Scores Come From?

The model is not manually programmed with rules such as:

```text
If:
capital + India + is

Then:
Delhi
```

Instead, these behaviors are learned during training.

During training, the model processes huge amounts of text and adjusts its parameters to improve its predictions.

Conceptually:

```text
Training Data
     ↓
Prediction
     ↓
Compare prediction with target
     ↓
Calculate error
     ↓
Update parameters
     ↓
Repeat
```

Over training, the model learns statistical patterns and relationships in the data.

---

# 23. Training vs Inference

### Training

The model's parameters are updated.

```text
Data
 ↓
Prediction
 ↓
Error
 ↓
Weight update
 ↓
Repeat
```

### Inference

The trained model is used to generate output.

```text
Input
 ↓
Transformer
 ↓
Probability distribution
 ↓
Next token
 ↓
Repeat
```

During normal generation, the main process is inference.

---

# 24. The Model Generates One Token at a Time

The model doesn't simply produce the entire paragraph in one operation.

Instead:

```text
Input
 ↓
Predict token 1
 ↓
Append token 1
 ↓
Predict token 2
 ↓
Append token 2
 ↓
Predict token 3
 ↓
...
```

For example:

```text
The capital of India is
        ↓
      Delhi
```

Then:

```text
The capital of India is Delhi
        ↓
        .
```

Then generation continues.

This is an **autoregressive generation process**.

---

# 25. Greedy Decoding

Once we have probabilities, one option is to always choose the highest-probability token.

For example:

```text
A → 70%
B → 20%
C → 10%
```

Greedy decoding chooses:

```text
A
```

every time.

Therefore:

```text
Same input
+
Greedy decoding
=
Same next-token choice
```

This approach is simple but can produce repetitive or less diverse outputs.

---

# 26. Sampling

Instead of always choosing the highest-probability token, we can sample from the probability distribution.

Suppose:

```text
I'm fine       → 50%
I'm doing well → 40%
Great          → 5%
Others         → 5%
```

Imagine 100 possible slots:

```text
1–50   → I'm fine
51–90  → I'm doing well
91–95  → Great
96–100 → Others
```

A random selection is made.

Therefore:

```text
I'm fine
```

is still the most likely result, but:

```text
I'm doing well
Great
```

can also be selected.

This allows more variation in generated responses.

---

# 27. Sampling vs Greedy Decoding

### Greedy

```text
Always choose:
highest probability
```

Result:

```text
More deterministic
Less variation
```

### Sampling

```text
Choose according to:
probability distribution
```

Result:

```text
More variation
More possible outputs
```

---

# 28. Temperature

Temperature modifies the logits before the softmax operation.

The simplified formula is:

```text
Adjusted Logit = Logit / Temperature
```

Then softmax is applied.

---

# 29. Temperature = 1

Suppose the logits are:

```text
5
4
3
2
1
```

At:

```text
Temperature = 1
```

we get:

```text
5/1 = 5
4/1 = 4
3/1 = 3
2/1 = 2
1/1 = 1
```

So the logits remain unchanged.

---

# 30. Lower Temperature

Suppose:

```text
Temperature = 0.5
```

Then:

```text
5/0.5 = 10
4/0.5 = 8
3/0.5 = 6
2/0.5 = 4
1/0.5 = 2
```

The differences become larger.

After softmax, the highest-scoring token becomes more dominant.

Therefore:

```text
Lower temperature
        ↓
Sharper distribution
        ↓
Higher-probability tokens dominate
        ↓
More predictable output
```

---

# 31. Higher Temperature

Suppose:

```text
Temperature = 2
```

Then:

```text
5/2 = 2.5
4/2 = 2
3/2 = 1.5
2/2 = 1
1/2 = 0.5
```

The logits become closer together.

After softmax:

```text
Probability distribution becomes flatter
```

Therefore:

```text
Higher temperature
        ↓
Flatter distribution
        ↓
Lower-probability tokens get more opportunity
        ↓
More varied output
```

---

# 32. Temperature Summary

```text
LOW TEMPERATURE
↓
Sharper probabilities
↓
More predictable
↓
Less variation
```

```text
HIGH TEMPERATURE
↓
Flatter probabilities
↓
More variation
↓
More possible token choices
```

Temperature is therefore not literally a "creativity" variable.

Technically, it changes the shape of the probability distribution used during token selection.

---

# 33. Important: Sampling Can Still Produce Different Answers at Temperature 1

Even if:

```text
Temperature = 1
```

sampling can still produce different outputs.

Why?

Because sampling itself involves probabilistic selection.

Temperature changes the distribution, while sampling determines how a token is selected from that distribution.

So:

```text
Sampling
+
Temperature
```

are related but different concepts.

---

# 34. Complete LLM Pipeline

The complete simplified process is:

```text
                    TEXT
                      │
                      ▼
                 TOKENIZER
                      │
                      ▼
                    TOKENS
                      │
                      ▼
                  TOKEN IDs
                      │
                      ▼
                 EMBEDDINGS
                      │
                      ▼
           POSITIONAL INFORMATION
                      │
                      ▼
          ┌───────────────────────┐
          │   TRANSFORMER LAYER   │
          │                       │
          │  Query ─┐             │
          │         ├─ Attention  │
          │  Key ───┘             │
          │                       │
          │  Value → Information │
          └───────────┬───────────┘
                      │
                      ▼
                 More Layers
                      │
                      ▼
           FINAL CONTEXT VECTOR
                      │
                      ▼
                    LOGITS
                      │
                      ▼
                   SOFTMAX
                      │
                      ▼
            PROBABILITY DISTRIBUTION
                      │
                      ▼
          SAMPLING / GREEDY DECODING
                      │
                      ▼
                 NEXT TOKEN
                      │
                      ▼
               APPEND TOKEN
                      │
                      ▼
                   REPEAT
```

---

# 35. The Three Most Important Numerical Representations

It is important not to confuse these three.

## 1. Token ID

```text
Delhi → 15342
```

This is an identifier.

---

## 2. Embedding

```text
15342
  ↓
[0.23, -0.71, 0.42, ...]
```

This is a vector representation used by the neural network.

---

## 3. Final Logits

After Transformer processing:

```text
Contextual representation
        ↓
Vocabulary scores
        ↓
Delhi    → 12.3
Mumbai   → 8.1
Kolkata  → 6.4
...
```

Then:

```text
Logits
 ↓
Softmax
 ↓
Probabilities
```

---

# 36. Key Concepts at a Glance

| Concept             | Purpose                                                   |
| ------------------- | --------------------------------------------------------- |
| **Tokenizer**       | Converts text into tokens                                 |
| **Token**           | A piece of text processed by the model                    |
| **Token ID**        | Numerical identifier for a token                          |
| **Embedding**       | Vector representation of a token                          |
| **Position**        | Represents token order/location                           |
| **Query**           | What information a token is looking for                   |
| **Key**             | What information a token offers                           |
| **Value**           | Information contributed by a token                        |
| **Self-Attention**  | Determines relationships between tokens                   |
| **Transformer**     | Architecture that processes contextual representations    |
| **Logits**          | Raw scores for possible next tokens                       |
| **Softmax**         | Converts logits into probabilities                        |
| **Greedy Decoding** | Always chooses the highest-probability token              |
| **Sampling**        | Selects tokens probabilistically                          |
| **Temperature**     | Controls how concentrated the probability distribution is |

---

# 37. Final Mental Model

The simplest way to understand an LLM is:

> **An LLM takes text, converts it into tokens and vectors, uses Transformer layers and self-attention to understand the relationships between those tokens, produces scores for every possible next token, converts those scores into probabilities, selects a token, adds it to the context, and repeats the process.**

In short:

```text
TEXT
 ↓
TOKENS
 ↓
VECTORS
 ↓
CONTEXT
 ↓
TRANSFORMER
 ↓
LOGITS
 ↓
PROBABILITIES
 ↓
NEXT TOKEN
 ↓
REPEAT
```

This repeated next-token prediction is the core mechanism behind autoregressive text generation in modern LLMs.
