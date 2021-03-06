# Interoperability

Package implementing a ROS-JAUS bridge for the IOP challenge.

The package has two nodes:
* `iop_node` : Main navigation and reporting component for the IOP challenge.
* `cvt_node` : Conformance Verification Tool to test the IOP stack.

### OpenJAUS

---

The package uses the OpenJAUS SDK for all JAUS related dependencies.
Once you have acces to the OpenJAUS SDK, make the following changes in CMakeLists.txt:
* Set **JAUS_INCLUDE_PATH** to "path-to-openjaus-sdk/include"
* Set **JAUS_LIB_PATH** to "path-to-openjaus-sdk/lib"

### Setup

---

When Solo's ROS stack is running on one system and the CVT is running on another system, they need to be on the same network for communication.
The last octet of the IP address of Solo's system on the network should be set as the Subsystem ID in *iop_node.ojconf*.
This will ensure proper communication between the CVT and Solo.

This package uses services provided by other packages for proper reporting and handling of CVT requests. These dependencies must be available to build this package.
The dependencies include:
* [Waypoint Server](https://github.com/Project-MANAS/waypoint_server): To set waypoints for waypoint navigation task
* [Fake Planner](https://github.com/Project-MANAS/fake_planner): To dynamically change maximum velocity for Solo based on movement and state requirements
* [Robot Localization](https://github.com/cra-ros-pkg/robot_localization): To set current local pose

### Usage

---

##### IOP Stack
Set the following parameters in *iop.launch*:  
  - `odom_topic (default: "/odom")`: Odometry information that gives the current velocity and pose of the robot (type: *nav_msgs/Odometry*)
  - `goal_reached_topic (default: "/goal_reached")`: Topic where the planner publishes when the current goal is reached (type: *std_msgs/Bool*) 
  - `set_pose_service (default: "/set_pose")`: Service provided by `robot_localization` package to set current local pose
  - `set_max_velocity_service (default: "/fake_planner/set_max_vel")`: Service provided by `fake_planner` package to set maximum velocity
  - `set_waypoint_service (default: "/set_pose_waypoint")`: Service provided by `waypoint_server` package to set waypoints
  - `get_waypoint_service (default: "/get_target_waypoint")`: Service provided by `waypoint_server` package to get current waypoint
  - `max_linear_x (default: 0.5)`: maximum linear velocity for direct cmd_vel commands
  - `max_angular_z (default: 0.2)`: maximum angular velocity for direct cmd_vel commands
  - `cmd_vel_topic (default: "/cmd_vel")`: topic where cmd_vel commands should be published

To launch the main IOP stack for Solo:
```bash
roslaunch interoperability iop.launch
```
---

##### Conformance Verification Tool
Set the following parameters in *cvt.launch*:
  - `local_pose_topic (default: "/CVT/local_pose")`: listens to this topic for local pose commands to send (type: *geometry_msgs/Pose*)
  - `waypoint_topic (default: "/CVT/waypoint")`: listens to this topic for waypoint commands to send (type: *geometry_msgs/Pose*)
  - `waypoint_list_topic (default: "/CVT/waypoint_list")`: listens to this topic for waypoint list commands to send (type: *geometry_msgs/PoseArray*)
  - `max_speed_topic (default: "/CVT/max_speed")`: listens to this topic for max speed commands to send (type: *std_msgs/Float64*)

To launch the CVT:
```bash
roslaunch interoperability cvt.launch
```
