---
layout: post
title: Dynamic Systems 101 Part 1 The Pendulum
date: 2025-08-28
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

其中'二阶'除了指最高含二阶导数以外，还意味着：1. 解需要两个初始条件；2.在数值分析里，要转化为一阶系统求解

---
下一步的逻辑，确实就是要进入两个常见的“等价形式”：

1. **残差形式 (Residual form)**
2. **一阶系统化 (First-order system)**

这两个部分分别解决不同的需求：一个是用于数值分析/解的判定（残差形式），另一个是用于算法实现（ODE 数值积分）。

---

## 2. 残差形式 (Residual form)

残差形式就是把方程写成“函数 = 0”的形式，便于统一处理。

对于单摆：

$$
R(\theta, \dot{\theta}, \ddot{\theta}) = \ddot{\theta} + \frac{g}{L}\sin\theta = 0
$$

* **好处**：

  * 所有动力学方程都可以抽象成 $F(x, \dot{x}, \ddot{x}, t)=0$。
  * 在数值分析（如牛顿迭代、Jacobian 构造、隐式方法）中，直接处理“残差=0”更自然。
  * 比如你要求 Jacobian 时，就对 $R$ 对各个变量求偏导。

* **解释**：

  * “残差”为零，说明当前解满足方程；
  * 如果残差不为零，说明解不满足动力学约束，可以用于迭代修正。

---

## 3. 一阶系统化 (First-order system)

在数值积分（如 Runge–Kutta）时，二阶方程要拆解为一阶系统，因为大多数 ODE 求解器只接受一阶形式：

设：

$$
x_1 = \theta, \quad x_2 = \dot{\theta}
$$

得到：

$$
\begin{cases}
x_1' = x_2 \\
x_2' = -\dfrac{g}{L}\sin(x_1)
\end{cases}
$$

* **好处**：

  * 可以直接丢给 ODE solver（如 Python 的 `solve_ivp`）。
  * 符合状态空间建模思想：

    $$
    \dot{\mathbf{x}} = f(\mathbf{x},t)
    $$

* **物理意义**：

  * $x_1$：角度，$x_2$：角速度；
  * 第一条方程：角度变化率等于角速度；
  * 第二条方程：角速度变化率等于角加速度。

---

## 4. 残差形式 vs 一阶系统化的用途

| 形式    | 表达式                                      | 用途                       |
| ----- | ---------------------------------------- | ------------------------ |
| 残差形式  | $R(\theta,\dot{\theta},\ddot{\theta})=0$ | 稳定性分析、构造 Jacobian、代数系统求解 |
| 一阶系统化 | $\mathbf{x}'=f(\mathbf{x},t)$            | 常见数值积分、状态空间建模、控制系统       |

---

👉 你接下来的讲述可以这样铺排：

1. **原始二阶非线性 ODE**（你已经写了）
2. **残差形式**（便于统一处理和求 Jacobian）
3. **一阶系统化**（转为数值积分可解的形式）

---

## 一阶系统化 (First-order System Transformation)

### 1. 基本思路

* 高阶 ODE 一般不方便直接数值求解。
* 所以我们**引入新的状态变量**，把高阶导数逐级“降阶”，直到全部变成一阶。
* 目标是把方程写成：

  $$
  \dot{\mathbf{x}} = f(\mathbf{x}, t)
  $$

  其中 $\mathbf{x}$ 是状态向量。

---

### 2. 单摆的例子

原始方程（二阶）：

$$
\ddot{\theta}(t) + \frac{g}{L}\sin\theta(t) = 0
$$

**步骤：**

1. 定义新的变量：

   $$
   x_1 = \theta \quad (\text{角度}), \quad x_2 = \dot{\theta} \quad (\text{角速度})
   $$

2. 根据定义：

   $$
   x_1' = \dot{x}_1 = \dot{\theta} = x_2
   $$

3. 原方程改写为：

   $$
   x_2' = \ddot{\theta} = -\frac{g}{L}\sin(x_1)
   $$

**最终得到的一阶系统：**

$$
\begin{cases}
x_1' = x_2 \\
x_2' = -\dfrac{g}{L}\sin(x_1)
\end{cases}
$$

---

### 3. 一阶系统的优点

* **统一性**：不管是二阶、三阶，统统转成“一阶系统”后，ODE 求解器都能处理。
* **状态空间形式**：和控制论、信号处理等领域的标准建模方法完全一致。

  $$
  \dot{\mathbf{x}} = f(\mathbf{x},t), \quad \mathbf{x}(0)=\mathbf{x}_0
  $$
* **数值计算**：

  * Runge–Kutta (RK4) 等方法要求一阶形式；
  * Python `scipy.integrate.solve_ivp` 也是要求你传入 $\dot{\mathbf{x}}=f(\mathbf{x},t)$。
* **扩展性**：

  * 方便加上阻尼、外力：

    $$
    x_2' = -\frac{g}{L}\sin(x_1) - c x_2 + F(t)
    $$

---

### 4. 一阶系统的物理解释

* **状态变量**：

  * $x_1$：摆的位置（角度）；
  * $x_2$：摆的速度（角速度）。

* **演化规律**：

  * 第一条方程：角度的变化率就是角速度；
  * 第二条方程：角速度的变化率由重力恢复力决定。

---

### 5. 推广

* 任意 $n$ 阶 ODE：

  $$
  y^{(n)} = F(t,y,y',\dots,y^{(n-1)})
  $$

  都可以引入 $n$ 个状态变量：

  $$
  x_1=y, \; x_2=y', \; \dots, \; x_n=y^{(n-1)}
  $$

  得到：

  $$
  \dot{x}_1=x_2,\;\; \dot{x}_2=x_3,\;\; \dots,\;\; \dot{x}_n=F(t,x_1,\dots,x_n)
  $$

---

✅ **总结**：
一阶系统化就是一种“降阶展开”的过程：

* **数学上**：把任意高阶 ODE 化成一阶系统；
* **物理上**：引入“状态变量”，把系统描述为“状态随时间演化”；
* **计算上**：方便使用标准 ODE 积分器直接数值求解。

---

要不要我帮你写一个 **Python 代码示例**（用 `solve_ivp` 解单摆的一阶系统），这样你能看到从理论公式到数值解的完整闭环？
