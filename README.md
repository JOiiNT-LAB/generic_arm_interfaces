# generic_arm_interfaces

Robot-agnostic ROS 2 service definitions (gripper command, pose saving), factored out into their own package so they can be shared **without** creating a dependency between the packages that use them.

## 📦 Services

### `srv/GripperCommand.srv`

```
string command       # "open" | "close" | "move"
float32 position      # only used with command:="move", range [0.0, 1.0]
string gripper_type   # "robotiq" | "softhand" | "rg2" | "franka_hand" | "auto" (empty = auto)
---
bool success
string message
```

### `srv/SavePose.srv`

```
int8 save_mode         # 0 = absolute, 1 = camera-relative, 2 = ArUco marker-relative
string task_name
string reference_frame # marker frame id, only used with save_mode 2
---
bool success
string message
```

## 🧩 Standalone use

This package only depends on standard ROS 2 interface packages (`builtin_interfaces`, `std_msgs`) — no other in-workspace dependency. It can be cloned and built on its own; see [`generic_arm_controller`'s README](https://github.com/JOiiNT-LAB/generic_arm_controller#-standalone-build) for the `vcs import` step that pulls this repo in when building `generic_arm_controller` outside the main workspace.

## 🔗 Why this is a separate repo

| Package | Relation | Depends on `generic_arm_controller`? |
|---|---|---|
| [`generic_arm_controller`](https://github.com/JOiiNT-LAB/generic_arm_controller) | Implements the service **servers** (`/gripper/command`, `/save_pose`) | — |
| `llm_app` | Calls the services as a **client** (`service_dispatch.py`, `skill_impl.py`, `chatlive.py`) | No — only depends on this package |

`generic_arm_controller` is its own submodule; `llm_app` lives in the main [`ros2_arise_vulcanexus_V2`](https://github.com/JOiiNT-LAB/ros2_arise_vulcanexus) workspace and has no build/code dependency on `generic_arm_controller` itself. Putting the shared `.srv` definitions here — instead of inside `generic_arm_controller` — lets `llm_app` depend on just the message contract, not the whole controller.
