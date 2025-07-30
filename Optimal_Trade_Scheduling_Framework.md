
# 🧮 Mathematical Framework for Optimal Trade Scheduling

## 🎯 Problem Statement

We aim to **optimally schedule trades** across a trading day to minimize execution cost due to *temporary market impact*. Let:

- \( T \): total number of time intervals (e.g. minutes)
- \( S \): total number of shares to buy
- \( x_t \): number of shares to trade at time \( t \)
- \( g_t(x_t) \): temporary impact function at time \( t \)

The **execution cost** at time \( t \) is defined as:

\[
C_t(x_t) = x_t \cdot g_t(x_t)
\]

Empirically, we estimate:

\[
g_t(x_t) = a_t \cdot x_t^{\alpha_t} \quad \text{with } \alpha_t < 1
\Rightarrow C_t(x_t) = a_t \cdot x_t^{1 + \alpha_t}
\]

---

## 🔢 Optimization Formulation

We want to minimize total cost:

\[
\min_{x_1, \dots, x_T} \sum_{t=1}^T a_t \cdot x_t^{1 + \alpha_t}
\quad \text{s.t.} \quad \sum_{t=1}^T x_t = S,\quad x_t \geq 0
\]

This is a **convex optimization problem** when \( \alpha_t > 0 \), enabling efficient solution using tools like `cvxpy`.

---

## 🧠 Algorithmic Approaches

### 1. **Closed-form (if \( \alpha_t = \alpha \) fixed)**

Using Lagrangian:

\[
\mathcal{L} = \sum_{t=1}^T a_t x_t^{1 + \alpha} - \lambda \left( \sum_{t=1}^T x_t - S \right)
\Rightarrow
x_t^* = S \cdot \frac{(a_t)^{-\frac{1}{\alpha}}}{\sum_{j=1}^T (a_j)^{-\frac{1}{\alpha}}}
\]

---

### 2. **Convex Programming (general \( \alpha_t \))**

We use:

```python
import cvxpy as cp

x = cp.Variable(T)
cost = cp.sum(cp.multiply(a_t, cp.power(x, 1 + alpha_t)))
constraints = [cp.sum(x) == S, x >= 0]
problem = cp.Problem(cp.Minimize(cost), constraints)
problem.solve()
```

This method works when the cost function is DCP-compliant (Disciplined Convex Programming).

---

### 3. **Fallback: Interpolated Objective**

When \( \alpha_t \) values make the problem non-DCP (e.g. fractional powers), use:

- Precompute discrete \( g_t(x) \cdot x \) from data
- Use linear interpolation and numeric optimization

---

## ⚙️ Tools & Libraries

- `cvxpy`: convex optimization solver
- `numpy`, `pandas`: data handling
- `scipy.optimize.curve_fit`: fit power-law models
- `matplotlib`: visual inspection of \( g_t(x) \)

---

## ✅ Summary

We propose a convex formulation that minimizes temporary impact cost under a fixed volume constraint. The function \( g_t(x) = a_t x^{\alpha_t} \) is fit empirically from LOB snapshots. Depending on DCP compliance, we use closed-form, convex solvers, or interpolated search to compute optimal allocations \( x_t \).

