
Hello, This project is completed for udacity self driving car nanodegree programme second project.

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
[image1]: ./camera_cal/calibration2.jpg "original"
[image2]: ./camera_cal/test_undist2.jpg "Undistorted"
[image3]: ./test_images/test4.jpg "raw image"
[image4]: ./output_images/disorted_img.png "disorted_img"
[image5]: ./output_images/gradx.png "gradx"
[image6]: ./output_images/grady.png "grady"
[image7]: ./output_images/combinedxy.png "combinedxy"
[image8]: ./output_images/combinedxy.png "combinedxy"
[image9]: ./output_images/dir_tr.png "dir"
[image10]: ./output_images/mag_tr.png "mag"
[image11]: ./output_images/combinedmagdir.png "combineddirmag"
[image12]: ./output_images/color_tr.png "mag"
[image13]: ./output_images/combined.png "combined all"
[image14]: ./output_images/warped.png "warped"
[image15]: ./output_images/slidewindow.png "slidewindow"
[image16]: ./output_images/slidewindow.png "slidewindow"

[image17]: ./output_images/test2.jpg "test2_output"

[image51]: ./examples/warped_straight_lines.jpg "Warp Example"
[image61]: ./examples/color_fit_lines.jpg "Fit Visual"
[image71]: ./examples/example_output.jpg "Output"
[video81]: ./project_video.mp4 "Video"

### Camera Calibration

Cameras have disortion problem due to their lenses.This problem cause that objects in the image are in wrong position. In order to fix this cv2.undistort(img, mtx, dist, None, mtx) is used by 16 different 9x6 chess board images with locating . After applying undisortion, image and undisorted images are shwoned in below.

<center>Before Calibraiton</center>

![alt text][image1]
<center>Undistorted</center>

![alt text][image2]


### Pipeline

After camera calibration I moved to line detection algortihm development. I used test images during this step. Below stages I used raw image as test4.jpg which inside (./test_images)

![alt text][image3]

#### 1. Example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image4]

#### 2. Thresholded binary image

I used a combination of color and gradient thresholds to generate a binary image.  Here's an example of my output for this step.
##### 2.1 Gradient Threshold
I started with gradient thresholds. In order to get gradients, sobel function is used (sobel works on derivatives to selected direction (x or y)). Below figures show gradient threshold outputs for x,y and combination of x and y.
<center>Gradx Thresholded</center>

![alt text][image5]
<center>Grady Thresholded</center>

![alt text][image6]
<center>Combined Gradx and Grady</center>

![alt text][image7]

##### 2.2 Magnitude and Directional Threshold

As a second threshold , cross gradient is used by applying gradx and grady before step with arctan. And for the magnitude of gradx and grady are used as additional threshold.
<center>Directional Thresholded</center>

![alt text][image9]
<center>Magnitude Thresholded</center>

![alt text][image10]
<center>Combined Directional and Magnitude</center>

![alt text][image11]

##### 2.3 Color Thresholded

In the first project RGB threshold was used. However, RGB threshold helps a lot for finding road lanes, under brightness conditions it is not efficient. This problem occurs mostly on bright areas on the image. HLS color space is more useful when the problem about brightness. For this project, HLS Threshold is used.
![alt text][image12]

##### 2.4 Combination of all thresholds
After all thresholds done, I combined them together by below rule;
```python
[((gradx == 1) & (grady == 1)) |  (color_tr==1)| ((mag_tr == 1) & (dir_tr == 1))]
```
![alt text][image13]


#### 3. Perspective transform

The code for my perspective transform includes a function called `warper()`, which appears in under Perpective Transform title.  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
        [[ 150, 720],
         [590,450],
         [720, 450],
        [1190, 720]])
dst = np.float32(
                [[200,720],
                 [200,0],
                 [1080,0],
                 [1080,720]])

```

This resulted in the following source and destination points:

| Source        | Destination   |
|:-------------:|:-------------:|
| 150, 720      | 200, 720        |
| 590, 450      | 200, 0      |
| 720, 450      | 1080, 0      |
| 1190, 720     | 1080, 720        |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image14]

#### 4. Identification of lane-line pixels and fit polynomial

Now I have perpective transformed and lane thresholded img. In this step window search method is applied to find lane lines and their polynomial parameters.
Slide window method is using point searches in rectangular areas. These windows are using center as each point coordinates avarages for their areas. After setting windows, polyfit function is applied to center of the windows.

Here sliding window method output:


![alt text][image15]

#### 5. Calculation of lane radius and vehicle position

I did this in lines # through # in my code in 79th line.

Let's say that our camera image has 720 relevant pixels in the y-dimension, and we' have  880 relevant pixels in the x-dimension. Therefore, to convert from pixels to real-world meter measurements, we can use:
```python
ym_per_pix = 30/720 # meters per pixel in y dimension (from the warp destination points yend-yinit)(720-0)
xm_per_pix = 3.7/880 # meters per pixel in x dimension (from the warp destination points xend-xinit)(1080-200)
```
Second order polynomial curvature formula is:
![\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\Large&space;R=\frac{(1+\{(2Ay_{perpix}\emph{y}+B)^2)^{(3/2)}}}{|2A|})

I calculated avarage line by using avarage of left and right line values. After that I applied this radius formula to center line in order to get radius of curvature value.

For vehicle position, firstly I found vehicle mid point by using bottom value of y and mid line formula (warped area cut was at y=690). Then I got the real value by using:
![\Large x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}](https://latex.codecogs.com/svg.latex?\Large&space;Midpoint=((img.shape[1])/2-Midpoint)x_{perpix})

Then I determined easily vehicle position as
```python
if VehPos > 0:
        VehLoc = "right"
    else:
        VehLoc = "left"
```


#### 6. Lane area identified results

Lane area draw, radius of curvature and vehicle position plotted on img by 90th line of the code.
![alt text][image17]

All test frames are processed and results are saved in /output_images folder

---

### Pipeline (video)

After I had all necessary functions for processing image in order to get lane area, vehicle position and curvature calculations. I rewrite all codes as functions and configure input-output of the functions. This part is under the FULL PIPELINE title in code AdvancedFindingLaneLines.ipynb


#### FINAL OUTPUT

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

During set up threshold values, every test images are passed then I write video pipeline with all functions. However I saw some frames are still not detectable. Therefore I inpected fail frames by saving frame figures with below code:
```python
clip1.save_frame("frame3base.jpg", t = 41.6) ## this frame was problematic for my thresholds. Thus I saved it with this function and recalibrated my combined thresholds by adding this frame at test images folder.
```
Then I recalibrate my thresholds. After that I faced with wrong curvature calculation frames. It was only 1 or 2 frames during video. Thus I added sanity checks. My sanity checks are using mid vehicle position. If mid vehicle position is changed between two frames above than 0.3 meters, use before line fit values. I used it directly like this, because between two frames it is not possible to change vehicle position too much like 0.3 meters.

Only this sanity check is solved my pipeline video output for project video. However my code is failing on harder and insane challenge videos. In future there should be more sanity checks depends on lane lines distances, curvature values, regarding brightness changable threshold values etc.
