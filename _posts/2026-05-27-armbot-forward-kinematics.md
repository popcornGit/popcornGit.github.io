---
layout: post
title: "7-DoF 机械臂正运动学：从 DH 参数到齐次变换"
date: 2026-05-27
tags: [机械臂, 运动学, DH参数, Python, 机器人]
---

## 前言

去年开始做了一个 7 自由度串联机械臂的开源项目 **armbot**，目标是打造一套轻量级、模块化的机械臂控制软件栈。Phase 1 完成了核心运动学引擎的搭建，包括：

- YAML 驱动的 DH 参数配置系统
- 基于 Modified DH (Craig) 的正运动学
- 基于 Scipy 信赖域反射法的数值逆运动学
- 带零空间投影的冗余度求解器
- 几何雅可比矩阵与末端速度映射

本篇聚焦 Phase 1 中最基础也最关键的部分——**正运动学 (Forward Kinematics)**。

## DH 参数法简介

DH 参数法 (Denavit-Hartenberg) 是机器人运动学最经典的方法，用四个参数描述相邻连杆之间的几何关系：

| 参数 | 符号 | 含义 |
|------|------|------|
| 连杆扭角 | alpha | 绕 X 轴从 Z_{i-1} 旋转到 Z_i 的角度 |
| 连杆长度 | a | 沿 X 轴从 Z_{i-1} 到 Z_i 的平移距离 |
| 连杆偏距 | d | 沿 Z_{i-1} 轴从 X_{i-1} 到 X_i 的平移距离 |
| 关节角 | theta | 绕 Z_{i-1} 轴从 X_{i-1} 到 X_i 的旋转角度 |

DH 参数有两种主流约定：

- **Standard DH (经典 DH)**：变换顺序为 `RotZ → TransZ → TransX → RotX`，坐标系 i 固定在连杆 i 的 **末端**
- **Modified DH (Craig DH)**：变换顺序为 `RotX → TransX → RotZ → TransZ`，坐标系 i 固定在连杆 i 的 **首端**

armbot 选用了 **Modified DH**，因为它在树形结构和闭链运动学的处理上更自然，且在视觉上更容易理解——每个坐标系都位于关节处。

## 7-DoF 混合关节设计

armbot 的 7 个关节采用 **Z-Y-X 交替轴向** 设计，目的是最大化工作空间，避免相邻关节同轴导致的退化构型。具体参数如下：

| 关节 | 名称 | 类型 | alpha (rad) | a (m) | d (m) | theta_offset | 限位 (rad) |
|------|------|------|-------------|-------|-------|-------------|------------|
| J1 | waist_yaw | 旋转Z | 0 | 0 | 0.25 | 0 | ±π |
| J2 | shoulder_pitch | 旋转Y | -π/2 | 0.05 | 0 | 0 | ±π/2 |
| J3 | shoulder_roll | 旋转X | π/2 | 0 | 0.35 | 0 | ±π/2 |
| J4 | elbow_pitch | 旋转Y | -π/2 | 0.05 | 0 | 0 | ±2.27 |
| J5 | wrist_yaw | 旋转Z | π/2 | 0 | 0.30 | 0 | ±π |
| J6 | wrist_pitch | 旋转Y | -π/2 | 0.03 | 0 | 0 | ±π/2 |
| J7 | wrist_roll | 旋转X | π/2 | 0 | 0.12 | 0 | ±π |

TCP 偏移 (J7 → 末端): `[0, 0, 0.08] m`

设计亮点：

1. **Z → Y → X → Y → Z → Y → X** 的交替旋转轴模式，使工作空间接近球体
2. 连杆长度逐段递减（0.35 → 0.30 → 0.12），类似人体手臂的上臂-前臂-手腕结构
3. J2 和 J4 有 `a=0.05m` 的微小偏置，避免零位时的运动学退化

## 齐次变换矩阵

Modified DH 的变换矩阵公式如下：

```
      [cosθ,  -sinθ,   0,     a   ]
T =   [sinθ*cosα, cosθ*cosα, -sinα, -d*sinα]
      [sinθ*sinα, cosθ*sinα,  cosα,  d*cosα]
      [0,         0,         0,     1     ]
```

Python 实现非常简洁：

```python
import numpy as np

def dh_transform(alpha: float, a: float, d: float, theta: float) -> np.ndarray:
    """
    Modified DH 变换矩阵

    Args:
        alpha: 连杆扭角 (rad)
        a: 连杆长度 (m)
        d: 连杆偏距 (m)
        theta: 关节角 (rad)

    Returns:
        4x4 齐次变换矩阵
    """
    ct = np.cos(theta)
    st = np.sin(theta)
    ca = np.cos(alpha)
    sa = np.sin(alpha)

    T = np.array([
        [ct, -st, 0, a],
        [st * ca, ct * ca, -sa, -sa * d],
        [st * sa, ct * sa, ca, ca * d],
        [0, 0, 0, 1]
    ])

    return T
```

## 链式乘法

从基座到末端的完整位姿 = 依次左乘每个关节的 DH 变换矩阵：

```python
class ForwardKinematics:
    def __init__(self, config_path: str):
        with open(config_path, 'r') as f:
            config = yaml.safe_load(f)

        self.joints = config['robot']['joints']
        self.end_effector = config['robot']['end_effector']

        # 提取 DH 参数
        self.dh_params = []
        for joint in self.joints:
            dh = joint['dh']
            self.dh_params.append({
                'alpha': dh['alpha'],
                'a': dh['a'],
                'd': dh['d'],
                'theta_offset': dh['theta']
            })

    def compute(self, q: np.ndarray) -> np.ndarray:
        """计算 base → TCP 的齐次变换矩阵"""
        T = np.eye(4)

        for i in range(self.dof):
            dh = self.dh_params[i]
            theta = q[i] + dh['theta_offset']
            T_i = dh_transform(dh['alpha'], dh['a'], dh['d'], theta)
            T = T @ T_i

        # 添加 TCP 偏移
        offset = np.array(self.end_effector['offset'])
        T[0:3, 3] += T[0:3, 0:3] @ offset

        return T

    def get_all_link_poses(self, q: np.ndarray) -> list:
        """获取所有连杆的位姿，用于可视化"""
        poses = []
        T = np.eye(4)
        poses.append(T.copy())  # 基座

        for i in range(self.dof):
            dh = self.dh_params[i]
            theta = q[i] + dh['theta_offset']
            T_i = dh_transform(dh['alpha'], dh['a'], dh['d'], theta)
            T = T @ T_i
            poses.append(T.copy())

        return poses
```

关键点：

- `compute()` 计算末端位姿，适合 IK 求解和运动规划调用
- `get_all_link_poses()` 返回所有中间连杆位姿，专为可视化设计
- TCP 偏移在链式乘法之后加入，而不是作为额外的 DH 关节

## 验证结果

使用 `fk_demo.py` 测试了 4 种典型构型：

![armbot 正运动学可视化](/assets/images/armbot-fk-demo.png)

| 构型 | 末端位置 (m) | 姿态 RPY (deg) | 说明 |
|------|-------------|----------------|------|
| Home | [0.130, 0.000, 1.100] | [0.0, 0.0, 0.0] | 竖直站立，臂展约 1.1m |
| Stretch | [0.130, 0.000, 1.100] | [0.0, 0.0, 0.0] | 与 Home 相同（零位） |
| Bend | [0.626, 0.247, 0.735] | [37.6, 64.9, -50.2] | 肘部弯曲，末端前伸 |
| Random | [-0.511, -0.201, 0.712] | [143.5, -59.1, 77.1] | 多关节协同运动 |

每个子图中的蓝点表示关节位置，红/绿/蓝线段表示每个关节坐标系的 X/Y/Z 轴。

## 下一步计划

Phase 2 的重点方向：

1. **速度控制** — 利用几何雅可比矩阵实现末端速度到关节速度的映射
2. **零空间优化** — 利用 7-DoF 的冗余自由度，在保持末端位姿的同时优化关节限位回避
3. **轨迹规划** — 笛卡尔空间直线/圆弧插补
4. **实时仿真** — 接入物理引擎进行动力学验证

## 交互式 Demo

想要在浏览器里试试正运动学计算？点击下面的链接打开交互式 3D 演示：

[armbot FK 交互式 Demo](/assets/demos/armbot-fk.html)
