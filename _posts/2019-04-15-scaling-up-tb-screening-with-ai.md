---
layout: post
title: "Scaling up TB screening with AI: Deploying automated X-ray screening in remote regions"
updated: 2019-04-11 12:00:00 +0530
author: Ankit Modi, Reshma Suresh
categories:
twitter_image: "http://blog.qure.ai/assets/images/tb/patient-flow.png"
---


> We have been deploying our deep learning based solutions across the globe. qXR, our product for automated chest X-ray reads, is being widely used for Tuberculosis screening. In this blog, we will understand the scale of the threat that TB presents. Thereafter, taking one of our deployments as a case study, we will explain how artificial intelligence can help us in fighting TB.


Qure.ai’s deep learning solutions are actively reading radiology images in over 82 sites spread across 12 countries. We have processed more than 50 thousand scans till date. One of the major use cases of our solutions is for fast-tracking Tuberculosis screening.

### Understanding Tuberculosis

Tuberculosis (TB) is caused by bacteria called Mycobacterium tuberculosis and it mostly affects the lungs. About one-fourth of the world’s population is infected by the bacteria inactively – a condition called latent TB. TB infection occurs when a person breathes in droplets produced due to an active TB person’s coughing, sneezing or spitting.

TB is a [curable and preventable disease](https://www.who.int/news-room/fact-sheets/detail/tuberculosis). Despite that, [WHO reports](https://www.who.int/news-room/fact-sheets/detail/tuberculosis) that it is one of the top 10 causes of deaths worldwide. In 2017, 10 million people fell ill with TB, out of which 1.6 million lost their lives. 1 million children got affected by it, with 230,000 fatalities. It is also the leading cause of death among HIV patients.

#### Diagnosis of TB
There are many tests to detect TB. Some of them are as follows:

* Chest X-ray: Typically used to screen for signs of TB in the lungs. They are a sensitive and inexpensive screening test, but may pick up other lung diseases too. So chest X-rays are not used for a final TB diagnosis. The presence of TB bacteria is confirmed using a bacteriological or molecular test of sputum or other biological sample.

* Sputum tests: The older AFB sputum tests (samples manually viewed through a microscope looking for signs of bacteria) are still used in low-income countries to confirm TB. A more sensitive sputum test that uses DNA amplification technology to detect traces is now in wide use to confirm TB – it’s not only more sensitive, but also can also look for TB resistance. Tests like Genexpert and TrueNat fall under this category. These are fairly expensive tests.

Molecular tests have shown [excellent results in South Africa](https://www.tbfacts.org/tb-south-africa-hiv/)  and are generally considered as the go-to test for TB. However, their high costs make it impossible to conduct them for every TB suspect.

#### Failure in early detection
Due to the high costs of molecular tests, Chest X-rays are generally preferred as a pre-test for TB suspects. Post that, sputum or molecular tests are performed for confirmation. In regions where these confirmatory tests are not available, Chest X-rays are used for final diagnosis.

Having understood the chest x-rays’ key role in TB diagnosis, it is important to note that there is a huge dearth of radiologists to read these X-rays. In India alone, 80 million chest x-rays are being captured every year. There aren’t enough radiologists to read them within acceptable timelines. Depending upon the extent of shortage for radiology expertise, it can take anywhere between 2 to 15 days for the report to arrive. This leads into critical time being lost for a TB patient thereby preventing its early detection. Failure in detecting it early is not only hazardous for the patient but also enhances the risk of its transmission to others.

Moreover, the error rates in reading these X-rays [lie around 25-30%](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3609674). An error in reading a TB suspect’s chest X-ray can be fatal for the patient.

<center>
<img width='100%' src="/assets/images/tb/tb-diagnosis.png" alt="TB diagnosis">
</center>


#### Where Qure.ai comes into the picture

This large gap between the number of TB incidences and the number of timely & accurately reported cases is a major reason why many lives are lost to this curable disease. This gap can be bridged with a solution that requires little manual intervention. This is precisely how Qure’s qXR solution, trained on more than a million Chest x-rays, attacks at the heart of the problem. The AI (Artificial Intelligence) encapsulated inside qXR automates reading chest x-rays and generates reports within seconds. Thereby, reducing the waiting time for TB confirmatory tests from weeks to a couple of hours and enrolling confirmed cases to treatment the same day!

<center>
<img width='100%' src="/assets/images/tb/qxr-features.png" alt="qXR features">
<br/>
<small class="caption">qXR features</small>
</center>

While bacteriological confirmatory tests on presumptive cases are preferred in a screening setting, the cost burden increases. Sputum culture testing will take weeks for the reports that could result in dropouts in collecting reports and treatment enrolment. Additionally, the shortage of sourcing Cartridge Based Nucleic Acid Amplification Test (CB-NAAT) becomes a limitation which results in a delay of the testing process. Qure.ai’s qXR helps in cutting down on costs incurred and time by reducing the number of individuals required to go through these tests. The whole program workflow happens as depicted in the following picture.


<center>
<img width='100%' src="/assets/images/tb/patient-flow.png" alt="Patient flow">
</center>

### Case Study: AccessTB, Philippines

While upscaling our solutions in the last 2 years, it has become evident that Qure.ai can play a vital role in humanity’s war against TB. We deployed qXR with ACCESS TB Project in Philippines in their Tuberculosis screening program. During the deployment, we learned the operational dynamics of deploying Artificial Intelligence (AI) at health centers.

#### TB screening process before incorporating qXR

The ACCESS TB program has mobile vans equipped with X-rays machines with trained radiographers and health workers. The program is intended to screen presumptive cases and individuals with a high-risk factor of Tuberculosis, by running the vans across different cities in the Philippines. Screening camps are either announced in conjunction with a nearby nursing home or health workers identify and invite individuals at risk on the days of programs.

The vans leave the office on Monday morning for remote villages with a predefined schedule. These villages are situated around 100kms from Manila. Two radiology technicians accompany each van. Once they reach the desired health center in the village, they start capturing X-rays for each individual. The X-ray machines are connected to a computer which stores these x-rays locally. One can also edit the dicom (radiology image) information inside the X-ray from this computer.

Individuals arrive inside the van on a first come first serve basis. They are given a receipt containing their patient id, name, etc. Their X-ray is also marked with the same ID using the computer. This approach of mass screening for TB is similar to the approach adopted by the USA during the 1930s to 1960s as depicted in the following picture.

<center>
<img width='80%' src="/assets/images/tb/tb-screening-van.png" alt="TB screening van"/>
<br/>
<small class="caption"><a href="https://erj.ersjournals.com/content/erj/49/5/1700364/F1.large.jpg?width=800&height=600&carousel=1">Source</a></small>
</center>

Once all the x-rays have been captured, the vans return to their stay in the same village. They visit a new village/ health center on subsequent weekdays. On Friday evening, all the vans return to Manila. Thereafter, all the X-rays captured in the 4 vans over the week are sent to a Radiologist for review. The lead time for the Radiologist report is between 3 working days and can extend to 2 weeks. The delay in reporting leads to delay in diagnosis and treatment, which can prove to be fatal for the patient and the neighborhood.

<center>
<img width='80%' src="/assets/images/tb/access-tb-van-blurred.png" alt="Access TB van"/>
<br/>
<small class="caption">Front & side view of AccessTB van with individuals queuing inside the van</small>
</center>


#### Challenges for Qure.ai

Our team arrived in Manila during the second week of September 2018 with the deep learning solution sitting nice and cozy on the cloud. The major challenges in front of us were two-fold:

1. To ensure smooth upload of images to our cloud server.
This was a challenge because some of the villages and towns being visited were really remote and there was no guarantee of sufficient internet connection for the upload to work properly. We had to make sure that everything worked fine even if there was no internet connectivity. To deal with this, we built an application which was installed on their computer to upload images on our cloud. In case of no internet connectivity, it would store all the information and wait for better connectivity. As soon as connectivity became available, the app would start processing deferred uploads.


2. To enable end to end patient management on one single platform.
This was the biggest concern and we designed the software to minimize manual intervention at various stages.

We built a portal where radiology assistants could register individuals, radiologists could report on them and patient history could be maintained. The diagnosis from the radiologist, qXR and CB-NAAT tests are all accumulated at a single place.

<center>
<img width='80%' src="/assets/images/tb/qxr-portal.png" alt="QXR Portal"/>
<br/>
<small class="caption">Snapshot of complete patient management system</small>
</center>

Features that could ease the workflow were added to the software that enabled the staff in the field to search for individuals by name, date or filter individuals by site or health center. We believe such feature addition and provisions in the software helped the staff to capture the progress of screening about an individual with a simple search using patient ID.

#### Implementation process

At Qure, we deliver our products and solutions understanding the customer needs and designing workflows to fit into their existing processes. Especially when it comes to mass screening programs, we understand that each one of them is uniquely designed by Program managers and strategists and requires specific customizations to deliver a seamless experience.

After understanding the existing workflow, we designed our software to include elements that can automate some of the existing processes. Thereafter, the software was built, tested, packaged and stored in a secure cloud.
We figured the best way to integrate with their existing X-ray console and completed the integration on all the vans in 2 working days’ time.

A field visit was arranged after the deployment to assess the software's performance in areas with limited network connectivity and its ease of usage for the radiology staff. Based on our on-field learnings, we further customized the software's workflow for the staff.

The implementation process ended with a classroom training program with the field staff, technicians and program managers. With the completion of the deployment, software adaptability assessment and training, we handed over the software to the program in 5 days before we leaving Manila.  


<center>
<img width='80%' src="/assets/images/tb/radiology-assistant-training.jpg" alt="Radiology Assistant Training"/>
<br/>
<small class="caption">Training program for radiology assistants post qXR deployment</small>
</center>

Quoting Preetham Srinivas (AI scientist at Qure) on qXR, “With qXR at the heart of it, we’ve developed a solution that is end to end. As in, with individual registrations, and then qXR does the automated analysis and flags individuals who need microbiological confirmation. Radiologists can verify in the same portal and then, an organization doing the microbiological tests can plug in their data in the same portal. And once you have a dashboard which collates all this information in one place, it becomes very powerful. The loss itself can be minimized. It becomes that much easier to track the person and make sure he is receiving the treatment.”

### Conclusion

WHO has given the [status of an epidemic to TB](https://www.who.int/news-room/fact-sheets/detail/tuberculosis). They adopted an END TB strategy in 2014 aimed at reducing TB deaths by 90% and cutting new cases by 80% between 2015 and 2030. Ending TB by 2030 is one of the health targets of their Sustainable Development Goals.

The scale of this epidemic cries out for technology to intervene. Technologies like AI, if incorporated into existing TB care ecosystem, can not only assist healthcare practitioners massively, but also enrich it by the supplied data and feedback. And this is not a mere speculation. With qXR, we are having a first-hand experience of how AI can accelerate our efforts in eradicating TB. Jerome Trinona, account coordinator for AccessTB project, says “Qure.ai’s complete TB software is very helpful in maximizing our time – now we can keep track of the entire patient workflow in one place.”


<center>
<img width='80%' src="/assets/images/tb/access-tb-success.jpg" alt="Access TB success"/>
<br/>
<small class="caption">Successful deployment of qXR with AccessTB Program staff</small>
</center>


Successful deployments like AccessTB show that Qure.ai is leading the battle against TB at the technology and innovation fronts. Post World TB day, let us all embrace AI as our newest ammunition against TB.

Let’s join hands to end TB by 2030. [^1]

[^1]: Reach out to us at partner@qure.ai
