## Self-Driving Car ENgineer Nanodegree

### Overivew

---
In the advanced lane finding project, I will write a pipline method by python to identify the lane boundaries in a video from a front-facing camera on a car. The camera calibration images, test road images, and project videos are available in the [project repository](https://github.com/udacity/CarND-Advanced-Lane-Lines).
And my soluation python code can be found [here]()

![image0](./examples/example_output.jpg "Output")

**Advanced Lane Finding Project**

### The goals / steps of this project are the following:

1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
2. Apply a distortion correction to raw images.
3. Use color transforms, gradients, etc., to create a thresholded binary image.
4. Apply a perspective transform to rectify binary image ("birds-eye view").
5. Detect lane pixels and fit to find the lane boundary.
6. Determine the curvature of the lane and vehicle position with respect to center.
7. Warp the detected lane boundaries back onto the original image.
8. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.




### Camera Calibration

#### 1.find object points and image points from calibrate image and calculatethe result matix .

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![image1](./output_images/calibrationimgcbc.jpg,"calibration")

### Pipeline (single images)

#### 1. Compute the test image by camera calibration with chessboard images

first read the test image and use the  `objpoins` and `imgpoints` to find ret , mtx, dist, rvecs and tvecs. After that use the `undistort()` to undistort the original image. The result shows like this:
![image2](./output_images/undist.jpg,"undist")

#### 2. Use color and gradient thresholds to generate a binary image.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`). 
I combine sobel, magnitude, direction and color channel method to find the binary image:
Here's an example of my output for this step.  

![image3](./output_images/mag_output.jpg, "mag_output")
![image4](./output_images/dir_output.jpg, "dir_output")
![image5](./output_images/combined_binary.jpg, "combined_binary")

#### 3. Performed a perspective transform .

The code for my perspective transform includes a function called `warper()`  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 585, 460      | 320, 0        | 
| 203, 720      | 320, 720      |
| 1127, 720     | 960, 720      |
| 695, 460      | 960, 0        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![image4](./output_images/warped.jpg, "warped")

#### 4. Find lane pixels
I used a hisgram to find out the peaks of the half image then I divided the image into 15 windows to locate the left x points and right x points of the lane. Moreover  I set the margin with 50 and minpix with 50 to draw the polynomial
Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:
![image5](./output_images/out_img.jpg, "out_img")
And to qucikly find the next frame of video, so that use the last image's second order polynomial to fit the new image's lane lines. The result like this:
![image5](./output_images/result.jpg, "result")



#### 5. Calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

First I did this wirh the real world transform. Convers the x and y from pixels space to meters, then use the formula of the radius to calculate the radius of curvature. Finally  through the middle of the imamge shape by x and the middle of two curvatures to find out the car position with respect to the lane. 
Those in my code in `measure_curvature_real()`function.

#### 6. Draw lane and text the information.

I used the `draw_lane()` function to draw the lane on the original image through `cv.fillPoly()` and `cv2,polylines()`. Second by the Minv to prespect back to the real world image. At last add the text on the image with the Left lane line curvature, Right lane line curvature, car offset from center information. Here is an example of my result on a test image:

![image6](./output_images/img_addtext.jpg, "img_addtext")

---

### Pipeline (video)

#### 1. define a PiplineProcess class and to process the video.
I used the functions before to make them works on video. I wrote a if else statement to decide whether use the `search_arounf_poly()` function or `fit_polynomial()` function to detect the lane.
Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion
It is  a  challenging project and I am quite excited with the results.

Moreover,  I need to improve the ProcessImage class for better detection:

check the video by each frame to make sure:
Perform some sanity checks to confirm that the detected lane lines are real:
1. Checking out wehther that they have similar curvature,
2. Checking that they are separated by approximately the right distance horizontally,
3. Checking out that they are roughly parallel.
4. Checking out whether tree shadows will recognize as lane lines.

Smoothing:

Although most of the results are good ,but due to the interference of shadows, there are occasional misidentifications. 


Maybe one problem , the pipeline might failed if a white vehicle is too close to the left or right lanes, and also when a white vehicle is in front of our car and quite close to us.

I glad to write the information to record my study.
  
