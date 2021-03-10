# Kinect Telepresence
This project aims on achieving real-time telepresence of a person wearing
virtual reality HMD among people being remotely captured by a single RGB & depth
sensor (initially we target Azure Kinect DK).

It was originated by Computer Vision Group at Skolkovo Institute of Science and
Technology (Skoltech) in Fall of 2020.

Project members:

* Victor Lempitsky -- Research group leader, project leader
* Alexey Larionov
* Svetlana Gabdullina
* Alexander Nevarko
* Konstantin Soshin

The project consists of two additional repositories (submodules):
* «‎Capture side» (made with C++) is meant to be launched on a desktop, it:
    * captures a stream of RGB and depth data from a connected sensor (initially
      we target Azure Kinect DK)
    * preprocesses the data using machine learning to construct a 3D model of
      captured people and updates it in realtime
    * transmits the model and its updates over a network to the Immersion side
* «Immersion side» (made with Unity) is an application for a VR head-mounted device
  (initially we target Oculus Quest), it shows an Extended Reality (XR) scene
  with projected 3D models of captured people thus making a session of
  telepresence for the person wearing VR device.
# Build

As of now we don't have any prebuilt binaries for either Capture or Immersion
side, so you'll need to build them on your own.

Both submodules can be downloaded directly from this repository:

**NOTE: `immersion` submodule might be large due to big number of assets used as of now**

```bash
git clone --recursive https://github.com/laralex/kinect-telepresence.git
```
or alternatively you can clone each repository separately:
*  [Capture side repository](https://github.com/laralex/kinect-telepresence-capture)
```bash
# equivalent ways
git clone --recurse-submodules="capture" https://github.com/laralex/kinect-telepresence.git
git clone --recursive https://github.com/laralex/kinect-telepresence-capture.git
```
* [Immersion side repository](https://github.com/laralex/kinect-telepresence-immersion)
```bash
# equivalent ways
git clone --recurse-submodules="immersion" https://github.com/laralex/kinect-telepresence.git
git clone --recursive https://github.com/laralex/kinect-telepresence-immersion.git
```

Should you choose cloning this (main) repository, you don't have to use `--recursive` flag, unless you need to contribute to or compile the corresponding `capture` or `immersion` submodule to add you own submodules/unrelated code/changes, you may clone without `--recursive` flag. 

## Building Capture side [(repository)](https://github.com/laralex/kinect-telepresence-capture)
Host desktop environment requirements:
1) for using Azure Kinect DK you'll need a USB 3.0 port, and at least either OS:
    * Windows 10 April 2018 (Version 1803, OS Build 17134) release (x64)
    * Linux Ubuntu 18.04 (x64), with a GPU driver that uses OpenGLv4.4 or a
      later version
2) for 30 fps realtime machine learning processing — a middle-range/high-end
   GPU and atleast 4Gb of RAM
3) for 30 fps realtime 3D models streaming — atleast 25 Mbps Internet
   connection (on the immersion side the WiFi shall comply)
#### Docker container (internally runs Ubuntu 18.04)
1) Open a terminal and proceed to `capture/` folder
2) Run 
```bash
docker build . -t ktp
docker run --privileged ktp
```
*\*the container has to run in privileged mode to access USB ports and thus access
   connected sensor device*

#### Manual build
1) As a prerequisite you'll have to install Azure Kinect SDK using their
   [repository](https://github.com/microsoft/Azure-Kinect-Sensor-SDK). As of now
   they provide prebuilt binaries as MSI packages for Windows and Debian
   packages for Linux, but you can build the repository by yourself
2) **(on Windows)** Add the path to the SDK in `K4A_DIR` environment variable. If you used an MSI
   installer, then it probably looks like `C:\Program Files\Azure Kinect SDK
   <version>`
3) Install CMake (version atleast 3.10)
4) For your ease, install `ninja` generator for C++ (if you prefer `make` or `cmake --build`, change following steps by yourself)
5) Clone Capture side submodule (see above)
6) Open a terminal and proceed to Capture side folder (contains Dockerfile, CMakeLists.txt)
7) Run
```bash
mkdir build
cd build
cmake .. -DCMAKE_BUILD_TYPE=Release -GNinja
ninja
```
6) Find the executables under `capture/build/release`, e.g. `ktp_capture_daemon.exe`
   which is a console application that runs indefinetely, or `all_tests.exe` that
   runs unit tests to check project integrity
## Building Immersion side [(repository)](https://github.com/laralex/kinect-telepresence-immersion)

1) Open the project under `immersion/unity/` folder in Unity 2019.4.15f1
2) Build it for a VR device.
3) Send the binaries to the VR device and launch them
