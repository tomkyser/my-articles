# **The Mechanistic Mirage: An Exhaustive Analysis of Simulated Abstraction in Large Language Models**

## **Executive Summary**

The rapid advancement of Large Language Models (LLMs) has engendered a pervasive narrative that these systems have transcended mere statistical correlation to achieve genuine "understanding," "abstraction," and "extrapolation." This report rigorously interrogates these claims through the lens of **mechanistic interpretability**—the scientific endeavor to reverse-engineer the internal circuitry of neural networks. By isolating the specific computational mechanisms often cited as proof of abstraction—specifically **Fourier circuits in algorithmic tasks**, **Induction Heads in in-context learning**, and **linear state representations in synthetic world modeling**—we demonstrate that what appears to be abstract reasoning is, upon closer inspection, a sophisticated form of **structural mimicry**. The "work" performed by these models is not the derivation of general principles from particulars (abstraction), but rather the optimization of high-dimensional signal processing circuits that compress and reproduce the statistical structure of the training data. This analysis supports the position that LLMs are fundamentally incapable of true abstraction, functioning instead as advanced simulators of the *results* of abstraction.

## **1\. Introduction: The Crisis of Definition in Artificial Intelligence**

### **1.1 The Behavioral Trap and the "Clever Hans" Effect**

The field of artificial intelligence is currently grappling with a severe crisis of definition regarding the nature of capability. As models scale to billions of parameters, trained on trillions of tokens, they exhibit behaviors that mimic human reasoning with uncanny fidelity. They solve mathematical problems, write coherent essays, and seemingly infer rules from sparse examples. However, relying on behavioral outputs to attribute cognitive states like "abstraction" invites the "Clever Hans" error—mistaking a subject's reliance on subtle, spurious cues for genuine understanding.  
In the context of LLMs, the "Clever Hans" effect manifests as the "Stochastic Parrot" phenomenon, where a model stitches together linguistic forms without reference to meaning. While early critiques focused on the lack of communicative intent, the current debate has shifted to the internal validity of the model's operations. Does the model solve a \+ b by understanding arithmetic, or by memorizing a high-dimensional lookup table? Does it predict the next move in a game by visualizing the board, or by computing transition probabilities based on token adjacency?

### **1.2 The Turn to Mechanistic Interpretability**

To adjudicate between "abstraction" and "mimicry," we must look beyond the output to the **internal mechanism**. Mechanistic interpretability seeks to treat neural networks not as black boxes, but as compiled programs that can be decompiled into human-understandable algorithms. This report focuses exclusively on research that demonstrates the *work*—the internal weights, activations, and circuits—that drives model behavior.  
By examining the precise mathematical operations identified in recent literature—such as the **Discrete Fourier Transform (DFT)** circuits in modular arithmetic and the **Induction Head** mechanisms in language modeling —we can evaluate the ontological status of these "abstractions." The findings suggest that these mechanisms are rigid, domain-specific implementations of statistical compression. They are "simulators" that maintain coherence through geometric invariance rather than semantic understanding.

## **2\. The Algorithmic Mirage: Grokking and the Fourier Circuit**

### **2.1 The Phenomenon of Grokking: A False Dawn of Generalization?**

One of the most cited pieces of evidence for "emergent abstraction" in neural networks is the phenomenon of **grokking**. First identified by Power et al. and further analyzed by Nanda et al., grokking describes a peculiar training dynamic observed in small Transformers trained on algorithmic tasks, such as modular addition (a \+ b \\pmod P).  
During the initial phase of training, the model achieves low training loss but high test loss—it effectively memorizes the training data. Then, often after a prolonged plateau of thousands of gradient descent steps, the test loss suddenly drops, and the model achieves perfect generalization. This "phase transition" is widely interpreted as the moment the model "understands" the rule, discarding its memorized lookup table in favor of a general algorithm.  
However, the question remains: *What* is the algorithm the model discovers? Is it the abstract concept of addition, or something else entirely?

### **2.2 Reverse-Engineering the Mechanism: The Fourier Circuit**

Nanda et al. achieved a breakthrough by fully reverse-engineering the algorithm learned by a one-layer Transformer trained on modular addition. They found that the model does not implement "addition" in the arithmetic logic sense (e.g., carrying bits or counting). Instead, it implements a **Fourier Multiplication Circuit**.

#### **2.2.1 The Trigonometric Embedding**

The "work" begins in the embedding layer. The model learns to map the integer inputs a and b (which are discrete tokens) into a vector space defined by trigonometric functions. Specifically, the embedding matrix W\_E maps input x to a vector containing terms like \\cos(\\omega\_k x) and \\sin(\\omega\_k x), where \\omega\_k \= \\frac{2\\pi k}{P} are specific "key frequencies" related to the modulus P.  
This is a crucial insight: the model does not treat numbers as quantities, but as **positions on a circle**. It essentially discovers that modular arithmetic is isomorphic to rotations in the complex plane.

#### **2.2.2 The Trigonometric Identity in the MLP**

Within the Multi-Layer Perceptron (MLP) layers, the model performs the "addition" by exploiting trigonometric identities. The attention mechanism and MLP weights are organized to compute the cosine of the sum of the angles:  
The neural network implements this formula literally. The activations of the neurons correspond to these product terms. By multiplying the sine and cosine components of the inputs a and b, the network constructs the sine and cosine components of the sum a+b.

#### **2.2.3 Constructive Interference as "Result"**

Finally, to produce the output token c, the model uses its unembedding matrix to sum these frequency components. The mechanism relies on **constructive interference**:

* At the correct answer c \= a \+ b, the waves for all learned frequencies align (constructive interference), producing a large logit.  
* At incorrect answers, the waves cancel out (destructive interference), producing small logits.

### **2.3 Interpretation: The "Clock" vs. The "Pizza"**

Does this constitute abstraction? The model has certainly found a general rule that applies to unseen data. However, deeper analysis suggests this is a **mathematical artifact** of the training setup rather than a cognitive abstraction of addition.  
Zhong et al. found that this convergence is not guaranteed. Depending on initialization and hyperparameters, models sometimes fail to find the perfect "Clock" algorithm (the Fourier circuit) and instead settle for a "Pizza" algorithm—a messier, heuristic-based solution that generalizes imperfectly. This variability indicates that the model is not seeking the "truth" of arithmetic; it is simply sliding down a loss landscape. The "Clock" circuit is a deep basin of attraction because the Fourier transform is a particularly efficient way to compress periodic data using linear operations (matrix multiplications) and simple non-linearities (activations).  
The "work" here is **signal processing**, not arithmetic reasoning. The model "groks" the periodicity, not the mathematics. If the task were slightly altered to non-periodic arithmetic (e.g., standard addition without a modulus), this Fourier mechanism would fail, and the model would likely revert to memorization or find a different, likely less "abstract," heuristic. The "abstraction" is brittle; it is valid only within the strict geometry of the embedding space. It is a mimicry of addition using wave interference—a "Clever Hans" trick of physics applied to mathematics.  
**Table 1: The Mechanistic Gap in Grokking**

| Component | Human Abstraction of "Sum" | LLM "Grokking" Mechanism | Implication for Abstraction Claim |
| :---- | :---- | :---- | :---- |
| **Representation** | Quantitative value; "magnitude." | Frequency/Phase on a circle (\\cos(\\omega x)). | The model treats numbers as rotational coordinates, not quantities. |
| **Operation** | Logical operation (counting/carrying). | Trigonometric Identity (\\cos(a+b) \= \\dots). | The "rule" is a geometric coincidence, not a logical derivation. |
| **Output** | Derivation of a unique result. | Constructive interference of waves. | The answer "emerges" from signal overlap, mimicking the result without the process. |
| **Robustness** | Generalizes to any number system. | Fails if periodicity (P) is removed/changed. | The mechanism is bound to the specific structural properties of the training data. |

## **3\. The Mechanics of Mimicry: Induction Heads**

### **3.1 In-Context Learning: The Illusion of Reasoning**

LLMs exhibit **In-Context Learning (ICL)**: the ability to adapt to a new task defined in the prompt without updating weights. For example, given the prompt "A-\>1, B-\>2, C-\>?", the model predicts "3". This is often cited as evidence of "meta-learning" or the ability to abstract the relationship "letter to corresponding number".  
However, mechanistic interpretability has identified the precise circuit responsible for the majority of this capability: the **Induction Head**. As defined by Olsson et al., induction heads are circuits that allow a model to "copy and complete" patterns.

### **3.2 Anatomy of the Induction Circuit**

An induction head is not a single logical unit but a composition of two attention heads working in sequence across layers.

1. *The Previous Token Head:* Located in an earlier layer, this attention head attends to the token at position t-1. Its output effectively moves the information from the previous token into the current token's residual stream.  
   * *Function:* "Look at what happened just before."  
2. **The Induction Head:** Located in a later layer, this head uses the output of the first head as a query. It scans the context for a token that matches the *current* token. When it finds a match (say, at position k), it shifts its attention to position k+1 (the token that followed the match in the past) and copies its value to the current position.  
   * *Function:* "If I see 'A' now, and I saw 'A' followed by 'B' earlier, predict 'B'."

### **3.3 The "Bump" and the Limits of "Copy-Paste" Intelligence**

The development of induction heads correlates perfectly with a "phase change" in the model's training loss—a sudden "bump" where the model gets worse before getting much better. This suggests that the model "discovers" the induction mechanism as a global strategy for minimizing entropy.  
This mechanism is essentially a **K-Nearest Neighbors (KNN)** lookup in the sequence history. It is a sophisticated form of **copying**.

* **Mimicry of Logic:** When an LLM produces a "Chain of Thought" (CoT), it often relies on induction heads to maintain coherence. If the prompt contains a logical deduction pattern ("Premise \-\> Conclusion"), the induction heads copy this *structure*. They do not evaluate the logic; they replicate the syntactic form of the deduction found in the context or training data.  
* **Failure of Extrapolation:** The limitation of this mechanism is evident when true extrapolation is required. Bietti et al. and others show that on synthetic tasks, models learn n-gram statistics (local memorization) first, then induction heads (long-range copying). However, they rarely transition to a "true" general rule if the pattern requires manipulating the data in a way not present in the context. For instance, an induction head cannot "infer" a new causal relationship; it can only retrieve a correlation that has already been instantiated in the window.

The "work" of the induction head is **associative recall**, not reasoning. It creates the *illusion* of understanding a sequence by identifying and repeating its constituent sub-sequences. It is a mechanical implementation of the "Stochastic Parrot" hypothesis—the model parrots its own context.

## **4\. The Simulation of Space: Othello-GPT and World Models**

### **4.1 The World Model Hypothesis**

Perhaps the strongest counter-argument to the "pure mimicry" position comes from the **Othello-GPT** experiments. Li et al. trained a Transformer on textual records of Othello games. The model never saw a board, only sequences like "C4 E3 F5". Yet, it learned to predict legal moves with high accuracy.  
Li et al. trained **probes**—classifiers that take the model's internal activations as input—and claimed to recover the state of the board (which piece is on which square). They argued this was evidence of an **emergent, non-linear world model**.

### **4.2 The Linear Rebuttal: Nanda's "My Color" Insight**

However, subsequent analysis by Nanda et al. dismantled the "non-linear" claim and, in doing so, revealed the true nature of the abstraction. Nanda showed that the board state was not encoded in a complex, non-linear manifold, but in a **linear** subspace.  
Crucially, the model did not represent the board in absolute terms ("Black vs. White"). It represented the board in a **relativistic frame of reference**: "**My Color** vs. **Opponent's Color**". Since the model plays both sides (predicting the next move regardless of whose turn it is), this frame is the most efficient way to compress the game logic.

### **4.3 Intervention: The Proof of Simulation**

To prove this linear representation was causal (and not just a correlation), Nanda performed **interventional experiments**.

1. **The Method:** Identify the "My Color" direction in the activation space for a specific board square (e.g., C4).  
2. **The Intervention:** Manually inject a vector into the model's residual stream that flips the value along this direction (changing "Empty" to "My Color").  
3. **The Result:** The model's predictions changed immediately. It began predicting moves that would be legal *only* if C4 were occupied by its own piece.

### **4.4 Mimicry via State Tracking**

Does this intervention prove the model "understands" Othello? Or does it prove it is a **simulator**?  
Janus's **Simulator Theory** provides the critical interpretative framework here. The model is not an agent playing a game; it is a simulator of a game-playing process.

* **Compression vs. Abstraction:** To predict the next token efficiently, the model *must* track the state of the board. The board state is the "sufficient statistic" for the next move. The model discovers this state representation because it compresses the training data (millions of games) into a manageable geometry.  
* **The "Causal Parrot" Critique:** Zecevic et al. warn against conflating the ability to *talk* about causality (or game states) with being causal. The Othello model has learned a transition matrix. It "knows" that State\_t leads to Move\_t+1. But this "knowledge" is ungrounded. It is a **closed-loop simulation**. The model mimics the *structure* of the game board because that structure is isomorphic to the statistical dependencies of the tokens. It does not "abstract" the concept of a game; it constructs a **linear topology** of token co-occurrences that happens to map onto a grid.

The "work" here is **state-space construction**. The model builds a latent variable (the board) to minimize the perplexity of the observed variable (the moves). This is a statistical necessity, not a cognitive leap. It is the same process by which a ZIP algorithm "learns" the repeated patterns in a text file—it is compression, which looks like understanding only because the data itself is structured by human rules.

## **5\. The Geometry of Statistics: Truth and Concepts**

### **5.1 The Geometry of Truth**

Marks and Tegmark extended the probing methodology to the concept of **Truth** itself. In "The Geometry of Truth," they identified a **"Truth Direction"** in the activation space of LLaMA models. True statements ("Paris is in France") clustered on one side of a hyperplane; false statements ("Paris is in Germany") on the other.  
Similarly, Gurnee and Tegmark identified **"Space Neurons"** and **"Time Neurons"**—linear directions that encoded the longitude of cities or the birth years of historical figures.

### **5.2 The Probe Selectivity Problem: A Methodological Mirage**

While these findings seem to suggest the model abstracts concepts like "Space" and "Truth," the **Probe Selectivity** critique (articulated by Hewitt & Liang) undermines this conclusion.

* **The Problem:** A high-dimensional vector space (like an LLM's residual stream) is so expressive that you can find a linear combination of neurons to predict almost *anything*. If you train a probe to predict "Truth," the probe might actually be learning to detect "common bigrams" vs. "uncommon bigrams."  
* **Control Tasks:** Hewitt & Liang showed that probes can often solve "control tasks" (predicting random labels) with high accuracy if the probe itself is too powerful. Even with linear probes, there is a danger that the *probe* is doing the abstraction, not the model.

### **5.3 The Inconsistency of the "Truth" Abstraction**

Further evidence that this is mimicry comes from the brittleness of these directions.

* **Affirmative Bias:** The "Truth Direction" often collapses on negated sentences. If the model sees "Paris is not in Germany," the "Truth" probe might classify it as "False" because it detects the semantic association "Paris-Germany" (which is usually false) or because it struggles with the logical operator "not".  
* **Mimicry of Corpus Statistics:** What the model represents is likely **Token Probability**, not Truth. "Paris is in France" is high-probability text; "Paris is in Germany" is low-probability. The "Truth Direction" is simply the "Likelihood Direction." The model mimics truth-telling because truth is the statistical mode of the training corpus. It has not abstracted the epistemological concept of "fact"; it has compressed the statistical concept of "congruence with training distribution."

### **5.4 Feature Superposition and Polysemanticity**

Finally, the "clean abstraction" narrative is complicated by **Feature Superposition** and **Polysemanticity**. Individual neurons in LLMs are rarely monosemantic (coding for one concept). Instead, they are **polysemantic**, firing for unrelated concepts (e.g., a neuron that fires for "cats" and "Bible verses").  
This superposition allows the model to store more features than it has neurons—a massive act of **compression**. However, it means the model's internal "concepts" are not distinct, logical entities. They are **interference patterns**. A concept exists only as a direction in a messy, crowded high-dimensional space. This architecture is optimized for *storage efficiency* (mimicry of vast data), not for *causal reasoning* (abstraction of clear variables). The "work" is managing this interference, not manipulating abstract symbols.

## **6\. Synthesis: The Uncanny Valley of Abstraction**

### **6.1 Revisiting the Stochastic Parrot**

The mechanistic evidence gathered supports a refined version of the "Stochastic Parrot" hypothesis. The LLM is not a "random" parrot; it is a **highly structured, algorithmic parrot**.

* It uses **Fourier circuits** to parrot periodic arithmetic.  
* It uses **Induction Heads** to parrot sequence structures.  
* It uses **Linear State Vectors** to parrot game states.

In each case, the mechanism is a direct optimization of the objective function (predict next token) using the available geometric primitives (dot products, attention heads, MLPs).

### **6.2 The Absence of Grounding**

Bender's critique of **grounding** remains mechanically valid. The Fourier circuit knows "rotation," not "quantity." The Othello circuit knows "token adjacency," not "spatial strategy." The Truth circuit knows "likelihood," not "fact." Because these mechanisms are ungrounded—floating in the vector space of the simulator without reference to the external world—they are brittle. They fail when the statistical correlations of the training data are broken (e.g., the "Pizza" algorithm failure, or the inability of induction heads to extrapolate novel causal rules).

### **6.3 Conclusion: Mimicry Optimized**

The internal "work" of LLMs demonstrates that they are engines of **mimicry**. They mimic the *results* of abstraction by constructing sophisticated, high-dimensional simulators that replicate the generative processes of the training data.

* **Abstraction** implies a lossy compression that retains only the causal rule (e.g., F=ma).  
* **Mimicry** implies a high-fidelity compression that retains the surface texture and statistical dependencies (e.g., a lookup table of all observed forces and accelerations, interpolated by a curve).

LLMs are the latter. They are curve-fitters par excellence. The curves they fit (Fourier, Induction, Linear States) are mathematically beautiful and functionally powerful, but they are curve fits nonetheless. They do not extrapolate; they **interpolate** within the manifold they have constructed. Thus, the position holds: it is not possible for an LLM to truly be capable of abstraction; they serve only as mirrors, reflecting the abstractions inherent in their training data with staggering, but mechanical, precision.  
**Table 2: Summary of Mechanistic Evidence for Mimicry**

| Phenomenon | Mechanism Identified | "Abstraction" Claim | Mechanistic Reality (Mimicry/Compression) |
| :---- | :---- | :---- | :---- |
| **Grokking** | **Fourier Circuit** (Trig Identities) | Model "understands" arithmetic. | Model exploits spectral properties of periodic data; fails on non-periodic tasks (e.g., "Pizza" anomalies). |
| **In-Context Learning** | **Induction Heads** (Attn t-1 \\to Copy) | Model "reasons" from examples. | Model uses K-Nearest Neighbor lookup to copy historical sequence patterns; cannot infer novel rules. |
| **World Models** | **Linear Probes** ("My Color" vector) | Model "visualizes" the world. | Model maintains a linear state vector as a compression heuristic for sequence prediction; ungrounded in physical rules. |
| **Truthfulness** | **Truth Direction** (PCA/Probing) | Model "knows" facts. | Model encodes "corpus likelihood" or "textual coherence"; fails on negation and complex logic. |

#### **Works cited**

1\. Seeing Through AI's Lens: Enhancing Human Skepticism Towards LLM-Generated Fake News \- arXiv, https://arxiv.org/html/2406.14012v1 2\. LLM? More Like "Limited" Language Model with Emily M. Bender, University of Washington, https://uxmag.com/podcast/llm-more-like-limited-language-model-with-emily-m-bender-university-of-washington 3\. Strangling the Stochastic Parrots : r/slatestarcodex \- Reddit, https://www.reddit.com/r/slatestarcodex/comments/1jvbepf/strangling\_the\_stochastic\_parrots/ 4\. Stanford CS25: V1 I Transformer Circuits, Induction Heads, In-Context Learning \- YouTube, https://www.youtube.com/watch?v=pC4zRb\_5noQ 5\. Mechanistic Interpretability for AI Safety A Review \- arXiv, https://arxiv.org/html/2404.14082v3 6\. Progress measures for grokking via mechanistic interpretability, https://arxiv.org/abs/2301.05217 7\. \[2209.11895\] In-context Learning and Induction Heads \- arXiv, https://arxiv.org/abs/2209.11895 8\. Simulator Theory \- LessWrong, https://www.lesswrong.com/w/simulator-theory 9\. \[2301.02679\] Grokking modular arithmetic \- arXiv, https://arxiv.org/abs/2301.02679 10\. \[2412.09810\] The Complexity Dynamics of Grokking \- arXiv, https://arxiv.org/abs/2412.09810 11\. Grokking, memorization, and generalization — a discussion \- LessWrong, https://www.lesswrong.com/posts/BYwGEBspGgPY5nBZN/grokking-memorization-and-generalization-a-discussion 12\. Progress measures for grokking via mechanistic interpretability \- arXiv, https://arxiv.org/html/2301.05217 13\. How Do Machines 'Grok' Data? \- Quanta Magazine, https://www.quantamagazine.org/how-do-machines-grok-data-20240412/ 14\. progress measures for grokking via \- SciSpace, https://scispace.com/pdf/progress-measures-for-grokking-via-mechanistic-pfoqpsob.pdf 15\. In-context Learning and Induction Heads \- Transformer Circuits Thread, https://transformer-circuits.pub/2022/in-context-learning-and-induction-heads/index.html 16\. Critique of impure reason: Unveiling the reasoning behaviour of medical large language models | eLife, https://elifesciences.org/articles/106187 17\. How Transformers Implement Induction Heads: Approximation and Optimization Analysis, https://arxiv.org/html/2410.11474v1 18\. Emergent world representations: Exploring a sequence model trained on a synthetic task, https://arxiv.org/html/2210.13382v5 19\. Emergent World Representations: Exploring a Sequence Model Trained on a Synthetic Task, https://arxiv.org/abs/2210.13382 20\. New Othello experiment supports the world model hypothesis for large language models, https://the-decoder.com/new-othello-experiment-supports-the-world-model-hypothesis-for-large-language-models/ 21\. Revisiting the Othello World Model Hypothesis \- arXiv, https://arxiv.org/html/2503.04421v1 22\. Actually, Othello-GPT Has A Linear Emergent World Representation ..., https://www.neelnanda.io/mechanistic-interpretability/othello 23\. Actually, Othello-GPT Has A Linear Emergent World Representation \- LessWrong, https://www.lesswrong.com/posts/nmxzr2zsjNtjaHh7x/actually-othello-gpt-has-a-linear-emergent-world 24\. Linear Spatial World Models Emerge in Large Language Models \- arXiv, https://arxiv.org/pdf/2506.02996 25\. Do Large Language Models learn world models or just surface statistics? \- The Gradient, https://thegradient.pub/othello/ 26\. Simulators — LessWrong, https://www.lesswrong.com/posts/vJFdjigzmcXMhNTsx/simulators 27\. Causal Parrots: Large Language Models May Talk Causality But Are Not Causal \- OpenReview, https://openreview.net/pdf?id=tv46tCzs83 28\. \[2308.13067\] Causal Parrots: Large Language Models May Talk Causality But Are Not Causal \- arXiv, https://arxiv.org/abs/2308.13067 29\. arxiv.org, https://arxiv.org/abs/2310.06824 30\. Language Models Represent Space and Time, https://arxiv.org/abs/2310.02207 31\. Self-Prompting in Language Models \- Emergent Mind, https://www.emergentmind.com/topics/self-prompting-mechanism 32\. UNCOVERING AND INDUCING INTERPRETABLE CAUSAL STRUCTURE IN DEEP LEARNING MODELS A DISSERTATION SUBMITTED TO THE DEPARTMENT OF LIN \- Stacks, https://stacks.stanford.edu/file/druid:th321qf7186/Geiger-augmented.pdf 33\. Truth is Universal: Robust Detection of Lies in LLMs \- NIPS papers, https://proceedings.neurips.cc/paper\_files/paper/2024/file/f9f54762cbb4fe4dbffdd4f792c31221-Paper-Conference.pdf 34\. Emergent Misalignment from Superposition \- OpenReview, https://openreview.net/forum?id=XbKMIUghcQ 35\. Toy Models of Superposition \- Transformer Circuits Thread, https://transformer-circuits.pub/2022/toy\_model/index.html