#
# (C) Copyright 2016 Johns Hopkins University (JHU), All Rights Reserved.
#
# --- begin cisst license - do not edit ---
#
# This software is provided "as is" under an open source license, with
# no warranty.  The complete license can be found in license.txt and
# http://www.cisst.org/cisst/license.txt.
#
# --- end cisst license ---

cmake_minimum_required (VERSION 2.8.3)

project (universal_robot_ros)

## find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package (catkin REQUIRED
              COMPONENTS
              cisst_ros_bridge
              geometry_msgs
              roscpp
              std_msgs
              roslib
              )

# find cisst and make sure the required libraries have been compiled
set (REQUIRED_CISST_LIBRARIES
     cisstCommon
     cisstCommonQt
     cisstVector
     cisstVectorQt
     cisstOSAbstraction
     cisstMultiTask
     cisstMultiTaskQt
     cisstParameterTypes
     cisstQt)

find_package (cisst REQUIRED ${REQUIRED_CISST_LIBRARIES})

if (cisst_FOUND_AS_REQUIRED)

  include (${CISST_USE_FILE})

  # include_directories(include)
  include_directories (${catkin_INCLUDE_DIRS})

  file (MAKE_DIRECTORY "${CATKIN_DEVEL_PREFIX}/include")

  catkin_package (INCLUDE_DIRS "${CATKIN_DEVEL_PREFIX}/include"
                  CATKIN_DEPENDS cisst_ros_bridge geometry_msgs roscpp std_msgs)

  # sawUniversalRobot has been compiled within cisst, we should find it automatically
  find_package (sawUniversalRobot 1.0.0)

  if (sawUniversalRobot_FOUND)

    include_directories (${CATKIN_DEVEL_PREFIX}/include
                         ${sawUniversalRobot_INCLUDE_DIR})

    link_directories (${sawUniversalRobot_LIBRARY_DIR})

    add_executable (universal_robot src/universal_robot.cpp)
    target_link_libraries (universal_robot
                           ${sawUniversalRobot_LIBRARIES}
                           ${catkin_LIBRARIES})
    cisst_target_link_libraries (universal_robot ${REQUIRED_CISST_LIBRARIES})

  else (sawUniversalRobot_FOUND)
    message ("Information: code in ${CMAKE_CURRENT_SOURCE_DIR} will not be compiled, it requires sawUniversalRobot")
  endif (sawUniversalRobot_FOUND)

endif (cisst_FOUND_AS_REQUIRED)
