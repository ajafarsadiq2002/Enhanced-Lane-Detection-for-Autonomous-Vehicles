# **Finding Lane Lines on the Road** 

## Documentation
---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report
---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

Current Pipeline:
    
1) Mask out everything excluding Lane Lines
2) Crop out the Region of Interest
3) Apply Canny Edge Detection
4) Compute the Hough Lines
5) Filter out and differentiate lines
6) Compute 2 main lines
7) Average main lines over previous frames
8) Overlay Lines and Lane over original input

### 2. Procedure:

1) Getting the Images
    First, we have to read the images before we can perform any operations on them. We'll be storing our input image files into the imageList list, then using a special function called display_images()

2) Masking Unnecessary Colors
    We need to mask out the unnecessary colors and noise to prevent the detection of extraneous lines. You can do this by thresholding the color values in an image. Our current image uses the RGB Colorspace, but thresholding in RGB isn't going to work very well due to noise, shadows, and sunlight. Instead, we can use the HSL(Hue, Saturation, Lightness) colorspace to be able to filter out the white and yellow lines with more room for variances due to noise, shadows, and sunlight. We'll be using two masks, one for white colors, and one for yellow colors. We will then combine them and use the combination to mask the input image.

3) Region of Interest
    You might have noticed in the previous operation that we had some parts of the image that weren't lane lines. We need to remove these and isolate the lane lines, and we'll be doing that by masking out everything outside our region of interest, which is represented as a polygon.

4) Edge Detection
    We now have an image in which the lane lines have been isolated. Now we need to compute the edges of the images. We'll be doing this by converting our input image to the "gray" colorspace, then compute the gradient(rate of change) of pixels adjacent to each other. This method of computing edges using gradients is called Canny Edge Detection.

5) Finding Lines
     We need to determine the lane lines from the canny edges we previously detected. How can you detect lines? We'll be computing the lines using cv2.HoughLinesP(), and then filtering out the lines. We'll start by removing lines that are outside a determined slope range, and zone. Additionally, we will filter the lines into their own corresponding lanes by seeing whether or not the filtered lines have positive or negative slope. Now we have the lines that correspond to their lane; how do we combine them into a single line? One way to approach it is by using the slope and intercept of the lines. We'll be calculating the slope and intercept of each of the lines in a particular lane, then averaging the slope and intercepts to produce one line. We'll do this to both lanes. We've detected the lanes somewhat accurately now, but if we were to apply this to a video stream, the lines would oscillate way too much, and is likely to remain unstable. We can make the line more stable by using interframe averaging. Basically, we will be averaging the slope and intercepts of the lane lines from previous frames. This way, the line moves much more slowly and accurately.

6) Overlaying the Image and the Lines
    We're close to the end of this project, all we need to do now is just overlay the detected lines and lane onto the input image. We'll be doing this via a weighted sum of the two images.

### 3. Identify potential shortcomings with your current pipeline

The current pipeline works great for detecting straight, or slightly curved lanes. It handled the challenge video extremely well, mainly due to the smoothing caused by the interframe averaging.The program doesn't account for curvature, and the lane filtering method does not work well with sharp turns.


### 4. Suggest possible improvements to your pipeline

 We can try doing a sliding window algorithm to produce a curve for curved lanes, which would detect lanes much more accurately. Another improvement that could improve accuracy would be detection of vehicles and adjacent lanes, which would allow for the car to localize itself better.
