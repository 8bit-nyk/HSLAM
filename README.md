# HSLAM (Hybrid-SLAM)

This project builds and runs monocular Visual SLAM system which integrates a hybrid approach utilizing both direct and indirect methods.

This project mainly runs on datasets.

For an implementation of HSLAM with live camera feed (utilizing ROS) and and out-of-the-box SLAM system leveraging docker refer to this repository [HSLAM_Docker](https://github.com/8bit-nyk/hslam_ros_docker) .

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
### Dependencies:
**[NOTE]: The correct versions will be installed when building project with the provided `build.sh` script. (See Section 4 for more details)**

The project is dependent on specific versions of the following libraries:
- C++11 or C++0x Compiler: We use the new thread and chrono functionalities of C++11.
- [Pangolin](https://github.com/stevenlovegrove/Pangolin) for visualization and user interface. (Included in Thirdparty folder)
- OpenCV 3.4.6 (Included in Thirdparty folder)
- DBoW2 and g2o (Included in Thirdparty folder)



# 3. Building HSLAM library and dependencies

1. Clone the repository:
```
git clone https://github.com/8bit-nyk/HSLAM.git
```
Note: We are cloning a specific branch with **-b** argument

2. Navigate to the project directory:
```
cd <your_working_directory>/HSLAM
```

Before building the main project we need to build the thirdparty dependancies


3. Navigate to the Thirdparty directory:
```
cd Thirdparty
```
4. We provide a script `build.sh` to download and install the  specific versions of the dependency libraries needed. Execute:

  ```
  chmod +x build.sh && ./build.sh
  ```

5. Configuring and building main project:

    In terminal navigate back to HSLAM main project directory
    ```
    cd ..
    or
    cd ~/<your_directory>/HSLAM
    ```
    
    Run the following command to create the build directory
    ```
    mkdir -p build 
    ```
    Navigate into the create build directory:
    ```
    cd build 
    ```
    Configure cmake:
    ```
    cmake .. -DCMAKE_BUILD_TYPE=RelwithDebInfo 
    ``` 
    Build the Project:
    ```
    make -j $(nproc)
    ```

# 4. Run Examples
1. Download one of the sequences from the [Monocular Visual Odometry Dataset provided by TUM](https://cvg.cit.tum.de/data/datasets/mono-dataset)


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





