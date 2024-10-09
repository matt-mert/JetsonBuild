

# Object Tracking and AI with Jetson Nano

The system built with NVIDIA Jetson Nano is covered in this documentation.

Instructions regarding the system, its components, and the libraries are provided.

## Required Components

> NVIDIA Jetson Nano

> MicroSD Card (min 32G)

> MicroSD-to-USB converter

> Raspberry Pi Camera V2

> Wireless NIC for 8265AC

> Host computer with Ubuntu 18.04

> External Display

> Keyboard/Mouse

> Female-to-Female jumper x2 (*)

## Initial Setup

The first and simplest way to build Jetson Nano is to flash JetPack image to MicroSD card. This procedure is explained in detail at:

https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

In this project, Jetson Nano Developer Kit with JetPack 4.6 is used.

Note: In general, jumper the J48 Power Select Header pins and connect a power supply to J25 power jack (barrel jack). MicroUSB port is not only used to connect Jetson Nano to the host computer, but also does not provide enough power for many applications.

However, this procedure may fail due to possible defects in other components of Jetson Nano other than the SD card itself; then the installation must be carried out by NVIDIA SDK Manager on a host computer with Ubuntu 18.04 installed.

Regarding the NVIDIA SDK Manager:

As stated above, J25 power jack must be used as the power supplying port. After that, upon connecting Jetson Nano to the host computer (with Ubuntu 18.04 and with NVIDIA SDK Manager open), the SDK Manager software must detect the module and ask for which version the module is (2GB, 4GB etc.). Consequently, the software guides the user to the necessary steps to flash Jetson Nano with the desired JetPack version and additional SDK (including CUDA, DeepStream etc.) if selected.

If SDK Manager software does not recognize Jetson Nano module, then the force recovery mode approach must be followed. The steps for recovering the module is as follows:

> Jumper the Force Recovery pins (3 and 4) on J40 Button Header with a female-to-female jumper.

(Further explanation of this step: There are various pins beneath the SD Card slot in Jetson Nano 4GB B01 version. These are the "button pins", which means that, for example, if we connect FC REC (Force Recovery) pin to GND (Ground) pin momentarily, it corresponds to pressing the force recovery button momentarily.)

> Disconnect and reconnect the power cable.

> Jetson Nano now has booted with force recovery mode, jumper can be plugged out.

> SDK Manager software should now be seeing Jetson Nano module and can flash.

## Installation of Required Software

First, required SDK must be installed via SDK Manager. SDK Manager software guides the user with a user-friendly interface. If necessary, enable the installation of DeepStream SDK as well.

Next, since Jetson Nano module comes with OpenCV installed, OpenCV-Contrib-Python package must be installed and this procedure includes uninstalling the currently installed OpenCV.

```bash

$ sudo apt-get purge *libopencv*

$ sudo apt-get update

$ sudo apt-get install build-essential cmake git curl

$ sudo apt-get install libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev

$ sudo apt-get install libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

$ sudo apt-get install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libdc1394-22-dev

$ sudo apt-get install libv4l-dev v4l-utils qv4l2 v4l2ucp

```

Now, open a folder named Workspace in Home directory of Jetson Nano. Navigate to this folder in terminal and in this folder execute the following commands:

```bash

$ curl -L https://github.com/opencv/opencv/archive/4.5.0.zip -o opencv-4.5.0.zip

$ curl -L https://github.com/opencv/opencv_contrib/archive/4.5.0.zip -o opencv_contrib-4.5.0.zip

$ unzip opencv-4.5.0.zip

$ unzip opencv_contrib-4.5.0.zip

$ cd opencv-4.5.0/

$ mkdir release

$ cd release/

$ cmake -D WITH_CUDA=ON -D WITH_CUDNN=ON -D CUDA_ARCH_BIN"5.3,6.2,7.2" -D CUDA_ARCH_PTX="" -D OPENCV_GENERATE_PKGCONFIG=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib-4.5.0/modules -D WITH_GSTREAMER=ON -D WITH_LIBV4L=ON -D BUILD_opencv_python2=ON -D BUILD_opencv_python3=ON -D BUILD_TESTS=OFF -D BUILD_EXAMPLES=OFF -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local ..

$ make -j$(nproc)

$ sudo make install

```

Note: Regarding the command "make -j$(nproc)", if the installation freezes at some point, unplug the power cable from Jetson Nano and re-plug to reboot Jetson Nano, navigate to release folder in opencv-4.5.0 directory (in Home/Workspace), and execute "make -j1" command which tells Jetson Nano to only use 1 processing unit for this operation; on the other hand, -j$(nproc) tells to use the maximum available processing units. Also note that the installation usually takes a long time to complete.

Congratulations! OpenCV-Contrib-Python has been successfully built.

Next, create a new folder on preferably Desktop, and clone the following repositories in this folder. To do so, execute the following commands in this new folder in terminal.

```bash

$ git clone https://github.com/JetsonHacksNano/CSI-Camera.git
$ git clone --recursive https://github.com/dusty-nv/jetson-inference

```

Since python3 is going to be used, pip3 needs to be installed at this point.

```bash

$ sudo apt-get update

$ sudo apt-get install python3-pip

```

Now using CSI-Camera scripts, camera can be tested. Navigate to CSI-Camera folder and simply do:

```bash

$ python3 simple_camera.py

```

Having cloned the jetson-inference module above, now navigate to jetson-inference folder (For instance; mert@mert-desktop:~/SomeFolder/SomeOtherFolder/jetson-inference$) and follow these steps to install jetson-inference:

```bash

$ mkdir build

$ cd build

$ cmake ../

$ make -j$(nproc)

$ sudo make install

$ sudo ldconfig

```

Various python packages can be installed depending on the application at this point. For example;

```bash

sudo pip3 install pyserial

```

## Troubleshooting

When installed manually, NVIDIA SDKs may have version incompatibility, which can be checked with;

```bash

$ sudo apt-cache policy nvidia-l4t-*

```

## To Do

Tracking module files with examples are going to be added to the repository.

## Useful Links

https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit

https://developer.nvidia.com/drive/sdk-manager

https://github.com/dusty-nv/jetson-inference

https://www.youtube.com/@paulmcwhorter

## Author

matt-mert, Mert Kucukakinci
