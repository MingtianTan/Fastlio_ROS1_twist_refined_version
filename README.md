# Fast-LIO (ROS1, MID360) — UM-iRaL Setup Guide v1.2

> Author: Teemty (teemty@umich.edu) • Date: 2025-08-27

This repository documents how to set up and run **FAST_LIO** with the **Livox MID360** LiDAR on **Ubuntu 20.04 + ROS Noetic**.  
The setup involves installing the Livox SDK/driver, compiling FAST_LIO, configuring network settings, and running mapping.


## Prerequisites

- Ubuntu 20.04 + ROS Noetic
- Two workspaces:
  - `Lidar_driver` for Livox SDK/driver
  - `Fast_lio_ws` for FAST_LIO


## 1. Install Livox-SDK2 and Driver

### 1.1 Install Livox-SDK2

	cd Lidar_driver/src
	git clone https://github.com/Livox-SDK/Livox-SDK2.git
	cd Livox-SDK2
	mkdir build && cd build
	cmake .. && make -j
	sudo make install

### 1.2 Install livox_ros_driver2

	cd lidar_driver/src
	mkdir -p catkin_livox_ros_driver2/src
	cd catkin_livox_ros_driver2/src
	git clone https://github.com/Livox-SDK/livox_ros_driver2.git

	cd livox_ros_driver2
	source /opt/ros/noetic/setup.sh
	./build.sh ROS1

## 2. Install FAST_LIO

	cd Fast_lio_ws/src
	git clone https://github.com/hku-mars/FAST_LIO.git
	cd FAST_LIO
	git submodule update --init
	cd ../..

	source ~/lidar_driver/src/catkin_livox_ros_driver2/devel/setup.bash
	catkin_make
	source devel/setup.bash

### 2.1 code adjustment
Inside /src/FAST_LIO, replace occurrences of livox_ros_driver with livox_ros_driver2 (~25 occurrences, use VSCode/global replace). 

## 3. Network Host setting for lidar

### 3.1 Wired IPV4 setting 
	Address: 192.168.1.50 
	Netmask: 255.255.255.0 
	Gateway: 192.168.1.1 

Turnoff Mwireless after connecting lidar wire. 

### 3.2 Launch file adjusting 
In /catkin ... /livox_ros_driver2/ ... /launch 
change boradcasting (bd list) code of lidar: for lidar used for Iral: 
47MDM6E0020163 

### 3.3 Config file setting
In /catkin ... /livox_ros_driver2/.../config/MID360_config.json: 

	  "host_net_info" : {
      "cmd_data_ip" : "192.168.1.50",
      "cmd_data_port": 56101,
      "push_msg_ip": "192.168.1.50",
      "push_msg_port": 56201,
      "point_data_ip": "192.168.1.50",
      "point_data_port": 56301,
      "imu_data_ip" : "192.168.1.50",
      "imu_data_port": 56401,
      "log_data_ip" : "",
      "log_data_port": 56501
    }
  	},
  	"lidar_configs" : [
    {
      "ip" : "192.168.1.163",
 
## 4. Node testing

## 4.1 Startup
in folder Lidar_ws/src/catkin_livox_ros_driver2: 

	source devel/setup.bash
	roslaunch livox_ros_driver2 msg_MID360.launch
 
open another terminal: 
in folder /fastlio 

	source devel/setup.bash
	roslaunch fast_lio mapping_mid360.launch

## 4.2 view result

in folder FAST_LIO/PCD: 

	sudo apt install pcl-tools
	pcl_viewer scans.pcd

