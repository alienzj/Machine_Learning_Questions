<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->

**Table of Contents**

- [Neural Networks](#neural-networks)
  - [GEMM](#gemm)
  - [Pooling](#pooling)
  - [Activation Function](#activation-function)
  - [Model compression](#model-compression)
    - [Weight Pruning](#weight-pruning)
  - [Two (Multi Task Learning) MTL methods for Deep Learning](#two-multi-task-learning-mtl-methods-for-deep-learning)
    - [Hard parameter sharing](#hard-parameter-sharing)
    - [Soft parameter sharing](#soft-parameter-sharing)
  - [Deep Learning](#deep-learning)
    - [CNN](#cnn)
    - [Bottleneck layer](#bottleneck-layer)
    - [RNN and LSTM](#rnn-and-lstm)
    - [Resnet](#resnet)
    - [[Mobilenet](docs/Neural_Network/Mobilenet.md)](#mobilenetdocsneuralnetworkmobilenetmd)
    - [[YOLO](docs/Neural_Networks/YOLO.md)](#yolodocsneuralnetworksyolomd)
    - [[Single Shot MultiBox Detector(SSD)](docs/Neural_Networks/SSD.md)](#single-shot-multibox-detectorssddocsneuralnetworksssdmd)
  - [Reference](#reference)

<!-- markdown-toc end -->

# Neural Networks

## GEMM

## Pooling

1. Pooling layers control the number of features the CNN model is learning and it avoids over fitting.
2. There are 2 different types of pooling layers - MAX pooling layer and AVG pooling layer. As the names suggest the MAX pooling layer picks maximum values from the convoluted feature maps and AVG pooling layer takes the average value of the features from the feature maps.

- MAX pooling focus on edge ,work better in practice
- Progressively reduce the spatial size of the representation to reduce the amount of parameters and computation in the network, and hence to also control overfitting.

## Activation Function

the activation function is usually an abstraction representing the rate of action potential firing in the cell. In its simplest form, this function is binary-that is, either the neuron is firing or not.

**Important**: The most important meaning add activation function is by adding the activation funciton, we are adding non-linearity to the model.

For neural networks

- Non-linearity: ReLU is often used. Use Leaky ReLU (a small positive gradient for negative input, say, `y = 0.01x` when x < 0) to reduce death during training

- Multi-class: softmax

  $$
  p_{o,c} = e^{y_{k}}/\sum_{c=1}^M e^{y_{c}}
  $$

- Binary: sigmoid

- Regression: linear

## Model compression

### Weight Pruning

**Getting faster/smaller networks is important for running these deep learning networks on mobile devices.**

The ranking can be done according to the L1/L2 mean of neuron weights, their mean activations, the number of times a neuron wasn't zero on some validation set, and other creative methods . After the pruning, the accuracy will drop (hopefully not too much if the ranking clever), and the network is usually trained more to recover.

If we prune too much at once, the network might be damaged so much it won't be able to recover.

So in practice this is an iterative process - often called 'Iterative Pruning': Prune / Train / Repeat.

![Pruning steps](http://jacobgil.github.io/assets/pruning_steps.png)

## Two (Multi Task Learning) MTL methods for Deep Learning

So far, we have focused on theoretical motivations for MTL. To make the ideas of MTL more concrete, we will now look at the two most commonly used ways to perform multi-task learning in deep neural networks. In the context of Deep Learning, multi-task learning is typically done with either _hard_ or _soft parameter sharing_ of hidden layers.

### Hard parameter sharing

Hard parameter sharing is the most commonly used approach to MTL in neural networks and goes back to <sup class="footnote-ref">[[6]](http://ruder.io/multi-task/index.html#fn6)</sup>. It is generally applied by sharing the hidden layers between all tasks, while keeping several task-specific output layers.

<figure>![](http://ruder.io/content/images/2017/05/mtl_images-001-2.png "Hard parameter sharing")

<figcaption>Figure 1: Hard parameter sharing for multi-task learning in deep neural networks</figcaption></figure>

Hard parameter sharing greatly reduces the risk of overfitting. In fact, <sup class="footnote-ref">[[7]](http://ruder.io/multi-task/index.html#fn7)</sup> showed that the risk of overfitting the shared parameters is an order N -- where N is the number of tasks -- smaller than overfitting the task-specific parameters, i.e. the output layers. This makes sense intuitively: The more tasks we are learning simultaneously, the more our model has to find a representation that captures all of the tasks and the less is our chance of overfitting on our original task.

### Soft parameter sharing

In soft parameter sharing on the other hand, each task has its own model with its own parameters. The distance between the parameters of the model is then regularized in order to encourage the parameters to be similar. <sup class="footnote-ref">[[8]](http://ruder.io/multi-task/index.html#fn8)</sup> for instance use the ℓ2<math xmlns="http://www.w3.org/1998/Math/MathML"><msub><mi>ℓ</mi><mn>2</mn></msub></math> norm for regularization, while <sup class="footnote-ref">[[9]](http://ruder.io/multi-task/index.html#fn9)</sup> use the trace norm.

<figure>![](http://ruder.io/content/images/2017/05/mtl_images-002-1.png "Soft parameter sharing")
<figcaption>Figure 2: Soft parameter sharing for multi-task learning in deep neural networks</figcaption></figure>

The constraints used for soft parameter sharing in deep neural networks have been greatly inspired by regularization techniques for MTL that have been developed for other models, which we will soon discuss.

## Deep Learning

### CNN

The Conv layer is the building block of a Convolutional Network. The Conv layer consists of a set of learnable filters (such as 5 x 5 x 3, width x height x depth). During the forward pass, we slide (or more precisely, convolve) the filter across the input and compute the dot product. Learning happens when the network back propagate the error layer by layer.

Initial layers capture low-level features such as angle and edges, while later layers learn a combination of the low-level features and in the previous layers and can therefore represent higher level feature, such as shape and object parts.

![CNN](../assets/cnn.jpg)

### Bottleneck layer

The bottleneck in a neural network is just a layer (e.g. convolution layer) with less neurons then the layer below or above it. Having such a layer encourages the network to compress feature representations to best fit in the available space, in order to get the best loss during training.

### RNN and LSTM

https://colah.github.io/posts/2015-08-Understanding-LSTMs/

RNN is another paradigm of neural network where we have difference layers of cells, and each cell not only takes the cell from the previous layer as input, but also the previous cell within the same layer. This gives RNN the power to model sequence.

![RNN](../assets/rnn.jpeg)

A recurrent neural network can be thought of as multiple copies of the same network, each passing a message to a successor. Consider what happens if we unroll the loop:

![An unrolled recurrent neural network.](https://colah.github.io/posts/2015-08-Understanding-LSTMs/img/RNN-unrolled.png)

All recurrent neural networks have the form of a chain of repeating modules of neural network. In standard RNNs, this repeating module will have a very simple structure, such as a single tanh layer.

![](https://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-SimpleRNN.png)

This seems great, but in practice RNN barely works due to exploding/vanishing gradient, which is cause by a series of multiplication of the same matrix. To solve this, we can use a variation of RNN, called long short-term memory (LSTM), which is capable of learning long-term dependencies.

The math behind LSTM can be pretty complicated, but intuitively LSTM introduce

- input gate
- output gate
- forget gate
- memory cell (internal state)

The cell state is kind of like a conveyor belt. It runs straight down the entire chain, with only some minor linear interactions. It's very easy for information to just flow along it unchanged.

LSTM resembles human memory: it forgets old stuff (old internal state _ forget gate) and learns from new input (input node _ input gate)

![lstm](../assets/lstm.png)

LSTMs also have this chain like structure, but the repeating module has a different structure. Instead of having a single neural network layer, there are four, interacting in a very special way.

![A LSTM neural network.](https://colah.github.io/posts/2015-08-Understanding-LSTMs/img/LSTM3-chain.png)

### Resnet

Increasing network depth does not work by simply stacking layers together. Deep networks are hard to train because of the notorious vanishing gradient problem-as the gradient is back-propagated to earlier layers, repeated multiplication may make the gradient infinitively small. As a result, as the network goes deeper, its performance gets saturated or even starts degrading rapidly.

The core idea of ResNet is introducing a so-called shortcut.

- When the input and output are of the same dimensions, we use "identity shortcut connection" that skips one or more layers, as shown in the following figure:

  ![img](https://cdn-images-1.medium.com/max/1500/1*ByrVJspW-TefwlH7OLxNkg.png)

- When the dimensions increase, we consider two options: (A) THe shortcut still performs identity mapping, with extra zero entries padded for increasing dimensions. (B) The projection shortcut is used to match dimensions.

The authors argues that stacking layers shouldn't degrade the network performance, because we could simply stack identity mappings (layer that doesn't do anything) upon the current network, and the resulting architecture would perform the same. This indicates that the deeper model should not produce a training error higher than its shallower counterparts.

### [Mobilenet](docs/Neural_Network/Mobilenet.md)

### [YOLO](docs/Neural_Networks/YOLO.md)

### [Single Shot MultiBox Detector(SSD)](docs/Neural_Networks/SSD.md)

## Reference

1. [standford cs231 notes](http://cs231n.github.io/)
2. [mobilenet v1](https://arxiv.org/pdf/1704.04861.pdf)
3. [mobilenet v2](https://arxiv.org/pdf/1801.04381.pdf)
4. [yolo v1](https://arxiv.org/pdf/1506.02640.pdf)
5. [yolo 9000](https://arxiv.org/pdf/1612.08242.pdf)
6. [yolo v3](https://pjreddie.com/media/files/papers/YOLOv3.pdf)
7. [real-time-object-detection-with-yolo-yolov2-](https://medium.com/@jonathan_hui/real-time-object-detection-with-yolo-yolov2-28b1b93e2088)