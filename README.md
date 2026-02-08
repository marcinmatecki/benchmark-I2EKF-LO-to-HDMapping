# [I2EKF-LO](https://github.com/YWL0720/I2EKF-LO.git) converter to [HDMapping](https://github.com/MapsHD/HDMapping)

## Hint

Please change branch to [Bunker-DVI-Dataset-reg-1](https://github.com/MapsHD/benchmark-I2EKF-LO-to-HDMapping/tree/Bunker-DVI-Dataset-reg-1) for quick experiment.  

## Example Dataset: 

Download the dataset from [Bunker DVI Dataset](https://charleshamesse.github.io/bunker-dvi-dataset/)  

## Intended use 

This small toolset allows to integrate SLAM solution provided by [I2EKF-LO](https://github.com/YWL0720/I2EKF-LO.git) with [HDMapping](https://github.com/MapsHD/HDMapping).
This repository contains ROS 1 workspace that :
  - submodule to tested revision of I2EKF-LO
  - a converter that listens to topics advertised from odometry node and save data in format compatible with HDMapping.

## Dependencies
```shell
sudo apt install -y nlohmann-json3-dev
sudo apt install libgflags-dev
sudo apt install libgoogle-glog-dev
```

## Ceres
```shell
cd ~
git clone https://ceres-solver.googlesource.com/ceres-solver
cd ceres-solver && git fetch --all --tags
git checkout tags/2.1.0
mkdir build && cd build
cmake ..
make -j$(nproc)
sudo make install
```

## Building

Clone the repo
```shell
mkdir -p /test_ws/src
cd /test_ws/src
git clone https://github.com/marcinmatecki/I2EKF-LO-to-HDMapping.git --recursive
cd ..
catkin_make
```

## Usage - data SLAM:

Prepare recorded bag with estimated odometry:

In first terminal record bag:
```shell
rosbag record /cloud_registered /aft_mapped_to_init
```

and start odometry:
```shell 
cd /test_ws/
source ./install/setup.sh # adjust to used shell
roslaunch i2ekf_lo xxx.launch
rosbag play {path_to_bag}
```

## Usage - conversion:

```shell
cd /test_ws/
source ./install/setup.sh # adjust to used shell
rosrun i2ekf-lo-to-hdmapping listener <recorded_bag> <output_dir>
```

## Modify

```shell
src/I2EKF-LO/config/ouster.yaml

lid_topic:  "/os_cloud_node/points"

to:

lid_topic:  "/os1_cloud_node1/points"
```

## Record the bag file:

```shell
rosbag record /cloud_registered /aft_mapped_to_init -O {your_directory_for_the_recorded_bag}
```

## I2EKF-LO Launch:

```shell
cd /test_ws/
source ./devel/setup.sh # adjust to used shell
roslaunch i2ekf_lo ouster.launch 
rosbag play {path_to_bag}
```

## During the record (if you want to stop recording earlier) / after finishing the bag:

```shell
In the terminal where the ros record is, interrupt the recording by CTRL+C
Do it also in ros launch terminal by CTRL+C.
```

## Usage - Conversion (ROS bag to HDMapping, after recording stops):

```shell
cd /test_ws/
source ./devel/setup.sh # adjust to used shell
rosrun i2ekf-lo-to-hdmapping listener <recorded_bag> <output_dir>
```