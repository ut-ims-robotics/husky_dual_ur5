# temoto_servo

#### Quick Start Guide for UR5 example

Clone `husky_dual_ur5` repo into your catkin workspace:

    git clone https://github.com/ut-ims-robotics/husky_dual_ur5.git

Build and subsequently source the catkin workspace. Startup the robot and MoveIt:

    roslaunch husky_dual_ur5_config husky_dual_ur5_sim.launch

    roslaunch husky_dual_ur5_config husky_dual_ur5_sim_viz.launch

In RViz, "plan and execute" a motion to a non-singular position (not all zero joint angles) that is not close to a joint limit.

Switch to a compatible type of `ros-control` controller. It should be a `JointGroupVelocityController` or a `JointGroupPositionController`, not a trajectory controller like MoveIt usually requires.

```sh
rosservice call /controller_manager/switch_controller "start_controllers:
- 'joint_group_position_controller'
stop_controllers:
- 'l_arm_controller'
strictness: 2"
```

### Note: 
The above command would fail, since the controller does not exist. Please refer to  Issues section at the end of the readme for further details.

Launch the servo node. This example uses commands from a SpaceNavigator joystick-like device:

    roslaunch temoto_servo temoto_servo.launch

If you dont have a SpaceNavigator, send commands like this:

```sh
rostopic pub -r 100 /servo_server/delta_twist_cmds geometry_msgs/TwistStamped "header: auto
twist:
  linear:
    x: 0.0
    y: 0.01
    z: -0.01
  angular:
    x: 0.0
    y: 0.0
    z: 0.0"
```

If you see a warning about "close to singularity", try changing the direction of motion.

## Issues

By default, ur5 package uses FollowJointTrajectory Controller, but for this setup, we need either `JointGroupPositionController` or `JointGroupVelocityController`. Thus, it should be further investigated to find out how to load these controllers.

