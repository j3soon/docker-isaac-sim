# Docker Isaac Sim

Unofficial minimal dockerfile for Isaac Sim.

This is often useful in scenarios when you are using a custom base image and want to install Isaac Sim in it. For an example, one may want to use a custom base image for ROS2 and install Isaac Sim in it.

| Name | Docker Image | Docker Hub Link |
|------|--------------|-----------------|
| Pip | `j3soon/isaac-sim-pip` | [![Docker Image](https://img.shields.io/badge/dockerhub-image-important.svg?logo=docker)](https://hub.docker.com/r/j3soon/isaac-sim-pip/tags) |
| Binary | `j3soon/isaac-sim-bin` | [![Docker Image](https://img.shields.io/badge/dockerhub-image-important.svg?logo=docker)](https://hub.docker.com/r/j3soon/isaac-sim-bin/tags) |

## Prerequisites

- [NVIDIA Driver](https://ubuntu.com/server/docs/nvidia-drivers-installation)
- [Docker](https://docs.docker.com/engine/install/ubuntu/)
- [NVIDIA Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html)

## Isaac Sim

### Official Isaac Sim Images

Before diving into the unofficial docker images, let's first see how to use the official images.

For using the official Isaac Sim docker images, please follow [the official guide](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_container.html#container-deployment), the commands should be something like below.

Headless mode:

```sh
docker pull nvcr.io/nvidia/isaac-sim:4.5.0
docker run --name isaac-sim --entrypoint bash -it --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
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
./runheadless.sh
```

> For using WebRTC streaming, wait until the streaming is ready, you should see something like this:
> 
> ```
> Isaac Sim Full Streaming App is loaded.
> ```
> 
> Download and use the [Isaac Sim WebRTC Streaming Client](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html#isaac-sim-latest-release) as mentioned in [the official guide](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/manual_livestream_clients.html#isaac-sim-setup-livestream-webrtc).
> 
> > For WebRTC streaming in Isaac Sim 4.2 and earlier, run the following command:
> >
> > ```sh
> > # in the container
> > ./runheadless.webrtc.sh
> > ```
> > Wait until the streaming is ready, you should see something like this:
> > ```
> > Isaac Sim Headless WebRTC App is loaded.
> > ```
> >
> > Visit <http://127.0.0.1:8211/streaming/webrtc-demo/?server=127.0.0.1>.
> >
> > Note that the URL must contain the IP address of the host machine, not the host/domain name.

GUI mode:

```sh
docker pull nvcr.io/nvidia/isaac-sim:4.5.0
xhost +local:docker
docker run --name isaac-sim --entrypoint bash -it --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
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

### Isaac Sim Image with Pip Install

> Note that pip install Isaac Sim is still [in experimental stage](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html). Personally, I have used this method in [j3soon/ros2-essentials](https://github.com/j3soon/ros2-essentials) and have not encountered any issues with it yet.

Build or pull image:

```sh
# build the preferred version
docker build -f Dockerfile_isaacsim_4_5_pip -t j3soon/isaac-sim-pip:4.5 .
docker build -f Dockerfile_isaacsim_4_2_pip -t j3soon/isaac-sim-pip:4.2 .
docker build -f Dockerfile_isaacsim_4_1_pip -t j3soon/isaac-sim-pip:4.1 .
docker build -f Dockerfile_isaacsim_4_0_pip -t j3soon/isaac-sim-pip:4.0 .
# or pull the preferred version
docker pull j3soon/isaac-sim-pip:4.5
docker pull j3soon/isaac-sim-pip:4.2
docker pull j3soon/isaac-sim-pip:4.1
docker pull j3soon/isaac-sim-pip:4.0
```

```sh
xhost +local:docker
ISAAC_SIM_VERSION=4.2
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
  j3soon/isaac-sim-pip:${ISAAC_SIM_VERSION}
# in the container
# For Isaac Sim 4.0 to 4.2, run:
isaacsim omni.isaac.sim
# or WebRTC streaming:
isaacsim omni.isaac.sim.headless.webrtc --no-window
```

> For Isaac Sim 4.5, run the following instead:
> 
> ```sh
> isaacsim isaacsim.exp.full
> # or WebRTC streaming:
> isaacsim isaacsim.exp.full.streaming --no-window
> ```

For WebRTC streaming usage, see [the official Docker images section](#official-docker-images) below.

> The slight difference in cache directories between the official Docker image and the pip-installed version is observed by comparing the logs of the Isaac Sim instance in both environments.

To test the ros2 bridge in Isaac Sim, click `Isaac Utils > Common Omnigraphs > ROS2 Clock` in the menu and click `OK`. Click the play button, and run the following in a new terminal:

```sh
docker run --rm -it --network=host -v /dev/shm:/dev/shm osrf/ros:humble-desktop-full bash
# in the container
ros2 topic list
ros2 topic echo /clock
```

> Please note that the ROS2 bridge is not working in Isaac Sim 4.5.0 yet. We're currently working on this issue.

References:

- [Python Environment Installation \| Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/install_python.html)
- [`isaacsim` \| PyPI](https://pypi.org/project/isaacsim/#history)

### Isaac Sim Image with Binary Install

Starting from Isaac Sim 4.5.0, the Isaac Sim binary installation is supported.

Build or pull image:

```sh
# build the preferred version
docker build -f Dockerfile_isaacsim_5_0_bin -t j3soon/isaac-sim-bin:5.0 .
docker build -f Dockerfile_isaacsim_5_0_bin_user -t j3soon/isaac-sim-bin:5.0-user .
docker build -f Dockerfile_isaacsim_4_5_bin -t j3soon/isaac-sim-bin:4.5 .
docker build -f Dockerfile_isaacsim_4_5_bin_user -t j3soon/isaac-sim-bin:4.5-user .
# or pull the preferred version
docker pull j3soon/isaac-sim-bin:5.0
docker pull j3soon/isaac-sim-bin:5.0-user
docker pull j3soon/isaac-sim-bin:4.5
docker pull j3soon/isaac-sim-bin:4.5-user
```

```sh
xhost +local:docker
ISAAC_SIM_VERSION=4.5
docker run --rm -it --runtime=nvidia --gpus all --network=host \
  -v ~/docker/isaac-sim-bin/cache/kit:/root/isaac-sim/kit/cache:rw \
  -v ~/docker/isaac-sim-bin/cache/ov:/root/.cache/ov:rw \
  -v ~/docker/isaac-sim-bin/cache/pip:/root/.cache/pip:rw \
  -v ~/docker/isaac-sim-bin/cache/glcache:/root/.cache/nvidia/GLCache:rw \
  -v ~/docker/isaac-sim-bin/cache/computecache:/root/.nv/ComputeCache:rw \
  -v ~/docker/isaac-sim-bin/logs:/root/.nvidia-omniverse/logs:rw \
  -v ~/docker/isaac-sim-bin/data:/root/.local/share/ov/data:rw \
  -v ~/docker/isaac-sim-bin/documents:/root/Documents:rw \
  -v $(pwd):/workspace \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  -v $HOME/.Xauthority:/root/.Xauthority \
  -v /dev/shm:/dev/shm \
  j3soon/isaac-sim-bin:${ISAAC_SIM_VERSION}
# in the container
~/isaacsim/isaac-sim.sh
# or WebRTC streaming:
~/isaacsim/isaac-sim.streaming.sh
```

For WebRTC streaming usage, see [the official Docker images section](#official-docker-images) below.

References:

- [Workstation Installation \| Isaac Sim](https://docs.isaacsim.omniverse.nvidia.com/4.5.0/installation/install_workstation.html)

## Isaac Lab

See the [official guide](https://isaac-sim.github.io/IsaacLab/main/source/deployment/docker.html#running-pre-built-isaac-lab-container).

## Isaac ROS

See the [official guide](https://nvidia-isaac-ros.github.io/getting_started/dev_env_setup.html).
