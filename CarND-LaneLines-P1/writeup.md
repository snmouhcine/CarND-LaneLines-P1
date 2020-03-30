# **Finding Lane Lines on the Road** 

## Writeup


---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

## Overview:

The goal of this project is to make a pipeline that finds lane lines on the road using Python and OpenCV. 

<img src="./test_images/solidWhiteRight.jpg" width="360" /> 

The pipeline will be tested on some images and videos provided by Udacity. The following assumptions are made:
* The camera always has the same position with respect to the road
* There is always a visible white or yellow line on the road
* We don't have any vehicle in front of us 
* We consider highway scenario with good weather conditions

---

## Reflection

###1. Describe your pipeline.

I will use the following picture to show you all the steps:  

<img src="./img_doc/original.png" width="360" alt="Combined Image" />

#### Color selection   
Firstly, I applied a color filtering to suppress non-yellow and non-white colors. The pixels that were above the thresholds have been retained, and pixels below the threshold have been blacked out.  

I will keep aside this mask and use it later.

#### Convert the color image in grayscale  

The original image is converted in grayscale. In this way we have only one channel




#### Use Canny for edge detection 
Before running the [Canny detector](http://docs.opencv.org/2.4/doc/tutorials/imgproc/imgtrans/canny_detector/canny_detector.html), I applied a [Gaussian smoothing](http://docs.opencv.org/2.4/modules/imgproc/doc/filtering.html?highlight=gaussianblur#gaussianblur) which is essentially a way of suppressing noise and spurious gradients by averaging. The Canny allows detecting the edges in the images. To improve the result, I'll try to use the OpenCV function `dilate` and `erode`. 



#### Merge Canny and Color Selection
In some cases, the Canny edge detector fails to find the lines. For example, when there is not enough contrast between the asphalt and the line, as in the challenge video. The color selection, on the other hand, doesn't have this problem. For this reason, I decided to merge the result of the Canny detector and the color selection:   



#### Region Of Interest Mask
I defined a left and right trapezoidal Region Of Interest (ROI) based on the image size. Since that the front facing camera is mounted in a fix position, we supposed here that the lane lines will always appear in the same region of the image. 
 


#### Run Hough transform to detect lines  
The Hough transform is used to detect lines in the images. At this step, I applied a slope filter to get rid of horizontal lines. 


#### Compute lines
Now I need to average/extrapolate the result of the Hough transform and draw the two lines onto the image. I used the function  [`fitLine`](http://docs.opencv.org/2.4/modules/imgproc/doc/structural_analysis_and_shape_descriptors.html#fitline)



## Results:

See the Python Script


You can find the original pictures and the results in the folder `test_images`.


### Videos
Here some results on test videos provided by Udacity:   


You can find the video files here: [video1](./yellow.mp4), [video2](./white.mp4).

#### Optional challenge:

I got a satisfactory result on the first two videos provided by Udacity, it was not the case for the challenge video. In the challenge video we can identify more difficulties:
* The color of the asphalt became lighter at a certain point. The Canny edge detector is not able to find the line using the grayscale image (where we lose information about the color)
* The car is driving on a curving road
* There are some shadows due to some trees  

To overcome theses problems, I introduced the color mask and resized the ROI. The right lane was detected but not the left.
 

The right line is a little jumpy mainly because of the curve: the function `fitline` is trying to fit a line on a curvy lane. It would be useful to shrink the ROI in this case, but I preferred to keep the same ROI size used in the first two videos.

I had some problems with this challenges, so due lack of time I didn't finished. 



###2. Identify potential shortcomings with your current pipeline

* This approach could not work properly:
    * if the camera is placed at a different position
    * if other vehicles in front are occluding the view
    * if one or more lines are missing
    * at different weather and light condition (fog, rain, or at night)


###3. Suggest possible improvements to your pipeline

Some possible improvements:

* Perform a color selection in the HSV space, instead of doing it in the RGB images
* Update the ROI mask dynamically
* Perform a segmentation of the road
* Using a better filter to smooth the current estimation, using the previous ones
* If a line is not detected, we could estimate the current slope using the previous estimations and/or the other line detection
* Use a moving-edges tracker for the continuous lines