# Fastlio_ROS1_twist_refined_version

Modifying laserMapping.cpp

#####################################
#   UM-iRal Lab                     #
#   Fast-lio Mapping                #
#   Version 1.2 made by Teemty      #
#   teemty@umich.edu                #
#               8/27/2025           #
#####################################

####################
# pull the repo   #
#  for driver      #
#                  #
####################

####
In Lidar_ws/src folder
####
	git clone https://github.com/Livox-SDK/Livox-SDK2.git
	cd ./Livox-SDK2/
	mkdir build
	cd build
	cmake .. && make -j
	sudo make install

####
In Fast_lio_ws/src folder
####		
	mkdir -p catkin_livox_ros_driver2/src
	cd catkin_livox_ros_driver2/src
	git clone https://github.com/Livox-SDK/livox_ros_driver2.git 
	 
	cd livox_ros_driver2
	source /opt/ros/noetic/setup.sh 
	./build.sh ROS1 

####################
# pull the repo    #
#  for fast_lio    #
#                  #
####################
####
In Fast_lio_ws/src folder
####
	git clone https://github.com/hku-mars/FAST_LIO.git
	cd FAST_LIO
	git submodule update --init
	cd ../..
	source ~/lidar_driver/src/catkin_livox_ros_driver2/devel/setup.bash
	catkin_make
	source devel/setup.bash

#####################
# code replacing    #
#                   #
#####################
using vscode in FAST_LIO folder, replacing 25 "livox_ros_driver" with "livox_ros_driver2"


#####################
#  IP setting       #
#                   #
#                   #
#####################

1. manually set up for ip address (Network/wired):
########################################
IPV4 	address 	Netmask 	Gateway
	192.168.1.50   255.255.255.0   192.168.1.1
	
	
2. host address in ros bag
##########################################
2.1 in launch_ros1 (in livox_ros_driver2)

change boradcasting (bd list) code of lidar: for lidar used for Iral:
47MDM6E0020163

2.2 in config/ MID360_config.json:
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
      
      
      
######################
#  running the nodes #
#                    #
######################
###
in folder Lidar_ws/src/catkin_livox_ros_driver2
###
	source devel/setup.bash
	roslaunch livox_ros_driver2 msg_MID360.launch
open another terminal:
###
in folder fastlio
###
	source devel/setup.bash
	roslaunch fast_lio mapping_mid360.launch
	
######################
#   using PCD viewer #
#                    #
######################
in folder FAST_LIO/PCD:
	sudo apt install pcl-tools
	pcl_viewer scans.pcd
