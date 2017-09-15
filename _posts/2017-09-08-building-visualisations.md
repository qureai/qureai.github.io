---
layout: post
title: A 2017 Guide to Saliency Map Visualization - Part I
author: Rohit Ghosh & Shubham Jain
updated: 2017-07-05 12:00:00 +0530
categories:
tags:
    - Classification
    - Visualisation
description:
twitter_image: "https://imgur.com/VUjIr8r.png"
---

At Qure, we're building deep learning systems which help diagnose abnormalities from medical images. Most of the deep learning models are classification models which predict a probability of abnormality from a scan. However, just the probability score of the abnormality doesn't amount much to a radiologist if it's not accompanied by a visual interpretation of the model's decision.

Interpretability of deep learning models is very much an active area of research and it becomes an even more crucial part of solutions in medical imaging.

In this post, I'll be giving a brief overview of the different saliency feature map visualization for deep learning based classification models along with their pros & cons.

The prevalent visualization methods can be broadly classified into 2 categories:

1. Perturbation based visualizations
2. Backpropagation based visualizations

We would be discussing perturbation based visualisations and their pitfalls in the current post and following up the backpropagation based visualisations in the next post.

## Perturbation visualizations

### Methods

This broad category of perturbation techniques involve perturbing the pixel intensity of input image with minimum noise and observing the change of prediction probability. The underlying principle being that the pixels which contribute maximally to the prediction, once altered, would drop the probability by the maximum amount. Let's have an overview glance at some of these methods - I've linked the paper for your further reading.


In the paper [Visualizing and Understanding Convolutional Networks](https://arxiv.org/pdf/1311.2901.pdf) <sup>1</sup>, published in 2013, Zeiler *et al* used deconvolutional layers - earliest applications of deconvolutional layers - to visualize the activity maps for each layer for different inputs. This helped the authors in understanding object categories responsible for activation in a given feature map. The authors also explored the technique of occluding patches of the network and monitoring the prediction and activations of feature map in the last layer that was maximally activated for unoccluded images.

The same idea was explored in depth in the Samek *et al*
in the 2015 paper [Evaluating the visualization of what a Deep Neural Network has learned](https://arxiv.org/pdf/1509.06321.pdf)<sup>2</sup> where authors suggests that we select the top k pixels by attribution and randomly vary their intensities and then measure the drop in score. If the attribution method is good, then the drop in score should be large.

But there's a slight problem with occluding patches in a systematic way in regular patterns. Often the object that is to be identified gets occluded in parts resulting in inappropriate decision by the network.

These sort of situations were better tackled in the [LIME paper](https://arxiv.org/pdf/1602.04938v1.pdf)<sup>3</sup> that came out in 2016. LIME isn't specifically about computer vision but as such for any classifier. I'll explain how LIME works for vision techniques explicitly  and leave the rest for your reading. Instead of occluding systematic patches at regular intervals, input image is divided into component superpixels thereby ensuring an object is a single superpixel component in itself. Now the predictions are obtained by occluding these superpixel components randomly and prediction probability is obtained. Now a simple regression, using presence and absence of each super pixel component as features and corresponding probability as output, easily assign importance of each super pixel component to overall prediction.

However, these techniques still have some downfalls. Occlusion of patches, systematic or superpixelwise can drastically affect the prediction of networks. For e.g.- at Qure we had trained nets for diagnosing abnormalities from Chest X Rays. Chest X Rays are generally grayscale images and abnormalities could include any thing like unlikely opacity at any place, or enlarged heart etc. Now with partial occlusion, resultant images would be abnormal images since a sudden black patch in the middle of X Ray is very well likely to be an abnormal case.

Instead of discretely occluding, another way to perturb images over a continuous spectrum were explored in a recent paper [Axiomatic Attribution for Deep Networks](https://arxiv.org/pdf/1703.01365.pdf)<sup>4</sup>. These models is in a way hybrid of gradient based methods & perturbation based methods. Here, the images are perturbed over a continuos domain from baseline image () to the current image, and sensitivity  of each pixel  with respect to prediction is integrated over the spectrum to give approximate attribution score for each pixel. That is, for a  given input image X with pixel intensities x<sub>ij</sub> the model takes k images, where each pixel varies over a linearly over a spectrum from 0 to x<sub>ij</sub>. The sensitivity of each pixel ∂a(x)/∂x<sub>ij</sub>  is calculated over  the k images and integrated to find the most sensitive pixels.

Finally, we discuss briefly about the most recent works of Fong *et al* in the paper [Interpretable Explanations of Black Boxes by Meaningful Perturbation](https://arxiv.org/pdf/1704.03296.pdf)<sup>5</sup>. In this paper the authors try and refine the heatmap mask of images, generated by sensitivity maps or otherwise, to fins the minimal mask to describe saliency. The goal of such a technique is to find the smallest subset of the image that preserves the prediction score. The method perturbs the sensitivity heatmap and monitors the probability drop to refine the heatmap to minimum pixels that can preserve the prediction score.

### Discussions

While most of these methods do a decently good job of producing relevant heatmaps. There are couple of drawbacks to perturbation based heatmaps which make them unsuitable for real time deployment.

1. **Computationally Expensive** : Most of these models are run multiple feed-forwards for computing a single heatmap for a given input image. This makes the algorithms slow and expensive and thereby unfit for deployment.

2. **Unstable to artifacts** : As discussed above, a sudden perturbation in the form of a blurred or an occluded patch is something the net is not familiar with from it's training set. The predictions for such a perturbed image becomes skewed a lot making the inferences from such a technique uninterpretable. A screening model trained for looking at abnormalities from normal X Rays, would predict abnormality whenever such a perturbed image is presented to it.

The drawbacks around unstable artifacts are mostly overcome by recent papers<sup>4,5</sup> resulting in much more stable heatmaps.

The backpropagation based methods are much cheaper computationally than perturbation based methods and would be discussed in the next part of the blog post.
