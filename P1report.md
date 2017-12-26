# **Finding Lane Lines on the Road** 

Shi Qiu
---
[//]: # (Image References)

[c1]: ./writeup/c1.png "Grayscale"
[c2]: ./writeup/c2.png "Edges"
[c3]: ./writeup/c3.png "LeftLane"
[c4]: ./writeup/c4.png "RightLane"
[c5]: ./writeup/c5.png "FianlLane"
[h1]: ./writeup/h1_50.png "Before transform"
[h2]: ./writeup/h2_50.png "After transform"

## Reflection

### 1. Pipeline.

My pipeline consisted of 8 steps. 

1. Convert the images to grayscale.  

2. Apply Gaussian smoothing to the grayscale images to reduce noises.

3. Apply canny edge detection to extract all the edges from the image.

4. Apply image masks to extract the region of interest. Here, in order to extract the features of the left lane line and right lane line for better detection results, two masks are created to detect the left line and right lane separately. 

5. Before applying hough line transformation to each masked image. A horizontal line filter function is applied to each image first to get rid of the horizontal lines. This is achieved by using the Scharr function in opencv library. The effects can be seen from the following images.  Left image is before filtering, right image is after filtering. The reason I did this is lane lines should be always somewhat vertical. By eliminating horizontal edges in the image can help to better detect the vertical features. Even though not all edges are strict horizontal, this function can still break down the edges that are more horizontal than vertical into smaller line segments.   ![alt text][h1]![alt text][h2]
 
6. Apply hough line transformation to the filtered images. To filter the horizontal line segments that are generated from previous step, we will choose the minimum number of pixels making up a line to be a slightly bigger number. After testing all three videos, we found that 30 is a good number. 

7. After obtaining the lines from hough line transformation, to further eliminate the edges that do not belong to lane lines, I built a function that filter the lines that do not meet the following objective. The lines from hough line transformation contains information of the starting point (x1, y1) and the ending point (x2, y2) of each line, where x2 is always bigger than x1 due to the direction of algorithm for searching lines. For left lane line detection, it should always point to the right thus I set that y2-y1 < -20. For right lane line detection, it is the opposite, y2 - y1 > 20. 

8. The last step is to draw lines on the image. In order to draw a connected line, a least squares regression line is developed using all the starting points and ending points from the detected lines.  

The following images show the general steps: 

**Greyscale**

![alt text][c1]

**Canny Edge**

![alt text][c2]

**Left lane detection, red line is the regression line**

![alt text][c3]

**Right lane detection, red line is the regression line**

![alt text][c4]

**Final lane detection**

![alt text][c5]

### 2. Identify potential shortcomings with the current pipeline

The current pipeline can eliminate both the horizontal noise and lines with opposite directions. However, it cannot take into all possible scenarios into account. For example, it is possible that the road condition is not good, there may be gaps on the road, which current pipeline cannot eliminate. In addition, the lane line markings on some old roads may fade away and there may be other cars or trucks blocking the markings. Different weather conditions may also affect current pipeline. 

To sum up, this pipeline is only the very first step to detect the lane line. It is restricted to a good condition on the road (no gaps, no faded color lines and a clear view in front of the camera). It is not generalized to perform well on all kinds of road conditions.   

### 3. Future improvement

A general improvement can be made to add more constrains to filter the noisy edges. For example, we can calculated the range of the slope for left lane and right lane at all driving scenarios (drive straight, sharp turn left or right, soft turn left or right). In addition, we can obtain the information of the lane width and add the width constrain on the detected land line (this may involve some 2D to 3D reconstruction).  

Besides the general improvement, the current pipeline needs to be tested on all kinds of extreme conditions, such as night view, snow weather, foggy weather, old bumped road. Based on these extreme conditions, the parameters used in the pipeline can be optimized. 

  
