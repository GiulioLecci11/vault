Neural Networks: A network is a structure consisting of interconnected computational nodes, or 'neurons', arranged in layers. 
These nodes perform mathematical operations on input data, learning some underlying patterns in the data, before producing some output based on those patterns. A <<computational graph>> has an input node where data is fed into the graph, a function node where the input data is processed, and an output node where the result of the computation is produced. It can be used to model different algorithms such as linear regression, logistic regression. Our computational graph setup makes it trivial to model different algorithms. For example, if we want to model a Perceptron instead of logistic regression, we need only switch our logistic function to a step function.

In a neural network, this function node we're changing is very special - we call it an <<artificial neuron>>. An artificial neuron is a fundamental computational element that receives inputs, performs a weighted operation on these inputs, and passes the result through a function.
In neural networks, these functions must be non-linear (otherwise we won't be able to approximate any complex function), and are referred to as <<activation functions>>.

Adding layers is the secret: Nothing is stopping us from chaining multiple artificial neurons together, feeding one to another. This is all a neural network is!!! In fact, the original neural networks were called multilayer perceptrons because they were composed of layers of perceptrons (artificial neurons with step functions) feeding one into another!!!!

Finally we can distinguish between 3 layers: input(a node for each network input), hidden (as many as we want, made by many neurons) and output (representation of the network's output).

In general the input and output layer will be selected for the specific problem, but the hidden layer is fairly arbitrary.
Neural networks can be "wide": having many neurons in a given hidden layer, or "deep": having many hidden layers in the network. Balancing neuron count optimizes performance; more neurons enable complex learning, at the cost of the risk of overfitting and more computational cost.

Activation functions are at the heart of artificial neurons in a neural network. These crucial components introduce non-linearity  into the model, transforming the weighted inputs to generate an output. Simply put, an activation function decides how much signal to pass onto the next layer based on the input it receives. This idea of chaining many weighted signals together is what allows neural networks to learn very complex relationships.

There are 4 main activation functions that are used the most:
1) Sigmoid (output between 0 and 1). It is very useful as output layer when we have to deal with binary classification problems but can suffer from VANISHING GRADIENTS problem
2) Hyperbolic tangent (practically identical to sigmoid but output stays between -1 and 1). Its 'zero-centered output' allows easier learning for the successive layer. Suffers from the same problems of Sigmoid
3) Rectified Linear Unit (ReLU) that is y=0 if x<0 and y=x if x>0 so it only keeps positive input values. It's very used inside hidden layers, this simplicity reduces computational cost and mitigates the vanishing gradients problem, but it can lead to dead neurons where some neurons never activate.
4) Step function (Perceptron) that returns as output just -1 or 1 (neuron fires or not)



BACKPROPAGATION:
The magic behind this learning process is a technique known as backpropagation. The goal of backpropagation is to update the weights so that the Neural Network makes better predictions. Specifically, backpropagation will calculate the gradient of the loss function with respect to the weights of the network, updating the weights layer-by-layer to minimize the network's prediction error.

Let's see it step by step:
1) FORWARD PASS: During the forward pass, input data is fed through a neural network's layers to produce a prediction. This process involves calculating the weighted sums and applying activation functions for each neuron in each layer.

2) ERROR: The error, also known as loss, measures the difference between the neural network's predicted output and the actual target values. By minimizing this error, the neural network learns to improve its predictions during training.

3)BACKWARD PASS: In the backward pass, the error is propagated back through the network, starting from the output layer, to adjust the weights and biases of each neuron. This weight adjustment process, guided by the gradients of the error with respect to the weights, aims to minimize the overall error of the network.

This entire process is known as backpropagation. It is a supervised learning algorithm used in neural networks that optimizes their weights and biases through iterative forward and backward passes. By computing the gradients of the error with respect to the weights, backpropagation enables the network to learn and improve its predictions. It must be done thousand of times and the key thing to note is that, at each run, the network's weights are updated in a manner that improves our model's performance!!!

The networks that work as described until now are often referred as Artificial Neural Networks (ANNs) or Feed Forward Networks but many other types exist:
1)Recurrent Neural Networks (RNNs) differ from feed-forward neural networks as they have a built-in memory, allowing them to process sequences of data. This makes RNNs well-suited for tasks like <<natural language processing>> and time series prediction. They can learn patterns in sequences by connecting the output from one time step to the input of the next, remembering previous information (the recurrence in the namesake).

2)Convolutional Neural Networks (CNNs) are specifically designed for processing spatial data, such as <<images>>. Unlike feed-forward networks, CNNs use special convolutional layers to scan and identify local patterns within the input. Imagine a grid sliding across an image, identifying patterns. This makes them more efficient for image recognition, object detection, and other computer vision tasks, where spatial information is crucial. They are also used for sequential tasks, such as time-series applications.

3)Generative Adversarial Networks (GANs) consist of two distinct neural networks, a generator and a discriminator, that compete against each other. The generator tries to create a data sample, and the discriminator tries to determine if that data sample came from the training data or the generator. By optimizing against each other, GANs learn to generate new data samples by capturing the distribution of the training data. They are widely used for tasks such as <<image synthesis, style transfer, and data augmentation>>.

4)Transformer architectures differ from feed-forward networks as they rely on a SELF-ATTENTION mechanism to process input data, allowing them to handle long-range dependencies more effectively. These models are massive, and <<actually incorporate feed forward neural networks>> in parts of their architecture. They have been especially successful in <<natural language processing>> tasks, such as machine translation and text summarization, due to their ability to capture contextual information across large sequences. Most of the currently hyped AI models, such as the GPT family, are variants of transformers.
#AI 
[[Cognitive Computing Summary]]