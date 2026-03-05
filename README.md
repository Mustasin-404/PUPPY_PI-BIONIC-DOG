# Project II: Puppy Pi – System Design and Implementation

## 📌 Overview
This repository documents the system architecture and implementation of an autonomous navigation and dynamic path replanning stack for the **Hiwonder PuppyPi** quadruped robot. Moving from wheeled robots to legged locomotion introduces unique challenges, particularly in odometry, sensor stability, and gait control. 

This project bridges the gap between high-level ROS 2 Nav2 algorithms and low-level quadruped kinematics, resulting in a fully autonomous legged system capable of real-time mapping, obstacle avoidance, and dynamic replanning.

## 🛠️ Hardware & Software Stack
* **Robot Frame:** Hiwonder PuppyPi
* **Compute Board:** Raspberry Pi 4
* **Operating System:** Ubuntu 22.04
* **Middleware:** ROS 2 Humble
* **Containerization:** Docker (Custom ROS 2 image replacing default ROS 1)
* **Visualization & Debugging:** Foxglove Studio, VDO Ninja
* **Sensors:** LD19 Lidar, Camera, IMU

---

## 🚀 Implementation Phases

### Phase 1: System Integration & Docker Bridge
**Goal:** Establish a secure, persistent bridge between a Docker container and the robot's internal hardware nodes.
* **Challenge:** Developing infrastructure to tunnel directly into a specific Docker container running on the robot to control hardware.
* **Result:** Successfully verified the hardware-software bridge. Enabled live subscription to IMU/Battery telemetry and published actuator commands (buzzer/servos) directly from a workstation.

### Phase 2: Teleoperation & Custom ROS 2 Architecture
**Goal:** Move beyond standard demos to a robust, developer-friendly debugging and teleoperation architecture.
* **The Nervous System (SSH):** Established secure shell access for seamless system monitoring and file management.
* **The Eyes (Foxglove Studio):** Integrated the Foxglove Bridge for real-time perception visualization. Instantly overlaying the TF tree, camera stream, and Lidar scans significantly accelerated the testing cycle.
* **The Brain (Custom Launch Architecture):** Built `my_puppy`, a custom overlay package. A single master launch file orchestrates the initialization of the `ros_robot_controller`, the gait engine, and optimized sensor nodes.
* **Control (SSH Teleop):** Developed a custom Python-based keyboard controller to manage state transitions (Sit/Stand/Walk) and send precise velocity commands to the navigation stack.
* **Result:** Achieved fluid gait synchronization, eliminated micro-stutters, and resolved camera frame freezing. The robot successfully took its first controlled steps.

### Phase 3: Sensor Fusion & 2D SLAM
**Goal:** Generate reliable maps despite the noisy odometry and Lidar shake inherent to quadruped locomotion.
* **Hardware Calibration:** Wrote a custom Python script to mechanically trim servos to exact pulse widths, correcting a 20-degree physical assembly error.
* **Gait Synchronization:** Developed a threaded teleop node to lock walking speed at `0.06 m/s`—the frequency where the Lidar optimally matches the gait cycle.
* **Sensor Fusion:** Tuned an Extended Kalman Filter (EKF) to fuse RF2O (Laser Odometry) with IMU data to stabilize mapping during slipping.
* **Result:** Generated maps with sharp walls and perfect loop closures, establishing reliable spatial awareness.

### Phase 4: Autonomous Navigation (Nav2)
**Goal:** Implement a fully autonomous loop using the ROS 2 Navigation stack.
* **Planning Stack:** Integrated **A*** for global planning and **TEB** (Timed Elastic Band) for local planning to handle non-holonomic constraints.
* **Mapping:** Utilized SLAM Toolbox (Karto).
* **Result:** The robot successfully navigates autonomously from a start point to a target coordinate without human intervention.

### Phase 5: Obstacle Avoidance & Dynamic Path Replanning
**Goal:** Achieve reactive obstacle avoidance and autonomous recovery behaviors.
* **The StictionTrap Solution:** Standard Nav2 velocity commands (<0.05 m/s) fell into the gait controller's physical deadzone. We built a custom **Booster Logic middleware** that intercepts weak velocity requests and amplifies them to the minimum torque threshold required for continuous motion.
* **Type-Safety Patching:** Identified and patched a critical float/int mismatch bug in the manufacturer’s driver that crashed the controller during Recovery Behaviors (spinning).
* **Ghost Wall Prevention:** Mapped internal `servo_deviation.json` files and built a custom ROS 2 calibration tool to ensure the Lidar remains perfectly parallel to the ground, preventing the robot from perceiving the floor as a "Ghost Wall."
* **Result:** The PuppyPi can autonomously patrol an area, detect dynamic or unknown obstacles, halt, back up, and dynamically re-route around them.

---


---

## 🚀 Practical Implementation

This project was developed in multiple stages. Below are the video demonstrations for each phase of the implementation:

### 🔹 Phase 1
[Watch Phase 1 Demonstration](https://github.com/user-attachments/assets/1865a9fb-7d39-4c8c-9a0c-ee44f4bd9ebb)

### 🔹 Phase 2
[Watch Phase 2 Demonstration](https://github.com/user-attachments/assets/f2c1609e-f8a5-467f-8752-193871682820)

### 🔹 Phase 3
[Watch Phase 3 Demonstration](https://github.com/user-attachments/assets/aa6ab230-2b6b-49cd-b060-fd9ca6f2e6c9)

### 🔹 Phase 4
[Watch Phase 4 Demonstration](https://github.com/user-attachments/assets/ebb6de9b-7c30-443e-9da0-d6a7d2384588)

### 🔹 Phase 5
[Watch Phase 5 Demonstration](https://github.com/user-attachments/assets/545351ef-4383-4c28-8e5c-ae58e50fe739)

---
