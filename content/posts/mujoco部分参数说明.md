---
title: mujoco-geom组件参数说明
description: mujoco中geom组件的参数说明
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-09-23T13:11:22+08:00'
lastmod: '2023-09-23T13:11:22+08:00'
featuredImage:
draft: false
---

## geom
### contype: int, “1” / conaffinity: int, “1”
`contype` 和 `conaffinity` 指定用于动态生成接触对的32位整数位掩码（请参见 Computation 章节中的[碰撞检测](https://mujoco.readthedocs.io/en/latest/computation/index.html#collision)）。如果一个geom的contype与另一个geom的conaffinity 是 "**compatible**" 的，则两个geom可以发生碰撞。

"**compatible**"意味着两个位掩码具有一个公共位设置为1。即：
`(contype1 & conaffinity2) || (contype2 & conaffinity1)` 的结果布尔值为 True。

由于默认值 `contype = conaffinity = 1`，因此平时可以忽略该参数的设置。

### condim: int, “3”
对于动态生成的接触对的接触空间的维度，设置为两个 geoms 中最大 `condim` 的值 （参考 Computation 章节中的 [Contact](https://mujoco.readthedocs.io/en/latest/computation/index.html#cocontact)）。

下面是可用的值和对应的含义:

|condim|Description|
|:---:|:---:|
|1|Frictionless contact.|
|3|Regular frictional contact, opposing slip in the tangent plane.|
|4|Frictional contact, opposing slip in the tangent plane and rotation around the contact normal. This is useful for modeling soft contacts (independent of contact penetration).|
|6|Frictional contact, opposing slip in the tangent plane, rotation around the contact normal and rotation around the two axes of the tangent plane. The latter frictional effects are useful for preventing objects from indefinite rolling.|

## joint
### armature: real, “0”
各自由度的 **armature inertia** (电机转子惯性) (or rotor inertia, or reflected inertia)。

在广义坐标中，作为常数被加到惯性矩阵的对角线上，使仿真更稳定，并增加了物理真实性。这是因为当电机与系统相连时，传动装置会将电机力放大 c，转子（即电机的运动部分）的惯性也会放大 c*c。行星齿轮箱早期阶段的齿轮也是如此。这些额外的惯性通常在模型中明确表示的机器人部件的惯性中占主导地位，而电枢属性正是对它们进行建模的方法。

