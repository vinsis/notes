# What linear algebra reveals about alias sampling

The alias sampling method allows sampling from a discrete probability distribution in O(1) time. It requires setting up a table which takes O(n) time. This cost is amortized over O(1) queries. 

This table can actually be seen as a sparse representation of a row stochastic matrix. This allows us view this method through the lens of linear algebra. 

## An example
The alias table for the distribution $[0.3, 0.6, 0.1]$ looks like so:

| Bin $i$ | $\tau_i$ | Alias $j$ | $1-\tau_i$ |
| :-----: | :------: | :-------: | :--------: |
| 1 | 0.9 | 2 | 0.1 |
| 2 | 1.0 | — | 0 |
| 3 | 0.3 | 2 | 0.7 |

Here $\tau = (0.9, 1.0, 0.3)$, and bins $1$ and $3$ both alias to bin $2$. To draw a sample, pick a bin $i$ uniformly at random, then keep $i$ with probability $\tau_i$ and return its alias $j$ otherwise. Bin $2$ has $\tau_2 = 1$, so it never sends mass elsewhere.


This table can also be seen as a {}(\clear) {}(\load-colors) {}(unit = \scalar 55) {}(grid-opacity = \scalar 0) {}(grid-bg-color = \text "#0b1020") {}(e1-center-1 = \point -3.2 0) {}(\hide e1-center-1) {}(e1-node-1 = \circle e1-center-1 0.6) {}(\set-fill e1-node-1 COLOR-GRAY-DARK) {}(\set-stroke e1-node-1 COLOR-WHITE) {}(e1-center-2 = \point 0 0) {}(\hide e1-center-2) {}(e1-node-2 = \circle e1-center-2 0.6) {}(\set-fill e1-node-2 COLOR-GREEN) {}(\set-stroke e1-node-2 COLOR-EMERALD) {}(e1-center-3 = \point 3.2 0) {}(\hide e1-center-3) {}(e1-node-3 = \circle e1-center-3 0.6) {}(\set-fill e1-node-3 COLOR-GRAY-DARK) {}(\set-stroke e1-node-3 COLOR-WHITE) {}(e1-loopa-1 = \point -3.5 0.52) {}(e1-loopb-1 = \point -2.9000000000000004 0.52) {}(e1-loopc-1 = \point -3.2 1.75) {}(\hide e1-loopa-1 e1-loopb-1 e1-loopc-1) {}(e1-loop-1 = \annotate-curved-arrow e1-loopa-1 e1-loopb-1 e1-loopc-1 0) {}(\set-stroke e1-loop-1 COLOR-GREEN) {}(e1-loopa-2 = \point -0.3 0.52) {}(e1-loopb-2 = \point 0.3 0.52) {}(e1-loopc-2 = \point 0 1.75) {}(\hide e1-loopa-2 e1-loopb-2 e1-loopc-2) {}(e1-loop-2 = \annotate-curved-arrow e1-loopa-2 e1-loopb-2 e1-loopc-2 0) {}(\set-stroke e1-loop-2 COLOR-GREEN) {}(e1-loopa-3 = \point 2.9000000000000004 0.52) {}(e1-loopb-3 = \point 3.5 0.52) {}(e1-loopc-3 = \point 3.2 1.75) {}(\hide e1-loopa-3 e1-loopb-3 e1-loopc-3) {}(e1-loop-3 = \annotate-curved-arrow e1-loopa-3 e1-loopb-3 e1-loopc-3 0) {}(\set-stroke e1-loop-3 COLOR-GREEN) {}(e1-edgea-1 = \point -2.6 0) {}(e1-edgeb-1 = \point -0.6 0) {}(\hide e1-edgea-1 e1-edgeb-1) {}(e1-alias-1 = \annotate-arrow e1-edgea-1 e1-edgeb-1 0.06) {}(\set-stroke e1-alias-1 COLOR-AMBER) {}(e1-edgea-3 = \point 2.6 0) {}(e1-edgeb-3 = \point 0.6 0) {}(\hide e1-edgea-3 e1-edgeb-3) {}(e1-alias-3 = \annotate-arrow e1-edgea-3 e1-edgeb-3 0.06) {}(\set-stroke e1-alias-3 COLOR-AMBER) {}(text-0 = \text "1") {}(e1-idx-1 = \annotate-text-box text-0 -3.2 0 20) {}(\set-fill e1-idx-1 COLOR-WHITE) {}(text-1 = \text "τ₁ = 0.9") {}(e1-tau-1 = \annotate-text-box text-1 -3.2 1.98 14) {}(\set-fill e1-tau-1 COLOR-GREEN) {}(text-2 = \text "2") {}(e1-idx-2 = \annotate-text-box text-2 0 0 20) {}(\set-fill e1-idx-2 COLOR-WHITE) {}(text-3 = \text "τ₂ = 1.0") {}(e1-tau-2 = \annotate-text-box text-3 0 1.98 14) {}(\set-fill e1-tau-2 COLOR-GREEN) {}(text-4 = \text "3") {}(e1-idx-3 = \annotate-text-box text-4 3.2 0 20) {}(\set-fill e1-idx-3 COLOR-WHITE) {}(text-5 = \text "τ₃ = 0.3") {}(e1-tau-3 = \annotate-text-box text-5 3.2 1.98 14) {}(\set-fill e1-tau-3 COLOR-GREEN) {}(text-6 = \text "1−τ₁ = 0.1  → bin 2") {}(e1-w-1 = \annotate-text-box text-6 -1.6 -0.75 13) {}(\set-fill e1-w-1 COLOR-AMBER) {}(text-7 = \text "1−τ₃ = 0.7  → bin 2") {}(e1-w-3 = \annotate-text-box text-7 1.6 -0.75 13) {}(\set-fill e1-w-3 COLOR-AMBER) {}(text-8 = \text "absorbing (τ=1)") {}(e1-abs = \annotate-text-box text-8 0 -1.05 12) {}(\set-fill e1-abs COLOR-EMERALD) {}(text-9 = \text "Alias DAG. Each bin keeps itself with probability τᵢ (green self-loop) and jumps to its alias bin with probability 1−τᵢ (amber edge). Bin 2 (τ=1) is absorbing.") {DAG with three nodes}(e1-summary = \annotate-text-box text-9 0 -2.4 13 9.4 -1) if we ignore the self-loops. 

---

## Table → Row stochastic matrix
Note that this table can be seen as a row stochastic matrix where each row has at most two non-zero entries. Row $i$ gets $\tau_i$ on the diagonal
and $1-\tau_i$ in the alias column $j$, and zeros everywhere else.


Here is {}(\clear) {}(\load-colors) {}(unit = \scalar 60) {}(grid-opacity = \scalar 0) {}(grid-bg-color = \text "#0b1020") {}(m-bl-1-1 = \point -1.9500000000000002 0.65) {}(\hide m-bl-1-1) {}(m-cell-1-1 = \square m-bl-1-1 1.3 0) {}(\set-fill m-cell-1-1 COLOR-GREEN) {}(\set-stroke m-cell-1-1 COLOR-WHITE) {}(m-bl-1-2 = \point -0.65 0.65) {}(\hide m-bl-1-2) {}(m-cell-1-2 = \square m-bl-1-2 1.3 0) {}(\set-fill m-cell-1-2 COLOR-AMBER) {}(\set-stroke m-cell-1-2 COLOR-WHITE) {}(m-bl-1-3 = \point 0.65 0.65) {}(\hide m-bl-1-3) {}(m-cell-1-3 = \square m-bl-1-3 1.3 0) {}(\set-fill m-cell-1-3 COLOR-GRAY-DARK) {}(\set-stroke m-cell-1-3 COLOR-GRAY-MID) {}(m-bl-2-1 = \point -1.9500000000000002 -0.65) {}(\hide m-bl-2-1) {}(m-cell-2-1 = \square m-bl-2-1 1.3 0) {}(\set-fill m-cell-2-1 COLOR-GRAY-DARK) {}(\set-stroke m-cell-2-1 COLOR-GRAY-MID) {}(m-bl-2-2 = \point -0.65 -0.65) {}(\hide m-bl-2-2) {}(m-cell-2-2 = \square m-bl-2-2 1.3 0) {}(\set-fill m-cell-2-2 COLOR-EMERALD) {}(\set-stroke m-cell-2-2 COLOR-WHITE) {}(m-bl-2-3 = \point 0.65 -0.65) {}(\hide m-bl-2-3) {}(m-cell-2-3 = \square m-bl-2-3 1.3 0) {}(\set-fill m-cell-2-3 COLOR-GRAY-DARK) {}(\set-stroke m-cell-2-3 COLOR-GRAY-MID) {}(m-bl-3-1 = \point -1.9500000000000002 -1.9500000000000002) {}(\hide m-bl-3-1) {}(m-cell-3-1 = \square m-bl-3-1 1.3 0) {}(\set-fill m-cell-3-1 COLOR-GRAY-DARK) {}(\set-stroke m-cell-3-1 COLOR-GRAY-MID) {}(m-bl-3-2 = \point -0.65 -1.9500000000000002) {}(\hide m-bl-3-2) {}(m-cell-3-2 = \square m-bl-3-2 1.3 0) {}(\set-fill m-cell-3-2 COLOR-AMBER) {}(\set-stroke m-cell-3-2 COLOR-WHITE) {}(m-bl-3-3 = \point 0.65 -1.9500000000000002) {}(\hide m-bl-3-3) {}(m-cell-3-3 = \square m-bl-3-3 1.3 0) {}(\set-fill m-cell-3-3 COLOR-GREEN) {}(\set-stroke m-cell-3-3 COLOR-WHITE) {}(text-10 = \text "0.9") {}(m-v-1-1 = \annotate-text-box text-10 -1.3 1.3 15) {}(\set-fill m-v-1-1 COLOR-WHITE) {}(text-11 = \text "0.1") {}(m-v-1-2 = \annotate-text-box text-11 0 1.3 15) {}(\set-fill m-v-1-2 COLOR-WHITE) {}(text-12 = \text "1.0") {}(m-v-2-2 = \annotate-text-box text-12 0 0 15) {}(\set-fill m-v-2-2 COLOR-WHITE) {}(text-13 = \text "0.7") {}(m-v-3-2 = \annotate-text-box text-13 0 -1.3 15) {}(\set-fill m-v-3-2 COLOR-WHITE) {}(text-14 = \text "0.3") {}(m-v-3-3 = \annotate-text-box text-14 1.3 -1.3 15) {}(\set-fill m-v-3-3 COLOR-WHITE) {}(text-15 = \text "col 1") {}(m-col-1 = \annotate-text-box text-15 -1.3 2.33 11) {}(text-16 = \text "col 2") {}(m-col-2 = \annotate-text-box text-16 0 2.33 11) {}(text-17 = \text "col 3") {}(m-col-3 = \annotate-text-box text-17 1.3 2.33 11) {}(text-18 = \text "row 1") {}(m-row-1 = \annotate-text-box text-18 -2.5500000000000003 1.3 11) {}(text-19 = \text "row 2") {}(m-row-2 = \annotate-text-box text-19 -2.5500000000000003 0 11) {}(text-20 = \text "row 3") {}(m-row-3 = \annotate-text-box text-20 -2.5500000000000003 -1.3 11) {}(text-21 = \text "P   (Example 1)") {}(m-title = \annotate-text-box text-21 0 2.93 15) {}(\set-fill m-title COLOR-WHITE) {}(text-22 = \text "Every row has exactly two nonzeros: τᵢ on the diagonal (green, = the eigenvalues of the triangular-ish P) and 1−τᵢ at the alias column (amber). Row 2 is absorbing (τ=1). Blank cells are 0.") {$P$ as a heatmap}(m-summary = \annotate-text-box text-22 0 -2.95 13 9.4 -1) for the above table.

$$
%id:P
P=\begin{bmatrix} 0.9 & 0.1 & 0 \\ 0 & 1 & 0 \\ 0 & 0.7 & 0.3 \end{bmatrix}
$$


---

## Sampling as a Markov step

Let $v = \begin{bmatrix} \teal{\tfrac13} & \teal{\tfrac13} & \teal{\tfrac13} \end{bmatrix}$ be the
uniform row vector. Then $vP$ is the target.


Watch how the uniform bars, pushed through $P$, {}(\clear) {}(\load-colors) {}(unit = \scalar 60) {}(grid-opacity = \scalar 0) {}(grid-bg-color = \text "#0b1020") {}(vp-lbl-1 = \point -4.425 -1.4) {}(\hide vp-lbl-1) {}(vp-lbar-1 = \rectangle vp-lbl-1 0.55 1.1333333333333333 0) {}(\set-fill vp-lbar-1 COLOR-GRAY) {}(\set-stroke vp-lbar-1 COLOR-WHITE) {}(vp-lbl-2 = \point -3.5749999999999997 -1.4) {}(\hide vp-lbl-2) {}(vp-lbar-2 = \rectangle vp-lbl-2 0.55 1.1333333333333333 0) {}(\set-fill vp-lbar-2 COLOR-GRAY) {}(\set-stroke vp-lbar-2 COLOR-WHITE) {}(vp-lbl-3 = \point -2.7249999999999996 -1.4) {}(\hide vp-lbl-3) {}(vp-lbar-3 = \rectangle vp-lbl-3 0.55 1.1333333333333333 0) {}(\set-fill vp-lbar-3 COLOR-GRAY) {}(\set-stroke vp-lbar-3 COLOR-WHITE) {}(vp-rbl-1 = \point 2.175 -1.4) {}(\hide vp-rbl-1) {}(vp-rbar-1 = \rectangle vp-rbl-1 0.55 1.02 0) {}(\set-fill vp-rbar-1 COLOR-BLUE) {}(\set-stroke vp-rbar-1 COLOR-WHITE) {}(vp-rbl-2 = \point 3.025 -1.4) {}(\hide vp-rbl-2) {}(vp-rbar-2 = \rectangle vp-rbl-2 0.55 2.04 0) {}(\set-fill vp-rbar-2 COLOR-BLUE) {}(\set-stroke vp-rbar-2 COLOR-WHITE) {}(vp-rbl-3 = \point 3.8749999999999996 -1.4) {}(\hide vp-rbl-3) {}(vp-rbar-3 = \rectangle vp-rbl-3 0.55 0.34 0) {}(\set-fill vp-rbar-3 COLOR-BLUE) {}(\set-stroke vp-rbar-3 COLOR-WHITE) {}(vp-ap = \point -1.5 -0.8) {}(vp-bp = \point 1.5 -0.8) {}(\hide vp-ap vp-bp) {}(vp-arrow = \annotate-arrow vp-ap vp-bp 0.02) {}(\set-stroke vp-arrow COLOR-WHITE) {}(text-23 = \text "1/3") {}(vp-lv-1 = \annotate-text-box text-23 -4.1499999999999995 0.01333333333333342 12) {}(text-24 = \text "1") {}(vp-lidx-1 = \annotate-text-box text-24 -4.1499999999999995 -1.72 12) {}(text-25 = \text "1/3") {}(vp-lv-2 = \annotate-text-box text-25 -3.3 0.01333333333333342 12) {}(text-26 = \text "2") {}(vp-lidx-2 = \annotate-text-box text-26 -3.3 -1.72 12) {}(text-27 = \text "1/3") {}(vp-lv-3 = \annotate-text-box text-27 -2.4499999999999997 0.01333333333333342 12) {}(text-28 = \text "3") {}(vp-lidx-3 = \annotate-text-box text-28 -2.4499999999999997 -1.72 12) {}(text-29 = \text "0.3") {}(vp-rv-1 = \annotate-text-box text-29 2.4499999999999997 -0.09999999999999987 12) {}(text-30 = \text "1") {}(vp-ridx-1 = \annotate-text-box text-30 2.4499999999999997 -1.72 12) {}(text-31 = \text "0.6") {}(vp-rv-2 = \annotate-text-box text-31 3.3 0.9200000000000002 12) {}(text-32 = \text "2") {}(vp-ridx-2 = \annotate-text-box text-32 3.3 -1.72 12) {}(text-33 = \text "0.1") {}(vp-rv-3 = \annotate-text-box text-33 4.1499999999999995 -0.7799999999999998 12) {}(text-34 = \text "3") {}(vp-ridx-3 = \annotate-text-box text-34 4.1499999999999995 -1.72 12) {}(text-35 = \text "P") {}(vp-plab = \annotate-text-box text-35 0 -0.6 16) {}(\set-fill vp-plab COLOR-AMBER) {}(text-36 = \text "v = [1/3, 1/3, 1/3]") {}(vp-lg = \annotate-text-box text-36 -3.3 -2.3499999999999996 13) {}(\set-fill vp-lg COLOR-GRAY) {}(text-37 = \text "vP = [0.3, 0.6, 0.1]") {}(vp-rg = \annotate-text-box text-37 3.3 -2.3499999999999996 13) {}(\set-fill vp-rg COLOR-BLUE) {}(text-38 = \text "Multiplying the uniform row vector v by P moves mass along the alias edges and recovers the target distribution vP.") {recover the
target}(vp-summary = \annotate-text-box text-38 0 -3.8 13 10.6 -1) $vP = \text{target}$ on the right. 

$$
\underbrace{\begin{bmatrix} \teal{\tfrac13} & \teal{\tfrac13} & \teal{\tfrac13} \end{bmatrix}}_{\teal{\text{uniform }} v}
\begin{bmatrix} \green{0.9} & \amber{0.1} & 0 \\ 0 & \green{1} & 0 \\ 0 & \amber{0.7} & \green{0.3} \end{bmatrix} =
\underbrace{\begin{bmatrix} \blue{0.3} & \blue{0.6} & \blue{0.1} \end{bmatrix}}_{\text{target } \blue{vP}}
$$

Green entries are the $\tau_i$ a bin keeps for itself, amber entries are the
$1-\tau_i$ it ships down an alias edge. Reading column $2$ of that product shows
where bin $2$'s final $0.6$ comes from:

$$\begin{aligned}
\blue{(vP)_2} &= \teal{\tfrac13}\,\amber{0.1} + \teal{\tfrac13}\,\green{1} + \teal{\tfrac13}\,\amber{0.7} \\ \\
&= \underbrace{\teal{\tfrac13}\amber{(0.1 + 0.7)}}_{\amber{\text{alias mass arriving from bins }1, 3}} + \underbrace{\teal{\tfrac13}\green{(1)}}_{\green{\text{bin }2\text{ keeping its own share}}} \\ \\
&= \blue{0.6}
\end{aligned}$$

---

## Insight 1: $\tau_i$ are the eigenvalues
Note that the rows and columns of this matrix can be shuffled to make it triangular. Thus its diagonal entries are the eigenvalues.

$$
\operatorname{spec}(P) = \{\green{\tau_1}, \green{\tau_2}, \ldots, \green{\tau_n}\}
$$

Also note that since it is a stochastic matrix, its largest eigenvalue is $\max_i \green{\tau_i} = 1$. 

---

## Insight 2: dominant eigenvalues correspond to sinks
Let's use a larger matrix for this: a probability distribution with $5$ states: $0.04, 0.08, 0.12, 0.36, 0.40$.

| Bin $i$ | $\tau_i$ | Alias $j$ | $1-\tau_i$ |
| :-----: | :------: | :-------: | :--------: |
| 1 | 0.2 | 4 | 0.8 |
| 2 | 0.4 | 5 | 0.6 |
| 3 | 0.6 | 5 | 0.4 |
| 4 | 1.0 | — | 0 |
| 5 | 1.0 | — | 0 |

Here $\tau = (0.2, 0.4, 0.6, 1.0, 1.0)$, bin $1$ aliases to $4$, and bins $2, 3$ alias to $5$. Bins $4$ and $5$ have $\tau = 1$, so they are the two sinks.


The DAG corresponding to the alias table {}(\clear) {}(\load-colors) {}(unit = \scalar 50) {}(grid-opacity = \scalar 0) {}(grid-bg-color = \text "#0b1020") {}(e2-center-1 = \point -2.6 2.3) {}(\hide e2-center-1) {}(e2-node-1 = \circle e2-center-1 0.55) {}(\set-fill e2-node-1 COLOR-GRAY-DARK) {}(\set-stroke e2-node-1 COLOR-WHITE) {}(e2-center-2 = \point -2.6 0) {}(\hide e2-center-2) {}(e2-node-2 = \circle e2-center-2 0.55) {}(\set-fill e2-node-2 COLOR-GRAY-DARK) {}(\set-stroke e2-node-2 COLOR-WHITE) {}(e2-center-3 = \point -2.6 -2.3) {}(\hide e2-center-3) {}(e2-node-3 = \circle e2-center-3 0.55) {}(\set-fill e2-node-3 COLOR-GRAY-DARK) {}(\set-stroke e2-node-3 COLOR-WHITE) {}(e2-center-4 = \point 2.6 1.4) {}(\hide e2-center-4) {}(e2-node-4 = \circle e2-center-4 0.55) {}(\set-fill e2-node-4 COLOR-GREEN) {}(\set-stroke e2-node-4 COLOR-EMERALD) {}(e2-center-5 = \point 2.6 -1.4) {}(\hide e2-center-5) {}(e2-node-5 = \circle e2-center-5 0.55) {}(\set-fill e2-node-5 COLOR-GREEN) {}(\set-stroke e2-node-5 COLOR-EMERALD) {}(e2-loopa-1 = \point -3.1 2.5599999999999996) {}(e2-loopb-1 = \point -3.1 2.04) {}(e2-loopc-1 = \point -4.1 2.3) {}(\hide e2-loopa-1 e2-loopb-1 e2-loopc-1) {}(e2-loop-1 = \annotate-curved-arrow e2-loopa-1 e2-loopb-1 e2-loopc-1 0) {}(\set-stroke e2-loop-1 COLOR-GREEN) {}(e2-loopa-2 = \point -3.1 0.26) {}(e2-loopb-2 = \point -3.1 -0.26) {}(e2-loopc-2 = \point -4.1 0) {}(\hide e2-loopa-2 e2-loopb-2 e2-loopc-2) {}(e2-loop-2 = \annotate-curved-arrow e2-loopa-2 e2-loopb-2 e2-loopc-2 0) {}(\set-stroke e2-loop-2 COLOR-GREEN) {}(e2-loopa-3 = \point -3.1 -2.04) {}(e2-loopb-3 = \point -3.1 -2.5599999999999996) {}(e2-loopc-3 = \point -4.1 -2.3) {}(\hide e2-loopa-3 e2-loopb-3 e2-loopc-3) {}(e2-loop-3 = \annotate-curved-arrow e2-loopa-3 e2-loopb-3 e2-loopc-3 0) {}(\set-stroke e2-loop-3 COLOR-GREEN) {}(e2-loopa-4 = \point 3.1 1.66) {}(e2-loopb-4 = \point 3.1 1.14) {}(e2-loopc-4 = \point 4.1 1.4) {}(\hide e2-loopa-4 e2-loopb-4 e2-loopc-4) {}(e2-loop-4 = \annotate-curved-arrow e2-loopa-4 e2-loopb-4 e2-loopc-4 0) {}(\set-stroke e2-loop-4 COLOR-GREEN) {}(e2-loopa-5 = \point 3.1 -1.14) {}(e2-loopb-5 = \point 3.1 -1.66) {}(e2-loopc-5 = \point 4.1 -1.4) {}(\hide e2-loopa-5 e2-loopb-5 e2-loopc-5) {}(e2-loop-5 = \annotate-curved-arrow e2-loopa-5 e2-loopb-5 e2-loopc-5 0) {}(\set-stroke e2-loop-5 COLOR-GREEN) {}(e2-edgea-1 = \point -2.058057221779504 2.206202211461837) {}(e2-edgeb-1 = \point 2.058057221779504 1.4937977885381628) {}(\hide e2-edgea-1 e2-edgeb-1) {}(e2-alias-1 = \annotate-arrow e2-edgea-1 e2-edgeb-1 0.05) {}(\set-stroke e2-alias-1 COLOR-AMBER) {}(e2-edgea-2 = \point -2.0689113328136317 -0.14298541039632992) {}(e2-edgeb-2 = \point 2.0689113328136317 -1.25701458960367) {}(\hide e2-edgea-2 e2-edgeb-2) {}(e2-alias-2 = \annotate-arrow e2-edgea-2 e2-edgeb-2 0.05) {}(\set-stroke e2-alias-2 COLOR-AMBER) {}(e2-edgea-3 = \point -2.058057221779504 -2.206202211461837) {}(e2-edgeb-3 = \point 2.058057221779504 -1.4937977885381628) {}(\hide e2-edgea-3 e2-edgeb-3) {}(e2-alias-3 = \annotate-arrow e2-edgea-3 e2-edgeb-3 0.05) {}(\set-stroke e2-alias-3 COLOR-AMBER) {}(text-39 = \text "1") {}(e2-idx-1 = \annotate-text-box text-39 -2.6 2.3 19) {}(\set-fill e2-idx-1 COLOR-WHITE) {}(text-40 = \text "τ=0.2") {}(e2-tau-1 = \annotate-text-box text-40 -4.45 2.3 13) {}(\set-fill e2-tau-1 COLOR-GREEN) {}(text-41 = \text "2") {}(e2-idx-2 = \annotate-text-box text-41 -2.6 0 19) {}(\set-fill e2-idx-2 COLOR-WHITE) {}(text-42 = \text "τ=0.4") {}(e2-tau-2 = \annotate-text-box text-42 -4.45 0 13) {}(\set-fill e2-tau-2 COLOR-GREEN) {}(text-43 = \text "3") {}(e2-idx-3 = \annotate-text-box text-43 -2.6 -2.3 19) {}(\set-fill e2-idx-3 COLOR-WHITE) {}(text-44 = \text "τ=0.6") {}(e2-tau-3 = \annotate-text-box text-44 -4.45 -2.3 13) {}(\set-fill e2-tau-3 COLOR-GREEN) {}(text-45 = \text "4") {}(e2-idx-4 = \annotate-text-box text-45 2.6 1.4 19) {}(\set-fill e2-idx-4 COLOR-WHITE) {}(text-46 = \text "τ=1.0") {}(e2-tau-4 = \annotate-text-box text-46 4.45 1.4 13) {}(\set-fill e2-tau-4 COLOR-GREEN) {}(text-47 = \text "5") {}(e2-idx-5 = \annotate-text-box text-47 2.6 -1.4 19) {}(\set-fill e2-idx-5 COLOR-WHITE) {}(text-48 = \text "τ=1.0") {}(e2-tau-5 = \annotate-text-box text-48 4.45 -1.4 13) {}(\set-fill e2-tau-5 COLOR-GREEN) {}(text-49 = \text "1−τ = 0.8  → bin 4") {}(e2-w-1 = \annotate-text-box text-49 0 2.05 10 -1) {}(\set-fill e2-w-1 COLOR-AMBER) {}(text-50 = \text "1−τ = 0.6  → bin 5") {}(e2-w-2 = \annotate-text-box text-50 0.15 -0.5 10 -1) {}(\set-fill e2-w-2 COLOR-AMBER) {}(text-51 = \text "1−τ = 0.4  → bin 5") {}(e2-w-3 = \annotate-text-box text-51 0.15 -2.05 10 -1) {}(\set-fill e2-w-3 COLOR-AMBER) {}(text-52 = \text "Example 2 alias DAG. Bins 4 and 5 have τ=1, so they are absorbing (self-loop only, no alias edge).") {looks like this}(e2-summary = \annotate-text-box text-52 0 -4 13 11 -1). Note that it has two sinks (absorbing states). The corresponding stochastic matrix is:

$$
%id:P2
P=\begin{bmatrix}
0.2 & 0 & 0 & 0.8 & 0 \\
0 & 0.4 & 0 & 0 & 0.6 \\
0 & 0 & 0.6 & 0 & 0.4 \\
0 & 0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 1
\end{bmatrix}
$$


with eigenvalues $\{0.2, 0.4, 0.6, \green{1}, \green{1}\}$. The eigenvalue $\lambda = \green{1}$ has multiplicity two, which corresponds to the two bins (or the two connected components).

---

## Insight 3: trace sets the branch probability
How O(1) sampling works: to sample, pick a bin $i$ uniformly, draw
$U \sim \text{Uniform}[0,1)$, and keep $i$ if $U < \tau_i$, otherwise jump to its
alias.


The {}(\clear) {}(\load-colors) {}(unit = \scalar 68) {}(grid-opacity = \scalar 0) {}(grid-bg-color = \text "#0b1020") {}(s-kbl-1 = \point -2.95 -1.5) {}(\hide s-kbl-1) {}(s-keep-1 = \rectangle s-kbl-1 1.9 2.7 0) {}(\set-fill s-keep-1 COLOR-GREEN) {}(\set-stroke s-keep-1 COLOR-WHITE) {}(s-jbl-1 = \point -2.95 1.2000000000000002) {}(\hide s-jbl-1) {}(s-jump-1 = \rectangle s-jbl-1 1.9 0.29999999999999993 0) {}(\set-fill s-jump-1 COLOR-AMBER) {}(\set-stroke s-jump-1 COLOR-WHITE) {}(s-kbl-2 = \point -0.95 -1.5) {}(\hide s-kbl-2) {}(s-keep-2 = \rectangle s-kbl-2 1.9 3 0) {}(\set-fill s-keep-2 COLOR-GREEN) {}(\set-stroke s-keep-2 COLOR-WHITE) {}(s-kbl-3 = \point 1.05 -1.5) {}(\hide s-kbl-3) {}(s-keep-3 = \rectangle s-kbl-3 1.9 0.8999999999999999 0) {}(\set-fill s-keep-3 COLOR-GREEN) {}(\set-stroke s-keep-3 COLOR-WHITE) {}(s-jbl-3 = \point 1.05 -0.6000000000000001) {}(\hide s-jbl-3) {}(s-jump-3 = \rectangle s-jbl-3 1.9 2.0999999999999996 0) {}(\set-fill s-jump-3 COLOR-AMBER) {}(\set-stroke s-jump-3 COLOR-WHITE) {}(text-53 = \text "bin 1") {}(s-bin-1 = \annotate-text-box text-53 -2 -1.85 12) {}(text-54 = \text "keep τ=0.9") {}(s-kl-1 = \annotate-text-box text-54 -2 -0.1499999999999999 11) {}(\set-fill s-kl-1 COLOR-WHITE) {}(text-55 = \text "jump 0.1") {}(s-jl-1 = \annotate-text-box text-55 -2 1.35 11) {}(\set-fill s-jl-1 COLOR-WHITE) {}(text-56 = \text "bin 2") {}(s-bin-2 = \annotate-text-box text-56 0 -1.85 12) {}(text-57 = \text "keep τ=1.0") {}(s-kl-2 = \annotate-text-box text-57 0 0 11) {}(\set-fill s-kl-2 COLOR-WHITE) {}(text-58 = \text "bin 3") {}(s-bin-3 = \annotate-text-box text-58 2 -1.85 12) {}(text-59 = \text "keep τ=0.3") {}(s-kl-3 = \annotate-text-box text-59 2 -1.05 11) {}(\set-fill s-kl-3 COLOR-WHITE) {}(text-60 = \text "jump 0.7") {}(s-jl-3 = \annotate-text-box text-60 2 0.44999999999999973 11) {}(\set-fill s-jl-3 COLOR-WHITE) {}(text-61 = \text "P(keep) = Tr(P)/n = 2.2 / 3 ≈ 0.73") {}(s-title = \annotate-text-box text-61 0 2.15 15) {}(\set-fill s-title COLOR-GREEN) {}(text-62 = \text "O(1) sampling: pick a bin i uniformly, draw U between 0 and 1. Keep i if U<τᵢ (green), else jump to the alias j (amber). The overall keep-probability is the green fraction Tr(P)/n.") {keep-jump split}(s-summary = \annotate-text-box text-62 0 -2.6 13 9.6 -1) stacks each bin's green keep-fraction
$\tau_i$ under its amber jump-fraction $1-\tau_i$. The chance a *random* draw
keeps its first bin is the average of the green heights, and that average is the
normalized **trace**:

$$\begin{aligned}
P(\text{keep}) &= \frac{1}{n}\sum_{i} \underbrace{\green{\tau_i}}_{\green{\text{bin }i\text{ keeps itself}}} \\ \\
&= \frac{\operatorname{Tr}(P)}{n}
\end{aligned}$$

For Example 1, $\operatorname{Tr}(P) = 0.9 + 1.0 + 0.3 = 2.2$, so a draw keeps
its first bin with probability $2.2/3 \approx 0.73$ and follows an alias edge
only about a quarter of the time. 

---

# Insight 4: trace and maximum coupling
Maximum coupling between $X$ and $Y$ is a joint distribution such that $\Pr[X = Y]$ is as large as possible. One way it is defined is:

$$
\max \Pr[X = Y] = \sum_i \min\big(\Pr[X = i],\, \Pr[Y = i]\big)
$$

Here the bin we land on is uniform, $\Pr[X = i] = \pink{\tfrac{1}{n}}$, and the
sample we return is the target, $\Pr[Y = i] = \blue{\pi_i}$. Now, divide the
nodes into two parts:

- under-filled: $\blue{\pi_i} < \pink{\tfrac{1}{n}}$. Note that $\green{\tau_i} = n\,\blue{\pi_i}$ for such a node.
- over-filled: $\blue{\pi_j} \ge \pink{\tfrac{1}{n}}$. Note that $\green{\tau_j} = 1$ for such a node.

Next, rewrite the mean trace as:

$$\begin{aligned}
P(\text{keep}) &= \sum_{i=1}^n \frac{1}{n} \green{\tau_i} \\ \\
&= \sum_{\text{under}} \frac{1}{n}\underbrace{\blue{(n \pi_i)}}_{\green{\tau_i}\text{ when under-filled}} + \sum_{\text{over}} \frac{1}{n}\underbrace{\pink{(1)}}_{\green{\tau_j}\text{ when over-filled}} \\ \\
&= \sum_{\text{under}} \blue{\pi_i} + \sum_{\text{over}} \pink{\frac{1}{n}} \\ \\
&= \sum_{i=1}^n \min\left(\pink{\frac{1}{n}}, \blue{\pi_i}\right)
\end{aligned}$$

The last step is just the definition of the split: on an under-filled bin the
smaller of the two is $\blue{\pi_i}$, and on an over-filled bin it is
$\pink{\tfrac{1}{n}}$.

The insight: the alias matrix is as efficient as it can be - it tries to maximize the chance that a draw keeps the bin it landed on, and the trace tells us it hits the ceiling $\sum_i \min\left(\pink{\tfrac{1}{n}}, \blue{\pi_i}\right)$ that no coupling of the uniform distribution with $\pi$ can beat.

<!-- texatlas:v1
{"P":{"highlights":[{"selector":{"op":"eq","axis":{"op":"sub","a":{"axis":"col"},"b":{"axis":"row"}},"value":{"const":0}},"color":"#10B981"},{"selector":{"op":"or","a":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":0}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}}},"b":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":2}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}}}},"color":"#F59E0B"}]},"P2":{"highlights":[{"selector":{"op":"or","a":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":3}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":3}}},"b":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":4}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":4}}}},"color":"#10B981"}]}}
-->
