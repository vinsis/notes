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

```pygeomatic
with group("dag1"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(55, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    subs = "₀₁₂₃₄₅₆₇₈₉"
    r = 0.6
    xs = [-3.2, 0.0, 3.2]
    taus = [0.9, 1.0, 0.3]
    alias = [2, None, 2]          # 1-based alias bin per source bin
    alias_w = [0.1, None, 0.7]    # 1 - tau_i
    centers = [(x, 0.0) for x in xs]
    for i, (x, y) in enumerate(centers):
        p = gm.point(x, y, out=f"e1-center-{i + 1}")
        gm.hide(p)
        circ = gm.circle(p, r, out=f"e1-node-{i + 1}")
        if taus[i] == 1.0:
            gm.set_fill(circ, c.GREEN)
            gm.set_stroke(circ, c.EMERALD)
        else:
            gm.set_fill(circ, c["COLOR-GRAY-DARK"])
            gm.set_stroke(circ, c.WHITE)
    for i, (x, y) in enumerate(centers):
        a = gm.point(x - 0.3, y + 0.52, out=f"e1-loopa-{i + 1}")
        b = gm.point(x + 0.3, y + 0.52, out=f"e1-loopb-{i + 1}")
        ctl = gm.point(x, y + 1.75, out=f"e1-loopc-{i + 1}")
        gm.hide(a, b, ctl)
        loop = gm.annotate_curved_arrow(a, b, ctl, 0.0, out=f"e1-loop-{i + 1}")
        gm.set_stroke(loop, c.GREEN)
    for i in range(3):
        if alias[i] is None:
            continue
        j = alias[i] - 1
        x1, y1 = centers[i]
        x2, y2 = centers[j]
        dx, dy = x2 - x1, y2 - y1
        d = (dx * dx + dy * dy) ** 0.5
        ux, uy = dx / d, dy / d
        pa = gm.point(x1 + r * ux, y1 + r * uy, out=f"e1-edgea-{i + 1}")
        pb = gm.point(x2 - r * ux, y2 - r * uy, out=f"e1-edgeb-{i + 1}")
        gm.hide(pa, pb)
        arr = gm.annotate_arrow(pa, pb, 0.06, out=f"e1-alias-{i + 1}")
        gm.set_stroke(arr, c.AMBER)
    for i, (x, y) in enumerate(centers):
        idx = gm.annotate_text_box(str(i + 1), x, y, 20, out=f"e1-idx-{i + 1}")
        gm.set_fill(idx, c.WHITE)
        tl = gm.annotate_text_box(
            f"τ{subs[i + 1]} = {taus[i]}", x, y + 1.98, 14, out=f"e1-tau-{i + 1}"
        )
        gm.set_fill(tl, c.GREEN)
    for i in range(3):
        if alias[i] is None:
            continue
        mx = (centers[i][0] + centers[alias[i] - 1][0]) / 2
        wl = gm.annotate_text_box(
            f"1−τ{subs[i + 1]} = {alias_w[i]}  → bin {alias[i]}",
            mx, -0.75, 13, out=f"e1-w-{i + 1}",
        )
        gm.set_fill(wl, c.AMBER)
    abs_lab = gm.annotate_text_box("absorbing (τ=1)", 0.0, -1.05, 12, out="e1-abs")
    gm.set_fill(abs_lab, c.EMERALD)
    gm.annotate_text_box(
        "Alias DAG. Each bin keeps itself with probability τᵢ "
        "(green self-loop) and jumps to its alias bin with probability 1−τᵢ "
        "(amber edge). Bin 2 (τ=1) is absorbing.",
        0.0, -2.4, 13, 9.4, -1, out="e1-summary",
    )
```

This table can also be seen as a {DAG with three nodes}(ref:dag1) if we ignore the self-loops. 

---

## Table → Row stochastic matrix
Note that this table can be seen as a row stochastic matrix where each row has at most two non-zero entries. Row $i$ gets $\tau_i$ on the diagonal
and $1-\tau_i$ in the alias column $j$, and zeros everywhere else.

```pygeomatic
with group("matrix"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(60, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    s = 1.3
    P = [[0.9, 0.1, 0.0], [0.0, 1.0, 0.0], [0.0, 0.7, 0.3]]
    for i in range(3):
        cy = (1 - i) * s
        for j in range(3):
            cx = (j - 1) * s
            bl = gm.point(cx - s / 2, cy - s / 2, out=f"m-bl-{i + 1}-{j + 1}")
            gm.hide(bl)
            cell = gm.square(bl, s, 0, out=f"m-cell-{i + 1}-{j + 1}")
            v = P[i][j]
            if v == 0:
                gm.set_fill(cell, c["COLOR-GRAY-DARK"])
                gm.set_stroke(cell, c["COLOR-GRAY-MID"])
            elif i == j:
                gm.set_fill(cell, c.EMERALD if v == 1.0 else c.GREEN)
                gm.set_stroke(cell, c.WHITE)
            else:
                gm.set_fill(cell, c.AMBER)
                gm.set_stroke(cell, c.WHITE)
    for i in range(3):
        cy = (1 - i) * s
        for j in range(3):
            v = P[i][j]
            if v != 0:
                lab = gm.annotate_text_box(
                    str(v), (j - 1) * s, cy, 15, out=f"m-v-{i + 1}-{j + 1}"
                )
                gm.set_fill(lab, c.WHITE)
    for j in range(3):
        gm.annotate_text_box(
            f"col {j + 1}", (j - 1) * s, 1.3 + s / 2 + 0.38, 11, out=f"m-col-{j + 1}"
        )
    for i in range(3):
        gm.annotate_text_box(
            f"row {i + 1}", -1.3 - s / 2 - 0.6, (1 - i) * s, 11, out=f"m-row-{i + 1}"
        )
    title = gm.annotate_text_box("P   (Example 1)", 0.0, 1.3 + s / 2 + 0.98, 15, out="m-title")
    gm.set_fill(title, c.WHITE)
    gm.annotate_text_box(
        "Every row has exactly two nonzeros: τᵢ on the diagonal (green, "
        "= the eigenvalues of the triangular-ish P) and 1−τᵢ at the alias "
        "column (amber). Row 2 is absorbing (τ=1). Blank cells are 0.",
        0.0, -2.95, 13, 9.4, -1, out="m-summary",
    )
```

Here is {$P$ as a heatmap}(ref:matrix) for the above table.

$$
%id:P
P=\begin{bmatrix} 0.9 & 0.1 & 0 \\ 0 & 1 & 0 \\ 0 & 0.7 & 0.3 \end{bmatrix}
$$

```pygeomatic
Ptex = gm.tex("P")
Ptex.highlight(Ptex.diag(), color="GREEN")
Ptex.highlight(Ptex[0, 1] | Ptex[2, 1], color="AMBER")
```

---

## Sampling as a Markov step

Let $v = \begin{bmatrix} \teal{\tfrac13} & \teal{\tfrac13} & \teal{\tfrac13} \end{bmatrix}$ be the
uniform row vector. Then $vP$ is the target.

```pygeomatic
with group("vp"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(60, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    scale = 3.4
    y0 = -1.4
    w = 0.55
    sp = 0.85
    uniform = ["1/3", "1/3", "1/3"]
    uniform_h = [1 / 3, 1 / 3, 1 / 3]
    target = [0.3, 0.6, 0.1]
    lc, rc = -3.3, 3.3
    lx = [lc - sp, lc, lc + sp]
    rx = [rc - sp, rc, rc + sp]
    for i in range(3):
        h = uniform_h[i] * scale
        bl = gm.point(lx[i] - w / 2, y0, out=f"vp-lbl-{i + 1}")
        gm.hide(bl)
        bar = gm.rectangle(bl, w, h, 0, out=f"vp-lbar-{i + 1}")
        gm.set_fill(bar, c.GRAY)
        gm.set_stroke(bar, c.WHITE)
    for i in range(3):
        h = target[i] * scale
        bl = gm.point(rx[i] - w / 2, y0, out=f"vp-rbl-{i + 1}")
        gm.hide(bl)
        bar = gm.rectangle(bl, w, h, 0, out=f"vp-rbar-{i + 1}")
        gm.set_fill(bar, c.BLUE)
        gm.set_stroke(bar, c.WHITE)
    ap = gm.point(-1.5, -0.8, out="vp-ap")
    bp = gm.point(1.5, -0.8, out="vp-bp")
    gm.hide(ap, bp)
    arr = gm.annotate_arrow(ap, bp, 0.02, out="vp-arrow")
    gm.set_stroke(arr, c.WHITE)
    for i in range(3):
        h = uniform_h[i] * scale
        gm.annotate_text_box(uniform[i], lx[i], y0 + h + 0.28, 12, out=f"vp-lv-{i + 1}")
        gm.annotate_text_box(str(i + 1), lx[i], y0 - 0.32, 12, out=f"vp-lidx-{i + 1}")
    for i in range(3):
        h = target[i] * scale
        gm.annotate_text_box(str(target[i]), rx[i], y0 + h + 0.28, 12, out=f"vp-rv-{i + 1}")
        gm.annotate_text_box(str(i + 1), rx[i], y0 - 0.32, 12, out=f"vp-ridx-{i + 1}")
    plab = gm.annotate_text_box("P", 0.0, -0.6, 16, out="vp-plab")
    gm.set_fill(plab, c.AMBER)
    lg = gm.annotate_text_box("v = [1/3, 1/3, 1/3]", lc, y0 - 0.95, 13, out="vp-lg")
    gm.set_fill(lg, c.GRAY)
    rg = gm.annotate_text_box("vP = [0.3, 0.6, 0.1]", rc, y0 - 0.95, 13, out="vp-rg")
    gm.set_fill(rg, c.BLUE)
    gm.annotate_text_box(
        "Multiplying the uniform row vector v by P moves mass along the alias edges "
        "and recovers the target distribution vP.",
        0.0, -3.8, 13, 10.6, -1, out="vp-summary",
    )
```

Watch how the uniform bars, pushed through $P$, {recover the
target}(ref:vp) $vP = \text{target}$ on the right. 

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

```pygeomatic
with group("dag2"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(50, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    r = 0.55
    pos = {
        1: (-2.6, 2.3),
        2: (-2.6, 0.0),
        3: (-2.6, -2.3),
        4: (2.6, 1.4),
        5: (2.6, -1.4),
    }
    taus = {1: 0.2, 2: 0.4, 3: 0.6, 4: 1.0, 5: 1.0}
    alias = {1: 4, 2: 5, 3: 5, 4: None, 5: None}
    alias_w = {1: 0.8, 2: 0.6, 3: 0.4, 4: None, 5: None}
    for k in range(1, 6):
        x, y = pos[k]
        p = gm.point(x, y, out=f"e2-center-{k}")
        gm.hide(p)
        circ = gm.circle(p, r, out=f"e2-node-{k}")
        if taus[k] == 1.0:
            gm.set_fill(circ, c.GREEN)
            gm.set_stroke(circ, c.EMERALD)
        else:
            gm.set_fill(circ, c["COLOR-GRAY-DARK"])
            gm.set_stroke(circ, c.WHITE)
    for k in range(1, 6):
        x, y = pos[k]
        left = x < 0
        sx = -1 if left else 1
        a = gm.point(x + sx * 0.5, y + 0.26, out=f"e2-loopa-{k}")
        b = gm.point(x + sx * 0.5, y - 0.26, out=f"e2-loopb-{k}")
        ctl = gm.point(x + sx * 1.5, y, out=f"e2-loopc-{k}")
        gm.hide(a, b, ctl)
        loop = gm.annotate_curved_arrow(a, b, ctl, 0.0, out=f"e2-loop-{k}")
        gm.set_stroke(loop, c.GREEN)
    for k in range(1, 6):
        if alias[k] is None:
            continue
        x1, y1 = pos[k]
        x2, y2 = pos[alias[k]]
        dx, dy = x2 - x1, y2 - y1
        d = (dx * dx + dy * dy) ** 0.5
        ux, uy = dx / d, dy / d
        pa = gm.point(x1 + r * ux, y1 + r * uy, out=f"e2-edgea-{k}")
        pb = gm.point(x2 - r * ux, y2 - r * uy, out=f"e2-edgeb-{k}")
        gm.hide(pa, pb)
        arr = gm.annotate_arrow(pa, pb, 0.05, out=f"e2-alias-{k}")
        gm.set_stroke(arr, c.AMBER)
    for k in range(1, 6):
        x, y = pos[k]
        idx = gm.annotate_text_box(str(k), x, y, 19, out=f"e2-idx-{k}")
        gm.set_fill(idx, c.WHITE)
        sx = -1 if x < 0 else 1
        tl = gm.annotate_text_box(
            f"τ={taus[k]}", x + sx * 1.85, y, 13, out=f"e2-tau-{k}"
        )
        gm.set_fill(tl, c.GREEN)
    edge_label_pos = {1: (0.0, 2.05), 2: (0.15, -0.5), 3: (0.15, -2.05)}
    for k in range(1, 6):
        if alias[k] is None:
            continue
        lx, ly = edge_label_pos[k]
        wl = gm.annotate_text_box(
            f"1−τ = {alias_w[k]}  → bin {alias[k]}",
            lx, ly, 10, -1, out=f"e2-w-{k}",
        )
        gm.set_fill(wl, c.AMBER)
    gm.annotate_text_box(
        "Example 2 alias DAG. Bins 4 and 5 have τ=1, so they are absorbing (self-loop only, "
        "no alias edge).",
        0.0, -4, 13, 11.0, -1, out="e2-summary",
    )
```

The DAG corresponding to the alias table {looks like this}(ref:dag2). Note that it has two sinks (absorbing states). The corresponding stochastic matrix is:

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

```pygeomatic
P2tex = gm.tex("P2")
P2tex.highlight(P2tex[3, 3] | P2tex[4, 4], color="GREEN")
```

with eigenvalues $\{0.2, 0.4, 0.6, \green{1}, \green{1}\}$. The eigenvalue $\lambda = \green{1}$ has multiplicity two, which corresponds to the two bins (or the two connected components).

---

## Insight 3: trace sets the branch probability
How O(1) sampling works: to sample, pick a bin $i$ uniformly, draw
$U \sim \text{Uniform}[0,1)$, and keep $i$ if $U < \tau_i$, otherwise jump to its
alias.

```pygeomatic
with group("sampling"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(68, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    taus = [0.9, 1.0, 0.3]
    H = 3.0
    y0 = -1.5
    w = 1.9
    xs = [-2.0, 0.0, 2.0]
    for i, x in enumerate(xs):
        keep = taus[i] * H
        jump = (1 - taus[i]) * H
        kbl = gm.point(x - w / 2, y0, out=f"s-kbl-{i + 1}")
        gm.hide(kbl)
        kbar = gm.rectangle(kbl, w, keep, 0, out=f"s-keep-{i + 1}")
        gm.set_fill(kbar, c.GREEN)
        gm.set_stroke(kbar, c.WHITE)
        if jump > 1e-9:
            jbl = gm.point(x - w / 2, y0 + keep, out=f"s-jbl-{i + 1}")
            gm.hide(jbl)
            jbar = gm.rectangle(jbl, w, jump, 0, out=f"s-jump-{i + 1}")
            gm.set_fill(jbar, c.AMBER)
            gm.set_stroke(jbar, c.WHITE)
    for i, x in enumerate(xs):
        keep = taus[i] * H
        jump = (1 - taus[i]) * H
        gm.annotate_text_box(f"bin {i + 1}", x, y0 - 0.35, 12, out=f"s-bin-{i + 1}")
        kl = gm.annotate_text_box(
            f"keep τ={taus[i]}", x, y0 + keep / 2, 11, out=f"s-kl-{i + 1}"
        )
        gm.set_fill(kl, c.WHITE)
        if jump > 1e-9:
            jl = gm.annotate_text_box(
                f"jump {round(1 - taus[i], 1)}", x, y0 + keep + jump / 2, 11, out=f"s-jl-{i + 1}"
            )
            gm.set_fill(jl, c.WHITE)
    title = gm.annotate_text_box(
        "P(keep) = Tr(P)/n = 2.2 / 3 ≈ 0.73", 0.0, 2.15, 15, out="s-title"
    )
    gm.set_fill(title, c.GREEN)
    gm.annotate_text_box(
        "O(1) sampling: pick a bin i uniformly, draw U between 0 and 1. Keep i "
        "if U<τᵢ (green), else jump to the alias j (amber). The overall "
        "keep-probability is the green fraction Tr(P)/n.",
        0.0, -2.6, 13, 9.6, -1, out="s-summary",
    )
```

The {keep-jump split}(ref:sampling) stacks each bin's green keep-fraction
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