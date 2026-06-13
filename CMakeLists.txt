cmake_minimum_required(VERSION 3.0.2)
project(mobile_manipulator_combined)

find_package(catkin REQUIRED COMPONENTS
  controller_manager
  gazebo_plugins
  gazebo_ros
  roscpp
  rospy
  std_msgs
)

catkin_package(
  CATKIN_DEPENDS
    controller_manager
    gazebo_plugins
    gazebo_ros
    roscpp
    rospy
    std_msgs
)

include_directories(
  ${catkin_INCLUDE_DIRS}
)

install(DIRECTORY config launch urdf
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
