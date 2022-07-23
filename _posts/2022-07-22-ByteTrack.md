---
layout: post
title: "How to Create Python Packages"
author: Souvik Mandal
twitter_image: "https://blog.qure.ai/assets/images/bytetrack/bytetrack_comparisons.png"
categories:
tags:
    - AI
    - Deep Learning
---
ByteTrack is a multi-object tracking algorithm. As of writing this blog, it is the SOTA on [MOT17](https://paperswithcode.com/sota/multi-object-tracking-on-mot17?p=bytetrack-multi-object-tracking-by-1) and [MOT20](https://paperswithcode.com/sota/multi-object-tracking-on-mot20-1?p=bytetrack-multi-object-tracking-by-1) datasets.
  <center>
  <img width='100%' src="/assets/images/bytetrack/bytetrack_comparisons.png" alt="ByteTrack comparison with other algos"/>
  <em>MOTA and FPS comparisons of different trackers (credits: ByteTrack paper)</em>
  </center>
To completely understand this paper we will divide this post into the following sections:
- Multi-Object Tracking (MOT)
- ByteTrack algorithm and implementation
- Results

## Multi-Object Tracking
- Multi-object tracking (MOT) aims at estimating bounding boxes and identities of objects in videos.
- A simple tracking algorithm can involve the following steps.

  1. First, we detect the bounding boxes for all the objects (here we are only detecting persons in the image) in all the frames. In the image below, we have three frames and bounding boxes for all the persons highlighted in yellow, and the corresponding confidence threshold is mentioned at the top. Here we use an object detection algorithm to get the bounding box coordinates in all the frames.
  <center>
  <img width='100%' src="/assets/images/bytetrack/det_across_frames.png" alt="Detection across frames"/>
  <em>Detection across frames (image credits: ByteTrack paper).</em>
  </center>

  2. Next, we use some algorithms like ByteTrack to associate these detection boxes across frames. In the image below, after applying the tracking algorithm we assign a tracking id to each object (persons). Here, objects with the same tracking ids are shown in the same color.
  <center>
  <img width='100%' src="/assets/images/bytetrack/box_association_result.png" alt="BBox association result"/>
  <em>BBox association result (image credit: ByteTrack paper).</em>
  </center>

**Note**: There are variations in how different tracking algorithms work than what I have mentioned above. In general, any algorithm which does detection first and then uses that detection result to get tracking ids are called Tracking-by-detection. We will discuss some of those variations later.

I am not going into the details of detection. Let’s assume we are using one of the detection models([Yolov5](https://github.com/ultralytics/yolov5), [YoloX](https://github.com/Megvii-BaseDetection/YOLOX) , [RetinaNet](https://github.com/facebookresearch/Detectron/blob/8170b25b425967f8f1c7d715bea3c5b8d9536cd8/detectron/modeling/retinanet_heads.py) and so on.) to get the bounding boxes from each frame. Note the official implementation of ByteTrack uses the YoloX model as object-detector.

Now for bounding box association, we can use two logic as follows:
### 1. IOU Trackers / Location-based trackers
  <center>
  <img width='100%' src="/assets/images/bytetrack/iou_trackers.png" alt="IoU Trackers "/>
  <em>IoU Trackers  (image credits: High-Speed Tracking-by-Detection Without Using Image Information paper)</em>
  </center>

  1. Here, we assume that the video is captured at high FPS. So, between two consecutive frames, there is minimal movement of any objects.

  2. Now to associate objects across frames we can just calculate IoU (intersection over union) between detections of two consecutive frames.

  3. In the image above, the bounding boxes (they are shown in the same color across frames for the same object) of two objects are shown across frames.

  4. Now if we calculate the IoU of detected bounding boxes, the same objects will have high overlap across frames than two different objects. In the above image, if we take any two consecutive frames the IoU for blue-blue or red-red boxes will be higher than for blue-red boxes.

  5. This way if two detected boxes have high IoU in two consecutive frames, we can give the same tracking id (in the image above, objects with the same tracking ids are in the same color).

Note: Detected boxes and bounding boxes are used interchangeably.

### 2. Feature-based trackers
1. Here instead of using location information (IoU), we use the features of the detected bounding boxes.

2. We first find the bounding boxes in two frames. Then we calculate the features for each of the bounding boxes.

3. Then we can use cosine similarity to calculate the similarity of all the boxes from frame 1 to all the boxes from frame 2.

4. We can give two detected boxes the same tracking id if they have a similarity value higher than a threshold and they don't have any higher similarity matching with another bounding box.

## Comparison of location-based and feature-based trackers
- Location-based tracker fails when there is movement in-camera because then there might be high relative movement of an object across the frame the IoU value would be 0. In this case, the feature-based method will work because the relative position does not matter here.
- If the detection algorithm has a low recall, then there will be objects which will be missed by the detector and if the detector fails for the same object continuously then when it will be detected again it might not have positive IoU with the last detected instance of this object in the previous frame.
- Feature-based methods work fine with models with low recall. Here instead of checking one of the previous frames for matches, we can check some N numbers of the previous frames, so that even if the model does not detect an object in some of the frames when it detects the object again we have some old history to assign the same tracking id instead of a new one.
- The feature-based method fails if there is very little distinction between two objects. For example, for tracking objects on a high-altitude thermal camera. Here every person will be a white blob (or black-blob based on the camera in white-hot mode or black hot mode) and will have a similar feature. The location-based method works fine here.
- Location-based methods are also simple. We need to extract features for all the detected boxes and then calculate some similarity metrics for the feature-based method. Compared to that, in the location-based method, we can just calculate the IoU. A location-based method in general will be faster than a feature-based method.
- Location-based methods take the assumption that cameras will have high FPS. Because most of the recent cameras have more than 30 FPS, it should not cause any issues.

## ByteTrack
### ByteTrack algorithm
- ByteTrack is an IoU-based association algorithm.
- Most methods obtain identities by associating detection boxes whose scores are higher than a threshold. The objects with low detection scores, e.g. occluded objects, are simply thrown away, which brings non-negligible true object missing and fragmented trajectories.
- To solve this problem, ByteTrack uses both high and low-confidence bounding boxes.

Let's see the algorithm step by step:

1. Let's assume a few things to understand the pseudo-code: The inputs are as follows: A video sequence V; object detector Det(this is YoloX); detection score threshold τ.
2. The output would be: Tracks `T` of the video
3. In beginning, we will start with empty tracks.
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_1.png" alt="ByteTrack algo part 1"/>
</center>

4. For each frame in the video, we predict the detection boxes and scores using the YoloX detector.
5. We separate all the detection boxes into two parts `D_high` and `D_low` according to the detection score threshold `τ`.
6. For the detection boxes whose scores are higher than τ, we put them into the high score detection boxes `D_high`. For the detection boxes whose scores are lower than τ, we put them into the low score detection boxes `D_low`
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_2.png" alt="ByteTrack algo part 2"/>
</center>

7. After separating the low score detection boxes and the high score detection boxes, we adopt the Kalman filter to predict the new locations in the current frame of each track in `T`
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_3.png" alt="ByteTrack algo part 3"/>
</center>

8. The first association is performed between the high score detection boxes `D_high` and all the tracks `T` (including the lost tracks `T_lost`).
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_4.png" alt="ByteTrack algo part 4"/>
</center>

9. We keep the unmatched detections in `D_remain` and the unmatched tracks in `T_remain`
10. The second association is performed between the low score detection boxes `D_low` and the remaining tracks `T_remain` after the first association.
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_5.png" alt="ByteTrack algo part 5"/>
</center>

11. We keep the unmatched tracks in `T_re−remain` and just delete all the unmatched low score detection boxes, since we view them as background.
12. For the unmatched tracks `T_re−remain` after the second association, we put them into `T_lost`. For each track in `T_lost`, only when it exists for more than a certain number of frames (in the paper this value is 30 frames), we delete it from the tracks `T` . Otherwise, we remain the lost tracks `T_lost` in `T`
13. Finally, we initialize new tracks from the unmatched high score detection boxes `D_remain` after the first association.
<center>
<img width='100%' src="/assets/images/bytetrack/bytetrack_algo_6.png" alt="ByteTrack algo part 6"/>
</center>

**Note**: for the association, we can use either location or feature-based method depending on the problem statement. The main addition of ByteTrack is of using both low and high-confidence bounding boxes.

### ByteTrack implementation
- Here I will explain important parts of the [official implementation](https://github.com/ifzhang/ByteTrack). Full implementation of ByteTrack code is [here](https://github.com/ifzhang/ByteTrack/blob/main/yolox/tracker/byte_tracker.py)
- We initialize a few lists to keep tracking history:
  
  1. `tracked_stracks`: tracks we are currently tracking and present in current frames.
  2. `lost_stracks`: tracks that we are currently tracking but missing in the current frame. Based on `buffer_size`(saved in `self.max_time_lost`).
  3. `removed_stracks`: tracks that we have removed but tracked before.
  4. We also initialize the kalman filter object to predict the location of the previously detected objects in the current frame. This along with high FPS with getting high IoU between the same object across frames.
  ```python
  class BYTETracker(object):
    def __init__(self, args, frame_rate=30):
        self.tracked_stracks = []  # type: list[STrack] # this is T
        self.lost_stracks = []  # type: list[STrack] # this is T_lost
        self.removed_stracks = []  # type: list[STrack] helps removing lost tracks if duration is finished

        self.frame_id = 0 # current frame id
        self.args = args
        #self.det_thresh = args.track_thresh 
        self.det_thresh = args.track_thresh + 0.1
        self.buffer_size = int(frame_rate / 30.0 * args.track_buffer) # how many frames to keep the lost frames
        self.max_time_lost = self.buffer_size # buffer size
        self.kalman_filter = KalmanFilter() # kalman filter, we will check this in details later
  ```
  
- The update function takes care of associating tracks.
    1. First, we rescale the bounding boxes to the original image size.
    2. Original image size information is saved into `img_info` variable and test image size is saved into `img_size` variable. We calculate the scale ratio and then rescale the bounding boxes to the original image size.
    ```python
    def update(self, output_results, img_info, img_size):
            """
            Update function in bytetrack class
            Args:
            output_results: predictions fro object detection [N, x1,y1,x2,y2, score], [N, x1, y1, x2, y2, cls_conf, obj_conf]
            img_info: original image size info
            img_size: test image size info
            """
            self.frame_id += 1
            activated_starcks = []
            refind_stracks = []
            lost_stracks = []
            removed_stracks = []

            if output_results.shape[1] == 5: # [N, x1,y1,x2,y2, score]
                scores = output_results[:, 4]
                bboxes = output_results[:, :4]
            else: # [N, x1, y1, x2, y2, cls_conf, obj_conf]
                output_results = output_results.cpu().numpy()
                scores = output_results[:, 4] * output_results[:, 5]
                bboxes = output_results[:, :4]  # x1y1x2y2
            img_h, img_w = img_info[0], img_info[1]
            scale = min(img_size[0] / float(img_h), img_size[1] / float(img_w))

            bboxes /= scale # rescale bboxes back to original image size
    ```
    
    3. Next, we separate bounding boxes into high confidence bounding boxes and low confidence bounding boxes.
    4. Here, we discard bounding boxes with less the 0.1 confidence. Low confidence bounding boxes have confidence between 0.1 and `self.args.track_thresh`
    5. High confidence bounding boxes are boxes with confidence higher than `self.args.track_thresh`
    
        ```python
        remain_inds = scores > self.args.track_thresh
        inds_low = scores > 0.1
        inds_high = scores < self.args.track_thresh

        inds_second = np.logical_and(inds_low, inds_high)
        dets_second = bboxes[inds_second]
        dets = bboxes[remain_inds]
        scores_keep = scores[remain_inds]
        ```
    
    6. Next, first, we convert each detection into tracks. Then we create a track pool by merging lost tracks with currently tracked tracks.
    7. We update track pool tracks with Kalman filter to get tracks with respect to the current frame.
    
        ```python
        if len(dets) > 0:
            '''Detections'''
            detections = [STrack(STrack.tlbr_to_tlwh(tlbr), s) for
                        (tlbr, s) in zip(dets, scores_keep)]
        else:
            detections = []

        ''' Add newly detected tracklets to tracked_stracks'''
        unconfirmed = []
        tracked_stracks = []  # type: list[STrack]
        for track in self.tracked_stracks:
            if not track.is_activated:
                unconfirmed.append(track)
            else:
                tracked_stracks.append(track)
        ''' Step 2: First association, with high score detection boxes'''
        strack_pool = joint_stracks(tracked_stracks, self.lost_stracks)
        # Predict the current location with KF
        STrack.multi_predict(strack_pool)
        ```
    
    8. Then we calculate the IoU distance between the track pool tracks and detection tracks and assign detections with tracks. We update the matched tracks with current detection boxes.
    
        ```python
        dists = matching.iou_distance(strack_pool, detections)
        if not self.args.mot20:
            dists = matching.fuse_score(dists, detections)
        matches, u_track, u_detection = matching.linear_assignment(dists, thresh=self.args.match_thresh)

        for itracked, idet in matches:
            track = strack_pool[itracked]
            det = detections[idet]
            if track.state == TrackState.Tracked:
                track.update(detections[idet], self.frame_id)
                activated_starcks.append(track)
            else:
                track.re_activate(det, self.frame_id, new_id=False)
                refind_stracks.append(track)
        ```
    
    9. Similarly, we take low confidence bounding boxes and update the tracks (only the tracks which were not matched during the first association) with detection information from the current frame.
    
        ```python
        ''' Step 3: Second association, with low score detection boxes'''
        # association the untrack to the low score detections
        if len(dets_second) > 0:
            '''Detections'''
            detections_second = [STrack(STrack.tlbr_to_tlwh(tlbr), s) for
                            (tlbr, s) in zip(dets_second, scores_second)]
        else:
            detections_second = []
        r_tracked_stracks = [strack_pool[i] for i in u_track if strack_pool[i].state == TrackState.Tracked]
        dists = matching.iou_distance(r_tracked_stracks, detections_second)
        matches, u_track, u_detection_second = matching.linear_assignment(dists, thresh=0.5)
        for itracked, idet in matches:
            track = r_tracked_stracks[itracked]
            det = detections_second[idet]
            if track.state == TrackState.Tracked:
                track.update(det, self.frame_id)
                activated_starcks.append(track)
            else:
                track.re_activate(det, self.frame_id, new_id=False)
                refind_stracks.append(track)
        ```
    
    10. The remaining unmatched tracks will be added to the lost track list.
    
        ```python
        for it in u_track:
            track = r_tracked_stracks[it]
            if not track.state == TrackState.Lost:
                track.mark_lost()
                lost_stracks.append(track)
        ```
    
    11. For unmatched high detection boxes, if the detection confidence is higher than det_thresh then we add them as new tracks.
    
        ```python
        """ Step 4: Init new stracks"""
        for inew in u_detection:
            track = detections[inew]
            if track.score < self.det_thresh:
                continue
            track.activate(self.kalman_filter, self.frame_id)
            activated_starcks.append(track)
        ```
    
    12. For all the tracks in the lost track list, if time is higher than max_time_lost then we discard those tracks.
    
        ```python
        """ Step 5: Update state"""
        for track in self.lost_stracks:
            if self.frame_id - track.end_frame > self.max_time_lost:
                track.mark_removed()
                removed_stracks.append(track)
        ```

## Results
<center>
<img width='100%' src="/assets/images/bytetrack/results_1.png" alt="ByteTrack results"/>
<img width='100%' src="/assets/images/bytetrack/results_2.png" alt="ByteTrack results"/>
<img width='100%' src="/assets/images/bytetrack/results_3.png" alt="ByteTrack results"/>
<em>ByteTrack performance (Source: ByteTrack paper)</em>
</center>

**Note**: There are many benchmark tests done by the authors of the paper and details are in the paper.

<center>
<img width='100%' src="/assets/images/bytetrack/papers_with_code_plot.png" alt="Papers with code SOTA methods"/>
<br/>    
<small class="caption"><i><a href="https://paperswithcode.com/sota/multi-object-tracking-on-mot17">SOTA methods on MOT17 in papers with code</a></i></small>
</center>

Hope you enjoyed this paper. Have a nice day.

## Resources:
1. [GitHub repo](https://github.com/ifzhang/ByteTrack)
2. [ByteTrack paper](https://arxiv.org/abs/2110.06864)
3. [Paper](http://elvera.nue.tu-berlin.de/files/1517Bochinski2017.pdf)

  
