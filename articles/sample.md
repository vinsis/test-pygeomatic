# Rotating a vector in the plane

A rotation is the one linear map that moves every point along a circle: the
length stays fixed, only the direction turns. The scene below builds that
picture piece by piece, and the formulas underneath it are live, so the numbers,
the paint on the matrix, and the derivation all follow the same $\theta$.

```pygeomatic
gm.scalar(0.3, out="grid-opacity")
gm.scalar(60, out="unit")
c = gm.load_colors()

with group("unit-circle"):
    unit_circle = gm.circle(gm.p0, 2.5)
    gm.set_stroke(unit_circle, c["COLOR-GRAY-DARK"])

with group("base-vector"):
    v_tip = gm.point(2.2658, 1.0565)
    gm.hide(v_tip)                 # ADDED: the tip dot only scaffolds the arrow
    v = gm.annotate_arrow(gm.p0, v_tip)
    gm.set_stroke(v, c.BLUE)

with group("rotate"):
    theta = gm.scalar(0)
    cos_t = gm.cos(theta)
    sin_t = gm.sin(theta)
    x_cos = gm.mul(v_tip.x, cos_t)
    y_sin = gm.mul(v_tip.y, sin_t)
    rot_x = gm.sub(x_cos, y_sin)
    x_sin = gm.mul(v_tip.x, sin_t)
    y_cos = gm.mul(v_tip.y, cos_t)
    rot_y = gm.add(x_sin, y_cos)
    vrot_tip = gm.point(rot_x, rot_y)
    gm.hide(vrot_tip)              # ADDED: same, keep only the arrow visible
    vrot = gm.annotate_arrow(gm.p0, vrot_tip)
    gm.set_stroke(vrot, c.AMBER)

with group("sweep"):
    theta_deg = gm.rad2deg(theta, out="thetadeg")
    arc_end = gm.add(25, theta_deg)
    sweep = gm.arc(gm.p0, 1.1, 25, arc_end)
    gm.set_stroke(sweep, c.AMBER)

with group("labels"):
    lab_x = gm.mul(rot_x, 1.3)
    lab_y = gm.mul(rot_y, 1.3)
    gm.annotate_text_box("v′ = R(θ)·v", lab_x, lab_y, 14)
    gm.annotate_text_box("v", 1.45, 0.05, 14)

with group("readout"):
    gm.annotate_text_box("θ = ${theta} rad = ${thetadeg}°", 0, 3.9, 14)

with group("caption"):
    gm.annotate_text_box("Rotation by θ keeps the length of v fixed and slides its tip along the circle; the rotated vector v′ = R(θ)·v mixes the two components of v with cosθ and sinθ.", 0, -4.0, 13, 7.6, -1)
```

Build the picture one beat at a time:

- Start with the {circle of radius $2.5$}(ref:unit-circle), the track every
  rotated tip is confined to.
- Put a {blue vector $\blue{v}$}(ref:base-vector) on it, with components
  $(x, y) = (2.2658, 1.0565)$.
- Apply the map to get the {amber vector $\amber{v'}$}(ref:rotate), whose tip is
  $(x\cos\theta - y\sin\theta,\; x\sin\theta + y\cos\theta)$.
  - At $\theta = 0$ the two arrows coincide, which is exactly $R(0) = I$.
- Draw the {angle sweep}(ref:sweep) between them, an arc that grows with
  $\theta$.
- Finally {name the two arrows}(ref:labels), add the {live readout}(ref:readout)
  of $\theta$, and a {short caption}(ref:caption).

Now turn the dial. $\theta$ is in radians, so $60° = \pi/3 \approx 1.0472$:

- {Turn by $60°$}(gm.animate(theta, 1.0472)) and watch the tip slide along the
  circle, never off it.
- {Turn by $90°$}(gm.animate(theta, 1.5708)): $\amber{v'}$ is now perpendicular
  to $\blue{v}$, since $\langle v, v'\rangle = 0$.
- {Reset to zero}(theta = gm.scalar(0)) to start over.

## The angle, read straight from the scene

The sweep you see is an integral of a unit rate over the turned interval, and
that same $\theta$ is a fraction of a full turn:

$$
%id:angle
\theta \;=\; \int_{0}^{\tau} d\varphi
\qquad\qquad
\text{turn fraction} \;=\; \frac{\tau}{2\pi}
$$

```pygeomatic
angle = gm.tex("angle")
angle.int.upper.bind(theta, fmt=".3f")
angle.frac.num.bind(theta, fmt=".3f")
```

Both slots read the same store node, so any of the buttons above (or a fresh
{nudge to $2.5$ rad}(theta = gm.scalar(2.5))) rewrites them with no re-render.

## The matrix, painted

$$
%id:R
R(\theta) \;=\;
\begin{pmatrix}
\cos\theta & -\sin\theta \\
\sin\theta & \cos\theta
\end{pmatrix}
$$

```pygeomatic
hrow = gm.scalar(0)
hcol = gm.scalar(0)
paint = gm.scalar(0)
R = gm.tex("R")
R.highlight((gm.rows == hrow).scale(paint), color="amber")
R.highlight((gm.cols == hcol).scale(paint), color="blue")
```

Row $i$ of $R$ says how the $i$-th output component is mixed; column $j$ says
where the $j$-th input component goes.

- {Turn the paint on}(paint = gm.scalar(1)): row $0$ and column $0$ light up,
  and the crossing cell $\cos\theta$ carries both.
- Move the amber band to the {second row}(hrow = gm.scalar(1)): that row,
  $(\sin\theta,\ \cos\theta)$, is the recipe for $y'$.
- Move the blue band to the {second column}(hcol = gm.scalar(1)):
  $(-\sin\theta,\ \cos\theta)$ is the image of the basis vector $e_2$.
- {Wipe the paint}(paint = gm.scalar(0)) when you are done.

## The derivation, line by line

$$
%id:deriv
\begin{aligned}
v' &= R(\theta)\,v \\
x' &= x\cos\theta - y\sin\theta \\
y' &= x\sin\theta + y\cos\theta \\
\lVert v' \rVert^2 &= \underbrace{x^2 + y^2}_{\cos^2\theta + \sin^2\theta = 1}
\end{aligned}
$$

```pygeomatic
k = gm.scalar(0)
why = gm.bool_(False)
deriv = gm.tex("deriv")
deriv.rows().reveal(gm.rows < k)
deriv.underbrace.reveal(why)
```

Strict $<$ means the gate counts lines, so $k = 0$ shows nothing:

- {One line}(k = gm.scalar(1)) · {two lines}(k = gm.scalar(2)) ·
  {three lines}(k = gm.scalar(3)) · {all four}(k = gm.scalar(4)).
- Or {play it through}(gm.animate(k, 4)) and let the reveal run as a sweep.
- {Show why the length holds}(why = gm.bool_(True)): the brace and its label fade
  in over the Pythagorean identity that makes $R(\theta)$ an isometry.
