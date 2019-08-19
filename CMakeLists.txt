cmake_minimum_required(VERSION 2.8.3)
project(se2lam)

add_subdirectory(Thirdparty/DBoW2)


list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/CMakeModules/)


# c++ 11 support for g2o, multi-thread and shared_ptr
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x -Wall")
SET(G2O_INCLUDE_DIR /home/te-6000-16/g2o-20160424_git/install/include)
SET(G2O_LIBRARY_DIR /home/te-6000-16/g2o-20160424_git/install/lib)

SET(G2O_ROOT /home/te-6000-16/g2o-20160424_git/g2o)
SET(G2O_DIR /home/te-6000-16/g2o-20160424_git/install)
SET(G2O_FOUND 1)
find_package(G2O REQUIRED)
find_package(Eigen REQUIRED)
find_package(OpenCV REQUIRED)
find_package(CSparse REQUIRED)
find_package(Cholmod REQUIRED)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  nav_msgs
  roscpp
  rospy
  tf
  roslib
  cv_bridge
  cmake_modules
  image_transport
  message_generation
)
#add_message_files(
#  FILES
#  SyncOdoImg.msg
#)

#generate_messages(
#  DEPENDENCIES
#  std_msgs
#  sensor_msgs
#  geometry_msgs
#)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES se2lam
#   CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy tf
#  DEPENDS Eigen OpenCV Boost

#   CATKIN_DEPENDS message_runtime
)


include_directories(
  ${catkin_INCLUDE_DIRS}
  
)
list(APPEND LINK_LIBS
  ${catkin_LIBRARIES}
)
# if do not use ros
#find_package(Pangolin REQUIRED)
#include_directories(
#  ${Pangolin_INCLUDE_DIRS}
#)
#list(APPEND LINK_LIBS
#  ${Pangolin_LIBRARIES}
#)

include_directories(
  include/se2lam/
  ${CMAKE_CURRENT_SOURCE_DIR}
  "/usr/include/eigen3/"
  ${OpenCV_INCLUDE_DIRS}
  ${CSPARSE_INCLUDE_DIR}
  ${Cholmod_INCLUDE_DIR}
  ${G2O_INCLUDE_DIR}
)

list(APPEND LINK_LIBS
  ${OpenCV_LIBS}
)

SET(G2O_LIBS
cholmod
cxsparse
${G2O_LIBRARY_DIR}/libg2o_core.so
${G2O_LIBRARY_DIR}/libg2o_solver_cholmod.so
${G2O_LIBRARY_DIR}/libg2o_solver_dense.so
${G2O_LIBRARY_DIR}/libg2o_stuff.so
${G2O_LIBRARY_DIR}/libg2o_solver_csparse.so
${G2O_LIBRARY_DIR}/libg2o_csparse_extension.so
${G2O_LIBRARY_DIR}/libg2o_types_sba.so
${G2O_LIBRARY_DIR}/libg2o_types_sim3.so
${G2O_LIBRARY_DIR}/libg2o_types_slam3d.so
${G2O_LIBRARY_DIR}/libg2o_types_slam3d.so
${G2O_LIBRARY_DIR}/libg2o_interface.so
${G2O_LIBRARY_DIR}/libg2o_types_icp.so
${G2O_LIBRARY_DIR}/libg2o_solver_slam2d_linear.so
${G2O_LIBRARY_DIR}/libg2o_types_slam2d.so
${G2O_LIBRARY_DIR}/libg2o_solver_structure_only.so
${G2O_LIBRARY_DIR}/libg2o_types_sclam2d.so
${G2O_LIBRARY_DIR}/libg2o_parser.so
${G2O_LIBRARY_DIR}/libg2o_solver_pcg.so
${G2O_LIBRARY_DIR}/libg2o_types_data.so
${G2O_LIBRARY_DIR}/libg2o_types_sim3.so
)

## Build a lib
FILE(GLOB_RECURSE IncFiles "include/se2lam/*.h")
aux_source_directory(src/. DIR_SRCS)
add_library(se2lam ${DIR_SRCS} ${IncFiles})

## Specify libraries to link a library or executable target against
target_link_libraries(se2lam
${LINK_LIBS}
${G2O_LIBS}
DBoW2se2lam
)

## Build an exe
add_executable(test_vn test/test_vn.cpp)

## Specify libraries to link a library or executable target against
target_link_libraries(test_vn
se2lam
${LINK_LIBS}
${G2O_LIBS}
#${CMAKE_CURRENT_SOURCE_DIR}/Thirdparty/DBoW2/lib/libDBoW2.so
)


add_executable(datapub test/datapub.cpp)
target_link_libraries(datapub
se2lam
${LINK_LIBS}
${G2O_LIBS}

)

add_executable(imgview test/imgview.cpp)
target_link_libraries(imgview
se2lam
${LINK_LIBS}
${G2O_LIBS}

)


# Build a subscriber test for vn dataset
add_executable(test_ros test/test_ros.cpp)
target_link_libraries(test_ros
se2lam
${LINK_LIBS}
${G2O_LIBS}

)
