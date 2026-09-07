# Neural Network Notes (Full 4-Part Series)

## 1. The Core Problem

- A neural network is a structure with many adjustable numbers (weights and biases) that gets tuned from examples, instead of a program with hand written rules.
- Example task used throughout: recognizing handwritten digits from 28x28 pixel images.
- Each image becomes 784 numbers (one per pixel), each between 0 and 1 representing brightness.
- Writing explicit rules for "what makes a 3 a 3" is nearly impossible in code, even though it is trivial for a human brain.

## 2. What a Neuron Is

- A neuron is just a container that holds a number, called its activation, $a$.
- In the classic example (sigmoid based), $a \in [0, 1]$.
- Input layer neurons hold pixel brightness values directly.
- Output layer neurons (10 of them, one per digit) represent how strongly the network believes the image is that digit.
- The "activation" concept generalizes beyond brightness:
  - In language models, it can represent presence of a word or an abstract embedding dimension.
  - In audio, it can represent intensity of a frequency.
  - In medical data, it can represent a normalized health metric.
  - In deeper layers, activations often represent abstract concepts with no physical meaning at all.
- Important correction: activation is not universally bound between 0 and 1. The range and meaning of activation is entirely defined by whichever function is used to compute it (sigmoid gives $[0,1]$, ReLU gives $[0,\infty)$, tanh gives $(-1,1)$, softmax gives a probability distribution across a layer).

## 3. Layer Structure

- Example network: $784 \to 16 \to 16 \to 10$.
- Hidden layers are simply layers that are neither input nor output.
- Information flows strictly forward, layer by layer. This is called a feedforward network.
- The number of neurons per hidden layer (16 in this case) is an arbitrary design choice, not a rule.

## 4. The Hopeful Story vs Reality (Interpretability)

- Hopeful idea: first hidden layer detects small edges, second hidden layer combines edges into loops and lines, output layer combines those into digits (pixels to edges to patterns to digits).
- Reality after training a real network: the learned weights, when visualized as images, look messy and largely uninterpretable. No clean edge detectors emerge in practice.
- Reason: gradient descent only optimizes for reducing the cost number. It has no built in preference for human interpretable structure.
- This is the practical demonstration of neural networks being a "black box":
  - Every weight and activation number can be inspected directly, nothing is hidden in a secrecy sense.
  - There is usually no simple human readable explanation for what a hidden neuron represents.
  - Interpretability research exists specifically to try to recover meaning from trained networks, with partial success (some edge detectors have been found in some image networks).
- A trained network can also be fed pure random noise and will still confidently output a specific digit with high activation, since nothing in training ever taught it to recognize "this input looks like nothing I was trained on."

## 5. Weights

- A weight $w$ is attached to a connection between one neuron in a layer and one neuron in the next layer, not to a neuron itself.
- One receiving neuron has its own full set of weights, one per neuron feeding into it from the previous layer.
- Large positive weight: strongly pushes the receiving neuron to activate based on that input.
- Large negative weight: strongly suppresses the receiving neuron based on that input.
- Weight near zero: that input barely matters to this neuron.
- Every neuron in a receiving layer independently answers "how do all previous activations combine to affect me," each with its own separate weight set.
- Visualization trick: since there is one weight per input pixel, the weights for one neuron can be arranged back into a 28x28 grid and displayed as an image (bright for positive, dark for negative, gray for near zero).
- A weight's practical leverage depends on how active the neuron feeding into it already is. A weight connected to an already strongly active neuron has more effect than one connected to a mostly dormant neuron ("neurons that fire together, wire together").

## 6. Bias

- Bias $b$ is a number added to the weighted sum before the activation function is applied.
- Formula so far: weighted sum of previous activations, plus bias.
- Bias controls how easily a neuron fires, independent of the weights.
- Can be thought of as a threshold or sensitivity knob:
  - High bias: neuron is more eager to activate even with a weak weighted sum.
  - Very negative bias: neuron is more skeptical, needs strong evidence (a large weighted sum) before activating.
- Important nuance: bias is a threshold shifter, not a volume control. It does not change how strongly each input matters (that is the weights' job). It only shifts the starting point for activation.
- Every neuron (except input layer neurons) has its own independent bias, tuned during training just like weights.

## 7. Sigmoid Function

- Raw weighted sum plus bias, called $z$, can be any real number.
- Sigmoid squashes any real number into the range $(0, 1)$, smoothly:

$$\sigma(z) = \frac{1}{1 + e^{-z}}$$

- Very negative $z$ goes toward 0, very positive $z$ goes toward 1, $z$ near 0 lands near 0.5.
- Full neuron formula using sigmoid:

$$a = \sigma(z) = \sigma(w \cdot a_{\text{prev}} + b)$$

- Sigmoid was originally chosen because it loosely echoes biological neurons gradually building up charge before firing.
- Sigmoid's derivative, $\sigma'(z)$, is largest near $z = 0$ and flattens out toward zero near the extremes. This flattening can slow down learning in deep networks (the gradient shrinks in flat regions).

## 8. ReLU Function

- ReLU (Rectified Linear Unit):

$$\text{ReLU}(x) = \max(0, x)$$

- Examples: input $-5$ gives $0$, input $-0.3$ gives $0$, input $0.3$ gives $0.3$, input $100$ gives $100$.
- Key correction: ReLU does NOT map values into a $[0, 1]$ range. Output can be any non negative number, unbounded on the top end. Only sigmoid (and similar functions) produce a bounded $(0,1)$ range.
- ReLU has no smooth curve, just a flat line at 0 for negative inputs and a straight line with slope 1 for positive inputs.
- Why negative values getting flattened to 0 is not a loss of important information:
  - Activation is meant to represent "how strongly is this feature present." Negative presence is not a coherent idea, so treating negative raw values as "feature absent" (0) is a reasonable interpretation.
  - A "negative association" is not necessarily lost. The network can represent it using a separate, different neuron whose weights are flipped (positive where the other neuron was negative), effectively assigning the "detects absence or opposite" job to a dedicated neuron rather than encoding it as a negative number on the same neuron.
  - Ultimately this is empirically justified: networks using ReLU tend to train faster and often perform as well or better than ones that try to preserve negative information directly, even though this is not a mathematically airtight proof.
- Because ReLU is simpler (no flattening in the positive region), it tends to avoid the "vanishing gradient" slowdown that sigmoid can cause in deep networks, making it the modern practical default.
- The final output layer of a network can still use a bounded function like softmax to turn raw unbounded scores into normalized, probability like values, even if internal hidden layers use ReLU.

## 9. Counting Parameters

- For the $784 \to 16 \to 16 \to 10$ example network:

$$\text{weights} = (784 \times 16) + (16 \times 16) + (16 \times 10) = 12{,}544 + 256 + 160 = 12{,}960$$

$$\text{biases} = 16 + 16 + 10 = 42$$

$$\text{total parameters} = 12{,}960 + 42 = 13{,}002$$

- "Learning" is literally the process of finding good values for these 13,002 numbers. It is a large but fully concrete search problem, not something mysterious.

## 10. Matrix Notation

- Writing every neuron's weighted sum individually is unwieldy, so it gets compressed into matrix form.
- $W$ = a matrix where each row holds all the weights belonging to one receiving neuron, and each column corresponds to one neuron in the previous layer.
- $a^{(0)}$ = a column vector of the previous layer's activation values.
- $b$ = a column vector of biases for the receiving layer.
- Multiplying $W$ by the previous activation vector performs every neuron's weighted sum simultaneously. Each row's dot product with the activation vector produces that row's neuron's weighted sum.
- Full layer computation in one line:

$$a^{(1)} = \sigma\left(W a^{(0)} + b\right)$$

applying $\sigma$ element wise to every entry.

- Matrix dimensions must match: number of columns in $W$ equals number of neurons in the previous layer, number of rows in $W$ equals number of neurons in the receiving layer.

## 11. Training Data

- Learning requires labeled training data: known correct answers paired with inputs.
- The classic dataset used is MNIST: 60,000 labeled handwritten digit images for training, plus a separate held out test set to check the network on images it has never seen.
- Generalizing well to new, unseen images (not just memorizing training images) is the actual goal.

## 12. Cost Function (Single Example)

- Desired output for a training example (say, an image labeled "3") is a vector $y$ with the "3" entry set to 1 and all other nine entries set to 0.
- Cost for one example:

$$C = \sum_{j} (a_j^{(L)} - y_j)^2$$

where $a_j^{(L)}$ is the actual activation of output neuron $j$ and $y_j$ is its desired value.

- Squaring the differences does two things: makes all errors positive so they cannot cancel out, and penalizes large errors much more than small ones.
- Small cost means the output closely matches the correct answer for that one example. Large cost means the output is far off.

## 13. Cost Function (Averaged Over All Training Data)

- Overall cost:

$$C_{\text{avg}} = \frac{1}{n} \sum_{k=1}^{n} C_k$$

where $n$ is the number of training examples (60,000 for MNIST) and $C_k$ is the per example cost from Section 12.

- This averaged cost is treated as a function of the weights and biases, not of any individual image. The training images and labels are fixed background data; the 13,002 weights and biases are what actually change during training.
- Why average across everything instead of optimizing separately per digit: the same shared weights and biases process every image, regardless of which digit it actually is. The network has no way to know in advance what digit is in front of it, so there is no way to have separate "sub networks" tuned per digit. The only coherent thing to optimize is overall average performance across everything the network will be asked to do.
- Digits the network currently performs badly on naturally produce larger individual costs, which pulls the average up more and causes gradient descent to react more strongly to correcting them, even without explicitly tracking cost per digit.
- Separately measuring performance per digit (confusion matrices, per class accuracy) is a valid and common practice, but it is a diagnostic/evaluation tool, separate from the single scalar number that training itself minimizes.

## 14. Cost as a Function of Weights and Biases

- Reframe: instead of thinking of the network as image goes in, guess comes out, think of the cost function as taking the 13,002 weights and biases as its input and producing a single number (the average cost) as output:

$$C(w_1, w_2, \dots, w_{13{,}002})$$

- Changing any single weight or bias changes how every training image is processed, which changes the overall average cost.
- This reframing turns training into an optimization problem: find values for 13,002 inputs that minimize one output number.

## 15. Gradient Descent (One Dimension)

- Simplify to one weight, one cost value. This traces a curve: x axis is the weight's value, y axis is the resulting cost.
- Goal: find the x value where the curve is lowest.
- Ball rolling downhill method:
  - Start at a random point (random initial weight value).
  - Check the slope at the current point.
  - Take a small step in the direction that decreases the cost (downhill).
  - Repeat: recompute slope, take another step.
  - Steep slope leads to bigger steps, shallow slope leads to smaller steps, which naturally prevents overshooting a minimum.
- This method only guarantees finding a local minimum (a low point relative to nearby points), not necessarily the global minimum (the absolute lowest point on the entire curve).

## 16. Local Minima vs Global Minima

- Random restarts (running gradient descent multiple times from different starting points and keeping the best result) is a valid technique to try to find a better minimum, though it is not covered as core material in the series.
- Reasons a single run's local minimum is often good enough in practice:
  - Cost is a proxy for actual accuracy, not the real goal itself. Many different points in the weight space can all correspond to similarly high real world accuracy, not just one perfect point.
  - Running training multiple times from different starting points is very computationally expensive at real world scale.
  - In very high dimensional spaces (13,002 dimensions or more), truly bad local minima are empirically rarer than low dimensional intuition suggests. For a point to be a true local minimum, the cost must be increasing in every single one of the thousands of directions simultaneously, which is a much harder coincidence than it sounds.
  - Other techniques (momentum, adjusting step size, the randomness introduced by stochastic gradient descent) help the optimization process avoid getting permanently stuck in shallow local minima anyway.
- This "high dimensional local minima are usually close to global" idea is an area of ongoing research and empirical observation, not one single settled proof. Knowing that it exists and roughly why is enough for practical understanding; reading the underlying research papers is only necessary for those pursuing rigorous ML research.

## 17. Gradient Descent (Many Dimensions)

- With 13,002 parameters, the same one dimensional idea is applied simultaneously to every parameter.
- For each individual weight or bias, ask "if I nudge this one number slightly, holding everything else fixed, does the cost go up or down, and by how much."
- Bundling all 13,002 of these individual slopes together gives a single object called the gradient.
- Each full training step nudges every weight and bias simultaneously, by an amount determined by that specific parameter's own slope.

## 18. The Gradient Vector

- The gradient, $\nabla C$, points in the direction of steepest increase of the cost function.
- Since the goal is to decrease cost, you move in the opposite direction of the gradient, $-\nabla C$, which is why the method is called gradient descent.
- The gradient encodes two things at once:
  - Direction: which way to adjust each parameter.
  - Magnitude: how large each component is indicates how sensitive the cost currently is to that specific parameter. Large component means that parameter matters a lot right now; near zero component means it barely matters right now.
- Update rule at each step:

$$\theta \leftarrow \theta - \eta \nabla C$$

where $\theta$ represents all weights and biases together, and $\eta$ (eta) is a small step size, often called the learning rate.

## 19. Definition of "Learning"

- Learning is precisely defined as: repeatedly computing $\nabla C$ and nudging all 13,002 weights and biases a small step in the direction of $-\nabla C$, over and over, across many iterations.
- There is no separate "understanding" happening. It is purely a mechanical optimization procedure with no awareness of digits, shapes, or meaning. It only ever asks whether nudging a parameter reduces the one cost number.

## 20. Backpropagation (Why It Is Needed)

- Naively computing the gradient by testing each of the 13,002 parameters one at a time (nudge one, rerun the whole network on all data, observe the change, repeat) would require far too many full computations to be practical, especially since this would need to happen at every single gradient descent step.
- Backpropagation is the algorithm that computes the entire gradient efficiently, in roughly one backward pass through the network, by reusing intermediate results rather than recomputing everything from scratch for each parameter.
- It works backward, from the output layer toward the input layer, which is why it is called "back" propagation.

## 21. Backpropagation Intuition: Three Levers Per Neuron

- For a single neuron's activation formula, $a = \sigma(w \cdot a_{\text{prev}} + b)$, there are exactly three things you could adjust to change that activation:
  - Increase the bias: directly shifts the pre squish value up, making the neuron more eager to fire.
  - Increase a weight: scales up the contribution of the previous neuron's activation. This lever is more effective when the previous neuron's activation is already large (fire together, wire together).
  - Increase the previous layer's activation: also affects the outcome, but this is not something directly controllable, since that activation is itself the output of the previous neuron's own weights and bias.
- Because the previous layer's activation cannot be adjusted directly, it becomes a "wish", a desired change that gets passed backward to become a goal for the neuron that produced it.
- Local knobs (a neuron's own weight and bias adjustments) stay local and are recorded as final recommendations. Only the wish for a changed activation travels backward to the previous layer.
- At each layer, an incoming wish gets translated into: a recommended nudge to that neuron's own bias, a recommended nudge to each of its own weights, and a new wish sent one layer further back.

## 22. Backpropagation Intuition: Multiple Neurons Per Layer

- In a real (fully connected) network, one neuron feeds into every neuron in the next layer, so going backward, that neuron receives a separate wish from every downstream neuron it connects to, not just one.
- These incoming wishes can conflict (one downstream neuron wants an increase, another wants a decrease).
- Resolution: sum all incoming wishes (appropriately weighted by how strong or confident each one is) into one net wish for that neuron's activation.
- This net wish determines that neuron's own final recommended nudges to its own bias and weights, and is used to generate its own outgoing wishes to the layer before it.

## 23. Backpropagation: Averaging Across All Training Examples

- The full backward pass process described above happens separately for every single training example, each producing its own complete wish list of nudges for all 13,002 parameters.
- Average all these individual wish lists together, parameter by parameter, across the entire training set.
- This averaged wish list is exactly the gradient $\nabla C$ described in the gradient descent section. Backpropagation is simply the efficient method for computing each example's individual contribution to this average.

## 24. Stochastic Gradient Descent (Mini Batches)

- Computing the true gradient by running backpropagation on all 60,000 training images before taking even one small step would be extremely slow.
- Solution: shuffle the training data and split it into small random mini batches (for example, 100 images each).
- Compute the gradient using just one mini batch, take a step, move to the next mini batch, compute a new (approximate) gradient, take another step, and so on.
- One full pass through all mini batches covering the entire dataset is called an epoch. After an epoch, the data is reshuffled and the process repeats.
- This approach trades a slightly less accurate ("noisy" or "stochastic") gradient at each step for the ability to take many more steps in the same amount of time, which works better in practice than fewer, perfectly accurate steps.
- The randomness in this process can also help avoid getting stuck in shallow local minima.

## 25. Backpropagation Calculus: Setup

- Uses a simplified network with one neuron per layer to make the calculus manageable without extra subscripts. All ideas generalize directly to the full multi neuron case.
- Two explicit stages per neuron, at layer $L$, are named separately:

$$z^{(L)} = w^{(L)} a^{(L-1)} + b^{(L)}$$

$$a^{(L)} = \sigma\left(z^{(L)}\right)$$

- Cost for a single output neuron in this simplified case:

$$C = \left(a^{(L)} - y\right)^2$$

where $y$ is the correct/desired value.

## 26. Backpropagation Calculus: Chain Rule for a Weight

- A weight does not affect cost directly. It affects $z^{(L)}$, which affects $a^{(L)}$, which affects $C$. This chain is exactly what the chain rule is built for.
- Formula:

$$\frac{\partial C}{\partial w^{(L)}} = \frac{\partial z^{(L)}}{\partial w^{(L)}} \cdot \frac{\partial a^{(L)}}{\partial z^{(L)}} \cdot \frac{\partial C}{\partial a^{(L)}}$$

- Each piece computed explicitly:

$$\frac{\partial z^{(L)}}{\partial w^{(L)}} = a^{(L-1)}$$

  This formalizes "fire together, wire together": a weight has more effect when the neuron feeding into it is already strongly active.

$$\frac{\partial a^{(L)}}{\partial z^{(L)}} = \sigma'\left(z^{(L)}\right)$$

  The derivative of the sigmoid function itself, largest near $z = 0$ and flattening near the extremes.

$$\frac{\partial C}{\partial a^{(L)}} = 2\left(a^{(L)} - y\right)$$

  A neuron that is already close to correct barely affects the cost derivative, while one that is far off contributes a large push.

- Multiplying these three together gives the total sensitivity of cost to that one weight:

$$\frac{\partial C}{\partial w^{(L)}} = a^{(L-1)} \cdot \sigma'\left(z^{(L)}\right) \cdot 2\left(a^{(L)} - y\right)$$

## 27. Backpropagation Calculus: Chain Rule for a Bias

- Same chain rule structure as the weight case: bias affects $z^{(L)}$, which affects $a^{(L)}$, which affects $C$.
- The last two factors are identical to the weight case, since they do not depend on whether you are looking at a weight or a bias.
- The new piece:

$$\frac{\partial z^{(L)}}{\partial b^{(L)}} = 1$$

since bias is added directly with a coefficient of 1.

- Result:

$$\frac{\partial C}{\partial b^{(L)}} = 1 \cdot \sigma'\left(z^{(L)}\right) \cdot 2\left(a^{(L)} - y\right) = \sigma'\left(z^{(L)}\right) \cdot 2\left(a^{(L)} - y\right)$$

- The bias's sensitivity formula is nearly identical to the weight's, just missing the $a^{(L-1)}$ multiplier. This matches the earlier intuition that bias acts unconditionally (full strength regardless of what is happening upstream), while a weight's effect depends on how active the previous neuron is.

## 28. Backpropagation Calculus: Chain Rule for the Previous Activation

- This is the piece that makes the algorithm recursive and lets it move backward through the network.
- Same chain rule structure, but computing $\frac{\partial C}{\partial a^{(L-1)}}$ instead of a weight or bias derivative.
- The last two factors are unchanged from the weight and bias cases.
- The new piece:

$$\frac{\partial z^{(L)}}{\partial a^{(L-1)}} = w^{(L)}$$

- Result:

$$\frac{\partial C}{\partial a^{(L-1)}} = w^{(L)} \cdot \sigma'\left(z^{(L)}\right) \cdot 2\left(a^{(L)} - y\right)$$

- Because this result is itself another "$\frac{\partial C}{\partial a}$" value, it can be plugged back into the exact same chain rule formulas to compute the previous layer's own weight derivatives, bias derivatives, and its own previous activation derivative, recursing one more layer back. This is the precise mathematical version of the "wish" being passed backward.

## 29. Backpropagation Calculus: Extending to Multiple Neurons Per Layer

- With multiple neurons per layer, every quantity needs extra indices (which neuron in this layer, which neuron in the previous layer it connects to). Let $w_{jk}^{(L)}$ be the weight from neuron $k$ in layer $L-1$ to neuron $j$ in layer $L$, and $a_j^{(L)}$ be the activation of neuron $j$ in layer $L$.
- Cost becomes a sum across all output neurons rather than a single term:

$$C = \sum_j \left(a_j^{(L)} - y_j\right)^2$$

- The weight and bias derivative formulas are structurally identical to the single neuron case, just with added indices for bookkeeping.
- The key new element: since one neuron in a given layer typically feeds into multiple neurons in the next layer, its total effect on cost must sum the chain rule contribution coming through each of those downstream connections:

$$\frac{\partial C}{\partial a_k^{(L-1)}} = \sum_j w_{jk}^{(L)} \cdot \sigma'\left(z_j^{(L)}\right) \cdot 2\left(a_j^{(L)} - y_j\right)$$

- This summation is the calculus justification for the earlier idea of "summing all incoming wishes from downstream neurons."

## 30. Final Summary

- Backpropagation is not a separate learning algorithm. Gradient descent is the actual learning strategy (repeatedly moving downhill in the cost landscape, $\theta \leftarrow \theta - \eta \nabla C$). Backpropagation is only the efficient method for computing which direction is downhill (the gradient) at each point.
- Every part of this process, from weight and bias nudges to activation wishes, is driven purely by reducing the single cost number $C$, with no built in concept of digits, edges, or meaning.
- All of the calculus involved is ordinary chain rule applied to many interconnected quantities. The apparent complexity comes from careful bookkeeping across many neurons and layers, not from any new or exotic mathematics.
