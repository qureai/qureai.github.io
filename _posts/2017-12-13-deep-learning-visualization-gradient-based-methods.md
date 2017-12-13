---
layout: post
title: Visualizing Deep Learning Networks  - Part II
author: Shubham Jain, Rohit Ghosh
updated: 2017-12-13 12:00:00 +0530
categories:
tags:
    - Classification
    - Visualisation
description:
twitter_image: "https://imgur.com/VUjIr8r.png"
---

>In the [previous post](http://127.0.0.1:4000/notes/visualizing_deep_learning) we looked at methods to visualize and interpret the decisions made by deep learning models using perturbation based techniques. In this post, I'll give a brief overview of the various gradient based algorithms for deep learning based classification models and their drawbacks.

To illustrate each algorithm, we would be considering a Chest X-Ray (image above) of a patient diagnosed with pulmonary consolidation. Pulmonary consolidation is simply a “solidification” of the lung tissue due to accumulation of solid and liquid material in the air spaces that would have normally been filled by gas [[1]](#consolidation-defn).The dense material deposition in the airways could have been affected by infection pneumonia (deposition of pus) or lung cancer (deposition of malignant cells) or pulmonary hemorrhage (airways filled with blood) etc. An easy way to diagnose consolidation is to look out for dense abnormal regions with ill-defined borders in the X-ray image.


In the last post we had considered detection of pleural effusion and interpretability of our deep learning models in terms of pleural effusion. However,pleural effusion is generally along the costophrenic angles of the lung while consolidation can be anywhere in the lung field. Hence, being able to locate consolidation is a more difficult task compared to pleural effusion.

We would be discussing the following types of algorithms in the post:
1. Gradient based algorithms
2. Relevance score based algorithms

In gradient based algorithms, the gradient of the output with respect to the input is used for constructing the saliency features. The algorithms in this class vary in the way the gradients are modified during backpropagation.Relevance score based algorithms try and attribute the probability score of the model by backpropagating the probability score instead of the gradient.


We would be considering this X-ray and one of our models trained for detecting consolidation for demonstration purposes. For this patient, our consolidation model predict a possible consolidation with XX% probability.

## Gradient Based


#### Gradient Input

What's better than measuring than relative importance of input features than to measure the gradient of the output decision with respect to input features?

There were 2 very similar papers that pioneered the idea in 2013. In these papers  - [Saliency features](https://arxiv.org/abs/1312.6034) by Simonyan et al. and [DeconvNet](https://arxiv.org/abs/1311.2901) by Zeiler et al. - authors use directly the gradient of the majority class prediction with respect to input to observe saliency features. The methods proposed the absolute value of gradient as an indicator of attribution scores. It is to be noted here, that DeepLIFT paper (which we'll discuss later) explores the idea of gradient * input also as an alternate indicator opposed to abs(gradient), as it leverages the strength and signal of input.


The problem with such a simple algorithm arises from non-linear activation functions like ReLU, eLU etc. The functions being inherently non-differentiable at certain locations, the gradients are not continuous over the entire range.

#### Guided Backpropagation

The main difference between the above papers was how the author handles the backpropagation of gradients through non-linear layers like ReLU. In Saliency features paper, the gradients of neurons with negative input were suppressed while propagating through ReLU layers whereas in the DeconvNet paper, the gradients of layers with negative gradients were suppressed. The next paper, released in 2014, [GuidedBackprop](https://arxiv.org/abs/1412.6806), suppressed flow of gradients through neurons wherein either of activations or gradients were negative.


#### Grad CAM
However, the problem of gradient flow through ReLU layers still remained a problem at large. Also, the gradients flow suffers in case of renormalisation layers like Batchnormalisation or Maxpooling. An effective way to circumnavigate these problems were explored in the [GradCAM](https://arxiv.org/abs/1610.02391). This paper was generalization of CAM paper that tried to describe discriminative powers of image using fully connected layers.

In this paper, a coarsegrained feature-importance map  is computed in the following steps

<hr>
1. A weighted activation for each pixel in the feature maps in the final convolutional layer is calculated  based on the gradients of each class w.r.t. each feature map

2. The weights and the corresponding activations of the feature maps are used to compute the weighted activations of each pixel in the feature maps.

3. The weighted activations across feature maps are added pixel-wise to indicate importance of each pixel in the coarsegrained feature-importance maps

4. The coarsegrained map is upsampled to original image dimensions to produce the relevant heatmap

5. [Optional] The authors suggest multiplying the final coarse heatmap with the heatmap obtained from GuidedBackprop to obtain a finer heatmap.

<hr>

Steps 1-4 makes up the GradCAM method. Including step 5 makes the up the Guided Grad CAM method. Here's how a heamap generated from Grad CAM method looks like. The best contribution from the paper was the generalization of the CAM paper in the presence of fully-connected layers.

## Relevance score based

There are couple of major problems with gradient based methods which can be summarised as follows:

1. **Discontinuous gradients for some non-linear activations** : As explained in the figure below (taken from DeepLIFT paper) the discontinuities in gradients cause undesirable artifacts. Also the attribution doesn't propagate back smoothly due to such non-linearities resulting in distortion of attribution scores.

2. **Saturation of gradients**: As explained through this simplistic network the gradients when either of i<sub>1</sub> and  i<sub>2</sub> are greater than 1, the gradient of the output w.r.t either them won't change as long as i<sub>1</sub> + i<sub>2</sub> > 1


#### Layerwise Relevance Propagation

To counter these issues relevance score based attribution technique was discussed for the first time by Bach et al. in 2015 in this [Layereise Relevance Propagation](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0130140) paper. The authors suggested a simple yet strong technique of propagating relevance score and redistribute it in the previous layers in accordance with the activation of previous layers. The redistribution based on activation scores means we steer clear of the difficulties that arise with non-linear activation layers.

<hr>

1. Compute the relevance score of the class as the final probability of the class we're interested in
2. Till input layer is reached
    - Redistribute the relevance score in the previous layers in terms of activations of previous layers

<hr>

#### DeepLIFT

[DeepLIFT](https://arxiv.org/abs/1704.02685), the last one we cover in this series, is based on layer wise relevance and was authored by Shrikumar et al.  However, herein instead of directly explaining the output prediction in previous models, the authors explain the difference in the output prediction and prediction on a baseline reference image. The reference image is chosen as neutral image, suitable for the problem at hand. Instead of propagating the relevance score as per pervious's layer's activation, DeepLIFT redistributes the difference in probabilities (of image and baseline image) as per per previous layer's differential activation (difference in activation with image and baseline image)

The concept is similar to Integrated Gradients which we discussed in the previous post. The authors bring out a valid concern with the gradient based methods described above - gradients don’t use a reference which limits the inference as gradient based methods only describe the local behaviour of the output at the specific input value, without
considering how the output behaves over a range of inputs.




### References

1. <a name='consolidation-defn'></a>[Consolidation of Lung – Signs, Symptoms and Causes](http://www.healthhype.com/consolidation-in-lung-signs-symptoms-and-causes.html)
