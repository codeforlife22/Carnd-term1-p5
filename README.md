
**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Apply a color transform and append binned color features, as well as histograms of color, to HOG feature vector. 
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run the pipeline on a video stream and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/training_example.png
[image2]: ./output_images/HOG_example.png
[image3]: ./output_images/HOG_example2.png
[image4]: ./output_images/HOG_Color.png
[image5]: ./output_images/HOG_sub1.png
[image6]: ./output_images/HOG_sub2.png
[image7]: ./output_images/heatmap1.png
[image8]: ./output_images/heatmap2.png
[image9]: ./output_images/final1.png
[image10]: ./output_images/final2.png
[video1]: ./project_video.mp4


### Histogram of Oriented Gradients (HOG)

#### 1. Feature extraction

The code for this step is contained in the cells [10] and [11] of the IPython notebook (car_detection.ipynb).

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]
![alt text][image3]
#### 2. Parameter tuning

I tried various combinations of parameters and and tested on random images to get some intuition. Using the params from the lecture/quiz, results were satisfactory. In addition, HOG in YCrCb color space seem to a natrual choice. 

#### 3. Model traning

I trained a linear SVM using HOG and color features extracted from images. In paticular, the features are arranged in the following order, raw pixel spatial feature, color histogram feature, and HOG feature. Features are normalized/scaled to prevent output from being biased towards certain inputs. The image set is split into test set and training set with a ratio of 0.2. 

### Sliding Window Search

#### 1. Navie sliding window (without optimiztion)
As the region of interest is the bottom half of the image, starting from the left corner and moving the window right and downward, all positive windows are recorded (determined to be vehicle image by the previously trained classifer). 
The results for six test images are shown below:
![alt text][image4]

As can be seen from the picture, cars are identified successfully, but there are some redudant and false boxes. 
In the following, two optimization techniques will be discussed.
#### 2. HGO sub-sampling window search (improved)
The find_cars only has to extract hog features once, for each of a small set of predetermined window sizes (defined by a scale argument), and then can be sub-sampled to get all of its overlaying windows
Also, each window is defined by a scaling factor that impacts the window size which can be tuned to draw reasonable size of boxes around cars.

Here is an example (using the first image in the figure above)

![alt text][image5]

#### 3. Heatmap and labeling
I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. 

![alt text][image7]
![alt text][image8]

#### 4. Final pipeline
Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Addtionally, heatmap is used to remove false positives and combine overlapping boxes. The results seemed to be very satisfactory. Here are some example images:
![alt text][image9]


![alt text][image10]

---

### Video Implementation

Here's a [link to my video result](https://github.com/codeforlife22/Carnd-term1-p5/blob/master/project_video_out.mp4)


### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
Most of the params are hard coded. The lack of flexibility should be addressed before the pipeline can be applied to more general cases (e.g. cars with different sizes, vehicles from distances etc.)
It takes quite a long time to finsih generating the project vedio, some optimization techniques should be studied to improve the timing complexity. 

