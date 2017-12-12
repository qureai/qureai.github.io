---
layout: post
title: Visualizing Deep Learning Networks  - Part II
author: Shubham Jain, Rohit Ghosh, Manoj TLD
updated: 2017-12-13 12:00:00 +0530
categories:
tags:
    - Classification
    - Visualisation
description: 
twitter_image: "https://imgur.com/VUjIr8r.png"
---

>In the [previous post](http://127.0.0.1:4000/notes/visualizing_deep_learning) we looked at methods to visualize and intepret the decisions made by deep learning models using perturbation based techniques. In this post, I'll give a brief overview of the various gradient based algorithms for deep learning based classification models and their drawbacks.

To illustrate each algorithm, we would be considering a Chest X-Ray (image above) of a patient diagnosed with pulmonary consolidation. Pulmonary consolidation is simply a “solidification” of the lung tissue due to accumulation of solid and liquid material in the air spaces that would have normally been filled by gas [[1]](#consolidation-defn). Pleural Effusion is generally along the costophrenic angles of the lung while consolidation can be anywhere in the lung field. Hence, being able to locate consolidation is a more difficult task compared to pleural effusion.

We would be considering this X-ray and one of our models trained for detecting consolidation for demonstration purposes. For this patient, our consolidation model predict a possible consolidation with XX% probability.



### References

1. <a name='consolidation-defn'></a>[Consolidation of Lung – Signs, Symptoms and Causes](http://www.healthhype.com/consolidation-in-lung-signs-symptoms-and-causes.html)
