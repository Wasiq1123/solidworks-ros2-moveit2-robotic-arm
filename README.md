# SolidWorks → ROS 2 → MoveIt 2 Robotic Arm

A custom 4-DOF robotic arm modeled in SolidWorks and integrated into ROS 2 for visualization and motion planning with MoveIt 2. This project demonstrates a CAD-to-robotics workflow covering the robot description, kinematic structure, MoveIt configuration, and motion-planning examples using the [MoveIt Setup Assistant](https://moveit.picknik.ai/main/doc/examples/setup_assistant/setup_assistant_tutorial.html).

- **SolidWorks CAD files:** [Browse the robotic arm models](https://huggingface.co/datasets/WasiqSaleem/robotic-arm-solidworks-cad-to-ros2/tree/main)
- **Project demonstration:** [View the robotics demonstration videos](https://huggingface.co/datasets/WasiqSaleem/robotics-project-demonstrations/tree/main)

---

## Pipeline

```
SolidWorks CAD  →  URDF export (meshes + kinematic chain)
                →  fanuc_description  (robot_state_publisher, RViz visualization)
                →  MoveIt Setup Assistant  →  fanuc_moveit_config
                →  Motion planning demos (joint goals, pose goals, Cartesian paths, gripper control)
```

The robot has four revolute joints, giving it **4 degrees of freedom (4-DOF)**. Its joints and links are represented in the URDF, while the MoveIt semantic description organizes the robot into the `arm` and `hand` planning groups. The exact joint membership of each group should match the robot's intended kinematic structure and control setup.

---

## CAD-to-MoveIt 2 Integration

The integration process follows these main steps:

1. **Prepare the robot description:** Export the SolidWorks model to a ROS-compatible description containing the links, joints, visual meshes, and collision geometry. Check link origins, joint axes, limits, and the parent-child chain in the URDF.
2. **Define the tool frame:** If the gripper or end effector needs a reference frame, add a tool frame such as `tool0` with a fixed joint to the appropriate preceding link. Attach the gripper to the correct parent link in the kinematic chain.
3. **Generate the MoveIt configuration:** Load the robot description in the MoveIt Setup Assistant and configure the planning groups, end-effector/tool settings, self-collision matrix, named states, kinematics, joint limits, and controller settings.
4. **Check controller configuration:** Ensure each configured controller references the correct joints and that the MoveIt controller configuration agrees with the ROS 2 control setup.
5. **Validate planning:** Launch the robot in RViz and test joint-space goals, pose goals, and Cartesian paths. Verify the joint hierarchy and planning-group membership if the robot does not display or plan as expected.

The `arm` and `hand` groups should only include joints that are actually intended to be controlled by those groups. Controller and planning configurations must be checked against the real URDF/SRDF rather than copied blindly from a tutorial or another robot.

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

## Build and Run

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

## Acknowledgments

The robot design and naming are inspired by FANUC industrial robotic arms.

---

## License

Apache-2.0
