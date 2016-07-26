---
layout: post
title: Ultrasound Nerve Segmentation Challenge using Torchnet
author: Shubham Jain
updated: 2016-07-21 20:20:31 +0530
categories:
tags:
    - tutorial
---

[Kaggle ultrasound-nerve-segmentation challenge](https://www.kaggle.com/c/ultrasound-nerve-segmentation) is one of the high profile challenges hosted on Kaggle. We have used U-net neural network architecture and [torchnet](httpsU://github.com/torchnet/torchnet) package for tackling the challenge and achieved some remarkable results. 
The challenge in itself is as a great learning experience for segmentation problems. 
Figure below is an example of the image and the mask to predict.

<p align="center">
    <img src="/assets/images/1_1.jpg" alt="Image" style="float:left;width:48%;margin:1%;margin-bottom:2em;"/>
    <img src="/assets/images/1_1_mask.jpg" alt="Mask" style="float:right;width:48%;margin:1%;margin-bottom:2em"/>
</p>

### Requirements

We assume following are installed in your system:

- CUDA Environment
- [Torch-7](http://torch.ch/)
- [Torchnet](https://github.com/torchnet/torchnet)
- Torch Libraries
    - [image](https://github.com/torch/image)
    - [hdf5](https://github.com/deepmind/torch-hdf5/blob/master/doc/usage.md)
    - [nngraph](https://github.com/torch/nngraph)
    - [optim](https://github.com/torch/optim)
    - [cudnn](https://github.com/soumith/cudnn.torch)
    - [paths](https://github.com/torch/paths)
    - [csvigo](https://github.com/clementfarabet/lua---csv)
    - [imagemagick](http://www.imagemagick.org/script/index.php)

## Dataset Generation

The [dataset](https://www.kaggle.com/c/ultrasound-nerve-segmentation/data) consists of 5635 training images and their masks, and 5508 testing images. 
The images are in tiff format, and to be able to load them into lua, we convert then to png format. So firstly we need to setup dataset such that

- Train images are in ``` /path/to/train/data/images ```
- Train masks are in ``` /path/to/train/data/masks ```
- Test images are in ``` /path/to/test/data ```

Now, go to each folder and run the following command, it will generate .png file for each .tif file in the folder. Be patient the procedure takes time.

``` mogrify -format png *.tif 
```

Now, we have all images in png format. To create datasets run the following command

```
th create_dataset.lua -train /path/to/train/data/images -trainOutput /path/to/train/data.h5 -test /path/to/test/data -testOutput /path/to/test/data.h5
```

This will package the dataset into HDF5 format, such that train images and masks of patient number `N` are in paths `/images_N` and `/masks_N` of the train HDF5 file respectively. The test images are in `/images` path of test HDF5 file generated.

## Model

We are using a slightly Modified [U-Net](http://arxiv.org/abs/1505.04597) with [Kaiming-He](https://arxiv.org/abs/1502.01852) initialization. The structure of U-Net generated using nngraph can be found [here](/assets/images/U-Net.svg).
Source code to create this model is at `models/unet.lua`

<p align="center">
    <img src="/assets/images/u-net-architecture.png" alt="U-Net Architecture">
    <br>
    <small> U-Net architecture </small>
</p>


## Training

You can start training right away by running

```bash
th main.lua [OPTIONS]
```


| Option | Default value | Description |
| ------ | --- | ----------- |
| `-dataset` | `data/train.h5` | Path to training dataset to be used |
| `-model` | `models/unet.lua` | Path of the model to be used |
| `-trainSize` | 100 | Amount of data to be used for training, -1 if complete train data to be used |
| `-valSize` | 25 | Amount of data to be used for validation, -1 if complete validation to be used |
| `-trainBatchSize` | 64 | Size of batch size to be used for training |
| `-valBatchSize` | 32 | Size of batch size to be used for validation |
| `-savePath` | `data/saved_models` | Path where models must be saved |
| `-optimMethod` | `sgd` | Method to be used for training, can be sgd or adam |
| `-maxepoch` | 250 | Maximum epochs for which training must be done |
| `-cvparam` | 2 | Cross validation parameter |

### Train Validation Data Split

The images are given for each patient, and thus in the dataset we have 47 patients with each patient having 119 or 120 images. To assess the real performance of our model, we divide the dataset into train and validation based on patients and use 80-20 split. Thus, now question arises which patients to use for validation and which for training.

To solve this, we keep a parameter `-cvparam`, such that all patients with `patient_id%5==cvparam` are used in validation, else in training. Now out of these images, we select `-trainSize` number of images and `-valSize` number of images for training and validation respectively. This allows us to do cross validation easily.

### Data Augmentation

Data augmentation plays a vital role in any segmentation problem with limited dataset. Here we do on-the-fly data augmentation using modified [Facebook's resnet's transformation file](https://github.com/facebook/fb.resnet.torch/blob/master/datasets/transforms.lua). The image goes through following transformations:

1. Horizontal flip with probability 0.5
2. Vertical flip with probability 0.5
3. Rotation between -5 to 5 degrees with uniform probability
4. Elastic transformations

### Constants

We resize the image to `imgWidth X imgHeight` and then pass to our model. 
For creating segmentation masks, we consider a pixel from the output to be a part of mask if `prob_pixel > baseSegmentationProb` where `prob_pixel` is predicted probability that pixel is nerve. 
One can define these values in `constants.lua` file.

## Submission file

```
th generate_submission.lua [OPTIONS]
```

| Option | Default value | Description |
| ------ | --- | ----------- |
| `-dataset` | `data/test.h5` | Path to dataset to be used |
| `-model` | `models/unet.t7` | Path of the model to be used |
| `-csv` | `submission.csv` | Path of the csv to be generated |
| `-testSize` | 5508 | Number of images to be used for generating test data, must be < 5508 |


## Torchnet

[Torchnet](https://github.com/torchnet/torchnet) was introduced in second half of June to enable code re-use and to make writing code in Torch much more simple. It is basically a well structured implementation of the boilerplate code such as permutation for batches, training for loop and all such things, into a single library. In this project, we have used 4 major tools provided by torchnet

1. Datasets
2. Dataset Iterators
3. Engine
4. Meters

### Datasets

Torchnet provides a abstract class `tnt.Dataset` and implementations of it to easily to easily concat, split, batch, resample etc. datasets. We use two of these implementations: 

* [`tnt.ListDataset`](https://github.com/torchnet/torchnet#tntlistdatasetself-list-load-path): Given a `list` and `load()` closure, ith sample of dataset will be returned by `load(list[i])`   
* [`tnt.ShuffleDataset`](https://github.com/torchnet/torchnet#tntshuffledatasetself-dataset-size-replacement): Given a `dataset` like above, it creates a new `Dataset` by shuffling it.

For our model to generalize as it converges, providing a shuffled dataset on every epoch is an important strategy. So we load the data with `tnt.ListDataset` and then wrap it with `tnt.ShuffleDataset`: 

```lua
local dataset = tnt.ShuffleDataset{
    dataset = tnt.ListDataset{
        list = torch.range(1,#images):long(),
        load = function(idx)
            return { input = images[idx], target = masks[idx] }
        end,
    },
    size = size
}
```

This ensures that whenever you query the `dataset` for ith sample using `dataset:get(i)`, you get the image chosen at random from the dataset without replacement.

<p align="center">
    <img src="/assets/images/dataset.png" alt="Dataset"/>
    <br>
    <small>Illustration of <code> dataset </code> </small>
</p>

### Dataset Iterator

While, it is easy to iterate over datasets using `dataset:get(i)` and a for loop, we can easily do on the fly and threaded data augmentation using `tnt.DatasetIterator`

We call the iterator in every epoch, and it returns the batch over which training must be done. Before a batch is put for training, we must ensure that transformations for data augmentation take place and then batch is formed of the given size. Using shuffled dataset ensures that we get new order of data every epoch and thus batches are non-uniform across the epochs. [`tnt.BatchDataset`](https://github.com/torchnet/torchnet#tntbatchdatasetself-dataset-batchsize-perm-merge-policy) ensures that batches are formed from underlying images.

```lua
return tnt.ParallelDatasetIterator{
  nthread = 1,
  transform = GetTransforms(mode), --transforms for data augmentation
  init = function()
     tnt = require 'torchnet'
  end,
  closure = function()
     return tnt.BatchDataset{
        batchsize = batchSize,
        dataset = ds
     }
  end
}
```

We use [`tnt.ParallelDatasetIterator`](https://github.com/torchnet/torchnet#tntparalleldatasetiteratorself-init-closure-nthread-perm-filter-transform-ordered) with transforms, which ensures that when the training is going for batch `n`, it will apply transforms on batch `n+1` in parallel and thus reducing the time for training.

<p align="center">
    <img src="/assets/images/dataset_iterator.png" alt="Dataset Iterator"/>
</p>

### Engine

From torch documentation,

> In experimenting with different models and datasets, the underlying training procedure is often the same. The Engine module provides the boilerplate logic necessary for the training and testing of models. This might include conducting the interaction between model (nn.Module), tnt.DatasetIterators, nn.Criterions, and tnt.Meters.

Engine is the main running core that will put your model into train. We use [optim engine](https://github.com/torchnet/torchnet#tntoptimengine) which wraps the optimization functions of [optim](https://github.com/torch/optim) package of torch. Engine has hooks attached with different events of training. We can define a callback function and attach to the hooks, hooks ensure that these functions are called at the end of event it is attached to. We use these hooks to update our meters, save model and print the statistics of the training.

```lua
self.engine:train{
  network   = self.model,
  iterator  = getIterator('train',self.trainDataset,self.trainBatchSize),
  criterion = self.criterion,
  optimMethod = self.optimMethod,
  config = self.optimConfig,
  maxepoch = self.maxepoch
}
```

Below is an example of the hook that we attach to on end epoch event. We validate the model, print the meters and save model.

```lua
local onEndEpochHook = function(state)
  state.t = 0
  self:test()
  self:PrintMeters()
  self:saveModels(state)
end
```

`state` supplied to hook function stores the current information about the training process, such as number of epochs done, model, criterion, etc.

### Meters

Again from torchnet's [documentation](https://github.com/torchnet/torchnet#meters), 

> When training a model, you generally would like to measure how the model is performing. Specifically, you may want to measure the average processing time required per batch of data, the classification error or AUC of a classifier a validation set, or the precision@k of a retrieval model.
> 
> Meters provide a standardized way to measure a range of different measures, which makes it easy to measure a wide range of properties of your models.

We use [`tnt.AverageValueMeter`](https://github.com/torchnet/torchnet#tntaveragevaluemeterself) for all parameters we want to observe such as validation dice scrore, validation loss, training loss, training dice score, etc. . They are set to zero on beginning of every epoch, updated at the end of an iteration in an epoch and printed at the end of every epoch.


Together all these combine to result into a very powerful system, with standing over the shoulder of torch.
