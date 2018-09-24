# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

![/test_images_output/] # (Image References)

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

As part of this project, a class *LaneFinder* was implemented. In this class, the method *process_image()* implements the pipeline that receives an image (or video frame) an return it with the drawn lane lines.
Such pipeline consists of 6 steps. First, the images are converted to grayscale, then they are passed trough a gaussian filter followed by a canny edge detector, whose output is in turn masked to take into account only rectangular region where the lane lines are expected to be found. The masked image with the marked edges is used as input for the the helper function *hough_lines()*(which was modified to return a list with the detected lines in the region of interest).

Finally, the detected lines are divided between left and right lines (based on the slope) and extrapolated, resulting in two solid lines that then are drawn with the helper function draw_lines.

In order to make a more robust extrapolation, and only in the case of a video input, the resulting solid lines of every extrapolation are saved together with the solid lines obtained in the previous frames (the number of previous frames is limited by *max_frame_counter*), so that is taken into account also the average to generate the next solid lane line. Additionally, the lines detected with the *hough_lines()* function are filtered based on their slope regards the average slope of the segments detected in the previous frames (also limited by *max_frame_counter*), i.e. the diference between the slope of every segment and the average slope must be below a tolerance level (*tolerance_slope*). Every *max_frame_counter=60* frames, all the mentioned averages are reseted.
.

The following images show the outcome of every step in the pipeline: 

![Input image][test_images/whiteCarLaneSwitch.jpg]
![Grey image][test_images_output/whiteCarLaneSwitch.jpg_img_grey.png]
![Blured image][test_images_output/whiteCarLaneSwitch.jpg_blur_grey.png]
![Edges detected][test_images_output/whiteCarLaneSwitch.jpg_edges.png]
![Segments detected][test_images_output/whiteCarLaneSwitch.jpg_line_segments.png]
![Solid lane lines][test_images_output/whiteCarLaneSwitch.jpg_extrapolated.png]

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the slope of the lane lines changes more rapidly as expected. In the implemented solution, the average slope and previous lane lines are saved in order to filter outlier segments and for this, the previous 60 frames are taken into account (which counts for approximately the last 2.4 seconds of video for a frame rate of 25 fps). This is precisely the reason because the solution implemented fails against the challenge.mp4 video.

Another shortcoming could be when there is a car in front of the camera and this one is inside the region of interest. This could make more difficult the detection of the lane lines.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to be able to describe the lane lines as curved lines, i.e. possibly in terms of a quadratic function for every side of the lane.

Another potential improvement could be to find a way to dynamically obtain the region of interest, instead of having a fixed rectangular region described with hardcoded vertices.
