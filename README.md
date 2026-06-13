# Mobile Manipulator (ROS + Gazebo)

A ROS package for a small differential-drive mobile manipulator with a 2-DOF arm and a parallel-jaw gripper, simulated in Gazebo.

The base uses a skid-steer drive (four continuous wheels), and the arm is mounted on a pedestal at the front of the chassis. Joint control is handled by `ros_control` (`JointTrajectoryController`), and the gripper fingers are coupled through a mimic-joint plugin.

## Demo

<video src="https://github.com/leenalmousa/mobile_manipulator_combined/raw/main/media/demo.mp4" controls width="720"></video>

> If the embedded player doesn't load, [click here to watch the demo](https://github.com/leenalmousa/mobile_manipulator_combined/raw/main/media/demo.mp4).

## Features

- Skid-steer mobile base driven by `libgazebo_ros_skid_steer_drive` (publishes on `/cmd_vel`).
- 2-DOF arm (`joint1` waist rotation, `joint2` tilt) with position-controlled trajectories.
- Parallel-jaw gripper with a mimic joint so both fingers move symmetrically from a single command.
- Self-contained launch file that spawns the robot in an empty Gazebo world and starts the controllers.

## Repository layout

```
mobile_manipulator_combined/
├── config/
│   └── combined_control.yaml      # ros_control PID gains and trajectory constraints
├── launch/
│   └── mobile_manipulator.launch  # spawns Gazebo + robot + controllers
├── urdf/
│   └── mobile_manipulator.urdf    # robot description (links, joints, plugins)
├── media/
│   └── demo.mp4                   # demo recording
├── CMakeLists.txt
├── package.xml
├── LICENSE
└── README.md
```

## Requirements

- Ubuntu 20.04 with **ROS Noetic** (or Ubuntu 18.04 with **ROS Melodic**)
- Gazebo 11 (Noetic) / Gazebo 9 (Melodic)
- ROS packages: `gazebo_ros`, `gazebo_plugins`, `controller_manager`, `joint_state_controller`, `position_controllers`, `joint_trajectory_controller`
- [`roboticsgroup_upatras_gazebo_plugins`](https://github.com/roboticsgroup/roboticsgroup_upatras_gazebo_plugins) for the gripper mimic-joint plugin

Install the ROS dependencies on Noetic:

```bash
sudo apt install \
  ros-noetic-gazebo-ros ros-noetic-gazebo-plugins \
  ros-noetic-controller-manager ros-noetic-joint-state-controller \
  ros-noetic-position-controllers ros-noetic-joint-trajectory-controller
```

## Build

Clone into the `src/` folder of a catkin workspace and build:

```bash
cd ~/catkin_ws/src
git clone https://github.com/leenalmousa/mobile_manipulator_combined.git
cd ~/catkin_ws
catkin_make
source devel/setup.bash
```

## Run

Launch the simulation:

```bash
roslaunch mobile_manipulator_combined mobile_manipulator.launch
```

Optional arguments:

| Argument | Default | Description                       |
|----------|---------|-----------------------------------|
| `x`      | `0.0`   | Spawn X position (m)              |
| `y`      | `0.0`   | Spawn Y position (m)              |
| `z`      | `0.0`   | Spawn Z position (m)              |
| `gui`    | `true`  | Show the Gazebo client GUI        |
| `paused` | `false` | Start the world paused            |

## Driving the robot

Move the base by publishing `geometry_msgs/Twist` to `/cmd_vel`:

```bash
rostopic pub -r 10 /cmd_vel geometry_msgs/Twist \
  '{linear: {x: 0.2}, angular: {z: 0.0}}'
```

Move the arm by sending a trajectory to the controller:

```bash
rostopic pub /arm_controller/command \
  trajectory_msgs/JointTrajectory \
  '{joint_names: ["joint1", "joint2", "gripper_joint"],
    points: [{positions: [0.5, -0.3, 0.008],
              time_from_start: {secs: 2}}]}'
```

The gripper finger range is `0` (closed) to `0.00825 m` (open); the second finger mirrors it automatically.

## Topics

| Topic                          | Type                                | Direction |
|--------------------------------|-------------------------------------|-----------|
| `/cmd_vel`                     | `geometry_msgs/Twist`               | subscribe |
| `/joint_states`                | `sensor_msgs/JointState`            | publish   |
| `/arm_controller/command`      | `trajectory_msgs/JointTrajectory`   | subscribe |
| `/arm_controller/state`        | `control_msgs/JointTrajectoryControllerState` | publish |

## Author

**Leen Almousa** &mdash; [github.com/leenalmousa](https://github.com/leenalmousa)

## License

Released under the [MIT License](LICENSE).
