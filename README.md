target-land
===========

This demo allows a 3DR Solo to land on a colorful object using only the quadcopter, the controller, a gopro and a laptop.  The CV runs offboard.

[Demo Video](https://youtu.be/PURlcAu6MEk)

### Setup instructions:

#### video:

The SDP file defines a network stream. Opening this file with VLC or OpenCV's `cv2.VideoCapture()` should work. Before opening the network stream, we need to tell the controller to start streaming video. We do this by running `nc 10.1.1.1 5502` on the computer where we want to receive the video. Let that run in the background. TODO make the python script open this instead, so the user isn't bothered by this extra step.


#### autopilot firmware:

Clone [ardupilot](github.com/diydrones/ardupilot) and `cd` into ArduCopter.  In the file called `APM_Config.h`, find the line that says `//#define PRECISION_LANDING ENABLED`.  Uncomment this line.  Now compile with `make px4-v2`. The build output is in this folder, and it's called `ArduCopter-v2.px4`.  Now upload it to the Solo:

- `scp ./ArduCopter-v2.px4 root@10.1.1.10:/firmware/ArduCopter-v2.px4`

restart the Solo and wait a minute for the changes to take effect. If it doesn't work, try bumping the version number in the filename of `/firmware/ArduCopter-v2.px4` to be higher than the last firmware that was flashed (located in `./loaded`).

#### parameters:

Using you favorite GCS, load the parameters file. Using MAVProxy it looks like this:

- `mavproxy.py --master=udpin:0.0.0.0:14550`
- `param load ./params.param`

Then do a compass calibration using the Solo app, not a traditional GCS.  A traditional GCS will do an offboard compass calibration, we want to do an onboard calibration.

#### colors:

In `target_land.py` there are variables near the top that need to be set in order to detect the right color. They are in HSV.  Change these depending on the object, camera, lighting, etc. Red objects might are not ideal, because their H value is sometimes very high and sometimes very low.

#### run it:

Make sure these dependencies are updated:
`sudo pip2 install mavproxy dronekit pymavlink numpy --upgrade`.

Also install OpenCV and put the right things in your path so that you can `import cv2` in Python2.

run it: `./target_land.py`

The script will connect to the video feed of the camera and then will connect to the vehicle over MAVLink.  Whenever it sees a target that is big enough, it will send a LANDING_TARGET message.
