# **Finding Lane Lines on the Road** 

[//]: # (Image References)

[image1]: ./images/gray.png "Grayscale"
[image2]: ./images/smoothed.png "Smoothed image"
[image3]: ./images/edges.png "Canny output"
[image4]: ./images/lines.png "Draw lines output"
[image5]: ./images/final.png "Final output"

### 1.Pipeline

My pipeline cosists of 5 major steps:
* Convert image to grayscale
![alt text][image1]
* Use a Gaussian Filter to smooth the image before feeding into edge detector. This helps in prevention of identifying spurious edges.
![alt text][image2]
* Set a lower and upper threshold to detect edges using the canny edge detector and find the edges in the smoothed image.
![alt text][image3]
* Using the edges detected find the lines that form the edges using the hough line function. This gives us a lot of small lines that need to be joined together.
In order to draw a single line on the left and right lanes, I modified the draw_lines() function as follows:
Maintain a dictionary called lineDict and samplecount. lineDict is a dictionary where the slope of the line is the key and the value corresponding to the key is a list of points(tuples) that are output by the HoughLines function. Lines that are within a ceratin limit of slope are categorized into the same key value.Each time we add a new line that has a slope within tolerance of an existing slope we take the mean. To take the mean we multiply the slope by the number of samples for which the mean was taken previously and then we divide it by number of samples+1 as a new line is going to be added to the slope. This ensures that the slope does not go crazy and is an overal representation of all the line segments (kind of like a best fit). samplecount keeps track of how many times lines with slopes within the tolerance has been encountered for each distinct slope.

From lineDict we find the extreme points (left most and right most) in the list of points corresponding to every slope and get the two largest lines based on the euclidean distance between the extreme points. Then we draw a line between these extreme points (for each of the two largest lines) and extend till it meets the lower and upper end of the image.

(Please look at code for clarity and more details)
![alt text][image4]

* Now we remove the portion of the lines above a certain level as they extend on both sides and are bound to intersect and add it to the original image.
![alt text][image5]


### 2. Potential shortcomings of the pipeline

One potential shortcoming is that the threshold are chosen for this particular lighting and this would fail if the lighting is a bit different.

Another shortcoming is that the parameters for hough lines is not perfect and needs to be tuned for better performance.


### 3. Possible improvements

A possible improvement would be to implement draw lines such that it only extends the lane lines to meet the bottom of the image instead of extending in both directions. This can be done easily but is not currently implemented

Another potential improvement could be that the thresholding can be done dynamically and based on the lighting in the image we will choose our threshold. We can use the otsu thresholding to get an automatic threshold from the histogram corresponding to the image.
