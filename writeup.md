# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidWhiteCurve.jpg 

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale.
Then I applied a Canny transform to find just the edges with strong gradients in the image.
After that, I applied a Hough transform to determine which edges came together to form lines.
I used a region of interest mask to limit the Hough transform to a specific region in front of the car shaped like a trapezoid. The I combined the generated lines with the original image.

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by first separating all the obtained lines into two buckets, right and left, based on their slope. Lines with positive slope were classified left, while lines with negative slope were right.
Then, I calculated the intercept for each line, and then the average slope and intercept for each bucket. The average was weighted based on the length of each line. I used the bottom of the image and the point 40% from the top as the two y coordinates, and then calculated the x coordinates from that before finally rendering the line.

![A shot with lane lines highlighted][image1]


### 2. Identify potential shortcomings with your current pipeline


One shortcoming is that it does not handle curves very well. Because I calculate the slope and intercept of the lane line as the average of all detected lines, when the curved line is added it causes my lane highlights to become askew. Additionally, since I bucket the lines into left or right based on slope, since the slope of a curve would be opposite to whatever side it was on it could very easily be misclassified.

Another shortcoming is that sometimes, when the lane consists of dashed lines rather than a solid line, the algorithm fails to detect the lines. This seemed to happen most often when one of the dashed lines had just left the field of view and the next one wasn't detected for whatever reason. 


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to increase the amount of Gaussian smoothing. The canny function in OpenCV already applies some, but it's possible that more would help.

Another potential improvement could be to take into account the previously drawn lane lines when calculating a new one. Since lanes don't typically vary too much between frames, the lines drawn in a previous frame could be utilized when drawing a new line, either to check for an unusual amount of deviation - in which case you could suspect an error in your current calculation - or as an input when calculating the new lines.

Another improvement could be gathering more data - since we know where the lane lines will be relative to the car in most cases, a camera mount on each side closer to the ground could monitor specifically for lane lines. There would be less interference with a smaller field of view.
