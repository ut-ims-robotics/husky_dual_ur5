# husky_dual_ur5
ROS packages for Clearpath Husky base with two UR5 manipulators. This repo is aimed to be used as the base configuration for **simulating dual UR5 Husky**.

Among the plethora of non-maintainable and consequently outdated dual UR5 Husky setups, this repo aims to glue dual UR5 Husky together without:
* outrageous amounts of forked repos as submodules
* badly placed configuration files (e.g., inside native packages of UR5 or Husky)
* unfathomable amount of dependencies, such as pan-tilt unit or gripper device drivers

## Installation

First install the dependencies:
```bash
sudo apt install gazebo11 ros-noetic-gazebo-ros ros-noetic-gazebo-ros-control ros-noetic-gazebo-plugins ros-noetic-lms1xx ros-noetic-robot-localization ros-noetic-interactive-marker-twist-server ros-noetic-twist-mux ros-noetic-map-server ros-noetic-amcl ros-noetic-move-base ros-noetic-joint-trajectory-controller ros-noetic-joint-state-controller ros-noetic-diff-drive-controller ros-noetic-dwa-local-planner ros-noetic-controller-manager
```

Then install this package and build it:
```bash
cd <your-catkin-workspace>/src
git clone --recursive https://github.com/ut-ims-robotics/husky_dual_ur5
cd ..
catkin build
source devel/setup.bash
```

## Run the simulation
Open a terminal and launch the simulation:
```bash 
roslaunch husky_dual_ur5_config husky_dual_ur5_sim.launch
```
You should see the Gazebo sim opening up. If you are launching it for the first time, then
it might take some time for Gazebo to download the world models. Once the world is loaded, you 
should see the robot. There might be couple of warnings in the terminal about `No p gain specified for pid` 
or `Failed to meet update rate!` but that's nothing to worry about.


Next, open a second terminal and launch the visualization:
```bash
roslaunch husky_dual_ur5_config husky_dual_ur5_sim_viz.launch
```

RViz should pop up and you should see the map, robot and lidar data. Use the `2D Nav Goal` tool to move the robot somewhere within the map and also try to move the manipulators via the interactive marker. If the robot performs sucessfully, then the simulation is set up properly.

## TODO and comments:
* Adding grippers to the simulation
* The reason why there is a copy of the clearpath_playpen.world file in the `husky_dual_ur5_config` package is that the original file inside `husky_gazebo` package has non-zero default values for the sim time. Combined with the [broken joint specification options](https://github.com/ros-simulation/gazebo_ros_pkgs/issues/93) of the `gazebo_ros spawn_model`, the robot will ignore the joint values defined in `husky_dual_ur5_config husky_dual_ur5_sim.launch`
* The URDF in the `husky_dual_ur5_description` package is far from ideal, because it's duplicating a lot of what already is in described in `husky_description`. The reason for that is `husky.urdf.xacro` in `husky_description` does not have a nice way to parametrically replace the top_plate link with the bulkhead link and thus it cannot be included as a xacro for the dual arm setup.