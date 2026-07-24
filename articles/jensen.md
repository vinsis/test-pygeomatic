# Different facets of Jensen's inequality

Jensen's inequality says that for a convex function $f$ and a random variable $x$,

$$
\mathbb{E}[f(x)] \ge f(\mathbb{E}[x]).
$$

The averaging happens *before* the function, and convexity guarantees the average
lands no lower than the function of the average. Below, a single convex function,
the exponential, exposes several familiar inequalities as different readings of the
same picture: a convex curve sits above its tangent line, and the vertical distance
between them is the Jensen gap.

```pygeomatic
gm.clear()
unit = gm.scalar(95)
c = gm.load_colors()
x = gm.scalar(0)

with group("ex-curve"):
    ex_y = gm.exp(x)
    ex_curve = gm.plot_reactive(x, ex_y)
    gm.set_stroke(ex_curve, c.CYAN)
    lab_ex = gm.annotate_text_box("eˣ", 1.35, 2.72, 15)
    gm.set_fill(lab_ex, c.CYAN)

with group("tangent-up"):
    tan_up = gm.line(gm.point(-1, 0), gm.point(1, 2))          # y = 1 + x
    gm.set_stroke(tan_up, c.BLUE)
    lab_up = gm.annotate_text_box("1 + x", 1.5, 1.55, 13)
    gm.set_fill(lab_up, c.BLUE)

with group("touch-point"):
    touch = gm.point(0, 1)
    lab_touch = gm.annotate_text_box("(0, 1)", 0, 0.55, 12)

with group("enx-curve"):
    negx = gm.mul(x, -1)
    enx_y = gm.exp(negx)
    enx_curve = gm.plot_reactive(x, enx_y)
    gm.set_stroke(enx_curve, c.PINK)
    lab_enx = gm.annotate_text_box("e⁻ˣ", -1.4, 2.72, 15)
    gm.set_fill(lab_enx, c.PINK)

with group("tangent-down"):
    tan_dn = gm.line(gm.point(-1, 2), gm.point(1, 0))          # y = 1 - x
    gm.set_stroke(tan_dn, c.RED)
    lab_dn = gm.annotate_text_box("1 − x", -1.5, 1.55, 13)
    gm.set_fill(lab_dn, c.RED)

with group("jensen-gap"):
    gap_lo = gm.point(1, 2)
    gap_hi = gm.point(1, 2.71828)
    gap = gm.line(gap_lo, gap_hi)
    gm.set_stroke(gap, c.YELLOW)
    gm.hide(gap_lo, gap_hi)                                    # scaffold dots for the gap line
    gm.annotate_leader_line(gm.point(1, 2.36), gm.point(2.55, 2.75), "gap ≥ 0")

with group("summary"):
    gm.annotate_text_box(
        "A convex curve stays above its tangent line. At x = 0: "
        "eˣ ≥ 1 + x and e⁻ˣ ≥ 1 − x, equality only at x = 0. "
        "The vertical gap is the Jensen gap E[f] − f(E[x]) ≥ 0, "
        "obtained from g′(0) and g′(1) of g(p) = E[f] − f(E[x]).",
        0, -2.5, 12, 5.2, -1,
    )
```

## Jensen applied to the exponential

The cleanest place to start is $f(x) = e^x$, which is convex everywhere. Plotting
the {exponential $\cyan{e^x}$}(ref:ex-curve) gives a curve that bends upward, so
its average over any spread of inputs must exceed the exponential of the mean. That
is Jensen's inequality with the concrete function in hand: $\mathbb{E}[e^x] \ge
e^{\mathbb{E}[x]}$.

## The tangent below the curve

Convexity has a purely local shadow. The tangent to $e^x$ at the origin is the
straight line $y = 1 + x$, and because the curve is convex it never dips below that
line. Drawing the {tangent $\blue{1+x}$}(ref:tangent-up) makes the classic bound
visible: $1 + x \le e^x$ for every $x$. The curve and its tangent {meet at
$(0,1)$}(ref:touch-point), the single point where the two sides are equal.

## One derivation, two inequalities

Where does the mirror-image bound $1 - x \le e^{-x}$ come from? Fix two outcomes,
$a$ and $0$, and let $x = pa + (1-p)\cdot 0$ interpolate between them as $p$ runs
from $0$ to $1$. Define the Jensen gap as a function of the mixing weight,

$$
g(p) = \mathbb{E}[f] - f(\mathbb{E}[x]).
$$

Differentiating and reading off the endpoints, $g'(0)$ produces $1 + x \le e^x$
while $g'(1)$ produces its reflection. Plotting {the reflected curve
$\pink{e^{-x}}$}(ref:enx-curve) and {its tangent $\red{1-x}$}(ref:tangent-down)
shows the second inequality as the same geometry flipped about the vertical axis:
$1 - x \le e^{-x}$, again with equality only at the origin.

The distance between tangent and curve is not just decoration. Measuring the
{vertical gap}(ref:jensen-gap) at a chosen $x$ is exactly $\mathbb{E}[f] -
f(\mathbb{E}[x])$, so the height of that yellow segment is the Jensen gap made
literal, and convexity keeps it non-negative.

## Where $1 - x \le e^{-x}$ earns its keep

The reflected bound is the workhorse of probability. Because $1 - x \le e^{-x}$
turns a subtraction into an exponential, it is the standard trick for bounding
moment generating functions and tail probabilities: replacing $(1-x)$ factors with
$e^{-x}$ makes products collapse into a single exponent, which is precisely how
Chernoff-style bounds and union-bound arguments are tightened. The {full
picture}(ref:summary) collects both readings, the tangent bound and the Jensen gap,
into one frame.

```pygeomatic
gm.clear()
unit = gm.scalar(90)
c = gm.load_colors()
x = gm.scalar(0)

with group("log-curve"):
    one_plus = gm.add(x, 1)
    log_y = gm.log(one_plus)
    log_curve = gm.plot_reactive(x, log_y)                    # y = ln(1+x)
    gm.set_stroke(log_curve, c.GREEN)
    lab_log = gm.annotate_text_box("ln(1 + x)", 2.45, 1.05, 14)
    gm.set_fill(lab_log, c.GREEN)

with group("line-yx"):
    line_yx = gm.line(gm.point(-0.85, -0.85), gm.point(2, 2))  # y = x
    gm.set_stroke(line_yx, c.AMBER)
    lab_line = gm.annotate_text_box("y = x", 1.55, 2.25, 14)
    gm.set_fill(lab_line, c.AMBER)

with group("origin-point"):
    origin_dot = gm.point(0, 0)
    lab_orig = gm.annotate_text_box("(0, 0)", 0.45, -0.35, 12)

with group("gap-b"):
    g_lo = gm.point(1, 0.6931)
    g_hi = gm.point(1, 1)
    gapB = gm.line(g_lo, g_hi)
    gm.set_stroke(gapB, c.YELLOW)
    gm.hide(g_lo, g_hi)                                        # scaffold dots for the gap line
    gm.annotate_leader_line(gm.point(1, 0.85), gm.point(2.4, 0.15), "gap ≥ 0")

with group("kld-summary"):
    gm.annotate_text_box(
        "ln(1 + x) ≤ x, equality at x = 0. Writing xᵢ = pᵢ/qᵢ − 1 gives "
        "Σ qᵢ ln(pᵢ/qᵢ) ≤ Σ(pᵢ − qᵢ) = 0, so the KL divergence "
        "D(p ‖ q) = Σ pᵢ ln(pᵢ/qᵢ) ≥ 0.",
        0, -2.9, 12, 5.4, -1,
    )
```

## The logarithmic form and positive KL divergence

Taking logarithms of the tangent bound gives a third face of the same fact:
$\log(1+x) \le x$. Here concavity of $\log$ puts the curve *below* its tangent, so
plotting {the logarithm $\green{\ln(1+x)}$}(ref:log-curve) against {the line
$\amber{y=x}$}(ref:line-yx) shows the log trailing beneath the diagonal
everywhere, touching only {at the origin}(ref:origin-point) where both equal zero.
The {vertical gap}(ref:gap-b) between line and curve is again non-negative.

This one inequality proves that relative entropy can never be negative. Writing
$x_i = p_i/q_i - 1$ and summing with weights $q_i$ turns $\log(1+x) \le x$ into

$$
\sum_i q_i \log\frac{p_i}{q_i} \le \sum_i (p_i - q_i) = 0,
$$

and flipping the sign gives $D(p \,\|\, q) = \sum_i p_i \log\frac{p_i}{q_i} \ge 0$.
The {full argument}(ref:kld-summary) shows how the tangent bound, read through the
logarithm, forces the KL divergence to stay non-negative.
