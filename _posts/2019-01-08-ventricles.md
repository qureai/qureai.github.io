---
layout: post
title: "The Average Brain Part 1: Study of Ventricle Volume in 15,000 Subjects with and without Hydrocephalus"
updated: 2019-01-29 12:00:00 +0530
categories:
---

> This post is Part 1 of a series that uses large datasets (20,000+) coupled with deep learning segmentation methods to re-establish and maybe revise what we know about normal brain anatomy and pathology. Subsequent posts will tackle intra-cranial bleeds, their typical volumes and locations across similarly sized datasets.

Before radiologic imaging, ventricle volume was quantified by post-mortem studies [1] and pneumoencephalography. When CT and subsequently MRI became available, facilitating non-invasive observation of the ventricular system these volumes were studied again, with somewhat larger datasets. Typical subject numbers in studies from this era ranged from 50 - 150 [2-6].

Now that deep learning segmentation methods have enabled automated precise measurements of ventricular volume, we can re-establish these reference ranges using datasets that are 2 orders of magnitude larger. This is likely to be especially useful for age group extremes - in children, where very limited reference data exist and the elderly, where the effects of aging on ventricle size of may co-exist with pathologic neurodegenerative processes.

There are no established standards for ventricle volume measurements, with the Evans’ index [1] being the nearest alternative. True volumetric measures are preferable to the Evans’ index for a number of reasons [7, 8] but have not been adopted so far. Now that automated precise quantification is feasible with deep learning, it is time to upgrade to a more precise volumetric measure.

Such quantitative measures will be useful in the monitoring of patients with hydrocephalus, and as an aid to diagnosing normal pressure hydrocephalus. In the future, automated measurements of ventricular, brain and cranial volumes could be displayed alongside established normal range for age and gender as a standard part of radiology head CT reports.

### Methods and Results

To train our deep learning model, lateral ventricles were manually annotated in 103 scans. We split these scans randomly in ratio of 4:1 for training and validation respectively. We trained a U-Net to segment lateral ventricles in each slice. Another U-Net model was trained to segment cranial vault using similar procedure. Models were validated using DICE score metric versus the annotations.

<table style="width:100%">
<thead>
<th>Anatomy</th>
<th>DICE Score</th>
</thead>
<tr>
    <td>Lateral Ventricles</td>
    <td>0.909</td>
</tr>
<tr>
    <td>Cranial Vault</td>
    <td>0.983</td>
</tr>

</table>


Validation set of about 20 scans might not have represented all the anatomical/pathological variations in the population. Therefore, we have visually verified that the resulting models worked despite pathologies like bleeds/infarcts or surgical implants like shunts. We show some representative scans and model outputs below.


<table style="width:100%">
<tr>
    <td>
        <p align="center">
            <img width='100%' src="/assets/images/ventricle_volume/focal_dilation.png" alt="Focal ventricle dilatation">
            <br>
            <small>30 year old male reported with 'focal dilatation of left lateral ventricle.'</small>
        </p>
    </td>
    <td>
        <p align="center">
            <img width='100%' src="/assets/images/ventricle_volume/mild_hydrocephalus.png" alt="Mild Hydrcephalus">
            <br>
            <small>7 year old female child reported with 'mild obstructive hydrocephalus'</small>
        </p>
    </td>
</tr>

<tr>
    <td>
        <p align="center">
            <img width='100%' src="/assets/images/ventricle_volume/hemorrhage.png" alt="Mild Hydrcephalus">
            <br>
            <small>28 year old male reported with fracture and hemorrhages</small>
        </p>
    </td>
    <td>
        <p align="center">
            <img width='100%' src="/assets/images/ventricle_volume/shunt.png" alt="Shunt">
            <br>
            <small>36 year old male reported with a intraventricular mass and with a VP shunt</small>
        </p>
    </td>
</tr>
</table>

To study lateral ventricular and cranial vault volume variation across population, we have randomly selected 14,153 scans from our database. These scans contained only 208 scans reported with hydrocephalus. Since we want to study ventricle volume variation in patients with hydrocephalus, we have added additional 1314 scans reported with 'hydrocephalus'. We have excluded those scans for which age/gender metadata was not available.
In total, our analysis dataset contained 15223 scans whose demographic characteristics were shown in the below table.

<table class="table">
    <thead>
        <th>Characterstic</th>
        <th>Value</th>
    </thead>
    <tr>
        <td>Number of scans</td>
        <td>15223</td>
    </tr>
    <tr>
        <td>Females</td>
        <td>6317 (41.5%)</td>
    </tr>
    <tr>
        <td>Age: median (interquartile range) </td>
        <td>40 (24 - 56) years</td>
    </tr>
    <tr>
        <td>Scans reported with cerebral atrophy</td>
        <td>1999 (13.1%)</td>
    </tr>
    <tr>
        <td>Scans reported with hydrocephalus</td>
        <td>1404 (9.2%)</td>
    </tr>
</table>

<p class="caption">Dataset demographics and prevelances.</p>

Histogram of age distribution is shown below. It can be observed that there are reasonable number of patients (>200) for all age and sex groups. This ensures that our analysis below is statistically powerful.

<center>
<img width='70%' src="/assets/images/ventricle_volume/age_hist.svg" alt="Shunt">
</center>

We have run the above trained deep learning models and measured lateral ventricular and cranial vault volumes for each of the above 15223 scans. Below is the scatter plot of all the analyzed scans.

<img width='100%' src="/assets/images/ventricle_volume/scatter.png" alt="Scatter plot">

In this scatter plot, x-axis is lateral ventricular volume while y-axis is cranial vault volume.  Scans with atrophy were circled with orange cirle while scans with hydrocephalus were marked with green square. Patients with atrophy were on the right to the mass of patients, indicating larger ventricles in these patients. Unsuprisingly, patients with hydrocephalus were the rightmost, with ventricle volumes even higher than those with atrophy.

To make this relation clearer, we have plotted distribution of ventricular volume for patients without hydrocephalus or atrophy and patients with one of these.

<img width='100%' src="/assets/images/ventricle_volume/distribution.svg" alt="Shunt">
<br>

Interesting thing to note is that hydrocephalus distribution has a very long tail while distribution of patients with neither hydrocephalus nor atrophy has a single narrow peak.

Next, let us observe cranial vault volume variation with age and sex. Bands around solid lines indicate interquartile range of cranial vault volume of the particular group.

<img width='100%' src="/assets/images/ventricle_volume/bm_volume_vs_age.svg" alt="Shunt">

Obvious feature of this plot is that cranial vault increases in size until age of 10-20 after which it remains constant. Cranial vault of males is approximately 13% larger than that of females. Another interesting point is that males' cranial vault grows until age of 15-20 while females' satures at age of 10-15.


Now, let's plot variation of lateral ventricles with age and sex. As before, bands indicate interquartile range for a particular age group.

<img width='100%' src="/assets/images/ventricle_volume/lv_volume_vs_age.svg" alt="Shunt">

This plot shows that ventricles grow in size as patients age. This may be explained by the fact that brain naturally loses mass with age, thus dilating the ventricles (i.e. cerebral atrophy)[2].


This information can be used as normal range of ventricle volume for a age and sex group. Ventricle volume outside this normal range can be indicative of hydrocephalus or a neurodegenerative disease.

We can incorporate this into our automated analysis report. For example, following is the CT scan of an 80 year old patient with a hemorrhage and our automated report.

<center>
<div style="align: center" id='example'>
</div>
</center>
<p class="caption">CT scan of a 80 Y/F patient. <br>Use scroll bar on the right to scroll through slices.</p>

<pre>
qER Analysis Report
===================

Patient ID: KSA18458
Patient Age: 80Y
Patient Sex: F

Preliminary Findings by Automated Analysis:

- Intraparenchymal hemorrhage of 21.80 ml in right temporal,
  right parietal regions.
- Subarachnoid hemorrhage.
- Intraventricular hemorrhage.
- Midline shift.
- Mass effect.
- <b>Dilated lateral ventricles.
  This might indicate neurodegenerative disease/hydrocephalus.
  Lateral ventricular volume = 106 ml.
  Interquartile range for female >75Y patients is 28 - 54 ml.</b>

This is a report of preliminary findings by automated analysis.
Other significant abnormalities may be present.
Please refer to final report.
</pre>

<p class="caption">Our auto generated report. Added text is indicated in bold.</p>

### Discussion

The question of how to ‘truth’ these measurements still remains to be answered. For this study, we use DICE scores versus manually outlined ventricles as an indicator of segmentation accuracy. Ventricle volumes annotated slice-wise by experts are an insufficient gold-standard not only because of scale, but also because of the lack of precision. The most likely places where these algorithms are likely to fail (and therefore need more testing) are anatomical variants and pathology that might alter the structure of the ventricles. We have tested some common co-occurring pathologies (bleeds), but it would be interesting to see how well the method performs on scans with congenital anomalies and other conditions such as subarachnoid cysts (which caused an earlier machine-learning-based algorithm to fail[9]).


-   Putting ventricle volume on reports is a good idea for future reference and comparison, in patients post-trauma as well as those with shunts
-   Establishing the accuracy of these automated segmentation methods algorithms also paves the way for more nuanced neuroradiology research on a scale that wasn’t previously possible.


### References
1. EVANS, WILLIAM A. "[An encephalographic ratio for estimating ventricular enlargement and cerebral atrophy](https://jamanetwork.com/journals/archneurpsyc/article-abstract/649255)." Archives of Neurology & Psychiatry 47.6 (1942): 931-937.
2. Matsumae, Mitsunori, et al. "[Age-related changes in intracranial compartment volumes in normal adults assessed by magnetic resonance imaging.](https://s3.amazonaws.com/academia.edu.documents/39907465/Age-related_changes_in_intracranial_comp20151111-24390-w6hyxp.pdf?AWSAccessKeyId=AKIAIWOWYYGZ2Y53UL3A&Expires=1547126908&Signature=LFp5zuldCmlyiy3QhkA%2Ba0U1q1c%3D&response-content-disposition=inline%3B%20filename%3DAge-related_changes_in_intracranial_comp.pdf)" Journal of neurosurgery 84.6 (1996): 982-991.
3. Scahill, Rachael I., et al. "[A longitudinal study of brain volume changes in normal aging using serial registered magnetic resonance imaging.](https://jamanetwork.com/journals/jamaneurology/fullarticle/784396)" Archives of neurology 60.7 (2003): 989-994.
4. Hanson, J., B. Levander, and B. Liliequist. "[Size of the intracerebral ventricles as measured with computer tomography, encephalography and echoventriculography.](https://www.ncbi.nlm.nih.gov/pubmed/782172)" Acta Radiologica. Diagnosis 16.346_suppl (1975): 98-106.
5. Gyldensted, C. "[Measurements of the normal ventricular system and hemispheric sulci of 100 adults with computed tomography.](https://www.ncbi.nlm.nih.gov/pubmed/304535)" Neuroradiology 14.4 (1977): 183-192.
6. Haug, G. "[Age and sex dependence of the size of normal ventricles on computed tomography.](https://www.ncbi.nlm.nih.gov/pubmed/304536)" Neuroradiology 14.4 (1977): 201-204.
7. Toma, Ahmed K., et al. "[Evans' index revisited: the need for an alternative in normal pressure hydrocephalus.](https://academic.oup.com/neurosurgery/article-abstract/68/4/939/2599368)" Neurosurgery 68.4 (2011): 939-944.
8. Ambarki, Khalid, et al. "[Brain ventricular size in healthy elderly: comparison between Evans index and volume measurement.](https://academic.oup.com/neurosurgery/article-abstract/67/1/94/2556570)" Neurosurgery 67.1 (2010): 94-99.
9. Yepes-Calderon, Fernando, Marvin D. Nelson, and J. Gordon McComb. "[Automatically measuring brain ventricular volume within PACS using artificial intelligence.](https://journals.plos.org/plosone/article?id=10.1371/journal.pone.0193152)" PloS one 13.3 (2018): e0193152.

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/2.7.2/Chart.bundle.min.js"></script>
<script type="text/javascript" src="/assets/js/ImageStack.js"></script>
<script type="text/javascript">
    var imageList = getImageList('/assets/images/ventricle_volume/example/', 32);
    var stack = new ImageStack({
    images: imageList,
    height: '20rem',
    width: '20rem'
    });
    $('#example').append(stack);

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

