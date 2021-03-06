cmake_minimum_required(VERSION 2.8.3)
project(submap2d)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  nav_msgs
  openslam_gmapping
  roscpp
  tf
  gmapping
)

## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system signals)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"
##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder
# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here
# generate_messages(
#   DEPENDENCIES
#   nav_msgs
# )

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
#  INCLUDE_DIRS include
#  LIBRARIES submap2d
#  CATKIN_DEPENDS nav_msgs roscpp tf
#  DEPENDS system_lib
)

###########
## Build ##
###########
# find_package(gmapping REQUIRED)
set(ROS_INCLUDE_DIRS /opt/ros/hydro/include)
set(gmapping_INCLUDE_DIRS ${ROS_INCLUDE_DIRS}/gmapping)
message("gmapping_INCLUDE_DIRS : ${gmapping_INCLUDE_DIRS}")
# message("gmapping_LIBRARY_DIRS : ${gmapping_LIBRARY_DIRS}")
include_directories(${gmapping_INCLUDE_DIRS})

###### G2O ######
# G2O
SET(G2O_DIR ${PROJECT_SOURCE_DIR}/3rdproject/g2o)
SET(G2O_LIB ${G2O_DIR}/lib)
SET(G2O_LIBS g2o_core g2o_parser g2o_incremental g2o_csparse_extension g2o_solver_csparse g2o_solver_dense g2o_solver_pcg g2o_stuff g2o_types_icp  g2o_types_sim3 g2o_types_slam2d g2o_types_slam3d g2o_interactive)
include_directories(${G2O_DIR})
link_directories(${G2O_LIB})

##### EIGEN #####
set(EIGEN_DIR /usr/include/eigen3)
include_directories(${EIGEN_DIR})

##### SuiteSparse #####
SET(SSPARSE_DIR /usr/include/suitesparse)
SET(SSPARSE_LIBS cholmod)
include_directories(${SSPARSE_DIR})

##### CSparse #####
set(CSPARSE_LIBS cxsparse)

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(
  ${catkin_INCLUDE_DIRS}
  src
  src/icp-mrpt
)

## Declare a cpp library
# add_library(submap2d
#   src/${PROJECT_NAME}/submap2d.cpp
# )

## Declare a cpp executable
# add_executable(submap2d_node src/submap2d_node.cpp)
# set(SRCS src/lsodo_listener.cpp src/main.cpp)

file(GLOB SRCS "src/*.cpp")
# message("SRCS: ${SRCS}")
# set(SRCS src/main.cpp )
message("CMAKE_CURRENT_SOURCE_DIR : ${CMAKE_CURRENT_SOURCE_DIR}")
link_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/icp-mrpt)
set(ICP_LIBS zhicp)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
# add_dependencies(submap2d_node submap2d_generate_messages_cpp)

add_executable(submap2d_node ${SRCS})
## Specify libraries to link a library or executable target against
target_link_libraries(submap2d_node
   ${catkin_LIBRARIES}
   ${gmapping_LIBRARIES}
   ${ICP_LIBS}
   ${G2O_LIBS}
   ${SSPARSE_LIBS}
   ${CSPARSE_LIBS}
 )

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
# install(TARGETS submap2d submap2d_node
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
# catkin_add_gtest(${PROJECT_NAME}-test test/test_submap2d.cpp)
# if(TARGET ${PROJECT_NAME}-test)
#   target_link_libraries(${PROJECT_NAME}-test ${PROJECT_NAME})
# endif()

## Add folders to be run by python nosetests
# catkin_add_nosetests(test)
