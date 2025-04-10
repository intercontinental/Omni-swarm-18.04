cmake_minimum_required(VERSION 2.8.3)
project(swarm_loop)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS_RELEASE "-g -O3 -Wall -Wno-deprecated-declarations -Wno-format")
set(USE_TENSORRT off)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rosmsg
  rospy
  std_msgs
  cv_bridge
  swarm_msgs
  message_generation
  camera_models
  message_filters
  vins
)


SET("OpenCV_DIR"  "/usr/local/share/OpenCV/")
find_package(OpenCV 3.4 REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(lcm REQUIRED)
# find_package(Backward)
set(TENSORRT_ROOT $ENV{HOME}/source/TensorRT-7.1.3.4)

if (USE_TENSORRT)
  set(Torch_DIR "$ENV{HOME}/source/libtorch/share/cmake/Torch")
  find_package(Torch REQUIRED)

  include_directories("$ENV{HOME}/source/yolo-tensorrt/modules/")
  include_directories("$ENV{HOME}/source/TensorRT-7.1.3.4/include")

  link_directories(${TENSORRT_ROOT}/lib)
  link_directories("$ENV{HOME}/source/yolo-tensorrt/build/")
  find_package(CUDA)
  include_directories(${CUDA_INCLUDE_DIRS} ${TORCH_INCLUDE_DIRS})
  add_definitions("-D USE_TENSORRT")
endif()

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES loop_cnn
  CATKIN_DEPENDS std_msgs cv_bridge roscpp rosmsg rospy swarm_msgs
  #DEPENDS system_lib
)

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
  /usr/local/include/
  include
  ${catkin_INCLUDE_DIRS}
  ${EIGEN3_INCLUDE_DIR}
)

add_library(libswarm_loop
  src/loop_cam.cpp
  src/loop_detector.cpp
  src/loop_net.cpp
  src/loop_params.cpp
  src/swarm_loop.cpp
  src/loop_utils.cpp
)

add_library(${PROJECT_NAME}_nodelet
  src/swarm_loop_nodelet.cpp
)

add_executable(${PROJECT_NAME}_node
  src/swarm_loop_node.cpp
)

add_executable(${PROJECT_NAME}_spy
  src/swarm_loop_spy.cpp
)

add_executable(${PROJECT_NAME}_net_tester
  src/loop_network_tester.cpp
)

set_property(TARGET ${PROJECT_NAME}_nodelet PROPERTY CXX_STANDARD 14)
set_property(TARGET ${PROJECT_NAME}_node PROPERTY CXX_STANDARD 14)
set_property(TARGET libswarm_loop PROPERTY CXX_STANDARD 14)

if (USE_TENSORRT)
  cuda_add_library(loop_cnn
    src/superpoint_tensorrt.cpp
    src/tensorrt_generic.cpp
    src/mobilenetvlad_tensorrt.cpp
  )
  target_link_libraries(loop_cnn nvinfer nvinfer_plugin  detector opencv_dnn)

  add_executable(loop_tensorrt_test
    src/loop_tensorrt_test.cpp
  )
  target_link_libraries(loop_tensorrt_test
    loop_cnn
    dw
    ${TORCH_LIBRARIES}
    ${OpenCV_LIBRARIES}
    ${catkin_LIBRARIES}
    )
    set_property(TARGET loop_cnn PROPERTY CXX_STANDARD 14)
endif()

add_dependencies(${PROJECT_NAME}_nodelet
    ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

add_dependencies(${PROJECT_NAME}_spy
    ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

if (USE_TENSORRT)
  target_link_libraries(libswarm_loop
    ${catkin_LIBRARIES}
    ${OpenCV_LIBRARIES}
    ${TORCH_LIBRARIES}
    lcm
    faiss
    dw
    loop_cnn
  )
else()
  target_link_libraries(libswarm_loop
    ${catkin_LIBRARIES}
    ${OpenCV_LIBRARIES}
    ${TORCH_LIBRARIES}
    lcm
    faiss
    dw
    # loop_cnn
  )
endif()


target_link_libraries(${PROJECT_NAME}_nodelet
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${TORCH_LIBRARIES}
  lcm
  faiss
  dw
  libswarm_loop
)

target_link_libraries(${PROJECT_NAME}_node
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${TORCH_LIBRARIES}
  lcm
  dw
  libswarm_loop
)

target_link_libraries(${PROJECT_NAME}_net_tester
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
  ${TORCH_LIBRARIES}
  lcm
  dw
  libswarm_loop
)

target_link_libraries(${PROJECT_NAME}_spy
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
  lcm
  dw
  libswarm_loop
)
