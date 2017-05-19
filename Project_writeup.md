**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/window.jpg
[image4a]: ./examples/pipeline1.jpg
[image4b]: ./examples/pipeline2.jpg
[image5]: ./examples/heatmap.jpg
[video1]: ./project_video_output.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!









### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I wrote a ```FeatureExtraction``` class for extracting color and hog feature. The relevant codes for this step is contained in the 12th code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]



I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]


#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters finallay getting these values:

```
colorspace = 'YCrCb' 
orient = 9
pix_per_cell = 8
cell_per_block = 2
hog_channel = "ALL"
hist_bins = 32
hist_bins_range = (0,256)
spatial_size = (32,32)


```

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I used both color feature and hog features (using color chanel = YCrCb) and codes are available at 24,25,26,27,28 code block of notebook under the topics classifier.
I shuffled the data and splited them into test and train and used ```LinearSVC``` with default setting of ```square-hinged``` loss function and ```l2``` normalization. I got accuracy of ```98.96% ~ 99%``` on test dataset. The trained model alongwith parameter used were saved in ```svc_pickle.p``` file.

-------------





### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?
Instead of using whole image, the multi-scale window approach prevents calculation of feature vectors for the complete image and thus helps in speeding up the process. I used the following set of  ```ystart, ystop``` and ```scale``` for sliding window search:

```
[(360, 560, 1.5),
(400, 600, 1.8), 
(440, 700, 2.5)]
```

I determined the scale for the multi-window search and overlap by emperical study.

Following are the images for multi scale sliding windows:



![alt text][image3]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?



  First of all I recorded the position of positive detections in each frame of the video. Those positive detection were further used to create a heatmap and threshold was used to identify actual vehicle position. I further used ```label``` to find individual blobs in the heatmap. Then a bounding box was created including those individual blobs.

  I optimized the search by skiping 4 frames before processing one complete frame and those skipped frames were also approached by restricted search. The restricted search was performed by appending 50 pixel to the heatmap found in last three frames.
  
  Following are the images for pipeline:

![alt text][image4a]
![alt text][image4b]




----------------




### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are three frames and their corresponding heatmaps:

![alt text][image5]

-----------------

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  

1. A neural network is a good candidiate to try out for building classifier model. I would try this out for fun.
2. Multi window search could be optimized further, what I implemented could be poor one.
3. This model may fail even if there are different lightening and intensities or the illumination conditions.
4. Feature extraction step could me made more precise and best representetive for the data.





