---
layout: post
title: Using Variational AutoEncoders to analyze medical images
author: Karthik Rao
updated: 2016-08-02 12:00:00 +0530
categories:
tags:
    - tutorial
description: 
twitter_image: "/assets/images/ultrasound_torchnet/1_1.jpg"

---

> Unsupervised learning is about infering hidden structure from unlabelled data. There is no supervision in the form of labels, so the model has to figure out how to represent the data and find patterns in it. When we have large quantities of data, and none or few labels, the most effective technique is to use unsupervised methods to learn from the data.

## Why do we need it?
Deep learning methods which are popular today require large quantities of labelled data for training. Labelling is a very time consuming task and in the case of medical images, requires significant time commitment from highly trained physicians with specialized skill-sets such as radiologists and pathologists. Lack of large datasets is by far the biggest challenge in applying Deep Learning techniques in the healthcare domain. One way, this can be mitigated is by using unsupervised methods to train on data without labels. These methods can learn patterns in the data which can then be clustered or used for supervised learning with small datasets.

The volume of medical data is growing at a rapid pace as was mentioned [here](http://blog.qure.ai/notes/on-qure-ai). The data are usually from different modalities (as in case of MRI) or from different enviroments (machines, patients). Building a supervised model to handle all these situations means painstakingly labelling or annotating data from every different scenario. To tackle this problem we need an approach which uses mix of supervised and unsupervised learning.

## Unsupervised Methods in DL
- RBMs ( Restricted Boltzmann Machines )
- Sparse Coding
- AutoEncoders
- Generative Models ( VAEs, GANs )

## What are VAEs ( Variational AutoEncoders )
VAE stands for Variational AutoEncoders. It is a type of generative model which were
introduced here [Auto Encoding Variational Bayes](http://arxiv.org/abs/1312.6114).
The basic idea behind a VAE is to use latent variables to model the data. 

<p align="center">
    <img src="/assets/images/vae/model.png" alt="U-Net Architecture">
    <br>
    <small> VAE as a graphical model </small>
</p>

We have to model P(X) in terms of latent variables to be able to generate samples during
inference.
The model has two parts to it. The Encoder part which models the input data to give Q(z|X).
This is done using a neural network. The Decoder part of the VAE which is also a neural
network learns to regenerate the image using the latent variable. This part is same as
the normal autoencoder. The condition which differentiates the VAE from AE is the condition
applied on the latent variable z. The latent variable z is forced to be as close to N(1,0) 
as possible. This means that during inference time we can just input z sampled from N(1,0)
to produce samples from the distribution.

<p align="center">
    <img src="/assets/images/vae/Encoder_Decoder_VAE.png" alt="U-Net Architecture">
    <br>
    <small> Encoder-Decoder-VAE </small>
</p>

## Running VAE on MNIST

### Data

We will be using the [Keras](https://keras.io) library for running our example. Keras also has an example implementation of [VAE](https://github.com/fchollet/keras/blob/master/examples/variational_autoencoder.py) in their repository. We will be using this as our implementation.
Here we will run an example of an autoencoder.

### Data Preprocessing

MNIST dataset consists of 10 digits from 0-9. For our run we will choose only two digits (7 & 9) because these two digits are difficult to differentiate given their similiar structure.

### Running the Network

<p align="center">
    <img src="/assets/images/vae/twonumbers.png" alt="U-Net Architecture">
    <br>
    <small> Clustered digits </small>
</p>

What we see here are two overlapping clusters of the digits 7 and 9. The clusters are overlapping because of the structural similarity between the digits 7 and 9. We also have to rememeber that these are handwritten digits and are very noisy. 

## Running on Medical Data

### Data

For running VAE on a medical dataset we will use the [Ischemic Lesion dataset](http://www.isles-challenge.org/ISLES2015/). The dataset contains 28 scans of brains which have Ischemic lesion. It also contains the manually segmented masks of the lesion regions. There are four modalities of MRI image. (T1, T2, DWI, FLAIR).

### Data Preprocessing

We will be using the DWI modality only. This is because Ischemic Lesions are visible in the DWI channel. For the preprocessing step we will be using SimpleITK to normalize the images. We also ensure the pixel values are scaled down to (0,1) range.

The next step is to extract 28x28 non overlapping patches from the 3D images. We will be reusing the same type of network as we used for the MNIST example. While extracting 28x28 non overlapping patches we also keep track of which patches have lesion regions and which are healthy patches.

### Running the Network

<p align="center">
    <img src="/assets/images/vae/bl.png" alt="U-Net Architecture">
    <br>
    <small> Partially clustered patches. blue colored dots are lesion patches red colored dots are healthy patches </small>
</p>

We can clearly see the data clustered into two groups. The center blue cluster which is free of any red dots is the lesion patches. The outer regions which consists of red and blue dots. This can be explained by the fact that many lesion patches will have only a few pixels of lesion in them making it difficult to differentiate between them.

## Next

In the next part we will explore how to improve clustering.
