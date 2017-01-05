---
layout: post
title: Perfusion in Neuroimaging and Machine Learning
author: Shubham Jain
updated: 2016-12-29 12:00:00 +0530
categories:
tags:
    - Brain
    - Perfusion
description:
twitter_image: "/assets/images/brain_anatomy/mas_illustration.jpg"
---

> Knowing the state of the cells and amount of blood flow in a brain, can reveal huge amount of information about it. Tumor cells have high consumption of blood than normal, dead cells have very low consumption, all this information single handedly can lead to a complete diagnosis of the brain. Perfusion Imaging allows you to see all of this. Combine this with Machine Learning, and we can build a single system that takes perfusion images and gives out a complete report about the brain. This is the future.

Brain tumors are the leading cause of cancer-related deaths in children (males and females) age 1-19[[1]](#ref1). It is estimated that as many as 5.1 million Americans may have Alzheimer's disease[[2]](#ref2). And there are many such degenerative diseases such as Parkinson's, Autism, Schizophrenia that affect thousands and millions of people amongst us. Neuroimaging or brain imaging is the use of various techniques to either directly or indirectly image the structure, function/pharmacology of the nervous system[[3]](#ref3). One of the techniques of neuroimaging is Perfusion Imaging.

### What is Perfusion Imaging ?

Perfusion imaging captures the qualitative and quantitative information of the blood flow and various kinetics related to blood flow in your brain. Technically, Perfusion is defined as the passage of fluid through the lymphatic system or blood vessels to an organ or a tissue[[4]](#ref4). If you know the blood flow of affected and the normal brain, it can be helpful in finding the abnormalities.
Perfusion imaging helps in measurement of various parameters such as

- cerebral blood volume : volume of blood flowing in brain
- cerebral blood flow : rate of flow of blood in brain
- volume transfer coefficient : the capacity of a blood vessel wall to allow for the flow of small molecules

### Perfusion Imaging for Brain Tumors

For Brain Tumors, the World Health Organization (WHO), has developed a histological classification system that focuses on the tumor's biological behavior. The system classifies tumors into grades I to IV. Grade I and II have some abnormal structure but these tumors look like normal brain tissues. Grade III have more abnormal features,increased number of blood vessels and higher cell densites. Grade IV are the most malignant primary brain tumors. Histopathological analysis or analysing a biopsy of brain serves as a final test to decide the grade. This is an invasive procedure and requires the availability of an expert for the analysis.

<p align="center">
    <img src="/assets/images/perfusion/imag1.png" alt="rCBV values in Deformed Brain">
    <br>
    <small>Both glioblastoma (A, B) and metastatic lesions (C, D) exhibits increased rCBV values, not permitting a differentiation based on perfusion imaging. Source :
    <a href="http://www.intechopen.com/books/neurooncology-newer-developments/advanced-mr-imaging-techniques-in-the-diagnosis-of-intraaxial-brain-tumors">Neurooncology - Newer Developments</a></small>
</p>


Recent papers[[5-7]](#ref5) have found strong correlation between perfusion parameters such as relative cerebral blood volume (abbr. rCBV), volume transfer coefficient (abbr. kTrans) and grade of the tumor. Higher perfusion values in marked RoIs (regions of interest) suggested higher grades. Taking a step further another paper[[8]](#ref8) also suggested use of perfusion to measure prognosis and thus it can be a great indicator to quickly measure the effects of treatment or medication the subject is undergoing.

### Perfusion Imaging for Alzheimer's

Alzheimer's is the cause of 60%-70% cases of dementia[[9]](#ref9) and it has no cure. Globally, dementia affects 47.5 million people[[9]](#ref9). About 3% of people between the ages of 65–74 have dementia, 19% between 75 and 84 and nearly half of those over 85 years of age[[10]](#ref10).

<p align="center">
    <img src="/assets/images/perfusion/alzheimers.jpg" alt="Images of a SPECT scan">
    <br>
    <small>The normal brain (on the left) shows normal blood perfusion, denoted by an abundance of yellow color. The scan on the right, of a person suffering from Alzheimer’s, shows pervasive low perfusion all around, denoted by blues and greens. Source :
    <a href="http://cerebrabraintech.com/alexia-turbobrain-technology/the-physiological-basis-of-alexia-turbobrain">The Physiological and Neurological basis of Cerebra TurboBrain</a></small>
</p>


A 2001 paper[[11]](#ref11) in American Journal of Neuroradiology showed that perfusion or rCBV values in particular can be used to replace nuclear medical imaging techniques for the evaluation of patients with Alzheimer's disease. Another paper[[12]](#ref12) published in 2014 suggests closely linked mechanisms of neurodegeneration mediating the evolution of dementia in both Alzheimers and Parkinsons. Many other papers[[13,14]](#ref13) suggest strong linkage between early Alzheimer's and cerebral blood flow and thus can help in detection at earlier stage.

### Perfusion Imaging and Machine Learning

A lot of work in last decade has also been done to try to develop autonomous/semi-autonomous process of decision making in solving various problems mentioned above. Some papers[[15-17]](#ref15) have shown promise in developing semi-autonomous systems using Support Vector Machines (SVM) and other ML techniques for brain tumor grade classification with accuracies as high as 80%. In the domain of neurodegenerative diseases, accuracies as high as 85% have been achieved in classification of MRIs[[18,19]](#ref18) using perfusion and ML, and a recent article[[20]](#ref20) suggested that Alzheimer's early detection might be possible using AI which could predict onset of Alzheimer's with accuracy of 85% to 90%.

### Problems with Perfusion Imaging

Even though the perfusion imaging looks promising, but there are some major hurdles due to which it has not yet seeped into the hospitals as go to method for analysis. This paper[[21]](#ref21) highlights various methods used in brain perfusion imaging. There aren't one or two different methods, but seven that are highlighted in the paper. Another paper[[22]](#ref22) published in Journal of Magnetic Resonance Imaging (JMRI) gives a deeper insight into two successful approaches being used. Measurements from different methods have different accuracies, and asks for different expertise from the doctors performing. Before perfusion comes from research based imaging to more mainstream technique, a question of standardisation have to be answered, and inclusion of any major change into industry as big as healthcare requires time. However at a small scale, perfusion imaging has been showing many signs of being a forefront technology. This can be used alongside current advances in ML to do automated diagnosis and prognosis of various brain related diseases and disorders.


### References

1. <a name="ref1"></a>[American Brain Tumor Association](http://www.abta.org/about-us/news/brain-tumor-statistics/)
2. <a name="ref2"></a>[Alzheimer's foundation of America](https://www.alzfdn.org/AboutAlzheimers/statistics.html)
3. <a name="ref3"></a>[Neuroimaging - Wikipedia](https://en.wikipedia.org/wiki/Neuroimaging)
4. <a name="ref4"></a>[American Psychological Association](http://www.apa.org/)
5. <a name="ref5"></a>Law, Meng, et al. "[Glioma Grading: Sensitivity, Specificity, and Predictive Values of Perfusion MR Imaging and Proton MR Spectroscopic Imaging Compared with Conventional MR Imaging](http://www.ajnr.org/content/24/10/1989.full)" American Journal of Neuroradiology 24.10 (2003): 1989-1998.
6. <a name="ref6"></a>Shin, Ji Hoon, et al. "[Using Relative Cerebral Blood Flow and Volume to Evaluate the Histopathologic Grade of Cerebral Gliomas: Preliminary Results](http://www.ajronline.org/doi/abs/10.2214/ajr.179.3.1790783)" American Journal of Roentgenology 179.3 (2002): 783-789.
7. <a name="ref7"></a>Patankar, Tufail F., et al. "[Is Volume Transfer Coefficient (Ktrans) Related to Histologic Grade in Human Gliomas?](http://www.ajnr.org/content/26/10/2455.long)" American journal of neuroradiology 26.10 (2005): 2455-2465.
8. <a name="ref8"></a>Mills, Samantha J., et al. "[Do Cerebral Blood Volume and Contrast Transfer Coefficient Predict Prognosis in Human Glioma?](http://www.ajnr.org/content/27/4/853.full.pdf+html)" American Journal of Neuroradiology 27.4 (2006): 853-858.
9. <a name="ref9"></a>World Health Organization. "[Dementia Fact sheet N°362](https://web.archive.org/web/20150318030901/http://www.who.int/mediacentre/factsheets/fs362/en)" (2012).
10. <a name="ref10"></a>Umphred, Darcy Ann, et al., eds. [Neurological rehabilitation](https://books.google.co.in/books?id=I9ltC-ZrNOMC&pg=PA838&redir_esc=y#v=onepage&q&f=false). Elsevier Health Sciences, 2013.
11. <a name="ref11"></a>Bozzao, Alessandro, et al. "[Diffusion and Perfusion MR Imaging in Cases of Alzheimer's Disease: Correlations with Cortical Atrophy and Lesion Load](http://www.ajnr.org/content/22/6/1030.full)" American Journal of Neuroradiology 22.6 (2001): 1030-1036.
12. <a name="ref12"></a>Le Heron, Campbell J., et al. "[Comparing cerebral perfusion in Alzheimer's disease and Parkinson's disease dementia: an ASL-MRI study](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4050238/)" Journal of Cerebral Blood Flow & Metabolism 34.6 (2014): 964-970.
13. <a name="ref13"></a>Roher, Alex E., et al. "[Cerebral blood flow in Alzheimer’s disease](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3481957/)" Vasc Health Risk Manag 8 (2012): 599-611.
14. <a name="ref14"></a>[MRI technique detects evidence of cognitive decline before symptoms appear](https://www.sciencedaily.com/releases/2014/10/141007092344.htm)
15. <a name="ref15"></a>Zacharaki, Evangelia I., et al. "[Classification of brain tumor type and grade using MRI texture and shape in a machine learning scheme](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC2863141/)" Magnetic Resonance in Medicine 62.6 (2009): 1609-1618.
16. <a name="ref16"></a>Zacharaki, Evangelia I., Vasileios G. Kanas, and Christos Davatzikos. "[Investigating machine learning techniques for MRI-based classification of brain neoplasms](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC4373074/)" International journal of computer assisted radiology and surgery 6.6 (2011): 821-828.
17. <a name="ref17"></a>Emblem, Kyrre E., et al. "[Predictive modeling in glioma grading from MR perfusion images using support vector machines.](https://www.ncbi.nlm.nih.gov/pubmed/18816815)" Magnetic resonance in medicine 60.4 (2008): 945-952.
18. <a name="ref18"></a>Fung, Glenn, and Jonathan Stoeckel. "[SVM feature selection for classification of SPECT images of Alzheimer's disease using spatial information](http://dl.acm.org/citation.cfm?id=1229092)" Knowledge and Information Systems 11.2 (2007): 243-258.
19. <a name="ref19"></a>López, M. M., et al. "[SVM-based CAD system for early detection of the Alzheimer's disease using kernel PCA and LDA.](https://www.ncbi.nlm.nih.gov/pubmed/19716856)" Neuroscience Letters 464.3 (2009): 233-238.
20. <a name="ref20"></a>[Artificial Intelligence Could Aid Earlier Diagnosis Of Alzheimer's](http://www.meddeviceonline.com/doc/artificial-intelligence-could-aid-earlier-diagnosis-of-alzheimer-s-0001)
21. <a name="ref21"></a>Wintermark, Max, et al. "[Comparative Overview of Brain Perfusion Imaging Techniques](http://stroke.ahajournals.org/content/36/9/e83#xref-ref-104-1)" Stroke 36.9 (2005): e83-e99.
22. <a name="ref22"></a>Barbier, Emmanuel L., Laurent Lamalle, and Michel Décorps. "[Methodology of brain perfusion imaging](http://onlinelibrary.wiley.com/doi/10.1002/jmri.1073/full)" Journal of Magnetic Resonance Imaging 13.4 (2001): 496-520.
