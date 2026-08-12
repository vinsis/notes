# Another proof of Fibonacci sequence in classic probability puzzle

Recently [Scientific American reported](https://www.scientificamerican.com/article/students-find-hidden-fibonacci-sequence-in-classic-probability-puzzle/) that Fibonacci sequence makes an unexpected appearance in the solution to the following problem:

> If I have `n` sticks with random lengths between 0 and 1, what is the probability that no three of those sticks can form a triangle?

The answer to this puzzle is

$$\begin{aligned}
P(n) &= \prod_{i=1}^{n} \frac{1}{\amber{F_i}} \\
     &= \frac{1}{\amber{1}} \cdot \frac{1}{\amber{1}} \cdot \frac{1}{\amber{2}} \cdot \frac{1}{\amber{3}} \cdots \frac{1}{\amber{F_n}}
\end{aligned}$$

where $\amber{F_i}$ is the $i$-th Fibonacci number. [This](https://arxiv.org/abs/2504.19911) is the arxiv link for the paper which contains a proof of it. Someone [posted](https://old.reddit.com/r/math/comments/1mk27de/students_find_hidden_fibonacci_sequence_in/n7fqmw3/) a novel proof of this puzzle which is more linear algebraic in nature. I made a small modification on top of this proof which is what I present here. Most of the credit to this proof goes to the Reddit user.

---

## 0. Outline of the proof

{}(\clear)
{}(\load-colors)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0.2)

Here is the overall idea:
- {}(text-0 = \text "Step 1 · Sort the lengths: l₁ ≤ l₂ ≤ … ≤ lₙ") {}(step-0 = \annotate-text-box text-0 -2.9 1.15 13 4 1.15) {Sort the lengths}(\set-stroke step-0 COLOR-BLUE) $l_i$ from shortest to longest.
- The vector of sorted lengths can be shown to be a {}(flow-a-0 = \point -0.75 1.15) {}(flow-b-0 = \point 0.75 1.15) {}(flow-0 = \annotate-arrow flow-a-0 flow-b-0 0) {}(\hide flow-a-0 flow-b-0) {}(\set-stroke flow-0 COLOR-GRAY) {}(text-1 = \text "Step 2 · Write l as a matrix times the vector of gaps ε") {}(step-1 = \annotate-text-box text-1 2.9 1.15 13 4 1.15) {linear combination}(\set-stroke step-1 COLOR-BLUE) of a certain basis.
- Once we get this basis, we use it {}(flow-a-1 = \point 2.9 0.3) {}(flow-b-1 = \point 2.9 -0.3) {}(flow-1 = \annotate-arrow flow-a-1 flow-b-1 0) {}(\hide flow-a-1 flow-b-1) {}(\set-stroke flow-1 COLOR-GRAY) {}(text-2 = \text "Step 3 · Read off the longest length lₙ in that basis") {}(step-2 = \annotate-text-box text-2 2.9 -1.15 13 4 1.15) {express the longest length}(\set-stroke step-2 COLOR-AMBER) $l_n$ in terms of it.
- Finally we use the condition that $\text{longest length} \le 1$ to {}(flow-a-2 = \point 0.75 -1.15) {}(flow-b-2 = \point -0.75 -1.15) {}(flow-2 = \annotate-arrow flow-a-2 flow-b-2 0) {}(\hide flow-a-2 flow-b-2) {}(\set-stroke flow-2 COLOR-GRAY) {}(text-3 = \text "Step 4 · Impose lₙ ≤ 1 and get a simplex") {}(step-3 = \annotate-text-box text-3 -2.9 -1.15 13 4 1.15) {get a simplex}(\set-stroke step-3 COLOR-AMBER).

These steps are implemented below:

---

## 1. Steps 1 and 2 - Expressing sorted stick lengths as a matrix-vector product

We do this for two cases - the case where the lengths can not form a triangle and the general case (the lengths may or may not form a triangle).

### 1.1 Lengths which cannot form a triangle

{}(\clear)
{}(\load-colors)
{}(unit = \scalar 60)
{}(grid-opacity = \scalar 0.25)

Given the {}(corner-l1-0 = \point -2.5 2.05) {}(bar-l1-0 = \rectangle corner-l1-0 1.2 0.42) {}(\set-stroke bar-l1-0 COLOR-BLUE) {}(\hide corner-l1-0) {}(text-4 = \text "l₁") {}(tbox-0 = \annotate-text-box text-4 -3.05 2.26 16) {}(text-5 = \text "ε₁") {shortest length}(tbox-1 = \annotate-text-box text-5 -1.9 2.26 13) is $\blue{l_1} = \teal{\varepsilon_1}$, we have that {}(corner-l2-0 = \point -2.5 0.95) {}(bar-l2-0 = \rectangle corner-l2-0 1.2 0.42) {}(\set-stroke bar-l2-0 COLOR-BLUE) {}(corner-l2-1 = \point -1.3 0.95) {}(bar-l2-1 = \rectangle corner-l2-1 0.8 0.42) {}(\set-stroke bar-l2-1 COLOR-AMBER) {}(\hide corner-l2-0 corner-l2-1) {}(text-6 = \text "l₂") {}(tbox-2 = \annotate-text-box text-6 -3.05 1.16 16) {}(text-7 = \text "l₁") {}(tbox-3 = \annotate-text-box text-7 -1.9 1.16 13) {}(text-8 = \text "ε₂") {$l_2 = \blue{l_1} + \teal{\varepsilon_2}$}(tbox-4 = \annotate-text-box text-8 -0.9 1.16 13). Note that $l_3$ {}(corner-l3-0 = \point -2.5 -0.35) {}(bar-l3-0 = \rectangle corner-l3-0 1.2 0.42) {}(\set-stroke bar-l3-0 COLOR-BLUE) {}(corner-l3-1 = \point -1.3 -0.35) {}(bar-l3-1 = \rectangle corner-l3-1 1.2 0.42) {}(\set-stroke bar-l3-1 COLOR-BLUE) {}(corner-l3-2 = \point -0.10000000000000009 -0.35) {}(bar-l3-2 = \rectangle corner-l3-2 0.8 0.42) {}(\set-stroke bar-l3-2 COLOR-AMBER) {}(\hide corner-l3-0 corner-l3-1 corner-l3-2) {}(text-9 = \text "l₃") {}(tbox-5 = \annotate-text-box text-9 -3.05 -0.13999999999999999 16) {}(text-10 = \text "l₁") {}(tbox-6 = \annotate-text-box text-10 -1.9 -0.13999999999999999 13) {}(text-11 = \text "l₁") {}(tbox-7 = \annotate-text-box text-11 -0.7000000000000001 -0.13999999999999999 13) {}(text-12 = \text "ε₂") {}(tbox-8 = \annotate-text-box text-12 0.29999999999999993 -0.13999999999999999 13) {}(b1 = \point -2.5 -0.53) {}(b2 = \point 0.7 -0.53) {}(\hide b1 b2) {}(text-13 = \text "l₁ + l₂  (the failure threshold)") {has to be bigger than}(brace-0 = \annotate-curly-bracket b1 b2 text-13) $l_1 + l_2 = 2\blue{l_1} + \teal{\varepsilon_2}$. {}(corner-l3-3 = \point 0.7000000000000002 -0.35) {}(bar-l3-3 = \rectangle corner-l3-3 0.9 0.42) {}(\set-stroke bar-l3-3 COLOR-GREEN) {}(\hide corner-l3-3) {}(text-14 = \text "ε₃") {}(tbox-9 = \annotate-text-box text-14 1.1500000000000001 -0.13999999999999999 13) {}(tip = \point 1.6 -0.13) {}(elbow = \point 2.5 1) {}(text-15 = \text "slack ε₃ ≥ 0") {}(lead-0 = \annotate-leader-line tip elbow text-15) {}(\hide tip elbow) {}(text-16 = \text "Sort the sticks. Three of them fail to form a triangle exactly when the longest is at least the sum of the other two, so l₃ = 2l₁ + ε₂ + ε₃ with every ε ≥ 0. Each new stick swallows the two before it, which is where the Fibonacci coefficients are born.") {We can assume that}(tbox-10 = \annotate-text-box text-16 0 -3 13 8 -1) $l_3 = 2\blue{l_1} + \teal{\varepsilon_2} + \teal{\varepsilon_3}$. {}(\clear) {}(\load-colors) {}(unit = \scalar 58) {}(grid-opacity = \scalar 0.25) {}(fcorner-0-0 = \point -2.3 2.4) {}(fbar-0-0 = \rectangle fcorner-0-0 0.5 0.4) {}(\set-stroke fbar-0-0 COLOR-GRAY) {}(fcorner-1-0 = \point -2.3 1.58) {}(fbar-1-0 = \rectangle fcorner-1-0 0.5 0.4) {}(\set-stroke fbar-1-0 COLOR-GRAY) {}(fcorner-2-0 = \point -2.3 0.76) {}(fbar-2-0 = \rectangle fcorner-2-0 0.5 0.4) {}(\set-stroke fbar-2-0 COLOR-BLUE) {}(fcorner-2-1 = \point -1.7999999999999998 0.76) {}(fbar-2-1 = \rectangle fcorner-2-1 0.5 0.4) {}(\set-stroke fbar-2-1 COLOR-AMBER) {}(fcorner-3-0 = \point -2.3 -0.06000000000000005) {}(fbar-3-0 = \rectangle fcorner-3-0 1 0.4) {}(\set-stroke fbar-3-0 COLOR-BLUE) {}(fcorner-3-1 = \point -1.2999999999999998 -0.06000000000000005) {}(fbar-3-1 = \rectangle fcorner-3-1 0.5 0.4) {}(\set-stroke fbar-3-1 COLOR-AMBER) {}(fcorner-4-0 = \point -2.3 -0.8799999999999999) {}(fbar-4-0 = \rectangle fcorner-4-0 1.5 0.4) {}(\set-stroke fbar-4-0 COLOR-BLUE) {}(fcorner-4-1 = \point -0.7999999999999998 -0.8799999999999999) {}(fbar-4-1 = \rectangle fcorner-4-1 1 0.4) {}(\set-stroke fbar-4-1 COLOR-AMBER) {}(fcorner-5-0 = \point -2.3 -1.6999999999999997) {}(fbar-5-0 = \rectangle fcorner-5-0 2.5 0.4) {}(\set-stroke fbar-5-0 COLOR-BLUE) {}(fcorner-5-1 = \point 0.20000000000000018 -1.6999999999999997) {}(fbar-5-1 = \rectangle fcorner-5-1 1.5 0.4) {}(\set-stroke fbar-5-1 COLOR-AMBER) {}(\hide fcorner-0-0 fcorner-1-0 fcorner-2-0 fcorner-2-1 fcorner-3-0 fcorner-3-1 fcorner-4-0 fcorner-4-1 fcorner-5-0 fcorner-5-1) {}(a1 = \point 1.85 -1.4999999999999998) {}(a2 = \point -0.6499999999999998 0.13999999999999996) {}(ctrl = \point 3.6 -0.6799999999999999) {}(text-17 = \text "F[i] = F[i-1] + F[i-2]") {}(grow = \annotate-curved-arrow a2 a1 ctrl 0.06 text-17) {}(\set-stroke grow COLOR-VIOLET) {}(\hide a1 a2 ctrl) {}(text-18 = \text "F₁") {}(tbox-11 = \annotate-text-box text-18 -2.8499999999999996 2.6 15) {}(text-19 = \text "= 1") {}(tbox-12 = \annotate-text-box text-19 -1.2499999999999998 2.6 14) {}(text-20 = \text "F₂") {}(tbox-13 = \annotate-text-box text-20 -2.8499999999999996 1.78 15) {}(text-21 = \text "= 1") {}(tbox-14 = \annotate-text-box text-21 -1.2499999999999998 1.78 14) {}(text-22 = \text "F₃") {}(tbox-15 = \annotate-text-box text-22 -2.8499999999999996 0.96 15) {}(text-23 = \text "F₂") {}(tbox-16 = \annotate-text-box text-23 -2.05 0.96 12) {}(text-24 = \text "F₁") {}(tbox-17 = \annotate-text-box text-24 -1.5499999999999998 0.96 12) {}(text-25 = \text "= 2") {Using the same argument}(tbox-18 = \annotate-text-box text-25 -0.7499999999999998 0.96 14), the {}(text-26 = \text "F₄") {}(tbox-19 = \annotate-text-box text-26 -2.8499999999999996 0.13999999999999996 15) {}(text-27 = \text "F₃") {}(tbox-20 = \annotate-text-box text-27 -1.7999999999999998 0.13999999999999996 12) {}(text-28 = \text "F₂") {}(tbox-21 = \annotate-text-box text-28 -1.0499999999999998 0.13999999999999996 12) {}(text-29 = \text "= 3") {}(tbox-22 = \annotate-text-box text-29 -0.24999999999999978 0.13999999999999996 14) {}(text-30 = \text "F₅") {}(tbox-23 = \annotate-text-box text-30 -2.8499999999999996 -0.6799999999999999 15) {}(text-31 = \text "F₄") {}(tbox-24 = \annotate-text-box text-31 -1.5499999999999998 -0.6799999999999999 12) {}(text-32 = \text "F₃") {}(tbox-25 = \annotate-text-box text-32 -0.2999999999999998 -0.6799999999999999 12) {}(text-33 = \text "= 5") {}(tbox-26 = \annotate-text-box text-33 0.7500000000000002 -0.6799999999999999 14) {}(text-34 = \text "F₆") {}(tbox-27 = \annotate-text-box text-34 -2.8499999999999996 -1.4999999999999998 15) {}(text-35 = \text "F₅") {}(tbox-28 = \annotate-text-box text-35 -1.0499999999999998 -1.4999999999999998 12) {}(text-36 = \text "F₄") {}(tbox-29 = \annotate-text-box text-36 0.9500000000000002 -1.4999999999999998 12) {}(text-37 = \text "= 8") {next three lengths}(tbox-30 = \annotate-text-box text-37 2.25 -1.4999999999999998 14) can be written down as:

$$\begin{aligned}
l_4 &= \amber{3}\blue{l_1} + \amber{2}\teal{\varepsilon_2} + \amber{1}\teal{\varepsilon_3} + \amber{1}\teal{\varepsilon_4} \\ \\
l_5 &= \amber{5}\blue{l_1} + \amber{3}\teal{\varepsilon_2} + \amber{2}\teal{\varepsilon_3} + \amber{1}\teal{\varepsilon_4} + \amber{1}\teal{\varepsilon_5} \\ \\
l_6 &= \amber{8}\blue{l_1} + \amber{5}\teal{\varepsilon_2} + \amber{3}\teal{\varepsilon_3} + \amber{2}\teal{\varepsilon_4} + \amber{1}\teal{\varepsilon_5} + \amber{1}\teal{\varepsilon_6} \\ \\
&\;\;\vdots \\ \\
l_n &= \underbrace{\amber{F_n}\blue{l_1} + \amber{F_{n-1}}\teal{\varepsilon_2} + \amber{F_{n-2}}\teal{\varepsilon_3} + \cdots + \amber{F_1}\teal{\varepsilon_n}}_{\text{\amber{Fibonacci}} \text{ coefficients on the } \text{\teal{gaps}}}
\end{aligned}$$

Thus {}(text-38 = \text "These coefficients are exactly the first column of the matrix: 1, 1, 2, 3, 5, 8, …, Fₙ, with the shifted copies filling the columns to its right.") {the vector of sorted lengths}(tbox-31 = \annotate-text-box text-38 0 -3 13 7.6 -1) can be written down as:

Written out for $n = 6$, with one color per Fibonacci number ($\lime{1}$, $\pink{2}$, $\green{3}$, $\orange{5}$, $\violet{8}$), every entry is visible at once:

$$\begin{bmatrix}
l_1 \\ l_2 \\ l_3 \\ l_4 \\ l_5 \\ l_6
\end{bmatrix} =
\underbrace{\begin{bmatrix}
\lime{1}   & \grey{0}   & \grey{0}   & \grey{0} & \grey{0} & \grey{0} \\
\lime{1}   & \lime{1}   & \grey{0}   & \grey{0} & \grey{0} & \grey{0} \\
\pink{2}   & \lime{1}   & \lime{1}   & \grey{0} & \grey{0} & \grey{0} \\
\green{3}  & \pink{2}   & \lime{1}   & \lime{1} & \grey{0} & \grey{0} \\
\orange{5} & \green{3}  & \pink{2}   & \lime{1} & \lime{1} & \grey{0} \\
\violet{8} & \orange{5} & \green{3}  & \pink{2} & \lime{1} & \lime{1}
\end{bmatrix}}_{\substack{\text{each color runs down a diagonal:} \\ \text{the same } F \text{ moves one row down} \\ \text{as it moves one column right}}}
\underbrace{\begin{bmatrix}
\teal{\varepsilon_1} \\ \teal{\varepsilon_2} \\ \teal{\varepsilon_3} \\ \teal{\varepsilon_4} \\ \teal{\varepsilon_5} \\ \teal{\varepsilon_6}
\end{bmatrix}}_{\text{\teal{gaps}} \ \ge 0}$$

The general entry is

$$M_{ij} = \begin{cases}
F_{\,i-j+1} & j \le i \\ \\
\grey{0}    & j > i
\end{cases}$$

### 1.2 Any arbitrary vector of lengths

This case is much simpler. As before, start with $\blue{l_1} = \teal{\varepsilon_1}$. Then since $l_2 \ge \blue{l_1}$, we have that $l_2 = \blue{l_1} + \teal{\varepsilon_2}$ and so on. For sake of completion, the remaining lengths look something like:

$$\begin{aligned}
l_3 &= \blue{l_1} + \teal{\varepsilon_2} + \teal{\varepsilon_3} \\
l_4 &= \blue{l_1} + \teal{\varepsilon_2} + \teal{\varepsilon_3} + \teal{\varepsilon_4} \\
&\;\;\vdots \\
l_n &= \underbrace{\blue{l_1} + \teal{\varepsilon_2} + \teal{\varepsilon_3} + \cdots + \teal{\varepsilon_n}}_{\text{every coefficient is now } 1}
\end{aligned}$$

In this case, the vector of sorted lengths can be written down as:

$$\begin{bmatrix}
l_1 \\ l_2 \\ l_3 \\ l_4 \\ l_5 \\ l_6
\end{bmatrix} =
\underbrace{\begin{bmatrix}
1 & 0 & 0 & 0 & 0 & 0 \\
1 & 1 & 0 & 0 & 0 & 0 \\
1 & 1 & 1 & 0 & 0 & 0 \\
1 & 1 & 1 & 1 & 0 & 0 \\
1 & 1 & 1 & 1 & 1 & 0 \\
1 & 1 & 1 & 1 & 1 & 1
\end{bmatrix}}_{\substack{\text{every Fibonacci coefficient} \\ \text{is replaced by } 1}}
\underbrace{\begin{bmatrix}
\teal{\varepsilon_1} \\ \teal{\varepsilon_2} \\ \teal{\varepsilon_3} \\ \teal{\varepsilon_4} \\ \teal{\varepsilon_5} \\ \teal{\varepsilon_6}
\end{bmatrix}}_{\text{\teal{gaps}} \ \ge 0}$$

The general entry is

$$M_{ij} = \begin{cases}
1 & j \le i \\ \\
0 & j > i
\end{cases}$$

---

## 2. Step 3 - largest length in each case

### 2.1 First case

In this case, the largest length is

$$l_n = \sum_{i=1}^{n} \amber{F_i} \, y_i$$

for positive coefficients $y_i$.

### 2.2 Second case

In this case, the largest length is

$$l_n = \sum_{i=1}^{n} x_i$$

for positive coefficients $x_i$: the same sum with every $\amber{F_i}$ replaced by $1$.

---

## 3. Step 4 - obtain simplexes for each case

{}(\clear)
{}(\load-colors)
{}(unit = \scalar 95)
{}(grid-opacity = \scalar 0.2)

Since these lengths are {}(origin = \point -1.3 -1.05) {}(x-end = \point 1.7 -1.05) {}(y-end = \point -1.3 1.95) {}(ax = \line origin x-end) {}(ay = \line origin y-end) {}(\set-stroke ax COLOR-GRAY) {}(\set-stroke ay COLOR-GRAY) {}(\hide origin x-end y-end) {}(text-39 = \text "x₁ , y₁") {}(tbox-32 = \annotate-text-box text-39 1.8499999999999999 -1.07 14) {}(text-40 = \text "x₂ , y₂") {sampled from the interval}(tbox-33 = \annotate-text-box text-40 -1.35 2.1500000000000004 14) $[0,1]$, they have to be less than or equal to one. Applying this condition to each of the largest lengths above we get:

i) $\sum_{i=1}^{n} \amber{F_i} \, y_i \le 1$: the points that satisfy this condition form a {}(fx = \point 1.2 -1.05) {}(fy = \point -1.3 0.19999999999999996) {}(scaled = \triangle origin fx fy) {}(\hide fx fy) {}(text-41 = \text "F₁y₁ + F₂y₂ ≤ 1") {}(tbox-34 = \annotate-text-box text-41 0.34999999999999987 -0.5 14 -1) {}(d3 = \point -2.2 0.19999999999999996) {}(d4 = \point -2.2 -1.05) {}(text-42 = \text "1/F₂") {}(dim-0 = \annotate-dim-line d3 d4 text-42) {}(\hide d3 d4) {}(\set-stroke scaled COLOR-AMBER) {}(text-43 = \text "V₁") {}(v1 = \annotate-text-box text-43 -0.8800000000000001 -0.75 15) {volume $\amber{V_1}$}(\set-fill v1 COLOR-AMBER). This is the volume of the simplex spanned by the origin and the vectors

$$\underbrace{\frac{1}{\amber{F_i}} \, e_i}_{\text{standard basis vector } e_i \text{ shrunk by } \amber{F_i}}$$

for $i$ from $1$ to $n$.

ii) $\sum_{i=1}^{n} x_i \le 1$: this is the {}(sx = \point 1.2 -1.05) {}(sy = \point -1.3 1.45) {}(std = \triangle origin sx sy) {}(\hide sx sy) {}(text-44 = \text "x₁ + x₂ ≤ 1") {}(tbox-35 = \annotate-text-box text-44 0.25 0.8499999999999999 14 -1) {}(d1 = \point -1.55 -1.05) {}(d2 = \point -1.55 1.45) {}(text-45 = \text "1") {}(dim-1 = \annotate-dim-line d2 d1 text-45) {}(\hide d1 d2) {}(\set-stroke std COLOR-BLUE) {}(text-46 = \text "V₂") {}(v2 = \annotate-text-box text-46 -0.25 0.30000000000000004 15) {}(\set-fill v2 COLOR-BLUE) {}(text-47 = \text "With F₁ = 1, F₂ = 2 the amber region V₁ is the standard simplex V₂ with the y₂ axis squeezed by 1/F₂, so V₁ = V₂ / (F₁F₂).") {standard simplex}(tbox-36 = \annotate-text-box text-47 0.2 -2 13 4.6 -1) and has a volume $\blue{V_2}$ of $1/n!$. The exact volume is not needed for the proof though.

---

## 4. Final step

{}(\clear)
{}(\load-colors)
{}(unit = \scalar 62)
{}(grid-opacity = \scalar 0.2)

The {}(o-0 = \point -4 -1) {}(xe-0 = \point -1.5 -1) {}(ye-0 = \point -4 1.4) {}(lx-0 = \line o-0 xe-0) {}(ly-0 = \line o-0 ye-0) {}(\set-stroke lx-0 COLOR-GRAY) {}(\set-stroke ly-0 COLOR-GRAY) {}(vx-0 = \point -2.3 -1) {}(vy-0 = \point -4 0.7) {}(tri-0 = \triangle o-0 vx-0 vy-0) {}(\set-stroke tri-0 COLOR-BLUE) {}(o-1 = \point 0.7 -1) {}(xe-1 = \point 3.2 -1) {}(ye-1 = \point 0.7 1.4) {}(lx-1 = \line o-1 xe-1) {}(ly-1 = \line o-1 ye-1) {}(\set-stroke lx-1 COLOR-GRAY) {}(\set-stroke ly-1 COLOR-GRAY) {}(vx-1 = \point 2.4 -1) {}(vy-1 = \point 0.7 -0.15000000000000002) {}(tri-1 = \triangle o-1 vx-1 vy-1) {}(\set-stroke tri-1 COLOR-AMBER) {}(\hide o-0 xe-0 ye-0 vx-0 vy-0 o-1 xe-1 ye-1 vx-1 vy-1) {}(text-48 = \text "x-space:  x₁ + x₂ ≤ 1") {}(tbox-37 = \annotate-text-box text-48 -3 1.75 14) {}(text-49 = \text "y-space:  F₁y₁ + F₂y₂ ≤ 1") {}(tbox-38 = \annotate-text-box text-49 1.9 1.75 14) {}(text-50 = \text "x₁") {}(tbox-39 = \annotate-text-box text-50 -1.2999999999999998 -1.05 13) {}(text-51 = \text "x₂") {}(tbox-40 = \annotate-text-box text-51 -4.15 1.6 13) {}(text-52 = \text "y₁") {}(tbox-41 = \annotate-text-box text-52 3.4000000000000004 -1.05 13) {}(text-53 = \text "y₂") {probability is given by}(tbox-42 = \annotate-text-box text-53 0.55 1.6 13) $\dfrac{\amber{V_1}}{\blue{V_2}}$. How do we {}(text-54 = \text "V₂") {}(vol2 = \annotate-text-box text-54 -3.45 -0.6 15) {}(\set-fill vol2 COLOR-BLUE) {}(text-55 = \text "V₁") {}(vol1 = \annotate-text-box text-55 1.1199999999999999 -0.72 15) {find $\amber{V_1}$ in terms of $\blue{V_2}$}(\set-fill vol1 COLOR-AMBER)? By {}(a1 = \point -1.2 0.55) {}(a2 = \point 0.35 0.55) {}(text-56 = \text "yᵢ = xᵢ / Fᵢ") {}(arrow = \annotate-arrow a1 a2 0.05 text-56) {}(\hide a1 a2) {defining a map}(\set-stroke arrow COLOR-VIOLET) $y_i = x_i / \amber{F_i}$. It is easy to see that the {}(m1 = \point -4 -1.3) {}(m2 = \point -2.3 -1.3) {}(m3 = \point 0.7 -1.3) {}(m4 = \point 2.4 -1.3) {}(n1 = \point 0.35 -0.15000000000000002) {}(n2 = \point 0.35 -1) {}(\hide m1 m2 m3 m4 n1 n2) {}(text-57 = \text "1") {}(dim-2 = \annotate-dim-line m1 m2 text-57) {}(text-58 = \text "1/F₁") {}(dim-3 = \annotate-dim-line m3 m4 text-58) {}(text-59 = \text "1/F₂") {determinant of the Jacobian}(dim-4 = \annotate-dim-line n1 n2 text-59) of this map is

$$\begin{aligned}
|J| &= \det \begin{bmatrix}
\frac{1}{\amber{F_1}} &        &        &  \\
       & \frac{1}{\amber{F_2}} &        &  \\
       &        & \ddots &  \\
       &        &        & \frac{1}{\amber{F_n}}
\end{bmatrix} \\ \\
    &= \prod_{i=1}^{n} \frac{1}{\amber{F_i}}
\end{aligned}$$

since the map only rescales each axis. Thus the volume $\amber{V_1} = |J| \, \blue{V_2}$, and this gives us the {}(text-60 = \text "The map yᵢ = xᵢ/Fᵢ only rescales the axes, so its Jacobian is diagonal with entries 1/Fᵢ, hence V₁/V₂ = |J| = ∏ᵢ 1/Fᵢ.") {probability to be}(tbox-43 = \annotate-text-box text-60 -0.3 -3.4 13 8.6 -1):

$$\frac{\amber{V_1}}{\blue{V_2}} = \prod_{i=1}^{n} \frac{1}{\amber{F_i}}$$
