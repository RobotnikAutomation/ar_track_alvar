cmake_minimum_required(VERSION 3.5)
project(ar_track_alvar)

find_package(ament_cmake REQUIRED)

find_package(OpenCV REQUIRED)
if(NOT DEFINED CMAKE_SUPPRESS_DEVELOPER_WARNINGS)
  set(CMAKE_SUPPRESS_DEVELOPER_WARNINGS 1 CACHE INTERNAL "No dev warnings")
endif()

find_package(sensor_msgs REQUIRED)
find_package(pcl_conversions REQUIRED)
find_package(tf2 REQUIRED)
find_package(tinyxml_vendor REQUIRED)

# Default to C++17
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 17)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  # About -Wno-sign-conversion: With Clang, -Wconversion implies -Wsign-conversion. There are a number of
  # implicit sign conversions in rclcpp and gtest.cc, see https://ci.ros2.org/job/ci_osx/9265/.
  # Hence disabling -Wsign-conversion for now until all those have eventually been fixed.
  # (from https://github.com/ros2/rclcpp/pull/1188#issuecomment-650229140)
  add_compile_options(-Wall -Wextra -Wconversion -Wno-sign-conversion -Wpedantic -Wnon-virtual-dtor -Woverloaded-virtual)
endif()


set(${PROJECT_NAME}_SRCS
  src/Bitset.cpp
  src/Camera.cpp
  src/CaptureDevice.cpp
  src/CaptureFactory.cpp
  src/CaptureFactory_unix.cpp
  src/ConnectedComponents.cpp
  src/CvTestbed.cpp
  src/DirectoryIterator.cpp
  src/DirectoryIterator_unix.cpp
  src/Draw.cpp
  src/FileFormatUtils.cpp
  src/Filter.cpp
  src/Kalman.cpp
  src/Line.cpp
  src/Marker.cpp
  src/MarkerDetector.cpp
  src/MultiMarker.cpp
  src/MultiMarkerBundle.cpp
  src/MultiMarkerInitializer.cpp
  src/Mutex.cpp
  src/Mutex_unix.cpp
  src/Optimization.cpp
  src/Plugin.cpp
  src/Plugin_unix.cpp
  src/Pose.cpp
  src/Rotation.cpp
  src/Threads.cpp
  src/Threads_unix.cpp
  src/Util.cpp
)

add_library(${PROJECT_NAME} SHARED ${${PROJECT_NAME}_SRCS})
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)
target_include_directories(${PROJECT_NAME} PUBLIC
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
  "$<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>"
  "$<INSTALL_INTERFACE:include/${PROJECT_NAME}>")

# Add OpenCV include directories
target_include_directories(${PROJECT_NAME} PUBLIC
  ${OpenCV_INCLUDE_DIRS}
)
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBRARIES} tinyxml)

ament_target_dependencies(${PROJECT_NAME}
  sensor_msgs
  pcl_conversions
  tf2
  tinyxml_vendor
)

install(
  TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}
  ARCHIVE DESTINATION lib
  LIBRARY DESTINATION lib
  RUNTIME DESTINATION bin
)

# Export old-style CMake variables
ament_export_include_directories("include/${PROJECT_NAME}")
ament_export_libraries(${PROJECT_NAME})

# Export modern CMake targets
ament_export_targets(${PROJECT_NAME})

ament_export_dependencies(
  OpenCV
  sensor_msgs
  pcl_conversions
  tf2
  tinyxml_vendor
)

ament_package()

install(
  DIRECTORY include/
  DESTINATION include/${PROJECT_NAME}
)
