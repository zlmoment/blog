+++
date = "2017-09-10T21:58:00"
draft = false
tags = ["paper note", "densenet"]
title = "Paper note - DenseNet"
math = true
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}

There are two papers about DenseNet:

* [Densely Connected Convolutional Networks](http://openaccess.thecvf.com/content_cvpr_2017/papers/Huang_Densely_Connected_Convolutional_CVPR_2017_paper.pdf)
* [Memory-Efficient Implementation of DenseNets](https://arxiv.org/pdf/1707.06990.pdf)

### Brief Introduction
---

In DenseNet, there are direct connections between any two layers with the same feature-map size. This introduces $\frac{L\times(L+1)}{2}$ connections in an $L$-Layer network, instead of just $L$. In contrast to ResNet, DenseNet uses concatenation to combine features instead of summation.  

![](/img/blog/densenet-1.png)

### Dense Connectivity
---

As the figure shown, the $l^{th}$ layer receives the feature-maps of all preceding layers, $x _ 0, ..., x _ {l-1}$, as input:

$$x _ l = H _ l([x _ 0, x _ 1, ..., x _ {l-1}])$$

where $[x _ 0, x _ 1, ..., x _ {l-1}]$ referes to the concatenation of the feature-maps produced in layer $0, ..., l-1$.

$H _ l(\cdot)$ is defined as a composite function of three consecutive operations: $BN \rightarrow ReLU \rightarrow Conv _ {(3\times3)}$.

### Pooling Layers
---

An essential part of convolutional netwroks is down-sampling layers that change the size of feature-maps. To facilitate down-sampling in DenseNet, the network is divided into multiple densely connected **dense blocks**. The layers between blocks are **transition layers**, which do convolution and pooling. The transition layers consist of $BN \rightarrow Conv _ {(1\times1)} \rightarrow AvgPooling _ {(2\times2)}$.


### Growth Rate
---

If $H_L$ produces $k$ feature-maps, it follows that the $l^{th}$ layer has $k _ 0 + k \times (l-1)$ input feature-maps. The hyperparameter $k$ is **growth rate**.


### Bottleneck Layers
---

Each layer produces $k$ output feature-maps, but it typically has many more inputs. It has been noted that a $1\times1$ convolution can be introduced as **bottleneck** layer before each $3\times3$ convolution to reduce the number of input feature-maps. 

The DenseNet with such design, i.e. $$BN \rightarrow ReLU \rightarrow Conv _ {(1\times1)} \rightarrow BN \rightarrow ReLU \rightarrow Conv _ {(3\times3)}$$ version of $H_l$, is called **DenseNet-B**.


### Compression 
---

We can reduce the number of feature-maps at trasition layers as well. If a dense block contains $m$ feature-maps, we let the following trasition layer generate $\lfloor \theta m \rfloor$ output feature-maps, where $0<\theta<1$ is called compression factor. This kind of DenseNet is called **DenseNet-C**. If both bottleneck and compression are used, it is called **DenseNet-BC** then. 


### Memory-Efficient Implementation
---

![](/img/blog/densenet-2.png)

The forward pass (Figure above right) is similar to the naïve implementation (Figure above top left), with the exception that intermediate feature maps are stored in Shared Memory Storage 1 or Shared Memory Storage 2. 

The backward pass requires one additional step: we first re-compute the concatenation and batch normalization operations (center left and center right) in order to re-populate the shared memory storage with the appropriate feature map data. Once the shared memory storage contains the correct data, we can perform regular back-propagation to compute gradients. In total, this DenseNet implementation only allocates memory for the output features (far right), which are constant in size. Its overall memory consumption for feature maps is linear in network depth.

### Implementation
---

See [Github](https://github.com/gpleiss/efficient_densenet_pytorch).