# Minimization of squared norm of a vector sum

$$f(\amber{x}) = \|\blue{a} + \pink{M}\amber{x}\|^2$$

---

## Problem statement

Given a vector of the form $\blue{a} + \pink{M}\amber{x}$, where $\pink{M}$ is a matrix and $\blue{a}$ is a vector, we want to find $\amber{x}$ that minimizes the squared norm of the vector.

$$\min_{\amber{x}} \ \Big\|\underbrace{\blue{a}}_{\in\, R^n} + \underbrace{\pink{M}}_{\in\, R^{n \times k}} \underbrace{\amber{x}}_{\in\, R^k}\Big\|^2$$

```pygeomatic
# canvas frame: content spans x in [-5, 5], y in [-1.9, 3.4]
unit = gm.scalar(46)
gm.scalar(0.3, out="grid-opacity")  # faint lattice, explicit axes below
c = gm.load_colors()

with group("affine-set"):
    gm.show(gm.p0)

    # the fixed vector a
    a = gm.point(2, 2.5)
    vec_a = gm.annotate_arrow(gm.p0, a)
    gm.set_stroke(vec_a, c.TEAL)
    lab_a = gm.annotate_text_box("a", 0.75, 1.4, 16)  # left of the shaft
    gm.set_fill(lab_a, c.TEAL)

    # the affine set { a + Mx : x in R }, with M = e1
    line_left = gm.point(-5, a.y)
    line_right = gm.point(5, a.y)
    affine_line = gm.line(line_left, line_right)
    gm.set_stroke(affine_line, c["COLOR-GRAY-MID"])
    gm.hide(line_left, line_right)
    lab_set = gm.annotate_text_box("{ a + Mx : x ∈ ℝ }", -5.3, 2.15, 13)
    gm.set_fill(lab_set, c.AMBER)

with group("slide-x"):
    # x is the single driving scalar: the slide and the objective follow it
    x = gm.scalar(1.5)
    sum_x = a.x + x
    tip = gm.point(sum_x, a.y)
    vec_mx = gm.annotate_arrow(a, tip)
    gm.set_stroke(vec_mx, c["COLOR-TEAL-LIGHT"])
    vec_sum = gm.annotate_arrow(gm.p0, tip)
    gm.set_stroke(vec_sum, c.BLUE)
    # both labels ride the moving tip
    half_x = x * 0.5
    lab_mx_x = a.x + half_x
    lab_mx = gm.annotate_text_box("Mx", lab_mx_x, 2.92, 14)
    gm.set_fill(lab_mx, c["COLOR-TEAL-LIGHT"])
    lab_sum_x = sum_x + 0.78
    lab_sum = gm.annotate_text_box("a + Mx", lab_sum_x, 2.92, 14)
    gm.set_fill(lab_sum, c.BLUE)
    # live readout of x and of the objective, computed from the nodes
    sq_x = sum_x * sum_x
    sq_y = a.y * a.y
    fsq = sq_x + sq_y
    readout = gm.annotate_text_box("x = ${x}; ‖a + Mx‖² = ${fsq}", 0, -3.35, 14, 6, -1)
    gm.set_fill(readout, c.BLUE)
    # level set of the objective: the circle of radius ‖a + Mx‖ about 0
    norm = gm.sqrt(fsq)
    level = gm.circle(gm.p0, norm)

with group("min-norm"):
    # the minimum-norm member of the family, and the right angle that defines it
    foot = gm.point(0, a.y)
    vec_min = gm.annotate_arrow(gm.p0, foot)
    gm.set_stroke(vec_min, c.AMBER)
    lab_min = gm.annotate_text_box("a + Mx*", 0, 2.95, 14)
    gm.set_fill(lab_min, c.AMBER)
    # the two helper rays must share the vertex id `foot` for the mark to render
    ray_to_origin = gm.line(foot, gm.p0)
    ray_along_set = gm.line(foot, a)
    # right_angle = gm.annotate_angle_mark(ray_to_origin, ray_along_set, "90°")
    # gm.set_stroke(right_angle, c.AMBER)
    gm.hide(ray_to_origin, ray_along_set)
```

Find $x$ for which $\|\blue{a} + \pink{M}\amber{x}\|^2$ is minimized. Here $a$ is {shown here}(ref:affine-set) and the vector sum {looks like this}(ref:slide-x). The radius of the circle is the length of the vector sum. {The vector with minimum norm}(ref:min-norm) is shown in bold.

---

## Solution

We can write the function as:

$$\begin{aligned}
f(\amber{x}) &= \langle \blue{a} + \pink{M}\amber{x}, \; \blue{a} + \pink{M}\amber{x} \rangle \\ \\
&= \langle \blue{a}, \blue{a} \rangle + 2 \langle \blue{a}, \pink{M}\amber{x} \rangle + \langle \pink{M}\amber{x}, \pink{M}\amber{x} \rangle \\ \\
&= \underbrace{\langle \blue{a}, \blue{a} \rangle}_{\text{constant}} + 2 \underbrace{\langle \pink{M}^T\blue{a}, \amber{x} \rangle}_{\text{linear in } \amber{x}} + \underbrace{\langle \pink{M}^T\pink{M}\amber{x}, \amber{x} \rangle}_{\text{quadratic in } \amber{x}} \\ \\
\end{aligned}$$

```pygeomatic
gm.hide(lab_sum, lab_mx)
```

It then follows that $\nabla f(\amber{x}) = 2\pink{M}^T\blue{a} + 2\pink{M}^T\pink{M}\amber{x}$. Setting it to zero, we get the solution:

> $$\amber{x}^* = -(\pink{M}^T\pink{M})^{-1}\pink{M}^T\blue{a}$$

Note that $\pink{M}\amber{x}^*$ is {the negative projection of $\blue{a}$}(gm.animate(x, -2)) onto the column space of $\pink{M}$.

> The optimal value of $\amber{x}$ is one for which $\pink{M}\amber{x}$ is the negative of the projection of $\blue{a}$ onto the column space of $\pink{M}$. 

Ideally, if $\pink{M}\amber{x}$ were equal to $-\blue{a}$, the norm of the sum would be zero. But that may not be possible if $\blue{a}$ is not in the column space of $\pink{M}$. Simply said, the optimal value of $\amber{x}$ tries to get $\pink{M}\amber{x}$ as close as possible to $-\blue{a}$.

