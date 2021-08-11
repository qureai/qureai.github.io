---
layout: post
title: "qCT-Lung: Catching lung cancer early
"
author: |
    Ankit Modi, Reena Rajan
categories:
twitter_image: "https://blog.qure.ai/assets/images/qct_lung/banner_lateral.png"
tags:
    - qCT
    - Deep Learning
    - AI
    - Lung cancer
    - qCT-Lung
    - Nodule
---

>In this blog, we will unbox _qCT-Lung_ – our latest AI powered product that analyses Chest CT scans for lung cancer. At Qure.ai, we have always taken a holistic approach towards building solutions for lung health. _qXR_ provides automated interpretation of chest X-rays and is complemented by _qTrack_, a disease & care pathway management platform with AI at its core. _qCT-Lung_ augments our lung health suite with the ability to detect lung nodules & emphysema on chest CTs and analyze their malignancy. It can quantify & track nodules over subsequent scans. _qCT-Lung_ is a CE certified product.


<center>
<img width='100%' src="/assets/images/qct_lung/banner_lateral.png" alt="qCT-Lung banner"/>
<br/>
<small class="caption"><i>qCT-Lung: Catching lung cancer early</i></small>
</center>

# Introduction
Medical Imaging has seen the biggest healthcare advancements in artificial intelligence (AI) and lung health has been at the forefront of these improvements. Lung health has also been a key domain of our product portfolio. We've built AI algorithms like _qXR_, which provides automated interpretation of chest X-rays. We augmented its capabilities with _qTrack_ – our AI powered disease management platform, which solves for active case finding & tracking patients in care pathways. These applications have empowered healthcare practitioners at all stages of the patient journey in TB, Covid-19 & lung cancer screenings.

We’re adding a new member to our lung health suite: _qCT-Lung_. Its AI-powered algorithms can interpret chest CTs for findings like lung nodules & emphysema, and analyze their malignancy. It empowers clinicians to detect lung cancer in both screening programs as well as opportunistic screening settings.

_qXR_ & _qCT-Lung_'s abilities to detect lung cancer in chest X-rays & CTs complement _qTrack_'s disease management & patient tracking capability. Together, they round up our lung health portfolio to make it a comprehensive, powerful & unique offering.


# Lung Cancer - The most fatal cancer

Lung cancer is the second most common cancer in both men & women.  2.2 million people were diagnosed with lung cancer worldwide in 2020 [[1](#1)]. With 1.74 million deaths in 2020, lung cancer is also the leading cause of cancer related deaths (18.4%) resulting in more deaths than the second and third deadliest cancers combined (colorectal - 9.2% & stomach - 8.2%).

Future projections don't look good either. Lung cancer incidents are projected to rise by 38%  and the mortality is projected to rise by 39%  by 2030 [[2](#2)].


There are two main types of lung cancer:
- Non-small cell lung cancer (NSCLC): NSCLC comprises of 80-85% of all lung cancer cases. Their major subtypes are adenocarcinoma, squamous cell carcinoma, and large cell carcinoma. They are grouped together because of shared similarity in treatment & prognoses.

- Small cell lung cancer (SCLC): SCLC tends to grow and spread faster than NSCLC. 10-15% of all lung cancers are SCLC.

There are also cancers that start in other organs (like breast) and spread to lung, but they don’t come under the vicinity of lung cancer.

# Early detection & outcomes
### Survival rates
The 5-year survival is a measure of what percent of people live at least 5 years after the cancer is found. The 5-year survival rates for both NSCLC & SCLC look as follows [[4](#4)]:

<center>
<img width='100%' src="/assets/images/qct_lung/survival_rate.png" alt="Lung Cancer Survival rates"/>
<br/>
<small class="caption">Lung Cancer Survival rates</small>
</center>

The data shows that lung cancer mortality can be reduced significantly if detected & treated early.

### Early detection

Data from England shows that the chances of surviving for at least a year decrease from 90% to 20% for the earliest to most advanced stage of lung cancer [[5](#5)]. WHO elaborates on two components for early detection [[6](#6)]:

#### Early diagnosis

Early identification of cancer results in better response to treatment, greater chances of survival, lesser morbidity & less expensive treatment. It comprises of 3 components:
- Being aware of early symptoms of lung cancer like persistent cough, coughing up blood, pain in breathing, continuous breathlessness, loss of appetite, unexplained weight loss, etc [[7](#7)].
- access to clinical evaluation and diagnostic services
- timely referral to treatment services.

#### Screening

Screening is aimed at identifying individuals with findings suggestive of lung cancer before they have developed symptoms. Further tests are conducted to establish if the diagnosis should be followed or referral for treatments should be made. They’re effective because symptoms of lung cancer do not appear until the disease is already at an advanced stage.


# Lung Cancer Screening Programs

Screening programs use regular chest X-rays and low dose CT/ CAT scans to study people at higher risk of getting lung cancer. CT scans have proven to be more effective than X-rays. They resulted in a 20% reduction in lung cancer-specific deaths as compared to X-rays [[2](#2)]. However, X-rays are more accessible and cheaper and thus, are important for low-income settings.

The U.S. Preventive Services Task Force (USPSTF) recommends yearly lung cancer screening with LDCT for people who [[9](#9)]:
- Have a 20 pack-year  or more smoking history, and
- Smoke now or have quit within the past 15 years, and
- Are between 50 and 80 years of age.


# Challenges in radiology screening today

Chest CTs are comparatively more accurate than chest X-rays for identification of thoracic abnormalities. This is because of lack of superimposition, greater contrast, and spatial resolution. However, there are many challenges in identifying & reporting lung cancer on Chest CTs. These challenges can be divided into the following categories:

### Misdiagnosis

A study revealed that 42.5% of malpractice suits on radiologists are because of failure to diagnose lung cancer [[14](#14)]. These lawsuits can cost as high as $10M [[15](#15)]. Misdiagnosis can occur due to two reasons [[11](#11)]:

- Lesion characteristics: Small dimension, poor conspicuousness , ill-defined margins and central location are the most common lesion characteristics that lead to missed lung cancers incidences.

- Observer Error: There are multiple sources of observer error like:
    - _Recognition_ error consists of missed detection of lesions.
    - _Decision making_ error includes cases of inaccurately interpreted characteristics of a detected malignant lesion as benign/ normal.
    - _Satisfaction of search_ error occurs when the observer fails to continue to search for subsequent abnormalities after identifying an initial one. Typically, this happens due to two possible mechanisms: ceasing the search for other abnormalities early in a positive exam and focusing on the wrong part of the exam.

### Analysis & Tracking

Post detection of a lesion, a major challenge is to analyse its characteristics and determine malignancy. Even when the lesion’s malignancy is determined correctly, tracking them over subsequent scans is challenging for screening programs due to lack of appropriate CADs & tools.

### Structured reporting & Follow-ups

Structured reporting helps to categorize results and recommend follow-ups based on chances of malignancy by considering size, appearance, and growth of the lesion. Further, volume measurement & volume doubling times (VDT) have been proposed in the management protocol of NELSON lung cancer screening trial [[13](#13)]. All these metrics are challenging to calculate & report in absence of appropriate tools.  This makes it hard to standardize follow up recommendations based on guidelines like Fleishner Society or Lung-RADS scores.

### Detecting relevant co-findings

Certain other pulmonary findings like COPD (chronic obstructive pulmonary disease) are an independent risk factor for lung cancer. Lung cancer screening subjects have a high prevalence of COPD which accounts for significant morbidity and mortality.
One of the major benefits of emphysema (a type of COPD) quantification in lung cancer screening patients is an earlier diagnosis and therapy of COPD with smoking cessation strategies. It can potentially lead to less COPD-related hospitalizations.

### Time constraints

Interpreting CT scans is a time intensive process. A CT scan can have 16 to 320 slices compared to one or two images in an X-ray. Radiologists spend 5-10 minutes to interpret & report each CT scan.

For chest CTs, detecting small nodules through hundreds of slices consumes a lot of time. There are tools that help with some of these issues but none of them solve for lung cancer screening comprehensively.



# qCT-Lung: AI powered lung nodule interpretation tool

qCT-Lung empowers lung cancer screening programs and facilitates opportunistic screening by detecting malignant lesions using AI. It is aimed at helping clinicians with all the issues discussed in the previous section - misdiagnosis, analysis, reporting, detection of co-findings & reducing time constraints. The algorithm is trained on more than 200k chest CTs and can detect, analyze, monitor and auto-report lung nodules.
This is how qCT-Lung assists clinicians in interpreting chest CTs for lung nodules:

### Detecting & Quantifying Lesions

<center>
<img width='100%' src="/assets/images/qct_lung/msc.jpg" alt="Secondary Capture"/>
<br/>
<small class="caption">Secondary Capture with detected nodule</small>
</center>

qCT can distinguish lung lesions from complex anatomical structures on lung CTs and minimize instances of letting lung cancers go undetected, by preventing nodules from being overlooked on scans.  Faster and more accurate detection helps decrease time to treatment and improves patient outcomes.

#### Advantages:
- Detects lung nodules as small as 3mm with high accuracy (sensitivity of 95% and less than 1 false positives per scan)
- Detects emphysema
- Reduces the chance of missed nodules and under-diagnosis
- Auto quantification of diameter & volume

### Analysis & Growth Monitoring

<center>
<img width='100%' src="/assets/images/qct_lung/summary_sc.jpg" alt="Nodule Analysis & Malignancy Risk"/>
<br/>
<small class="caption">Nodule Analysis & Malignancy Risk</small>
</center>

qCT analyzes nodule characteristics to determine malignancy. The algorithm also assigns a malignancy risk score for each of the nodules that helps clinicians plan treatments.

#### Advantages:
- Analyses localization, spiculation, size, calcification & texture (solid, sub-solid & ground glass nodules)
- Calculates Malignancy Risk Score
- Measures volumetry and tracks growth of nodules
- Predicts nodule volume doubling time
- Precisely quantifies response to treatment


### Reporting Assistance

<center>
<img width='100%' src="/assets/images/qct_lung/qCT_lung_report.png" alt="Pre-filled report with suggested follow-ups"/>
<br/>
<small class="caption">Pre-filled report with suggested follow-ups</small>
</center>

qCT-Lung utilizes pre-populated results to offer clinicians faster reporting, that reduces time to treatment and further diagnosis. It can also recommend timelines for follow-up scans.

#### Advantages:
- Automates reporting to save time and reduce reporting workload
- Pre-fed with the Lung-RADS & Fleischer Society Guidelines to suggest follow-ups.

### Modifiable Results
qCt-Lung also offers a lung nodule reporting platform that is designed for screening programs. It enables clinicians to choose which nodules to include in the report and also to add new nodules. The platform pre-populates the image viewer with nodules identified by qCT-Lung. Clinicians can then exclude or add new nodules to this list. The final list after these changes is sent to the RIS.

#### Advantages
The platform empowers physicians to modify the results generated by qCT-Lung and report on what's profoundly important for them.


### Qure’s Lung Health suite: A 3–pronged approach

<br>
<center>
<img width='100%' src="/assets/images/qct_lung/lung_health_logo.png" alt="Qure's Lung Health Suite"/>
<br/>
<!-- <small class="caption">Qure's Lung Health Suite</small> -->
</center>

<br>
We have built an end-to-end portfolio for managing lung cancer screenings in all kinds of resource-settings.  Lung cancer screening has many challenges. While CTs are recommended imaging modality, resource limited settings must depend on X-rays for its cost benefit and easy availability. Patient tracking, disease management and long term follow up for individuals with high-risk cases are also a challenge. Our comprehensive lung health suite takes care of these challenges.

1.	qXR – our chest X-ray interpretation algorithm detects lung nodules on X-rays with high accuracy.
2.	qCT-lungs does the same on chest CTs.
3.	qTrack is built and designed for community screening to track an individual’s disease and manage care pathways.

Together, these solutions can help in active case screening, monitoring disease progression, reducing turn-around-time, linking care to treatment, & improving care pathways.

<br>
Write to us at <qct-lung@qure.ai> to integrate qCT-Lung in your lung nodule management pathway.









# References
1.	<a name="1"></a>[Cancer.org: Key Statistics for Lung Cancer](https://www.cancer.org/cancer/lung-cancer/about/key-statistics.html)

2.	<a name="2"></a>[Chestnet.org: Lung Cancer Fact Sheet](https://www.chestnet.org/newsroom/chest-news/2020/07/world-lung-cancer-day-2020-fact-sheet)

3.	<a name="3"></a>[Cancer.org: What Is Lung Cancer?](https://www.cancer.org/cancer/lung-cancer/about/what-is.html)

4.	<a name="4"></a>[Cancer.org: Lung Cancer Survival Rates](https://www.cancer.org/cancer/lung-cancer/detection-diagnosis-staging/survival-rates.html)

5.	<a name="5"></a>[Cancer Research UK: Why is early diagnosis important?](https://www.cancerresearchuk.org/about-cancer/cancer-symptoms/why-is-early-diagnosis-important)

6.	<a name="6"></a>[WHO: Fact Sheet on Cancer](https://www.who.int/news-room/fact-sheets/detail/cancer)

7.	<a name="7"></a>[NHS UK: Lung Cancer Symptoms](https://www.nhs.uk/conditions/lung-cancer/symptoms/)

8.	<a name="8"></a>[Cancer.org: Can Lung Cancer Be Found Early?](https://www.cancer.org/cancer/lung-cancer/detection-diagnosis-staging/detection.html)

9.	<a name="9"></a>[CDC: Who Should Be Screened for Lung Cancer?](https://www.cdc.gov/cancer/lung/basic_info/screening.htm)

10.	<a name="10"></a>National Lung Screening Trial Research Team, Aberle DR, Berg CD, et al. "[The National Lung Screening Trial: overview and study design.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3009383/)" Radiology. 2011;258(1):243–253.

11.	<a name="11"></a>del Ciello A, et al. "[Missed lung cancer: when, where, and why? Diagnos.](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5338577/)" Intervent. Radiol. 2017;23:118–126. doi: 10.5152/dir.2016.16187.

12.	<a name="12"></a>Widmann, G. "[Challenges in implementation of lung cancer screening—radiology requirements.](https://link.springer.com/article/10.1007/s12254-019-0490-9)" memo 12, 166–170 (2019).

13.	<a name="13"></a>Dong Ming Xu, Hester Gietema, Harry de Koning, René Vernhout, Kristiaan Nackaerts, Mathias Prokop, Carla Weenink, Jan-Willem Lammers, Harry Groen, Matthijs Oudkerk, Rob van Klaveren, "[Nodule management protocol of the NELSON randomised lung cancer screening trial](https://www.sciencedirect.com/science/article/abs/pii/S016950020600434X)", Lung Cancer, Volume 54, Issue 2, 2006, Pages 177-184, ISSN 0169-5002

14.	<a name="14"></a>Baker SR, Patel RH, Yang L, Lelkes VM, Castro A 3rd. "[Malpractice suits in chest radiology: an evaluation of the histories of 8265 radiologists.](https://pubmed.ncbi.nlm.nih.gov/24149862/)" J Thorac Imaging. 2013 Nov;28(6):388-91.

15.	<a name="15"></a>[HealthImaging: Lung cancer missed on CT prompts $10M lawsuit against U.S. government](https://www.healthimaging.com/topics/healthcare-economics/misread-ct-scan-prompts-10m-lawsuit-against-government)
