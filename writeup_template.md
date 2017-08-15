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

[image1]: ./output_images/chessboard_original.jpg "origin"
[image2]: ./output_images/chessboard_undistort.jpg "Undistorted"
[image3]: ./output_images/origin.jpg "origin"
[image4]: ./output_images/undistorted_image.jpg "Undistorted"
[image5]: ./output_images/rgb_warped.jpg "Warp Example"
[image6]: ./output_images/color_stack.jpg
[image7]: ./output_images/combined_binary.jpg
[image8]: ./output_images/visualize_hist_search.jpg "Histogram search"
[image9]: ./output_images/visualize_conv_search.jpg "Convolutional search"
[image10]: ./output_images/curvature_radius.jpg "snapshoot_for_vehicle"

[video1]: ./result.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the python of the file called `camera_calibration_.py`).  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

original image:
![alt text][image1]
undistorted image
![alt text][image2]

After using the camera_calibration.py and generate the camera_calibration_result.p. This could be used in the P4.ipynb

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:

I use the LaneFinder class and visualize the result in the second section of my P4.ipynb notebook. Here I use the test 1 as an example to demonstrate the result

Test 1 origin
![alt text][image3]

Test 1 distortion-corrected image
![alt text][image4]

I use the camera 

lf = LaneFinder()
lf.calibrate_camera()
img = cv2.imread('./camera_cal/calibration1.jpg')
rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)
undist = lf.undistort(img)



#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I do the color transform and thresholding step after doing the perspective transform.
I used a combination of color(S_channel) and gradient thresholds(L_channel) on the HLS. S channel did will on most of the ase but it could not use under massice shadows on the road so the L_channel could help to complete. The L_direction,S_magnitude and combined_thresholding function could find the detail.

Origin
![alt text][image5]
Stacked binary
(red: L_channel  green: S_channel)
![alt text][image6]
combine binary result
![alt text][image7]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform`in the "LanFinder" class,and the `get_perspective_transform` function could generate the transform matrix.

This resulted in the following source and destination points:

|               | Source        | Destination   | 
|:-------------:|:-------------:|:-------------:| 
|:top left-----:| 585, 470      | 300, 300        | 
|:right bottom-:| 220, 720      | 300, 720      |


I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

Wraped the image
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

For finding the lane lines, I first use a histogram search to find lane lines naively I first think that i could use this to detect the lane and finally found that I failed since the result from historgram function is not accurate enough so I use a convolutional search to find a more accurate position about the lines. The implementaion detail is inside the find_lines, histogram_find_lines and convolution_find_lines methods of the LaneFinder class

I verified this by visualizing the results:
Origin
![alt text][image5]

Histogram search
![alt text][image8]

Convolutional search
![alt text][image9]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I calculate the radius of the curvature by calculating the second dirivitive of the fitted polynomial over the bottom of the fitted line, and I assumed the warped pixel space has 30/720 meters per pixel height and 3.7/700 meters per pixel width. (please see the curvature_radius method in the Line class for more implementaion detail). As the offset to the center, I calculate the x pixel at the bottom of both lines and average them as the center of the lines, and convert the pixel difference between center of the lines and center of the image into meters. See img_pipeline for more detail The result will be demonstrated in the next section.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 18 section in my code in P4.ipynb. Here is an example of my result on a test image:

![alt text][image10]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [video1]:

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I first use the Histogram method first and i find that most of the time the line would not change on the road. The reason is that the range for accurancy by using the Histogram method is not enough to detect the curve on the road. So I apply another method Convolutional search and find that this would give accurancy data to detect the road. However, the idea could figure out most of the time on the video.Some shadow of the tree cause the S channel fails to get the lines. Also the bumpy road would lead to the perspective transform matrix changes. I skipped some bad frames to avoid it. The result video looks quit good.(See the line_valid in Line class for more information.)
