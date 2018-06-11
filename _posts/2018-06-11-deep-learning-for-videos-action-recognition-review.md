---
layout: post
title: "Deep Learning for Videos: A 2018 Guide to Action Recognition"
author: Rohit Ghosh
updated: 2018-06-11 12:00:00 +0530
categories:
tags:
    - Action Recognition
    - Videos
    - Review
description:
twitter_image: ""
---
Medical images like MRIs, CTs (3D images) are very similar to videos - both of them encode 2D spatial information over a 3rd dimension. Much like diagnosing abnormalities from 3D images, action recognition from videos would require capturing context from entire video rather than just capturing information from each frame.

<div style="overflow: auto">
    <div style="float: left" id='volume'>
    </div>
    <div id='video' style="float: right">
    </div>

</div>

<p align="center" class="caption">Fig 1: Left: Example Head CT scan. Right: Example video from a <a href="http://www.wisdom.weizmann.ac.il/%7Evision/SpaceTimeActions.html">action recognition dataset</a>. Z dimension in the CT volume is analogous to time dimension in the video.</p>

In this post, I summarize the literature on action recognition from videos. The post is organized into three sections -

1. [What is action recognition and why is it tough](#sec-1)
2. [Overview of approaches](#sec-2)
3. [Summary of papers](#sec-3)

<a name='sec-1'></a>

## Action recognition and why is it tough?

Action recognition task involves the identification of different actions from video clips (a sequence of 2D frames) where the action may or may not be performed throughout the entire duration of the video. This seems like a natural extension of image classification tasks to multiple frames and then aggregating the predictions from each frame. Despite the stratospheric success of deep learning architectures in image classification (ImageNet), progress in architectures for video classification and representation learning has been slower.

*What made this task tough?*

1. **Huge Computational Cost**
A simple convolution 2D net for classifying 101 classes has just ~5M parameters whereas the same architecture when inflated to a 3D structure results in ~33M parameters.  It takes 3 to 4 days to train a 3DConvNet on UCF101 and about two months on Sports-1M, which makes extensive architecture search difficult and overfitting likely[[1](#1)].

2. **Capturing long context**
Action recognition involves capturing spatiotemporal context across frames. Additionally, the spatial information captured has to be compensated for camera movement. Even having strong spatial object detection doesn't suffice as the motion information also carries finer details. There's a local as well as global context w.r.t. motion information which needs to be captured for robust predictions. For example, consider the video representations shown in Figure 2. A strong image classifier can identify human, water body in both the videos but the nature of temporal periodic action differentiates front crawl from breast stroke.

    <p align="center">
        <img src="/assets/images/actionrec/fronststroke.gif" float="left" width="40%">
        <img src="/assets/images/actionrec/breaststroke.gif" float="right" width="40%">
        <br>
        <small>Fig 2: Left: Front crawl. Right: Breast stroke. Capturing temporal motion is critical to differentiate these two seemingly similar cases. Also notice, how camera angle suddenly changes in the middle of front crawl video.</small>
    </p>


3. **Designing classification architectures**
Designing architectures that can capture spatiotemporal information involve multiple options which are non-trivial and expensive to evaluate. For example, some possible strategies could be
- One network for capturing spatiotemporal information vs. two separate ones for each spatial and temporal
- Fusing predictions across multiple clips
- End-to-end training vs. feature extraction and classifying separately

4. **No standard benchmark**
The most popular and benchmark datasets have been UCF101 and Sports1M for a long time. Searching for reasonable architecture on Sports1M can be extremely expensive. For UCF101, although the number of frames is comparable to ImageNet, the high spatial correlation among the videos makes the actual diversity in the training much lesser. Also, given the similar theme (sports) across both the datasets, generalization of benchmarked architectures to other tasks remained a problem. This has been solved lately with the introduction of Kinetics dataset[[2](#2)].

    <p align="center">
        <img src="http://www.thumos.info/assets/banner.gif" alt="Karpathy_fusion" width="100%">
        <small>Sample illustration of UCF-101. <a href="http://www.thumos.info/">Source</a>.</small>
    </p>

<a name='sec-2'></a>

It must be noted here that abnormality detection from 3D medical images doesn't involve all the challenges mentioned here. The major differences between action recognition from medical images are mentioned as below

1. In case of medical imaging, the temporal context may not be as important as action recognition. For example, detecting hemorrhage in a head CT scan could involve much less temporal context across slices. Intracranial hemorrhage can be detected from a single slice only. As opposed to that, detecting lung nodule from chest CT scans would involve capturing temporal context as the nodule as well as bronchi and vessels all look like circular objects in 2D scans. It's only when 3D context is captured, that nodules can be seen as spherical objects as opposed to cylindrical objects like vessels

2. In case of action recognition, most of the research ideas resort to using pre-trained 2D CNNs as a starting point for drastically better convergence. In case of medical images, such pre-trained networks would be unavailable.

## Overview of approaches
Before deep learning came along, most of the traditional CV algorithm variants for action recognition can be broken down into the following 3 broad steps:

<hr>

1. Local high-dimensional visual features that describe a region of the video are extracted either densely [[3](#3)] or at a sparse set of interest points[[4](#4) , [5](#5)].
2. The extracted features get combined into a fixed-sized video level description. One popular variant to the step is to bag of visual words (derived using hierarchical or k-means clustering) for encoding features at video-level.
3. A classifier, like SVM or RF, is trained on bag of visual words for final prediction

<hr>

Of these algorithms that use shallow hand-crafted features in Step 1, improved Dense Trajectories [[6](#6)] (iDT)  which uses densely sampled trajectory features was the state-of-the-art. Simultaneously, 3D convolutions were used as is for action recognition without much help in 2013[[7](#7)]. Soon after this in 2014, two breakthrough research papers were released which form the backbone for all the papers we are going to discuss in this post. The major differences between them was the design choice around combining spatiotemporal information.

<a name='singlestream'></a>

#### Approach 1: Single Stream Network

 In this [work](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/42455.pdf) [June 2014], the authors - Karpathy et al. - explore multiple ways to fuse temporal information from consecutive frames using 2D pre-trained convolutions.

 <p align="center">
     <img src="/assets/images/actionrec/Karpathy_fusion.jpg" alt="Karpathy_fusion" width="80%">
     <br>
     <small>Fig 3: Fusion Ideas <a href="https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/42455.pdf">Source</a>.</small>
 </p>


As can be seen in Fig 3, the consecutive frames of the video are presented as input in all setups. *Single frame* uses single architecture that fuses information from all frames at the last stage. *Late fusion* uses two nets with shared params, spaced 15 frames apart, and also combines predictions at the end. *Early fusion* combines in the first layer by convolving over 10 frames. *Slow fusion* involves fusing at multiple stages, a balance between early and late fusion. For final predictions, multiple clips were sampled from entire video and prediction scores from them were averaged for final prediction.


Despite extensive experimentations the authors found that the results were significantly worse as compared to state-of-the-art hand-crafted feature based algorithms. There were multiple reasons attributed for this failure:

1. The learnt spatiotemporal features didn't capture motion features
2. The dataset being less diverse, learning such detailed features was tough

<a name='2stream'></a>

#### Approach 2: Two Stream Networks

In this pioneering [work](https://arxiv.org/pdf/1406.2199.pdf) [June 2014] by Simmoyan and Zisserman, the authors build on the failures of the previous work by Karpathy et al. Given the toughness of deep architectures to learn motion features, authors explicitly modeled motion features in the form of stacked optical flow vectors. So instead of single network for spatial context, this architecture has two separate networks - one for spatial context (pre-trained), one for motion context. The input to the spatial net is a single frame of the video. Authors experimented with the input to the temporal net and found bi-directional optical flow stacked across for 10 successive frames was performing best. The two streams were trained separately and combined using SVM. Final prediction was same as previous paper, i.e. averaging across sampled frames.

<p align="center">
    <img src="/assets/images/actionrec/2stream_high.png" alt="2 stream architecture" height="200" width="100%">
    <br>
    <small>Fig 4: Two stream architecture <a href="https://arxiv.org/pdf/1406.2199.pdf">Source</a>.</small>
</p>

Though this method improved the performance of single stream method by explicitly capturing local temporal movement, there were still a few drawbacks:

1. Because the video level predictions were obtained from averaging predictions over sampled clips, the long range temporal information was still missing in learnt features.
2. Since training clips are sampled uniformly from videos, they suffer from a problem of *false label assignemnt*. The ground truth of each of these clips are assumed same as ground truth of the video which may not be the case if the action just happens for a small duration within the entire video.
3. The method involved pre-computing optical flow vectors and storing them separately. Also, the training for both the streams was separate implying end-to-end training on-the-go is still a long road.

<a name='sec-3'></a>

## Summaries

Following papers which are, in a way, evolutions from the two papers (single stream and two stream) which are summarized as below:

1. [LRCN](#lrcn)
2. [C3D](#c3d)
3. [Conv3D & Attention](#attentionandconv3d)
4. [TwoStreamFusion](#2streamfusion)
5. [TSN](#tsn)
6. [ActionVlad](#actionvlad)
7. [HiddenTwoStream](#hidden2stream)
8. [I3D](#i3d)
9. [T3D](#t3d)

The recurrent theme around these papers can be summarized as follows. All of the papers are improvisations on top of these basic ideas.

<p align="center">
    <img src="/assets/images/actionrec/recurrent_theme_high.png" alt="SegNet Architecture">
    <br>
    <small>Recurrent theme across papers. <a href="https://arxiv.org/pdf/1705.07750.pdf">Source</a>.</small>
</p>

For each of these papers, I list down their key contributions and explain them.
I also show their benchmark scores on [UCF101-split1](http://crcv.ucf.edu/data/UCF101.php).

<a name="lrcn"></a>

### LRCN


<ul class="no-bullets">
    <li> Long-term Recurrent Convolutional Networks for Visual Recognition and Description </li>
    <li> Donahue et al. </li>
    <li> Submitted on 17 November 2014 </li>
    <li> <a href="https://arxiv.org/abs/1411.4389">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Building on previous work by using RNN as opposed to stream based designs
* Extension of encoder-decoder architecture for video representations
* End-to-end trainable architecture proposed for action recognition

*Explanation*:

In a previous work by Ng et al[[9](#9)]. authors had explored the idea of using LSTMs on separately trained feature maps to see if it can capture temporal information from clips. Sadly, they conclude that temporal pooling of convoluted features proved more effective than LSTM stacked after trained feature maps. In the current paper, authors build on the same idea of using LSTM blocks (decoder) after convolution blocks(encoder) but using end-to-end training of entire architecture. They also compared RGB and optical flow as input choice and found that a weighted scoring of predictions based on both inputs was the best.

<p align="center">
    <img src="/assets/images/actionrec/LRCNactrec_high.png" alt="2 stream architecture" height="250" float="left">
      <img src="/assets/images/actionrec/GenericLRCN_high.png" alt="2 stream architecture" hspace="30" height="250" width="50%" float="right">
    <br>
    <small>Fig 5: Left: LRCN for action recognition. Right: Generic LRCN architecture for all tasks  <a href="https://arxiv.org/pdf/1411.4389.pdf">Source</a>.</small>
</p>

*Algorithm*:

During training, 16 frame clips are sampled from video. The architecture is trained end-to-end with input as RGB or optical flow of 16 frame clips. Final prediction for each clip is the average of predictions across each time step. The final prediction at video level is average of predictions from each clip.

*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
82.92 | Weighted score of flow and RGB inputs |
71.1  | Score with just RGB |

*My comments*:

Even though the authors suggested end-to-end training frameworks, there were still a few drawbacks

* False label assignment as video was broken to  clips
* Inability to capture long range temporal information
* Using optical flow meant pre-computing flow features separately

Varol et al. in their work[[10](#10)] tried to compensate for the stunted temporal range problem by using lower spatial resolution of video and longer clips (60 frames) which led to significantly better performance.

<a name="c3d"></a>

### C3D

<ul class="no-bullets">
    <li> Learning Spatiotemporal Features with 3D Convolutional Networks </li>
    <li> Du Tran et al. </li>
    <li> Submitted on 02 December 2014 </li>
    <li> <a href="https://arxiv.org/pdf/1412.0767">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Repurposing 3D convolutional networks as feature extractors
* Extensive search for best 3D convolutional kernel and architecture
* Using deconvolutional layers to interpret model decision

*Explanation*:

In this work authors built upon [work](#singlestream) by Karpathy et al. However, instead of using 2D convolutions across frames, they used 3D convolutions on video volume. The idea was to train these vast networks on Sports1M and then use them (or an ensemble of nets with different temporal depths) as feature extractors for other datasets. Their finding was a simple linear classifier like SVM on top of ensemble of extracted features worked better than she ttate-of-the-art algorithms. The model performed even better if hand crafted features like iDT were  used  additionally.


<p align="center">
    <img src="/assets/images//actionrec/c3d_high.png" alt="SegNet Architecture" width="100%" float="right">
    <br>
    <small>Differences in C3D paper and single stream paper <a href="https://arxiv.org/pdf/1412.0767">Source</a>.</small>
</p>

The other interesting part of the work was using deconvolutional layers (explained [here](http://blog.qure.ai/notes/visualizing_deep_learning)) to interpret the decisions. Their finding was that the net focussed on spatial appearance in first few frames and tracked the motion in the subsequent frames.

*Algorithm*:

During training, five random 2-second clips are extracted for each video with ground truth as action reported in the entire video. In test time, 10 clips are randomly sampled and predictions across them are averaged for final prediction.

<p align="center">
    <img src="/assets/images//actionrec/trial.gif" alt="SegNet Architecture" height="200" width="200">
    <br>
    <small>3D convolution where convolution is applied on a spatiotemporal cube.</small>
</p>



*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
82.3 | C3D (1 net) + linear SVM |
85.2 | C3D (3 nets) + linear SVM |
90.4 | C3D (3 nets) + iDT + linear SVM |

*My comments*:

The long range temporal modeling was still a problem. Moreover, training such huge networks is computationally a problem - especially for medical imaging where pre-training from natural images doesn't help a lot.

**Note**: Around the same time Sun et al.[[11](#11)] introduced the concept of factorized 3D conv networks (F<sub>ST</sub>CN), where the authors explored the idea of breaking 3D convolutions into spatial 2D convolutions followed by temporal 1D convolutions. The 1D convolution, placed after 2D conv layer, was implemented as 2D convolution over temporal and channel dimension. The factorized 3D convolutions (F<sub>ST</sub>CN) had comparable results on UCF101 split.

<p align="center">
    <img src="/assets/images/actionrec/fstcn_high.png" alt="SegNet Architecture" width="100%" float="right">
    <br>
    <small>F<sub>ST</sub>CN paper and the factorization of 3D convolution <a href="https://arxiv.org/pdf/1510.00562.pdf">Source</a>.</small>
</p>

<a name="attentionandconv3d"></a>

### Conv3D & Attention


<ul class="no-bullets">
    <li> Describing Videos by Exploiting Temporal Structure </li>
    <li> Yao et al. </li>
    <li> Submitted on 25 April 2015 </li>
    <li> <a href="https://arxiv.org/abs/1502.08029">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Novel 3D CNN-RNN encoder-decoder architecture which captures local spatiotemporal information
* Use of an attention mechanism within a CNN-RNN encoder-decoder framework to capture global context

*Explanation*:

Although this work is not directly related to action recognition, but it was a landmark work in terms of video representations. In this paper the authors use a 3D CNN + LSTM as base architecture for video description task. On top of the base, authors use a pre-trained 3D CNN for improved results.


*Algorithm*:

The set up is almost same as encoder-decoder architecture described in [LRCN](#lrcn) with two differences

1. Instead of passing features from 3D CNN as is to LSTM, 3D CNN feature maps for the clip are concatenated with stacked 2D feature maps for the same set of frames to enrich representation {v<sub>1</sub>, v<sub>2</sub>, ..., v<sub>n</sub>} for each frame i. *Note*: The 2D & 3D CNN used is a pre-trained one and not trained end-to-end like [LRCN](#lrcn)
2. Instead of averaging temporal vectors across all frames, a weighted average is used to combine the temporal features. The *attention  weights* are decided based on LSTM output at every time step.

<p align="center">
    <img src="/assets/images/actionrec/Larochelle_paper_high.png" alt="Attention Mechanism" widht="60%">
    <br>
    <small>Attention mechanism for action recognition. <a href="https://arxiv.org/abs/1502.08029">Source</a>.</small>
</p>

*Benchmarks*:

Score | Comment |
----- | ------- |
 -- | Network used for video description prediction |

*My comments*:

This was one of the landmark work in 2015 introducing attention mechanism for the first time for video representations.


<a name="2streamfusion"></a>

### TwoStreamFusion

<ul class="no-bullets">
    <li> Convolutional Two-Stream Network Fusion for Video Action Recognition </li>
    <li> Feichtenhofer et al. </li>
    <li> Submitted on 22 April 2016 </li>
    <li> <a href="https://arxiv.org/abs/1604.06573">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Long range temporal modeling through better long range losses
* Novel multi-level fused architecture

*Explanation*:

In this work, authors use the base two stream architecture with two novel approaches and demonstrate performance increment without any significant increase in size of parameters. The authors explore the efficacy of two major ideas.

1. Fusion of spatial and temporal streams (how and when) - For a task discriminating between brushing hair and brushing teeth - spatial net can capture the spatial dependency in a video (if it's hair or teeth) while temporal net can capture presence of periodic motion for each spatial location in video. Hence it's important to map spatial feature maps pertaining to say a particular facial region to temporal feature map for the corresponding region. To achieve the same, the nets need to be fused at an early level such that responses at the same pixel position are put in correspondence rather than fusing at end (like in base two stream architecture).

2. Combining temporal net output across time frames so that long term dependency is also modeled.

*Algorithm*:

Everything from two stream architecture remains almost similar except

1. As described in the figure below, outputs of conv_5 layer from both streams are fused by conv+pooling. There is yet another fusion at the end layer. The final fused output was used for spatiotemporal loss evaluation.
    <p align="center">
        <img src="/assets/images/actionrec/fusion_strategies_high.png" alt="SegNet Architecture">
        <br>
        <small>Possible strategies for fusing spatial and temporal streams. The one on right performed better. <a href="https://arxiv.org/abs/1604.06573">Source</a>.</small>
    </p>

2. For temporal fusion, output from temporal net, stacked across time, fused by conv+pooling was used for temporal loss

<p align="center">
    <img src="/assets/images/actionrec/2streamfusion.png" width="100%" alt="SegNet Architecture">
    <br>
    <small>Two stream fusion architecture. There are two paths one for step 1 and other for step 2 <a href="https://arxiv.org/abs/1604.06573">Source</a>.</small>
</p>


*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
92.5 | TwoStreamfusion |
94.2 | TwoStreamfusion + iDT |

*My comments*:
The authors established the supremacy of the TwoStreamFusion method as it improved the performance over C3D without the extra parameters used in C3D.

<a name="tsn"></a>

### TSN

<ul class="no-bullets">
    <li> Temporal Segment Networks: Towards Good Practices for Deep Action Recognition </li>
    <li> Wang et al. </li>
    <li> Submitted on 02 August 2016 </li>
    <li> <a href="https://arxiv.org/abs/1608.00859">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Effective solution aimed at long range temporal modeling
* Establishing the usage of batch normalization, dropout and pre-training as good practices

*Explanation*:

In this work authors improved on two streams architecture to produce state-of-the-art results. There were two major differences from the original paper

1. They suggest sampling clips sparsely across the video to better model long range temporal signal instead of the random sampling across entire video.
2. For final prediction at video-level authors explored multiple strategies. The best strategy was

    1. Combining scores of temporal and spatial streams (and other streams if other input modalities are involved) separately by averaging across snippets
    2. Fusing score of final spatial and temporal scores using weighted average and applying softmax over all classes.

The other important part of the work was establishing the problem of overfitting (due to small dataset sizes) and demonstrating usage of now-prevalent techniques like batch normalization, dropout and pre-trainign to counter the same. The authors also evaluated two new input modalities as alternate to optical flow - namely warped optical flow and RGB difference.

*Algorithm*:

During training and prediction a video is divided into K segments
of equal durations. Thereafter, snippets are sampled  randomly from each of the K segments. Rest of the steps remained similar to two stream architecture with changes as mentioned above.

<p align="center">
    <img src="/assets/images/actionrec/tsn_high.png" alt="SegNet Architecture">
    <br>
    <small>Temporal Segment Network architecture. <a href="https://arxiv.org/pdf/1608.00859.pdf">Source</a>.</small>
</p>

*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
94.0 | TSN (input RGB + Flow ) |
94.2 | TSN (input RGB + Flow + Warped flow) |

*My comments*:

The work attempted to tackle two big challenges in action recognition - overfitting due to small sizes and long range modeling and the results were really strong. However,the problem of pre-computing optical flow and related input modalities was still a problem at large.


<a name="actionvlad"></a>

### ActionVLAD

<ul class="no-bullets">
    <li> ActionVLAD: Learning spatio-temporal aggregation for action classification </li>
    <li> Girdhar et al. </li>
    <li> Submitted on 10 April 2017 </li>
    <li> <a href="https://arxiv.org/pdf/1704.02895.pdf">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Learnable video-level aggregation of features
* End-to-end trainable model with video-level aggregated features to capture long term dependency

*Explanation*:

In this work, the most notable contribution by the authors is the usage of learnable feature aggregation (VLAD) as compared to normal aggregation using maxpool or avgpool. The aggregation technique is akin to bag of visual words. There are multiple learned anchor-point (say c<sub>1</sub>, ...c<sub>k</sub>) based vocabulary representing k typical action (or sub-action) related spatiotemporal features. The output from each stream in two stream architecture is encoded in terms of k-space “action words" features - each feature being difference of the output from the corresponding anchor-point for any given spatial or temporal location.

<p align="center">
    <img src="/assets/images/actionrec/actionvlad.png" alt="SegNet Architecture">
    <br>
    <small>ActionVLAD - Bag of action based visual "words". <a href="https://arxiv.org/pdf/1704.02895.pdf">Source</a>.</small>
</p>

Average or max-pooling represent the entire distribution of points as only a single descriptor which can be sub-optimal for representing an entire video composed of multiple sub-actions. In contrast, the proposed video aggregation represents an entire distribution of descriptors with multiple sub-actions by splitting the descriptor space into k cells and pooling inside each of the cells.

<p align="center">
    <img src="/assets/images/actionrec/pooling_difference_high.png" alt="SegNet Architecture">
    <br>
    <small>While max or average pooling are good for similar features, they do not not adequately capture the complete distribution of features. ActionVlAD clusters the appearance and motion features and aggregates their residuals from nearest cluster centers. <a href="https://arxiv.org/pdf/1704.02895.pdf">Source</a>.</small>
</p>

*Algorithm*:

Everything from two stream architecture remains almost similar except the usage of ActionVLAD layer. The authors experiment multiple layers to place ActionVLAD layer with the late fusion after conv layers working out as the best strategy.



*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
92.7 | ActionVLAD |
93.6 | ActionVLAD + iDT |

*My comments*:
The use of VLAD as an effective way of pooling was already proved long back. The extension of the same in an end-to-end trainable framework made this technique extremely robust and state-of-the-art for most action recognition tasks in early 2017.

<a name="hidden2stream"></a>

### HiddenTwoStream

<ul class="no-bullets">
    <li> Hidden Two-Stream Convolutional Networks for Action Recognition </li>
    <li> Zhu et al. </li>
    <li> Submitted on 2 April 2017 </li>
    <li> <a href="https://arxiv.org/abs/1704.00389">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Novel architecture for generating optical flow input on-the-fly using a separate network

*Explanation*:

The usage of optical flow in the two stream architecture made it mandatory to pre-compute optical flow for each sampled frame before hand thereby affecting storage and speed adversely. This paper advocates the usage of an unsupervised architecture to generate optical flow for a stack of frames.

Optical flow can be regarded as an image reconstruction problem. Given a pair of adjacent frames I<sub>1</sub> and I<sub>2</sub> as input, our CNN generates a flow field V. Then using the predicted flow field V and I<sub>2</sub>, I<sub>1</sub>  can be reconstructed as  I<sub>1</sub><sup>'</sup> using inverse warping such that difference between I<sub>1</sub> and it's reconstruction is minimized.

*Algorithm*:

The authors explored multiple strategies and architectures to generate optical flow with largest fps and least parameters without hurting accuracy much. The final architecture was same as two stream architecture with changes as mentioned:

1. The temporal stream now had the optical flow generation net (MotionNet) stacked on the top of the general temporal stream architectures. The input to the temporal stream was now consequent frames instead of preprocessed optical flow.

2. There's an additional multi-level loss for the unsupervised training of MotionNet

The authors also demonstrate improvement in performance using TSN based fusion instead of conventional architecture for two stream approach.

<p align="center">
    <img src="/assets/images/actionrec/hidden2stream_high.png" alt="SegNet Architecture">
    <br>
    <small>HiddenTwoStream - MotionNet generates optical flow on-the-fly. <a href="https://arxiv.org/pdf/1704.00389.pdf">Source</a>.</small>
</p>

*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
89.8 | Hidden Two Stream |
92.5 | Hidden Two Stream + TSN |


*My comments*:
The major contribution of the paper was to improve speed and associated cost of prediction. With automated generation of flow, the authors relieved the dependency on slower traditional methods to generate optical flow.

<a name="i3d"></a>  

### I3D

<ul class="no-bullets">
    <li> Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset </li>
    <li> Carreira et al. </li>
    <li> Submitted on 22 May 2017 </li>
    <li> <a href="https://arxiv.org/abs/1705.07750">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Combining 3D based models into two stream architecture leveraging pre-training
* Kinetics dataset for future benchmarking and improved diversity of action datasets

*Explanation*:

This paper takes off from where C3D left. Instead of a single 3D network, authors use two different 3D networks for both the streams in the two stream architecture. Also, to take advantage of pre-trained 2D models the authors repeat the 2D pre-trained weights in the 3rd dimension. The spatial stream input now consists of frames stacked in time dimension instead of single frames as in basic two stream architectures.

*Algorithm*:

Same as basic two stream architecture but with 3D nets for each stream

<!-- <p align="center">
    <img src="/assets/images/segmentation-review/segnet_architecture.png" alt="SegNet Architecture">
    <br>
    <small>Segnet Architecture. <a href="https://arxiv.org/abs/1511.00561">Source</a>.</small>
</p> -->

*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
93.4 | Two Stream I3D |
98.0 | Imagenet + Kinetics pre-training |


*My comments*:

The major contribution of the paper was the demonstration of evidence towards benefit of using pre-trained 2D conv nets. The Kinetics dataset, that was open-sourced along the paper, was the other crucial contribution from this paper.

<a name="t3d"></a>

### T3D

<ul class="no-bullets">
    <li> Temporal 3D ConvNets: New Architecture and Transfer Learning for Video Classification </li>
    <li> Diba et al. </li>
    <li> Submitted on 22 Nov 2017 </li>
    <li> <a href="https://arxiv.org/abs/1711.08200">Arxiv Link</a></li>
</ul>

*Key Contributions*:

* Architecture to combine temporal information across variable depth
* Novel training architecture & technique to supervise transfer learning between 2D pre-trained net to 3D net

*Explanation*:

The authors extend the work done on I3D but suggest using a single stream 3D DenseNet based architecture with multi-depth temporal pooling layer (Temporal Transition Layer) stacked after dense blocks to capture different temporal depths The multi depth pooling is achieved by pooling with kernels of varying temporal sizes.

<p align="center">
    <img src="/assets/images/actionrec/ttl_layer_high.png" alt="SegNet Architecture">
    <br>
    <small>TTL Layer along with rest of DenseNet architecture. <a href="https://arxiv.org/abs/1711.08200">Source</a>.</small>
</p>


Apart from the above, the authors also devise a new technique of supervising transfer learning betwenn pre-trained 2D conv nets and T3D. The 2D pre-trianed net and T3D are both presented frames and clips from videos where the clips and videos could be from same video or not. The architecture is trianed to predict 0/1 based on the same and the error from the prediction is back-propagated through the T3D net so as to effectively transfer knowledge.

<p align="center">
    <img src="/assets/images/actionrec/transfer_learning_high.png" alt="SegNet Architecture">
    <br>
    <small>Transfer learning supervision. <a href="https://arxiv.org/abs/1711.08200">Source</a>.</small>
</p>

*Algorithm*:

The architecture is basically 3D modification to DenseNet [[12](#12)] with added variable temporal pooling.


*Benchmarks (UCF101-split1)*:

Score | Comment |
----- | ------- |
90.3 | T3D |
91.7 | T3D + Transfer |
93.2 | T3D + TSN |


*My comments*:

Although the results don't improve on I3D results but that can mostly attributed to much lower model footprint as compared to I3D. The most novel contribution of the paper was the supervised transfer learning technique.

## References

1. <a name="1"></a>[ConvNet Architecture Search for Spatiotemporal Feature Learning](https://arxiv.org/abs/1708.05038) by Du Tran et al.
2. <a name="2"></a>[Quo Vadis, Action Recognition? A New Model and the Kinetics Dataset](https://deepmind.com/research/open-source/open-source-datasets/kinetics/)
3. <a name="3"></a>[Action recognition by dense trajectories](https://hal.inria.fr/inria-00583818/document) by Wang et. al.
4. <a name="4"></a>[On space-time interest points](http://www.irisa.fr/vista/Papers/2005_ijcv_laptev.pdf) by Laptev
5. <a name="5"></a>[Behavior recognition via sparse spatio-temporal features](http://webee.technion.ac.il/control/info/Projects/Students/2012/Itay%20Hubara%20and%20Amit%20Nishri/Book/Papers-STIP/DollarVSPETS05cuboids.pdf) by Dollar et al
6. <a name="6"></a>[Action Recognition with Improved Trajectories](https://www.cv-foundation.org/openaccess/content_iccv_2013/papers/Wang_Action_Recognition_with_2013_ICCV_paper.pdf) by Wang et al.
7. <a name="7"></a>[3D Convolutional Neural Networks for Human Action Recognition](https://pdfs.semanticscholar.org/52df/a20f6fdfcda8c11034e3d819f4bd47e6207d.pdf) by Ji et al.
8. <a name="8"></a>[Large-scale Video Classification with Convolutional Neural Networks](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/42455.pdf) by Karpathy et al.
9. <a name="9"></a>[Beyond Short Snippets: Deep Networks for Video Classification](https://arxiv.org/abs/1503.08909) by Ng et al.
10.  <a name="10"></a>[Long-term Temporal Convolutions for Action Recognition](https://arxiv.org/abs/1604.04494) by Varol et al.
11. <a name="11"></a>[Human Action Recognition using Factorized Spatio-Temporal Convolutional Networks](https://arxiv.org/abs/1510.00562) by Sun et al.
12. <a name="12"></a>[Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993) by Huang et al.


<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.min.js"></script>
<script type="text/javascript" src="/assets/js/ImageStack.js"></script>
<script type="text/javascript">
    var imageList = getImageList('/assets/images/head_ct_study/stacks/QURE-3', 30);
    var stack = new ImageStack({
    images: imageList,
    height: '15rem',
    width: '15rem'
    });
    $('#volume').append(stack);

    var imageList = getImageList('/assets/images/head_ct_study/stacks/denis_walk_avi', 28);
    var stack = new ImageStack({
    images: imageList,
    height: '15rem',
    width: '20rem'
    });
    $('#video').append(stack);
</script>


<style type="text/css">
    /*Scroll Stuff*/
    .custom-scroll{
      float: none;
      margin: 0 auto;
    }

    .custom-scroll::-webkit-scrollbar-track
    {
      -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,0.3);
      border-radius: 5px;
      background-color: #F5F5F5;
    }

    .custom-scroll::-webkit-scrollbar
    {
      width: 12px;
      background-color: #F5F5F5;
    }

    .custom-scroll::-webkit-scrollbar-thumb
    {
      border-radius: 5px;
      -webkit-box-shadow: inset 0 0 6px rgba(0,0,0,.3);
      background-color: #464646;
    }

    td{
        word-wrap: break-word;
        hyphens: auto;
    }
</style>
