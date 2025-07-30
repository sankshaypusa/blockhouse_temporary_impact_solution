# 🧮 Mathematical Framework for Optimal Trade Scheduling

## Problem Statement

Let:

- \( T \): Total number of time intervals (e.g., minutes in a day)
- \( S \): Total number of shares to buy
- \( x_t \): Number of shares to trade at time interval \( t \)
- \( g_t(x_t) \): Temporary impact function at time \( t \)

The **execution cost** at time \( t \) is:
\[
C_t(x_t) = x_t \cdot g_t(x_t)
\]

Empirically, the impact function is modeled as:
\[
g_t(x_t) = a_t \cdot x_t^{\alpha_t}\,, \quad \alpha_t > 0
\]
So the cost per interval is:
\[
C_t(x_t) = a_t \cdot x_t^{1+\alpha_t}
\]

## Optimization Formulation

We wish to minimize total cost:
\[
\begin{align*}
&\min_{x_1, \dots, x_T}~ \sum_{t=1}^{T} a_t \cdot x_t^{1+\alpha_t} \\
&\text{subject to:} \\
&~~~ \sum_{t=1}^{T} x_t = S \\
&~~~ x_t \geq 0,\quad t = 1, \dots, T
\end{align*}
\]

## Closed-Form Solution (If all \( \alpha_t = \alpha \))

Construct the Lagrangian:
\[
\mathcal{L} = \sum_{t=1}^T a_t x_t^{1+\alpha} - \lambda \left(\sum_{t=1}^T x_t - S\right)
\]

Stationarity condition:
\[
\frac{\partial \mathcal{L}}{\partial x_t} = (1+\alpha)a_t x_t^{\alpha} - \lambda = 0
\]
\[
\Rightarrow~ x_t^\ast = \left( \frac{\lambda}{(1+\alpha)a_t} \right)^{1/\alpha}
\]

Sum for the volume constraint:
\[
S = \sum_{t=1}^{T} x_t^\ast = \sum_{t=1}^{T} \left(\frac{\lambda}{(1+\alpha)a_t}\right)^{1/\alpha}
\]

Solve for \( \lambda \):
\[
\lambda = (1+\alpha) \left( \frac{S}{ \sum_{j=1}^{T} a_j^{-1/\alpha} } \right)^\alpha
\]

**Final closed-form solution:**
\[
x_t^\ast = S \cdot \frac{ a_t^{-1/\alpha} }{ \sum_{j=1}^{T} a_j^{-1/\alpha} }
\]

## Algorithmic Approaches

### 1. Fixed Impact Exponent (\( \alpha_t = \alpha \))

\[
x_t^\ast = S \frac{a_t^{-1/\alpha}}{\sum_{j=1}^T a_j^{-1/\alpha}}
\]

### 2. Heterogeneous Exponents — Convex Programming

If exponents differ across time, solve:
\[
\min~ \sum_{t=1}^T a_t x_t^{1+\alpha_t}
\]
subject to total volume and non-negativity, using e.g. [cvxpy](https://www.cvxpy.org/):

