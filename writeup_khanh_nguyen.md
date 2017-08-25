# **Finding Lane Lines on the Road** 

By Khanh Nguyen
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline follows closely the steps outlined in the video lectures. The steps are as follow:

Step 1: Convert the images to grayscale and smooth them with gaussian blur.
Step 2: Apply canny edge detection on the images
Step 3: Select a ROI with 4 vertices. 
Step 4: Apply Hough transformation to detect lines and extrapolate two left and right lane. 
Step 5: Blend the image from 4) with the original image to get the final result.

In order to draw a single line on the left and right lanes, I modified the draw\_lines() function as follow. First, I segment the lines into two sets: left\_lane set and right\_lane set. The left\_lane set contains lines whose slope is negative. The right\_lane set contains lines whose slope is positive. In the beginning, it took me a bit to realize that the coordinate is slightly different. The point (0,0) is the top left, instead of bottom left so I mistakenly put positive-slope lines in the left\_lane set and negative-slope lines in the right\_lane set.

Furthermore, I use two filters to refine the line selection. First, I only pick lines whose angle is between 30 and 60 degree. Second, I pick lines a little below y=320 because I observes that at the top of the ROI there usually too many noise (i.e house, tree, other cars..) which affect my calculation of the slope and intercept of the lane line.

Once the lines for each lane have been selected, I extrapolate one single line from each set to be the lane line. To do that, I take the average of x1,x2,y1,y2 and fit a line to (x1\_avg, y1\_avg), (x2\_avg, y2\_avg) using np.polifit(). The function returns a [slope, intercept], which I use to calculate two top and bottom points for the lane, given the y-coordinate of the points are 330 and img.shape[0].


### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when there is no line in one or both left\_lane and right\_lane set. In that case, I draw a fixed line with hard-coded coordinate.

The quality of the pipeline depends heavily on the result of the edge detection and the ROI. If cannied image contains too much noise, it's unlikely we will detect lanes correctly.

The pipeline works relatively well when the lanes are relatively symmetric from the camera view. It fails spectacularly when the lane is skewed toward the left or right, like in 'challenge.mp4'


### 3. Suggest possible improvements to your pipeline

If somehow we can make the ROI adaptive, rather than a fixed box.

Currently, we treat each frame as separate entities. Another potential improvement could be to take into account the result from previous frames, we could make the annotation smoother because sometimes we see the lane line suddenly jumps around the screen between frame.
