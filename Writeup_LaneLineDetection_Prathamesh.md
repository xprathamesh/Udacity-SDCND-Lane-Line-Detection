# **Finding Lane Lines on the Road** 

## Prathamesh Pandit

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My initial explorations consisted of observing image dimension, and the area of interest to extract. I also went throught previous lessons and code provided in this course.

Initially I also tried exploring changing the steps of transformations, to see how it reflected on the outputs. When I performed extraction of ROI from the Greyscale images itself and then proceeding, I noticed that the edges of the RoI influenced the outputs of Canny Edge detection, which further influenced the detected lanes.

Upon learning from search explorations I decided that the below sequence of transformations provided intended accurate results.

Thus, my pipeline consisted of the following steps:
1. Converting input images to greyscale
2. Applying Gaussian Blur to smoothen the image and reduce noise
3. Generating Canny Edges
4. Generating Region of interest (ROI) by defining vertices of region
5. Perform Hough transformation on ROI
6. Draw the lines detected by Hough transform onto the input image

Upon following my pipeline, here is a sample input being transformed into having its lane lines detected
1. Sample Input
![Sample Input](./test_images/whiteCarLaneSwitch.jpg)

2. Converting input images to greyscale
![Converting input images to greyscale](./test_images/whiteCarLaneSwitch-gray.jpg)

3. Applying Gaussian Blur to smoothen the image and reduce noise
![Applying Gaussian Blur to smoothen the image and reduce noise](./test_images/whiteCarLaneSwitch-gblur.jpg)

4. Generating Canny Edges
![Generating Canny Edges](./test_images/whiteCarLaneSwitch-canny.jpg)

5. Generating Region of interest (ROI) by defining vertices of region
![Generating Region of interest (ROI) by defining vertices of region](./test_images/whiteCarLaneSwitch-roi.jpg)

6. Perform Hough transformation on ROI
![Perform Hough transformation on ROI](./test_images/whiteCarLaneSwitch-hough-standard.jpg)

7. Drawing Hough transform lines on the input
![Drawing Hough transform lines on the input](./test_images/whiteCarLaneSwitch-with-edges-standard.jpg)

Here, I came across the problem that the lines drawn on the image were not complete lines, and were not completely present on the immediate route of the vehicle.
For this purpose, it was necessary to average out the lines from Hough transform and extrapolate them before drawing them onto the image.

I was'nt sure what sort of algorithm could be used here, as this was one of my first image processing project tasks. Upon researching, I found a [useful resource here](https://medium.com/@mrhwick/simple-lane-detection-with-opencv-bfeb6ae54ec0) which I came across while going through 'Student Hub', which also explained how I could implement this. I went through this resource and understood the logic behind their implementation, which helped me get better results.

I created a seperate helper function called draw_average_extrapolated_lines() for performing this functionality. I created four lists to distinguish, x and y co-ordinates of left and right lane lines respectively. Later I calculated the slope of the lines generated from Hough Transform to detect if they were left/right lane related lines. I separated the cordinates of the lines according to their slopes (all the while ignoring very small slope lines, which could arise due to steep turns). Once the cordinates where seperated, I used the numpy polyfit function to fit values and provide best coefficients. Upon that I implemented the poly1d function to generate the linear equation of the lines. Using the fact that the range of y coordinate values for the lines would be from the image bottom upto somewhere below the halfpoint of the image, I used the OpenCV line drawing function to generate two lines whose Y coordinates followed this range and whose X cordinates where generated from the equation opbtained from the poly1d and polyfit functions earlier. This helped provide better lines to represent the lanes, as you can see below

8. Perform Hough transformation on ROI along with averaging and extrapolation
![Perform Hough transformation on ROI along with averaging and extrapolation](./test_images/whiteCarLaneSwitch-hough.jpg)

9. Drawing Hough transform lines on the input
![Drawing Hough transform lines on the input](./test_images/whiteCarLaneSwitch-with-edges.jpg)



### 2. Potential shortcomings identified with my current pipeline

Some of the shortcomings that I noticed while working on this are

* This project assumes the lanes have lines, so naturally it wont work for countries with bad roads or with roads missing lines etc.
* Plenty of white cars (crossing or changing lanes) within the Region of Interest can cause issues with line detection
* Might cause issues when there is more traffic
* At turns and intersections, the implementation wont work properly as it does not take those into account

### 3. Possible improvements in my pipeline

* Creation of different pipelines for different scenarios like traffic, intersections etc.
* Better tune the parameters according to the scenario