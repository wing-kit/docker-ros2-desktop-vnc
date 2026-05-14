# docker-ros2-desktop-vnc

[![Publish to Registry (Humble)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-humble.yml/badge.svg)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-humble.yml)
[![Publish to Registry (Iron)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-iron.yml/badge.svg)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-iron.yml)
[![Publish to Registry (Jazzy)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-jazzy.yml/badge.svg)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-jazzy.yml)
[![Publish to Registry (Rolling)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-rolling.yml/badge.svg)](https://github.com/Tiryoh/docker-ros2-desktop-vnc/actions/workflows/deploy-rolling.yml)

[![Docker Automated build](https://img.shields.io/docker/automated/tiryoh/ros2-desktop-vnc)](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc)
[![](https://img.shields.io/docker/pulls/tiryoh/ros2-desktop-vnc.svg)](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc)
[![Contributor Covenant](https://img.shields.io/badge/Contributor%20Covenant-2.0-4baaaa.svg)](code_of_conduct.md)

Dockerfiles to provide HTML5 VNC interface to access Ubuntu Desktop + ROS2, based on [AtsushiSaito/docker-ubuntu-sweb](https://github.com/AtsushiSaito/docker-ubuntu-sweb)

ROS 1 version: https://github.com/Tiryoh/docker-ros-desktop-vnc

![animation](https://github.com/user-attachments/assets/137a5272-f6a3-490f-8bfc-168d082ac949)

There are official ROS 2 Docker images provided by Open Robotics.  
https://github.com/osrf/docker_images/blob/master/README.md#official-library

このツールの詳細については以下の記事で紹介しています。  
The details of these tools are written in Japanese on this page.  
https://memoteki.net/archives/2955

## Quick Start

Run the docker container and access with port `6080`.  
Change the `shm-size` value depending on the situation.

__NOTE__: `--security-opt seccomp=unconfined` flag is required to launch humble image. See https://github.com/Tiryoh/docker-ros2-desktop-vnc/pull/56.

```sh
docker run -p 6080:80 -p 2222:22 --security-opt seccomp=unconfined --shm-size=512m ghcr.io/tiryoh/ros2-desktop-vnc:humble
```

Browse http://127.0.0.1:6080/.

To develop your own packages, create a workspace on the host and mount it with `-v` (see [Developing Your Own Packages](#developing-your-own-packages) below).

![default desktop](https://github.com/user-attachments/assets/29ff479f-de54-4032-995d-d1be244ff4e7)

## macOS

[Docker Desktop for Mac](https://docs.docker.com/desktop/setup/install/mac-install/) is required.

### Apple Silicon

Pre-built images for `linux/arm64` are available. Docker automatically selects the matching architecture, so no extra flags are needed on Apple Silicon Macs.

To run:

```sh
docker run -p 6080:80 -p 2222:22 --security-opt seccomp=unconfined --shm-size=512m ghcr.io/tiryoh/ros2-desktop-vnc:humble
```

To build natively on Apple Silicon:

```sh
cd humble && docker build -t tiryoh/ros2-desktop-vnc:humble .
```

To force the `amd64` image on Apple Silicon (e.g. for compatibility testing), add `--platform linux/amd64`:

```sh
docker run -p 6080:80 -p 2222:22 --security-opt seccomp=unconfined --platform linux/amd64 --shm-size=2048m tiryoh/ros2-desktop-vnc:humble-amd64
```

To cross-build for `linux/amd64` from Apple Silicon, use `docker buildx` with the `--platform` flag:

```sh
cd humble && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:humble-amd64 .
```

### Intel Mac

Use the same commands as the Quick Start and Build sections below. To run the `amd64` image explicitly:

```sh
docker run -p 6080:80 -p 2222:22 --security-opt seccomp=unconfined --platform linux/amd64 --shm-size=2048m tiryoh/ros2-desktop-vnc:humble-amd64
```

To build on Intel Mac:

```sh
cd humble && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:humble-amd64 .
```

## Build

### Humble (with AIC dependencies)

Build the full desktop image with additional dependencies (redis-server, python3-tk, htop, tmux, nano, Python redis package):

```sh
cd humble && docker build -f Dockerfile -t tiryoh/ros2-desktop-vnc:humble-aic .
```

Build the lightweight variant with additional ROS2 packages for neo_simulation2:

```sh
cd humble && docker build -f Dockerfile.with-deps -t tiryoh/ros2-desktop-vnc:humble-aic .
```

### Other ROS2 distributions

* dashing (deprecated)
```sh
cd dashing && docker build -t tiryoh/ros2-desktop-vnc:dashing .
```

* eloquent (deprecated)
```sh
cd eloquent && docker build -t tiryoh/ros2-desktop-vnc:eloquent .
```

* foxy (deprecated)
```sh
cd foxy && docker build -t tiryoh/ros2-desktop-vnc:foxy .
```

* galactic (deprecated)
```sh
cd galactic && docker build -t tiryoh/ros2-desktop-vnc:galactic .
```

* humble
```sh
# using "docker build"
cd humble && docker build -t tiryoh/ros2-desktop-vnc:humble .
# using "docker buildx" (amd64)
cd humble && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:humble-amd64 .
# using "docker buildx" (arm64)
cd humble && docker buildx build --platform=linux/arm64 --progress=plain -t tiryoh/ros2-desktop-vnc:humble-arm64 .
```

* iron
```sh
# using "docker build"
cd iron && docker build -t tiryoh/ros2-desktop-vnc:iron .
# using "docker buildx" (amd64)
cd iron && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:iron-amd64 .
# using "docker buildx" (arm64)
cd iron && docker buildx build --platform=linux/arm64 --progress=plain -t tiryoh/ros2-desktop-vnc:iron-arm64 .
```

* jazzy
```sh
# using "docker build"
cd jazzy && docker build -t tiryoh/ros2-desktop-vnc:jazzy .
# using "docker buildx" (amd64)
cd jazzy && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:jazzy-amd64 .
# using "docker buildx" (arm64)
cd jazzy && docker buildx build --platform=linux/arm64 --progress=plain -t tiryoh/ros2-desktop-vnc:jazzy-arm64 .
```

* rolling
```sh
# using "docker build"
cd rolling && docker build -t tiryoh/ros2-desktop-vnc:rolling .
# using "docker buildx" (amd64)
cd rolling && docker buildx build --platform=linux/amd64 --progress=plain -t tiryoh/ros2-desktop-vnc:rolling-amd64 .
# using "docker buildx" (arm64)
cd rolling && docker buildx build --platform=linux/arm64 --progress=plain -t tiryoh/ros2-desktop-vnc:rolling-arm64 .
```

## TurtleBot3 Simulation

TurtleBot3 simulation packages are pre-installed in the following images.

| Distro | Architectures | Simulator |
|--------|--------------|-----------|
| `humble` | `amd64` only | Gazebo Classic |
| `jazzy`  | `amd64`, `arm64` | Gazebo (gz sim) |

Pre-installed packages include:
- `turtlebot3`, `turtlebot3-msgs`, `turtlebot3-simulations`, `turtlebot3-gazebo`, `turtlebot3-teleop`
- `navigation2`, `nav2-bringup`
- `cartographer`, `cartographer-ros`

### Quick Start

Open a terminal inside the container and launch the TurtleBot3 world:

```sh
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

In another terminal, run keyboard teleoperation:

```sh
ros2 run turtlebot3_teleop teleop_keyboard
```

To launch autonomous navigation with SLAM:

```sh
ros2 launch nav2_bringup tb3_simulation_launch.py slam:=True
```

### Changing the Robot Model

The default model is `burger`. You can switch to `waffle` or `waffle_pi` by setting the environment variable before launching:

```sh
export TURTLEBOT3_MODEL=waffle
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py
```

> **Note**: On Humble, TurtleBot3 simulation is only available on `amd64` because it depends on Gazebo Classic, which is not pre-installed on the `arm64` image.

## Developing Your Own Packages

To develop your own ROS 2 packages, create a workspace on your host machine and mount it into the container. This keeps your source code on the host while the container provides the build environment.

```sh
mkdir -p ~/ros2_ws/src
docker run -p 6080:80 -p 2222:22 -v ~/ros2_ws:/home/ubuntu/ros2_ws --security-opt seccomp=unconfined --shm-size=2048m ghcr.io/tiryoh/ros2-desktop-vnc:humble
```

Inside the container, the workspace is available at `/home/ubuntu/ros2_ws`. You can build packages with `colcon build` and they persist on your host.

## VS Code Remote Development

An SSH server is included so you can use [VS Code Remote - SSH](https://code.visualstudio.com/docs/remote/ssh) for a native IDE experience.

1. Start the container with SSH port forwarded (e.g., `-p 2222:22`).
2. In VS Code, run `Remote-SSH: Connect to Host...` and enter:
   ```
   ssh ubuntu@localhost -p 2222
   ```
3. Use the password `ubuntu` (or the `PASSWORD` you set).

Once connected, open `/home/ubuntu/ros2_ws` and install the ROS extension for syntax highlighting and IntelliSense.

## Docker tags on hub.docker.com

* ~~[`dashing`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=dashing) which is based on [`dashing/Dockerfile`](./dashing/Dockerfile)~~ deprecated
* ~~[`eloquent`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=eloquent) which is based on [`eloquent/Dockerfile`](./eloquent/Dockerfile)~~ deprecated
* [`foxy`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=foxy) which is based on [`foxy/Dockerfile`](./foxy/Dockerfile)
* ~~[`galactic`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=galactic) which is based on [`galactic/Dockerfile`](./galactic/Dockerfile)~~ deprecated
* [`humble`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=humble), [`latest`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=latest) which is based on [`humble/Dockerfile`](./humble/Dockerfile)
* [`iron`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=iron) which is based on [`iron/Dockerfile`](./iron/Dockerfile)
* [`jazzy`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=jazzy) which is based on [`jazzy/Dockerfile`](./jazzy/Dockerfile)
* [`rolling`](https://hub.docker.com/r/tiryoh/ros2-desktop-vnc/tags?page=1&name=rolling) which is based on [`rolling/Dockerfile`](./rolling/Dockerfile)

Docker tags and build logs are listed on this page.  
https://github.com/Tiryoh/docker-ros2-desktop-vnc/wiki

## Related projects

* https://github.com/atinfinity/nvidia-egl-desktop-ros2
  * Dockerfile to use ROS 2 on Xfce Desktop container with NVIDIA GPU support via VNC/[Selkies](https://github.com/selkies-project/selkies-gstreamer)(Full desktop streaming with WebRTC)
* https://github.com/fcwu/docker-ubuntu-vnc-desktop
  * Dockerfile to access Ubuntu Xfce/LXDE/LxQT desktop environment via web VNC interface
* https://github.com/AtsushiSaito/docker-ubuntu-sweb
  * Dockerfile to access Ubuntu MATE desktop environment via web VNC interface

## License

This repository is released under the Apache License 2.0, see [LICENSE](./LICENSE).  
Unless attributed otherwise, everything in this repository is under the Apache License 2.0.

```
Copyright 2020-2025 Tiryoh <tiryoh@gmail.com>

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

### Acknowledgements

* This Dockerfile is based on [AtsushiSaito/docker-ubuntu-sweb](https://github.com/AtsushiSaito/docker-ubuntu-sweb), licensed under the [Apache License 2.0](https://github.com/AtsushiSaito/docker-ubuntu-sweb/blob/5e7ba8571d2f4d1e4fca0c1527d090c20f7f5e90/LICENSE).
* This Dockerfile is based on [fcwu/ubuntu-desktop-lxde-vnc](https://github.com/fcwu/docker-ubuntu-vnc-desktop), licensed under the [Apache License 2.0](https://github.com/fcwu/docker-ubuntu-vnc-desktop/blob/60f9ae18e71e9fabbfb23f67b212e64ab72c206e/LICENSE).

## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=Tiryoh/docker-ros2-desktop-vnc,Tiryoh/docker-ros-desktop-vnc&type=Date)](https://star-history.com/#Tiryoh/docker-ros2-desktop-vnc&Tiryoh/docker-ros-desktop-vnc&Date)
