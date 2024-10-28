# `BEVFusion-ROS-TensorRT-CPP`

This repository contains source code and models for BEVFusion online real-time inference using CUDA, TensorRT & ROS.

**Supports ROS2**. please switch to the [galactic-devel branch](https://github.com/linClubs/BEVFusion-ROS-TensorRT/tree/galactic-devel)


![](configs/cuda-bevfusion.gif)


# 1 Dependency

+ **`ubuntu-20.04,noetic,cuda-11.3, cudnn-8.6.0, TensorRT-8.5`**

1. By default, noetic, cuda, and cudnn are downloaded, and the TensorRT source code downloaded.

2. `ros` Dependency

~~~python
# 1. Building a ros workspace
mkdir -p bevfusion_ws/src

# 2. Enter the bevfusion_ws/src directory and pull the source code
cd bevfusion_ws/src
git clone https://github.com/linClubs/BEVFusion-ROS-TensorRT.git

# 3. Enter bevfusion_ws workspace one-click installation feature package requires ros dependency
cd .. 
rosdep install -r -y --from-paths src --ignore-src --rosdistro $ROS_DISTRO
~~~

3. [Model Download Reference](https://github.com/linClubs/BEVFusion-ROS-TensorRT/blob/main/model/readme.md)

4. [Model Export Reference](https://github.com/NVIDIA-AI-IOT/Lidar_AI_Solution/blob/master/CUDA-BEVFusion/qat/README.md)

+ Amend`./tool/environment. The path to cuda Tensorrt cudnn in sh, run. /tool/build_trt_engine.sh` Generate TensorRt inference model

~~~python
./tool/build_trt_engine.sh
~~~


5. `ros` Pack up

+ `bevfusion` Officially provided trained 'nuscenes' model parameters

+ rosbagData Conversion Reference[`nuscenes2rosbag`](https://github.com/linClubs/nuscenes2rosbag)

+ `nuscenes` Parameters between sensors are given without calibration.

If you need to connect a real sensor for scene testing, you need to complete **training** and **calibration** in advance.

[Sensor Calibration Reference](https://github.com/linClubs/Calibration-Is-All-You-Need)


# 2 Compile and run

1.Modifications required before compilation in CMakeLists.txt, modify the paths for TensorRT and CUDA as follows.

~~~python
...
# cuda
set(CUDA_TOOLKIT_ROOT_DIR /usr/local/cuda-11.3) # CUDA Modify this line
set(CUDA_INSTALL_TARGET_DIR targets/x86_64-linux)
set(CUDA_INCLUDE_DIRS ${CUDA_TOOLKIT_ROOT_DIR}/${CUDA_INSTALL_TARGET_DIR}/include)
set(CUDA_LIBS ${CUDA_TOOLKIT_ROOT_DIR}/${CUDA_INSTALL_TARGET_DIR}/lib)

# TENSORRT
set(TensorRT_ROOT /home/lin/software/TensorRT-8.5.3.1)  # TensorRT Modify this line
# set(TensorRT_ROOT ~/share/TensorRT-8.5.3.1)           
set(TensorRT_INCLUDE_DIRS ${TensorRT_ROOT}/include)
set(TensorRT_LIBS ${TensorRT_ROOT}/lib/)
...
~~~

2. Compilation and Execution

+ `bevfusion_node.launch `modify` model_name` and `precision` parameter values

`model_name: resnet50/resnet50int8/swint`
`precision:  fp16/int8`
`swint + int8` Mode does not work.

~~~python
# 1. Compile
catkin_make

# 2. Source Work space
source devel/setup.bash

# 3. Run bevfusion_node
roslaunch bevfusion bevfusion_node.launch

# 4. Play data set
 rosbag play 103.bag 
~~~

3. Run error: The file tool/simhei.ttf cannot be found. Please perform a global search using the keywords tool/simhei.ttf or UseFont.

Please navigate to /src/common/visualize.cu and modify the value of UseFont to the correct path for simhei.ttf.

---

# References

+ [bevfusion](https://github.com/mit-han-lab/bevfusion)
+ [Lidar_AI_Solution](https://github.com/NVIDIA-AI-IOT/Lidar_AI_Solution)
