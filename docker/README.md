---
tip: translate by baidu@2024-10-23 16:22:16
date: 2024-10-24 09:06:41
---

# Open AD Kit: containerized workloads for Autoware

[Open AD Kit](https://autoware.org/open-ad-kit/) offers containers for Autoware to simplify the development and deployment of Autoware and its dependencies. This directory contains scripts to build and run the containers.

> [打开 AD 工具包]为 Autoware 提供容器，以简化 Autoware 及其依赖项的开发和部署。此目录包含用于构建和运行容器的脚本。

Detailed instructions on how to use the containers can be found in the [Open AD Kit documentation](https://autowarefoundation.github.io/autoware-documentation/main/installation/autoware/docker-installation/).

> 有关如何使用容器的详细说明，请参阅[Open AD Kit 文档].

## Development containers

When using Open AD Kit as a development container, it is easy to use Autoware's all-in-one development container image, `ghcr.io/autowarefoundation/autoware:universe-devel-cuda`.

> 当使用 Open AD Kit 作为开发容器时，很容易使用 Autoware 的一体化开发容器映像`ghcr.io/autowarefoundation/Autoware:universe-devel-cuda`。

If you do not need the CUDA drivers, you can also use the smaller image `ghcr.io/autowarefoundation/autoware:universe-devel`.

> 如果你不需要 CUDA 驱动程序，你也可以使用较小的镜像`ghcr.io/autowarefoundation/autoware:universe-devel`。

```shell
$ git clone git@github.com:autowarefoundation/autoware.git
$ cd autoware
$ vcs import src < autoware.repos
$ docker run -it --rm \
  –v $PWD/src/universe/autoware.universe/XXX/autoware_YYY:/autoware/src/autoware_YYY \
  ghcr.io/autowarefoundation/autoware:universe-devel-cuda
$ colcon build --mixin debug compile-commands
$ source install/setup.bash
$ ros2 run --prefix "gdb -ex run --args" autoware_YYY ZZZ
```

For example, if you want to make modifications to [`autoware.universe/perception/autoware_bytetrack`](https://github.com/autowarefoundation/autoware.universe/tree/main/perception/autoware_bytetrack), you can execute the following commands to perform the volume mount and debug build and execution of only the `autoware_bytetrack`.

> 例如，如果你想修改[`autoware.universe/perception/autoware_bytetrack`]，您可以执行以下命令来执行卷装载和调试构建，并仅执行`autoware_bytetrack`。

Note that `gdb` is not currently installed in the development containers, but it would be installed in the near future.

> 请注意，`gdb`目前尚未安装在开发容器中，但将在不久的将来安装。

```shell
$ docker run -it --rm \
  -v $PWD/src/universe/autoware.universe/perception/autoware_bytetrack:/autoware/src/autoware_bytetrack \
  ghcr.io/autowarefoundation/autoware:universe-devel-cuda
$ root@a566e785c4d2:/autoware# colcon build --mixin debug compile-commands
Starting >>> autoware_bytetrack
[Processing: autoware_bytetrack]
Finished <<< autoware_bytetrack [37.9s]

Summary: 1 package finished [38.1s]
$ root@a566e785c4d2:/autoware# source install/setup.bash
$ root@a566e785c4d2:/autoware# apt update && apt install gdb
$ root@a566e785c4d2:/autoware# ros2 run --prefix "gdb -ex run --args" autoware_bytetrack bytetrack_node_exe
GNU gdb (Ubuntu 12.1-0ubuntu1~22.04.2) 12.1
...
[Thread debugging using libthread_db enabled]
...
[New Thread 0x7ff6f3fff640 (LWP 1205)]
Init ByteTrack!
```

## Runtime containers

In the execution container, there is the all-in-one runtime container for Autoware, `ghcr.io/autowarefoundation/autoware:universe-cuda`, and the multi-containerized `ghcr.io/autowarefoundation/autoware:universe-***-cuda` for each component of Autoware Universe.

> 在执行容器中，有用于 Autoware 的一体化运行时容器`ghcr.io/autowarefoundation/Autoware:universe-cuda`，以及用于 Autoware universe 每个组件的多容器化`ghcr.io/autowarefoundation/Autoware：universe-***-cuda`。

The all-in-one execution container also has the autoware_launch package installed, allowing it to be started in the same way as a locally built Autoware.

> 一体化执行容器还安装了 autoware_launch 包，使其能够以与本地构建的 autoware 相同的方式启动。

```shell
git clone git@github.com:autowarefoundation/autoware.git
cd autoware
docker run -it --rm ghcr.io/autowarefoundation/autoware:universe-cuda
ros2 launch autoware_launch planning_simulator.launch.xml map_path:=...
```

For example, if you want to run the runtime container that only includes the `sensing/perception` components, you can execute it as follows.

> 例如，如果你想运行只包含`sensing/perception`组件的运行时容器，你可以按如下方式执行它。

```shell
docker run -it --rm ghcr.io/autowarefoundation/autoware:universe-sensing-perception-cuda
ros2 launch autoware_pointcloud_preprocessor preprocessor.launch.xml
```

## Multi-stage Dockerfile structure

![](./Dockerfile.svg)

The suffix `-devel` (e.g. `universe-devel`) is intended for use as a [development container](https://containers.dev). On the other hand, those without the `-devel` suffix (e.g. `universe`) are intended to be used as a runtime container.

> 后缀`-devel`(例如`universe-devel`)旨在用作[开发容器]. 另一方面，那些没有`-devel`后缀的(例如`universe`)旨在用作运行时容器。

### `$BASE_IMAGE`

This is a base image of this Dockerfile. [`ros:humble-ros-base-jammy`](https://hub.docker.com/_/ros/tags?page=&page_size=&ordering=&name=humble-ros-base-jammy) will be given.

> 这是此 Dockerfile 的基本映像。`ros:humble-ros-base-jammy` 将被授予。

### `base`

This stage performs only the basic setup required for all Autoware images.

> 此阶段仅执行所有 Autoware 映像所需的基本设置。

### `rosdep-depend`

The ROS dependency package list files will be generated.
These files will be used in the subsequent stages:

- `core-devel`
- `universe-common`
- `universe-COMPONENT-devel` (e.g. `universe-sensing-perception-devel`)
- `universe-COMPONENT` (e.g. `universe-sensing-perception`)
- `universe-devel`
- `universe`

By generating only the package list files and copying them to the subsequent stages, the dependency packages will not be reinstalled during the container build process unless the dependency packages change.

> 通过仅生成包列表文件并将其复制到后续阶段，除非依赖包发生更改，否则在容器构建过程中不会重新安装依赖包。

### `core-devel`

This stage installs the dependency packages based on `/rosdep-core-depend-packages.txt` and build the packages under the `core` directory of `autoware.repos`.

> 此阶段安装基于`/rosdep-core-depend-packages.txt`的依赖包，并在`autoware.rerepos`的`core`目录下构建包。

### `universe-common-devel`

This stage installs the dependency packages based on `/rosdep-universe-common-depend-packages.txt` and build the packages under the following directories of `autoware.repos`.

> 此阶段安装基于`/rosdep universe common depend-packages.txt`的依赖包，并在`autoware.rerepos`的以下目录下构建包。

- `universe/external`
- `universe/autoware.universe/common`

### `universe-sensing-perception-devel`

This stage installs the dependency packages based on `/rosdep-universe-sensing-perception-depend-packages.txt` and build the packages under the following directories of `autoware.repos`.

> 此阶段安装基于`/rosdep universe sensing perception depend packages.txt`的依赖包，并在`autoware.rerepos`的以下目录下构建包。

- `universe/autoware.universe/perception`
- `universe/autoware.universe/sensing`

### `universe-sensing-perception`

This stage is a Autoware Universe Sensing/Perception runtime container. It only includes the dependencies given by `/rosdep-universe-sensing-perception-exec-depend-packages.txt` and the binaries built in the `universe-sensing-perception-devel` stage.

> 此阶段是一个 Autoware Universe Sensing/Perception 运行时容器。它只包括由`/rosdep universe sensing perception exec depend packages.txt`给出的依赖关系和在`universe sensitive perception devel`阶段构建的二进制文件。

### `universe-localization-mapping-devel`

This stage installs the dependency packages based on `/rosdep-universe-localization-mapping-depend-packages.txt` and build the packages under the following directories of `autoware.repos`.

> 此阶段基于`/rosdep universe 本地化映射 depend-packages.txt`安装依赖包，并在`autoware.rerepos`的以下目录下构建包。

- `universe/autoware.universe/localization`
- `universe/autoware.universe/map`

### `universe-localization-mapping`

This stage is a Autoware Universe Localization/Mapping runtime container. It only includes the dependencies given by `/rosdep-universe-localization-mapping-exec-depend-packages.txt` and the binaries built in the `universe-localization-mapping-devel` stage.

> 此阶段是 Autoware Universe 本地化/映射运行时容器。它只包括由`/rosdep universe localization mapping exec depend packages.txt`给出的依赖关系和在`universe localized mapping devel`阶段构建的二进制文件。

### `universe-planning-control-devel`

This stage installs the dependency packages based on `/rosdep-universe-planning-control-depend-packages.txt` and build the packages under the following directories of `autoware.repos`.

> 此阶段基于`/rosdep universe planning control depend-packages.txt`安装依赖包，并在`autoware.rerepos`的以下目录下构建包。

- `universe/autoware.universe/control`
- `universe/autoware.universe/planning`

### `universe-planning-control`

This stage is a Autoware Universe Planning/Control runtime container. It only includes the dependencies given by `/rosdep-universe-planning-control-exec-depend-packages.txt` and the binaries built in the `universe-planning-control-devel` stage.

> 此阶段是 Autoware Universe 规划/控制运行时容器。它只包括由`/rosdep universe planning control exec depend packages.txt`给出的依赖关系和在`universe planing control devel`阶段构建的二进制文件。

### `universe-vehicle-system-devel`

This stage installs the dependency packages based on `/rosdep-universe-vehicle-system-depend-packages.txt` and build the packages under the following directories of `autoware.repos`.

> 此阶段安装基于`/rosdep universe vehicle system depend-packages.txt`的依赖包，并在`autoware.reps`的以下目录下构建包。

- `universe/autoware.universe/vehicle`
- `universe/autoware.universe/system`

### `universe-vehicle-system`

This stage is a Autoware Universe Vehicle/System runtime container. It only includes the dependencies given by `/rosdep-universe-vehicle-system-exec-depend-packages.txt` and the binaries built in the `universe-vehicle-system-devel` stage.

> 此阶段是 Autoware Universe 车辆/系统运行时容器。它只包括由`/rosdep universe vehicle system exec depend packages.txt`给出的依赖关系和在`universe veicle system devel`阶段构建的二进制文件。

### `universe-devel`

This stage installs the dependency packages based on `/rosdep-universe-depend-packages.txt` and build the remaining packages of `autoware.repos`:

> 此阶段安装基于`/rosdep universe depend-packages.txt`的依赖包，并构建`autoware.rerepos`的其余包：

- `launcher`
- `param`
- `sensor_component`
- `sensor_kit`
- `universe/autoware.universe/evaluator`
- `universe/autoware.universe/launch`
- `universe/autoware.universe/simulator`
- `universe/autoware.universe/system`
- `universe/autoware.universe/tools`
- `universe/autoware.universe/vehicle`
- `vehicle`

This stage provides an all-in-one development container to Autoware developers. By running the host's source code with volume mounting, it allows for easy building and debugging of Autoware.

> 此阶段为 Autoware 开发人员提供了一个一体化的开发容器。通过卷装载运行主机的源代码，可以轻松构建和调试 Autoware。

### `universe`

This stage is an Autoware Universe runtime container. It only includes the dependencies given by `/rosdep-exec-depend-packages.txt`, the binaries built in the `universe-devel` stage, and artifacts.

> 此阶段是 Autoware Universe 运行时容器。它只包括由`/rosdep-exec-depend-packages.txt`给出的依赖关系、在`universe-devel`阶段构建的二进制文件和工件。
