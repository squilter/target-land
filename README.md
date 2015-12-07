target-land
===========

This demo allows a 3DR Solo to land on a colorful object using only the quadcopter, the controller, a gopro and a laptop.  The CV runs offboard.

TODO post video

###Setup instructions:
####video:
The SDP file defines a network stream. Opening this file with VLC or opencv's `cv2.VideoCapture()` should work.  But it didn't when I tried it.  I think it was related to multicast. So I changed the settings to just send the video right to the IP address of my computer.  Note that this means the video will not display in the app anymore until the change is reverted.

- ssh into the controller root@10.1.1.1
- edit `/usr/bin/video_recv.sh`
- find the line that starts with `sn_decoder start` and change the IP address from `127.0.0.1` to `10.1.1.XX`

####autopilot firmware:
Clone [ardupilot](github.com/diydrones/ardupilot) and `cd` into ArduCopter.  In the file called `APM_Config.h`, find the line that says `//#define PRECISION_LANDING ENABLED`.  Uncomment this line.  Now compile with `make px4-v2`. The build output is in this folder, and it's called `ArduCopter-v2.px4`.  Now upload it to the Solo:

- `scp ./ArduCopter-v2.px4 root@10.1.1.10:/firmware/ArduCopter-v2.px4`

restart the Solo and wait a minute for the changes to take effect.

####parameters:
Using you favorite GCS, change these parameters:

- `PLAND_ENABLE` = 1
- `PLAND_TYPE` = 1 

####colors:
In `target_land.py` there are variables near the top that need to be set in order to detect the right color. They are in HSV.  Change these depending on the object, camera, lighting, etc.

####run it:
Make sure these dependencies are updated:
`sudo pip2 install mavproxy dronekit pymavlink numpy --upgrade`
Also install opencv and put the right things in your path so that you can `import cv2`.

run it: `./target_land.py`

The script will connect to the video feed of the camera and then will connect to the vehicle over mavlink.  Whenever it sees a target that is big enough, it will send a LANDING_TARGET message.  
