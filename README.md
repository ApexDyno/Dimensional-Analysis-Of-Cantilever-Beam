

---


#  Dimensional Analysis of a Cantilever Beam under Large Deflections

##  Overview

This project investigates the **nonlinear large-deflection behavior** of a cantilever beam under a **uniformly distributed load**. Traditional linear beam theory breaks down at large deflections, requiring nonlinear analysis to accurately capture beam behavior. To generalize and simplify this behavior, we use **dimensional analysis** to collapse the deflection data across a range of beam lengths (`L`), loads (`q`), and stiffness values (`EI`) into a **universal dimensionless curve**.

---

##  Why Dimensional Analysis?

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

##  File Info

All code is implemented inside a single Jupyter Notebook:

📁 **`Dimensional Analysis Of Cantilever Beam.ipynb`**

---

##  Dependencies

Install the required packages before running:

```bash
pip install numpy matplotlib pandas
````

---

## 🗂️ Code Structure (7 Sections in One Notebook)



---

###  1. Newton-Raphson Solver**

This part of the code numerically solves the **nonlinear beam deflection equation** using the **Newton-Raphson method**:

$$
\frac{d^2v}{dx^2} \Big/ \left(1 + \left(\frac{dv}{dx}\right)^2\right)^{3/2} = \frac{M(x)}{EI}
$$

* The equation models large deflection behavior where linear assumptions no longer apply.
* The beam is **discretized using finite differences**, and both the **residual** and **Jacobian** are computed at each iteration.
* The Newton-Raphson loop updates the deflection profile until convergence.
* The result is a full deflection array `v_full`, which gives deflection at every point along the beam, including the tip.

This solver is used repeatedly to compute deflection for various combinations of beam length (L), load (q), and stiffness (EI).

---




###  2. Newton-Raphson Solver (`helper()` function)

* Solves the **nonlinear differential equation**:

 $$
\frac{d^2v}{dx^2} \Big/ \left(1 + \left(\frac{dv}{dx}\right)^2 \right)^{3/2} = \frac{M(x)}{EI}
$$

* This is an iterative root-finding method to solve the beam deflection profile `v(x)` and extract tip deflection `δ`.

**Tips for improvement**:

* Use analytical Jacobian (if available)
* Include robust stopping conditions
* Handle divergence gracefully
  
---

###  3. Binary Search to Find `EI_min`

* Iteratively narrows down the **minimum value of `EI`** that still allows Newton-Raphson to converge for a given `(L, q)` pair.
* Convergence checked via helper function.
* Implemented using binary search between a lower and upper EI bound.

**Optimization suggestions**:

* Perform search in **log space** for faster convergence
* Cache recent values as guesses for nearby `q`

> **Note:** If you have already computed and saved the `EI_min` values, you do **not** need to rerun this section. Simply load your saved `EI_min` array and proceed directly to **Section 4/Code 4**.

---

###  4. Generate `EI_array`

* Once `EI_min` is found, a list of 10 stiffness values is created from `EI_min` to 2000 for each case.
* Used to analyze how tip deflection `δ` reduces with increasing stiffness.
  
---

###  5. Fit Scaling Law (Log-Log Regression)

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


---

###  6. Global Sorting of EI and Deflection for Plotting** 

* After collecting deflection results (`delta_values_NR`) for each (L, q, EI) combination, all values are **flattened** and **paired** with their corresponding EI values.
* These `(EI, δ)` pairs are then **globally sorted by EI** to prepare smooth and properly scaled plots.
* This step ensures that combined linear and log-log plots show **clear trends without data jumps**, improving visualization and comparison across all beam cases.



---

###  7. Logarithmic Scale Plot**

* A **log-log plot** is generated to observe the power-law relationship between normalized deflection $(\delta/L)$ and the load parameter $(qL^3/EI)$.
* After **globally sorting** all data by EI, the plot shows a **smooth collapsed curve**, confirming that results from all (L, q, EI) combinations follow a universal scaling law.
* Color gradients in the plot represent **different EI levels**, helping visualize how stiffness affects nonlinear deflection.

---

###  8. Linear Scale Plot**

* A **linear plot** is also generated using the sorted data to visualize transitions from linear to nonlinear bending.
* At **low loads**, the deflection varies almost linearly, while **at higher loads**, the curve flattens, showing physical limits of bending.
* Colored curves (sorted by EI) make it easier to compare deflection trends and see how increasing stiffness reduces deflection at all load levels.

---






## 📊 Data Collapse Plots Summary (Detailed Observations)

We present four key plots that validate our dimensionless scaling law. Below each, find deeper insights into what the curves tell us about beam behavior.

---

### 🔹 1. Log-Log Plot – All Data  
<img width="940" height="717" alt="Image" src="https://github.com/user-attachments/assets/bda02500-cc79-4789-90e6-1fc234f29d4b" />

- **What you see:** Nearly all points from tiny loads (left) up to very large loads (right) lie on a straight line in log–log space.  
- **Slope ≈ α:** The straight‐line region (from approximately 10⁻⁴ to 10⁰ on the x‐axis) has a slope of about **1.5**, matching our fitted exponent α ≈ 1.5.  
- **Low‐load regime:** At the far left (small dimensionless loads), the slope is slightly shallower—indicating the beam behaves more “linearly” (small‐deflection theory dominates).  
- **Nonlinear region:** As you move right, the curve steepens exactly with the power‐law slope, showing geometric nonlinearity.  
- **Plateau/drop‐off:** Beyond dimensionless load ≈ 1–2, the points begin to flatten or scatter—this marks the limit where even very stiff beams start to deflect a maximum relative amount.  

**Physical takeaway:** The power‐law holds over nearly three orders of magnitude in load, and deviations at the extremes highlight the transition between small‐ and large‐deflection behavior.

---




### 🔹 2. Linear Plot – All Data  
<img width="940" height="696" alt="Image" src="https://github.com/user-attachments/assets/af1875e9-ce8b-4a00-85a8-aa0b15459753" />

- **Combined view:** All points plotted together without color‐coding.  
- **Trend:** A rapid rise in δ/L up to ~3.5, then a gentle decline—mirroring the beam going from load‐dominated bending into stiffness saturation.  
- **Scatter at high loads:** Slight spread beyond δ/L ≈ 2.8 shows numeric noise and solver sensitivity when EI_min was just barely sufficient.  

**Physical takeaway:** The plot initially exhibits a rapid increase in normalized tip deflection—reflecting the dominance of bending under rising load—and subsequently transitions to a plateau, indicating that the beam’s flexural rigidity constrains any further deformation.

---








## 🏁 How to Run
# Just open the notebook in Jupyter or VSCode
```bash

jupyter notebook Nondimensionalizing_variationOf(_q_,_L_&_EI_)(_Continuous_Load_).ipynb
```

> **Note:** If you have already computed and saved the `EI_min` values (from Section 3), you do **not** need to rerun Section 3 each time. Simply load your saved `EI_min` array and proceed directly to **Section 4** to build the final `EI_array`.
---

##  Observations

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





