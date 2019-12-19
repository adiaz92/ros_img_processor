## ros_img_processor
Just a simple template node receivng an image and doing something. Links to OpenCV and ROS wrapped.

## How to run the code
In a terminal window, type:
```sh
$ roslaunch ros_img_processor ros_img_processor.launch
```

## Tip
Check your webcam encodings (yuyv,mjpeg,...) and set them accordingly at file launch/usb_camera.launch


## Exercise 2.2: ros_img_processor

Write a program that provides the direction vector of a ball seen by the camera (in the camera reference frame). Start by forking https://github.com/beta-robots/ros_img_processor. Deliver it by indicating a github repository link. Do not forget to document at Readme file.


The first step is to write the program to find the ball by applying the Hough Transform to find the circles and we draw a circle on the image.

````
		// find the ball

    cv::Mat gray_image;
    std::vector<cv::Vec3f> circles;
    cv::Point center;
    int radius;


    //clear previous circles
    circles.clear();

    // If input image is RGB, convert it to gray
    cv::cvtColor(cv_img_out_.image, gray_image, CV_BGR2GRAY);

    //Reduce the noise so we avoid false circle detection
    cv::GaussianBlur( gray_image, gray_image, cv::Size(GAUSSIAN_BLUR_SIZE, GAUSSIAN_BLUR_SIZE), GAUSSIAN_BLUR_SIGMA );

    //Apply the Hough Transform to find the circles
    cv::HoughCircles( gray_image, circles, CV_HOUGH_GRADIENT, HOUGH_ACCUM_RESOLUTION, MIN_CIRCLE_DIST, CANNY_EDGE_TH,   HOUGH_ACCUM_TH, MIN_RADIUS, MAX_RADIUS );

    //draw circles on the image
    for(unsigned int ii = 0; ii < circles.size(); ii++ )
    {
        if ( circles[ii][0] != -1 )
        {
                center = cv::Point(cvRound(circles[ii][0]), cvRound(circles[ii][1]));
                radius = cvRound(circles[ii][2]);
                cv::circle(cv_img_out_.image, center, 5, cv::Scalar(0,255,0), -1, 8, 0 );// circle center in green
                cv::circle(cv_img_out_.image, center, radius, cv::Scalar(255,0,0), 3, 8, 0 );// circle perimeter in red
        }
 ````
        
        
        
Then to find the direction of the vector we draw a bounding box around the ball so that the system finally shows the direction of the vector according to that bounding box.

Finally it will show it on the screen.

````

	// find the direction vector
		direction_ << center.x,center.y,1;  // just to draw something with the arrow marker

        // draw a bounding box around the ball
        box.x = center.x-radius;
        box.y = center.y-radius;
        box.width = radius*2+1;
        box.height = radius*2+1;
        cv::rectangle(cv_img_out_.image, box, cv::Scalar(0,255,255), 3);
    }

    //reset input image
    cv_img_ptr_in_ = nullptr;
}

````

That is the precess I should take, but unexpectedly when I launch the program it detects an error and the program does not load me well.

![Error Screenshot](https://github.com/adiaz/ros_img_processor/blob/master/media/error.png)
