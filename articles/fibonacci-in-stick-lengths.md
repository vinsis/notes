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

```pygeomatic
gm.clear()
c = gm.load_colors()
unit = gm.scalar(50)
gm.scalar(0.2, out="grid-opacity")

# Four step chips, read left→right on the top row then right→left on the bottom.
# width=4.0, height=1.15 at unit=50 → footprint 220 x 68 px (the +10 px pad on
# every side is included), so centres 5.8 apart leave a 70 px arrow corridor.
steps = [
    (-2.9, 1.15, "Step 1 · Sort the lengths: l₁ ≤ l₂ ≤ … ≤ lₙ", "BLUE"),
    (2.9, 1.15, "Step 2 · Write l as a matrix times the vector of gaps ε", "BLUE"),
    (2.9, -1.15, "Step 3 · Read off the longest length lₙ in that basis", "AMBER"),
    (-2.9, -1.15, "Step 4 · Impose lₙ ≤ 1 and get a simplex", "AMBER"),
]
flow = [
    ((-0.75, 1.15), (0.75, 1.15)),    # step 1 → step 2
    ((2.9, 0.3), (2.9, -0.3)),        # step 2 → step 3
    ((0.75, -1.15), (-0.75, -1.15)),  # step 3 → step 4
]

def chip(i):
    x, y, body, col = steps[i]
    box = gm.annotate_text_box(body, x, y, 13, 4.0, 1.15, out="step-%d" % i)
    gm.set_stroke(box, c["COLOR-" + col])

def arrow(k):
    (ax, ay), (bx, by) = flow[k]
    a = gm.point(ax, ay, out="flow-a-%d" % k)
    b = gm.point(bx, by, out="flow-b-%d" % k)
    tip = gm.annotate_arrow(a, b, 0, out="flow-%d" % k)
    gm.hide(a, b)
    gm.set_stroke(tip, c.GRAY)

with group("outline-sort"):
    chip(0)

with group("outline-basis"):
    arrow(0)
    chip(1)

with group("outline-longest"):
    arrow(1)
    chip(2)

with group("outline-simplex"):
    arrow(2)
    chip(3)
```

Here is the overall idea:
- {Sort the lengths}(ref:outline-sort) $l_i$ from shortest to longest.
- The vector of sorted lengths can be shown to be a {linear combination}(ref:outline-basis) of a certain basis.
- Once we get this basis, we use it {express the longest length}(ref:outline-longest) $l_n$ in terms of it.
- Finally we use the condition that $\text{longest length} \le 1$ to {get a simplex}(ref:outline-simplex).

These steps are implemented below:

---

## 1. Steps 1 and 2 - Expressing sorted stick lengths as a matrix-vector product

We do this for two cases - the case where the lengths can not form a triangle and the general case (the lengths may or may not form a triangle).

### 1.1 Lengths which cannot form a triangle

```pygeomatic
gm.clear()
c = gm.load_colors()
unit = gm.scalar(60)
gm.scalar(0.25, out="grid-opacity")

L1, E2, E3 = 1.2, 0.8, 0.9
x0 = -2.5          # common left edge of every stick
h = 0.42           # bar height
rows = {
    "l1": (2.05, [(L1, "BLUE")]),
    "l2": (0.95, [(L1, "BLUE"), (E2, "AMBER")]),
    "l3": (-0.35, [(L1, "BLUE"), (L1, "BLUE"), (E2, "AMBER"), (E3, "GREEN")]),
}
seg_labels = {
    "l1": ["ε₁"],
    "l2": ["l₁", "ε₂"],
    "l3": ["l₁", "l₁", "ε₂", "ε₃"],
}
right = {}

# One beat per row: the row's bars, its row tag and its segment labels, so a
# click reveals only the length the sentence is talking about.
def draw_row(name, tag_text, first, last):
    y, blocks = rows[name]
    tags = seg_labels[name]
    x = x0 + sum(w for w, _c in blocks[:first])
    corners = []
    for k in range(first, last):
        w, col = blocks[k]
        corner = gm.point(x, y, out="corner-%s-%d" % (name, k))
        corners.append(corner)
        block = gm.rectangle(corner, w, h, out="bar-%s-%d" % (name, k))
        gm.set_stroke(block, c["COLOR-" + col])
        x += w
    right[name] = x
    gm.hide(*corners)                 # added: corner dots are scaffolding
    if first == 0:
        gm.annotate_text_box(tag_text, x0 - 0.55, y + h / 2, 16)
    xt = x0 + sum(w for w, _c in blocks[:first])
    for k in range(first, last):
        w, _col = blocks[k]
        gm.annotate_text_box(tags[k], xt + w / 2, y + h / 2, 13)
        xt += w

with group("sticks-row1"):
    draw_row("l1", "l₁", 0, 1)

with group("sticks-row2"):
    draw_row("l2", "l₂", 0, 2)

with group("sticks-row3"):
    # only the l₁ + l₁ + ε₂ part: this is the failure threshold itself
    draw_row("l3", "l₃", 0, 3)
    y3 = rows["l3"][0]
    b1 = gm.point(x0, y3 - 0.18)
    b2 = gm.point(x0 + 2 * L1 + E2, y3 - 0.18)
    gm.hide(b1, b2)                   # added: bracket anchors are scaffolding
    gm.annotate_curly_bracket(b1, b2, "l₁ + l₂  (the failure threshold)")

with group("sticks-slack"):
    draw_row("l3", "l₃", 3, 4)        # the ε₃ block the slack sentence adds
    tip = gm.point(right["l3"], y3 + h - 0.2)
    elbow = gm.point(right["l3"] + 0.9, y3 + 1.35)
    gm.annotate_leader_line(tip, elbow, "slack ε₃ ≥ 0")
    gm.hide(tip, elbow)               # added: leader anchors are scaffolding
    gm.annotate_text_box(
        "Sort the sticks. Three of them fail to form a triangle exactly when "
        "the longest is at least the sum of the other two, so l₃ = 2l₁ + ε₂ + ε₃ "
        "with every ε ≥ 0. Each new stick swallows the two before it, which is "
        "where the Fibonacci coefficients are born.",
        0.0, -3, 13, 8.0, -1,
    )

with group("fib-recurrence"):
    gm.clear()
    c = gm.load_colors()
    unit = gm.scalar(58)
    gm.scalar(0.25, out="grid-opacity")

    fib = [1, 1, 2, 3, 5, 8]
    s = 0.5            # units per Fibonacci count
    x0 = -2.3
    h = 0.4
    ys = [2.4 - 0.82 * i for i in range(6)]
    fhidden = []
    for i, f in enumerate(fib):
        y = ys[i]
        if i < 2:
            parts = [(f, "GRAY")]
        else:
            parts = [(fib[i - 1], "BLUE"), (fib[i - 2], "AMBER")]
        x = x0
        for j, (w, col) in enumerate(parts):
            corner = gm.point(x, y, out="fcorner-%d-%d" % (i, j))
            fhidden.append(corner)
            bar = gm.rectangle(corner, w * s, h, out="fbar-%d-%d" % (i, j))
            gm.set_stroke(bar, c["COLOR-" + col])
            x += w * s
    gm.hide(*fhidden)                 # added: corner dots are scaffolding

    # The same argument, drawn: each bar is the two above it laid end to end.
    a1 = gm.point(x0 + fib[5] * s + 0.15, ys[5] + h / 2)
    a2 = gm.point(x0 + fib[3] * s + 0.15, ys[3] + h / 2)
    ctrl = gm.point(x0 + fib[5] * s + 1.9, ys[4] + h / 2)
    grow = gm.annotate_curved_arrow(a2, a1, ctrl, 0.06, "F[i] = F[i-1] + F[i-2]")
    gm.set_stroke(grow, c.VIOLET)
    gm.hide(a1, a2, ctrl)             # added: arrow anchors are scaffolding

    # F₁, F₂, F₃ belong to "the same argument": they are the base case and the
    # first step of the recurrence.
    def label_row(i):
        y = ys[i] + h / 2
        gm.annotate_text_box("F%s" % "₁₂₃₄₅₆"[i], x0 - 0.55, y, 15)
        if i >= 2:
            gm.annotate_text_box(
                "F%s" % "₁₂₃₄₅"[i - 1], x0 + fib[i - 1] * s / 2, y, 12
            )
            gm.annotate_text_box(
                "F%s" % "₁₂₃₄"[i - 2], x0 + fib[i - 1] * s + fib[i - 2] * s / 2, y, 12
            )
        gm.annotate_text_box("= %d" % fib[i], x0 + fib[i] * s + 0.55, y, 14)

    for i in (0, 1, 2):
        label_row(i)

with group("fib-lengths"):
    for i in (3, 4, 5):
        label_row(i)

with group("fib-column"):
    # width 7.6 at unit 58 → 231 px half-width including the 10 px pad; 3 wrapped
    # lines at fontSize 13 → 37 px half-height, clear of the lowest bar at y=-1.7.
    gm.annotate_text_box(
        "These coefficients are exactly the first column of the matrix: "
        "1, 1, 2, 3, 5, 8, …, Fₙ, with the shifted copies filling the "
        "columns to its right.",
        0.0, -3, 13, 7.6, -1,
    )
```

Given the {shortest length}(ref:sticks-row1) is $\blue{l_1} = \teal{\varepsilon_1}$, we have that {$l_2 = \blue{l_1} + \teal{\varepsilon_2}$}(ref:sticks-row2). Note that $l_3$ {has to be bigger than}(ref:sticks-row3) $l_1 + l_2 = 2\blue{l_1} + \teal{\varepsilon_2}$. {We can assume that}(ref:sticks-slack) $l_3 = 2\blue{l_1} + \teal{\varepsilon_2} + \teal{\varepsilon_3}$. {Using the same argument}(ref:fib-recurrence), the {next three lengths}(ref:fib-lengths) can be written down as:

$$\begin{aligned}
l_4 &= \amber{3}\blue{l_1} + \amber{2}\teal{\varepsilon_2} + \amber{1}\teal{\varepsilon_3} + \amber{1}\teal{\varepsilon_4} \\ \\
l_5 &= \amber{5}\blue{l_1} + \amber{3}\teal{\varepsilon_2} + \amber{2}\teal{\varepsilon_3} + \amber{1}\teal{\varepsilon_4} + \amber{1}\teal{\varepsilon_5} \\ \\
l_6 &= \amber{8}\blue{l_1} + \amber{5}\teal{\varepsilon_2} + \amber{3}\teal{\varepsilon_3} + \amber{2}\teal{\varepsilon_4} + \amber{1}\teal{\varepsilon_5} + \amber{1}\teal{\varepsilon_6} \\ \\
&\;\;\vdots \\ \\
l_n &= \underbrace{\amber{F_n}\blue{l_1} + \amber{F_{n-1}}\teal{\varepsilon_2} + \amber{F_{n-2}}\teal{\varepsilon_3} + \cdots + \amber{F_1}\teal{\varepsilon_n}}_{\text{\amber{Fibonacci}} \text{ coefficients on the } \text{\teal{gaps}}}
\end{aligned}$$

Thus {the vector of sorted lengths}(ref:fib-column) can be written down as:

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

```pygeomatic
gm.clear()
c = gm.load_colors()
unit = gm.scalar(95)
gm.scalar(0.2, out="grid-opacity")

# unit 95 · figure origin at (-1.3, -1.05) so the first-quadrant picture and the
# caption below it straddle (0,0). The caption is width 4.6 → 229 px half-width
# and 3 wrapped lines → 37 px half-height, which keeps its padded top 34 px below
# the x-axis at y = -1.05.
S = 2.5                     # canvas units per coordinate 1.0
ox, oy = -1.3, -1.05        # where the figure's own origin sits
F1, F2 = 1.0, 2.0

with group("simplex-axes"):
    origin = gm.point(ox, oy)
    x_end = gm.point(ox + 3.0, oy)
    y_end = gm.point(ox, oy + 3.0)
    ax = gm.line(origin, x_end)
    ay = gm.line(origin, y_end)
    gm.set_stroke(ax, c.GRAY)
    gm.set_stroke(ay, c.GRAY)
    gm.hide(origin, x_end, y_end)     # added: axis endpoints are scaffolding
    gm.annotate_text_box("x₁ , y₁", ox + 3.15, oy - 0.02, 14)
    gm.annotate_text_box("x₂ , y₂", ox - 0.05, oy + 3.2, 14)

# Item i) is the amber region V₁, so it is now created first; item ii) is the
# blue standard simplex V₂. Coordinates are unchanged.
with group("simplex-v1"):
    fx = gm.point(ox + S / F1, oy)            # (1/F1, 0)
    fy = gm.point(ox, oy + S / F2)            # (0, 1/F2)
    scaled = gm.triangle(origin, fx, fy)
    gm.hide(fx, fy)                           # added: vertices are scaffolding
    gm.annotate_text_box("F₁y₁ + F₂y₂ ≤ 1", ox + 1.65, oy + 0.55, 14, -1)
    d3 = gm.point(ox - 0.9, oy + S / F2)
    d4 = gm.point(ox - 0.9, oy)
    gm.annotate_dim_line(d3, d4, "1/F₂")
    gm.hide(d3, d4)                   # added: dimension anchors are scaffolding
    gm.set_stroke(scaled, c.AMBER)
    v1 = gm.annotate_text_box("V₁", ox + 0.42, oy + 0.3, 15)
    gm.set_fill(v1, c.AMBER)

with group("simplex-v2"):
    sx = gm.point(ox + S, oy)                 # (1, 0)
    sy = gm.point(ox, oy + S)                 # (0, 1)
    std = gm.triangle(origin, sx, sy)
    gm.hide(sx, sy)                           # added: vertices are scaffolding
    gm.annotate_text_box("x₁ + x₂ ≤ 1", ox + 1.55, oy + 1.9, 14, -1)
    d1 = gm.point(ox - 0.25, oy)
    d2 = gm.point(ox - 0.25, oy + S)
    gm.annotate_dim_line(d2, d1, "1")
    gm.hide(d1, d2)                   # added: dimension anchors are scaffolding
    gm.set_stroke(std, c.BLUE)
    v2 = gm.annotate_text_box("V₂", ox + 1.05, oy + 1.35, 15)
    gm.set_fill(v2, c.BLUE)
    gm.annotate_text_box(
        "With F₁ = 1, F₂ = 2 the amber region V₁ is the standard simplex V₂ "
        "with the y₂ axis squeezed by 1/F₂, so V₁ = V₂ / (F₁F₂).",
        0.2, -2, 13, 4.6, -1,
    )
```

Since these lengths are {sampled from the interval}(ref:simplex-axes) $[0,1]$, they have to be less than or equal to one. Applying this condition to each of the largest lengths above we get:

i) $\sum_{i=1}^{n} \amber{F_i} \, y_i \le 1$: the points that satisfy this condition form a {volume $\amber{V_1}$}(ref:simplex-v1). This is the volume of the simplex spanned by the origin and the vectors

$$\underbrace{\frac{1}{\amber{F_i}} \, e_i}_{\text{standard basis vector } e_i \text{ shrunk by } \amber{F_i}}$$

for $i$ from $1$ to $n$.

ii) $\sum_{i=1}^{n} x_i \le 1$: this is the {standard simplex}(ref:simplex-v2) and has a volume $\blue{V_2}$ of $1/n!$. The exact volume is not needed for the proof though.

---

## 4. Final step

```pygeomatic
gm.clear()
c = gm.load_colors()
unit = gm.scalar(62)
gm.scalar(0.2, out="grid-opacity")

# unit 62 · two panels either side of the origin. The caption is width 8.6 →
# 277 px half-width including the 10 px pad, centred at x = -0.3 so it stays
# inside ±320 px; 3 wrapped lines → 37 px half-height, clearing the dim lines.
S = 1.7
F = [1.0, 2.0]
panels = []

with group("jacobian-panels"):
    jhidden = []
    for k, (ox, oy) in enumerate([(-4.0, -1.0), (0.7, -1.0)]):
        o = gm.point(ox, oy, out="o-%d" % k)
        xe = gm.point(ox + 2.5, oy, out="xe-%d" % k)
        ye = gm.point(ox, oy + 2.4, out="ye-%d" % k)
        lx = gm.line(o, xe, out="lx-%d" % k)
        ly = gm.line(o, ye, out="ly-%d" % k)
        gm.set_stroke(lx, c.GRAY)
        gm.set_stroke(ly, c.GRAY)
        vx = gm.point(ox + S / (F[0] if k else 1.0), oy, out="vx-%d" % k)
        vy = gm.point(ox, oy + S / (F[1] if k else 1.0), out="vy-%d" % k)
        tri = gm.triangle(o, vx, vy, out="tri-%d" % k)
        gm.set_stroke(tri, c.AMBER if k else c.BLUE)
        jhidden += [o, xe, ye, vx, vy]
        panels.append((ox, oy, vx, vy))
    gm.hide(*jhidden)                 # added: panel anchors are scaffolding
    gm.annotate_text_box("x-space:  x₁ + x₂ ≤ 1", -3.0, 1.75, 14)
    gm.annotate_text_box("y-space:  F₁y₁ + F₂y₂ ≤ 1", 1.9, 1.75, 14)
    gm.annotate_text_box("x₁", -4.0 + 2.7, -1.05, 13)
    gm.annotate_text_box("x₂", -4.15, -1.0 + 2.6, 13)
    gm.annotate_text_box("y₁", 0.7 + 2.7, -1.05, 13)
    gm.annotate_text_box("y₂", 0.55, -1.0 + 2.6, 13)

with group("jacobian-volumes"):
    vol2 = gm.annotate_text_box("V₂", -4.0 + 0.55, -1.0 + 0.4, 15)
    gm.set_fill(vol2, c.BLUE)
    vol1 = gm.annotate_text_box("V₁", 0.7 + 0.42, -1.0 + 0.28, 15)
    gm.set_fill(vol1, c.AMBER)

with group("jacobian-map"):
    a1 = gm.point(-1.2, 0.55)
    a2 = gm.point(0.35, 0.55)
    arrow = gm.annotate_arrow(a1, a2, 0.05, "yᵢ = xᵢ / Fᵢ")
    gm.hide(a1, a2)                   # added: arrow anchors are scaffolding
    gm.set_stroke(arrow, c.VIOLET)

with group("jacobian-dims"):
    m1 = gm.point(-4.0, -1.3)
    m2 = gm.point(-4.0 + S, -1.3)
    m3 = gm.point(0.7, -1.3)
    m4 = gm.point(0.7 + S / F[0], -1.3)
    n1 = gm.point(0.35, -1.0 + S / F[1])
    n2 = gm.point(0.35, -1.0)
    gm.hide(m1, m2, m3, m4, n1, n2)   # added: dimension anchors are scaffolding
    gm.annotate_dim_line(m1, m2, "1")
    gm.annotate_dim_line(m3, m4, "1/F₁")
    gm.annotate_dim_line(n1, n2, "1/F₂")

with group("jacobian-caption"):
    gm.annotate_text_box(
        "The map yᵢ = xᵢ/Fᵢ only rescales the axes, so its Jacobian is diagonal "
        "with entries 1/Fᵢ, hence V₁/V₂ = |J| = ∏ᵢ 1/Fᵢ.",
        -0.3, -3.4, 13, 8.6, -1,
    )
```

The {probability is given by}(ref:jacobian-panels) $\dfrac{\amber{V_1}}{\blue{V_2}}$. How do we {find $\amber{V_1}$ in terms of $\blue{V_2}$}(ref:jacobian-volumes)? By {defining a map}(ref:jacobian-map) $y_i = x_i / \amber{F_i}$. It is easy to see that the {determinant of the Jacobian}(ref:jacobian-dims) of this map is

$$\begin{aligned}
|J| &= \det \begin{bmatrix}
\frac{1}{\amber{F_1}} &        &        &  \\
       & \frac{1}{\amber{F_2}} &        &  \\
       &        & \ddots &  \\
       &        &        & \frac{1}{\amber{F_n}}
\end{bmatrix} \\ \\
    &= \prod_{i=1}^{n} \frac{1}{\amber{F_i}}
\end{aligned}$$

since the map only rescales each axis. Thus the volume $\amber{V_1} = |J| \, \blue{V_2}$, and this gives us the {probability to be}(ref:jacobian-caption):

$$\frac{\amber{V_1}}{\blue{V_2}} = \prod_{i=1}^{n} \frac{1}{\amber{F_i}}$$
