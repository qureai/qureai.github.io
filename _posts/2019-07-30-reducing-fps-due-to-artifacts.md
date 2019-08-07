---
layout: post
title: "Improving performance of AI models in presence of artifacts"
updated: 2019-07-30 12:00:00 +0530
author: Mayug Maniparambil, Sasank Chilamkurthy, Swetha Tanamala
categories:
twitter_image: "http://blog.qure.ai/assets/artifact/artifact_discount_illustration.png"
tags:
    - Deep learning
    - Cycle GAN
    - Augmentation
    - CT artifact
    - hemorrhage
    - sub dural
---

> Our deep learning models have become really good at recognizing hemorrhages from Head CT scans. Real-world performance is sometimes hampered by several external factors both hardware-related and human-related. In this blog post, we analyze how acquisition artifacts are responsible for performance degradation and introduce two methods that we tried, to solve this problem.


Medical Imaging is often accompanied by acquisition artifacts which can be subject related or hardware related. These artifacts make confident diagnostic evaluation difficult in two ways:

* by making abnormalities less obvious visually by overlaying on them.
* by mimicking an abnormality.

Some common examples of artifacts are

* Clothing artifact- due to clothing on the patient at acquisition time See fig 1 below. Here a button on the patient's clothing looks like a coin lesion on a Chest X Ray. Marked by red arrow.
<center>
<img width='60%' src="/assets/images/artifact/button-mimicking-a-coin-lesion-copy.png" alt="clothing artifact"/>
<br/>
<small class="caption">Fig 1. A button mimicking coin lesion in Chest X Ray. Marked by red arrow.<a href="https://radiopaedia.org/cases/37852">Source</a>.</small>
</center>

* Motion artifact- due to voluntary or involuntary subject motion during acquisition. Severe motion artifacts due to voluntary motion would usually call for a rescan. Involuntary motion like respiration or cardiac motion, or minimal subject movement could result in artifacts that go undetected and mimic a pathology. See fig 2. Here subject movement has resulted in motion artifacts that mimic subdural hemorrhage(SDH).

<center>
<img width='60%' src="/assets/images/artifact/artefact-mimicking-bilateral-acute-subdural-haematomas-left.jpg" alt="motion artifact"/>
<br/>
<small class="caption">Fig 2. Artifact due to subject motion, mimicking a subdural hemorrhage in a Head CT.<a href="https://radiopaedia.org/cases/18267">Source</a></small>
</center>

<!-- * Beam Hardening artifact- due to X-ray beam being polychromatic, the material acts as a high pass filter resulting in higher beam intensities at points of exit of the beam. This results in dark, bright streaks, mostly in CT, that can cause mimics. For example in CT for coronary artery imaging, beam-hardening artifacts  can mimic the appearance of myocardial perfusion defects and hamper blood flow assesment. See fig 3. The hypodensity due to streak artifact can be misidentified as myocardial hypoperfusion which has a similar appearance.

<center>
<img width='70%' src="/assets/images/artifact/streak-artifact.png" alt="streak artifact"/>
<br/>
<small class="caption">Fig 3. Streak artifact mimicking a myocradial hyperfusion, marked by white arrow in picture B</small>
</center> -->

<!-- * Constant Hardware Artifact- See fig 4. This artifact is caused due to contrast material stuck on gantry window, resulting in dark blotches of hypodensity on the scan, which can be misidentified as ischemia in a Head CT.

<center>
<img width='80%' src="/assets/images/artifact/artifact-ischemia.png" alt="constant hardware artifact ischemia"/>
<br/>
<small class="caption">Fig 4. Artifact due to contrast material stuck on gantry window, mimicking ischemia marked by yellow circles</small>
</center> -->


* Hardware artifact- See fig 3. This artifact is caused due to  air bubbles in the cooling system. There are subtle irregular dark bands in scan, that can be misidentifed as cerebral edema.

<center>
<img width='80%' src="/assets/images/artifact/artifact-edema.png" alt="hardware artifact edema"/>
<br/>
<small class="caption">Fig 3. A hardware related artifact, mimicking cerebral edema marked by yellow arrows.<a href="http://amos3.aapm.org/abstracts/pdf/115-31943-387514-118256-420288666.pdf">Source</a></small>
</center>


Here we are investigating motion artifacts that look like SDH, in Head CT scans. These artifacts result in increase in false positive (FPs) predictions of subdural hemorrhage models. We confirmed this by quantitatively analyzing the FPs of our AI model deployed at an urban outpatient center. The FP rates were higher for this data when compared to our internal test dataset. 
The reason for these false positive predictions is due to the lack of variety of artifact-ridden data in the training set used. Its practically difficult to acquire and include scans containing all varieties of artifacts in the training set.

<!-- Here is a set of SDH images and a set of motion artifact images.
<center>
<img width='150%' src="/assets/images/artifact/sdh_set_artifact_set.png" alt="sdh set and artifact set"/>
<br/>
<small class="caption">Fig 6. Set of SDH images and Set of artifact images</small>
</center> -->

<!-- Qualitative analysis of the False Positive scans led to the conclusion that the models recognized motion artifacts as subdural hemorrhage(SDH) because of similarity in shape and location (both hyperdense areas close to the cranial bones).  -->

<center>
<img width='100%' src="/assets/images/artifact/sdh_mistaken_image.png" alt="artifact mistaken for sdh"/>
<br/>
<small class="caption">Fig 4. Model identifies an artifact slice as SDH because of similarity in shape and location. Both are hyperdense areas close to the cranial bones</small>
</center>

We tried to solve this problem in the following two ways.


* Making the models invariant to artifacts, by explicitly including artifact images into our training dataset.
* Discounting slices with artifact when calculating the probability of bleed in a scan.


#### Method 1. Artifact as an augmentation using Cycle GANs 

We reasoned that the artifacts were misclassified as bleeds because the model has not seen enough artifact scans while training. 
The number of images containing artifacts is relatively small in our annotated training dataset. But we have access to several unannotated scans containing artifacts acquired from various centers with older CT scanners.(Motion artifacts are more prevalent when using older CT scanners with poor in plane temporal resolution). If we could generate artifact ridden versions of all the annotated images in our training dataset, we would be able to effectively augment our training dataset and make the model invariant to artifacts. 
We decided to use a Cycle GAN to generate new training data containing artifacts.

Cycle GAN[[1](#1)] is a generative adversarial network that is used for unpaired image to image translation. It serves our purpose because we have an unpaired image translation problem where X domain has our training set CT images with no artifact and Y domain has artifact-ridden CT images. 

<center>
<img width='80%' src="/assets/images/artifact/cycle_gan_illustration.gif" alt="cycle gan illustration"/>
<br/>
<small class="caption">Fig 5. Cycle GAN was used to convert a short clip of horse into that of a zebra.<a href="https://arxiv.org/pdf/1703.10593.pdf">Source</a></small>
</center>


We curated a A dataset of 5000 images without artifact and B dataset of 4000 images with artifacts  and used this to train the Cycle GAN.

Unfortunately, the quality of generated images was not very good. See fig 6. 
The generator was unable to capture all the variety in CT dataset, meanwhile introducing artifacts of its own, thus rendering it useless for augmenting the dataset.  Cycle GAN authors state that the performance of the generator when the transformation involves geometric changes for ex. dog to cat, apples to oranges etc. is worse when compared to transformation involving color or style changes. Inclusion of artifacts is a bit more complex than color or style changes because it has to introduce distortions to existing geometry. This could be one of the reasons why the generated images have extra artifacts. 

<center>
<img width='120%' src="/assets/images/artifact/cyclegan_generated_images.png" alt="cycle gan images"/>
<br/>
<small class="caption">Fig 6. Sampling of generated images using Cycle GAN. real_A are input images and fake_B are the artifact_images generated by Cycle GAN.</small>
</center>






#### Method 2. Discounting artifact slices
In this method, we trained a model to identify slices with artifacts and show that discounting these slices made the AI model identifying subdural hemorrhage (SDH) robust to artifacts.
A manually annotated dataset was used to train a convolutional neural network (CNN) model to detect if a CT slice had artifacts or not. The original SDH model was also a CNN which predicted if a slice contained SDH. The probabilities from artifact model was used to discount the slices containing artifact and artifact free slices of a scan were used in computation of score for presence of bleed.
See fig 7.

<center>
<img width='120%' src="/assets/images/artifact/artifact_discount_illustration.png" alt="Method 2 illustration"/>
<br/>
<small class="caption">Fig 7. Method 2 Using a trained artifacts model to discount artifact slices while calculating SDH probability.</small>
</center>




#### Results



Our validation dataset contained 712 head CT scans, of which 42 contained SDH. Original SDH model predicted 35 false positives and no false negatives. Quantitative analysis of FPs confirmed that 17 (48%) of them were due to CT artifacts. Our trained artifact model had slice-wise AUC of 96%. Proposed modification to the SDH model had reduced the FPs to 18 (decrease of 48%) without introducing any false negatives. Thus using method 2, all scanwise FP's due to artifacts were corrected.



In summary, using method 2, we improved the precision of SDH detection from 54.5% to 70% while maintaining a sensitivity of 100 percent. 
<center>
<img width='100%' src="/assets/images/artifact/confusion-matrix.png" alt="confusion matrics"/>
<br/>
<small class="caption">Fig 8. Confusion Matrix before and after using artifact model for SDH prediction</small>
</center>


See fig 9. for model predictions on a representative scan.

<center>
<img width='100%' src="/assets/images/artifact/discount_explanation.jpeg" alt="artifact discount explanaation"/>
<br/>
<small class="caption">Fig 9. Model predictions for few representative slices in a scan falsely predicted as positive by original SDH model</small>
</center>

A drawback of Method 2 is that if SDH and artifact are present in the same slice, its probable that the SDH could be missed.




#### Conclusion
Using a cycle GAN to augment the dataset with artifact ridden scans would solve the problem by enriching the dataset with both SDH positive and SDH negative scans with artifacts over top of it. But the current experiments do not give realistic looking image synthesis results. The alternative we used, meanwhile reduces the problem of high false positives due to artifacts while maintaining the same sensitivity.

#### References
1. <a name="1"></a>[Unpaired Image-to-Image Translation using Cycle-Consistent Adversarial Networks](https://arxiv.org/pdf/1703.10593.pdf) by Jun-Yan Zhu et al.


[^1]: Reach out to us at partner@qure.ai
