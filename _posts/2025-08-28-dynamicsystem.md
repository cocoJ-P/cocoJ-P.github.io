---
layout: post
title: Dynamic Systems 101 Part 1 — The Pendulum
date: 2025-08-28 15:10:00
description: this is what included plotly.js code could look like
tags: formatting charts
categories: sample-posts
chart:
  plotly: true
---


# Dynamic Systems 101: Part 1 — The Pendulum

In this series, when we talk about dynamical systems, we mainly mean finite-dimensional systems described by ordinary differential equations (ODEs). These are models where the state evolves in time but does not explicitly depend on space, such as the pendulum, the logistic map, or the Lorenz system.

While partial differential equations (PDEs) — like those in fluid dynamics — are also dynamical systems in a broader sense (infinite-dimensional ones), they belong to a different research community and require specialized mathematical and numerical tools. To keep this series focused and beginner-friendly, we will concentrate on ODE-based dynamical systems: their modeling, equilibrium points, stability, and basic numerical methods.

---

**我们从单摆问题入手，开始一步步的结合 MosaicX 完成简单动力学系统的求解工作。**

## 1. 单摆的动力学方程

单摆的基本方程是一个二阶非线性 ODE：

$$
\ddot{\theta}(t) + \frac{g}{L}\sin\theta(t) = 0
$$

其中：

- $\theta(t)$：摆角
- $g$：重力加速度
- $L$：摆长

---
