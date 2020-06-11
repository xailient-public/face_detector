!!! error "DEPRICATED"
    [Xailient FaceSDK portal](https://sdk.xailient.com) is out-of-date and will no longer be maintained. Please use [Xailient AI Console](https://console.xailient.com) instead to get the updated FaceDetector SDK and more detector models.
    This documentation is out-of-date and will no longer be maintained. [Visit our new documentation](https://xailient-docs.readthedocs.org).
    
# Face Detection on Static Image

<pre><code>
import dnn
import cv2 as cv

#By default Low resolution DNN for face detector will be loaded.
#To load the high resolution Face detector please comment the below lines.
#dnn.FaceDetector.set_param("resolution", dnn.RESOLUTION.HIGH)
detectum = dnn.FaceDetector()
THRESHOLD = 0.4 # Value between 0 and 1 for confidence score

im = cv.imread('../data/beatles.jpg')
_, bboxes = detectum.process_frame(im, THRESHOLD)

# Loop through list (if empty this will be skipped) and overlay green bboxes
for i in bboxes:
    cv.rectangle(im, (i[0], i[1]), (i[2], i[3]), (0, 255, 0), 3)

cv.imwrite('../data/beatles_output.jpg', im)
</code></pre>


# Face Detection on Pi Camera Streaming Video

<pre><code>
# This script has been tested using a Raspberry Pi Camera Modeule v1.3

from picamera.array import PiRGBArray
from picamera import PiCamera
import time
import cv2 as cv
import math
import dnn
import numpy as np

detectum = dnn.FaceDetector()
THRESHOLD = 0.8
 
# initialize the camera and grab a reference to the raw camera capture
RES_W = 640 # 1280 # 640 # 256 # 320 # 480 # pixels
RES_H = 480 # 720 # 480 # 144 # 240 # 360 # pixels

camera = PiCamera()
camera.resolution = (RES_W, RES_H)
camera.framerate = 30 # FPS
rawCapture = PiRGBArray(camera, size=(RES_W, RES_H))
 
# allow the camera to warmup
time.sleep(0.1)
 
# capture frames from the camera
for frame in camera.capture_continuous(rawCapture, format="bgr", use_video_port=True):
    # grab the raw NumPy array representing the image, then initialize the timestamp
    # and occupied/unoccupied text
    image = frame.array
    image_cpy = np.copy(image) # Create copy since cant modify orig

    # Return's list of bounding box co-ordinates
    _, bboxes = detectum.process_frame(image, THRESHOLD)

    # Loop through list (if empty this will be skipped) and overlay green bboxes
    for i in bboxes:
        cv.rectangle(image_cpy, (i[0], i[1]), (i[2], i[3]), (0, 255, 0), 3)
    
    # show the frame
    cv.imshow("Frame", image_cpy)
    key = cv.waitKey(1) & 0xFF
 
    # clear the stream in preparation for the next frame
    rawCapture.truncate(0)
 
    # if the `q` key was pressed, break from the loop
    if key == ord("q"):
        break

</code></pre>