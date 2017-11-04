+++
date = "2017-09-04T14:00:00"
draft = false
tags = ["paper note"]
title = "Paper note - Globally and Locally Consistent Image Completion"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++

{{% toc %}}


![](/img/blog/image-completion1.png)

This is a (another) paper about image completion. It considers both the local continuity and the global composition of the scene, in a single framework for image completion. It also compares results with the previous methods.

Paper link: [click me](http://hi.cs.waseda.ac.jp/~iizuka/projects/completion/data/completion_sig2017.pdf)

### Related Work
---

Different approaches to image completion (inpaiting):

* Diffusion based
* Patch based
* Others

Comparison:

![](/img/blog/image-completion2.png)

### Approach
---

Network structure:

![](/img/blog/image-completion3.png)

Their work is based on Context-Encoder (CE). They extend their work to handle arbitrary resolutions by using a fully convolutional network, and significantly improve the visual quality by a global and local disriminator. 

The dilated convolution layers are used to increase the area each layer can use as input without increasing the number of learnable weights by spreading the convolution kernel across the input map. In this way, the model can effectively "see" a larger area of the input image. However, in my opinion, sometimes increasing the number of layer can also do that. It really depends on what the model is used for.

### Training
---

To increase the stability of training, they pre-train the completion and the discriminator networks. The training is split into three phases: : first, the completion network is trained with the MSE loss from Eq. (2) for Tc iterations. Afterwards, the completion network is fixed and the discriminators are trained from scratch for Td iterations. Finally, both the completion network and content discriminators are trained jointly until the end of training.

There is a simple post-processing with fast marching method and Poisson image blending to make the results more realistic. 

### Hyperparameters
---

* Tc = 90,000
* Td = 10,000
* Ttrain = 500,000

One thing that draws my attention is, the entire training procedure takes roughtly 2 months on a single machine with 4 K80 GPUs. Well, that's impressive.
