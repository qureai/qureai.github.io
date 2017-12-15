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

<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
</script>

>In the [previous post](http://blog.qure.ai/notes/visualizing_deep_learning) we looked at methods to visualize and interpret the decisions made by deep learning models using perturbation based techniques.
To summarize the previous post, perturbation based methods do a good job of explaining decisions but they suffer from
expensive computations and instability to surprise artifacts. In this post, we'll give a brief overview and drawbacks of the various gradient-based algorithms for deep learning based classification models.

We would be discussing the following types of algorithms in the post:

1. Gradient-based algorithms
2. Relevance score based algorithms

In gradient-based algorithms, the gradient of the output with respect to the input is used for constructing the saliency features. The algorithms in this class vary in the way the gradients are modified during backpropagation. Relevance score based algorithms try and attribute the probability score of the model to the relevance of each pixel by backpropagating the probability score instead of the gradient. However, all of these methods involve a single forward and backward pass through the net to generate heatmaps as opposed to multiple forward passes for the perturbation based methods. Evidently, all of these methods are computationally cheaper as well as free of artifacts originating from perturbation techniques.

To illustrate each algorithm, we would be considering a Chest X-Ray (image below) of a patient diagnosed with pulmonary consolidation. Pulmonary consolidation is simply a “solidification” of the lung tissue due to the accumulation of solid and liquid material in the air spaces that would have normally been filled by gas [[1]](#consolidation-defn). The dense material deposition in the airways could have been affected by infection pneumonia (deposition of pus) or lung cancer (deposition of malignant cells) or pulmonary hemorrhage (airways filled with blood) etc. An easy way to diagnose consolidation is to look out for dense abnormal regions with ill-defined borders in the X-ray image.

<p align="center">
    <img width='100%' src="/assets/images/visualisation_2/xray_annotated.png" alt="Annotated_x">
    <br>
    <small>Chest X-ray with consolidation.</small>
</p>

We would be considering this X-ray and one of our models trained for detecting consolidation for demonstration purposes. For this patient, our consolidation model predicts a possible consolidation with XX% probability.

## Gradient Based

### Gradient Input

<ul class="no-bullets">
    <li> Deep inside convolutional networks: Visualising image classification models and saliency maps </li>
    <li> Submitted on 23 Nov 2015 </li>
    <li> <a href="https://arxiv.org/abs/1312.6034">Arxiv Link</a></li>
</ul>

*Explanation*:
Measure the relative importance of input features by calculating the gradient of the output decision with respect to those input features.

There were 2 very similar papers that pioneered the idea in 2013. In these papers  - Saliency features [[2]](#1312.6034) by Simonyan et al. and DeconvNet [[3]](#1311.2901) by Zeiler et al. - authors use directly the gradient of the majority class prediction with respect to input to observe saliency features. The main difference between the above papers was how the authors handle the backpropagation of gradients through non-linear layers like ReLU. In Saliency features paper, the gradients of neurons with negative input were suppressed while propagating through ReLU layers whereas, in the DeconvNet paper, the gradients of neurons with negative gradients were suppressed.

*Algorithm*:
Given an image *I<sub>0</sub>*, a class *c*, and a classification ConvNet with the class score function *S<sub>c</sub>(I)*. The heatmap is calculated as absolute of the gradient of *S<sub>c</sub>* with respect to *I* at *I<sub>0</sub>*
\\[\frac{\partial S_c}{\partial I} |_{I_0} \\]

It is to be noted here, that DeepLIFT paper (which we'll discuss later) explores the idea of gradient * input also as an alternate indicator as it leverages the strength and signal of input
\\[\frac{\partial S_c}{\partial I} |_{I_0} * I_0 \\]

*Shortcomings*:
The problem with such a simple algorithm arises from non-linear activation functions like ReLU, ELU etc. Such non-linear functions being inherently non-differentiable at certain locations have discontinuous gradients. Now as the methods measure partial derivatives with respect to each pixel, the gradient heatmap is inherently discontinuous over the entire image and produces artifacts if viewed as it is. Some of it can be overcome by convolving with a Gaussian kernel. Also, the gradient flow suffers in case of renormalization layers like BatchNorm or max pooling.  


### Guided Backpropagation

<ul class="no-bullets">
    <li> Striving for simplicity: The all convolutional net </li>
    <li> <a href="https://arxiv.org/abs/1412.6806">Arxiv Link</a></li>
</ul>

*Explanation*:
 The next paper [[4]](#1412.6806), by Springenberg et. al, released in 2014 introduces GuidedBackprop, suppressed the flow of gradients through neurons wherein either of activations or gradients were negative. Springenberg et al. show the difference amongst their methods through a beautiful illustration given below. As we discussed, this paper combined the gradient handling of both the Simonyan et al. and Zeiler et al.

 <p align="center">
     <img width="100%" src="/assets/images/visualisation_2/grad_algo.png" alt="GuidedBackprop">
     <br>
     <small>GuidedBackprop Explained <a href="https://arxiv.org/abs/1412.6806">Source</a>.</small>
 </p>

<p align="center">
    <img width="100%" src="/assets/images/visualisation_2/xray-guided_backprop.png" alt="Annotated_x">
    <br>
    <small>Heatmap by GuidedBackprop against original mask.</small>
</p>

 *Shortcomings*:
The problem of gradient flow through ReLU layers still remained a problem at large. Tackling renormalization layers were still an unresolved problem as most of the above papers (including GuidedBackprop paper) proposed mostly fully convolutional architectures (without max pool layers) and batch normalization was yet to 'alchemised' in 2014. Another such fully-convolutional architecture paper was CAM [[6]](#1512.04150)

### Grad CAM

<ul class="no-bullets">
    <li> Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization </li>
    <li> <a href="https://arxiv.org/abs/1610.02391v3">Arxiv Link</a></li>
</ul>

*Explanation*:
 An effective way to circumnavigate the backpropagation problems were explored in the GradCAM [[5]](#1610.02391) by Selvaraju et al. This paper was a generalization of CAM [[6]](#1512.04150) algorithm given by Zhou et al., that tried to describe attribution scores using fully connected layers. The idea is instead of trying to propagate back the gradients, can the activation maps of the final convolutional layer directly be used to infer downsampled relevance map of the input pixels. The downsampled heatmap is upsampled to obtain a coarse relevance heatmap.

*Algorithm*:

<hr>
Let the feature maps in the final convolutional layers be *F<sub>1</sub>*, *F<sub>2</sub>* ... ,*F<sub>n</sub>*. Like before assume image *I<sub>0</sub>*, a class *c*, and a classification ConvNet with the class score function *S<sub>c</sub>(I)*.

1. Weights (*w<sub>1</sub>*, *w<sub>2</sub>* ,..., *w<sub>n</sub>*) for each pixel in the  *F<sub>1</sub>*, *F<sub>2</sub>* ... , *F<sub>n</sub>*  is calculated based on the gradients of each class w.r.t. each feature map such as
\\(w_i = \frac{\partial S_c}{\partial F} |_{F_i} \  \forall i=1 \dots n \\)

2. The weights and the corresponding activations of the feature maps are multiplied to compute the weighted activations (*A<sub>1</sub>*,*A<sub>2</sub>*, ... , *A<sub>n</sub>*) of each pixel in the feature maps.
\\(A_i = w_i * F_i \ \forall i = 1 \dots n \\)

3. The weighted activations across feature maps are added pixel-wise to indicate importance of each pixel in the downsampled feature-importance map \\( H_{i,j} \\) as
\\( H_{i,j} = \sum_{k=1}^{n}A_k(i,j) \ \forall i = 1 \dots n\\)
4. The downsampled heatmap \\( H_{i,j} \\) is upsampled to original image dimensions to produce the coarse-grained relevant heatmap
5. [Optional] The authors suggest multiplying the final coarse heatmap with the heatmap obtained from GuidedBackprop to obtain a finer heatmap.
<hr>



Steps 1-4 makes up the GradCAM method. Including step 5 makes the up the Guided Grad CAM method. Here's how a heat map generated from Grad CAM method looks like. The best contribution from the paper was the generalization of the CAM paper in the presence of fully-connected layers.

*Shortcomings*:
The algorithm although managed to keep out backpropagating the gradients all the way up to inputs - it only propagates the gradients only till the final convolutional layer. The major problem with GradCAM was it's limitation to specific architectures which use the AveragePooling layer to connect convolutional layers to fully connected layers. The other major drawback of GradCAM was the upsampling to coarse heatmap results in artifacts and loss in signal.

<p align="center">
    <img width="100%" src="/assets/images/visualisation_2/xray-grad_cam.png" alt="Annotated_x">
    <br>
    <small>Heatmap by GradCAM against original mask.</small>
</p>


## Relevance score based

There are a couple of major problems with gradient-based methods which can be summarised as follows:

1. **Discontinuous gradients for some non-linear activations** : As explained in the figure below (taken from DeepLIFT paper) the discontinuities in gradients cause undesirable artifacts. Also, the attribution doesn't propagate back smoothly due to such non-linearities resulting in distortion of attribution scores.
    <p align="center">
        <img src="https://imgur.com/yVJK4f5.png" alt="Discontinuous gradients">
        <br>
        <small>Saturation problems of gradient based methods <a href="https://arxiv.org/abs/1704.02685">Source</a>.</small>
    </p>
2. **Saturation of gradients**: As explained through this simplistic network the gradients when either of *i<sub>1</sub>* and  *i<sub>2</sub>* are greater than 1, the gradient of the output w.r.t either them won't change as long as *i<sub>1</sub> + i<sub>2</sub>* > 1
  <p align="center">
      <img src="https://i.imgur.com/XCkwk2I.png" alt="Gradient saturation">
      <br>
      <small>Saturation problems of gradient based methods <a href="https://arxiv.org/abs/1704.02685">Source</a>.</small>
  </p>


#### Layerwise Relevance Propagation

<ul class="no-bullets">
    <li> On Pixel-Wise Explanations for Non-Linear Classifier Decisions by Layer-Wise Relevance Propagation</li>
    <li> <a href="https://arxiv.org/abs/1610.02391v3">Journal Link</a></li>
</ul>

*Explanation*:
To counter these issues relevance score based attribution technique was discussed for the first time by Bach et al. in 2015 in this [[7]](#PLOS) paper. The authors suggested a simple yet strong technique of propagating relevance score and redistribute it in the previous layers in the proportion of the activation of previous layers. The redistribution based on activation scores means we steer clear of the difficulties that arise with non-linear activation layers.

*Algorithm*:

<hr>
This implementation is according to epsilon-LRP[[8]](#1509.06321) where small epsilon is added in denominator to propagate relevance with numerical stability. Like before assume image *I<sub>0</sub>*, a class *c*, and a classification ConvNet with the class score function *S<sub>c</sub>(I)*.

1. Relevance score (*R<sup>f</sup>*) for the final layer is S<sub>c</sub>
2. While input layer is not reached
    - Redistribute the relevance score in the current layer (R<sup>l</sup>) in the previous layer (*R<sup>l+1</sup>*) in proportion of activations.
    Say *z<sub>ij</sub>* is the activation of the j<sup>th</sup> neuron in layer l+1 with input from i<sup>th</sup> neuron in layer l where *z<sub>j</sub>* is 
    \\(z_j = \sum_{i}^{}z_{ij}\\)
  <p align="center">
      <img src="https://imgur.com/2mH1qTq.png" alt="Relevance propagation">
  </p>

<hr>
<p align="center">
    <img width="100%" src="/assets/images/visualisation_2/xray-epslrp.png" alt="Annotated_x">
    <br>
    <small>Heatmap by Epsilon LRP against original mask.</small>
</p>


#### DeepLIFT

<ul class="no-bullets">
    <li>Learning Important Features Through Propagating Activation Differences</li>
    <li> <a href="https://arxiv.org/abs/1704.02685">Journal Link</a></li>
</ul>

*Explanation*:
 The last paper[[9]](#1704.02685) we cover in this series, is based on layer-wise relevance. However, herein instead of directly explaining the output prediction in previous models, the authors explain the difference in the output prediction and prediction on a baseline reference image.The concept is similar to Integrated Gradients which we discussed in the previous post. The authors bring out a valid concern with the gradient-based methods described above - gradients don’t use a reference which limits the inference as gradient-based methods only describe the local behavior of the output at the specific input value, without considering how the output behaves over a range of inputs.

*Algorithms*:
 The reference image (*I<sub>R</sub>*) is chosen as the neutral image, suitable for the problem at hand. For class *c*, and a classification ConvNet with the class score function *S<sub>c</sub>(I)*, *S<sub>Rc></sub>* be the probability for image *I<sub>R</sub>*. The relevance score to be propagated is not *S<sub>c</sub>* but *S<sub>c</sub> -  S<sub>Rc</sub>*.

<!-- Insert DeepLIFT image here -->



## Discussions

We have so far understood both perturbation based algorithms as well as gradient-based methods. Computationally and practically, perturbation based methods are not much of a win until although their performance is relatively uniform and consistent with an underlying concept of interpretability. The gradient-based methods are cheaper computationally and measure the contribution of the pixels in the neighborhood of the original image. But these papers are plagued by the difficulties in propagating back gradients through non-linear and renormalization layers. The layer relevance techniques go a step ahead and directly redistribute relevance in the proportion of activations, thereby steering clear of the problems in propagating through non-linear layers. In order to understand the relative importance of pixels, not only in the local neighborhood of pixel intensities, DeepLIFT redistributes difference of activation of an image and a baseline image.

We'll be following up with a final post on the performance of all the methods discussed in the current and previous post and detailed analysis of their performance.

### References

1. <a name='consolidation-defn'></a>[Consolidation of Lung – Signs, Symptoms and Causes](http://www.healthhype.com/consolidation-in-lung-signs-symptoms-and-causes.html)
2. <a name='1312.6034'></a>Simonyan, K., Vedaldi, A., & Zisserman, A. (2013). [Deep inside convolutional networks: Visualising image classification models and saliency maps](https://arxiv.org/abs/1312.6034). arXiv preprint arXiv:1312.6034.
3. <a name='1311.2901'></a>Zeiler, M. D., & Fergus, R. (2014, September). [Visualizing and understanding convolutional networks](https://arxiv.org/abs/1311.2901). In European conference on computer vision (pp. 818-833). Springer, Cham.
4. <a name='1412.6806'></a>Springenberg, J. T., Dosovitskiy, A., Brox, T., & Riedmiller, M. (2014). [Striving for simplicity: The all convolutional net](https://arxiv.org/abs/1412.6806). arXiv preprint arXiv:1412.6806.
5. <a name='1610.02391'></a>Selvaraju, R. R., Cogswell, M., Das, A., Vedantam, R., Parikh, D., & Batra, D. (2016). [Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization](https://arxiv.org/abs/1610.02391v3).
6. <a name='1512.04150'></a>Zhou, B., Khosla, A., Lapedriza, A., Oliva, A., & Torralba, A. (2016). [Learning deep features for discriminative localization](https://arxiv.org/abs/1512.04150). In Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (pp. 2921-2929).
7. <a name='PLOS'></a>Bach, S., Binder, A., Montavon, G., Klauschen, F., Müller, K. R., & Samek, W. (2015). [On pixel-wise explanations for non-linear classifier decisions by layer-wise relevance propagation](http://journals.plos.org/plosone/article?id=10.1371/journal.pone.0130140). PloS one, 10(7), e0130140.
8. <a name='1509.06321'></a>Samek, W., Binder, A., Montavon, G., Lapuschkin, S., & Müller, K. R. (2017). [Evaluating the visualization of what a deep neural network has learned](https://arxiv.org/pdf/1509.06321.pdf). IEEE transactions on neural networks and learning systems.
9.  <a name='1704.02685'></a>Shrikumar, A., Greenside, P., & Kundaje, A. (2017). [Learning important features through propagating activation differences](https://arxiv.org/abs/1704.02685). arXiv preprint arXiv:1704.02685.
