```pygeomatic
r = gm.ui.slider(1, 5, step=0.5, value=3, label="radius")
with group("draw"):
  c = gm.circle(gm.p0, r)
```

Draw the {circle}(ref:draw)

```pygeomatic
gm.md(f"Drag to resize the circle: {r}")
```

---

```pygeomatic
side = r * 2
with group("square"):
  sq = gm.square(gm.p0, side, 0)
```

The {square}(ref:square) has side twice the radius. 

---

### The other five controls

```pygeomatic
n = gm.ui.number(0, 10, value=4, step=0.5, label="n")
mode = gm.ui.dropdown(["sum", "product", "quotient"], label="operation")
pick = gm.ui.radio(["left", "right"], label="side")
name = gm.ui.text("world", label="name", placeholder="type something")
show = gm.ui.checkbox(False, label="Show the explanation")

gm.md(f"**numbers** - an exact value rather than a sweep: {n}")
gm.md(f"**dropdown** - {mode}")
gm.md(f"**radio** - {pick}")
gm.md(f"**text** - {name}")
gm.md(f"**checkbox** - {show}")
```

**Conditional text**

```pygeomatic
with gm.when(show):
  gm.md('''
    This paragraph is gated by a tick box. Math works here: $e^{i \pi} + 1 = 0$
  ''')

with gm.when(gm.cond.eq(mode, "sum")):
  gm.md("The dropdown is sum")

with gm.when(gm.cond.all_(gm.cond.eq(pick, "left"), gm.cond.ge(n, 5))):
  gm.md("Two conditions at once: left and n >= 5")
```