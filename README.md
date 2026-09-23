# ROS 2 Notes

## Getting Started

**Source the ROS 2 setup script (do this first, every new terminal):**
```bash
source /opt/ros/humble/setup.bash
```

**List installed packages:**
```bash
ros2 pkg list
```

You'll see many packages. As beginners, the ones we'll use most are `turtlesim` and `teleop`, along with their executables.

## Turtlesim Basics

**Launch turtlesim:**
```bash
ros2 run turtlesim turtlesim_node
```

**Launch with custom parameters:**
```bash
ros2 run turtlesim turtlesim_node --ros-args --params-file turtlesim.yaml
```

**Move the turtle manually (keyboard teleop):**
```bash
ros2 run turtlesim turtle_teleop_key
```

**Publish velocity commands directly:**
```bash
ros2 topic pub --rate 1 /turtle1/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 1.0, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

**Spawn a new turtle:**
```bash
ros2 service call /spawn turtlesim/srv/Spawn "{x: 2.0, y: 2.0, theta: 0.2, name: 'turtle2'}"
```

**Clear the trajectory lines:**
```bash
ros2 service call /clear std_srvs/srv/Empty
```

**View the node/topic graph:**
```bash
rqt_graph
```

## Parameters

**Change the background color** (`r`, `g`, or `b`):
```bash
ros2 param set /turtlesim background_g 255
```

**Get the current value of a parameter:**
```bash
ros2 param get /turtlesim <parameter_name>
```

**Save all current parameters to a file:**
```bash
ros2 param dump /turtlesim > turtlesim.yaml
```

## Actions

**List all actions:**
```bash
ros2 action list
```

**Get info on a node:**
```bash
ros2 node info /teleop_turtle
```

**See the data type/definition of an action:**
```bash
ros2 interface show turtlesim/action/RotateAbsolute
```

**Send an action goal:**
```bash
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}"
```

## Installing Colcon and Creating a Workspace

**Install colcon:**
```bash
sudo apt install python3-colcon-common-extensions
```

**Create a workspace directory:**
```bash
mkdir -p ~/<directory_name>/src
cd ~/<directory_name>
```

**Clone the tutorial source code:**
```bash
git clone https://github.com/ros/ros_tutorials.git -b humble
```

**Set up colcon tab completion:**
```bash
echo "source /usr/share/colcon_argcomplete/hook/colcon_argcomplete.bash" >> ~/.bashrc
```

**Build with symlinks (instead of copies):**
```bash
colcon build --symlink-install
```

**Source the overlay (from the workspace directory):**
```bash
source install/local_setup.bash
```

**Now run turtlesim from this overlay** — this instance is now served from the workspace you just built/sourced, distinct from the system-installed one used above:
```bash
ros2 run turtlesim turtlesim_node
```

## Recording and Playing Back Data

**Echo a topic's data:**
```bash
ros2 topic echo /turtle1/cmd_vel
```

**Record a topic to a bag:**
```bash
ros2 bag record -o bagData /turtle1/cmd_vel
```

**Check info on a recorded bag:**
```bash
ros2 bag info bagData
```

**Play back the recorded data:**
```bash
ros2 bag play bagData/
```

## Setting Up a Fresh Colcon Workspace

```bash
sudo apt install python3-colcon-common-extensions

mkdir -p ~/ros_prac
cd ~/ros_prac

echo "source /usr/share/colcon_argcomplete/hook/colcon_argcomplete.bash" >> ~/.bashrc

colcon build --symlink-install
```

## Creating a Package

**Create a package** (run from `~/ros_prac/src`):
```bash
cd ~/ros_prac/src
ros2 pkg create --build-type ament_cmake --node-name <my_node> <my_package>
```

**Build a specific package** (re-source `local_setup.bash` afterward):
```bash
colcon build --packages-select <package_name>
```

**Run the node:**
```bash
ros2 run <package_name> <node_name>
```

### Creating a Publisher and Subscriber in Python

```bash
cd ~/ros_prac/src
ros2 pkg create --build-type ament_python py_pubsub
```

**Install/update dependencies via rosdep:**
```bash
rosdep install -i --from-path src --rosdistro humble -y
```

**Build (from `~/ros_prac`):**
```bash
colcon build --packages-select py_pubsub
```

## Simulation Packages (Gazebo / RViz)

You need to create a package any time you want to run something in Gazebo or RViz. It should contain the launch files — `display.launch.py` and `gazebo.launch.py` (exact names depend on your URDF/xacro setup and how you generated the files) — plus the robot's STL meshes, the URDF/xacro description, and `CMakeLists.txt`.

**Once these files are created, build the package:**
```bash
colcon build
```

**Install RViz and Gazebo dependencies:**
```bash
sudo apt install ros-humble-ros-gz          # for Gazebo
sudo apt install ros-humble-xacro
sudo apt install ros-humble-joint-state-publisher-gui
```

**Launch the simulation** (after installing and building):
```bash
ros2 launch <package_name> gazebo.launch.py   # for Gazebo
ros2 launch <package_name> display.launch.py  # for RViz
```
(The exact launch file names depend on what you created.)

## Components

Components let you load nodes at runtime.

**List component types:**
```bash
ros2 component types
```

<details>
<summary>Example output</summary>

```
ros_gz_bridge
  ros_gz_bridge::RosGzBridge
action_tutorials_cpp
  action_tutorials_cpp::FibonacciActionClient
  action_tutorials_cpp::FibonacciActionServer
logging_demo
  logging_demo::LoggerConfig
  logging_demo::LoggerUsage
tf2_ros
  tf2_ros::StaticTransformBroadcasterNode
teleop_twist_joy
  teleop_twist_joy::TeleopTwistJoy
demo_nodes_cpp_native
  demo_nodes_cpp_native::Talker
demo_nodes_cpp
  demo_nodes_cpp::OneOffTimerNode
  demo_nodes_cpp::ReuseTimerNode
  demo_nodes_cpp::ServerNode
  demo_nodes_cpp::ClientNode
  demo_nodes_cpp::ListParameters
  demo_nodes_cpp::ParameterBlackboard
  demo_nodes_cpp::SetAndGetParameters
  demo_nodes_cpp::ParameterEventsAsyncNode
  demo_nodes_cpp::EvenParameterNode
  demo_nodes_cpp::ContentFilteringPublisher
  demo_nodes_cpp::ContentFilteringSubscriber
  demo_nodes_cpp::Talker
  demo_nodes_cpp::LoanedMessageTalker
  demo_nodes_cpp::SerializedMessageTalker
  demo_nodes_cpp::Listener
  demo_nodes_cpp::SerializedMessageListener
  demo_nodes_cpp::ListenerBestEffort
quality_of_service_demo_cpp
  quality_of_service_demo::MessageLostListener
  quality_of_service_demo::MessageLostTalker
  quality_of_service_demo::QosOverridesListener
  quality_of_service_demo::QosOverridesTalker
robot_state_publisher
  robot_state_publisher::RobotStatePublisher
image_tools
  image_tools::Cam2Image
  image_tools::ShowImage
examples_rclcpp_minimal_subscriber
  WaitSetSubscriber
  StaticWaitSetSubscriber
  TimeTriggeredWaitSetSubscriber
depthimage_to_laserscan
  depthimage_to_laserscan::DepthImageToLaserScanROS
composition
  composition::Talker
  composition::Listener
  composition::NodeLikeListener
  composition::Server
  composition::Client
joy
  joy::Joy
  joy::GameController
```

</details>

## Reference

- [ROS 2 tutorial video](https://youtu.be/HJAE5Pk8Nyw?t=2208)
