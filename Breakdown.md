# Breakdown

A technical companion to the hollow mimic thesis. This maps the philosophical arguments to the actual architecture -- what the transformer is mathematically doing when it looks like it's thinking.


## The core thesis: probabilistic determinism

Large language models have one objective function: minimize cross-entropy loss over a sequence of discrete tokens.

L = -Sum log P(x_t | x_1, ..., x_{t-1})

That equation dictates everything the model does. It doesn't solve for truth, logic, or causality. It solves for statistical likelihood.


## 1. Lateral thinking as vector proximity

What the hollow mimic thesis calls "lateral thinking" has a precise mathematical definition: cosine similarity in embedding space.

The embedding layer projects input tokens into a high dimensional vector space. "Associative" reasoning is the calculation of the angle between vectors. The classic example: vec(King) - vec(Man) + vec(Woman) ~ vec(Queen). The model hasn't understood gender or monarchy. It has traversed a linear path in the vector space. Nothing more.

The hard limit: the model can only "associate" concepts that share statistical proximity in the training distribution. It cannot form a connection that doesn't exist as a path on the learned manifold.


## 2. Pattern matching via induction heads

What appears to be logic or reasoning is, mechanistically, the operation of induction heads and copy-paste circuits.

Induction heads are specific attention heads that learn the algorithm [A] [B] ... [A] -> [B]. They scan backward through the context, find a previous occurrence of the current token, and increase the probability of the token that followed it. The model does not execute IF P THEN Q. It executes "attend to P, copy Q." Exact pattern matching, using the context window as a lookup buffer. When the pattern breaks -- the "reversal curse" is a good example -- the induction head fails to attend, and the "logic" collapses.


## 3. Feed-forward networks as key-value memories

The model's "knowledge" isn't stored as conceptual schemas. It's stored as key-value pairs in the MLP layers.

The feed-forward layers act as static hash maps. The key detects a specific pattern in the input ("The Eiffel Tower is in"). The value adds the vector for the likely completion ("Paris") to the residual stream. Encyclopedia plus autocomplete. The model retrieves static memories based on pattern triggers. It doesn't synthesize answers from first principles.


## The orchestration layer

"Higher reasoning" is an artifact of orchestration engineering -- prompt engineering, chain of thought, system prompts -- not architectural capability.

Chain of thought doesn't enable the model to think. It exploits the autoregressive property. In a single forward pass, the model can't perform multi-step computation (depth is fixed). By forcing intermediate steps ("let's think step by step"), we dump the intermediate computation into the context window. The model then reads its own previous output as new input, using it to statistically constrain the search space for the next token. Recursive input refinement, not cognition.


## The symbol grounding wall

Syntactic manipulation can't yield semantic understanding. This is Harnad's symbol grounding problem, and it's the reason abstraction stays out of reach.

LLMs operate purely on syntax: the relationship of symbol to symbol. Abstraction requires semantics: the relationship of symbol to object. The model learns P(word | context). It never learns P(word | world). Because the model has no sensory transducer to ground tokens in reality, it can't form a concept. It possesses distributional representations -- usage patterns -- which aren't enough for genuine abstraction.


## The convex hull constraint

Extrapolation fails for geometric reasons. The training data forms a shape (a manifold) in high dimensional space. The model learns the topology of this shape.

Interpolation -- navigating the space between known training points -- is what the model does when it appears creative. It generates "new" text, but the text lives within the boundaries of what it has already seen.

Extrapolation would require predicting a point outside the convex hull of the training data. Outside the manifold, the probability gradients are undefined. The model hallucinates or reverts to the mean. It can't apply a concept to a novel domain because the vector path to that domain doesn't exist in its weights.


## What this amounts to

The mechanisms map to the thesis cleanly enough. What we call cognition is induction heads and vector arithmetic. What we call reasoning is context stuffing. The model can't abstract because it operates on pure syntax with no grounding, and it can't extrapolate because it's confined to the convex hull of its training data.

I keep coming back to the same image: a stochastic parrot traversing a high dimensional manifold of memorized patterns. The parrot is impressive. It's mathematically sophisticated. It's still a parrot.
