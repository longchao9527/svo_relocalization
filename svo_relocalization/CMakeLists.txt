cmake_minimum_required(VERSION 2.8.3)
project(svo_relocalization)

set(CMAKE_BUILD_TYPE Debug)

# add suport for std::shared_ptr
IF(CMAKE_COMPILER_IS_GNUCC)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
ELSE()
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
ENDIF()

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  roscpp
  vikit_common
  vikit_ros
  opengv 
  # To be removed
  svo
  svo_ros
)

## System dependencies are found with CMake's conventions
FIND_PACKAGE(OpenCV REQUIRED)
FIND_PACKAGE(Eigen REQUIRED)
FIND_PACKAGE(Sophus REQUIRED)
pkg_search_module(YAMLCPP REQUIRED yaml-cpp)


###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
  LIBRARIES svo_relocalization
  CATKIN_DEPENDS roscpp
  DEPENDS Eigen OpenCV Sophus
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
  include
  ${Eigen_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${Sophus_INCLUDE_DIRS}
  ${YAMLCPP_INCLUDE_DIRS}
  ${catkin_INCLUDE_DIRS}
)

LIST(APPEND MY_LINK_LIBS
  ${OpenCV_LIBRARIES}
  ${Sophus_LIBRARIES}
  ${Boost_LIBRARIES}
  ${YAMLCPP_LIBRARIES}
  ${catkin_LIBRARIES}
)

# Declare a cpp library
add_library(svo_relocalization
  ./src/img_align_se2.cpp
  #./src/klein_murray_relocalizer.cpp
  ./src/se2_align_se3.cpp
  ./src/esm_relpos_finder.cpp
  ./src/cc_place_finder.cpp
  ./src/multiple_relocalizer.cpp
  ./src/5pt_relpos_finder.cpp
  ./src/3pt_relpos_finder.cpp
  ./src/feature_detector.cpp
  ./src/naive_place_finder.cpp
  ./src/fern.cpp
  ./src/fern_classifier.cpp
  ./src/yaml_parse.cpp
  ./src/svo_reloc_utils.cpp
  ./src/ferns_relocalizer.cpp
)
target_link_libraries(svo_relocalization
  ${MY_LINK_LIBS}
)

# Declare a cpp executable
add_executable(test_aligment src/test/test_aligment.cpp)
target_link_libraries(test_aligment svo_relocalization)

add_executable(test_aligment2 src/test/test_aligment2.cpp)
target_link_libraries(test_aligment2 svo_relocalization)

add_executable(test_relocalizer src/test/test_relocalization.cpp)
target_link_libraries(test_relocalizer svo_relocalization)

add_executable(test_confusion_matrix src/test/test_confusion_matrix.cpp)
target_link_libraries(test_confusion_matrix svo_relocalization)

add_executable(test_feature_detector src/test/test_feature_detector.cpp)
target_link_libraries(test_feature_detector svo_relocalization)

add_executable(test_ferns src/test/test_ferns.cpp)
target_link_libraries(test_ferns svo_relocalization)


#To be removed
add_executable(svo_node ./src/svo_relocalizer_node.cpp)
target_link_libraries(svo_node svo_relocalization svo)

add_executable(svo_node_ferns ./src/svo_relocalizer_node_ferns.cpp)
target_link_libraries(svo_node_ferns svo_relocalization svo)


## Specify libraries to link a library or executable target against
# target_link_libraries(nano_slam_relocalization_node
#   ${catkin_LIBRARIES}
# )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark executables and/or libraries for installation
# install(TARGETS nano_slam_relocalization nano_slam_relocalization_node
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )

## Mark other files for installation (e.g. launch and bag files, etc.)
# install(FILES
#   # myfile1
#   # myfile2
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

#############
## Testing ##
#############

## Add gtest based cpp test target and link libraries
# catkin_add_gtest(${PROJECT_NAME}-test test/test_nano_slam_relocalization.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
