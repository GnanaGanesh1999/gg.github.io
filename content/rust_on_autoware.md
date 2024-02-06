+++
title = "Rust on Autoware"
template = "page.html"
date = 2024-02-06T17:13:14+05:30
[taxonomies]
tags = ["rust", "autoware"]
[extra]
summary = ""
+++
### Introduction
Autoware is a software platform for autonomous driving that provides various capabilities such as perception, planning, control, simulation, and visualization. Autoware is based on the Robot Operating System (ROS) and is designed to be modular, scalable, and open-source.

Rust is a modern programming language that offers many benefits for automotive software development. Rust enforces memory safety and safe concurrency at compile time, which prevents common errors such as memory leaks, data races and use-after-free vulnerabilities. These errors can cause unpredictable and catastrophic failures in complex embedded systems that require high levels of functional safety and security. Rust also has comparable performance with C and C++, the traditional languages used for automotive software, but with a more mature ecosystem of tools and libraries. Rust is being evaluated by several automotive organisations as a potential alternative for developing software defined vehicles (SDVs) that feature electrification, autonomous driving and connectivity.

In this article, we will explore how to add Rust to an existing C++ project like Autoware.ai

### What are we going to write in Rust?
In Autoware, the nodes are the structural component. It can be related to the executables. We will be focussing on the `ekf_localiser` node. This node takes care of handling the input from the lidar and estimates the vehicle's pose and velocity using an extended Kalman filter (EKF). It fuses data from odometry, IMU, and GNSS sensors to provide a more accurate localization than using any single sensor alone. This node is dependant on `Kalman filter` algorithm. We will replacing the existing C++ implementation of this algorithm with the Rust implementation.

### Current working of ekf_localiser
(Diagram)

### Build system
Rust by default uses `cargo` as its build system. It is used for building, running, testing and packaging rust applications.  But Autoware uses `colcon` build system where each node has its own `cmake` projects.  Therefore for smooth integration with the autoware build system, we will be using `cmake` to build our Rust project.

### CXX
Rust language does not provide out of the box support for C++ interoperability. There are some open source tools written by the enthusiastic Rust community. `cxx` is one such tool. It provides interoperability between Rust and C++.

### Creating the Rust project






### Rust project
The project has the following files.
`src/lib.rs` -> Bindings for the C++
`src/kalman_filter_rust.rs` -> Rust implementation of Kalman filter
`Cargo.toml` -> The project metadata and the dependency for `nalgebra` crate
`Cmakelists.txt` -> Builds the `kalman_filter_rust` lib (static library)

### C++ project
The `src/kalman_filter.cpp` and `include/kalman_filter.hpp` files had been changed to bind the Rust code. `kf_rust` attribute is added to the KalmanFilter class. This attribute acts as a bridge between C++ and Rust.

### Shared structure
Shared structure `Matrix` is used to convert data between Rust and C++. In C++, the `Eigen::MatrixXd` is converted to `shared::Matrix` and vice-versa. In Rust, the `shared::Matrix` is converted to `nalgebra::DMatrix` and vice-versa.

### Build the project
```bash
  ./build_autoware.sh
```

### Run the tests
```bash
cd /home/autoware/autoware.ai/build/amathutils_lib/devel/lib/amathutils_lib
./test-kalman_filter
```
