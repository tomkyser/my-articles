# The mechanistic mirage

## What LLMs actually do when they look like they're thinking

Large language models look like they think. They solve math problems, write coherent arguments, infer patterns from a handful of examples. The natural conclusion is that something like reasoning is happening inside.

I don't think it is, and I think we can now show this mechanistically, not just philosophically.

Mechanistic interpretability treats neural networks as compiled programs that can be decompiled. Instead of arguing about whether a model "understands" arithmetic based on whether it gets the answer right, researchers have opened the hood and traced the actual circuits. What they find is consistent across multiple domains: sophisticated compression machinery that reproduces the *outputs* of abstract reasoning without performing the *process* of abstract reasoning. The model doesn't derive general principles from particulars. It optimizes high dimensional signal processing circuits that compress and reproduce the statistical structure of its training data.

This article walks through the evidence for that claim across three mechanisms: Fourier circuits in arithmetic, induction heads in sequence prediction, and linear state representations in Othello-GPT.


## 1. The Clever Hans problem

### Behavioral evidence isn't enough

AI has a Clever Hans problem. The horse that appeared to do arithmetic was actually reading his handler's micro-expressions. With LLMs, the equivalent mistake is attributing cognition based on outputs. A model that answers math questions correctly might understand arithmetic, or it might have memorized a very good lookup table. A model that predicts legal chess moves might visualize the board, or it might compute transition probabilities from token sequences.

The "stochastic parrot" critique -- that LLMs stitch together linguistic forms without reference to meaning -- captures part of this, but the debate has moved past it. Early versions focused on the absence of communicative intent. The current question is more specific: what computations actually happen between input and output? Does the model solve a + b by understanding arithmetic, or by memorizing a high dimensional lookup table? Does it predict the next game move by visualizing the board, or by computing transition probabilities based on token adjacency?

### Why we need to look inside

To tell abstraction from mimicry, we need to examine the weights, activations, and circuits that produce the behavior. That's what mechanistic interpretability does: treat neural networks not as black boxes but as compiled programs that can be decompiled into human-readable algorithms. The rest of this article examines three mechanisms that have been fully or partially reverse-engineered, each drawn from a domain where abstraction claims are strongest.


## 2. Grokking and the Fourier circuit

### The grokking phenomenon

One of the strongest-seeming cases for "emergent abstraction" is grokking. First identified by Power et al. and further analyzed by Nanda et al., it describes a peculiar training dynamic in small transformers trained on modular addition (a + b mod P).

During the first phase of training, the model achieves low training loss but high test loss -- pure memorization. Then, thousands of gradient descent steps later, the test loss drops suddenly and the model achieves perfect generalization. This looks like a phase transition from memorization to understanding. The model seems to "get it."

But get *what*, exactly?

### What the model actually computes

Nanda et al. reverse-engineered the algorithm completely. The model does not implement addition in any recognizable sense. No carrying, no counting, nothing that maps to how we think about "adding." It implements a Fourier multiplication circuit.

The embedding step. The model maps integer inputs onto trigonometric functions -- input x becomes a vector containing cos(wk x) and sin(wk x) terms, where the frequencies wk = 2pi*k/P relate to the modulus P. Numbers are treated as positions on a circle, not as quantities. The model has discovered that modular arithmetic is isomorphic to rotations in the complex plane.

The computation step. Inside the MLP layers, the model performs "addition" by exploiting trigonometric identities. The attention mechanism and MLP weights are organized to compute cos(a+b) by multiplying the sine and cosine components of the individual inputs a and b. The neural activations correspond to these product terms literally.

The output step. The unembedding matrix sums these frequency components to produce the answer. At the correct output c = a + b, the waves for all learned frequencies align (constructive interference), producing a large logit. At wrong answers, the waves cancel out (destructive interference), producing small logits.

### This is signal processing, not arithmetic

The model found a general rule that works on unseen data. But the rule is a spectral decomposition of periodic data using linear algebra and simple nonlinearities.

Zhong et al. showed this convergence isn't even reliable. Depending on initialization and hyperparameters, models sometimes find the clean "Clock" algorithm (the Fourier circuit) and sometimes settle for a messy "Pizza" algorithm that generalizes imperfectly. The model isn't searching for mathematical truth. It's sliding down a loss landscape, and the Fourier transform happens to be an efficient basin of attraction for periodic data.

The model groks the periodicity, not the mathematics. Change the task to non-periodic arithmetic -- standard addition without a modulus -- and this mechanism breaks. The "abstraction" lives only within the strict geometry of the embedding space. It mimics addition using wave interference.

| Component | How humans do addition | How the model does it | What this tells us |
| :---- | :---- | :---- | :---- |
| Representation | Numbers as quantities (magnitude) | Frequency/phase on a circle (cos(wk x)) | The model treats numbers as rotational coordinates, not quantities |
| Operation | Logical operation (counting, carrying) | Trigonometric identity (cos(a+b) = ...) | The "rule" is a geometric coincidence, not a logical derivation |
| Output | Derivation of a unique result | Constructive interference of waves | The answer emerges from signal overlap, mimicking the result without the process |
| Robustness | Generalizes to any number system | Fails if periodicity (P) is removed or changed | The mechanism is bound to the structural properties of the training data |


## 3. Induction heads

### In-context learning looks like reasoning

LLMs exhibit in-context learning: the ability to adapt to a new task described in the prompt without updating weights. Show a model "A->1, B->2, C->?" and it predicts "3." This looks like meta-learning, the ability to abstract the relationship "letter to corresponding number."

Mechanistic interpretability has identified the circuit responsible: the induction head. It's not reasoning. It's pattern matching.

### How induction heads work

An induction head isn't a single logical unit. It's a composition of two attention heads working in sequence across layers.

1. The previous token head (early layer) attends to the token at position t-1, moving information from the previous token into the current token's residual stream. Function: "What came just before?"

2. The induction head (later layer) uses that output as a query, scanning the context for a token matching the current one. When it finds a match at position k, it shifts attention to position k+1 -- the token that *followed* the match -- and copies its value forward. Function: "Last time I saw 'A', it was followed by 'B'. Predict 'B'."

### Copy-paste, not inference

The development of induction heads correlates with a phase change in training loss -- a sudden bump where the model briefly gets worse before improving sharply. The model "discovers" the induction mechanism as a global strategy for minimizing entropy.

But what the mechanism actually does is K-nearest-neighbor lookup in the sequence history. It copies.

When an LLM produces a chain of thought, induction heads maintain coherence by replicating the *structure* of deductive patterns found in the context or training data. They don't evaluate the logic. They copy the syntactic form of deduction.

The limitation shows when real extrapolation is needed. Bietti et al. demonstrated that on synthetic tasks, models learn n-gram statistics first (local memorization), then induction heads (long-range copying). But they rarely transition to a genuinely general rule if it requires manipulating data in ways not already instantiated in the context window. An induction head can retrieve a correlation. It cannot infer a new causal relationship.

The work of the induction head is associative recall. It creates the appearance of understanding a sequence by identifying and repeating its constituent sub-sequences. The model parrots its own context.


## 4. Othello-GPT and "world models"

### The best challenge to the mimicry thesis

The Othello-GPT experiments push back harder on this argument than anything else I've found. Li et al. trained a transformer on text records of Othello games. The model never saw a board, only move sequences like "C4 E3 F5." It learned to predict legal moves with high accuracy.

Li et al. then trained probes -- classifiers that take the model's internal activations as input -- and claimed to recover the board state (which piece is on which square). They argued this was evidence of an emergent, nonlinear world model.

### What the model actually represents

Nanda et al. took apart the "nonlinear" claim. The board state wasn't encoded in some complex manifold. It was in a linear subspace.

The more interesting finding: the model didn't represent the board in absolute terms (Black vs. White). It used a relative frame -- "My Color" vs. "Opponent's Color." Since the model plays both sides, predicting moves regardless of whose turn it is, this frame is the most efficient way to compress the game logic.

### Intervention proves it's causal. But causal isn't the same as understanding.

Nanda proved the representation was causally active, not just a correlation the probe detected. By identifying the "My Color" direction in activation space for a specific square, then manually injecting a vector to flip its value, he changed the model's predictions. It started predicting moves that would only be legal if that square were occupied by its own piece.

So the model genuinely tracks the board state. Does that mean it understands Othello?

No, and Janus's Simulator Theory explains why. The model is not an agent playing a game. It is a simulator of game-playing processes. Those are different things.

To predict the next token efficiently, the model *has* to track the board state -- it's the sufficient statistic for the next move. The model discovers this representation because it compresses millions of games into a manageable geometry. As Zecevic et al. point out, the ability to talk about causality (or game states) isn't the same as being causal. The Othello model has learned a transition matrix. It "knows" that state_t leads to move_t+1, but this knowledge is ungrounded -- a closed-loop simulation. The model mirrors the structure of the game board because that structure is isomorphic to the statistical dependencies of the tokens. It doesn't abstract the concept of a game; it constructs a linear topology of token co-occurrences that happens to map onto a grid.

The work here is state-space construction. Build a latent variable (the board) to minimize the perplexity of the observed variable (the moves). A ZIP algorithm "learns" repeated patterns in a text file the same way. Compression looks like understanding when the data itself is structured by human rules.


## 5. The geometry of "truth" and "concepts"

### Truth directions and space neurons

Marks and Tegmark found what they called a "truth direction" in the activation space of LLaMA models. True statements ("Paris is in France") clustered on one side of a hyperplane; false statements ("Paris is in Germany") on the other. Similarly, Gurnee and Tegmark identified "space neurons" and "time neurons" -- linear directions encoding the longitude of cities or birth years of historical figures.

At first glance, this looks like evidence that models abstract concepts like truth and spatial location.

### The probe selectivity problem

It probably isn't. The core objection, articulated by Hewitt and Liang, is this: a high dimensional vector space is so expressive that you can find a linear combination of neurons to predict almost *anything*. If you train a probe to detect "truth," the probe might actually be picking up on common versus uncommon bigrams. Even linear probes can solve random-label "control tasks" with surprising accuracy. When that happens, the probe is doing the abstraction, not the model.

### "Truth" is probably just "likelihood"

The brittleness of the truth direction supports this reading. The truth probe often collapses on negated sentences. "Paris is not in Germany" might get classified as false because the model detects the Paris-Germany association (which is usually false in its training data) or because it chokes on "not" as a logical operator.

What the model most likely represents is token probability, not truth. "Paris is in France" is high probability text. "Paris is in Germany" is low probability text. The "truth direction" is the likelihood direction. The model mimics truth-telling because true statements are the statistical mode of the training corpus. It hasn't abstracted the concept of "fact." It has compressed the concept of "congruent with training distribution."

### Superposition complicates everything

The "clean abstraction" reading gets worse when you account for feature superposition and polysemanticity. Individual neurons in LLMs rarely code for a single concept. They're polysemantic -- a single neuron might fire for "cats" and also for "Bible verses." This lets the model store more features than it has neurons, a massive act of compression, but it means the model's internal "concepts" aren't distinct logical entities. They're interference patterns. A concept exists only as a direction in a crowded high dimensional space, optimized for storage efficiency rather than causal reasoning. The work is managing the interference, not manipulating abstract symbols.


## 6. What this adds up to

The mechanistic evidence supports a refined stochastic parrot hypothesis. Not a random parrot. An algorithmic one. Fourier circuits parrot periodic arithmetic. Induction heads parrot sequence structures. Linear state vectors parrot game states. Each mechanism directly optimizes "predict next token" using the geometric primitives available: dot products, attention heads, MLPs. None of them generalize beyond the statistical structure they were trained on.

Bender's grounding critique holds up under mechanistic scrutiny. The Fourier circuit operates on rotation, not quantity. The Othello circuit tracks token adjacency, not spatial strategy. The truth circuit encodes likelihood, not fact. These mechanisms float in vector space without reference to the external world, and they break when the statistical correlations of the training data break down.

Here is what I keep coming back to. Abstraction implies lossy compression that retains only the causal rule -- F=ma. You throw away everything except the relationship between force, mass, and acceleration, and that relationship holds in contexts you've never seen. Mimicry implies high fidelity compression that retains surface texture and statistical dependencies -- a lookup table of observed forces and accelerations, interpolated by a fitted curve. Both produce correct answers within the training distribution. Only one works outside of it.

LLMs are curve fitters. The curves they fit -- Fourier, induction, linear state -- are mathematically beautiful and functionally powerful. But they are curve fits. They don't extrapolate; they interpolate within the manifold they've constructed. They reflect the abstractions in their training data with mechanical precision. And that's all they do.

| Phenomenon | Mechanism identified | What gets claimed | What's actually happening |
| :---- | :---- | :---- | :---- |
| Grokking | Fourier circuit (trig identities) | Model "understands" arithmetic | Exploits spectral properties of periodic data; fails on non-periodic tasks |
| In-context learning | Induction heads (attend t-1 -> copy) | Model "reasons" from examples | KNN lookup copying historical sequence patterns; can't infer novel rules |
| World models | Linear probes ("My Color" vector) | Model "visualizes" the world | Linear state vector as compression heuristic; ungrounded in physical rules |
| Truthfulness | Truth direction (PCA/probing) | Model "knows" facts | Encodes corpus likelihood and textual coherence; fails on negation |


#### Works cited

1. Seeing Through AI's Lens: Enhancing Human Skepticism Towards LLM-Generated Fake News, arXiv, https://arxiv.org/html/2406.14012v1
2. LLM? More Like "Limited" Language Model with Emily M. Bender, University of Washington, https://uxmag.com/podcast/llm-more-like-limited-language-model-with-emily-m-bender-university-of-washington
3. Strangling the Stochastic Parrots, r/slatestarcodex, https://www.reddit.com/r/slatestarcodex/comments/1jvbepf/strangling_the_stochastic_parrots/
4. Stanford CS25: V1 I Transformer Circuits, Induction Heads, In-Context Learning, YouTube, https://www.youtube.com/watch?v=pC4zRb_5noQ
5. Mechanistic Interpretability for AI Safety: A Review, arXiv, https://arxiv.org/html/2404.14082v3
6. Progress measures for grokking via mechanistic interpretability, https://arxiv.org/abs/2301.05217
7. In-context Learning and Induction Heads, arXiv, https://arxiv.org/abs/2209.11895
8. Simulator Theory, LessWrong, https://www.lesswrong.com/w/simulator-theory
9. Grokking modular arithmetic, arXiv, https://arxiv.org/abs/2301.02679
10. The Complexity Dynamics of Grokking, arXiv, https://arxiv.org/abs/2412.09810
11. Grokking, memorization, and generalization -- a discussion, LessWrong, https://www.lesswrong.com/posts/BYwGEBspGgPY5nBZN/grokking-memorization-and-generalization-a-discussion
12. Progress measures for grokking via mechanistic interpretability, arXiv, https://arxiv.org/html/2301.05217
13. How Do Machines 'Grok' Data?, Quanta Magazine, https://www.quantamagazine.org/how-do-machines-grok-data-20240412/
14. Progress measures for grokking via mechanistic interpretability, SciSpace, https://scispace.com/pdf/progress-measures-for-grokking-via-mechanistic-pfoqpsob.pdf
15. In-context Learning and Induction Heads, Transformer Circuits Thread, https://transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html
16. Critique of impure reason: Unveiling the reasoning behaviour of medical large language models, eLife, https://elifesciences.org/articles/106187
17. How Transformers Implement Induction Heads: Approximation and Optimization Analysis, https://arxiv.org/html/2410.11474v1
18. Emergent world representations: Exploring a sequence model trained on a synthetic task, https://arxiv.org/html/2210.13382v5
19. Emergent World Representations: Exploring a Sequence Model Trained on a Synthetic Task, https://arxiv.org/abs/2210.13382
20. New Othello experiment supports the world model hypothesis for large language models, https://the-decoder.com/new-othello-experiment-supports-the-world-model-hypothesis-for-large-language-models/
21. Revisiting the Othello World Model Hypothesis, arXiv, https://arxiv.org/html/2503.04421v1
22. Actually, Othello-GPT Has A Linear Emergent World Representation, https://www.neelnanda.io/mechanistic-interpretability/othello
23. Actually, Othello-GPT Has A Linear Emergent World Representation, LessWrong, https://www.lesswrong.com/posts/nmxzr2zsjNtjaHh7x/actually-othello-gpt-has-a-linear-emergent-world
24. Linear Spatial World Models Emerge in Large Language Models, arXiv, https://arxiv.org/pdf/2506.02996
25. Do Large Language Models learn world models or just surface statistics?, The Gradient, https://thegradient.pub/othello/
26. Simulators, LessWrong, https://www.lesswrong.com/posts/vJFdjigzmcXMhNTsx/simulators
27. Causal Parrots: Large Language Models May Talk Causality But Are Not Causal, OpenReview, https://openreview.net/pdf?id=tv46tCzs83
28. Causal Parrots: Large Language Models May Talk Causality But Are Not Causal, arXiv, https://arxiv.org/abs/2308.13067
29. https://arxiv.org/abs/2310.06824
30. Language Models Represent Space and Time, https://arxiv.org/abs/2310.02207
31. Self-Prompting in Language Models, Emergent Mind, https://www.emergentmind.com/topics/self-prompting-mechanism
32. Uncovering and Inducing Interpretable Causal Structure in Deep Learning Models, Stanford Stacks, https://stacks.stanford.edu/file/druid:th321qf7186/Geiger-augmented.pdf
33. Truth is Universal: Robust Detection of Lies in LLMs, NeurIPS 2024, https://proceedings.neurips.cc/paper_files/paper/2024/file/f9f54762cbb4fe4dbffdd4f792c31221-Paper-Conference.pdf
34. Emergent Misalignment from Superposition, OpenReview, https://openreview.net/forum?id=XbKMIUghcQ
35. Toy Models of Superposition, Transformer Circuits Thread, https://transformer-circuits.pub/2022/toy_model/index.html
