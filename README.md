# ANYmal C Gazebo Simulation (ROS 2 Jazzy & Gazebo Harmonic)

A state-of-the-art, fully integrated simulation environment for the **ANYmal C quadruped robot**, completely migrated and optimized for **ROS 2 Jazzy Jalisco** and **Gazebo Harmonic (Gz Sim)**.

This repository implements real-time joint-space trajectory control, sensor pipelines, and simulation-to-ROS bridging, enabling high-fidelity quadruped simulation.

---

## 🚀 Features & Custom Integration
*   **ROS 2 Jazzy & Gazebo Harmonic Migration:** Replaced legacy Gazebo Classic (`gazebo_ros`) components with modern `ros_gz_sim` and configured the environment loader (`GZ_SIM_RESOURCE_PATH`) for modern `.sdf` formats.
*   **Unified `ros2_control` Setup:** Integrated a single hardware controller block (`gz_ros2_control/GazeboSimSystem`) claiming all 12 joints, enabling smooth position/effort joint trajectories and state broadcasts.
*   **Decoupled Clock & Sensor Bridging:** Solved simulation timing mismatches (`No clock received` warnings) by implementing a unified, robust `ros_gz_bridge` pipeline that bridges:
    *   **Simulation Time:** Unidirectional `/clock` forwarding to keep controllers in sync.
    *   **IMU sensor:** Bridged `/gz/imu` directly to ROS 2's `/imu/data`.
    *   **Ground-Truth Odometry:** Bridged `/model/anymal_c/odometry` to `/odom/ground_truth`.
    *   **Foot Contacts:** Bridges all four foot-contact sensors (`/gz/contact_lf/rf/lh/rh`) to their respective ROS 2 `/contact_force_sensors/*` topics.
*   **Modular Launch & Bringup Configuration:** Developed standard config setups, custom spawners, and automated multi-package launch configurations.

---

## 🛠️ Quick Start

### 1. Build and Source
Ensure your workspace is fully built and sourced using a clean ROS 2 Jazzy environment:

```bash
# Build the workspace
colcon build --symlink-install

# Source the overlay
source install/setup.bash
```

### 2. Launch the Gazebo Simulation
Spawn the robot and launch the simulator (this will also automatically spin up the topic bridges and load the controllers):

```bash
ros2 launch anymal_c_config gazebo.launch.py
```

### 3. Verify Active Controllers
Check if the joints are properly registered and running:

```bash
ros2 control list_controllers
```

### 4. Send Joint Trajectories
Send a control command to move the ANYmal C joints:

```bash
ros2 topic pub --once /joint_group_position_controller/joint_trajectory \
  trajectory_msgs/msg/JointTrajectory "{ \
    joint_names: [LF_HAA_joint, LF_HFE_joint, LF_KFE_joint, LH_HAA_joint, LH_HFE_joint, LH_KFE_joint, RF_HAA_joint, RF_HFE_joint, RF_KFE_joint, RH_HAA_joint, RH_HFE_joint, RH_KFE_joint], \
    points: [{positions: [0.0, 0.2, -0.4, 0.0, 0.2, -0.4, 0.0, 0.2, -0.4, 0.0, 0.2, -0.4], time_from_start: {sec: 1, nanosec: 0}}] \
  }"
```

---

## 🤝 Credits & Acknowledgements
This simulation environment was developed using core open-source resources, adapted and linked for next-generation ROS 2 Jazzy compatibility:
*   **ANYbotics** ([Linus Isler](mailto:lisler@anybotics.com) & [Remo Diethelm](mailto:rdiethelm@anybotics.com)): Authors of the original `anymal_c_simple_description` package, including the high-precision 3D meshes and URDF/xacro skeletons for the ANYmal C quadruped.
*   **Juan Miguel Jimeno** ([CHAMP Quadruped Framework](https://github.com/chvmp/champ)): Original author of the CHAMP quadruped configuration packages, which provided the base package structures and template foundations for the Gazebo setup.
