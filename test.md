{}(r = \scalar 3)

Draw the {circle}(c = \circle p0 r)


Drag to resize the circle: <span class="nova-ui" data-kind="slider" data-node="r" data-initial-value='3.0' data-start='1.0' data-stop='5.0' data-step='0.5' data-label='&quot;radius&quot;' data-show-value='true'></span>


---

{}(side = \mul r 2)

The {square}(sq = \square p0 side 0) has side twice the radius. 

---

### The other five controls

{}(n = \scalar 4)
{}(mode = \text "sum")
{}(pick = \text "left")
{}(name = \text "world")
{}(show = \bool 0)

**numbers** - an exact value rather than a sweep: <span class="nova-ui" data-kind="number" data-node="n" data-initial-value='4.0' data-start='0.0' data-stop='10.0' data-step='0.5' data-label='&quot;n&quot;'></span>

**dropdown** - <span class="nova-ui" data-kind="dropdown" data-node="mode" data-initial-value='&quot;sum&quot;' data-options='[&quot;sum&quot;,&quot;product&quot;,&quot;quotient&quot;]' data-label='&quot;operation&quot;'></span>

**radio** - <span class="nova-ui" data-kind="radio" data-node="pick" data-initial-value='&quot;left&quot;' data-options='[&quot;left&quot;,&quot;right&quot;]' data-label='&quot;side&quot;'></span>

**text** - <span class="nova-ui" data-kind="text" data-node="name" data-initial-value='&quot;world&quot;' data-label='&quot;name&quot;' data-placeholder='&quot;type something&quot;'></span>

**checkbox** - <span class="nova-ui" data-kind="checkbox" data-node="show" data-initial-value='false' data-label='&quot;Show the explanation&quot;'></span>


**Conditional text**


<div class="nova-when" data-when='{&quot;node&quot;:&quot;show&quot;}' style="display:none">

This paragraph is gated by a tick box. Math works here: $e^{i \pi} + 1 = 0$

</div>

<div class="nova-when" data-when='{&quot;op&quot;:&quot;eq&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;mode&quot;},&quot;b&quot;:{&quot;const&quot;:&quot;sum&quot;}}'>

The dropdown is sum

</div>

<div class="nova-when" data-when='{&quot;op&quot;:&quot;and&quot;,&quot;a&quot;:{&quot;op&quot;:&quot;eq&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;pick&quot;},&quot;b&quot;:{&quot;const&quot;:&quot;left&quot;}},&quot;b&quot;:{&quot;op&quot;:&quot;ge&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;n&quot;},&quot;b&quot;:{&quot;const&quot;:5.0}}}' style="display:none">

Two conditions at once: left and n >= 5

</div>

