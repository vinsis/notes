# A new solution to GPTQ quantization algorithm

$$
%id:weights
\begin{bmatrix} x_{11} & x_{12} & x_{13} & x_{14} & x_{15} & x_{16} \\ x_{21} & x_{22} & x_{23} & x_{24} & x_{25} & x_{26} \\ x_{31} & x_{32} & x_{33} & x_{34} & x_{35} & x_{36} \\ x_{41} & x_{42} & x_{43} & x_{44} & x_{45} & x_{46} \end{bmatrix}
$$

```pygeomatic
# `i` = index of the last quantized column; -1 = nothing quantized yet.
i = gm.scalar(-1, out="i")
weights = gm.tex("weights")
weights.highlight(gm.cols <= i, color="teal")

with group("sweep-columns"):
    i = gm.scalar(0)
    gm.animate(i, 6)
```

GPTQ quantizes each row in parallel, one weight ($w \rightarrow \amber{w_q}$) at a time. Every column is quantized {left to right}(ref:sweep-columns), until the whole matrix is quantized.

---

## 0. Introduction

The papers [Optimal Brain Compression](https://arxiv.org/abs/2208.11580) and [GPTQ](https://arxiv.org/abs/2210.17323) introduced a post-training quantization method that is one of the most popular quantization methods at the moment. The algorithm quantizes the rows of a matrix independently. 

In each row, at each step $i$
- a weight $w_i$ is quantized at a time and the remaining unquantized weights, $w_{i+1, \cdots}$,  are modified (not quantized) to compensate for the error due to quantization.

The quantization error and the modified weights both have closed form solutions provided in the papers above. I have also provided a proof of these equations [here](https://github.com/TinyVolt/optimal-brain-compression?tab=readme-ov-file#deep-dive-into-key-ideas).

Here I provide a novel way to approach the problem and come up with a different solution. It has an expression that is easier to comprehend and make sense of geometrically. Finally I show that the solution is equivalent to the one provided in the papers above.

---

## 1. Problem setup

Let $W \in R^n$ be a vector of weights that needs to be quantized, and $dW$ be a perturbation of weights. Here the first weight has been quantized already $dW_0 = W_{0q} - W_0$ and we want to find out the perturbation for the remaining weights $dW_{1,2,...,n}$ that minimizes the quantization error (which is the mean squared error).

$$\begin{align}
f(\amber{dW}) &= \|(W + \amber{dW})X - WX\|^2 \\
&= \cancel{J dW} + \frac{1}{2} \amber{dW^T} H \amber{dW} + \cancel{\mathcal{O}(dW^3)} \\
&= \frac{1}{2} \amber{dW^T} H \amber{dW}
\end{align}$$

where $dW$ is:

$$\begin{bmatrix} \left. dW_0 \ \ \ \right\}{\text{constant}} \\ \left. \amber{dW_{1:n}} \ \right\}{\amber{\text{variables}}} \end{bmatrix}$$

We want to find how to modify the unquantized weights after the first weight has been quantized to minimize the error.

---

## 2. Solution

In the original proof, the already quantized weight is used as a constraint to form a Lagrangian. Here we will use a different approach. The first thing we do is break down the Hessian into its Cholesky decomposition: $H = LL^T$ which immediately gives us (ignoring the constant 0.5):

$$\begin{align}
f(\amber{dW}) &= \| L^T \amber{dW} \|^2 \\
&= \| \underbrace{L_{:,0}^T \ dW_0}_{a} + \underbrace{L_{:,1:}^T}_{M} \ \underbrace{\amber{dW_{1:}}}_{\amber{x}} \|^2 \\ \\
g(\amber{x}) &= \| \underbrace{a}_{\in R^n} + \underbrace{M}_{\in R^{n \times n-1}} \ \ \underbrace{\amber{x}}_{\amber{\in R^{n-1}}} \|^2
\end{align}$$

Thus we simplified the problem to find $x$ which minimizes the squared norm of a sum of vectors. I wrote about this problem and its solution [here](/lafp/blog/minimize_norm_of_vector_sum). Its solution is given by:

$$\begin{align}
\amber{x} &= \amber{dW_{1:}} \\
&= -(M^TM)^{-1} M^T a
\end{align}$$

In addition, we also get an insight into what the optimal weights do geometrically:

> The optimal weights $dW_{1:}$ negate the projection of $a$ onto the column space of $M$.

```pygeomatic
TOP = 5.0            # y of the top edge of both grids (bottom edge = 0)
XL = -6.2            # x of the left edge of the L^T grid
XR = 1.2             # x of the left edge of the H grid
N = 5                # matrices are 5x5


def rd(v):
    """keep loop-computed coordinates free of binary-float noise."""
    return round(v, 4)


def cell_xy(x0, row, col):
    """bottom-left corner of cell (row, col) of a grid whose left edge is x0."""
    return (rd(x0 + col), rd(TOP - row - 1))


with group("lt-grid"):
    gm.clear()
    unit = gm.scalar(32)
    gm.scalar(0, out="grid-opacity")
    c = gm.load_colors()

    lt = {}
    lt_corners = []
    for r in range(N):
        for k in range(N):
            x, y = cell_xy(XL, r, k)
            corner = gm.point(x, y, out=f"lt-corner-{r}-{k}")
            sq = gm.square(corner, 1, out=f"lt-cell-{r}-{k}")
            gm.set_stroke(sq, c["COLOR-GRAY-MID"])
            gm.set_fill(sq, c["COLOR-GRAY-DARK"] if k >= r else c.BLACK)
            lt[(r, k)] = sq
            lt_corners.append(corner)
    gm.hide(*lt_corners)

    for k in range(N):
        idx = gm.annotate_text_box(str(k), rd(XL + k + 0.5), 5.3, 12, out=f"lt-col-idx-{k}")
        gm.set_fill(idx, c["COLOR-GRAY-LIGHT"])
    for r in range(N):
        idx = gm.annotate_text_box(str(r), rd(XL - 0.35), rd(TOP - r - 0.5), 12,
                                   out=f"lt-row-idx-{r}")
        gm.set_fill(idx, c["COLOR-GRAY-LIGHT"])

    lt_title = gm.annotate_text_box("Lᵀ  (upper triangular)", -3.7, 6.3, 15)
    gm.set_fill(lt_title, c["COLOR-GRAY-LIGHT"])

# with group("column-zero-spike"):
# with group("m-block"):
    # for r in range(N):
    #     gm.set_stroke(lt[(r, 0)], c.AMBER)
    # gm.set_fill(lt[(0, 0)], c.AMBER)

    # l00 = gm.annotate_text_box("l₀₀", rd(XL + 0.5), rd(TOP - 0.5), 13)
    # gm.set_fill(l00, c.BLACK)
    # for r in range(1, N):
    #     z = gm.annotate_text_box("0", rd(XL + 0.5), rd(TOP - r - 0.5), 13, out=f"lt-zero-{r}")
    #     gm.set_fill(z, c.AMBER)



with group("m-block"):
    for r in range(N):
        for k in range(1, N):
            if k >= r:
                gm.set_fill(lt[(r, k)], c.TEAL)
            else:
                gm.set_stroke(lt[(r, k)], c.TEAL)

    m_label = gm.annotate_text_box("M", rd(XL + 3.5), rd(TOP - 1.5), 22)
    gm.set_fill(m_label, c.BLACK)
    a_note = gm.annotate_text_box(
      "Relationship between M (n x n-1) and Lᵀ (n x n)",
      -3.7, -1.85, 13, 5, -1,
    )
    gm.set_fill(a_note, c.GRAY)
    # m_span = gm.annotate_text_box("cols 1…4", rd(XL + 2.5), rd(TOP - 4.5), 8, -1)
    # gm.set_fill(m_span, c["COLOR-TEAL-LIGHT"])

with group("h-block"):
    hh = {}
    h_corners = []
    for r in range(N):
        for k in range(N):
            x, y = cell_xy(XR, r, k)
            corner = gm.point(x, y, out=f"h-corner-{r}-{k}")
            sq = gm.square(corner, 1, out=f"h-cell-{r}-{k}")
            gm.set_stroke(sq, c["COLOR-GRAY-MID"])
            gm.set_fill(sq, c.TEAL if (r >= 1 and k >= 1) else c["COLOR-GRAY-DARK"])
            hh[(r, k)] = sq
            h_corners.append(corner)
    gm.hide(*h_corners)

    for k in range(N):
        idx = gm.annotate_text_box(str(k), rd(XR + k + 0.5), 5.3, 12, out=f"h-col-idx-{k}")
        gm.set_fill(idx, c["COLOR-GRAY-LIGHT"])
    for r in range(N):
        idx = gm.annotate_text_box(str(r), rd(XR + N + 0.35), rd(TOP - r - 0.5), 12,
                                   out=f"h-row-idx-{r}")
        gm.set_fill(idx, c["COLOR-GRAY-LIGHT"])

    h_title = gm.annotate_text_box("H = L Lᵀ", rd(XR + 2.5), 6.3, 15)
    gm.set_fill(h_title, c["COLOR-GRAY-LIGHT"])

    h_block = gm.annotate_text_box("H₁:,₁:", rd(XR + 3), rd(TOP - 3), 14, -1)
    gm.set_fill(h_block, c.TEAL)

    link_from = gm.point(rd(XL + N + 0.05), rd(TOP - 2.5))
    link_to = gm.point(rd(XR - 0.05), rd(TOP - 2.5))
    link = gm.annotate_arrow(link_from, link_to, 0.05, "MᵀM")
    gm.set_stroke(link, c.TEAL)
    gm.hide(link_from, link_to)

    mtm_note = gm.annotate_text_box(
        "MᵀM is exactly the bottom right 4×4 block of H, that is H₁:,₁:.",
        3.7, -1.85, 13, 5.6, -1,
    )
    gm.set_fill(mtm_note, c.TEAL)

    summary = gm.annotate_text_box(
        "Cholesky turns the objective into ‖a + M x‖²: column 0 of Lᵀ gives the fixed "
        "spike a, everything right of it is M, and MᵀM is the Hessian block H₁:,₁: "
        "of the weights not yet quantized.",
        0, -5.15, 13, 12, -1,
    )
    gm.set_fill(summary, c["COLOR-GRAY-LIGHT"])
```

Next, let us express $M$ and $a$ in terms of $H$ and $dW$. Note that the `i`-th column of $M$ is $i+1$-th column of $L^T$ or the $i+1$-th row of $L$:

$$M = L_{:,1:}^T \Rightarrow M^T = L_{1:,:}$$

The {grey squares}(ref:lt-grid) are the upper triangular matrix $L^T$ and {the teal block is $M$}(ref:m-block).

Thus, $M^TM$ is just {the bottom right $n-1 \times n-1$ block of $H$}(ref:h-block)

and the vector $M^Ta$ is:

$$\begin{align}
\begin{bmatrix} \amber{l_{10}} & l_{11} & ... & 0 \\ \amber{l_{20}} & l_{21} & ... & 0 \\ ... & ... & ... & 0 \\ \amber{l_{n-1,0}} & l_{n-1,1} & ... & l_{n-1,n-1} \end{bmatrix} \begin{bmatrix} dW_0l_{00} \\ 0 \\ ... \\ 0 \end{bmatrix}
&= \underbrace{dW_0}_{(w_{0q} - w_0)}l_{00} \begin{bmatrix} \amber{l_{10}} \\ \amber{l_{20}} \\ ... \\ \amber{l_{n-1,0}} \end{bmatrix} \\ \\
&= \amber{H_{1:,0}} \ dW_0
\end{align}$$


Finally, note that $H_{00} = l_{00}^2$

$$H = \begin{bmatrix} \underbrace{H_{0,0}}_{= l_{00}^2} & H_{1:,0}^T \\ \rose{\underbrace{H_{1:,0}}_{= \frac{M^Ta}{dW_0}}} & \underbrace{\amber{H_{1:,1:}}}_{\amber{= M^TM}} \end{bmatrix}$$

We re-write the final expression as:

> $$dW_{1:} = -\amber{(H_{1:,1:})^{-1}} \rose{H_{1:,0}} \ (w_{0q} - w_0)$$

---

## 3. Equivalence to the original solution

The original solution is given by:

$$dW_{1:} = \frac{(w_{0q} - w_0)}{(H^{-1})_{00}} \ (H^{-1})_{1:,0}$$

Note that in this expression, the matrix blocks come from $H^{-1}$ whereas in the expression above, the matrix blocks come from $H$. To connect these two, we express $H^{-1}$ in terms of $H$ using the Schur complement:

$$H^{-1} = \begin{bmatrix} \underbrace{S^{-1}}_{(H^{-1})_{00}} & ... \\ {} & {} \\ \underbrace{-\amber{(H_{1:,1:})^{-1}} \rose{H_{1:,0}} \ S^{-1}}_{(H^{-1})_{1:,0}} & ... \end{bmatrix}$$

Substituting this into the expression above:

$$\frac{(H^{-1})_{1:,0}}{(H^{-1})_{00}} = -\amber{(H_{1:,1:})^{-1}} \rose{H_{1:,0}}$$

This proves the equivalence. The exact value of $S$ does not matter here since it will be canceled out (assuming it is non-zero) but for sake of completion, $S = H_{00} - H_{01}H_{11}^{-1}H_{10}$.

