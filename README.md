# Docker Isaac Sim

[<img src="https://img.shields.io/badge/dockerhub-image-important.svg?logo=docker">](https://hub.docker.com/r/j3soon/isaac-sim-pip/tags)

Unofficial minimal dockerfile for Isaac Sim.

This is often useful in scenarios when you are using a custom base image and want to install Isaac Sim in it.

Prerequisites:
- [NVIDIA Driver](https://ubuntu.com/server/docs/nvidia-drivers-installation)
- [Docker](https://docs.docker.com/engine/install/ubuntu/)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

## Pip Install

> Note that pip install Isaac Sim is still [in experimental stage](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html). Personally, I have used this method in [j3soon/ros2-essentials](https://github.com/j3soon/ros2-essentials) and have not encountered any issues with it yet.

Build or pull image:

```sh
# build the preferred version
docker build -f Dockerfile_isaacsim_4_2_pip -t j3soon/isaac-sim-pip:4.2 .
docker build -f Dockerfile_isaacsim_4_1_pip -t j3soon/isaac-sim-pip:4.1 .
docker build -f Dockerfile_isaacsim_4_0_pip -t j3soon/isaac-sim-pip:4.0 .
# or pull the preferred version
docker pull j3soon/isaac-sim-pip:4.2
docker pull j3soon/isaac-sim-pip:4.1
docker pull j3soon/isaac-sim-pip:4.0
```

```sh
xhost +local:docker
docker run --rm -it --runtime=nvidia --gpus all --network=host \
  -v ~/docker/isaac-sim-pip/cache/kit:/usr/local/lib/python3.10/site-packages/omni/cache:rw \
  -v ~/docker/isaac-sim-pip/cache/ov:/root/.cache/ov:rw \
  -v ~/docker/isaac-sim-pip/cache/pip:/root/.cache/pip:rw \
  -v ~/docker/isaac-sim-pip/cache/glcache:/root/.cache/nvidia/GLCache:rw \
  -v ~/docker/isaac-sim-pip/cache/computecache:/root/.nv/ComputeCache:rw \
  -v ~/docker/isaac-sim-pip/logs:/root/.nvidia-omniverse/logs:rw \
  -v ~/docker/isaac-sim-pip/data:/root/.local/share/ov/data:rw \
  -v ~/docker/isaac-sim-pip/documents:/root/Documents:rw \
  -v $(pwd):/workspace \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v $HOME/.Xauthority:/root/.Xauthority \
  -v /dev/shm:/dev/shm \
  j3soon/isaac-sim-pip:4.2
# in the container
# For Isaac Sim 4.0 to 4.2, run:
isaacsim omni.isaac.sim
# # For Isaac Sim 4.5, run:
# isaacsim isaacsim.exp.full
```

> The slight difference in cache directories between the official Docker image and the pip-installed version is observed by comparing the logs of the Isaac Sim instance in both environments.

To test the ros2 bridge in Isaac Sim, click `Isaac Utils > Common Omnigraphs > ROS2 Clock` in the menu and click `OK`. Click the play button, and run the following in a new terminal:

```sh
docker run --rm -it --network=host -v /dev/shm:/dev/shm osrf/ros:humble-desktop-full bash
# in the container
ros2 topic list
ros2 topic echo /clock
```

References:
- [Python Environment Installation \| Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html)
- [`isaacsim` \| PyPI](https://pypi.org/project/isaacsim/#history)

> As a side note, [a binary installer](https://docs.isaacsim.omniverse.nvidia.com/4.5.0/installation/install_workstation.html) is provided since Isaac Sim 4.5.0. This may also be used instead of pip install. However, I have not explored this option yet.

## Official Docker Images

For using the official Isaac Sim docker images, please follow [the official guide](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_container.html#container-deployment), the commands should be something like below.

Headless mode:

```sh
docker pull nvcr.io/nvidia/isaac-sim:4.5.0
docker run --name isaac-sim --entrypoint bash -it --runtime=nvidia --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
  -e "PRIVACY_CONSENT=Y" \
  -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
  -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
  -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
  -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
  -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
  -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
  -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
  -v ~/docker/isaac-sim/documents:/root/Documents:rw \
  nvcr.io/nvidia/isaac-sim:4.5.0
# in the container
./runheadless.sh -v
```

GUI mode:

```sh
docker pull nvcr.io/nvidia/isaac-sim:4.5.0
xhost +local:docker
docker run --name isaac-sim --entrypoint bash -it --runtime=nvidia --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
  -e "PRIVACY_CONSENT=Y" \
  -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
  -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
  -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
  -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
  -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
  -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
  -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
  -v ~/docker/isaac-sim/documents:/root/Documents:rw \
  -v $(pwd):/workspace \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v $HOME/.Xauthority:/root/.Xauthority \
  nvcr.io/nvidia/isaac-sim:4.5.0
# in the container
./runapp.sh
```

References:
- [Container Installation - Isaac Sim Documentation](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_container.html)
- [Isaac Sim \| NVIDIA NGC](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/isaac-sim/tags)
