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

# 1. 单摆控制方程

下面用**能量守恒**把单摆方程一步步推出来；这是最直接、最“由守恒律来”的推导。结论会是

$$
\boxed{\ \ddot\theta+\frac{g}{L}\sin\theta=0\ }
$$

**理想化假设**：细而不可伸长的轻绳/刚杆，摆球质量 $m$，摆长 $L$，铰接无摩擦，仅受重力，无空气阻力。

---

### Step 1：选取广义坐标与几何关系

用偏离竖直向下的夹角 $\theta(t)$ 描述位置。摆球速度大小

$$
v = L\,\dot\theta .
$$

### Step 2：写动能 $T$

$$
T=\frac12 m v^2=\frac12 m L^2 \dot\theta^2 .
$$

### Step 3：写势能 $U$

取最低点为零势能面。摆球升高的竖直高度

$$
h = L(1-\cos\theta)\quad\Rightarrow\quad
U = m g h = m g L(1-\cos\theta).
$$

### Step 4：应用**机械能守恒**

在理想单摆中，无非保守力做功，因此**总机械能守恒**：

$$
E \equiv T+U=\frac12 m L^2 \dot\theta^2 + m g L(1-\cos\theta)=\text{常数}.
$$

### Step 5：对上式对时间求导并置零

$$
\frac{dE}{dt}
= m L^2 \dot\theta\,\ddot\theta + m g L \sin\theta\,\dot\theta = 0 .
$$

### Step 6：提取公共因子并化简为微分方程

将 $\dot\theta$ 提出来（若某瞬间 $\dot\theta=0$，由连续性同样成立）：

$$
\dot\theta\Big( m L^2 \ddot\theta + m g L \sin\theta \Big)=0
\;\;\Rightarrow\;\;
m L^2 \ddot\theta + m g L \sin\theta = 0 .
$$

两边除以 $mL^2$：

$$
\boxed{\ \ddot\theta+\frac{g}{L}\sin\theta=0\ }.
$$

---

## 补充与常见变体

* **无量纲化**：令 $\tau = t\sqrt{g/L}$，则

  $$
  \theta''(\tau) + \sin\theta = 0,
  $$

  其中 $'$ 为对 $\tau$ 的导数。
* **小角度线性化**：$\sin\theta \approx \theta$（$|\theta|\ll1$）得

  $$
  \ddot\theta+\frac{g}{L}\theta=0 \;\Rightarrow\; \theta(t)=A\cos\!\big(\sqrt{g/L}\,t+\phi\big).
  $$
* **第一积分（显式能量式）**：由 Step 4 也可得

  $$
  \frac12 L^2\dot\theta^2 + gL(1-\cos\theta)=\text{常数}
  \;\Rightarrow\;
  \dot\theta^2=\frac{2g}{L}\big(\cos\theta-\cos\theta_{\max}\big),
  $$

  便于分析最大摆角与速度关系。
* **有阻尼/外力时不再能量守恒**：若有粘性阻尼矩 $c\dot\theta$ 或外驱动力矩 $M(t)$，则

  $$
  \ddot\theta+\frac{c}{mL^2}\dot\theta+\frac{g}{L}\sin\theta=\frac{M(t)}{mL^2},
  $$

  这时总机械能不守恒（其变化率等于外力/阻尼功率）。

对的！拿到 **一阶系统的 Jacobian**

$$
J(\theta)=\begin{bmatrix}0&1\\-\cos\theta&0\end{bmatrix}
$$

之后，就可以把你离散数据里的每个 $\theta_i$ **直接代入**来算指标了。

**保守单摆的闭式结果（超省事）：**

* 行列式：$\det J_i=\cos\theta_i$
* 奇异值：$\sigma_{\min,i}=|\cos\theta_i|$、$\sigma_{\max,i}=1$
* 条件数：$\kappa_i=1/|\cos\theta_i|$

**数值例子（弧度）：**

* $\theta=0$: $\det=1$, $\sigma_{\min}=1$, $\kappa=1$（最稳）
* $\theta=\pi/3\approx1.047$: $\cos\theta=0.5\Rightarrow \det=0.5$, $\sigma_{\min}=0.5$, $\kappa=2$
* $\theta=\pi/2-0.01$: $\cos\theta\approx0.01\Rightarrow \sigma_{\min}\approx0.01$, $\kappa\approx100$（接近奇异）
* $\theta=\pi/2$: $\cos\theta=0\Rightarrow \sigma_{\min}=0$, $\kappa=\infty$（奇异边界）

**实操清单：**

1. 确保 $\theta$ 用弧度（必要时 `unwrap`）。
2. 对每个样本计算 $|\cos\theta_i|$；设阈值 $\tau$（如 0.05），做分区：

   * 良态：$|\cos\theta_i|\ge\tau$
   * 边界：$|\cos\theta_i|<\tau$
3. 画时间序列或相图热力图（$|\cos\theta|$、$\kappa$）。
4. 在良态区做你后续的显函数拟合；靠近 $\pi/2+k\pi$ 的样本谨慎/分片处理。

> 若日后加入阻尼 $\zeta$：$J=\begin{bmatrix}0&1\\-\cos\theta&-2\zeta\end{bmatrix}$。这时用样本点数值算 SVD/特征值即可（$\det=\cos\theta$ 仍然是奇异判据）。
