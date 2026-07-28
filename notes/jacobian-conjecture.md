# Jacobian Conjecture: the missing context

#### Why I wrote this post
The Jacobian Conjecture was recently [proven to be wrong](https://terrytao.wordpress.com/2026/07/21/a-digestion-of-the-jacobian-conjecture-counterexample/) for the case of dimension 3 and higher by Fable (and then an internal OpenAI model IIRC). Most of the posts I came across were either about the sequence of events leading to the announcement or an interpretation of the example used to disprove it. This kind of discourse makes sense for someone who is well familiar with it and working in a related area. 

This article is written for someone who is not, yet is curious and knowledgeable enough to understand it. It takes the reader on a journey to understand what the conjecture is, what motivated it, and a couple of other related statements which were believed to be true but were later disproven, much like the conjecture itself.

#

## Polynomials and invertibility in $\mathbb{R}^n$ and $\mathbb{C}^n$
Consider a function $f$ that maps $x \in \mathbb{R}^n$ (or $\mathbb{C}^n$) to $y$ in the same space, where every output coordinate is a polynomial in the input coordinates:

$$f\begin{pmatrix} \blue{x_1} \\ \vdots \\ \blue{x_n} \end{pmatrix} = \begin{pmatrix} \pink{y_1} \\ \vdots \\ \pink{y_n} \end{pmatrix}, \qquad \pink{y_i} = p_i(\blue{x_1}, \blue{x_2}, \dots, \blue{x_n})$$

Now add some constraints:

- This function may or may not be invertible. 
- Assume it is invertible. The inverse function $g$ may or may not be a polynomial.
- Assume it is a polynomial function.

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

The field genuinely does not matter here. The only thing the argument used was that degrees add under multiplication, $\deg(PQ) = \deg P + \deg Q$, which is true over $\mathbb{R}$ and $\mathbb{C}$ alike. The fundamental theorem of algebra is a $\mathbb{C}$-only fact and does not enter until the next section, which is exactly where $\mathbb{R}$ and $\mathbb{C}$ start behaving differently.

---

### Implication: local and global invertibility
To understand the implication of it, it is important to understand local and global invertibility. 

- A function can be invertible locally i.e. in the neighborhood of a given point. 
- If the inverse of a function $f$ exists, it implies that $f$ is invertible everywhere. 
Thus global invertibility implies local invertibility everywhere. **Two unique inputs always map to two unique outputs**.

Now one can ask:
- If a function is locally invertible everywhere, does it make it globally invertible? A handwavy but useful way to frame it is:

> If two arbitrarily close but unique input points always map to unique output points, does it imply that two unique input points, no matter how far apart they are, also map to unique output points? 

Plainly said, does local invertibility imply global invertibility?

$$\begin{aligned}
&\underbrace{\text{for every } a \text{ there is a neighborhood } U \ni a \text{ on which } f \text{ is injective}}_{\amber{\text{locally invertible everywhere}}} \\ \\
\overset{?}{\Longrightarrow} \quad &\underbrace{f(a) = f(b) \; \Longrightarrow \; a = b \quad \text{for all } a, b}_{\green{\text{globally invertible}}}
\end{aligned}$$

---

## Time for some detours 
Before getting to the conjecture, let's spend some more time on the implication and the behavior it leads to.

### A detour from polynomial functions

```pygeomatic
gm.clear()
unit = gm.scalar(50)
gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

pts = {}
pending = []

def P(x, y):
    key = (round(x, 4), round(y, 4))
    node = pts.get(key)
    if node is None:
        node = gm.point(key[0], key[1], out=f"q-{len(pts)}")
        pts[key] = node
        pending.append(node)
    return node

def hide_helpers():
    if pending:
        gm.hide(*pending)
        pending.clear()

with group("exp-local"):
    # the two complex planes, then z0 and the disc on which exp is injective
    z_re_axis = gm.line(P(-5.9, -3.4), P(-0.7, -3.4))
    z_im_axis = gm.line(P(-3.2, -4.3), P(-3.2, 4.7))
    w_re_axis = gm.line(P(0.7, 0.0), P(5.9, 0.0))
    w_im_axis = gm.line(P(3.0, -2.4), P(3.0, 2.6))
    gm.set_stroke(z_re_axis, c["COLOR-GRAY-MID"])
    gm.set_stroke(z_im_axis, c["COLOR-GRAY-MID"])
    gm.set_stroke(w_re_axis, c["COLOR-GRAY-MID"])
    gm.set_stroke(w_im_axis, c["COLOR-GRAY-MID"])
    hide_helpers()
    zlow = gm.point(-2.8, -2.5)
    disc = gm.circle(zlow, 0.35)
    gm.set_stroke(zlow, c.WHITE)
    gm.set_stroke(disc, c.AMBER)

with group("exp-two"):
    # the second preimage z0 + 2pi i, and the single image point it shares
    zhigh = gm.point(-2.8, 3.783)
    column = gm.line(zlow, zhigh)
    gm.set_stroke(zhigh, c.WHITE)
    gm.set_stroke(column, c["COLOR-GRAY-MID"])
    wimg = gm.point(3.927, 1.169)
    wring = gm.circle(wimg, 0.18)
    gm.set_stroke(wimg, c["COLOR-RED-MID"])
    gm.set_stroke(wring, c["COLOR-RED-MID"])

with group("exp-collapse"):
    # two preimages, one image: the arrows, the names, the 2pi i period
    lower_ctrl = P(0.4, -3.2)
    upper_ctrl = P(0.4, 4.2)
    hide_helpers()
    lower_map = gm.annotate_curved_arrow(zlow, wimg, lower_ctrl, 0.07, "exp")
    upper_map = gm.annotate_curved_arrow(zhigh, wimg, upper_ctrl, 0.07, "exp")
    gm.set_stroke(lower_map, c["COLOR-RED-MID"])
    gm.set_stroke(upper_map, c["COLOR-RED-MID"])
    plane_z = gm.annotate_text_box("z-plane", -3.2, 5.4, 14)
    plane_w = gm.annotate_text_box("w-plane", 3.0, 5.4, 14)
    gm.set_fill(plane_z, c["COLOR-GRAY-MID"])
    gm.set_fill(plane_w, c["COLOR-GRAY-MID"])
    lab_z0 = gm.annotate_text_box("z₀", -2.15, -1.75, 14)
    lab_z1 = gm.annotate_text_box("z₀ + 2πi", -4.3, 3.783, 14)
    lab_w0 = gm.annotate_text_box("exp(z₀)", 4.75, 1.169, 14)
    gm.set_fill(lab_z0, c.WHITE)
    gm.set_fill(lab_z1, c.WHITE)
    gm.set_fill(lab_w0, c["COLOR-RED-MID"])
    lab_disc = gm.annotate_text_box("injective here", -4.85, -2.5, 14)
    gm.set_fill(lab_disc, c.AMBER)
    period = gm.annotate_dim_line(zlow, zhigh, "2πi")
    gm.set_stroke(period, c["COLOR-RED-MID"])
    gm.annotate_text_box(
        "z₀ and z₀ + 2πi are far apart, yet exp sends both to the same point. "
        "Injective on the amber disc, never on all of ℂ.",
        2.9,
        -4.3,
        14,
        5.4,
        -1,
    )
```

Local invertibility does not imply global invertibility in general. A simple example is $f(z) = e^z, z \in \mathbb{C}$. In every {small enough neighborhood}(ref:exp-local), this function is invertible. However it is obviously not globally invertible {}(ref:exp-two) since the points $z$ and $z + 2\pi i$ {map to the same point}(ref:exp-collapse). 

$$\underbrace{f'(z) = e^z \ne 0 \text{ for every } z}_{\amber{\text{locally invertible everywhere}}} \qquad \text{but} \qquad \underbrace{e^{\,z + 2\pi i} = e^z}_{\rose{\text{global invertibility fails}}}$$

---

### A detour from constant Jacobians
Note that for a function to be (globally) invertible, its Jacobian determinant doesn't need to be a non-zero constant. It just needs to be non-zero everywhere. 

- In the field $\mathbb{C}$, they are the same thing. The only polynomial in $\mathbb{C}$ that never vanishes is a non-zero constant $c$. This is due to [the fundamental theorem of algebra](https://en.wikipedia.org/wiki/Fundamental_theorem_of_algebra): a non-constant polynomial always has a root.

$$\underbrace{P(z) \ne 0 \text{ for every } z \in \mathbb{C}}_{\amber{\text{never vanishes}}} \; \iff \; \underbrace{P(z) = c \ne 0}_{\green{\text{is a non-zero constant}}}$$

  - It implies the only invertible polynomial in $\mathbb{C}$ is of the form $f(z) = az + b$ with $a \ne 0$. This is only true for single dimension.

#### A common gotcha

```pygeomatic
gm.clear()
unit = gm.scalar(50)
gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

pts = {}
pending = []

def P(x, y):
    key = (round(x, 4), round(y, 4))
    node = pts.get(key)
    if node is None:
        node = gm.point(key[0], key[1], out=f"q-{len(pts)}")
        pts[key] = node
        pending.append(node)
    return node

def hide_helpers():
    if pending:
        gm.hide(*pending)
        pending.clear()

xs = -4.4  # source local origin
xi = 2.1  # image local origin
y_levels = [-1.5, -1.0, -0.5, 0.0, 0.5, 1.0, 1.5]
x_levels = [-1.0, 0.0, 1.0]

with group("gotcha-domain"):
    # the source grid, then the unit square sitting inside it
    for i, yc in enumerate(y_levels):
        ln = gm.line(P(xs - 1.0, yc), P(xs + 1.0, yc), out=f"src-y-{i}")
        gm.set_stroke(ln, c.PINK)
    for i, xc in enumerate(x_levels):
        ln = gm.line(P(xs + xc, -1.5), P(xs + xc, 1.5), out=f"src-x-{i}")
        gm.set_stroke(ln, c.BLUE)
    hide_helpers()
    sq_bl = P(xs + 0.0, 0.5)
    sq_br = P(xs + 1.0, 0.5)
    sq_tr = P(xs + 1.0, 1.5)
    sq_tl = P(xs + 0.0, 1.5)
    src_bottom = gm.line(sq_bl, sq_br)
    src_right = gm.line(sq_br, sq_tr)
    src_top = gm.line(sq_tr, sq_tl)
    src_left = gm.line(sq_tl, sq_bl)
    hide_helpers()
    for edge in (src_bottom, src_right, src_top, src_left):
        gm.set_stroke(edge, c.AMBER)

with group("gotcha-image"):
    # pink lines only slide, blue lines bend, and the square comes along
    for i, yc in enumerate(y_levels):
        shift = yc * yc
        ln = gm.line(P(xi - 1.0 + shift, yc), P(xi + 1.0 + shift, yc), out=f"img-y-{i}")
        gm.set_stroke(ln, c.PINK)
    for i, xc in enumerate(x_levels):
        # x = xc + y^2 over y in [-1.5, 1.5] is an exact quadratic Bezier
        # with control point (xc + a*b, (a+b)/2), a = -1.5, b = 1.5.
        curve = gm.bezier_quadratic(
            P(xi + xc + 2.25, -1.5),
            P(xi + xc - 2.25, 0.0),
            P(xi + xc + 2.25, 1.5),
            out=f"img-x-{i}",
        )
        gm.set_stroke(curve, c.BLUE)
    hide_helpers()
    im_bl = P(xi + 0.25, 0.5)
    im_br = P(xi + 1.25, 0.5)
    im_tr = P(xi + 3.25, 1.5)
    im_tl = P(xi + 2.25, 1.5)
    im_bottom = gm.line(im_bl, im_br)
    im_right = gm.bezier_quadratic(im_br, P(xi + 1.75, 1.0), im_tr)
    im_top = gm.line(im_tr, im_tl)
    im_left = gm.bezier_quadratic(im_tl, P(xi + 0.75, 1.0), im_bl)
    for edge in (im_bottom, im_right, im_top, im_left):
        gm.set_stroke(edge, c.AMBER)
    hide_helpers()
    cap_src = gm.annotate_text_box("domain", xs, 2.7, 14)
    cap_img = gm.annotate_text_box("image under f", 3.225, 2.7, 14)
    gm.set_fill(cap_src, c["COLOR-GRAY-MID"])
    gm.set_fill(cap_img, c["COLOR-GRAY-MID"])
    lab_det = gm.annotate_text_box("det J = 1 everywhere", -1.15, 1.5, 14)
    gm.set_fill(lab_det, c.AMBER)
    map_arrow = gm.annotate_arrow(
        P(-2.9, 0.0), P(0.6, 0.0), 0.05, "f(x, y) = (x + y², y)"
    )
    hide_helpers()
    gm.set_stroke(map_arrow, c["COLOR-GRAY-MID"])

with group("gotcha-area"):
    # the Cavalieri reading: every horizontal slice keeps its length
    span_src = gm.annotate_dim_line(sq_tr, sq_tl)
    span_img = gm.annotate_dim_line(im_tr, im_tl)
    gm.set_stroke(span_src, c.AMBER)
    gm.set_stroke(span_img, c.AMBER)
    gm.annotate_text_box(
        "Every horizontal slice keeps its length and only slides right by y², "
        "so the bent image has exactly the area of the square. That is det J = 1.",
        0,
        -2.9,
        14,
        8,
        -1,
    )
```

In higher dimensions, there can be {higher degree polynomials}(ref:gotcha-domain) with a {constant Jacobian determinant}(ref:gotcha-image). Eg 

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} + \pink{y}^2 \\ \pink{y} \end{pmatrix}$$

Even though the first coordinate is quadratic, the $\pink{y}$ that would carry the degree sits in the *upper right* of the Jacobian, where the determinant {never multiplies it}(ref:gotcha-area) against anything but a zero:

$$\begin{aligned}
J_f &= \begin{pmatrix}
\frac{\partial}{\partial \blue{x}}\big(\blue{x} + \pink{y}^2\big) & \frac{\partial}{\partial \pink{y}}\big(\blue{x} + \pink{y}^2\big) \\
\frac{\partial}{\partial \blue{x}}\big(\pink{y}\big) & \frac{\partial}{\partial \pink{y}}\big(\pink{y}\big)
\end{pmatrix} \\ \\
&= \begin{pmatrix} 1 & 2\pink{y} \\ 0 & 1 \end{pmatrix} \\ \\
\det J_f &= 1 \cdot 1 - \underbrace{2\pink{y} \cdot 0}_{\text{the degree dies here}} = \amber{1}
\end{aligned}$$

#

> Therefore, for polynomial maps over complex numbers, saying "the Jacobian is a non-zero constant" and saying "the map is locally invertible everywhere" are mathematically identical statements.

- However, in the field $\mathbb{R}$, they are not the same thing. E.g. $x^2 + 1$ is always positive but not constant.

$$\underbrace{x^2 + 1 > 0 \text{ for every } x \in \mathbb{R}}_{\amber{\text{never vanishes}}} \qquad \text{yet} \qquad \underbrace{x^2 + 1 \ne c}_{\rose{\text{not a constant}}}$$

  - For a long time, this was the OG Jacobian conjecture (the strong real Jacobian conjecture): it was believed that in the field of real numbers, if a $\mathbb{R}^n \to \mathbb{R}^n$ polynomial map had a non-zero Jacobian determinant everywhere (implying local invertibility everywhere), it had a global inverse.

$$\underbrace{\det J_f(x) \ne 0 \text{ for every } x \in \mathbb{R}^n}_{\amber{\text{locally invertible everywhere}}} \; \overset{\rose{\times}}{\Longrightarrow} \; \underbrace{f \text{ has a global inverse}}_{\rose{\text{disproven in 1994}}}$$

Note that the strong real conjecture did not require the inverse to be a polynomial. It only conjectured the existence of an inverse, that's it.

This conjecture was [disproven by a counterexample](https://scholar.google.com/scholar_lookup?doi=10.1007/bf02571929) in 1994. 

---

### A detour to constant Jacobian real polynomial maps
Another interesting development in this area was related to the case of inverse polynomial maps for constant Jacobian functions i.e. polynomial functions with a constant Jacobian that also had an inverse polynomial function. Every example could be decomposed to one of these three patterns:

**An affine map** $f(x) = Ax + b$, whose Jacobian is the constant matrix $A$ itself:

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} 2 & 1 \\ 1 & 1 \end{pmatrix}\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} + \begin{pmatrix} 3 \\ -1 \end{pmatrix} = \begin{pmatrix} 2\blue{x} + \pink{y} + 3 \\ \blue{x} + \pink{y} - 1 \end{pmatrix}$$

$$\det J_f = \det \begin{pmatrix} 2 & 1 \\ 1 & 1 \end{pmatrix} = 2 - 1 = \amber{1}$$

```pygeomatic
gm.clear()
unit = gm.scalar(56)
gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

pts = {}
pending = []

def P(x, y):
    key = (round(x, 4), round(y, 4))
    node = pts.get(key)
    if node is None:
        node = gm.point(key[0], key[1], out=f"q-{len(pts)}")
        pts[key] = node
        pending.append(node)
    return node

def hide_helpers():
    if pending:
        gm.hide(*pending)
        pending.clear()

a = 0.9  # half-width of each panel; a^3 = 0.729
a3 = 0.729
x1, x2, x3 = -3.55, 0.0, 3.55

def flat_panel(ox, tag, color):
    edges = [
        gm.line(P(ox - a, -a), P(ox + a, -a), out=f"{tag}-bottom"),
        gm.line(P(ox + a, -a), P(ox + a, a), out=f"{tag}-right"),
        gm.line(P(ox + a, a), P(ox - a, a), out=f"{tag}-top"),
        gm.line(P(ox - a, a), P(ox - a, -a), out=f"{tag}-left"),
        gm.line(P(ox - a, 0.0), P(ox + a, 0.0), out=f"{tag}-midh"),
        gm.line(P(ox, -a), P(ox, a), out=f"{tag}-midv"),
    ]
    hide_helpers()
    for e in edges:
        gm.set_stroke(e, color)

def cubic_row(yc, tag):
    # exact cubic Bezier for y = yc + x^3 on x in [-a, a]:
    # x-controls (-a, -a/3, a/3, a), y-controls yc + a^3 * (-1, 1, -1, 1)
    return gm.bezier_cubic(
        P(x2 - a, yc - a3),
        P(x2 - a / 3.0, yc + a3),
        P(x2 + a / 3.0, yc - a3),
        P(x2 + a, yc + a3),
        out=tag,
    )

with group("tri-start"):
    flat_panel(x1, "start", c.BLUE)

with group("tri-bend"):
    # after f: horizontal lines bend into y = c + x³, vertical lines slide up
    bent_bottom = cubic_row(-a, "bent-bottom")
    bent_right = gm.line(P(x2 + a, -a + a3), P(x2 + a, a + a3))
    bent_top = cubic_row(a, "bent-top")
    bent_left = gm.line(P(x2 - a, a - a3), P(x2 - a, -a - a3))
    bent_midh = cubic_row(0.0, "bent-midh")
    bent_midv = gm.line(P(x2, -a), P(x2, a))
    hide_helpers()
    for e in (bent_bottom, bent_right, bent_top, bent_left, bent_midh, bent_midv):
        gm.set_stroke(e, c.AMBER)

with group("tri-undo"):
    # after f inverse: exactly the square we started with
    flat_panel(x3, "back", c.EMERALD)
    dot_a = gm.point(x1 + a, 0.45)
    dot_b = gm.point(x2 + a, 0.45 + a3)
    dot_c = gm.point(x3 + a, 0.45)
    gm.set_stroke(dot_a, c.PINK)
    gm.set_stroke(dot_b, c.PINK)
    gm.set_stroke(dot_c, c.PINK)
    cap_1 = gm.annotate_text_box("(x, y)", x1, 2.8, 14)
    cap_2 = gm.annotate_text_box("(x, y + x³)", x2, 2.8, 14)
    cap_3 = gm.annotate_text_box("f⁻¹(f(x, y)) = (x, y)", x3, 2.8, 14)
    gm.set_fill(cap_1, c.BLUE)
    gm.set_fill(cap_2, c.AMBER)
    gm.set_fill(cap_3, c.EMERALD)
    lab_back = gm.annotate_text_box("back to the start", x3, -1.4, 14)
    gm.set_fill(lab_back, c.EMERALD)
    apply_f = gm.annotate_arrow(P(x1 + 1.1, 2.0), P(x2 - 1.1, 2.0), 0.08, "f")
    undo_f = gm.annotate_arrow(P(x2 + 1.1, 2.0), P(x3 - 1.1, 2.0), 0.08, "f⁻¹")
    gm.set_stroke(apply_f, c.AMBER)
    gm.set_stroke(undo_f, c.EMERALD)
    hide_helpers()
    rise = gm.annotate_dim_line(P(x2 + a, 0.45), dot_b, "x³")
    gm.set_stroke(rise, c.AMBER)
    hide_helpers()
    gm.annotate_text_box(
        "Each output coordinate is its own variable plus a polynomial in the "
        "earlier ones, so back-substitution inverts f exactly: subtract x³ and "
        "the square snaps back.",
        0,
        -2.8,
        14,
        8,
        -1,
    )
```

**A triangular map**, where each coordinate is {its own variable}(ref:tri-start) {plus a polynomial}(ref:tri-bend) in the *earlier* variables only. The Jacobian is then triangular with $1$s on the diagonal, so its determinant is $1$ for free, and the inverse can be {read off by back-substitution}(ref:tri-undo):

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^3 \end{pmatrix}, \qquad f^{-1}\begin{pmatrix} \blue{u} \\ \pink{v} \end{pmatrix} = \begin{pmatrix} \blue{u} \\ \pink{v} - \blue{u}^3 \end{pmatrix}$$

$$J_f = \begin{pmatrix} 1 & 0 \\ 3\blue{x}^2 & 1 \end{pmatrix} \; \Longrightarrow \; \det J_f = \amber{1}$$

The same trick stacks up in any dimension, one variable at a time:

$$f\begin{pmatrix} \blue{x} \\ \pink{y} \\ \cyan{z} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^3 \\ \cyan{z} + \pink{y}^2 + \blue{x} \end{pmatrix}, \qquad f^{-1}\begin{pmatrix} \blue{u} \\ \pink{v} \\ \cyan{w} \end{pmatrix} = \begin{pmatrix} \blue{u} \\ \pink{v} - \blue{u}^3 \\ \cyan{w} - (\pink{v} - \blue{u}^3)^2 - \blue{u} \end{pmatrix}$$

```pygeomatic
gm.clear()
unit = gm.scalar(34)
gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

pts = {}
pending = []

def P(x, y):
    key = (round(x, 4), round(y, 4))
    node = pts.get(key)
    if node is None:
        node = gm.point(key[0], key[1], out=f"q-{len(pts)}")
        pts[key] = node
        pending.append(node)
    return node

def hide_helpers():
    if pending:
        gm.hide(*pending)
        pending.clear()

with group("tame-frame"):
    # the container that defines "tame", and the two generators inside it
    title = gm.annotate_text_box("Constant-Jacobian polynomial maps", 0, 7.5, 13, 8, -1)
    gm.set_fill(title, c["COLOR-GRAY-LIGHT"])
    tame_frame = gm.rectangle(P(-8.0, 0.0), 16.0, 6.6)
    gm.set_stroke(tame_frame, c.EMERALD)
    hide_helpers()
    tame_chip = gm.annotate_text_box("TAME", -6.2, 6.6, 14, 1.6, -1)
    gm.set_fill(tame_chip, c.EMERALD)
    gen_affine = gm.annotate_text_box(
        "affine:  f(x, y) = (2x + y + 3, x + y - 1)", -4.2, 4.6, 12, 6.0, -1
    )
    gen_tri = gm.annotate_text_box(
        "triangular:  f(x, y) = (x, y + x³)", 4.2, 4.6, 12, 6.0, -1
    )
    gm.set_fill(gen_affine, c.EMERALD)
    gm.set_fill(gen_tri, c.EMERALD)

with group("tame-compose"):
    # they compose into something that looks like neither, same constant det
    feed_a = gm.annotate_arrow(P(-2.4, 3.6), P(-1.0, 2.3), 0.05)
    feed_b = gm.annotate_arrow(P(2.4, 3.6), P(1.0, 2.3), 0.05)
    gm.set_stroke(feed_a, c.EMERALD)
    gm.set_stroke(feed_b, c.EMERALD)
    hide_helpers()
    gen_comp = gm.annotate_text_box(
        "composition A∘T:  f(x, y) = (y + x², x)", 0.0, 1.3, 12, 6.0, -1
    )
    gm.set_fill(gen_comp, c.EMERALD)
    det_affine = gm.annotate_text_box("det J = 1", -4.2, 2.95, 11)
    det_tri = gm.annotate_text_box("det J = 1", 4.2, 2.95, 11)
    det_comp = gm.annotate_text_box("det J = -1", 5.2, 1.3, 11)
    gm.set_fill(det_affine, c.AMBER)
    gm.set_fill(det_tri, c.AMBER)
    gm.set_fill(det_comp, c.AMBER)

with group("tame-wild"):
    # a second bucket, sitting outside the first, with the same amber det J = 1
    wild_frame = gm.rectangle(P(-8.0, -4.85), 16.0, 3.6)
    gm.set_stroke(wild_frame, c["COLOR-RED-MID"])
    hide_helpers()
    wild_chip = gm.annotate_text_box("WILD", -6.2, -1.25, 14, 1.6, -1)
    gm.set_fill(wild_chip, c["COLOR-RED-MID"])
    nagata = gm.annotate_text_box(
        "Nagata (1972):  N(x, y, z) = (x - 2yΔ - zΔ², y + zΔ, z)  with  Δ = xz + y²",
        1.3,
        -2.5,
        12,
        11.0,
        -1,
    )
    gm.set_fill(nagata, c["COLOR-RED-MID"])
    det_nagata = gm.annotate_text_box("det J = 1", -4.2, -4.15, 11)
    gm.set_fill(det_nagata, c.AMBER)
    proven = gm.annotate_text_box("proven wild in 2003", 1.5, -4.15, 11)
    gm.set_fill(proven, c["COLOR-RED-MID"])

with group("tame-rule"):
    # what actually decides which bucket you land in
    rule_low = gm.annotate_text_box(
        "n ≤ 2:  every such map is tame", -4.1, -6.05, 12, 7.0, -1
    )
    rule_high = gm.annotate_text_box(
        "n ≥ 3:  wild maps exist", 4.1, -6.05, 12, 7.0, -1
    )
    gm.set_fill(rule_low, c.EMERALD)
    gm.set_fill(rule_high, c["COLOR-RED-MID"])
    summary = gm.annotate_text_box(
        "Nagata's map has the same det J = 1 as the tame generators, "
        "yet is not built from them.",
        0,
        -7.85,
        12,
        14,
        -1,
    )
    gm.set_fill(summary, c["COLOR-GRAY-LIGHT"])
```

**A composition of the above maps**, which is where it stops being obvious. Compose the triangular $T$ with the {affine coordinate swap}(ref:tame-frame) $A$:

$$T\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^2 \end{pmatrix}, \qquad A\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} = \begin{pmatrix} \pink{y} \\ \blue{x} \end{pmatrix}$$

$$\begin{aligned}
(A \circ T)\begin{pmatrix} \blue{x} \\ \pink{y} \end{pmatrix} &= A\begin{pmatrix} \blue{x} \\ \pink{y} + \blue{x}^2 \end{pmatrix} \\ \\
&= \underbrace{\begin{pmatrix} \pink{y} + \blue{x}^2 \\ \blue{x} \end{pmatrix}}_{\text{neither affine nor triangular}} \\ \\
\det J_{A \circ T} &= \det \begin{pmatrix} 2\blue{x} & 1 \\ 1 & 0 \end{pmatrix} = \amber{-1}
\end{aligned}$$

It looks like neither building block, yet it is {built from both}(ref:tame-compose), and its inverse is polynomial for the same reason: $(A \circ T)^{-1}(\blue{u}, \pink{v}) = (\pink{v}, \blue{u} - \pink{v}^2)$.

It was believed that all such polynomial functions were decomposable i.e. they were "tame" maps. In 1972, Japanese mathematician Nagata proposed a polynomial map which did not seem to be decomposable - a {"wild" map}(ref:tame-wild):

$$N\begin{pmatrix} \blue{x} \\ \pink{y} \\ \cyan{z} \end{pmatrix} = \begin{pmatrix} \blue{x} - 2\pink{y}\,\teal{\Delta} - \cyan{z}\,\teal{\Delta}^2 \\ \pink{y} + \cyan{z}\,\teal{\Delta} \\ \cyan{z} \end{pmatrix}, \qquad \teal{\Delta} = \blue{x}\cyan{z} + \pink{y}^2, \qquad \det J_N = \amber{1}$$

This fact was [proven to be true](https://doi.org/10.1090/S0894-0347-03-00440-5) only in 2003. This led to the fall of yet another conjecture related to constant Jacobian polynomial maps. 

As of today, this is {what we know}(ref:tame-rule):
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
  - uses the fact that the only invertible polynomial in $\mathbb{C}$ is of the form $f(z) = az + b$ - something we saw earlier.
- Since it always exists, $D_i(y) \ne 0$, which is possible only if $D_i(y)$ is a constant. Thus $x_i$ is a polynomial for all $i$.

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

```pygeomatic
gm.clear()
unit = gm.scalar(38)
gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

pts = {}
pending = []

def P(x, y):
    key = (round(x, 4), round(y, 4))
    node = pts.get(key)
    if node is None:
        node = gm.point(key[0], key[1], out=f"q-{len(pts)}")
        pts[key] = node
        pending.append(node)
    return node

def hide_helpers():
    if pending:
        gm.hide(*pending)
        pending.clear()

rows = [
    (
        3.4,
        "any holomorphic f:  f'(z) ≠ 0 everywhere",
        "f is injective on ℂ",
        "false - counterexample: exp(z)",
    ),
    (
        0.0,
        "strong real Jacobian conjecture:  det J ≠ 0 everywhere on ℝⁿ",
        "f has a global inverse",
        "false - disproven in 1994",
    ),
    (
        -3.4,
        "Jacobian Conjecture:  det J = c ≠ 0 on ℂⁿ",
        "f⁻¹ exists and is a polynomial map",
        "false for n ≥ 3, 2026 - stood 90 years",
    ),
]

with group("imp-table"):
    # the question, the column headers, and the hypothesis column that always holds
    banner = gm.annotate_text_box(
        "Does local invertibility imply global invertibility?",
        0,
        6.4,
        15,
        12,
        -1,
    )
    gm.set_fill(banner, c["COLOR-GRAY-LIGHT"])
    head_hyp = gm.annotate_text_box("hypothesis", -4.3, 5.0, 12)
    head_con = gm.annotate_text_box("conclusion", 4.3, 5.0, 12)
    gm.set_fill(head_hyp, c.AMBER)
    gm.set_fill(head_con, c.EMERALD)
    for i, (y, hyp, _con, _cap) in enumerate(rows):
        box = gm.annotate_text_box(hyp, -4.3, y, 13, 4.8, -1, out=f"hyp-{i}")
        gm.set_fill(box, c.AMBER)

with group("imp-conclusion"):
    # the implication each setting would like to make, and what it hoped to conclude
    for i, (y, _hyp, _con, _cap) in enumerate(rows):
        link = gm.annotate_arrow(P(-1.45, y), P(1.45, y), 0.04, out=f"link-{i}")
        gm.set_stroke(link, c["COLOR-RED-MID"])
    hide_helpers()
    for i, (y, _hyp, con, _cap) in enumerate(rows):
        box = gm.annotate_text_box(con, 4.3, y, 13, 4.8, -1, out=f"con-{i}")
        gm.set_fill(box, c.EMERALD)

with group("imp-verdict"):
    # the badges land: the same answer three times over, and only now does the
    # column get its name - an empty labelled column reads as a broken render
    head_ver = gm.annotate_text_box("verdict", 0.0, 5.0, 12)
    gm.set_fill(head_ver, c["COLOR-RED-MID"])
    for i, (y, _hyp, _con, _cap) in enumerate(rows):
        badge = gm.annotate_text_box("✗", 0.0, y, 18, 0.8, -1, out=f"badge-{i}")
        gm.set_fill(badge, c["COLOR-RED-MID"])
    for i, (y, _hyp, _con, cap) in enumerate(rows):
        note = gm.annotate_text_box(cap, 4.3, round(y - 1.55, 4), 11, out=f"cap-{i}")
        gm.set_fill(note, c["COLOR-RED-MID"])
    newest = gm.rectangle(P(-7.3, -5.45), 14.6, 3.3)
    gm.set_stroke(newest, c["COLOR-RED-MID"])
    hide_helpers()
    footer = gm.annotate_text_box(
        "In every row the hypothesis holds and the conclusion fails: "
        "local invertibility never buys global invertibility.",
        0,
        -6.75,
        13,
        13,
        -1,
    )
    gm.set_fill(footer, c["COLOR-GRAY-LIGHT"])
```

The conjecture essentially claims that for polynomials functions, local invertibility {implies global invertibility}(ref:imp-table).

This is a {massive claim}(ref:imp-conclusion) because, outside of complex polynomials, local invertibility {does not guarantee}(ref:imp-verdict) global invertibility. We saw the example of $e^z$ earlier.  

$$\begin{aligned}
\text{any holomorphic } f: \quad & \underbrace{f'(z) \ne 0 \text{ everywhere}}_{\amber{\text{local}}} \; \overset{\rose{\times}}{\Longrightarrow} \; \underbrace{f \text{ is injective}}_{\rose{\text{fails: } e^z}} \\ \\
\text{polynomial } f: \quad & \underbrace{\det J_f = c \ne 0}_{\amber{\text{local}}} \; \overset{?}{\Longrightarrow} \; \underbrace{f \text{ is injective}}_{\green{\text{the conjecture}}}
\end{aligned}$$

