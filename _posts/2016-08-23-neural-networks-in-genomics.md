---
layout: post
title: Neural Networks for Genomics
author: Pooja Rao
updated: 2016-08-23 12:00:00 +0530
categories:
tags:
    - Genomics
description:
twitter_image: "/assets/images/genomics/qure_genomics.jpg"

---

> When we first set out to apply deep learning to genomics, we asked ourselves what the current state of the art is. What problems are researchers working on and what approaches are they using? This post contains a summary of what we found — an overview of popular network architectures in genomics, the types of data used to train deep models, and the outcomes predicted or inferred.

Despite being able to sequence the genome at nucleotide-level resolution, and the abundance of publicly available labeled datasets from sources like the 1000-genome project, ENCODE and GEO, we are still far from bridging the genotype-phenotype divide or predicting disease from genome sequences. [This talk](https://www.youtube.com/watch?v=bYTq4QEDA78) by Brendan Frey puts the deep learning-and-genomics problem in context, explaining why sequencing more genomes may not be the answer. The genome is complex and contains many interacting information layers. Most current approaches involve developing a system to interpret the genomic code or a part of it, rather than directly training a network that predicts phenotype from sequence.
Below are some of the ways that deep learning has been used for genomics, with emphasis on implementations for the human genome or transcriptome.

## Early work using shallow, fully connected networks
Some of the first applications of neural networks in genomics involved training single-layer fully connected neural networks on gene expression data, typically after using principal component analysis to reduce the dimensions of the input. These networks were used to [distinguish between tumor types](http://www.nature.com/nm/journal/v7/n6/full/nm0601_673.html), [predict tumor grade](http://mct.aacrjournals.org/content/7/5/1013.long), and [predict patient survival](http://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-4-13) from gene expression patterns. Improvements to this included developing feature selection techniques for neural networks that could [identify subsets of genes](http://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-8-5)  or [‘signatures’](http://www.ncbi.nlm.nih.gov/pubmed/14556665) that were most predictive.

Similarly, simple ANNs have been used to predict tumor grade of colorectal tumors based on microRNA expression patterns and identify those microRNA’s which can predict tumor status, as described in [this paper](http://link.springer.com/article/10.1007%2Fs00384-011-1279-4).

## Autoencoders for dimensionality reduction and feature extraction
Autoencoders have been used in genomics to reduce feature space as well as to construct useful features from gene expression data. One example is  [this early work](http://matlabproject.ir/forms/files/392437.pdf) that demonstrates the use of autoencoders to learn a concise feature representation from unlabeled gene expression data, and  subsequently use the representation to learn a classifier for a number of labeled tumor datasets.
More recent examples include [this paper](http://www.worldscientific.com/doi/pdf/10.1142/9789814644730_0014), which uses denoising autoencoders to extract features from breast cancer gene expression data,  and ADAGE ([paper](http://msystems.asm.org/content/1/1/e00025-15) and [repository](https://github.com/greenelab/adage)), a similar approach on bacterial gene expression data. Features are either nodes in the encoded layer, or sets of genes whose weights most greatly influence a certain node.
Both these demonstrate the ability of autoencoders to pick up individual features which can identify tumor subtypes, estrogen-receptor status, and predict patient survival. This success in classifying tumors of different types based on features learned from a single dataset suggests that gene expression features may be shared across the human transcriptome. Would training a single autoencoder with a variety of expression profiles generate a universal common set of features that are predictive in all tissue types? Given that the method seems to work for gene expression data, a second question that arises is - are there any useful features to be learned from using an auto-encoder on DNA sequence data?

## Deep learning to predict gene or transcript expression levels
Since genes are expressed in a coordinated manner, levels of expression of different genes are highly correlated. This means that expression levels of all genes could be inferred or predicted from the profiles of a subset of genes, potentially reducing the cost and complexity of gene expression profiling. A method called [D-GEX](http://bioinformatics.oxfordjournals.org/content/32/12/1832) has been developed that uses a multi-task deep neural network trained on the publicly available CMAP dataset, to predict the expression of all genes, given the expression of ~1000 genes. A recent [topcoder challenge](https://community.topcoder.com/longcontest/?module=ViewProblemStatement&rd=16753&pm=14337) also focuses on the same task.

A related, much harder task is predicting the expression level of an exon or transcript from DNA sequence data. Expression level depends not only on the sequence but also on the cellular context. [This paper](http://bioinformatics.oxfordjournals.org/content/30/12/i121.full), titled ‘Deep learning of the tissue-regulated splicing code’ describes a model that predicts the percent of transcripts with exon spliced in (PSI), given the DNA sequence surrounding the exon. Hand-generated genomic features are used to train a model that can predict splicing patterns based on genomic features in specific mouse cell types. After reducing feature space with an autoencoder, the encoded features with additional inputs representing cell type are used to train a multilayer fully connected network. Based on this method, the authors developed and validated a [tool](http://tools.genes.toronto.edu) that can score the effect of a single-nucleotide-variant (or mutation) on splicing.

## Convolutional networks for epigenomics
A large proportion of recent work is focused on using convolutional neural networks to answer epigenomic questions such as predicting transcription factor binding sites, enhancer regions and chromatin accessibility from gene sequence. Since this often involves training on more than one data type, custom CNN architectures are used, such as training the same network to predict 2 different targets, or combining different kinds of input via independent convolution modules.

[DeepBind](http://www.nature.com/nbt/journal/v33/n8/full/nbt.3300.html) is a method that can predict the specificity of DNA- or RNA-binding proteins given a sequence. In order to do this, a CNN was trained using data from a large number of different high-throughput epigenomic experiments like protein-binding micro-array and (ChIP)-seq. The convolution stage of the network scans a set of ‘motif detector’ matrices across the one-hot encoded sequence. The learned filters are akin to position weight matrices are used in genomics to understand and depict DNA sequence motifs. The network has been able to learn both known and previously unknown motifs. [DeepMotif](https://arxiv.org/abs/1605.01133) is a deeper model on similar lines, trained to classify transcription factor binding (yes or no) to a sequence, and focuses on extracting motifs.
[DeepSEA](http://www.nature.com/nmeth/journal/v12/n10/full/nmeth.3547.html) is a model trained a with a variety of epigenomic data from ENCODE, that can predict the effects of mutations on transcription factor binding and DNAse senstitivity with single-nucleotide sensitivity. Key features of this model are the use of a hierarchical architecture that learns sequence features at different scales, the consequent ability to scan a wide sequence context, and multitask joint learning of diverse chromatin factors sharing predictive features.
Other examples of convolutional networks used in epigenomics include [Basset](http://genome.cshlp.org/content/26/7/990.full), which predicts chromatin accessibility code based on DNA sequence; [DeepCpG](http://www.nature.com/articles/srep19598), which models DNA methylation; [DEEP](http://nar.oxfordjournals.org/content/early/2014/11/05/nar.gku1058.full), an ensemble framework predicting enhancers or regions of DNA where transcription factors bind to increase the transcription of a gene; and a [CNN-based method](http://biorxiv.org/content/early/2016/05/07/052118) to attenuate noise and enhance signal from ChIP-seq experiments.

## Language-inspired models

Parallels are often made between language processing and genome interpretation, suggesting that the methods used in language analysis may be useful ways to understand genomics.

### Recurrent neural networks

Recurrent neural networks have been used to capture long‐range interactions in DNA sequences. [DanQ](http://nar.oxfordjournals.org/content/44/11/e107.long) is a hybrid convolutional and bi-directional long short-term memory recurrent neural network where the convolution layer captures regulatory motifs, while the recurrent layer captures long-term dependencies between the motifs in order to learn a regulatory 'grammar'. Other examples of RNNs include [DNA-level splice junction prediction](http://arxiv.org/abs/1512.05135) where an RNN is trained to detect splice junctions or the boundaries between introns and exons; and [detection of protein coding regions in viral genomes](http://www.sciencedirect.com/science/article/pii/S0950705111002024).

### Word embedding or word2vec-like models

A project  titled [‘Gene2vec: Neural word embeddings of genetic data’](https://github.com/davidcox143/Gene2vec), uses the original google word2vec implementation on genome sequences, splitting the genome into 27-bp ‘words’. It would be interesting to see if this can be applied for practical use in the human genome, and if a similar word2vec-style model is can be meaningfully trained on gene expression data.
<br><br>

<p align="center">
The list of applications of neural networks in genomics is growing rapidly, with a number of new research papers published this year. Many of the recently-evolved deep learning paradigms have been applied to questions in genomics or epigenomics. It remains to be seen how these will translate to real-world applications in biology or medicine.
</p>
