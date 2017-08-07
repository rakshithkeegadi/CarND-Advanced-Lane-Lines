# **[Advanced Lane Finding Project](https://github.com/rakshithkeegadi/CarND-Advanced-Lane-Lines/blob/master/advanced_lanes.ipynb)** 
by [Rakshith Krishnamurthy](https://www.linkedin.com/in/rakshith-krishnamurthy-360682b/)

---


### The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/Boundary_images.png "Boundary Image"
[image2]: ./output_images/Chessboard_edges.png "Chessboard Images"
[image3]: ./output_images/Histogram.png "Histogram"
[image4]: ./output_images/Lane_mapping.png "Lane Mapping"
[image5]: ./output_images/Original_Distortion.png "Original Distortion"
[image6]: ./output_images/Original_distorted_car.png "Original Distortion Car"
[image7]: ./output_images/Perspective_Transformation.png "Perspective Transformation"
[image8]: ./output_images/Sliding_window.png "Sliding Window"
[image9]: ./output_images/Transformation_image_list.png "Transformation Image"
[image10]: ./output_images/polyfit.png "Polyfit"
[video1]: ./project_video_output.mp4 "Video_Output"

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code to calibrate the image can be found in [here](https://github.com/rakshithkeegadi/CarND-Advanced-Lane-Lines/blob/master/advanced_lanes.ipynb). At first, all the images that are read were distorted images of a chessboard and then the distorted images edges were recognised using cv2.drawChessboardCorners. 
#### Chessboard images with edges are as below

![alt text][image2]

Once the edges were recognized the camera was calibrated using cv2.calibrateCamera which gives out the parameters to undistort the image. The image parameters are then used to undistort the image and here is a sample of undistored chessboard.

![alt text][image5]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I started off using a test image of the road which I undistorted using the parameters from earlier cv2.calibrateCamera function and it looks like this.
![alt text][image6]


#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Once the images was selected then I had to perform a perspective transformation. To do this efficiently I chose a test image where the lanes were straight just to mark my boundaries. Once the images was chose I identified the boundaries of my lanes as shown below

![alt text][image1]


```   
    src=np.float32(
        [[x1,y1],
         [x2,y2],
         [x3,y3],
         [x4,y4]])
    
    dst=np.float32(
        [[0.15*x,0.9*y],
         [0.05*x,0.15*y],
         [0.95*x,0.15*y],
         [0.9*x,0.9*y]])

This resulted in the following source and destination points:
```
| New X Y | Computation | 
|:-------:|:-----------:| 
| x1      | 0.15*x      | 
| y1      | 0.9*y       |
| x2      | 0.40*x      |
| y2      | 0.65*y      |
| x3      | 0.60*x      | 
| y3      | 0.65*y      |
| x4      | 0.9*x       |
| y4      | 0.9*y       |


Now, I used the PerspectiveTransform open cv function with the source and destination values to tranform my image and now the parallel lanes look like this.

![alt text][image7]

#### 3. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Now I color transfomed the image using a sobel magnitude and sobel absolute conversion. I also made use of the r channel and the s channel which helped me in identifying the yellow and white lanes. This also helped me reduce a lot of distortions. Also combining all the transformed images and I get a final image with the lanes detected.
Here are few samples of different lane images detected.

![alt text][image9]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The lane line pixels were identifed using a histogram. The mid point of x axis of the image was chosen as a reference point and then the peaks of true immediate pixels to the left and right of the mid reference points were identified as the left and right lanes. 
![alt text][image3]

Using sliding window we start off from the bottom of the image using histogram values and then move up to construct lanes. The lanes are constructed for both the left and right lanes.

![alt text][image8]

Once the lanes were identfied usign sliding window a polynomial Ay^2+By+C is used to provide a polyfit curve instead of rectangels in sliding window. 

![alt text][image10]

This is very handy because if there is already a part of the lane detected we can construct the lanes using the polynomial fit for the next frame.


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The radius of curvature was very intersting to calulate because I had to convert the pixels to meters ratio.
ym_per_pix = 30/720 for y &
xm_per_pix = 3.7/700 for x.

Rcurve=((1+(2Ay+B)^2)^3/2)/∣2A∣
 the x & y pixel values are used to compute the left and right radius of curvatures in meters.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Once the radius is calculated now I draw lanes using cv2.warpPerspective() function which uses the Minv parameter from prespective transform function to transform the image back to orignal. This using x and y values end up transforming the image with lanes identified like this.

![alt text][image4]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

The pipline was constructed as mentioned below
* Undistort the image.
* Perspective transform
* Color Thresholding
* Sliding window(if previous fit is None or first frame)
* Polyfit (if frame is present)
* Calculate the radius ((left + right)/ 2)
* Retransform the image.

The pipeline output of my image is as shown [below](/project_video_output.mp4).

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline can be implemented better and I for see the following improvements can be made:
* It needs lanes on both sides the driving region.
* My pipeline does not do well on roads with shadows.
* It can be improved to perform better on steep curves.
* Pipeline should be enhanced to identify the roads without lanes marked.
* This lane marking was done on broad day light so it does not do well for night driving.
