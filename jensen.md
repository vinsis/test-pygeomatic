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

{}(\clear)
{}(unit = \scalar 95)
{}(\load-colors)
{}(x = \scalar 0)

## Jensen applied to the exponential

The cleanest place to start is $f(x) = e^x$, which is convex everywhere. Plotting
the {}(ex-y = \exp x) {}(ex-curve = \plot x ex-y) {}(\set-stroke ex-curve COLOR-CYAN) {}(text-0 = \text "eˣ") {}(lab-ex = \annotate-text-box text-0 1.35 2.72 15) {exponential $\cyan{e^x}$}(\set-fill lab-ex COLOR-CYAN) gives a curve that bends upward, so
its average over any spread of inputs must exceed the exponential of the mean. That
is Jensen's inequality with the concrete function in hand: $\mathbb{E}[e^x] \ge
e^{\mathbb{E}[x]}$.

## The tangent below the curve

Convexity has a purely local shadow. The tangent to $e^x$ at the origin is the
straight line $y = 1 + x$, and because the curve is convex it never dips below that
line. Drawing the {}(p-0 = \point -1 0) {}(p-1 = \point 1 2) {}(tan-up = \line p-0 p-1) {}(\set-stroke tan-up COLOR-BLUE) {}(text-1 = \text "1 + x") {}(lab-up = \annotate-text-box text-1 1.5 1.55 13) {tangent $\blue{1+x}$}(\set-fill lab-up COLOR-BLUE) makes the classic bound
visible: $1 + x \le e^x$ for every $x$. The curve and its tangent {}(touch = \point 0 1) {}(text-2 = \text "(0, 1)") {meet at
$(0,1)$}(lab-touch = \annotate-text-box text-2 0 0.55 12), the single point where the two sides are equal.

## One derivation, two inequalities

Where does the mirror-image bound $1 - x \le e^{-x}$ come from? Fix two outcomes,
$a$ and $0$, and let $x = pa + (1-p)\cdot 0$ interpolate between them as $p$ runs
from $0$ to $1$. Define the Jensen gap as a function of the mixing weight,

$$
g(p) = \mathbb{E}[f] - f(\mathbb{E}[x]).
$$

Differentiating and reading off the endpoints, $g'(0)$ produces $1 + x \le e^x$
while $g'(1)$ produces its reflection. Plotting {}(negx = \mul x -1) {}(enx-y = \exp negx) {}(enx-curve = \plot x enx-y) {}(\set-stroke enx-curve COLOR-PINK) {}(text-3 = \text "e⁻ˣ") {}(lab-enx = \annotate-text-box text-3 -1.4 2.72 15) {the reflected curve
$\pink{e^{-x}}$}(\set-fill lab-enx COLOR-PINK) and {}(p-2 = \point -1 2) {}(p-3 = \point 1 0) {}(tan-dn = \line p-2 p-3) {}(\set-stroke tan-dn COLOR-RED) {}(text-4 = \text "1 − x") {}(lab-dn = \annotate-text-box text-4 -1.5 1.55 13) {its tangent $\red{1-x}$}(\set-fill lab-dn COLOR-RED)
shows the second inequality as the same geometry flipped about the vertical axis:
$1 - x \le e^{-x}$, again with equality only at the origin.

The distance between tangent and curve is not just decoration. Measuring the
{}(gap-lo = \point 1 2) {}(gap-hi = \point 1 2.71828) {}(gap = \line gap-lo gap-hi) {}(\set-stroke gap COLOR-YELLOW) {}(\hide gap-lo gap-hi) {}(p-4 = \point 1 2.36) {}(p-5 = \point 2.55 2.75) {}(text-5 = \text "gap ≥ 0") {vertical gap}(lead-0 = \annotate-leader-line p-4 p-5 text-5) at a chosen $x$ is exactly $\mathbb{E}[f] -
f(\mathbb{E}[x])$, so the height of that yellow segment is the Jensen gap made
literal, and convexity keeps it non-negative.

## Where $1 - x \le e^{-x}$ earns its keep

The reflected bound is the workhorse of probability. Because $1 - x \le e^{-x}$
turns a subtraction into an exponential, it is the standard trick for bounding
moment generating functions and tail probabilities: replacing $(1-x)$ factors with
$e^{-x}$ makes products collapse into a single exponent, which is precisely how
Chernoff-style bounds and union-bound arguments are tightened. The {}(text-6 = \text "A convex curve stays above its tangent line. At x = 0: eˣ ≥ 1 + x and e⁻ˣ ≥ 1 − x, equality only at x = 0. The vertical gap is the Jensen gap E[f] − f(E[x]) ≥ 0, obtained from g′(0) and g′(1) of g(p) = E[f] − f(E[x]).") {full
picture}(tbox-0 = \annotate-text-box text-6 0 -2.5 12 5.2 -1) collects both readings, the tangent bound and the Jensen gap,
into one frame.

{}(\clear)
{}(unit = \scalar 90)
{}(\load-colors)
{}(x = \scalar 0)

## The logarithmic form and positive KL divergence

Taking logarithms of the tangent bound gives a third face of the same fact:
$\log(1+x) \le x$. Here concavity of $\log$ puts the curve *below* its tangent, so
plotting {}(one-plus = \add x 1) {}(log-y = \log one-plus) {}(log-curve = \plot x log-y) {}(\set-stroke log-curve COLOR-GREEN) {}(text-7 = \text "ln(1 + x)") {}(lab-log = \annotate-text-box text-7 2.45 1.05 14) {the logarithm $\green{\ln(1+x)}$}(\set-fill lab-log COLOR-GREEN) against {}(p-6 = \point -0.85 -0.85) {}(p-7 = \point 2 2) {}(line-yx = \line p-6 p-7) {}(\set-stroke line-yx COLOR-AMBER) {}(text-8 = \text "y = x") {}(lab-line = \annotate-text-box text-8 1.55 2.25 14) {the line
$\amber{y=x}$}(\set-fill lab-line COLOR-AMBER) shows the log trailing beneath the diagonal
everywhere, touching only {}(origin-dot = \point 0 0) {}(text-9 = \text "(0, 0)") {at the origin}(lab-orig = \annotate-text-box text-9 0.45 -0.35 12) where both equal zero.
The {}(g-lo = \point 1 0.6931) {}(g-hi = \point 1 1) {}(gapB = \line g-lo g-hi) {}(\set-stroke gapB COLOR-YELLOW) {}(\hide g-lo g-hi) {}(p-8 = \point 1 0.85) {}(p-9 = \point 2.4 0.15) {}(text-10 = \text "gap ≥ 0") {vertical gap}(lead-1 = \annotate-leader-line p-8 p-9 text-10) between line and curve is again non-negative.

This one inequality proves that relative entropy can never be negative. Writing
$x_i = p_i/q_i - 1$ and summing with weights $q_i$ turns $\log(1+x) \le x$ into

$$
\sum_i q_i \log\frac{p_i}{q_i} \le \sum_i (p_i - q_i) = 0,
$$

and flipping the sign gives $D(p \,\|\, q) = \sum_i p_i \log\frac{p_i}{q_i} \ge 0$.
The {}(text-11 = \text "ln(1 + x) ≤ x, equality at x = 0. Writing xᵢ = pᵢ/qᵢ − 1 gives Σ qᵢ ln(pᵢ/qᵢ) ≤ Σ(pᵢ − qᵢ) = 0, so the KL divergence D(p ‖ q) = Σ pᵢ ln(pᵢ/qᵢ) ≥ 0.") {full argument}(tbox-1 = \annotate-text-box text-11 0 -2.9 12 5.4 -1) shows how the tangent bound, read through the
logarithm, forces the KL divergence to stay non-negative.
