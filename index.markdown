---
layout: page
title: Fetch Interview Assignment
---


## Part 1: Building a Simulated Warehouse
For Part 1 of this assignment, a warehouse with the layout shown in Figure 1 is to be created.

![warehouse_layout.png](images/warehouse_layout.png)
<center><h4><i>Figure 1: Warehouse Environment Layout</i></h4></center>


Figure 2 shows a short animation of a 3D Warehouse environment created in Gazebo with the layout from Figure 1. The shelf and floor models were created in Blender and exported as `.dae` files. Then, `model.sdf` and `model.config` files were created for each of these models. Finally, all of these models were assembled into the 3D environment shown in Figure 2 using a `.world` file. All model and world files can be downloaded using the links below.

<iframe width="740" height="400" src="https://www.youtube.com/embed/rzzO0s8lRzU" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 2: Animation of the 3D Warehouse Environment created using Gazebo</i></h4></center>


## Part 2.A: Running Freight Robot Simulation on Fetch Environment

For this part, Fetch's Freight robot is simulated in the built-in Fetch environment provided as part of `fetch_gazebo` package. 

First of all, the Freight robot model is started in a empty environment to verify if the robot model is being rendered properly. This is done by running the command `roslaunch fetch_gazebo simulation.launch robot:=freight`. Figure 3 shows the Freight robot in an empty environment. 

<iframe width="740" height="400" src="https://www.youtube.com/embed/MO_dBMwOx8c" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 3: Animation of the Freight robot in an empty environment</i></h4></center>


Now, the Freight robot can be simulated in the built-in Fetch environment. This can be done using the command `roslaunch fetch_gazebo playground.launch robot:=freight`. Figure 4 shows the synchronised Gazebo and Rviz views of the Freight robot under simulation. Rviz view shows how the Freight robot perceives the simulated world through its simulated 2D Lidar.  

<iframe width="740" height="400" src="https://www.youtube.com/embed/AnGpSja6Qak" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 4: Synchronised Gazebo and Rviz views of the Fright robot under simulation with a 2D Lidar</i></h4></center>


Next, the robot model is modified to include a 3D Depth Sensor. Gazebo's Depth Camera Plugin is used to create a 3D Depth Sensor and integrate it into the robot. This is done by modifying the `freight.gazebo.xacro` and `freight.urdf` files. `freight.gazebo.xacro` is used to integrate the 3D Depth Sensor into the robot at a predefined frame location, define sensor parameters and the topic names. `freight.urdf` is used to define the frame locations for the sensor. Modified `freight.gazebo.xacro` and `freight.urdf` can be found in the links below.

Once the 3D Depth Sensor is integrated into the Freight Robot, it is simulated in the built-in Fetch environment once again. Figure 5 shows the synchronised Gazebo and Rviz views of the Freight robot under simulation with a 3D Depth Sensor.

<iframe width="740" height="400" src="https://www.youtube.com/embed/3ra6steuV4M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 5: Synchronised Gazebo and Rviz views of the Fright robot under simulation with a 3D Depth Sensor</i></h4></center>


## Part 2.B: Recording Relevant Topics

When the Freight robot simulation is started, a wide range of topics are created and published. Following code snipped shows the list of all topics being published:
```
/base_controller/command
/base_scan
/base_scan_raw
/camera/color/camera_info
/camera/color/image_raw
/camera/color/image_raw/compressed
/camera/color/image_raw/compressed/parameter_descriptions
/camera/color/image_raw/compressed/parameter_updates
/camera/color/image_raw/compressedDepth
/camera/color/image_raw/compressedDepth/parameter_descriptions
/camera/color/image_raw/compressedDepth/parameter_updates
/camera/color/image_raw/theora
/camera/color/image_raw/theora/parameter_descriptions
/camera/color/image_raw/theora/parameter_updates
/camera/depth/camera_info
/camera/depth/image_raw
/camera/depth/points
/clock
/cmd_vel
/cmd_vel_mux/selected
/gazebo/l_wheel_joint/position/parameter_descriptions
/gazebo/l_wheel_joint/position/parameter_updates
/gazebo/l_wheel_joint/velocity/parameter_descriptions
/gazebo/l_wheel_joint/velocity/parameter_updates
/gazebo/link_states
/gazebo/model_states
/gazebo/parameter_descriptions
/gazebo/parameter_updates
/gazebo/performance_metrics
/gazebo/r_wheel_joint/position/parameter_descriptions
/gazebo/r_wheel_joint/position/parameter_updates
/gazebo/r_wheel_joint/velocity/parameter_descriptions
/gazebo/r_wheel_joint/velocity/parameter_updates
/gazebo/set_link_state
/gazebo/set_model_state
/head_camera/parameter_descriptions
/head_camera/parameter_updates
/joint_states
/odom
/query_controller_states/cancel
/query_controller_states/feedback
/query_controller_states/goal
/query_controller_states/result
/query_controller_states/status
/rosout
/rosout_agg
/teleop/cmd_vel
/tf
/tf_static
```

Out of all the topics listed above, following topics are relevant for mapping, planning and testing:

 - `/base_scan` or `/base_scan_raw`: These topics show what the 2D Lidar of the Freight Robot is currently observing. 2D Lidar 2D-Pointcloud/Laserscan frames can be sent into a SLAM algorithm to generate a map of the robot environment and localize or provide a pose of the robot within the robot environment. Robot pose can be very very useful while planning a path and ensuring that it follows the path.
 -  `/camera/color/image_raw`: This topic can be used to obtain images of the robot environment. These images can then be used to conduct object classification and differentiate between static and dynamic obstacles. This information can then be used to produce a better map of the robot environment.
 - `/camera/depth/points`: This topic produces a dense 3D-Pointcloud representation of the robot environment. This can be used to improve the performance of SLAM algorithm and improve quality of the map produced.
 -  `/odom`: This topic provides the current linear and angular velocity of the robot calculated based on wheel rotation speeds. Actual robot velocities can be very useful while implementing a control algorithms. A well-functioning control algorithm is required to test if a robot can follow a path to reach a goal position. This topic also provides pose of the robot relative to its starting positon which is calculated using wheel odometry. This can be very useful for a SLAM algorithm. However, due to wheel slippage, wheel odometry is generally very inaccurate and not quite useful. 
 - `/query_controller_states/feedback`, `/query_controller_states/goal`, `/query_controller_states/result` and `/query_controller_states/status`: These topics can be very useful to evaluate the high-level states of a robot during test runs. We can check what the start and goal positions for a robot are and generate a path based on that. We can check where on a path a robot is. We can also check what the current status of a robot is and if the robot has encountered any faults.
 - `/clock`: This topic keeps a record of time and ensures that all other topics work properly.

A rosbag containing the topics vital for mapping, planning and testing can be found in the link below.

## Part 2.C: Simulating Freight Robot in Part 1 Warehouse Environment

For this part, the Fright robot is placed and simulated in the warehouse environment created in Part 1. Starting pose of the robot is changed by modifying the `freight.launch.xml` file so that the robot doesn't start in the same place as a shelf. Then, `playground.launch` file is modified to select the world file for the warehouse environment from Part 1. 

Figure 6 shows Freight robot in the warehouse environment created in Part 1. Figure 7 shows the robot moving around in this environment.

<iframe width="740" height="400" src="https://www.youtube.com/embed/EYGr-PgB4NM" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 6: Freight Robot in the Warehouse Environment created in Part 1</i></h4></center>


<iframe width="740" height="400" src="https://www.youtube.com/embed/FjOibs9Lni0" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
<center><h4><i>Figure 7: Freight Robot moving around in the Warehouse Environment created in Part 1</i></h4></center>


A rosbag containing a record of the Freight robot run in this environment can be found in the link below.

