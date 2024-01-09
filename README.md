# HSLAM (Hybrid-SLAM)

This branch holds clear instructions on how to resolve all dependency issues and build & run Hybrid-SLAM on datasets.
For an implementation of HSLAM with live camera feed (utilizing ROS) refer to repository flsam_ros_docker.


### Related Publications:

[HSLAM]Younes, G. (2021). A Unified Hybrid Formulation for Visual SLAM (Doctoral dissertation). **[PDF] (https://scholarworks.aub.edu.lb/bitstream/handle/10938/22253/YounesGeorges_2021.pdf?sequence=5)**

# 1. License

HSLAM is released under [GPLv3 license]

# 2. Prerequisites
We have tested the library in **Ubuntu 18.04** and **20.04**, but it should be easy to compile in other platforms. A powerful computer (e.g. i7) will ensure real-time performance and provide more stable and accurate results.

# 3. Dependencies:
The project is dependent on specific versions of the following libraries. 
The correct versions will be installed when building project with the provided `build.sh` script. (See Section 4 for more details)

## C++11 or C++0x Compiler
We use the new thread and chrono functionalities of C++11.

## Pangolin
We use [Pangolin](https://github.com/stevenlovegrove/Pangolin) for visualization and user interface. Dowload and install instructions can be found at: https://github.com/stevenlovegrove/Pangolin.

## OpenCV
We use [OpenCV](http://opencv.org) to manipulate images and features. Dowload and install instructions can be found at: http://opencv.org. **Required at least 3.4.6**.

## Eigen3
Required by g2o (see below). Download and install instructions can be found at: http://eigen.tuxfamily.org. **Required at least 3.1.0**.

## DBoW2 and g2o (Included in Thirdparty folder)
We use modified versions of the [DBoW2](https://github.com/dorian3d/DBoW2) library to perform place recognition and [g2o](https://github.com/RainerKuemmerle/g2o) library to perform non-linear optimizations. Both modified libraries (which are BSD) are included in the *Thirdparty* folder.


# 4. Building HSLAM library and dependencies

1. Clone the repository:
```
$ git clone -b phd_code https://github.com/AUBVRL/FSLAM.git
```
Note: We are cloning a specific branch with **-b** argument

2. Navigate to project repository:
```
$ cd <your_working_directory>/FSLAM-phd_code
```

3. We provide a script `build.sh` to download and install the specific versions of the dependency libraries needed. Execute:

```
$ chmod +x build.sh
$ ./build.sh
```
After the script finishes we will have to modify and build some thirdparty libraries.

4. Building and configuring third party libraries:
    Open terminal in project directory and navigate to g2o folder located in Thirdparty directory:
    ```
    ~/FSLAM-phd_code$ cd /Thirdparty/g2o
    ```
    Create build directory and navigate to it:
    ```
    ~/Thirdparty/g2o$ mkdir build && cd build
    ```
    Configure CMake flags:
    ```
    ~/Thirdparty/g2o/build$ ccmake ..
    ```
    This will open the CMake GUI in the terminal as per the screenshot below:
    [TODO:insert screenshot]

    
    Press [c] to configure and [t] to toggle advanced mode ON.

    Once in advanced mode:
        Locate the five parameters with **CMAKE_CXX_FLAGS** and **append** the following string to all **CMAKE_CXX_FLAGS** parameters:
        
        -march=native -std=gnu++0x
    

    Set the following flags as indicated below:
     ```
    - BUILD_WITH_MARCH_NATIVE=ON
    - CMAKE_BUILD_TYPE : RelwithDebInfo
    - CMAKE_INSTALL_PREFIX: <your_directory>/FSLAM-phd_code/Thirdparty/CompiledLibs
    - CMAKE_RELWITHDEBINFO_POSTFIX : ""
    - CMAKE_MINSIZEREL_POSTFIX : ""
    - G2O_BUILD_APPS : OFF
    - G2O_BUILD_EXAMPLES : OFF
    - G2O_USE_OPENMP : OFF
    ```
    Press [c] again to configure then press [g] to generate.

    In the terminal run the following commands:
    ```
    ~/../Thirdparty/g2o/build$ make -j4
    ~/../Thirdparty/g2o/build$ make install
    ```
We need to re-build the main project.

5. Configuring and re-building main project:

    In terminal navigate to FSLAM main project diirectory
    ```
    $ cd ~/<your_directory>/FSLAM-phd_code
    ```
    Run the following command
    ```
    ~/../FSLAM-phd_code$ ccmake .
    ```
    This will open the CMake GUI.

    Press [c] to configure and [t] to toggle advanced mode ON.

    Once in advanced mode:
        Locate the five parameters with **CMAKE_CXX_FLAGS** and **append** the following string to all parameters:
        
        -march=native -std=gnu++0x

    Set the build type flag
    ``` 
    CMAKE_BUILD_TYPE : RelwithDebInfo
    ```
    Press [c] again to configure then press [g] to generate.
    In the terminal run the following commands:
    ```
    ~/../FSLAM-phd_code$ make -j4
    ```

# 5. Run Examples





