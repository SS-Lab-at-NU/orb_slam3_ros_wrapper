**Ongoing development.**

# ROS wrapper for ORB-SLAM3

A ROS wrapper for [ORB-SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3). The main idea is to use the ORB-SLAM3 as a standalone library and interface with it instead of putting everything in one package. 

Tested with ORB-SLAM3 V1.0, primarily on Ubuntu 20.04.

- **Pros**:
  - Easy to update [ORB-SLAM3](https://github.com/UZ-SLAMLab/ORB_SLAM3#orb-slam3) indepedently.
  - Easy to replace different variants that are not built for ROS.
- **Cons**:
  - Development involves more steps (1. Make changes in ORB-SLAM3 library -> 2. Build ORB-SLAM3 -> 3. Change the roswrapper if necessary -> 4. Test).
  - Might break when dependencies or upstream changes.


# Installation

General guide: first, install ORB-SLAM3 normally with all of its dependencies (any location is fine). Then, install this package in a ```catkin build``` environment.

## 1. ORB-SLAM3

- Install the [prerequisites](https://github.com/SS-Lab-at-NU/ORB_SLAM3#2-prerequisites).

- Clone ORB-SLAM3:
```
cd ~
git clone https://github.com/SS-Lab-at-NU/ORB_SLAM3.git ORB_SLAM3
```
- Make changes to the source code if necessary to build successfully. For Ubuntu 20.04, you will need to change CMakeList from C++11 to C++14. I have incorporated the changes in [this fork](
https://github.com/thien94/ORB_SLAM3).
- Build:
```
cd ORB_SLAM3
chmod +x build.sh
./build.sh
```
- Make sure that **`libORB_SLAM3.so`** is created in the *ORB_SLAM3/lib* folder. If not, check the issue list from the [original repo](https://github.com/UZ-SLAMLab/ORB_SLAM3/issues) and retry.

## 2. orb_slam3_ros_wrapper

- Clone the package. Note that it should be a `catkin build` workspace.
```
cd ~/catkin_ws/src/
git clone https://github.com/SS-Lab-at-NU/orb_slam3_ros_wrapper.git
```

- Set ORB_SLAM3 path
```
export ORB_SLAM3_DIR=<path>
```

- Build the package normally.
```
cd ~/catkin_ws/
catkin build
```

- Next, copy the `ORBvoc.txt` file from `ORB-SLAM3/Vocabulary/` folder to the `config` folder in this package. Alternatively, you can change the `voc_file` param in the launch file to point to the right location.

- (Optional) Install `hector-trajectory-server` to visualize the trajectory.
```
sudo apt install ros-[DISTRO]-hector-trajectory-server
```

- If everything works fine, you can now try the different launch files in the `launch` folder.

## 3. How to run

### Aerobat Datasets:

- In one terminal, launch the node:
VIO
```
roslaunch orb_slam3_ros_wrapper orb_slam3_aerobat_monoimu.launch
```
VO
```
roslaunch orb_slam3_ros_wrapper orb_slam3_aerobat_mono.launch
```

- In another terminal, playback the bag:
```
rosbag play 320x240_data.bag

### EuRoC dataset:

- In one terminal, launch the node:
```
roslaunch orb_slam3_ros_wrapper orb_slam3_euroc_monoimu.launch
```
- In another terminal, playback the bag:
```
rosbag play MH_01_easy.bag
```
Similarly for other sensor types.

# Topics
The following topics are published by each node:
- `/orb_slam3_ros/map_points` ([`PointCloud2`](http://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/PointCloud2.html)) containing all keypoints being tracked.
- `/orb_slam3_ros/camera` ([`PoseStamped`](http://docs.ros.org/en/melodic/api/geometry_msgs/html/msg/PoseStamped.html)): current pose of the camera in the world frame, as returned by ORB-SLAM3 with the world coordinate transformed to conform the ROS standard.
- `tf`: transformation from the camera fraame to the world frame.
