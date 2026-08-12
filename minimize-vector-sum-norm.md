# Minimization of squared norm of a vector sum

$$f(\amber{x}) = \|\blue{a} + \pink{M}\amber{x}\|^2$$

---

## Problem statement

Given a vector of the form $\blue{a} + \pink{M}\amber{x}$, where $\pink{M}$ is a matrix and $\blue{a}$ is a vector, we want to find $\amber{x}$ that minimizes the squared norm of the vector.

$$\min_{\amber{x}} \ \Big\|\underbrace{\blue{a}}_{\in\, R^n} + \underbrace{\pink{M}}_{\in\, R^{n \times k}} \underbrace{\amber{x}}_{\in\, R^k}\Big\|^2$$

{}(unit = \scalar 46)
{}(grid-opacity = \scalar 0.3)
{}(\load-colors)

Find $x$ for which $\|\blue{a} + \pink{M}\amber{x}\|^2$ is minimized. Here $a$ is {}(\show p0) {}(a = \point 2 2.5) {}(vec-a = \annotate-arrow p0 a) {}(\set-stroke vec-a COLOR-TEAL) {}(text-0 = \text "a") {}(lab-a = \annotate-text-box text-0 0.75 1.4 16) {}(\set-fill lab-a COLOR-TEAL) {}(line-left = \point -5 a.y) {}(line-right = \point 5 a.y) {}(affine-line = \line line-left line-right) {}(\set-stroke affine-line COLOR-GRAY-MID) {}(\hide line-left line-right) {}(text-1 = \text "{ a + Mx : x ∈ ℝ }") {}(lab-set = \annotate-text-box text-1 -5.3 2.15 13) {shown here}(\set-fill lab-set COLOR-AMBER) and the vector sum {}(x = \scalar 1.5) {}(sum-x = \add a.x x) {}(tip = \point sum-x a.y) {}(vec-mx = \annotate-arrow a tip) {}(\set-stroke vec-mx COLOR-TEAL-LIGHT) {}(vec-sum = \annotate-arrow p0 tip) {}(\set-stroke vec-sum COLOR-BLUE) {}(half-x = \mul x 0.5) {}(lab-mx-x = \add a.x half-x) {}(text-2 = \text "Mx") {}(lab-mx = \annotate-text-box text-2 lab-mx-x 2.92 14) {}(\set-fill lab-mx COLOR-TEAL-LIGHT) {}(lab-sum-x = \add sum-x 0.78) {}(text-3 = \text "a + Mx") {}(lab-sum = \annotate-text-box text-3 lab-sum-x 2.92 14) {}(\set-fill lab-sum COLOR-BLUE) {}(sq-x = \mul sum-x sum-x) {}(sq-y = \mul a.y a.y) {}(fsq = \add sq-x sq-y) {}(text-4 = \text "x = ${x}; ‖a + Mx‖² = ${fsq}") {}(readout = \annotate-text-box text-4 0 -3.35 14 6 -1) {}(\set-fill readout COLOR-BLUE) {}(norm = \sqrt fsq) {looks like this}(level = \circle p0 norm). The radius of the circle is the length of the vector sum. {}(foot = \point 0 a.y) {}(vec-min = \annotate-arrow p0 foot) {}(\set-stroke vec-min COLOR-AMBER) {}(text-5 = \text "a + Mx*") {}(lab-min = \annotate-text-box text-5 0 2.95 14) {}(\set-fill lab-min COLOR-AMBER) {}(ray-to-origin = \line foot p0) {}(ray-along-set = \line foot a) {The vector with minimum norm}(\hide ray-to-origin ray-along-set) is shown in bold.

---

## Solution

We can write the function as:

$$\begin{aligned}
f(\amber{x}) &= \langle \blue{a} + \pink{M}\amber{x}, \; \blue{a} + \pink{M}\amber{x} \rangle \\ \\
&= \langle \blue{a}, \blue{a} \rangle + 2 \langle \blue{a}, \pink{M}\amber{x} \rangle + \langle \pink{M}\amber{x}, \pink{M}\amber{x} \rangle \\ \\
&= \underbrace{\langle \blue{a}, \blue{a} \rangle}_{\text{constant}} + 2 \underbrace{\langle \pink{M}^T\blue{a}, \amber{x} \rangle}_{\text{linear in } \amber{x}} + \underbrace{\langle \pink{M}^T\pink{M}\amber{x}, \amber{x} \rangle}_{\text{quadratic in } \amber{x}} \\ \\
\end{aligned}$$

{}(\hide lab-sum lab-mx)

It then follows that $\nabla f(\amber{x}) = 2\pink{M}^T\blue{a} + 2\pink{M}^T\pink{M}\amber{x}$. Setting it to zero, we get the solution:

> $$\amber{x}^* = -(\pink{M}^T\pink{M})^{-1}\pink{M}^T\blue{a}$$

Note that $\pink{M}\amber{x}^*$ is {the negative projection of $\blue{a}$}(\animate x -2) onto the column space of $\pink{M}$.

> The optimal value of $\amber{x}$ is one for which $\pink{M}\amber{x}$ is the negative of the projection of $\blue{a}$ onto the column space of $\pink{M}$. 

Ideally, if $\pink{M}\amber{x}$ were equal to $-\blue{a}$, the norm of the sum would be zero. But that may not be possible if $\blue{a}$ is not in the column space of $\pink{M}$. Simply said, the optimal value of $\amber{x}$ tries to get $\pink{M}\amber{x}$ as close as possible to $-\blue{a}$.

