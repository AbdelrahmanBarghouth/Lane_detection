# **Finding Lane Lines on the Road** 

## Writeup Template

### This is a project to detect lane lines in a road using simple Computer vision algorithms (Canny, Hough)

---

[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg
[image2]: ./test_images_output/solidWhiteCurve.jpg

[image3]: ./test_images/solidWhiteRight.jpg
[image4]: ./test_images_output/solidWhiteRight.jpg

[image5]: ./test_images/solidYellowCurve.jpg
[image6]: ./test_images_output/solidYellowCurve.jpg

[image7]: ./test_images/solidYellowCurve2.jpg
[image8]: ./test_images_output/solidYellowCurve2.jpg

[image9]: ./test_images/solidYellowLeft.jpg
[image10]: ./test_images_output/solidYellowLeft.jpg

[image11]: ./test_images/whiteCarLaneSwitch.jpg
[image12]: ./test_images_output/whiteCarLaneSwitch.jpg
---

### Pipeline

In this section will go throught the pipeline to reproduce the outcome videos and images in output folders. For each image we go through 8 steps as following:

1- Convert image from 3 color channel (RGB image) to one channel (grayscale image). This will make processing much faster and it is easier to work on just one channel of the image if it is sufficient enough for our purpose as in our case.

2- Apply a gaussian blur noise to the image. This will help us as the next step is doing filtering for the image and double filtering will lead to different outcome.

3- At this stage the image is ready for canny edge detection algorithm to be applied on the image. The choice of the parameters of the canny is so important for the algorithm to give proper outcome at the end. For the choice of the parameters I have designed a simple a simple gui window with slide tracks to choose the thresholds of the canny. The algorithm starts with taking a filename as a command line argument to a python file which reads it as a video and while video has more frames it will display the next frame. For each frame I can have a look at the different numbers of thresholds that best fits to each frame and then I chose the best numbers that can give best results on the whole video. This algorithm was improved again by next step. Canny edge detection algorithm works basically with 2 thresholds and calculates the hysteresis procedure. Then it outputs a one channel image with the strongest links as edge detected in white and rest of image as black. 

4- In this step I chose a specific region of Interest (ROI) in the image. Adding this to my gui tool enabled me to get better numbers for the canny thresholds. I specify a region of vertices and add a mask on the image. This operation acts like cropping a part of the image and extracting edges from this ROI. 

5- After selecting the ROI in image, I use hough algorithm to detect lines in it. The hough parameters are selected by also another GUI with slide trackers as done for the canny parameters. These lines should be extracting the lane lines drawn on the street and detected as edges.

6- Using these lines, I count slopes and classify them. The slopes are classified either between -0.5 to 0.5 and infinity, or slopes for the left part of the image with slope smaller than -0.5 and placed in the left half of the ROI in the image, and third and last partition is for slopes bigger than 0.5 and placed in the right hand side of the ROI in the image. These partitions are then used to extrapolate the existing lines detected by hough algorithm. For left lines, I extract the biggest point and smallest point with respect to their y position and set them as a start and end point to the joint left lane indicator to give at the end one line for the left lane marker. The same is done for the right lane lines so we have 2 lines indicating the left line and right line. At the end I compare between the start of each of these 2 lines and if one found to be starting more higher in y position than the other line, I use the same height of the longer line and the slope of the shorter line to detect the values of the proposed start point of this short line to be having same starting point as the long line. If no lines were detected either on the right hand side or on the left hand side due to big difference in brightness and lack of robustness in the algorithm I deployed, the new lines are dropped and old lines will be drawn again. Taken in consideration that lane lines almost have the same position in the road and the camera is fixed then the lines should be either correect or close to the correct places of the lane lines. Taking old lines is also taken if the length of the lines is very short or very long as this will assume that the detected points for start and end of the lane lines are faulty and should not be used. If all those cases were not found then the new line is drawn on the image and will be saved as the last correct drawn lines for reference in next frames.

7- At this point we have 2 lines indicating left and right lanes. They are represented as array of arrays, where each array is indicating a list of points to draw each line starting from (x1, y1) for the starting point of the line till (x2, y2) which represents the end point of the line drawn. Hence those 4 parameters indicate the drawing of the array where I use the open cv line drawing tool with thickness 5 mm and colored as red to draw red overlay on the detected lane lines.

8- After drawing this on the image which is till now is just ROI of the image, I add this ROI image with lines drawn over it and the original image to generate an image with RGB channels representing the real image with only red overlays in the predicated places of left and right lane lines.

In this table is the output of the test images used in this project. The output images are produced by following the pipeline illustrated above.

| Source Image | Output Image |
| ------------ | ------------ |
|![alt text][image1]  | ![alt text][image2]  |
|![alt text][image3]  | ![alt text][image4]  |
|![alt text][image5]  | ![alt text][image6]  |
|![alt text][image7]  | ![alt text][image8]  |
|![alt text][image9]  | ![alt text][image10] |
|![alt text][image11] | ![alt text][image12] |


### Shortcomings


### Improvements