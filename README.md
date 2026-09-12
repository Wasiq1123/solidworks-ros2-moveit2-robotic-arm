# SolidWorks → ROS 2 → MoveIt 2 Robotic Arm

A 4-DOF robotic arm designed from scratch in SolidWorks, exported to ROS 2, and brought into motion planning with MoveIt 2 — the full pipeline from CAD to URDF to a working MoveIt configuration and motion planning demos, built using the [MoveIt Setup Assistant](https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html).

---

## Pipeline

```
SolidWorks CAD  →  URDF export (meshes + kinematic chain)
                →  fanuc_description  (robot_state_publisher, RViz visualization)
                →  MoveIt Setup Assistant  →  fanuc_moveit_config
                →  Motion planning demos (joint goals, pose goals, Cartesian paths, gripper control)
```

The arm has 4 degrees of freedom: 3 arm joints (`joint1`, `joint2`, `joint3`) plus a gripper joint (`joint4`), organized into two planning groups (`arm`, `hand`) in the MoveIt semantic robot description.

---

## Repository Structure

```
my_robotics_arm/
├── fanuc_description/              # URDF + meshes exported from the SolidWorks model
│   ├── urdf/robot.urdf
│   ├── meshes/
│   │   ├── base_link.STL
│   │   ├── link1.STL … link4.STL
│   ├── CMakeLists.txt
│   ├── package.xml
│   └── LICENSE
│
├── fanuc_moveit_config/             # Built with the MoveIt Setup Assistant
│   ├── config/
│   │   ├── fanuc.srdf                # semantic robot description (groups, poses, collision pairs)
│   │   ├── fanuc.urdf.xacro
│   │   ├── fanuc.ros2_control.xacro
│   │   ├── kinematics.yaml
│   │   ├── joint_limits.yaml
│   │   ├── moveit_controllers.yaml
│   │   ├── ros2_controllers.yaml
│   │   ├── initial_positions.yaml
│   │   ├── pilz_cartesian_limits.yaml
│   │   └── sensors_3d.yaml
│   ├── launch/
│   │   ├── demo.launch.py
│   │   └── moveit_rviz.launch.py
│   └── .setup_assistant
│
└── moveit2_examples/                 # Motion planning demos against this arm
    ├── cpp_examples/
    │   └── src/
    │       ├── joint_goal.cpp
    │       ├── named_goal.cpp
    │       ├── pose_goal.cpp
    │       ├── cartesian_path.cpp
    │       ├── gripper_open.cpp
    │       └── gripper_joint_value.cpp
    └── python_examples/
        └── python_examples/
            ├── joint_goal.py
            ├── named_goal.py
            └── pose_goal.py
```

---

## Packages

**`fanuc_description`** — the robot's physical description: URDF and visual/collision meshes exported directly from the SolidWorks model, packaged as a standalone ROS 2 description package.

**`fanuc_moveit_config`** — the MoveIt configuration for this arm, built with the MoveIt Setup Assistant: planning groups (`arm`, `hand`), named poses (`ready`, `open`, `close`), collision disable pairs between adjacent links, kinematics/controller configuration, and the `demo.launch.py` entry point for interactive motion planning in RViz.

**`moveit2_examples`** — C++ and Python nodes using the MoveGroup Interface API against this arm: joint-space goals, named-pose goals, pose goals, Cartesian path planning, and gripper open/close control.

---

## Build & Run

```bash
colcon build
source install/setup.bash
```

**Interactive motion planning in RViz:**
```bash
ros2 launch fanuc_moveit_config demo.launch.py
```

**Run a motion planning example directly:**
```bash
ros2 run cpp_examples joint_goal
# or
ros2 run python_examples joint_goal
```

---

## License

`fanuc_description` is licensed under Apache-2.0 — see its `LICENSE` file.
