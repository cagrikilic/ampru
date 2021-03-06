cmake_minimum_required(VERSION 2.8.3)
project(ampru_base)

find_package(
  catkin
  REQUIRED
  COMPONENTS
  roslaunch
  roscpp
  roslint
  serial
  controller_manager
  hardware_interface
  rosserial_arduino
  rosserial_client
  ampru_msgs
)

rosserial_generate_ros_lib(
  PACKAGE rosserial_arduino
  SCRIPT make_libraries.py
)

rosserial_configure_client(
  DIRECTORY firmware
  TOOLCHAIN_FILE ${ROSSERIAL_ARDUINO_TOOLCHAIN}
)

rosserial_add_client_target(firmware ampru_wheel_base ALL)
rosserial_add_client_target(firmware ampru_wheel_base-upload)

catkin_package(
  INCLUDE_DIRS include
  CATKIN_DEPENDS
  roscpp
  serial
  hardware_interface
  ampru_msgs
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
)

set_directory_properties(
  PROPERTIES
  COMPILE_OPTIONS "-std=c++11;"
)

add_executable(
  ampru_node
  src/ampru_base.cpp
  src/ampru_hardware.cpp
  src/message.cpp
  src/serial_port.cpp
)

target_link_libraries(
  ampru_node
  ${catkin_LIBRARIES}
)

add_dependencies(
  ampru_node
  ampru_msgs_generate_messages_cpp
)

roslint_cpp(
  src/ampru_base.cpp
  src/ampru_hardware.cpp
  src/message.cpp
  src/serial_port.cpp
  include/ampru_base/ampru_hardware.h
  include/ampru_base/message.h
  include/ampru_base/serial_port.h
)

roslaunch_add_file_check(launch)

install(
  TARGETS ampru_node
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)
install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(
  DIRECTORY launch
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
