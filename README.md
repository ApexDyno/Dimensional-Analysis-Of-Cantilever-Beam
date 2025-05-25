

---


# 📐 Dimensional Analysis of a Cantilever Beam under Large Deflections

## 🔍 Overview

This project investigates the **nonlinear large-deflection behavior** of a cantilever beam under a **uniformly distributed load**. Traditional linear beam theory breaks down at large deflections, requiring nonlinear analysis to accurately capture beam behavior. To generalize and simplify this behavior, we use **dimensional analysis** to collapse the deflection data across a range of beam lengths (`L`), loads (`q`), and stiffness values (`EI`) into a **universal dimensionless curve**.

---

## 🧠 Why Dimensional Analysis?

Dimensional analysis helps reduce complex systems into simpler, scalable relationships. In this case, we aim to unify deflection data using:

 $$
\frac{δ}{L} = C \cdot \left(\frac{qL^3}{EI}\right)^{α}
 $$

 Where:

* `δ` is the tip deflection,
* `L` is the beam length,
* `q` is the uniform distributed load,
* `EI` is the flexural rigidity,
* `C` and `α` are empirical constants.

This expression:

- Makes the solution **independent of absolute scale**
- Reveals how deflection scales with parameters
- Enables comparison across materials and beam geometries

---

## 📄 File Info

All code is implemented inside a single Jupyter Notebook:

📁 **`Nondimensionalizing_variationOf(_q_,_L_&_EI_)(_Continuous_Load_).ipynb`**

---

## 🧰 Dependencies

Install the required packages before running:

```bash
pip install numpy matplotlib pandas
````

---

## 🗂️ Code Structure (7 Sections in One Notebook)

### ✅ 1. Generate `q_array` (Load Array)

* Create a 2D array of 40 values of uniformly distributed loads `q` for each beam length `L`.
* The range of `q` values increases with `L`, allowing detailed exploration.

### ✅ 2. Newton-Raphson Solver (`helper()` function)

* Solves the **nonlinear differential equation**:

 $$
\frac{d^2v}{dx^2} \Big/ \left(1 + \left(\frac{dv}{dx}\right)^2 \right)^{3/2} = \frac{M(x)}{EI}
$$

* This is an iterative root-finding method to solve the beam deflection profile `v(x)` and extract tip deflection `δ`.

**Tips for improvement**:

* Use analytical Jacobian (if available)
* Include robust stopping conditions
* Handle divergence gracefully

### ✅ 3. Binary Search to Find `EI_min`

* Iteratively narrows down the **minimum value of `EI`** that still allows Newton-Raphson to converge for a given `(L, q)` pair.
* Convergence checked via helper function.
* Implemented using binary search between a lower and upper EI bound.

**Optimization suggestions**:

* Perform search in **log space** for faster convergence
* Cache recent values as guesses for nearby `q`

> **Note:** If you have already computed and saved the `EI_min` values, you do **not** need to rerun this section. Simply load your saved `EI_min` array and proceed directly to **Section 4/Code 4**.

### ✅ 4. Generate `EI_array`

* Once `EI_min` is found, a list of 10 stiffness values is created from `EI_min` to 2000 for each case.
* Used to analyze how tip deflection `δ` reduces with increasing stiffness.

### ✅ 5. Fit Scaling Law (Log-Log Regression)

* Using deflection data across all combinations, fit the relationship:

$$
\log\left(\frac{\delta}{L}\right) = \log(C) + \alpha \cdot \log\left(\frac{qL^3}{EI}\right)
$$

* Fit is performed using **least squares regression** on the log-transformed data.

* Results:

```bash
For N = 250, L = 1, EI is varing between final EI array (Which is generated in code 4 (Building the final 'EI_array')), q is varing between q_array values, w(Relaxation Fcator)  = 0.5, init_guess as uniform, dv_dx_at_firstPoint as FD2
```

  * **α ≈ 1.1220**
  * **C ≈ 1.5713e-01**

These values are used in the final plots to validate scaling behavior.

### ✅ 6. Log-Log Plotting (Universal Curve)

* Plots dimensionless `δ/L` vs scaled load `qL³/EI` on a **log-log scale**.
* Data from all (L, q, EI) combinations collapse beautifully into one curve.

### ✅ 7. Linear Scale Plot

* Same data plotted on **linear axes** to highlight behavior at low and high load regions.
* Useful for identifying transition from linear to nonlinear regimes.

---



## 📊 Data Collapse Plots Summary (Detailed Observations)

We present four key plots that validate our dimensionless scaling law. Below each, find deeper insights into what the curves tell us about beam behavior.

---

### 🔹 1. Log-Log Plot – All Data  
![Image](https://github.com/user-attachments/assets/e397386f-f67b-4804-a9f2-9222450a67c0)

- **What you see:** Nearly all points from tiny loads (left) up to very large loads (right) lie on a straight line in log–log space.  
- **Slope ≈ α:** The straight‐line region (from approximately 10⁻⁴ to 10⁰ on the x‐axis) has a slope of about **1.5**, matching our fitted exponent α ≈ 1.5.  
- **Low‐load regime:** At the far left (small dimensionless loads), the slope is slightly shallower—indicating the beam behaves more “linearly” (small‐deflection theory dominates).  
- **Nonlinear region:** As you move right, the curve steepens exactly with the power‐law slope, showing geometric nonlinearity.  
- **Plateau/drop‐off:** Beyond dimensionless load ≈ 1–2, the points begin to flatten or scatter—this marks the limit where even very stiff beams start to deflect a maximum relative amount.  

**Physical takeaway:** The power‐law holds over nearly three orders of magnitude in load, and deviations at the extremes highlight the transition between small‐ and large‐deflection behavior.

---

---

### 🌈 2. Log-Log Plot – Grouped by L  
![Image](https://github.com/user-attachments/assets/466e1706-f7fa-4ada-a66e-3c5a5d60ea6b)
- **Grouped slopes:** Each color line overlaps the master straight‐line region, reaffirming α ≈ 1.5 across all L.  
- **Low‐load clustering:** At the lower left, shorter beams (blue/orange/green) cluster tightly, while longer beams fan out more—showing small‐deflection linear theory works best for stiff (short) beams.  
- **Consistency check:** The collapse is truly universal—all lengths align on the same log‐log line up to the critical dimensionless load.  

**Physical takeaway:** Length only affects the transition point on the x‐axis, not the slope—confirming our scaling removes L‐dependence in the power‐law regime.

---

### 🟢 3. Linear Plot – All Data  
![Image](https://github.com/user-attachments/assets/4c1c5f2a-e5e0-43fd-98ba-5b358a6c48de)

- **Combined view:** All points plotted together without color‐coding.  
- **Trend:** A rapid rise in δ/L up to ~3.5, then a gentle decline—mirroring the beam going from load‐dominated bending into stiffness saturation.  
- **Scatter at high loads:** Slight spread beyond δ/L ≈ 2.8 shows numeric noise and solver sensitivity when EI_min was just barely sufficient.  

**Physical takeaway:** The plot initially exhibits a rapid increase in normalized tip deflection—reflecting the dominance of bending under rising load—and subsequently transitions to a plateau, indicating that the beam’s flexural rigidity constrains any further deformation.

---

### 🔸 4. Linear Plot – Grouped by L  
![Image](https://github.com/user-attachments/assets/9285816e-361c-4056-a23a-241af41a80ae)

- **Color groups:** Each color is a fixed beam length (L = 1…10).  
- **Overlap quality:** For dimensionless loads up to ~2, curves from all L values collapse almost perfectly—this confirms length‐independence once properly scaled.  
- **High‐load spread:** Past that, longer beams (e.g. L=10 in cyan) deviate slightly earlier than shorter ones, indicating that very long, flexible beams enter extreme nonlinearity sooner.  
- **“Knee” point:** All curves share a visible “knee” around δ/L ≈ 3.2, where deflection growth slows—this is the stiffness‐dominated regime.  

**Physical takeaway:** Even though absolute deflection differs, after scaling by δ/L and (qL³/EI)^α, beam length ceases to matter—up to the stiff‐beam limit.

---





## 🏁 How to Run
# Just open the notebook in Jupyter or VSCode
```bash

jupyter notebook Nondimensionalizing_variationOf(_q_,_L_&_EI_)(_Continuous_Load_).ipynb
```

> **Note:** If you have already computed and saved the `EI_min` values (from Section 3), you do **not** need to rerun Section 3 each time. Simply load your saved `EI_min` array and proceed directly to **Section 4** to build the final `EI_array`.
---

## 🧠 Observations

* `α ≈ 1.1220` confirms strong geometric nonlinearity
* `C ≈ 1.5713e-01` acts as a universal coefficient
* All data from different `(L, q, EI)` collapse onto a single master curve
* The Newton-Raphson method is sensitive to stiffness, which is why we determine `EI_min`

---

---

## 🔭 Future Work

* Add support for point loads and non-uniform loading types
* Extend analysis to simply supported beams
* Include shear deformation using Timoshenko beam theory
* Explore dynamic (time-dependent) loading and vibration
* Validate results with experimental or FEM comparisons
* Build a GUI for interactive analysis and visualization or web app/website

Feel free to enhance simulations by integrating more complex physics, improving numerical methods, or expanding visualization options.
---

## 🤝 Connect with Me

I’d love to connect, collaborate, or just chat about structural analysis, Python simulations, or mechanical engineering!

- 📧 Email: [nitinnishikantadas@gmail.com](mailto:nitinnishikantadas@gmail.com)
- 📞 Phone: +91 7976841096
- 💼 LinkedIn: [LinkedIn Profile](www.linkedin.com/in/nitin-nishikanta-das)
- 💻 GitHub: [https://github.com/ApexDyno](https://github.com/ApexDyno)

Feel free to reach out if you have suggestions, questions, or want to collaborate on a project!





