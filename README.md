# **Finding Lane Lines on the Road**

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/grid.png "Grid"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 7 steps as described as follows:
1. The images were converted to gray scale since canny edge detection works on gray scale images.
1. Gaussian blurring was applied to the images to reduce the noises
1. Edges were found by using canny edge detection.
1. Region mask was applied to the binary edge image.
1. Lines were found by using Probabilistic Hough Transform.
1. Left and right lines were calculated.
1. Left and right lines were drawn onto original images.

I modified two function, `draw_lines` and `hough_lines` and added a function named `reduce_lines`.

#### draw_lines
The modified `draw_lines` header is now `draw_lines(imshape, lines, color=[255, 0, 0], thickness=2)`. Currently, the function creates an empty image which has a shape as `imshape`, draws lines on this empty image and return final image.

#### houng_lines
The header of this function is same as before but it now only calls the houng transform function of opencv and returns found lines.

#### reduce_lines
This is newly added function and its header is `reduce_lines(lines, y_top, y_bottom, min_slope, max_slope)`.

The idea behind this function is to classify lines into two groups and average them in a way to find left and right lines of lane.

Briefly, `reduce_lines` reduces lines found by hough transformation into two lines, left and right. First, it groups lines into two groups one for left and one for right by looking their slope like this:
* If a line slope is between `max_slope` and `min_slope`, the function saves the line in right lines group.
* If a line slope is between `-min_slope` and `-max_slope`, the function saves the line in left lines group.
* Otherwise, the line is discarded.

Then, the function calculates its cross point of x-axis while `y = y_bottom` and also saves it. After completing the grouping, slopes and cross points of x-axis while `y = y_bottom` of left and right lines are calculated by averaging the slopes and cross points in each groups. Now, we have two slope and two bottom points which define two lines, left and right. Then, the function calculates top points. Finally, left and right lines are rearranged as `draw_lines` requested.

Here is a grid of calculated some intermediate images and final image of the pipeline:
![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline

The potential shortcomings would be:
1. The current pipeline only considers straight road lanes but roads are generally not straight.
1. Region of interest may prevent finding edges while road has steep slopes.
1. `reduce_lines` requires two predefined parameters which are `max_slope` and `min_slope` and calculates left and right lines by using them. When the road has sharp turn, it would not calculate left and right lines.
1. Illumination may disrupt edge detection.

### 3. Suggest possible improvements to your pipeline

Possible improvements would be:
1. Curve fitting may solve winding road problem instead of finding lines.
1. Adaptive region of interest can be implemented by using the information of frame sequence of videos because previous frame unveils information for next frame
1. Again, `max_slope` and `min_slope` should be adaptive to keep up with the changing conditions of the road.
1. Local edge detection may decrease effect of illumination.
