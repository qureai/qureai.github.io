---
layout: post
title: "Re-purposing qXR for COVID-19"
updated: 2020-08-04 18:00:00 +0530
author: qXR team
categories:
twitter_image: "http://blog.qure.ai/assets/images/qureai.blog/assets/images/qxr_covid19/sample_output.png"
tags:
    - qXR
    - Chest X-Rays
    - Deep Learning
    - COVID-19
---

> We have re-purposed qXR to detect signs of COVID-19. We have validated it on a test set of 11479 CXRs with 515 confirmed positives for COVID-19. We have observed an AUC of `0.9 (95% CI : 0.88 - 0.92)` on this testset. At our operating thresholds, we have observed the sensitivity to be `0.912 (95% CI : 0.88 - 0.93)` and specificity to be `0.775 (95% CI : 0.77 - 0.78)`. qXR for COVID-19 is used at over **35 sites across the world** for triaging suspect patients, to monitor the progress of the infection in admitted patients and impacting over **4000 lives** and more.

The emergence of the COVID-19 pandemic has already caused a great deal of disruption around the world. Healthcare systems across the countries are overwhelmed as we speak, in the face of WHO guidance to ‘[test, test, test](https://www.who.int/dg/speeches/detail/who-director-general-s-opening-remarks-at-the-media-briefing-on-covid-19---16-march-2020){:target="_blank"}’. Many countries are facing a severe shortage of [Reverse Transcription Polymerase Chain Reaction](https://en.wikipedia.org/wiki/Reverse_transcription_polymerase_chain_reaction){:target="_blank"} (RT-PCR) tests. There has been a lot of debate around the role of radiology — both chest X-rays and chest CT scans — as an alternative or supplement to RT-PCR in triage and diagnosis. Opinions on the subject range from ‘[Radiology is  fundamental in this process](https://healthcare-in-europe.com/en/news/imaging-the-coronavirus-disease-covid-19.html){:target="_blank"}’ to ‘[framing CT as pivotal for COVID-19 diagnosis is a distraction during a pandemic, and possibly dangerous](https://www.thelancet.com/journals/lancet/article/PIIS0140-6736(20)30728-5/fulltext){:target="_blank"}’.  

<h1 align="left"> Role of Radiography </h1>

The humble chest X-ray has emerged as the frontline screening and diagnostic tool for COVID-19 infection in a few countries and is used in conjunction with clinical history and key blood markers such as C-Reactive Protein (CRP) test and lymphopenia. Ground glass opacities and consolidations which are peripheral and bilateral in nature are attributed to be the most common findings with respect to COVID related infections on CXRs and chest CTs. CXRs can help in identifying early stage infections and can be used as a triage tool in most cases. In fact, Italian and British hospitals are employing CXR as a [first-line triage tool](https://blogs.bmj.com/bmj/2020/03/20/lessons-from-the-frontline-of-the-covid-19-outbreak/){:target="_blank"} due to [high RT-PCR turnaround times](https://www.youtube.com/watch?v=zV5WPIJ3UxE){:target:"_blank"}. A [recent study](https://pubs.rsna.org/doi/10.1148/radiol.2020201160){:target="_blank"} which examined CXRs of 64 patients found that in 9% of cases, initial RT-PCR was negative whereas CXRs showed abnormalities. All these cases subsequently tested positive for RT-PCR within 48 hours. The American college of Radiology recommends considering  [portable chest X-rays](https://www.acr.org/Advocacy-and-Economics/ACR-Position-Statements/Recommendations-for-Chest-Radiography-and-CT-for-Suspected-COVID19-Infection){:target="_blank"} to avoid bringing patients to radiography rooms. The Canadian Association of Radiologists suggest the use of mobile chest X-ray units for [preliminary diagnosis of suspected cases](https://journals.sagepub.com/doi/10.1177/0846537120914428){:target="_blank"} and to monitor critically ill patients, but have reported that no abnormalities are seen on CXRs in the initial stages of the infection.


<center>
<img width='100%' src="/assets/images/qxr_covid19/bsti_radiology_support_tool.png" alt="qXR sample"/>
<br/>
<small class="caption"><i>Radiology decision tool for suspected COVID-19 - <a href="https://www.bsti.org.uk/media/resources/files/NHSE_BSTI_APPROVED_Radiology_on_CoVid19_v6_modified1__-_Read-Only.pdf">The British Society of Thoracic Imaging</a></i></small>
</center>


As of today, despite calls for opening up imaging data on COVID-19 and [outstanding efforts](https://threadreaderapp.com/thread/1243928581983670272.html){:target="_blank"} from physicians on the front-lines, there are limited X-ray or CT datasets in the public domain pertaining specifically to COVID. These datasets remain insufficient  to train an AI model for COVID-19 triage or diagnosis but are potentially useful in evaluating the model – provided the model hasn’t been trained on the same data sources. 

<h1 align="left"> Leveraging qXR </h1>

>*Over the last month, customers, collaborators, healthcare providers, NGOs, state and national governments have reached out to us for help with COVID detection on chest X-rays and CTs*.

In response, we have adapted our tried-and-tested **chest X-ray AI tool, qXR** to identify findings related to COVID-19 infections. qXR is trained using a dataset of 3.5 million X-rays (that included bacterial and viral pneumonia and many other chest X-ray findings) and is currently deployed in over **25 countries**. qXR detects the following findings that are indicative of COVID-19: `Opacities` and `Consolidation` with bilateral and peripheral distribution and the following findings that are contra-indicative of COVID-19: `hilar enlargement`, `discrete pulmonary nodule`, `calcification`, `cavity` and `pleural effusion`.

These CE-marked capabilities have been leveraged for a COVID-19 triage product that is highly sensitive to COVID-19 related findings. This version of qXR gives out the likelihood of a CXR being positive for COVID-19, called Covid risk. Covid risk is computed using a post processing algorithm which combines the model outputs for the above mentioned findings. The algorithm is tuned on a set of 300 COVID-19 positives and 300 COVID-19 negatives collected from India and Europe.

 Most new qXR users for COVID-19 are using it as a **triage tool**, often in settings with limited diagnostic resources. This version of qXR also localizes and quantifies the affected region. This capability is being used to **monitor the progression of infection** and to evaluate response to treatment in new clinical studies.


<center>
<img src="/assets/images/qxr_covid19/sample_output.png" alt="qXR sample"/>
<br/>
<small class="caption"><i>Sample Output of qXR</i></small>
</center>

### Evaluation of the algorithm

We have created an independent testset of `11479 CXRs` to evaluate our algorithm. The [WHO](https://www.who.int/publications-detail/laboratory-testing-for-2019-novel-coronavirus-in-suspected-human-cases-20200117){:target="_blank"} recommends a confirmatory diagnosis of COVID-19 using Reverse-Transcriptase Polymerase Chain Reaction (RT-PCR) - a specialised Nucleic Acid Amplification Test (NAAT) which looks for unique signatures using primers designed for the COVID-19 RNA sequence. Positives in this test set are defined as any CXR that is acquired while the patient has tested positive on RT-PCR test based on sputum/ lower respiratory and or upper respiratory aspirates/throat swab samples for COVID-19.  Negatives in this test set are defined as any CXR which was acquired before the first case of  COVID-19 was discovered. Negative set is created based on the available prevalence in the [literature](https://news.gallup.com/opinion/gallup/306458/estimating-covid-prevalence-symptomatic-americans.aspx){:target="_blank"}. The test set has `515 positives` and `10964 negatives`. Negatives are sampled randomly from an independent set 250,000 CXRs. Negative set has 1609 cases of bilateral opacity and 547 cases of pulmonary consolidation in it (findings which are indicative of COVID-19 on a CXR), where the final diagnosis is not COVID-19. We have used Area under [Receiver Operating Characteristic Curve](https://en.wikipedia.org/wiki/Receiver_operating_characteristic){:target="_blank"} (AUC) along with Sensitivity and Specificity at the operating point to evaluate the performance of our algorithm.  
 
 A subset (1000 cases) of this test set was independently reviewed by radiologists to create pixel level annotations to localize opacity and consolidation. Localization and progression monitoring capability of  qXR is validated by computing the [Jaccard Index](https://en.wikipedia.org/wiki/Jaccard_index){:target="_blank"} between algorithm output and radiologist annotations.


<table class="table">
    <thead>
        <th>Characteristic</th>
        <th>Value</th>
    </thead>
    <tr>
        <td>Number of scans</td>
        <td>11479</td>
    </tr>
    <tr>
        <td>Positives</td>
        <td>515 </td>
    </tr>
    <tr>
        <td>Negatives </td>
        <td>10964</td>
    </tr>
    <tr>
        <td>Normals</td>
        <td>9000</td>
    </tr>
    <tr>
        <td>Consolidation</td>
        <td>547</td>
    </tr>
    <tr>
        <td>Opacities</td>
        <td>1609</td>
    </tr>
    <tr>
        <td>Other Abnormalities</td>
        <td>355</td>
    </tr>
</table>

<p class="caption"><i>Test set demographics</i></p>

### Metrics

To detect signs of COVID-19, We have observed an AUC of `0.9 (95% CI: 0.88 - 0.92)` on this test set. At the operating threshold, we have observed the sensitivity to be `0.912 (95% CI : 0.88 - 0.93)` and specificity to be `0.775 (95% CI : 0.77 - 0.78)`. While there are no WHO guidelines yet for an imaging based triage tool for COVID, WHO recommends a minimum sensitivity and specificity of 0.9 and 0.7 for community screening tests for Tuberculosis, which is a deadly infectious disease in itself. We have observed a Jaccard index  of `0.88` between qXR’s output and expert’s annotations.

<center>
<img src="/assets/images/qxr_covid19/roc_curve.png" alt="qXR deployments"/>
<br/>
<small class="caption"><i>Receiver Operating Characteristic Curve</i></small>
</center>

<h1 align="left"> Using qXR </h1>

qXR is available as a web-api and can be deployed within minutes. Built using our learnings of deploying globally and remotely, it can interface with a variety of PACS and RIS systems, and is very intuitive to interpret. qXR can be used to triage suspect patients in resource constrained countries to make effective use of RT-PCR test kits. qXR is being used for screening and triage at multiple hospitals such as Kasturba Hospital, Hiranandani Hospital and Kokilaben Hospital in Mumbai, India and few hospitals in Mexico. 


San Raffaele Hospital in Milan, Italy has [deployed qXR](https://www.auntminnieeurope.com/index.aspx?sec=ser&sub=def&pag=dis&ItemID=618613){:target="_blank"} to monitor patients and to evaluate patient’s response to treatments. In the neighbouring countries of India, qXR powered mobile vans are being used at 29 sites to identify potential suspects early and thus reducing burden on the healthcare system. 


<center>
<img src="/assets/images/qxr_covid19/covid_deployment_timeline.png" alt="qXR deployments"/>
<br/>
<small class="caption"><i>Timeline of qXR for COVID</i></small>
</center>

In the UK, all the suspected COVID-19 patients presenting to the emergency department are undergoing [blood tests and CXR](https://blogs.bmj.com/bmj/2020/03/20/lessons-from-the-frontline-of-the-covid-19-outbreak/){:target="_blank"}. This puts a tremendous amount of workload on already burdened radiologists as it becomes critical for radiologists to report the CXRs urgently. qXR, with its ability to handle huge workloads, provides significant value in such a scenario and thus reduce the burden on radiologists. 

qXR can also be scaled for rapid and extensive population screening. Frontline clinicians are increasingly relying on chest X-rars to triage the sickest patients, while they await RT-PCR results. When there is high clinical suspicion for COVID-19 infection, the need for a patient with positive chest X-ray to get admitted in a hospital is conceivable. qXR can help solve this problem at scale. 

<center>
<img  src="/assets/images/qxr_covid19/deployment_count.png" alt="qXR deployments"/>
<br/>
<small class="caption"><i>Impact of qXR for COVID-19</i></small>
</center>


<h1 align="left"> Join Us in the fight </h1>

With new evidence published every day, and evolving guidance and protocols adapting in suit for COVID-19, national responses globally remain fluid. [Singapore](https://www.forbes.com/sites/margiewarrell/2020/03/30/singapore-sets-gold-standard-against-covid-19-be-ready-be-decisive-be-bold/#6d5b03e77a22){:target="_blank"}, [Taiwan](https://jamanetwork.com/journals/jama/article-abstract/2762689){:target="_blank"} and [South Korea](https://www.theguardian.com/world/2020/mar/18/covid-19-south-koreans-keep-calm-and-carry-on-testing){:target="_blank"} have shown that aggressive and  proactive testing plays a crucial role in containing the spread of the disease. We believe qXR can play an important role in aggressive screening in the community to help reduce the burden on healthcare systems. If you want to join us in this fight, please reach out to [us](mailto:partner@qure.ai){:target="_blank"}.