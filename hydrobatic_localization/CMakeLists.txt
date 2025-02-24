cmake_minimum_required(VERSION 3.8)
project(hydrobatic_localization)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(sam_msgs REQUIRED)
find_package(smarc_msgs REQUIRED)
find_package (Eigen3 3.3 REQUIRED NO_MODULE)
find_package(tf2 REQUIRED)
find_package(tf2_ros REQUIRED)
find_package(tf2_geometry_msgs REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(GeographicLib REQUIRED)


# GTSAM stuff
find_package(GTSAMCMakeTools)
find_package(GTSAM REQUIRED)
include_directories(${GTSAM_INCLUDE_DIR})
#include the headers
include_directories(${PROJECT_SOURCE_DIR}/include/)
#include GeographicLib
include_directories(${GeographicLib_INCLUDE_DIRS})

# add_executable(hydrobatic_localization src/main.cpp src/BarometerFactor.cpp)
# ament_target_dependencies(hydrobatic_localization rclcpp sensor_msgs
#                            smarc_msgs std_msgs nav_msgs sam_msgs sam_msgs tf2 tf2_ros tf2_geometry_msgs geometry_msgs)
# target_link_libraries(hydrobatic_localization gtsam Eigen3::Eigen GeographicLib::GeographicLib)

add_executable(state_estimator src/state_estimator.cpp src/gtsam_graph.cpp src/BarometerFactor.cpp src/DvlFactor.cpp)
ament_target_dependencies(state_estimator
  rclcpp
  sensor_msgs
  std_msgs
  nav_msgs
  sam_msgs
  smarc_msgs
  tf2
  tf2_ros
  tf2_geometry_msgs
  geometry_msgs
)
target_link_libraries(state_estimator
  gtsam
  Eigen3::Eigen
  GeographicLib::GeographicLib
  )
install(TARGETS
  # hydrobatic_localization
  state_estimator
  DESTINATION lib/${PROJECT_NAME})

install(DIRECTORY include/
  DESTINATION include/
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

ament_package()
