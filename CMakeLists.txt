cmake_minimum_required(VERSION 3.8)
project(dsd_control_demo)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# set the same behavior for windows as it is on linux
set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)

# find dependencies
set(THIS_PACKAGE_INCLUDE_DEPENDS
  hardware_interface
  pluginlib
  rclcpp
  rclcpp_lifecycle
)

# find dependencies
find_package(backward_ros REQUIRED)
find_package(ament_cmake REQUIRED)
foreach(Dependency IN ITEMS ${THIS_PACKAGE_INCLUDE_DEPENDS})
  find_package(${Dependency} REQUIRED)
endforeach()

## COMPILE
add_library(
  dsd_control_demo
  SHARED
  hardware/dsdbot_system.cpp
)
target_compile_features(dsd_control_demo PUBLIC cxx_std_17)
target_include_directories(dsd_control_demo PUBLIC
$<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/hardware/include>
$<INSTALL_INTERFACE:include/dsd_control_demo>
)
ament_target_dependencies(
  dsd_control_demo PUBLIC
  ${THIS_PACKAGE_INCLUDE_DEPENDS}
)

# Export hardware plugins
pluginlib_export_plugin_description_file(hardware_interface dsd_control_demo.xml)

# INSTALL
install(
  DIRECTORY hardware/include/
  DESTINATION include/dsd_control_demo
)
install(
  DIRECTORY description/launch description/ros2_control description/urdf
  DESTINATION share/dsd_control_demo
)
install(
  DIRECTORY bringup/launch bringup/config
  DESTINATION share/dsd_control_demo
)
install(TARGETS dsd_control_demo
  EXPORT export_dsd_control_demo
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # comment the line when a copyright and license is added to all source files
  set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # comment the line when this package is in a git repo and when
  # a copyright and license is added to all source files
  set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

## EXPORTS
ament_export_targets(export_dsd_control_demo HAS_LIBRARY_TARGET)
ament_export_dependencies(${THIS_PACKAGE_INCLUDE_DEPENDS})
ament_package()
