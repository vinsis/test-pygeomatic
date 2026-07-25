# Thales' theorem

Pick any point $P$ on a circle and join it to the two ends of a diameter $AB$.
No matter where $P$ sits, the angle $\green{\angle APB}$ is exactly $90^\circ$.
Here is the picture, built one click at a time.

```pygeomatic
unit = gm.scalar(55)
c = gm.load_colors()

# the driving angle: P sits at (3 cos theta, 3 sin theta)
theta = gm.scalar(58)
rad = gm.deg2rad(theta)
cs = gm.cos(rad)
sn = gm.sin(rad)
px = gm.mul(cs, 3)
py = gm.mul(sn, 3)

# every piece is created up front and hidden, so each click reveals one beat
thales = gm.circle(gm.p0, 3)
gm.hide(thales)
left = gm.point(-3, 0)
right = gm.point(3, 0)
apex = gm.point(px, py)
gm.hide(left)
gm.hide(right)
gm.hide(apex)
diameter = gm.line(left, right)
leg_left = gm.line(apex, left)
leg_right = gm.line(apex, right)
gm.hide(diameter)
gm.hide(leg_left)
gm.hide(leg_right)

with group("circle"):
    gm.set_stroke(thales, c.GRAY)
    gm.show(thales)

with group("diameter"):
    gm.set_stroke(diameter, c.WHITE)
    gm.show(left)
    gm.show(right)
    gm.show(diameter)

with group("legs"):
    gm.set_stroke(leg_left, c.BLUE)
    gm.set_stroke(leg_right, c.AMBER)
    gm.set_fill(apex, c.EMERALD)
    gm.show(apex)
    gm.show(leg_left)
    gm.show(leg_right)

with group("trail"):
    gm.trail(apex)
```

- Start with a {circle of radius 3}(ref:circle), centered at the origin.
- Cut it with a {full diameter}(ref:diameter) from $A = (-3, 0)$ to $B = (3, 0)$.
- Put $\green{P}$ on the rim and drop {two legs}(ref:legs), $\blue{PA}$ and $\amber{PB}$.
- Ask $P$ to {leave a trail}(ref:trail) as it moves.

```pygeomatic
lx = gm.mul(cs, 3.7)
ly = gm.mul(sn, 3.7)

with group("labels"):
    apex_label = gm.annotate_text_box("P", lx, ly)
    gm.set_fill(apex_label, c.EMERALD)
    gm.annotate_text_box("A", -3.4, -0.45)
    gm.annotate_text_box("B", 3.4, -0.45)

with group("right-angle"):
    gm.annotate_angle_mark(leg_left, leg_right)

with group("readouts"):
    apex_angle = gm.angle(left, apex, right)
    theta_readout = gm.annotate_text_box("θ = ${theta}°", -3.5, 4.4)
    gm.set_fill(theta_readout, c.VIOLET)
    angle_readout = gm.annotate_text_box("∠APB = ${apex-angle}°", 3.3, 4.4)
    gm.set_fill(angle_readout, c.EMERALD)

with group("caption"):
    gm.annotate_text_box("Thales: any point P on the circle sees the diameter AB at a right angle. Drive θ and ∠APB stays 90°.", 0, -4.6, 14, 8, -1)
```

- {Name the three vertices}(ref:labels) $A$, $B$ and $P$.
- Mark the corner at $P$ with a {square angle mark}(ref:right-angle).
- Print {two live readouts}(ref:readouts): the driver $\violet{\theta}$ and the measured $\green{\angle APB}$.
- And a {one-line caption}(ref:caption) under the figure.

Now drive it and watch the right-hand readout refuse to move:

- {Sweep $P$ around}(gm.animate(theta, 300)) the whole circle.
- Or jump: {set $\theta$ to 20}(theta = gm.scalar(20)).
- {Zoom out a little}(unit = gm.scalar(40)) to see the trail close up.

## Why it is always $90^\circ$

$OA$, $OP$ and $OB$ are all radii, so $\triangle OPA$ and $\triangle OPB$ are
isosceles, and the base angle at $P$ is half of the apex angle at $O$:

$$
%id:half
\angle OPA \;=\; \frac{\theta}{2}
$$

```pygeomatic
half = gm.tex("half")
half.frac.num.bind(theta, fmt=".0f")
```

The numerator is a live value slot: it shows whatever $\violet{\theta}$
currently is, so {set $\theta$ to 100}(theta = gm.scalar(100)) rewrites the
fraction and the drawing together.

The two legs, written as column vectors out of $P$:

$$
%id:legs
\begin{pmatrix} -3 - 3\cos\theta & 3 - 3\cos\theta \\ -3\sin\theta & -3\sin\theta \end{pmatrix}
$$

```pygeomatic
paint = gm.scalar(0)
leg = gm.scalar(0)
legs = gm.tex("legs")
legs.highlight((gm.cols == leg).scale(paint), color="BLUE")
```

- {Paint the first column}(paint = gm.scalar(1)): that is $\blue{\vec{PA}}$.
- {Move the paint over}(leg = gm.scalar(1)) to column two, $\amber{\vec{PB}}$.
- Their dot product is $(9\cos^2\theta - 9) + 9\sin^2\theta = 0$, so the columns are orthogonal.

The whole argument, in four lines:

$$
%id:proof
\begin{aligned}
|OA| &= |OP| = |OB| = r \\
\angle OPA &= \frac{\theta}{2} \\
\angle OPB &= \frac{180^\circ - \theta}{2} \\
\angle APB &= \underbrace{\frac{\theta}{2} + \frac{180^\circ - \theta}{2}}_{\theta \text{ cancels}} \;=\; 90^\circ
\end{aligned}
$$

```pygeomatic
k = gm.scalar(0)
cancel = gm.bool_(False)
proof = gm.tex("proof")
proof.rows().reveal(gm.rows < k)
proof.underbrace.reveal(cancel)
```

- {Radii first}(k = gm.scalar(1)), then the {base angle at $A$}(k = gm.scalar(2)),
  then the {base angle at $B$}(k = gm.scalar(3)), then the {closing sum}(k = gm.scalar(4)).
- Or {play the whole proof}(gm.animate(k, 4)) in one go.
- {Reveal the punchline}(cancel = gm.bool_(True)): $\theta$ cancels, so the answer
  never depends on where $P$ sits.
