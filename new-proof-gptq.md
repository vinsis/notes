# A new solution to GPTQ quantization algorithm

$$
%id:weights
\begin{bmatrix} x_{11} & x_{12} & x_{13} & x_{14} & x_{15} & x_{16} \\ x_{21} & x_{22} & x_{23} & x_{24} & x_{25} & x_{26} \\ x_{31} & x_{32} & x_{33} & x_{34} & x_{35} & x_{36} \\ x_{41} & x_{42} & x_{43} & x_{44} & x_{45} & x_{46} \end{bmatrix}
$$

{}(i = \scalar -1)

GPTQ quantizes each row in parallel, one weight ($w \rightarrow \amber{w_q}$) at a time. Every column is quantized {}(i = \scalar 0) {left to right}(\animate i 6), until the whole matrix is quantized.

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


Next, let us express $M$ and $a$ in terms of $H$ and $dW$. Note that the `i`-th column of $M$ is $i+1$-th column of $L^T$ or the $i+1$-th row of $L$:

$$M = L_{:,1:}^T \Rightarrow M^T = L_{1:,:}$$

The {}(\clear) {}(unit = \scalar 32) {}(grid-opacity = \scalar 0) {}(\load-colors) {}(lt-corner-0-0 = \point -6.2 4) {}(lt-cell-0-0 = \square lt-corner-0-0 1) {}(\set-stroke lt-cell-0-0 COLOR-GRAY-MID) {}(\set-fill lt-cell-0-0 COLOR-GRAY-DARK) {}(lt-corner-0-1 = \point -5.2 4) {}(lt-cell-0-1 = \square lt-corner-0-1 1) {}(\set-stroke lt-cell-0-1 COLOR-GRAY-MID) {}(\set-fill lt-cell-0-1 COLOR-GRAY-DARK) {}(lt-corner-0-2 = \point -4.2 4) {}(lt-cell-0-2 = \square lt-corner-0-2 1) {}(\set-stroke lt-cell-0-2 COLOR-GRAY-MID) {}(\set-fill lt-cell-0-2 COLOR-GRAY-DARK) {}(lt-corner-0-3 = \point -3.2 4) {}(lt-cell-0-3 = \square lt-corner-0-3 1) {}(\set-stroke lt-cell-0-3 COLOR-GRAY-MID) {}(\set-fill lt-cell-0-3 COLOR-GRAY-DARK) {}(lt-corner-0-4 = \point -2.2 4) {}(lt-cell-0-4 = \square lt-corner-0-4 1) {}(\set-stroke lt-cell-0-4 COLOR-GRAY-MID) {}(\set-fill lt-cell-0-4 COLOR-GRAY-DARK) {}(lt-corner-1-0 = \point -6.2 3) {}(lt-cell-1-0 = \square lt-corner-1-0 1) {}(\set-stroke lt-cell-1-0 COLOR-GRAY-MID) {}(\set-fill lt-cell-1-0 COLOR-BLACK) {}(lt-corner-1-1 = \point -5.2 3) {}(lt-cell-1-1 = \square lt-corner-1-1 1) {}(\set-stroke lt-cell-1-1 COLOR-GRAY-MID) {}(\set-fill lt-cell-1-1 COLOR-GRAY-DARK) {}(lt-corner-1-2 = \point -4.2 3) {}(lt-cell-1-2 = \square lt-corner-1-2 1) {}(\set-stroke lt-cell-1-2 COLOR-GRAY-MID) {}(\set-fill lt-cell-1-2 COLOR-GRAY-DARK) {}(lt-corner-1-3 = \point -3.2 3) {}(lt-cell-1-3 = \square lt-corner-1-3 1) {}(\set-stroke lt-cell-1-3 COLOR-GRAY-MID) {}(\set-fill lt-cell-1-3 COLOR-GRAY-DARK) {}(lt-corner-1-4 = \point -2.2 3) {}(lt-cell-1-4 = \square lt-corner-1-4 1) {}(\set-stroke lt-cell-1-4 COLOR-GRAY-MID) {}(\set-fill lt-cell-1-4 COLOR-GRAY-DARK) {}(lt-corner-2-0 = \point -6.2 2) {}(lt-cell-2-0 = \square lt-corner-2-0 1) {}(\set-stroke lt-cell-2-0 COLOR-GRAY-MID) {}(\set-fill lt-cell-2-0 COLOR-BLACK) {}(lt-corner-2-1 = \point -5.2 2) {}(lt-cell-2-1 = \square lt-corner-2-1 1) {}(\set-stroke lt-cell-2-1 COLOR-GRAY-MID) {}(\set-fill lt-cell-2-1 COLOR-BLACK) {}(lt-corner-2-2 = \point -4.2 2) {}(lt-cell-2-2 = \square lt-corner-2-2 1) {}(\set-stroke lt-cell-2-2 COLOR-GRAY-MID) {}(\set-fill lt-cell-2-2 COLOR-GRAY-DARK) {}(lt-corner-2-3 = \point -3.2 2) {}(lt-cell-2-3 = \square lt-corner-2-3 1) {}(\set-stroke lt-cell-2-3 COLOR-GRAY-MID) {}(\set-fill lt-cell-2-3 COLOR-GRAY-DARK) {}(lt-corner-2-4 = \point -2.2 2) {}(lt-cell-2-4 = \square lt-corner-2-4 1) {}(\set-stroke lt-cell-2-4 COLOR-GRAY-MID) {}(\set-fill lt-cell-2-4 COLOR-GRAY-DARK) {}(lt-corner-3-0 = \point -6.2 1) {}(lt-cell-3-0 = \square lt-corner-3-0 1) {}(\set-stroke lt-cell-3-0 COLOR-GRAY-MID) {}(\set-fill lt-cell-3-0 COLOR-BLACK) {}(lt-corner-3-1 = \point -5.2 1) {}(lt-cell-3-1 = \square lt-corner-3-1 1) {}(\set-stroke lt-cell-3-1 COLOR-GRAY-MID) {}(\set-fill lt-cell-3-1 COLOR-BLACK) {}(lt-corner-3-2 = \point -4.2 1) {}(lt-cell-3-2 = \square lt-corner-3-2 1) {}(\set-stroke lt-cell-3-2 COLOR-GRAY-MID) {}(\set-fill lt-cell-3-2 COLOR-BLACK) {}(lt-corner-3-3 = \point -3.2 1) {}(lt-cell-3-3 = \square lt-corner-3-3 1) {}(\set-stroke lt-cell-3-3 COLOR-GRAY-MID) {}(\set-fill lt-cell-3-3 COLOR-GRAY-DARK) {}(lt-corner-3-4 = \point -2.2 1) {}(lt-cell-3-4 = \square lt-corner-3-4 1) {}(\set-stroke lt-cell-3-4 COLOR-GRAY-MID) {}(\set-fill lt-cell-3-4 COLOR-GRAY-DARK) {}(lt-corner-4-0 = \point -6.2 0) {}(lt-cell-4-0 = \square lt-corner-4-0 1) {}(\set-stroke lt-cell-4-0 COLOR-GRAY-MID) {}(\set-fill lt-cell-4-0 COLOR-BLACK) {}(lt-corner-4-1 = \point -5.2 0) {}(lt-cell-4-1 = \square lt-corner-4-1 1) {}(\set-stroke lt-cell-4-1 COLOR-GRAY-MID) {}(\set-fill lt-cell-4-1 COLOR-BLACK) {}(lt-corner-4-2 = \point -4.2 0) {}(lt-cell-4-2 = \square lt-corner-4-2 1) {}(\set-stroke lt-cell-4-2 COLOR-GRAY-MID) {}(\set-fill lt-cell-4-2 COLOR-BLACK) {}(lt-corner-4-3 = \point -3.2 0) {}(lt-cell-4-3 = \square lt-corner-4-3 1) {}(\set-stroke lt-cell-4-3 COLOR-GRAY-MID) {}(\set-fill lt-cell-4-3 COLOR-BLACK) {}(lt-corner-4-4 = \point -2.2 0) {}(lt-cell-4-4 = \square lt-corner-4-4 1) {}(\set-stroke lt-cell-4-4 COLOR-GRAY-MID) {}(\set-fill lt-cell-4-4 COLOR-GRAY-DARK) {}(\hide lt-corner-0-0 lt-corner-0-1 lt-corner-0-2 lt-corner-0-3 lt-corner-0-4 lt-corner-1-0 lt-corner-1-1 lt-corner-1-2 lt-corner-1-3 lt-corner-1-4 lt-corner-2-0 lt-corner-2-1 lt-corner-2-2 lt-corner-2-3 lt-corner-2-4 lt-corner-3-0 lt-corner-3-1 lt-corner-3-2 lt-corner-3-3 lt-corner-3-4 lt-corner-4-0 lt-corner-4-1 lt-corner-4-2 lt-corner-4-3 lt-corner-4-4) {}(text-0 = \text "0") {}(lt-col-idx-0 = \annotate-text-box text-0 -5.7 5.3 12) {}(\set-fill lt-col-idx-0 COLOR-GRAY-LIGHT) {}(text-1 = \text "1") {}(lt-col-idx-1 = \annotate-text-box text-1 -4.7 5.3 12) {}(\set-fill lt-col-idx-1 COLOR-GRAY-LIGHT) {}(text-2 = \text "2") {}(lt-col-idx-2 = \annotate-text-box text-2 -3.7 5.3 12) {}(\set-fill lt-col-idx-2 COLOR-GRAY-LIGHT) {}(text-3 = \text "3") {}(lt-col-idx-3 = \annotate-text-box text-3 -2.7 5.3 12) {}(\set-fill lt-col-idx-3 COLOR-GRAY-LIGHT) {}(text-4 = \text "4") {}(lt-col-idx-4 = \annotate-text-box text-4 -1.7 5.3 12) {}(\set-fill lt-col-idx-4 COLOR-GRAY-LIGHT) {}(text-5 = \text "0") {}(lt-row-idx-0 = \annotate-text-box text-5 -6.55 4.5 12) {}(\set-fill lt-row-idx-0 COLOR-GRAY-LIGHT) {}(text-6 = \text "1") {}(lt-row-idx-1 = \annotate-text-box text-6 -6.55 3.5 12) {}(\set-fill lt-row-idx-1 COLOR-GRAY-LIGHT) {}(text-7 = \text "2") {}(lt-row-idx-2 = \annotate-text-box text-7 -6.55 2.5 12) {}(\set-fill lt-row-idx-2 COLOR-GRAY-LIGHT) {}(text-8 = \text "3") {}(lt-row-idx-3 = \annotate-text-box text-8 -6.55 1.5 12) {}(\set-fill lt-row-idx-3 COLOR-GRAY-LIGHT) {}(text-9 = \text "4") {}(lt-row-idx-4 = \annotate-text-box text-9 -6.55 0.5 12) {}(\set-fill lt-row-idx-4 COLOR-GRAY-LIGHT) {}(text-10 = \text "Lᵀ  (upper triangular)") {}(lt-title = \annotate-text-box text-10 -3.7 6.3 15) {grey squares}(\set-fill lt-title COLOR-GRAY-LIGHT) are the upper triangular matrix $L^T$ and {}(\set-fill lt-cell-0-1 COLOR-TEAL) {}(\set-fill lt-cell-0-2 COLOR-TEAL) {}(\set-fill lt-cell-0-3 COLOR-TEAL) {}(\set-fill lt-cell-0-4 COLOR-TEAL) {}(\set-fill lt-cell-1-1 COLOR-TEAL) {}(\set-fill lt-cell-1-2 COLOR-TEAL) {}(\set-fill lt-cell-1-3 COLOR-TEAL) {}(\set-fill lt-cell-1-4 COLOR-TEAL) {}(\set-stroke lt-cell-2-1 COLOR-TEAL) {}(\set-fill lt-cell-2-2 COLOR-TEAL) {}(\set-fill lt-cell-2-3 COLOR-TEAL) {}(\set-fill lt-cell-2-4 COLOR-TEAL) {}(\set-stroke lt-cell-3-1 COLOR-TEAL) {}(\set-stroke lt-cell-3-2 COLOR-TEAL) {}(\set-fill lt-cell-3-3 COLOR-TEAL) {}(\set-fill lt-cell-3-4 COLOR-TEAL) {}(\set-stroke lt-cell-4-1 COLOR-TEAL) {}(\set-stroke lt-cell-4-2 COLOR-TEAL) {}(\set-stroke lt-cell-4-3 COLOR-TEAL) {}(\set-fill lt-cell-4-4 COLOR-TEAL) {}(text-11 = \text "M") {}(m-label = \annotate-text-box text-11 -2.7 3.5 22) {}(\set-fill m-label COLOR-BLACK) {}(text-12 = \text "Relationship between M (n x n-1) and Lᵀ (n x n)") {}(a-note = \annotate-text-box text-12 -3.7 -1.85 13 5 -1) {the teal block is $M$}(\set-fill a-note COLOR-GRAY).

Thus, $M^TM$ is just {}(h-corner-0-0 = \point 1.2 4) {}(h-cell-0-0 = \square h-corner-0-0 1) {}(\set-stroke h-cell-0-0 COLOR-GRAY-MID) {}(\set-fill h-cell-0-0 COLOR-GRAY-DARK) {}(h-corner-0-1 = \point 2.2 4) {}(h-cell-0-1 = \square h-corner-0-1 1) {}(\set-stroke h-cell-0-1 COLOR-GRAY-MID) {}(\set-fill h-cell-0-1 COLOR-GRAY-DARK) {}(h-corner-0-2 = \point 3.2 4) {}(h-cell-0-2 = \square h-corner-0-2 1) {}(\set-stroke h-cell-0-2 COLOR-GRAY-MID) {}(\set-fill h-cell-0-2 COLOR-GRAY-DARK) {}(h-corner-0-3 = \point 4.2 4) {}(h-cell-0-3 = \square h-corner-0-3 1) {}(\set-stroke h-cell-0-3 COLOR-GRAY-MID) {}(\set-fill h-cell-0-3 COLOR-GRAY-DARK) {}(h-corner-0-4 = \point 5.2 4) {}(h-cell-0-4 = \square h-corner-0-4 1) {}(\set-stroke h-cell-0-4 COLOR-GRAY-MID) {}(\set-fill h-cell-0-4 COLOR-GRAY-DARK) {}(h-corner-1-0 = \point 1.2 3) {}(h-cell-1-0 = \square h-corner-1-0 1) {}(\set-stroke h-cell-1-0 COLOR-GRAY-MID) {}(\set-fill h-cell-1-0 COLOR-GRAY-DARK) {}(h-corner-1-1 = \point 2.2 3) {}(h-cell-1-1 = \square h-corner-1-1 1) {}(\set-stroke h-cell-1-1 COLOR-GRAY-MID) {}(\set-fill h-cell-1-1 COLOR-TEAL) {}(h-corner-1-2 = \point 3.2 3) {}(h-cell-1-2 = \square h-corner-1-2 1) {}(\set-stroke h-cell-1-2 COLOR-GRAY-MID) {}(\set-fill h-cell-1-2 COLOR-TEAL) {}(h-corner-1-3 = \point 4.2 3) {}(h-cell-1-3 = \square h-corner-1-3 1) {}(\set-stroke h-cell-1-3 COLOR-GRAY-MID) {}(\set-fill h-cell-1-3 COLOR-TEAL) {}(h-corner-1-4 = \point 5.2 3) {}(h-cell-1-4 = \square h-corner-1-4 1) {}(\set-stroke h-cell-1-4 COLOR-GRAY-MID) {}(\set-fill h-cell-1-4 COLOR-TEAL) {}(h-corner-2-0 = \point 1.2 2) {}(h-cell-2-0 = \square h-corner-2-0 1) {}(\set-stroke h-cell-2-0 COLOR-GRAY-MID) {}(\set-fill h-cell-2-0 COLOR-GRAY-DARK) {}(h-corner-2-1 = \point 2.2 2) {}(h-cell-2-1 = \square h-corner-2-1 1) {}(\set-stroke h-cell-2-1 COLOR-GRAY-MID) {}(\set-fill h-cell-2-1 COLOR-TEAL) {}(h-corner-2-2 = \point 3.2 2) {}(h-cell-2-2 = \square h-corner-2-2 1) {}(\set-stroke h-cell-2-2 COLOR-GRAY-MID) {}(\set-fill h-cell-2-2 COLOR-TEAL) {}(h-corner-2-3 = \point 4.2 2) {}(h-cell-2-3 = \square h-corner-2-3 1) {}(\set-stroke h-cell-2-3 COLOR-GRAY-MID) {}(\set-fill h-cell-2-3 COLOR-TEAL) {}(h-corner-2-4 = \point 5.2 2) {}(h-cell-2-4 = \square h-corner-2-4 1) {}(\set-stroke h-cell-2-4 COLOR-GRAY-MID) {}(\set-fill h-cell-2-4 COLOR-TEAL) {}(h-corner-3-0 = \point 1.2 1) {}(h-cell-3-0 = \square h-corner-3-0 1) {}(\set-stroke h-cell-3-0 COLOR-GRAY-MID) {}(\set-fill h-cell-3-0 COLOR-GRAY-DARK) {}(h-corner-3-1 = \point 2.2 1) {}(h-cell-3-1 = \square h-corner-3-1 1) {}(\set-stroke h-cell-3-1 COLOR-GRAY-MID) {}(\set-fill h-cell-3-1 COLOR-TEAL) {}(h-corner-3-2 = \point 3.2 1) {}(h-cell-3-2 = \square h-corner-3-2 1) {}(\set-stroke h-cell-3-2 COLOR-GRAY-MID) {}(\set-fill h-cell-3-2 COLOR-TEAL) {}(h-corner-3-3 = \point 4.2 1) {}(h-cell-3-3 = \square h-corner-3-3 1) {}(\set-stroke h-cell-3-3 COLOR-GRAY-MID) {}(\set-fill h-cell-3-3 COLOR-TEAL) {}(h-corner-3-4 = \point 5.2 1) {}(h-cell-3-4 = \square h-corner-3-4 1) {}(\set-stroke h-cell-3-4 COLOR-GRAY-MID) {}(\set-fill h-cell-3-4 COLOR-TEAL) {}(h-corner-4-0 = \point 1.2 0) {}(h-cell-4-0 = \square h-corner-4-0 1) {}(\set-stroke h-cell-4-0 COLOR-GRAY-MID) {}(\set-fill h-cell-4-0 COLOR-GRAY-DARK) {}(h-corner-4-1 = \point 2.2 0) {}(h-cell-4-1 = \square h-corner-4-1 1) {}(\set-stroke h-cell-4-1 COLOR-GRAY-MID) {}(\set-fill h-cell-4-1 COLOR-TEAL) {}(h-corner-4-2 = \point 3.2 0) {}(h-cell-4-2 = \square h-corner-4-2 1) {}(\set-stroke h-cell-4-2 COLOR-GRAY-MID) {}(\set-fill h-cell-4-2 COLOR-TEAL) {}(h-corner-4-3 = \point 4.2 0) {}(h-cell-4-3 = \square h-corner-4-3 1) {}(\set-stroke h-cell-4-3 COLOR-GRAY-MID) {}(\set-fill h-cell-4-3 COLOR-TEAL) {}(h-corner-4-4 = \point 5.2 0) {}(h-cell-4-4 = \square h-corner-4-4 1) {}(\set-stroke h-cell-4-4 COLOR-GRAY-MID) {}(\set-fill h-cell-4-4 COLOR-TEAL) {}(\hide h-corner-0-0 h-corner-0-1 h-corner-0-2 h-corner-0-3 h-corner-0-4 h-corner-1-0 h-corner-1-1 h-corner-1-2 h-corner-1-3 h-corner-1-4 h-corner-2-0 h-corner-2-1 h-corner-2-2 h-corner-2-3 h-corner-2-4 h-corner-3-0 h-corner-3-1 h-corner-3-2 h-corner-3-3 h-corner-3-4 h-corner-4-0 h-corner-4-1 h-corner-4-2 h-corner-4-3 h-corner-4-4) {}(text-13 = \text "0") {}(h-col-idx-0 = \annotate-text-box text-13 1.7 5.3 12) {}(\set-fill h-col-idx-0 COLOR-GRAY-LIGHT) {}(text-14 = \text "1") {}(h-col-idx-1 = \annotate-text-box text-14 2.7 5.3 12) {}(\set-fill h-col-idx-1 COLOR-GRAY-LIGHT) {}(text-15 = \text "2") {}(h-col-idx-2 = \annotate-text-box text-15 3.7 5.3 12) {}(\set-fill h-col-idx-2 COLOR-GRAY-LIGHT) {}(text-16 = \text "3") {}(h-col-idx-3 = \annotate-text-box text-16 4.7 5.3 12) {}(\set-fill h-col-idx-3 COLOR-GRAY-LIGHT) {}(text-17 = \text "4") {}(h-col-idx-4 = \annotate-text-box text-17 5.7 5.3 12) {}(\set-fill h-col-idx-4 COLOR-GRAY-LIGHT) {}(text-18 = \text "0") {}(h-row-idx-0 = \annotate-text-box text-18 6.55 4.5 12) {}(\set-fill h-row-idx-0 COLOR-GRAY-LIGHT) {}(text-19 = \text "1") {}(h-row-idx-1 = \annotate-text-box text-19 6.55 3.5 12) {}(\set-fill h-row-idx-1 COLOR-GRAY-LIGHT) {}(text-20 = \text "2") {}(h-row-idx-2 = \annotate-text-box text-20 6.55 2.5 12) {}(\set-fill h-row-idx-2 COLOR-GRAY-LIGHT) {}(text-21 = \text "3") {}(h-row-idx-3 = \annotate-text-box text-21 6.55 1.5 12) {}(\set-fill h-row-idx-3 COLOR-GRAY-LIGHT) {}(text-22 = \text "4") {}(h-row-idx-4 = \annotate-text-box text-22 6.55 0.5 12) {}(\set-fill h-row-idx-4 COLOR-GRAY-LIGHT) {}(text-23 = \text "H = L Lᵀ") {}(h-title = \annotate-text-box text-23 3.7 6.3 15) {}(\set-fill h-title COLOR-GRAY-LIGHT) {}(text-24 = \text "H₁:,₁:") {}(h-block = \annotate-text-box text-24 4.2 2 14 -1) {}(\set-fill h-block COLOR-TEAL) {}(link-from = \point -1.15 2.5) {}(link-to = \point 1.15 2.5) {}(text-25 = \text "MᵀM") {}(link = \annotate-arrow link-from link-to 0.05 text-25) {}(\set-stroke link COLOR-TEAL) {}(\hide link-from link-to) {}(text-26 = \text "MᵀM is exactly the bottom right 4×4 block of H, that is H₁:,₁:.") {}(mtm-note = \annotate-text-box text-26 3.7 -1.85 13 5.6 -1) {}(\set-fill mtm-note COLOR-TEAL) {}(text-27 = \text "Cholesky turns the objective into ‖a + M x‖²: column 0 of Lᵀ gives the fixed spike a, everything right of it is M, and MᵀM is the Hessian block H₁:,₁: of the weights not yet quantized.") {}(summary = \annotate-text-box text-27 0 -5.15 13 12 -1) {the bottom right $n-1 \times n-1$ block of $H$}(\set-fill summary COLOR-GRAY-LIGHT)

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


<!-- texatlas:v1
{"weights":{"highlights":[{"selector":{"op":"le","axis":{"axis":"col"},"value":{"node":"i"}},"color":"#14B8A6"}]}}
-->
