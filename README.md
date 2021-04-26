This ROS Node publishes pose data from HTC Vive Tracker (Tested on Ubuntu 18.04 + ROS Melodic and Ubuntu 20.04 + ROS Noetic)

Forked and modified from on https://github.com/moon-wreckers/vive_tracker

# Prerequisites

Up to date graphics drivers

x86 architecture

SteamVR requires >4GB disk space


# Installation Instructions
1. [Install the latest VulkanSDK from here.](https://vulkan.lunarg.com/sdk/home#linux)
   
      1. Download the latest VulkanSDK tarball file vulkansdk-linux-x86_64-xxx.tar.gz 
      2. Decompress the file and run the `vulkansdk` file in the folder


2. Install Steam from http://store.steampowered.com/

      1. `sudo apt install steam`

      2. Open steam with `steam` command, or through the Ubuntu menu. Make a Steam account & Log in.
  
      3. Enable the Steam beta through the Steam Menu -> Settings -> Account -> Beta Participation. [See the video here to see how to enable the beta.](https://www.youtube.com/watch?v=7AFUcj3HpvE)
   
      4. (Recommended) Save your credentials while logging in, and once you do log in open the `Steam` Menu item in the top left corner and select `Go Offline`. This prevents Steam from updating every time you use the Vive Tracker. 

2. Install SteamVR. 

   1. Click Library

   2. Click VR

   3. On the left you should now see SteamVR. Select it and click the the blue Install button.

   4. Make a Symbolic Link from libudev.so.0 to libudev.so.1 for SteamVR to use.

       `sudo ln -s /lib/x86_64-linux-gnu/libudev.so.1 /lib/x86_64-linux-gnu/libudev.so.0`

3. Install pyopenvr 

   - For Ubuntu 18.04 (ROS Melodic), install pyopenvr (version<1.4) `pip install --user 'openvr<1.4'`. Because the default python for ROS Melodic is python2, but pyopenvr after 1.4 doesn't have python 2 release anymore.
   - For Ubuntu 20.04 (ROS Noetic), install pyopenvr by `pip install openvr` directly.


4. Disable the headset requirement and enable a null (simulated) headset :
  [Source](https://www.reddit.com/r/Vive/comments/4uo053/how_to_use_steamvr_tracked_devices_without_a_hmd/) 

   1. Open file
   
      ```gedit ~/.steam/steam/steamapps/common/SteamVR/resources/settings/default.vrsettings```

   2. Change the third line from `"requireHmd" : true,` to `"requireHmd" : false,`

   3. Also change `"activateMultipleDrivers" : true`, and the line `"forcedDriver": "null"`. save and exit.
   
   4. Open `default.vrsettings` by 
   
      ```gedit ~/.steam/steam/steamapps/common/SteamVR/drivers/null/resources/settings/default.vrsettings```

   5. Set `enable` (line 3) to `true` in null driver to enable it.

  
5. Build package.

```
cd ~/workspace/src/
git clone https://github.com/luorui93/vive_tracker.git
catkin build
```

# Usage
1. Start SteamVR from the Steam Library (If you encounter `VRClientDLLNotFound`, make sure all of the dependencies are installed properly, especially VulkanSDK, and delete and recreate the symbolic link described above).

2. Turn on the tracker with its button, and make sure that its wireless USB dongle is plugged in to your computer. If the tracker shows up in the SteamVR overlay skip to step 4.

3. Sync the tracker. Hold the button on the tracker until the light blinks. On the SteamVR overlay click the "SteamVR" dropdown menu. Click Devices->Pair Controller. The Tracker should then pair with the computer, and a green outline of the tracker should appear on the SteamVR overlay. If this doesn't work try unplugging the wireless USB dongle, plugging it back in, and restarting SteamVR. Restarting your computer wouldn't hurt either.

4. Ensure the Lighthouse base stations are turned on, facing each other, have green lights showing on them Place the tracker in view of the Base Stations. The SteamVR overlay should now show two green square Base Stations and a solid green Tracker hexagon. The tracker is now working. 

     1. If you're only using 1 Base Station, make sure it's set to mode A.
     
     2. If you're using 2 Base Stations without a sync cable, ensure they're set to modes B and C.
     
     3. If you're using 2 Base Stations with a sync cable, ensure they're set to modes A and B.

5. Run this ROS node. 

```
source ~/catkin_ws/devel/setup.bash
roslaunch vive_tracker vive_tracker.launch
``` 
   1. Now open another terminal and run `rostopic echo /vive_tracker` to view the x y z roll pitch yaw output from the tracker.

6. (Optional) Start RViz in another terminal with `rviz`

7. (Optional) In the lower left corner of RViz click on `Add`, and scroll down the Add menu to add a `TF`. If all went well you should now be able to see the tracker moving in RViz. 

8. If for some reason it isn't working, check to ensure that the Tracker is turned on, SteamVR is still running, the tracker icon is green, and the vive_tracker ros node is still running.


# Command Line

Here's a handy command, run this in the command line to start SteamVR with the command `steamvr`

`alias steamvr='LD_LIBRARY_PATH=~/.steam/bin32/ ~/.steam/bin32/steam-runtime/run.sh ~/.steam/steam/steamapps/common/SteamVR/bin/vrstartup.sh' >> ~/.bashrc && source ~/.bashrc`

This will start the server in another process, so you're free to keyboard interrupt (Ctrl+C) the terminal once the server starts. 

To kill the SteamVR process:

`sudo pkill -9 vr*`




# Links

This is based off of Triad Semiconductor's awesome tutorial found here:

http://help.triadsemi.com/steamvr-tracking/steamvr-tracking-without-an-hmd/

Also thanks to Christopher Bruns for his work on pyopenvr.

https://github.com/cmbruns/pyopenvr
