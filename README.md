# pipeline
1. Data collection and initial processing

1.1 image_converter 
image_converter node subscribes orginal img data from fisheye camera and publish inference results creating child thread. 

1.1.1 function call
image_converter{parse_args, TrtThread}. 
A{B,C} means function A calls B, and C in order.

1.1.1.1 parse_args
parse input data from fisheye camera. 

1.1.1.2 image_converter
parse, and set robot args, publish and subscribe image 
callback: Converting ROS image messages to OpenCV images, put cv_image to img_raw_queue, find bounidng boxes

1.1.1.3 TrtThread
It implements the child thread (read images from cam to TRT inferencing). The child thread stores the input image and detection results (global & condition variable)
run: run until running flag is set to False in main thread. It detects human by function trt_ssd.detect(img, self.conf_th). 

1.1.2 overall
parse(cam)->detect(by trt_ssd.detect) human->make bbox and publish


1.2 object3d_detecter
object3d_detecter conducts clustering points from lidar. It also calculate centroid, and min & max value of x,y, z postion and publish it.

1.2.1 function call 
Object3dDetector
