# **Finding Lane Lines on the Road** 
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)

<img src="examples/laneLines_thirdPass.jpg" width="480" alt="Combined Image" />

NOTE: WRITEUP ALSO AVAILABLE IN P1_WRITEUP.MD

## P1 Writeup

## Selin Sirinterlikci

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

To perform lane edge detection on an image (or a series of images), I set up the following pipeline:
1. Convert the image to grayscale. Function used: grayscale()
2. Perform Gaussian smoothing on the grayscale image with a kernel size of 7. Function used: gaussian_blur()
3. Set lower and upper thresholds of 50 and 100, respectively, and run Canny edge detection on the image obtained in Step 2. Function used: canny()
4. Create an image mask to check for lane edges only in a specific part of the image.
5. Using the image mask created in Step 4, mask the Canny edge detection image. Function used: region_of_interest()
6. Define rho, theta, thresholds, minimum line length and maximum line gap for the upcoming Hough transform.
7. Perform a Hough transform on the masked image created in Step 5, using the transform parameters defined in Step 6. Function used: hough_lines()
8. Weight the image after the Hough Transform to add highlighted lane edge lines on top of the image. Function used: weighted_img()

In Step 7 of this pipeline, the Hough Transform calls a function called draw_lines(). The original implementation of this function relied on no differentiation between left and right lines and potentially drawing multiple copies of the same line on top of each other. Lanes frequently do not look the same on both sides. There may be a yellow line on one side and white dashed on the other, or double lines on one side and single lines on the other. Due to the angle of the road, there may even be different lines and slopes that should be accounted for on both sides. To make the draw_lines() function work in more of these scenarios, I utilize the simple slope/intercept function to calculate new X and Y values on both the left and right lane. The left line can be differentiated from the right line when drawing the new interpolated lines based on the slope of the line. The left line should always have a positive slope in the image, and the right line should always have a negative slope. A new, interpolated left and right line will be drawn each time this function is called, based on the interpolated x and y values.


### 2. Identify potential shortcomings with your current pipeline

The current pipeline can be a little jumpy from frame to frame since it is recalculating the slope, intercepts, and interpolated line each time. When I changed the implementation, a wider lane would cause multiple lane lines to be generated, and the lane line image would look "frayed" on the bottom.


### 3. Suggest possible improvements to your pipeline

An improvement that could be made to the pipeline is finding ways to "weight" the interpolated value. Looking at the previous interpolated coordinates, you might be able to weight that value with the newly calculated coordinates to find a more ideal x and y coordinate to base your line off of. Another improvement that could be made is trying to find locations where the lines may differ from each other on each side and perhaps perform an average of all those lines. This should also handle the "fraying" behavior that I described in Part 2 of this writeup, without requiring a new line to be drawn based on no information from a previous frame.
