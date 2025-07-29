# AR Track Alvar

## Deprecated Package Notice

This package has been ported to jazzy by Robotnik Automation. It is no longer actively maintained by the original authors.

The underlying OpenCV Implementation has been upgraded, but it is unclear how worthwhile it is to keep maintaining this package.
Users should consider using the tag tracking [libraries](https://docs.opencv.org/4.2.0/d5/dae/tutorial_aruco_detection.html) natively supported by OpenCV as they handle a greater variety of tags, and provide similar functionality.

## Overview

This package is a ROS 2 wrapper to expose a library for tracking AR tags using Alvar.

## Usage

In CMakeLists.txt, you can find the following lines:

```cmake
find_package(ar_track_alvar REQUIRED)
ament_target_dependencies(<your_target>
  ar_track_alvar
  cv_bridge
  geometry_msgs
  image_transport
)
```
This will link the `ar_track_alvar` library to your target, allowing you to use its functionalities.

Here is an example of how to use the `ar_track_alvar` library in your code:

```cpp
#include <ar_track_alvar/Alvar.h>
#include <ar_track_alvar/MarkerDetector.h>
#include <ar_track_alvar/Camera.h>

using MarkerDetector = alvar::MarkerDetector<alvar::MarkerData>;
std::unique_ptr<alvar::Camera> camera_ = std::make_unique<alvar::Camera>();
std::unique_ptr<MarkerDetector> marker_detector_ = std::make_unique<MarkerDetector>();

// Peform perception
camera_->SetCameraInfo(camera_info);
camera_->getCamInfo_ = true;

cv_bridge::CvImagePtr cv_image{ cv_bridge::toCvCopy(image, image->encoding) };
marker_detector_->Detect(
    cv_image->image, camera_.get(), true, false, max_new_marker_error, max_track_error, alvar::CVSEQ, true
);
auto const& markers{ marker_detector_->markers };
```
This code initializes the camera and marker detector, sets the camera information, and performs detection on the provided image. The detected markers can then be accessed through the `markers` variable.
