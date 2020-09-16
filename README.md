# pipeline
1. Data collection and initial processing
This process contains image_converter and object3d_detecter. Each of them receives images from a fisheye cam and a lider and processes them for the first time.image_converters makes bbox and object3d_detecter makes groups of points. These data will be published and further used through the later process.

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
Object3dDetector::pointCloudCallback{extractCluster, classify}

1.2.1.1 extractCluster
It compare distances of pc_indices(from lidar) with range. If distance is in range, then push it to indices_array. (do space divde) And it
set cluster_size min, max value. Also it conducts clustering, set(calculate) width, height, centroid. And limit size(not accurate).

1.2.1.2 classify
It marks id, min& max values, frame_id, and colors. And it sets position of pose as centroid. 

1.2.1.3 pointCloudCallback
It calls function extractCluster, and classify. After, it prints fps and time.

1.2.2 overall
Divide space(comparing distance)->clustering->classifying->publishing & printing
