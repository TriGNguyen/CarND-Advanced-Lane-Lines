
**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistorted_output.png "Undistorted"
[image2]: ./examples/undistorted_test1.png "Road Transformed"
[image3]: ./examples/lanelines.png "Lane Lines"
[image4]: ./examples/warped_lanelines.png "Warped Lane Lines"
[image5]: ./examples/fit_lines.png "Fit lines"
[image6]: ./examples/final_output.png "Final Output"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the "In [3]" cell of the IPython notebook located in "./Pipeline.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
In "In [5]" cell, I apply the "PERPECTIVE_TRANSFORM_MATRIX" to the "test_1.jpg" image and obtain the following:
![alt text][image2]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
In "In [7]" cell, I used a combination of color and gradient thresholds to generate a binary image. I thresholded the image on s channel, gradient in x direction, griadient in y direction, gradient manitute and gradient angle.  I then crop the image to the proportion of the lanelines. Here's an example of my output for this step.

![alt text][image3]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

In "In [9]" cell, I perform perspective transform on the lane lines.
The source and destination points are:

WARP_SOURCE =      [(267, 713), (589, 461), (707, 460), (1152, 712)]
WARP_DESTINATION = [(300, 720), (300, 0),   (900, 0),   (900, 720)]

I applied the results bellow after perspective transform:

![alt text][image4]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In "In [10]" cell, I search for the centroids of the lane lines, by first convolving the pixel histogram with a gausian, and then selecting the position where the covlution value is highest.
Given the centroids, I collect all the positive pixels on the left and right of the centroids.
In "In [14]" cell, I draw the line of best fit to all positive pixels.

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

In "In [16]" cell, I first create best fit lines for left and right lanes with scaling the pixel to actual measurement, then I apply a function to convert the polinomial to the curvature radius.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I execute all steps above in "In [19]" cell:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I have tuned many parameters including edge detection thresholds (s chanel, x/ y/ manitude/ gradient), centroild finding (number of pixels), lane detection (search window width and height). These parameters work well for this video and other similar diving conditions (lightness, road colors, road width, camera relative position to the lane). We may need to recalibrate these parameters when the driving condition changes (diffent weather/ road), or train a Machine Learning to adapt the parameters to images.

