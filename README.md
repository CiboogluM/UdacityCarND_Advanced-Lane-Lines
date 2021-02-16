## Advanced Lane Finding
[![Udacity - Self-Driving Car NanoDegree](https://s3.amazonaws.com/udacity-sdc/github/shield-carnd.svg)](http://www.udacity.com/drive)
![Lanes Image](./examples/example_output.jpg)

In this project, my goal is to write a software pipeline to identify the lane boundaries in a video


The Project
---

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

Here's a [link to my video result](./test_video_output//project_video_output.mp4)

The images for camera calibration are stored in the folder called `camera_cal`.

The images in `test_images` are for testing pipeline on single frames.

Examples of the output from each stage of pipeline can found in the folder called `output_images`

Camera Calibration code file is `CameraCalibration.ipynb`

Main code file is `AdvancedFindingLaneLines.ipynb`

Raw video file is `project_video.mp4` and result of the project is in `test_video_output` folder
