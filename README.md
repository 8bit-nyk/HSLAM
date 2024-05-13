# HSLAM (Hybrid-SLAM)

This branch holds clear instructions on how to resolve all dependency issues and build & run Hybrid-SLAM on datasets.
For an implementation of HSLAM with live camera feed (utilizing ROS) refer to this repository [HSLAM_Docker](https://github.com/8bit-nyk/hslam_ros_docker) .

### Related Publications:
[A Unified Hybrid Formulation for Visual SLAM](https://scholarworks.aub.edu.lb/bitstream/handle/10938/22253/YounesGeorges_2021.pdf?sequence=5) (Doctoral dissertation), Younes, G. (2021).

[H-SLAM: Hybrid Direct-Indirect Visual SLAM](https://arxiv.org/pdf/2306.07363)  Younes, G. et al (2023).

Please cite the paper if used in an academic context.
```
@article{younes2023h,
  title={H-SLAM: Hybrid Direct-Indirect Visual SLAM},
  author={Younes, Georges and Khalil, Douaa and Zelek, John and Asmar, Daniel},
  journal={arXiv preprint arXiv:2306.07363},
  year={2023}
}

```

# 1. License

HSLAM is released under [GPLv3 license]

# 2. Prerequisites
We have tested the library in **Ubuntu 18.04** and **20.04**, but it should be easy to compile in other platforms. A powerful computer (e.g. i7) will ensure real-time performance and provide more stable and accurate results.

# 3. Dependencies:
The project is dependent on specific versions of the following libraries:

**The correct versions will be installed when building project with the provided `build.sh` script. (See Section 4 for more details)**

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
$ git clone https://github.com/8bit-nyk/HSLAM.git
```
Note: We are cloning a specific branch with **-b** argument

2. Navigate to project repository:
```
$ cd <your_working_directory>/HSLAM
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
    ~/HSLAM$ cd /Thirdparty/g2o
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
    ![hslam-cmake-tutorial](https://github.com/8bit-nyk/HSLAM/assets/49674476/7833e0cf-baf9-4640-96a2-3f02dc7474e5)

    
    Press [c] to configure and [t] to toggle advanced mode ON.

    Once in advanced mode:
        Locate the five parameters with **CMAKE_CXX_FLAGS** and _**APPEND**_ the following string to all **CMAKE_CXX_FLAGS** parameters:
        
        -march=native -std=gnu++0x
    

    Set the following flags as indicated below:
     ```
    - BUILD_WITH_MARCH_NATIVE=ON
    - CMAKE_BUILD_TYPE : RelwithDebInfo
    - CMAKE_INSTALL_PREFIX: <your_directory>/HSLAM/Thirdparty/CompiledLibs
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

    In terminal navigate to HSLAM main project diirectory
    ```
    $ cd ~/<your_directory>/HSLAM
    ```
    Run the following command
    ```
    ~/../HSLAM$ ccmake .
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
    ~/../HSLAM$ make -j4
    ```

# 5. Run Examples
1. Download one of the sequences from the [Monocular Visual ODometry Dataset provided by TUM](https://cvg.cit.tum.de/data/datasets/mono-dataset)


2. Navigate to the build directory of the project
  ```
  cd ~/HSLAM/build/
  ```

3.  Run the following commands:
   
  Note that the following commands assume you downloaded **sequence_30**  you will need to adjust the files paths according to your local setup

If there are NO photmetric calibration parameters available run:

  ```
 ./bin/HSLAM \
 -files=PATH/TO/DATASET/sequence_30/images.zip \
 -calib=PATH/TO/DATASET/sequence_30/camera.txt \
 -vocabPath=PATH/TO/HSLAM/misc/orbvoc.dbow3 \
 -mode=1 

  ```

If photometric calinration is avaliable run:
```
  ./bin/HSLAM \
 -files=PATH/TO/DATASET/sequence_30/images.zip \
 -calib=PATH/TO/DATASET/sequence_30/camera.txt \
 -vocabPath=/PATH/TO/HSLAM/misc/orbvoc.dbow3 \
 -mode=0 \
 -gamma=/home/nykvm/datasets/sequence_30/pcalib.txt \
 -vignette=/home/nykvm/datasets/sequence_30/vignette.png \
```

  You should get an output similair to screenshot below showing the SLAM process:
  ![hslam-output-screenshot](https://github.com/8bit-nyk/HSLAM/assets/49674476/ab3a1c94-8f38-41da-855d-c25566f720c4)





