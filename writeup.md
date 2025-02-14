## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

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
[image1]: ./camera_cal/calibration3.jpg "Before"
[image1_u]: ./camera_cal_output/Output_calibration3.jpg "After distortion correction"
[image2_u]: ./output_images/Undistort_test3.jpg "Road Transformed"
[image2]: ./test_images/test3.jpg "Road Transformed"
[image3]: ./output_images/Transformed_straight_lines2.jpg "Binary Example"
[image4]: ./test_images/straight_lines1.jpg "Original Example"
[image4_w]: ./output_images/Projected_straight_lines1.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./output_images/Final_test3.jpg "Output"
[video1]: ./solution_project.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/karabC/CarND-Advanced-Lane-Lines/writeup.md) is a template writeup for this project you can use as a guide and a starting point.  

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the forth code cell of the IPython notebook located in "./Advance Lane Lines.ipynb". (under section "Calibrating the camera by the Chess box Image")

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]
![alt text][image1_u]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
##### Before correction
![alt text][image2]
##### After correction
![alt text][image2_u]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of HLS and gradient thresholds to generate a binary image. Here's an example of my output for this step. Please refer to section "Transform the image" in the notebook.

![alt text][image3]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`, which appears in second cell of Section "Perspective transform" of the notebook.  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src2 = np.float32([(576,465),
                  (705,465), 
                  (300,685), 
                  (1000,685)])
dst1 = np.float32([(450,0),
                  (width - 450,0),
                  (450,height),
                  (width -450,height)])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]
![alt text][image4_w]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

After warpping the images, I use the histogram approach to identify the interested part from bottom. Then build the rectangle blocks above. Then I fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

Before the further calculation, I applied a transformation to the real space.
The code detail please refer to the section "Find Lane lines"

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Please refer to the section "Compute Curvature". The radius would be the average of the left and right curvature.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in the last cell of section "Find Lane lines".  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./solution_project.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I found that is hard to manually determinate the perspective transformation src/dest coordinates. Also, my pipeline will sometime fail in the place with intense sun light on the yellow lane-line. Tunning on transformation should help the cases.
