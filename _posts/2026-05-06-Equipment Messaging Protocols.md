---
title: "Equipment Messaging Protocols: SECS/GEM (S2F41), OPC-UA, and ROS2 Integration"
date: 2026-08-15 19:00:00 +0900
categories: [Semiconductor, Protocols]
tags: [secs-gem, opc-ua, ros2, eap, recipe-selection, s2f41]
math: true
---

### Command Execution Sequence

- When EAP commands an equipment to select a recipe, it sends SECS/GEM message : **S2F41** (Host command Send)
- The command contains parameter names like PPSELECT along with the target recipe ID (e.g., **ppid02**).

![2026-08-15-075208.png](/assets/images/2026-08-15-075208.png)
**OR**
![2026-08-15-075225.png](/assets/images/2026-08-15-075225.png)

### Translation Layer (SECS/GEM to Modern Standards):

**Basically Old machines speak SECS/GEM.**
New modern robotic machines speak new languages like **OPC-UA** OR **ROS2**

- **OPC-UA**: The SECS message gets mapped to OPC-UA node read/write operations (e.g., client.get_node(), node.write()).

- **ROS2 / Robotics**: For modern robotic equipment controllers, SECS commands translate to ROS2 Publish/Subscribe topics(ros2 topic pub /ppid/msg/select).

![2026-08-15-075412.png](/assets/images/2026-08-15-075412.png)

---