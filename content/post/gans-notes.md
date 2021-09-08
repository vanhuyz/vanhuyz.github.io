+++
author = "Huy Van"
categories = ["Deep Learning", "English"]
date = 2017-01-06T05:28:00Z
description = ""
draft = false
slug = "gans-notes"
tags = ["Deep Learning", "English"]
title = "A note about Generative Adversarial Networks"

+++


## Generative Adversarial Networks (GAN)
* Use a latent code
* Asymptotically consistent (unlike variational methods - e.g. VAE)
* No Markov chains needed (unlike Boltzmann Machines)
* Often regarded as producing the best samples (?)

### Discriminator
* The discriminator examines samples to determine whether they are **real** or **fake**.


* Cost:

$$
J^{(D)}\big(\boldsymbol{\theta}^{(D)},\boldsymbol{\theta}^{(G)}\big)=-\frac{1}{2}\mathbb{E}_ {\boldsymbol{x} \sim p_ {data}} \log D(\boldsymbol{x}) - \frac{1}{2}\mathbb{E}_ \boldsymbol{z}\log(1-D(G(\boldsymbol{z})))
$$

This is just the standard cross-entropy cost that is minimized when training a standard binary classifier with a sigmoid output. The only difference is that the classifier is trained on two minibatches of data; one coming from the dataset, where the label is 1 for all examples, and one coming from the generator, where the label is 0 for all examples.

### Generator
There are 3 variants:

* Minimax

$$
J^{(G)} = -J^{(D)}
$$

* Heuristic, non-saturating

$$
J^{(G)} = -\frac{1}{2}\mathbb{E}_ \boldsymbol{z}\log(D(G(\boldsymbol{z})))
$$

* Maximum likelihood

$$
J^{(G)} = -\frac{1}{2}\mathbb{E}_ \boldsymbol{z}\exp\big(\sigma^{-1}(D(G(\boldsymbol{z})))\big)
$$

The heuristically designed non-saturating cost seems to be most successful in practice.

## DCGAN

DCGAN stands for “deep, convolution GAN.”

* Use batch normalization
* Mostly borrowed from the all-convolutional net (Springenberg et al., 2015)
* Use Adam optimizer

## Tips and Tricks

https://github.com/soumith/ganhacks

* Train with labels
* One-sided label smoothing
* Virtual batch normalization
* Balance G and D
    * Usually D is bigger and deeper than G

## Research Frontiers
### Non-convergence
The largest problem facing GANs that researchers should try to resolve is the issue of non-convergence.

There are some attempts to solve this problem:

* StackGANs (Zhang et al., 2016)
* Minibatch GANs (Salimans et al., 2016)
* Unrolled GANs (Metz et al., 2016)

### Semi-supervised learning

* CatGANs (Springenberg, 2015)
* Feature matching GANs (Salimans et al., 2016): currently is the state of the art in semi-supervised learning on MNIST, SVHN, and CIFAR-10

### Using the code

GANs learn a representation **z** of the image **x**. It is already known that this representation can capture useful high-level abstract semantic properties (*the code*) of **x**, but it can be somewhat difficult to make use of this information.

* InfoGANs (Chen et al., 2016a): train the code to be more useful

## Plug and Play Generative Networks (PPGNs)

* Nguyen et al., 2016　https://arxiv.org/abs/1612.00005
* Currently is the state of the art generative model that produces high quality images at higher resolutions (227x227) than previous generative models
* PPGNs are new and not yet well understood

## References

Ian Goodfellow. NIPS 2016 Tutorial: Generative Adversarial Networks
Slides: http://www.iangoodfellow.com/slides/2016-12-04-NIPS.pdf
Full report: https://arxiv.org/abs/1701.00160

