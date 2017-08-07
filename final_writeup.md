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

The code to calibrate the image can be found in [here](https://github.com/rakshithkeegadi/CarND-Advanced-Lane-Lines/blob/master/advanced_lanes.ipynb). At first, all the images that are read were distorted images of a chessboard and after reading the distorted images the edges were recognised. 
#### Chessboard images with edges are as below

![alt text][image2]

Once the edges were recognized the camera is calibrated using cv2.calibrateCamera which gives out the parameters to undistort the image. The image parameters are then used to undistort the image and here is a sample of undistored chessboard.

![alt text][image5]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I started off using a test image of the road which I undistorted the using the parameters from earlier cv2.calibrateCamera function and it looks like this.
![alt text][image6]


#### 2. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Once the images was selected then I had to perform a perspective transformation. To do this efficiently I chose an test image where the lanes were straight just to mark my boundaries. Once the images was chose I identified the boundaries of my lanes as shown below

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

---

Now, I used the PerspectiveTransform open cv function with the source and festination to tranform my image and now the parallel lanes looked like this.

![alt text][image7]

#### 3. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

The code for my perspective transform includes a function called `warper()`, which appears in lines 1 through 8 in the file `example.py` (output_images/examples/example.py) (or, for example, in the 3rd code cell of the IPython notebook).  The `warper()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:



I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
