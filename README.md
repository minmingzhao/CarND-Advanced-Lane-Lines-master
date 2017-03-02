## Advanced Lane Finding
My Advanced Lane Finding project from Udacity Self Driving Car Nanodegree. This is Project 4. 

[//]: # (Image References)
[IdentifyChessboardCorners.png]: ./output_images/IdentifyChessboardCorners.png
[Undistort_images.png]: ./output_images/Undistort_images.png
[ApplyBinaryThresholds.png]: ./output_images/ApplyBinaryThresholds.png
[bridview_images.png]: ./output_images/bridview_images.png
[FitPolynomial_ApplyToOriginalImages.png]: ./output_images/FitPolynomial_ApplyToOriginalImages.png

### Overview
---
In this project, the goal is to write a software pipeline to identify the lane boundaries in a video from a front-facing camera on a car. In order to accomplish that, we need to have the capability to identify from images. Below I used opencv packages to accomplish all the steps. 
Basic steps of identifying lanes from images includes: 
Step 1 Distortion Correction: Compute the camera calibration matrix and distortion coefficients given a set of chessboard images. And apply a distortion correction to raw images. 
- key items: cv2.findChessboardCorners, cv2.drawChessboardCorners, cv2.calibrateCamera
- example image

![alt text][IdentifyChessboardCorners.png]
 
![alt text][Undistort_images.png]
 
Step 2 Perspective Transform: Apply a perspective transform to rectify binary image ("birds-eye view").
- key items: cv2.getPerspectiveTransform(src, dst), cv2.warpPerspective
- example image

![alt text][bridview_images.png]
 
Step 3 Apply Binary Thresholds: Use color transforms, gradients, etc., to create a thresholded binary image.
- key: cv2.COLOR_BGR2HLS, cv2.COLOR_BGR2LUV, cv2.COLOR_BGR2Lab
- example image

![alt text][ApplyBinaryThresholds.png]
 
Step 4 Fitting a polynomial to the lane lines: Detect lane pixels and fit to find the lane boundary. 
Step 5 Lane curvature & vehicle position: Determine the curvature of the lane and vehicle position with respect to center.
- key: np.polyfit, cv2.polylines
- example image: 

![alt text][FitPolynomial_ApplyToOriginalImages.png]
 
Step 6: Warp the detected lane boundaries back onto the original image.
Step 7: Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

After we are able to handle images, then we build video pipelines. The video processing pipeline essentially just duplicates what we have covered previous above with consideration of taking easier steps when previous film frames already have successfully identified lane boundaries. That is `found_search()` and `blind_search()` function. After that, we could output processed videos from 3 task videos. 

### The repository contains: 

* Advanced_Lane_Finding_MZ.ipynb (main script)
* camera_cal folder (chessboard images to calibration camera and undistort images)
* test_images folder (images to test undistort, bird view, fit polynomial etc.)
* project_video.mp4, challenge_video.mp4, harder_challenge_video.mp4 (task video to process)
* result_MZ.mp4, result_challenge_MZ.mp4, result_harder_challenge_MZ.mp4 (output videos)
* output_images (example output images to illustrate the pipeline processing)

### Dependencies
This project need the conda environment:
* [CarND Term1 Starter Kit](https://github.com/udacity/CarND-Term1-Starter-Kit)


### Results discussion
---
project_video.mp4: The script successfully identified all the lane boundary and calculate the curvature at a reasonable magnitude. 

challenge_video.mp4: The script was able to identify most of the conditions except the big shadow when the vehicle cross the under of the bridge. 

harder_challenge_video.mp4: The script was not able to identify at an acceptable accuracy. And this still needs improvement. 

#### Discussion on further possible Improvement: 
As we could see from the result_MZ.mp4 that the curvature radius estimation is not still perfect as sometimes the curvature jump to a high value while most of the time is at around 1 km (The actual curvature is around 1km). I belive this could be done better if 
- I assign a longer forward estimation in src and dst region. This could be done easily but be careful on the accuracy of those further forward section. 
- The current found_search and blind_search algorithm works like as long as there is some pixel of binary value 1 we will remain using found_search. I belive this make senses as it works like a filter to smooth lane detection. But one may find a better smart way to fulfill this function. 

Another major thing is from some isolated frame of challenge video and most of the even harder challenge curvy moutrain video, the lane detection is not perfect. As a matter of fact, some of them are terrible. I believe this is due to static (i.e. hard coded) binary threshold setting does not work for all. In the future, a better detection should dynamically evaluate the result of each images after binary threshold and compare to previous images to avoid some huge jumping errors. 
