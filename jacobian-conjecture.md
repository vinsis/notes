# Jacobian Conjecture: the missing context

#### Why I wrote this post
The Jacobian Conjecture was recently [proven to be wrong](https://terrytao.wordpress.com/2026/07/21/a-digestion-of-the-jacobian-conjecture-counterexample/) for the case of dimension 3 and higher by Fable (and then an internal OpenAI model IIRC). Most of the posts I came across were either about the sequence of events leading to the announcement or an interpretation of the example used to disprove it. This kind of discourse makes sense for someone who is well familiar with it and working in a related area. 

This article is written for someone who is not, yet is curious and knowledgeable enough to understand it. It takes the reader on a journey to understand what the conjecture is, what motivated it, and a couple of other related statements which were believed to be true but were later disproven, much like the conjecture itself.

#

## Polynomials and invertibility in $\mathbb{R}^n$ and $\mathbb{C}^n$
Consider a function $f$ that maps $x \in \mathbb{R}^n$ (or $\mathbb{C}^n$) to $y$ in the same space, where every output coordinate is a polynomial in the input coordinates:

$$f\begin{pmatrix} \blue{x_1} \\ \vdots \\ \blue{x_n} \end{pmatrix} = \begin{pmatrix} \pink{y_1} \\ \vdots \\ \pink{y_n} \end{pmatrix}, \qquad \pink{y_i} = p_i(\blue{x_1}, \blue{x_2}, \dots, \blue{x_n})$$

Now add some constraints by making these assumptions:

- Assume it is invertible, with $g$ being the inverse function. 
- Assume the inverse $g$ is a polynomial function.

Under this assumption the Jacobian determinant $\det J_f$ has to be a non-zero constant.

#### Why?
Since $g$ undoes $f$, composing them returns the input unchanged. Differentiating that identity with the chain rule turns the composition into a *matrix product*, and taking determinants turns the matrix product into an ordinary product of numbers:

$$\begin{aligned}
g\big(f(\blue{x})\big) &= \blue{x} \\ \\
\underbrace{\teal{J_g\big(f(\blue{x})\big) \, J_f(\blue{x})}}_{\text{a matrix times its inverse}} &= I_n \\ \\
\det J_g\big(f(\blue{x})\big) \cdot \det J_f(\blue{x}) &= \amber{1}
\end{aligned}$$

Each partial derivative in $J_f$ and $J_g$ is a polynomial function, because differentiating a polynomial gives a polynomial:

$$\underbrace{J_f(\blue{x})}_{\text{every entry is a polynomial in } \blue{x}} = \begin{pmatrix}
\frac{\partial p_1}{\partial \blue{x_1}} & \cdots & \frac{\partial p_1}{\partial \blue{x_n}} \\
\vdots & \ddots & \vdots \\
\frac{\partial p_n}{\partial \blue{x_1}} & \cdots & \frac{\partial p_n}{\partial \blue{x_n}}
\end{pmatrix}$$

A determinant is a sum of products of entries, so the determinant of $J_f$ and $J_g$ is also a polynomial function. That leaves us with two polynomials whose product is the constant $1$:

$$\underbrace{\det J_f(\blue{x})}_{\text{polynomial in } \blue{x}} \cdot \underbrace{\det J_g\big(f(\blue{x})\big)}_{\text{polynomial in } \blue{x}} = \amber{1}$$

The product of two polynomial functions can be a constant only if they both are constants, because degrees add under multiplication and neither degree can be negative:

$$\underbrace{\deg\big(\det J_f\big)}_{\ge\, 0} + \underbrace{\deg\big(\det J_g \circ f\big)}_{\ge\, 0} = \deg(\amber{1}) = \amber{0}$$

Both terms are non-negative and they sum to zero, so both are zero. Thus $\det J_f$ is a constant, and it is non-zero since its product with something equals $1$.

---

Thus, what people had was this:
> If a polynomial map $f : \mathbb{R}^n \to \mathbb{R}^n$ (or $f : \mathbb{C}^n \to \mathbb{C}^n$) has an inverse polynomial map $g$, then the Jacobian determinant of $f$ is a non-zero constant.

$$\underbrace{f, \; g \text{ both polynomial on } \mathbb{R}^n \text{ or } \mathbb{C}^n, \quad g \circ f = \text{id}}_{\amber{\text{what we assumed}}} \; \Longrightarrow \; \underbrace{\det J_f = c \ne 0}_{\green{\text{what we get}}}$$

---

### Implication: local and global invertibility
To understand the implication of it, it is important to understand local and global invertibility. 

- A function can be invertible locally i.e. in the neighborhood of a given point. 
- If the inverse of a function $f$ exists, it implies that $f$ is invertible everywhere. 
Thus global invertibility implies local invertibility everywhere. **Two unique inputs always map to two unique outputs**.

Now one can ask:
- If a function is locally invertible everywhere, does it make it globally invertible? A handwavy but useful way to frame it is:

> If nearby points don't share an output, does it guarantee that distant points also don't share one?

Plainly said, does local invertibility imply global invertibility?

$$\begin{aligned}
&\underbrace{\text{for every } a \text{ there is a neighborhood } U \ni a \text{ on which } f \text{ is injective}}_{\amber{\text{locally invertible everywhere}}} \\ \\
\overset{?}{\Longrightarrow} \quad &\underbrace{f(a) = f(b) \; \Longrightarrow \; a = b \quad \text{for all } a, b}_{\green{\text{globally invertible}}}
\end{aligned}$$

---

## Time for some detours 
Before getting to the conjecture, let's spend some more time on the implication and the behavior it leads to.

### A detour from polynomial functions

{}(\clear)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

Local invertibility does not imply global invertibility in general. A simple example is $f(z) = e^z, z \in \mathbb{C}$. In every {}(q-0 = \point -5.9 -3.4) {}(q-1 = \point -0.7 -3.4) {}(z-re-axis = \line q-0 q-1) {}(q-2 = \point -3.2 -4.3) {}(q-3 = \point -3.2 4.7) {}(z-im-axis = \line q-2 q-3) {}(q-4 = \point 0.7 0) {}(q-5 = \point 5.9 0) {}(w-re-axis = \line q-4 q-5) {}(q-6 = \point 3 -2.4) {}(q-7 = \point 3 2.6) {}(w-im-axis = \line q-6 q-7) {}(\set-stroke z-re-axis COLOR-GRAY-MID) {}(\set-stroke z-im-axis COLOR-GRAY-MID) {}(\set-stroke w-re-axis COLOR-GRAY-MID) {}(\set-stroke w-im-axis COLOR-GRAY-MID) {}(\hide q-0 q-1 q-2 q-3 q-4 q-5 q-6 q-7) {}(zlow = \point -2.8 -2.5) {}(disc = \circle zlow 0.35) {}(\set-stroke zlow COLOR-WHITE) {small enough neighborhood}(\set-stroke disc COLOR-AMBER), this function is invertible. However it is obviously not globally invertible {}(zhigh = \point -2.8 3.783) {}(column = \line zlow zhigh) {}(\set-stroke zhigh COLOR-WHITE) {}(\set-stroke column COLOR-GRAY-MID) {}(wimg = \point 3.927 1.169) {}(wring = \circle wimg 0.18) {}(\set-stroke wimg COLOR-RED-MID) {}(\set-stroke wring COLOR-RED-MID) since the points $z$ and $z + 2\pi i$ {}(q-8 = \point 0.4 -3.2) {}(q-9 = \point 0.4 4.2) {}(\hide q-8 q-9) {}(text-0 = \text "exp") {}(lower-map = \annotate-curved-arrow zlow wimg q-8 0.07 text-0) {}(text-1 = \text "exp") {}(upper-map = \annotate-curved-arrow zhigh wimg q-9 0.07 text-1) {}(\set-stroke lower-map COLOR-RED-MID) {}(\set-stroke upper-map COLOR-RED-MID) {}(text-2 = \text "z-plane") {}(plane-z = \annotate-text-box text-2 -3.2 5.4 14) {}(text-3 = \text "w-plane") {}(plane-w = \annotate-text-box text-3 3 5.4 14) {}(\set-fill plane-z COLOR-GRAY-MID) {}(\set-fill plane-w COLOR-GRAY-MID) {}(text-4 = \text "z₀") {}(lab-z0 = \annotate-text-box text-4 -2.15 -1.75 14) {}(text-5 = \text "z₀ + 2πi") {}(lab-z1 = \annotate-text-box text-5 -4.3 3.783 14) {}(text-6 = \text "exp(z₀)") {}(lab-w0 = \annotate-text-box text-6 4.75 1.169 14) {}(\set-fill lab-z0 COLOR-WHITE) {}(\set-fill lab-z1 COLOR-WHITE) {}(\set-fill lab-w0 COLOR-RED-MID) {}(text-7 = \text "injective here") {}(lab-disc = \annotate-text-box text-7 -4.85 -2.5 14) {}(\set-fill lab-disc COLOR-AMBER) {}(text-8 = \text "2πi") {}(period = \annotate-dim-line zlow zhigh text-8) {}(\set-stroke period COLOR-RED-MID) {}(text-9 = \text "z₀ and z₀ + 2πi are far apart, yet exp sends both to the same point. Injective on the amber disc, never on all of ℂ.") {map to the same point}(tbox-0 = \annotate-text-box text-9 2.9 -4.3 14 5.4 -1). 

$$\underbrace{f'(z) = e^z \ne 0 \text{ for every } z}_{\amber{\text{locally invertible everywhere}}} \qquad \text{but} \qquad \underbrace{e^{\,z + 2\pi i} = e^z}_{\rose{\text{global invertibility fails}}}$$

---

### A detour from constant Jacobians
Note that for a function to be (globally) invertible, its Jacobian determinant doesn't need to be a non-zero constant. It just needs to be non-zero everywhere. 

- In the field $\mathbb{C}$, they are the same thing. The only polynomial in $\mathbb{C}$ that never vanishes is a non-zero constant $c$. This is due to [the fundamental theorem of algebra](https://en.wikipedia.org/wiki/Fundamental_theorem_of_algebra): a non-constant polynomial always has a root.

$$\underbrace{P(z) \ne 0 \text{ for every } z \in \mathbb{C}}_{\amber{\text{never vanishes}}} \; \iff \; \underbrace{P(z) = c \ne 0}_{\green{\text{is a non-zero constant}}}$$

  - It implies the only invertible polynomial in $\mathbb{C}$ is of the form $f(z) = az + b$ with $a \ne 0$. This is only true for single dimension.

#### A common gotcha

{}(\clear)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

In higher dimensions, there can be {}(q-0 = \point -5.4 -1.5) {}(q-1 = \point -3.4 -1.5) {}(src-y-0 = \line q-0 q-1) {}(\set-stroke src-y-0 COLOR-PINK) {}(q-2 = \point -5.4 -1) {}(q-3 = \point -3.4 -1) {}(src-y-1 = \line q-2 q-3) {}(\set-stroke src-y-1 COLOR-PINK) {}(q-4 = \point -5.4 -0.5) {}(q-5 = \point -3.4 -0.5) {}(src-y-2 = \line q-4 q-5) {}(\set-stroke src-y-2 COLOR-PINK) {}(q-6 = \point -5.4 0) {}(q-7 = \point -3.4 0) {}(src-y-3 = \line q-6 q-7) {}(\set-stroke src-y-3 COLOR-PINK) {}(q-8 = \point -5.4 0.5) {}(q-9 = \point -3.4 0.5) {}(src-y-4 = \line q-8 q-9) {}(\set-stroke src-y-4 COLOR-PINK) {}(q-10 = \point -5.4 1) {}(q-11 = \point -3.4 1) {}(src-y-5 = \line q-10 q-11) {}(\set-stroke src-y-5 COLOR-PINK) {}(q-12 = \point -5.4 1.5) {}(q-13 = \point -3.4 1.5) {}(src-y-6 = \line q-12 q-13) {}(\set-stroke src-y-6 COLOR-PINK) {}(src-x-0 = \line q-0 q-12) {}(\set-stroke src-x-0 COLOR-BLUE) {}(q-14 = \point -4.4 -1.5) {}(q-15 = \point -4.4 1.5) {}(src-x-1 = \line q-14 q-15) {}(\set-stroke src-x-1 COLOR-BLUE) {}(src-x-2 = \line q-1 q-13) {}(\set-stroke src-x-2 COLOR-BLUE) {}(\hide q-0 q-1 q-2 q-3 q-4 q-5 q-6 q-7 q-8 q-9 q-10 q-11 q-12 q-13 q-14 q-15) {}(q-16 = \point -4.4 0.5) {}(src-bottom = \line q-16 q-9) {}(src-right = \line q-9 q-13) {}(src-top = \line q-13 q-15) {}(src-left = \line q-15 q-16) {}(\hide q-16) {}(\set-stroke src-bottom COLOR-AMBER) {}(\set-stroke src-right COLOR-AMBER) {}(\set-stroke src-top COLOR-AMBER) {higher degree polynomials}(\set-stroke src-left COLOR-AMBER) with a {}(q-17 = \point 3.35 -1.5) {}(q-18 = \point 5.35 -1.5) {}(img-y-0 = \line q-17 q-18) {}(\set-stroke img-y-0 COLOR-PINK) {}(q-19 = \point 2.1 -1) {}(q-20 = \point 4.1 -1) {}(img-y-1 = \line q-19 q-20) {}(\set-stroke img-y-1 COLOR-PINK) {}(q-21 = \point 1.35 -0.5) {}(q-22 = \point 3.35 -0.5) {}(img-y-2 = \line q-21 q-22) {}(\set-stroke img-y-2 COLOR-PINK) {}(q-23 = \point 1.1 0) {}(q-24 = \point 3.1 0) {}(img-y-3 = \line q-23 q-24) {}(\set-stroke img-y-3 COLOR-PINK) {}(q-25 = \point 1.35 0.5) {}(q-26 = \point 3.35 0.5) {}(img-y-4 = \line q-25 q-26) {}(\set-stroke img-y-4 COLOR-PINK) {}(q-27 = \point 2.1 1) {}(q-28 = \point 4.1 1) {}(img-y-5 = \line q-27 q-28) {}(\set-stroke img-y-5 COLOR-PINK) {}(q-29 = \point 3.35 1.5) {}(q-30 = \point 5.35 1.5) {}(img-y-6 = \line q-29 q-30) {}(\set-stroke img-y-6 COLOR-PINK) {}(q-31 = \point -1.15 0) {}(img-x-0 = \bezier-quadratic q-17 q-31 q-29) {}(\set-stroke img-x-0 COLOR-BLUE) {}(q-32 = \point 4.35 -1.5) {}(q-33 = \point -0.15 0) {}(q-34 = \point 4.35 1.5) {}(img-x-1 = \bezier-quadratic q-32 q-33 q-34) {}(\set-stroke img-x-1 COLOR-BLUE) {}(q-35 = \point 0.85 0) {}(img-x-2 = \bezier-quadratic q-18 q-35 q-30) {}(\set-stroke img-x-2 COLOR-BLUE) {}(\hide q-17 q-18 q-19 q-20 q-21 q-22 q-23 q-24 q-25 q-26 q-27 q-28 q-29 q-30 q-31 q-32 q-33 q-34 q-35) {}(q-36 = \point 2.35 0.5) {}(im-bottom = \line q-36 q-26) {}(q-37 = \point 3.85 1) {}(im-right = \bezier-quadratic q-26 q-37 q-30) {}(im-top = \line q-30 q-34) {}(q-38 = \point 2.85 1) {}(im-left = \bezier-quadratic q-34 q-38 q-36) {}(\set-stroke im-bottom COLOR-AMBER) {}(\set-stroke im-right COLOR-AMBER) {}(\set-stroke im-top COLOR-AMBER) {}(\set-stroke im-left COLOR-AMBER) {}(\hide q-36 q-37 q-38) {}(text-10 = \text "domain") {}(cap-src = \annotate-text-box text-10 -4.4 2.7 14) {}(text-11 = \text "image under f") {}(cap-img = \annotate-text-box text-11 3.225 2.7 14) {}(\set-fill cap-src COLOR-GRAY-MID) {}(\set-fill cap-img COLOR-GRAY-MID) {}(text-12 = \text "det J = 1 everywhere") {}(lab-det = \annotate-text-box text-12 -1.15 1.5 14) {}(\set-fill lab-det COLOR-AMBER) {}(q-39 = \point -2.9 0) {}(q-40 = \point 0.6 0) {}(text-13 = \text "f(x, y) = (x + y², y)") {}(map-arrow = \annotate-arrow q-39 q-40 0.05 text-13) {}(\hide q-39 q-40) {constant Jacobian determinant}(\set-stroke map-arrow COLOR-GRAY-MID). Eg 

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} + \pink{y}^2 \\ \pink{y} \end{pmatrix}$$

Even though the first coordinate is quadratic, the $\pink{y}$ that would carry the degree sits in the *upper right* of the Jacobian, where the determinant {}(span-src = \annotate-dim-line q-13 q-15) {}(span-img = \annotate-dim-line q-30 q-34) {}(\set-stroke span-src COLOR-AMBER) {}(\set-stroke span-img COLOR-AMBER) {}(text-14 = \text "Every horizontal slice keeps its length and only slides right by y², so the bent image has exactly the area of the square. That is det J = 1.") {never multiplies it}(tbox-1 = \annotate-text-box text-14 0 -2.9 14 8 -1) against anything but a zero:

$$\begin{aligned}
J_f &= \begin{pmatrix}
\frac{\partial}{\partial \blue{x}}\big(\blue{x} + \pink{y}^2\big) & \frac{\partial}{\partial \pink{y}}\big(\blue{x} + \pink{y}^2\big) \\
\frac{\partial}{\partial \blue{x}}\big(\pink{y}\big) & \frac{\partial}{\partial \pink{y}}\big(\pink{y}\big)
\end{pmatrix} \\ \\
&= \begin{pmatrix} 1 & 2\pink{y} \\ 0 & 1 \end{pmatrix} \\ \\
\det J_f &= 1 \cdot 1 - \underbrace{2\pink{y} \cdot 0}_{\text{the degree dies here}} = \amber{1}
\end{aligned}$$

#

> Therefore, for polynomial maps over complex numbers, saying "the Jacobian is a non-zero constant" and saying "the map is locally invertible everywhere" are mathematically equivalent statements.

- However, in the field $\mathbb{R}$, they are not the same thing. E.g. $x^2 + 1$ is always positive but not constant.

$$\underbrace{x^2 + 1 > 0 \text{ for every } x \in \mathbb{R}}_{\amber{\text{never vanishes}}} \qquad \text{yet} \qquad \underbrace{x^2 + 1 \ne c}_{\rose{\text{not a constant}}}$$

  - For a long time, this was the OG Jacobian conjecture (the strong real Jacobian conjecture): it was believed that in the field of real numbers, if a $\mathbb{R}^n \to \mathbb{R}^n$ polynomial map had a non-zero Jacobian determinant everywhere (implying local invertibility everywhere), it had a global inverse.

$$\underbrace{\det J_f(x) \ne 0 \text{ for every } x \in \mathbb{R}^n}_{\amber{\text{locally invertible everywhere}}} \; \overset{\rose{\times}}{\Longrightarrow} \; \underbrace{f \text{ has a global inverse}}_{\rose{\text{disproven in 1994}}}$$

Note that the strong real conjecture did not require the inverse to be a polynomial. It only conjectured the existence of an inverse, that's it.

This conjecture was [disproven by a counterexample](https://scholar.google.com/scholar_lookup?doi=10.1007/bf02571929) in 1994. 

---

### A detour to constant Jacobian real polynomial maps
Another interesting development in this area was related to the case of inverse polynomial maps for constant Jacobian functions i.e. polynomial functions with a constant Jacobian that also had an inverse polynomial function. Every example could be decomposed to one of these three patterns:

**1. An affine map** $f(x) = Ax + b$, whose Jacobian is the constant matrix $A$ itself:

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} 2 & 1 \\ 1 & 1 \end{pmatrix}\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} + \begin{pmatrix} 3 \\ -1 \end{pmatrix} = \begin{pmatrix} 2\blue{x} + \pink{y} + 3 \\ \blue{x} + \pink{y} - 1 \end{pmatrix}$$

$$\det J_f = \det \begin{pmatrix} 2 & 1 \\ 1 & 1 \end{pmatrix} = 2 - 1 = \amber{1}$$

{}(\clear)
{}(unit = \scalar 56)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

**2. A triangular map**, where each coordinate is {}(q-0 = \point -4.45 -0.9) {}(q-1 = \point -2.65 -0.9) {}(start-bottom = \line q-0 q-1) {}(q-2 = \point -2.65 0.9) {}(start-right = \line q-1 q-2) {}(q-3 = \point -4.45 0.9) {}(start-top = \line q-2 q-3) {}(start-left = \line q-3 q-0) {}(q-4 = \point -4.45 0) {}(q-5 = \point -2.65 0) {}(start-midh = \line q-4 q-5) {}(q-6 = \point -3.55 -0.9) {}(q-7 = \point -3.55 0.9) {}(start-midv = \line q-6 q-7) {}(\hide q-0 q-1 q-2 q-3 q-4 q-5 q-6 q-7) {}(\set-stroke start-bottom COLOR-BLUE) {}(\set-stroke start-right COLOR-BLUE) {}(\set-stroke start-top COLOR-BLUE) {}(\set-stroke start-left COLOR-BLUE) {}(\set-stroke start-midh COLOR-BLUE) {its own variable}(\set-stroke start-midv COLOR-BLUE) {}(q-8 = \point -0.9 -1.629) {}(q-9 = \point -0.3 -0.171) {}(q-10 = \point 0.3 -1.629) {}(q-11 = \point 0.9 -0.171) {}(bent-bottom = \bezier-cubic q-8 q-9 q-10 q-11) {}(q-12 = \point 0.9 1.629) {}(bent-right = \line q-11 q-12) {}(q-13 = \point -0.9 0.171) {}(q-14 = \point -0.3 1.629) {}(q-15 = \point 0.3 0.171) {}(bent-top = \bezier-cubic q-13 q-14 q-15 q-12) {}(bent-left = \line q-13 q-8) {}(q-16 = \point -0.9 -0.729) {}(q-17 = \point -0.3 0.729) {}(q-18 = \point 0.3 -0.729) {}(q-19 = \point 0.9 0.729) {}(bent-midh = \bezier-cubic q-16 q-17 q-18 q-19) {}(q-20 = \point 0 -0.9) {}(q-21 = \point 0 0.9) {}(bent-midv = \line q-20 q-21) {}(\hide q-8 q-9 q-10 q-11 q-12 q-13 q-14 q-15 q-16 q-17 q-18 q-19 q-20 q-21) {}(\set-stroke bent-bottom COLOR-AMBER) {}(\set-stroke bent-right COLOR-AMBER) {}(\set-stroke bent-top COLOR-AMBER) {}(\set-stroke bent-left COLOR-AMBER) {}(\set-stroke bent-midh COLOR-AMBER) {plus a polynomial}(\set-stroke bent-midv COLOR-AMBER) in the *earlier* variables only. The Jacobian is then triangular with $1$s on the diagonal, so its determinant is $1$ for free, and the inverse can be {}(q-22 = \point 2.65 -0.9) {}(q-23 = \point 4.45 -0.9) {}(back-bottom = \line q-22 q-23) {}(q-24 = \point 4.45 0.9) {}(back-right = \line q-23 q-24) {}(q-25 = \point 2.65 0.9) {}(back-top = \line q-24 q-25) {}(back-left = \line q-25 q-22) {}(q-26 = \point 2.65 0) {}(q-27 = \point 4.45 0) {}(back-midh = \line q-26 q-27) {}(q-28 = \point 3.55 -0.9) {}(q-29 = \point 3.55 0.9) {}(back-midv = \line q-28 q-29) {}(\hide q-22 q-23 q-24 q-25 q-26 q-27 q-28 q-29) {}(\set-stroke back-bottom COLOR-EMERALD) {}(\set-stroke back-right COLOR-EMERALD) {}(\set-stroke back-top COLOR-EMERALD) {}(\set-stroke back-left COLOR-EMERALD) {}(\set-stroke back-midh COLOR-EMERALD) {}(\set-stroke back-midv COLOR-EMERALD) {}(dot-a = \point -2.65 0.45) {}(dot-b = \point 0.9 1.179) {}(dot-c = \point 4.45 0.45) {}(\set-stroke dot-a COLOR-PINK) {}(\set-stroke dot-b COLOR-PINK) {}(\set-stroke dot-c COLOR-PINK) {}(text-15 = \text "(x, y)") {}(cap-1 = \annotate-text-box text-15 -3.55 2.8 14) {}(text-16 = \text "(x, y + x³)") {}(cap-2 = \annotate-text-box text-16 0 2.8 14) {}(text-17 = \text "f⁻¹(f(x, y)) = (x, y)") {}(cap-3 = \annotate-text-box text-17 3.55 2.8 14) {}(\set-fill cap-1 COLOR-BLUE) {}(\set-fill cap-2 COLOR-AMBER) {}(\set-fill cap-3 COLOR-EMERALD) {}(text-18 = \text "back to the start") {}(lab-back = \annotate-text-box text-18 3.55 -1.4 14) {}(\set-fill lab-back COLOR-EMERALD) {}(q-30 = \point -2.45 2) {}(q-31 = \point -1.1 2) {}(text-19 = \text "f") {}(apply-f = \annotate-arrow q-30 q-31 0.08 text-19) {}(q-32 = \point 1.1 2) {}(q-33 = \point 2.45 2) {}(text-20 = \text "f⁻¹") {}(undo-f = \annotate-arrow q-32 q-33 0.08 text-20) {}(\set-stroke apply-f COLOR-AMBER) {}(\set-stroke undo-f COLOR-EMERALD) {}(\hide q-30 q-31 q-32 q-33) {}(q-34 = \point 0.9 0.45) {}(text-21 = \text "x³") {}(rise = \annotate-dim-line q-34 dot-b text-21) {}(\set-stroke rise COLOR-AMBER) {}(\hide q-34) {}(text-22 = \text "Each output coordinate is its own variable plus a polynomial in the earlier ones, so back-substitution inverts f exactly: subtract x³ and the square snaps back.") {read off by back-substitution}(tbox-2 = \annotate-text-box text-22 0 -2.8 14 8 -1):

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^3 \end{pmatrix}, \qquad f^{-1}\begin{pmatrix} \blue{u} \\ \pink{v} \end{pmatrix} = \begin{pmatrix} \blue{u} \\ \pink{v} - \blue{u}^3 \end{pmatrix}$$

$$J_f = \begin{pmatrix} 1 & 0 \\ 3\blue{x}^2 & 1 \end{pmatrix} \; \Longrightarrow \; \det J_f = \amber{1}$$

The same trick stacks up in any dimension, one variable at a time:

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \\ \cyan{z} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^3 \\ \cyan{z} + \pink{y}^2 + \blue{x} \end{pmatrix}, \qquad f^{-1}\begin{pmatrix} \blue{u} \\ \pink{v} \\ \cyan{w} \end{pmatrix} = \begin{pmatrix} \blue{u} \\ \pink{v} - \blue{u}^3 \\ \cyan{w} - (\pink{v} - \blue{u}^3)^2 - \blue{u} \end{pmatrix}$$

{}(\clear)
{}(unit = \scalar 34)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

**3. A composition of the above maps**, which is where it stops being obvious. Compose the triangular $T$ with the {}(text-23 = \text "Constant-Jacobian polynomial maps") {}(title = \annotate-text-box text-23 0 7.5 13 8 -1) {}(\set-fill title COLOR-GRAY-LIGHT) {}(q-0 = \point -8 0) {}(tame-frame = \rectangle q-0 16 6.6) {}(\set-stroke tame-frame COLOR-EMERALD) {}(\hide q-0) {}(text-24 = \text "TAME") {}(tame-chip = \annotate-text-box text-24 -6.2 6.6 14 1.6 -1) {}(\set-fill tame-chip COLOR-EMERALD) {}(text-25 = \text "affine:  f(x, y) = (2x + y + 3, x + y - 1)") {}(gen-affine = \annotate-text-box text-25 -4.2 4.6 12 6 -1) {}(text-26 = \text "triangular:  f(x, y) = (x, y + x³)") {}(gen-tri = \annotate-text-box text-26 4.2 4.6 12 6 -1) {}(\set-fill gen-affine COLOR-EMERALD) {affine coordinate swap}(\set-fill gen-tri COLOR-EMERALD) $A$:

$$T\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^2 \end{pmatrix}, \qquad A\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \pink{y} \\ \blue{x} \end{pmatrix}$$

$$\begin{aligned}
(A \circ T)\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} &= A\begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^2 \end{pmatrix} \\ \\
&= \underbrace{\begin{pmatrix} \pink{y} + \blue{x}^2 \\ \blue{x} \end{pmatrix}}_{\text{neither affine nor triangular}} \\ \\
\det J_{A \circ T} &= \det \begin{pmatrix} 2\blue{x} & 1 \\ 1 & 0 \end{pmatrix} = \amber{-1}
\end{aligned}$$

It looks like neither building block, yet it is {}(q-1 = \point -2.4 3.6) {}(q-2 = \point -1 2.3) {}(feed-a = \annotate-arrow q-1 q-2 0.05) {}(q-3 = \point 2.4 3.6) {}(q-4 = \point 1 2.3) {}(feed-b = \annotate-arrow q-3 q-4 0.05) {}(\set-stroke feed-a COLOR-EMERALD) {}(\set-stroke feed-b COLOR-EMERALD) {}(\hide q-1 q-2 q-3 q-4) {}(text-27 = \text "composition A∘T:  f(x, y) = (y + x², x)") {}(gen-comp = \annotate-text-box text-27 0 1.3 12 6 -1) {}(\set-fill gen-comp COLOR-EMERALD) {}(text-28 = \text "det J = 1") {}(det-affine = \annotate-text-box text-28 -4.2 2.95 11) {}(text-29 = \text "det J = 1") {}(det-tri = \annotate-text-box text-29 4.2 2.95 11) {}(text-30 = \text "det J = -1") {}(det-comp = \annotate-text-box text-30 5.2 1.3 11) {}(\set-fill det-affine COLOR-AMBER) {}(\set-fill det-tri COLOR-AMBER) {built from both}(\set-fill det-comp COLOR-AMBER), and its inverse is polynomial for the same reason: $(A \circ T)^{-1}(\blue{u}, \pink{v}) = (\pink{v}, \blue{u} - \pink{v}^2)$.

It was believed that all such polynomial functions were decomposable i.e. they were "tame" maps. In 1972, Japanese mathematician Nagata proposed a polynomial map which did not seem to be decomposable - a {}(q-5 = \point -8 -4.85) {}(wild-frame = \rectangle q-5 16 3.6) {}(\set-stroke wild-frame COLOR-RED-MID) {}(\hide q-5) {}(text-31 = \text "WILD") {}(wild-chip = \annotate-text-box text-31 -6.2 -1.25 14 1.6 -1) {}(\set-fill wild-chip COLOR-RED-MID) {}(text-32 = \text "Nagata (1972):  N(x, y, z) = (x - 2yΔ - zΔ², y + zΔ, z)  with  Δ = xz + y²") {}(nagata = \annotate-text-box text-32 1.3 -2.5 12 11 -1) {}(\set-fill nagata COLOR-RED-MID) {}(text-33 = \text "det J = 1") {}(det-nagata = \annotate-text-box text-33 -4.2 -4.15 11) {}(\set-fill det-nagata COLOR-AMBER) {}(text-34 = \text "proven wild in 2003") {}(proven = \annotate-text-box text-34 1.5 -4.15 11) {"wild" map}(\set-fill proven COLOR-RED-MID):

$$N\begin{pmatrix} \blue{x} \\ \pink{y} \\ \cyan{z} \end{pmatrix} = \begin{pmatrix} \blue{x} - 2\pink{y}\,\teal{\Delta} - \cyan{z}\,\teal{\Delta}^2 \\ \pink{y} + \cyan{z}\,\teal{\Delta} \\ \cyan{z} \end{pmatrix}, \qquad \teal{\Delta} = \blue{x}\cyan{z} + \pink{y}^2, \qquad \det J_N = \amber{1}$$

This fact was [proven to be true](https://doi.org/10.1090/S0894-0347-03-00440-5) only in 2003. This led to the fall of yet another conjecture related to constant Jacobian polynomial maps. 

As of today, this is {}(text-35 = \text "n ≤ 2:  every such map is tame") {}(rule-low = \annotate-text-box text-35 -4.1 -6.05 12 7 -1) {}(text-36 = \text "n ≥ 3:  wild maps exist") {}(rule-high = \annotate-text-box text-36 4.1 -6.05 12 7 -1) {}(\set-fill rule-low COLOR-EMERALD) {}(\set-fill rule-high COLOR-RED-MID) {}(text-37 = \text "Nagata's map has the same det J = 1 as the tame generators, yet is not built from them.") {}(summary = \annotate-text-box text-37 0 -7.85 12 14 -1) {what we know}(\set-fill summary COLOR-GRAY-LIGHT):
- all such polynomial automorphisms are tame for dimension $n \le 2$.
- for dimension $n \ge 3$, such an automorphism can be a wild one.

---

## The Jacobian Conjecture
Now it is a good time to state the conjecture that was proven to be wrong just a few days ago:

Given a polynomial map with a non-zero constant Jacobian, it has an inverse. _If_ such an inverse exists, it is guaranteed to be polynomial. 

$$\underbrace{\det J_f = c \ne 0}_{\amber{\text{constant, non-zero Jacobian}}} \; \overset{?}{\Longrightarrow} \; \underbrace{f^{-1} \text{ exists and is a polynomial map}}_{\green{\text{polynomial inverse}}}$$

#### Why is it guaranteed to be polynomial?
The proof follows these arguments:
- the polynomial inverse, if it exists, has to be of the form $x_i = \frac{N_i(y)}{D_i(y)}$ i.e. it is a fraction of two polynomials.
- Since it exists everywhere, $D_i(y) \ne 0$ at every point, which is possible only if $D_i(y)$ is a constant. Thus $x_i$ is a polynomial for all $i$.

$$x_i = \frac{N_i(y)}{\underbrace{D_i(y)}_{\amber{\text{never vanishes} \; \Rightarrow \; \text{constant}}}} \; \Longrightarrow \; \underbrace{x_i \text{ is a polynomial in } y}_{\green{\text{the inverse is polynomial}}}$$

Take the map from the gotcha above, now written in indexed coordinates:

$$f\begin{pmatrix} \blue{x_1} \\ \pink{x_2} \end{pmatrix} = \begin{pmatrix} \blue{x_1} + \pink{x_2}^2 \\ \pink{x_2} \end{pmatrix} = \begin{pmatrix} y_1 \\ y_2 \end{pmatrix}, \qquad \det J_f = \amber{1}$$

Solving for the inputs one at a time is pure back-substitution:

$$\begin{aligned}
\pink{x_2} &= y_2 \\ \\
\blue{x_1} &= y_1 - \pink{x_2}^2 \\
&= y_1 - y_2^{\,2}
\end{aligned}$$

Written in the $N_i/D_i$ form the argument above predicts, both denominators come out as the constant $1$:

$$\blue{x_1} = \frac{\overbrace{y_1 - y_2^{\,2}}^{N_1(y)}}{\underbrace{1}_{\amber{D_1(y)}}}, \qquad \pink{x_2} = \frac{\overbrace{y_2}^{N_2(y)}}{\underbrace{1}_{\amber{D_2(y)}}}$$

So nothing rational survives, and the inverse is an honest polynomial map:

$$f^{-1}\begin{pmatrix} y_1 \\ y_2 \end{pmatrix} = \begin{pmatrix} y_1 - y_2^{\,2} \\ y_2 \end{pmatrix}$$

---

### Implications of this conjecture

{}(\clear)
{}(unit = \scalar 38)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

The conjecture essentially claims that for polynomials function, local invertibility {}(text-38 = \text "Does local invertibility imply global invertibility?") {}(banner = \annotate-text-box text-38 0 6.4 15 12 -1) {}(\set-fill banner COLOR-GRAY-LIGHT) {}(text-39 = \text "hypothesis") {}(head-hyp = \annotate-text-box text-39 -4.3 5 12) {}(text-40 = \text "conclusion") {}(head-con = \annotate-text-box text-40 4.3 5 12) {}(\set-fill head-hyp COLOR-AMBER) {}(\set-fill head-con COLOR-EMERALD) {}(text-41 = \text "any holomorphic f:  f'(z) ≠ 0 everywhere") {}(hyp-0 = \annotate-text-box text-41 -4.3 3.4 13 4.8 -1) {}(\set-fill hyp-0 COLOR-AMBER) {}(text-42 = \text "strong real Jacobian conjecture:  det J ≠ 0 everywhere on ℝⁿ") {}(hyp-1 = \annotate-text-box text-42 -4.3 0 13 4.8 -1) {}(\set-fill hyp-1 COLOR-AMBER) {}(text-43 = \text "Jacobian Conjecture:  det J = c ≠ 0 on ℂⁿ") {}(hyp-2 = \annotate-text-box text-43 -4.3 -3.4 13 4.8 -1) {implies global invertibility}(\set-fill hyp-2 COLOR-AMBER).

This was a {}(q-0 = \point -1.45 3.4) {}(q-1 = \point 1.45 3.4) {}(link-0 = \annotate-arrow q-0 q-1 0.04) {}(\set-stroke link-0 COLOR-RED-MID) {}(q-2 = \point -1.45 0) {}(q-3 = \point 1.45 0) {}(link-1 = \annotate-arrow q-2 q-3 0.04) {}(\set-stroke link-1 COLOR-RED-MID) {}(q-4 = \point -1.45 -3.4) {}(q-5 = \point 1.45 -3.4) {}(link-2 = \annotate-arrow q-4 q-5 0.04) {}(\set-stroke link-2 COLOR-RED-MID) {}(\hide q-0 q-1 q-2 q-3 q-4 q-5) {}(text-44 = \text "f is injective on ℂ") {}(con-0 = \annotate-text-box text-44 4.3 3.4 13 4.8 -1) {}(\set-fill con-0 COLOR-EMERALD) {}(text-45 = \text "f has a global inverse") {}(con-1 = \annotate-text-box text-45 4.3 0 13 4.8 -1) {}(\set-fill con-1 COLOR-EMERALD) {}(text-46 = \text "f⁻¹ exists and is a polynomial map") {}(con-2 = \annotate-text-box text-46 4.3 -3.4 13 4.8 -1) {massive claim}(\set-fill con-2 COLOR-EMERALD) because, outside of complex polynomials, local invertibility {}(text-47 = \text "verdict") {}(head-ver = \annotate-text-box text-47 0 5 12) {}(\set-fill head-ver COLOR-RED-MID) {}(text-48 = \text "✗") {}(badge-0 = \annotate-text-box text-48 0 3.4 18 0.8 -1) {}(\set-fill badge-0 COLOR-RED-MID) {}(text-49 = \text "✗") {}(badge-1 = \annotate-text-box text-49 0 0 18 0.8 -1) {}(\set-fill badge-1 COLOR-RED-MID) {}(text-50 = \text "✗") {}(badge-2 = \annotate-text-box text-50 0 -3.4 18 0.8 -1) {}(\set-fill badge-2 COLOR-RED-MID) {}(text-51 = \text "false - counterexample: exp(z)") {}(cap-0 = \annotate-text-box text-51 4.3 1.85 11) {}(\set-fill cap-0 COLOR-RED-MID) {}(text-52 = \text "false - disproven in 1994") {}(cap-1 = \annotate-text-box text-52 4.3 -1.55 11) {}(\set-fill cap-1 COLOR-RED-MID) {}(text-53 = \text "false for n ≥ 3, 2026 - stood 90 years") {}(cap-2 = \annotate-text-box text-53 4.3 -4.95 11) {}(\set-fill cap-2 COLOR-RED-MID) {}(q-6 = \point -7.3 -5.45) {}(newest = \rectangle q-6 14.6 3.3) {}(\set-stroke newest COLOR-RED-MID) {}(\hide q-6) {}(text-54 = \text "In every row the hypothesis holds and the conclusion fails: local invertibility never buys global invertibility.") {}(footer = \annotate-text-box text-54 0 -6.75 13 13 -1) {does not guarantee}(\set-fill footer COLOR-GRAY-LIGHT) global invertibility. We saw the example of $e^z$ earlier.  

$$\begin{aligned}
\text{any holomorphic } f: \quad & \underbrace{f'(z) \ne 0 \text{ everywhere}}_{\amber{\text{local}}} \; \overset{\rose{\times}}{\Longrightarrow} \; \underbrace{f \text{ is injective}}_{\rose{\text{fails: } e^z}} \\ \\
\text{polynomial } f: \quad & \underbrace{\det J_f = c \ne 0}_{\amber{\text{local}}} \; \overset{\rose{\times}}{\Longrightarrow} \; \underbrace{f \text{ is injective}}_{\rose{\text{recently proven false}}}
\end{aligned}$$

It was disproved only recently (July 2026) for dimension $n \ge 3$. It still remains an open conjecture for $n = 2$.