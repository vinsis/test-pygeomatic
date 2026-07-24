# QR decomposition via Gram–Schmidt (2D)

Every matrix with independent columns factors as $A = QR$: an **orthonormal**
matrix $Q$ (its columns are unit vectors at right angles) times an
**upper-triangular** $R$. Gram–Schmidt builds $Q$ one column at a time, straight
from the columns of $A$. We take the two columns $\blue{a_1}=(2,3)$ and
$\cyan{a_2}=(4,1)$ and watch $Q$ and $R$ fill in click by click.

$$
%id:aqr
A=\begin{pmatrix} 2 & 4 \\ 3 & 1 \end{pmatrix}
=\begin{pmatrix} q_{11} & q_{21} \\ q_{12} & q_{22} \end{pmatrix}
\begin{pmatrix} r_{11} & r_{12} \\ 0 & r_{22} \end{pmatrix}
$$

```pygeomatic
gm.clear()
unit = gm.scalar(55, out="unit")
c = gm.load_colors()

origin = gm.p0

# exact Gram-Schmidt coordinates (chosen so proj lies on q1 and u2 ⟂ q1)
pa1 = gm.point(2.0, 3.0)
pa2 = gm.point(4.0, 1.0)
pq1 = gm.point(0.5547002, 0.8320503)
pq2 = gm.point(0.8320503, -0.5547002)
pproj = gm.point(1.6923077, 2.5384615)
pu2 = gm.point(2.3076923, -1.5384615)
gm.hide(pa1, pa2, pq1, pq2, pproj, pu2)   # the arrows carry the visible geometry

# live numeric values bound into the formulas below
anorm = gm.scalar(3.6055513, out="anorm")   # ‖a1‖ = √13
adotq = gm.scalar(0, out="adotq")           # a2·q1, starts 0 and fills in at the projection beat
unorm = gm.scalar(2.7735010, out="unorm")   # ‖u2‖

# per-beat gates that drive the formula reactivity (0 → 1 at each click)
gate_a = gm.scalar(0, out="gate-a")       # lights q1's column of Q
gate_proj = gm.scalar(0, out="gate-proj") # reveals a2·q1 and R's (1,2) entry
gate_b = gm.scalar(0, out="gate-b")       # lights q2's column of Q
kq = gm.scalar(0, out="kq")               # how many columns of Q are revealed

with group("originals"):
    ucirc = gm.circle(origin, 1)
    gm.set_stroke(ucirc, c.GRAY)
    arr_a1 = gm.annotate_arrow(origin, pa1)
    arr_a2 = gm.annotate_arrow(origin, pa2)
    gm.set_stroke(arr_a1, c.BLUE)
    gm.set_stroke(arr_a2, c.CYAN)
    lab_a1 = gm.annotate_text_box("a1", 2.35, 3.4, 14)
    lab_a2 = gm.annotate_text_box("a2", 4.55, 1.25, 14)
    gm.set_fill(lab_a1, c.BLUE)
    gm.set_fill(lab_a2, c.CYAN)      # completer

with group("q1beat"):
    arr_q1 = gm.annotate_arrow(origin, pq1)
    gm.set_stroke(arr_q1, c.AMBER)
    gate_a = gm.scalar(1, out="gate-a")   # spotlight Q's first column
    kq = gm.scalar(1, out="kq")           # reveal Q's first column
    lab_q1 = gm.annotate_text_box("q1", 1.05, 0.45, 14)
    gm.set_fill(lab_q1, c.AMBER)     # completer

with group("projbeat"):
    arr_proj = gm.annotate_arrow(origin, pproj)
    gm.set_stroke(arr_proj, c.TEAL)
    adotq = gm.scalar(3.0508510, out="adotq")  # a2·q1 fills into the frac slot
    gate_proj = gm.scalar(1, out="gate-proj")  # reveal a2·q1, light R's (1,2)
    lab_proj = gm.annotate_text_box("proj", 0.65, 2.75, 13)
    gm.set_fill(lab_proj, c.TEAL) # completer

with group("u2beat"):
    arr_u2res = gm.annotate_arrow(pproj, pa2)
    gm.set_stroke(arr_u2res, c.GREEN)
    arr_u2 = gm.annotate_arrow(origin, pu2)
    gm.set_stroke(arr_u2, c.GREEN)
    lab_u2res = gm.annotate_text_box("u2 = a2 - proj", 3.55, 2.0, 13)
    lab_u2 = gm.annotate_text_box("u2", 2.7, -1.8, 14)
    gm.set_fill(lab_u2res, c.GREEN)
    gm.set_fill(lab_u2, c.GREEN)     # completer

with group("q2beat"):
    arr_q2 = gm.annotate_arrow(origin, pq2)
    gm.set_stroke(arr_q2, c.ORANGE)
    gate_b = gm.scalar(1, out="gate-b")   # spotlight Q's second column
    kq = gm.scalar(2, out="kq")           # reveal Q's second column
    lab_q2 = gm.annotate_text_box("q2", 1.2, -0.8, 14)
    gm.set_fill(lab_q2, c.ORANGE)    # completer

with group("rightangle"):
    q1line = gm.line(origin, pq1)
    q2line = gm.line(origin, pq2)
    gm.hide(q1line, q2line)          # helper lines only fix the mark's vertex
    rightangle = gm.annotate_angle_mark(q1line, q2line, "90")
    gm.set_stroke(rightangle, c.WHITE)   # completer
```

We start with the raw data: the {two original columns}(ref:originals)
$\blue{a_1}=(2,3)$ and $\cyan{a_2}=(4,1)$ drawn from the origin, with the unit
circle as a length gauge. Nothing in $Q$ or $R$ is known yet.

$$
%id:q1def
q_1=\frac{1}{\lVert a_1\rVert}\begin{pmatrix} 2 \\ 3 \end{pmatrix}
=\begin{pmatrix} 0.5547 \\ 0.8321 \end{pmatrix}
$$

The first column of $Q$ is just $a_1$ rescaled to length one. {Normalize to
$\amber{q_1}$}(ref:q1beat): the amber arrow lands exactly on the unit circle, and
$Q$'s first column lights up.

- The norm $\lVert a_1\rVert=\sqrt{13}$ is bound live into the formula, and it
  is $r_{11}$ of $R$.

$$
%id:proj
\text{proj}=\frac{a_2\!\cdot q_1}{\lVert q_1\rVert^{2}}\,q_1,\qquad
\underbrace{a_2\!\cdot q_1}_{=\,r_{12}}
$$

Now split $\cyan{a_2}$ into a part along $q_1$ and a part perpendicular to it.
{Project $\cyan{a_2}$ onto $\amber{q_1}$}(ref:projbeat): the teal arrow slides
along $q_1$, and the live inner product $\teal{a_2\!\cdot q_1}$ fills into the
numerator, this is $r_{12}$.

$$
%id:u2def
u_2=a_2-\text{proj},\qquad
q_2=\frac{1}{\lVert u_2\rVert}\begin{pmatrix} 2.3077 \\ -1.5385 \end{pmatrix}
=\begin{pmatrix} 0.8321 \\ -0.5547 \end{pmatrix}
$$

What is left over is orthogonal to $q_1$. The {residual $\green{u_2}$}(ref:u2beat)
is $a_2$ minus its projection, drawn both as the drop from $\teal{\text{proj}}$
up to $a_2$ and translated back to the origin. Its length
$\lVert u_2\rVert=2.7735$ is $r_{22}$.

- {Normalize to $\orange{q_2}$}(ref:q2beat) to finish the basis: the orange arrow
  is the second unit column of $Q$.
- The {right-angle marker}(ref:rightangle) confirms $q_1\cdot q_2=0$, so
  $\{q_1,q_2\}$ really is orthonormal.

**Takeaway.** The two arrows $\amber{q_1}$ and $\orange{q_2}$ you built *are* the
columns of $Q$. Because they are orthonormal, $Q^{\top}Q=I$, so $R=Q^{\top}A$, and
every entry below its diagonal is a dot product of orthogonal columns, which is
$0$. That is why $R$ comes out upper-triangular for free.

```pygeomatic
aqr = gm.tex("aqr")
# Q's columns fade in one at a time as q1, q2 are built (matrix reveal)
aqr.reveal(gm.cols < kq, matrix=1)
# spotlight the Q column being constructed at each beat (matrix highlight)
aqr.highlight((gm.cols == 0).scale(gate_a), color="amber", matrix=1)
aqr.highlight((gm.cols == 1).scale(gate_b), color="orange", matrix=1)
# R's entries light up as each scalar is computed
aqr.highlight(((gm.rows == 0) & (gm.cols == 0)).scale(gate_a), color="amber", matrix=2)
aqr.highlight(((gm.rows == 0) & (gm.cols == 1)).scale(gate_proj), color="teal", matrix=2)
aqr.highlight(((gm.rows == 1) & (gm.cols == 1)).scale(gate_b), color="orange", matrix=2)

# live values bound into the definition formulas
q1def = gm.tex("q1def")
q1def.frac.denom.bind(anorm, fmt=".4f")   # ‖a1‖ = √13

proj = gm.tex("proj")
proj.frac.num.bind(adotq, fmt=".4f")   # a2·q1 = 11/√13 lands in the numerator (‖q1‖²=1)
proj.underbrace.reveal(gate_proj)      # pure reveal, appears at the projection beat

u2def = gm.tex("u2def")
u2def.frac.denom.bind(unorm, fmt=".4f")   # ‖u2‖
```

# QR at full size: a 5×5 column-by-column build

The 2D picture was the whole algorithm in miniature. Gram–Schmidt does not care
how many columns there are: it normalizes the first residual to $q_1$, subtracts
that direction out of the rest, and repeats. Here is the identical move on a
concrete $5\times5$ matrix $A$, whose real `numpy` factorization is

$$
%id:qr5
A=\begin{pmatrix}
2 & -1 & 0 & 1 & 3 \\
1 & 4 & -2 & 0 & 1 \\
0 & 2 & 5 & -1 & 2 \\
-1 & 1 & 1 & 3 & 0 \\
3 & 0 & -1 & 2 & 4
\end{pmatrix}
=\begin{pmatrix}
-0.52 & 0.24 & -0.18 & -0.11 & -0.79 \\
-0.26 & -0.84 & 0.42 & 0.12 & -0.20 \\
0.00 & -0.43 & -0.89 & 0.18 & 0.05 \\
0.26 & -0.23 & -0.08 & -0.93 & -0.09 \\
-0.77 & 0.04 & -0.04 & -0.28 & 0.56
\end{pmatrix}
\begin{pmatrix}
-3.87 & -0.26 & 1.55 & -1.29 & -4.91 \\
0 & -4.68 & -0.73 & 0.07 & -0.80 \\
0 & 0 & -5.30 & 0.37 & -2.08 \\
0 & 0 & 0 & -3.63 & -0.96 \\
0 & 0 & 0 & 0 & -0.22
\end{pmatrix}
$$

The left factor $Q$ starts blank and the right factor $R$ starts blank. Each click
below builds one column: the pivot norm $\lVert u_k\rVert$ is the live readout, and
the active column of $Q$ and its matching column of $R$ light up in $\teal{teal}$.

$$
%id:piv5
\text{beat}=\frac{0}{5},\qquad \lVert u_k\rVert=\sqrt{0.0000}
$$

```pygeomatic
gm.clear()                          # ADDED: wipe the 2D canvas for the 5×5 view

# counters and gates driving the progressive reveal (all ADDED for this section)
kcol5 = gm.scalar(0, out="kcol5")       # how many columns of Q and R are revealed
step5 = gm.scalar(0, out="step5")       # beat index 0→5, shown as k/5
pivotsq5 = gm.scalar(0, out="pivotsq5") # ‖u_k‖² of the active column (under the sqrt)
gcol0 = gm.scalar(0, out="gcol0")       # per-column spotlight gates, one lit at a time
gcol1 = gm.scalar(0, out="gcol1")
gcol2 = gm.scalar(0, out="gcol2")
gcol3 = gm.scalar(0, out="gcol3")
gcol4 = gm.scalar(0, out="gcol4")

with group("col-1"):
    step5 = gm.scalar(1, out="step5")
    pivotsq5 = gm.scalar(15.0000, out="pivotsq5")
    gcol0 = gm.scalar(1, out="gcol0")   # spotlight the first column
    kcol5 = gm.scalar(1, out="kcol5")   # completer: Q's and R's column 1 fade in

with group("col-2"):
    step5 = gm.scalar(2, out="step5")
    pivotsq5 = gm.scalar(21.9333, out="pivotsq5")
    gcol0 = gm.scalar(0, out="gcol0")   # dim the previous column
    gcol1 = gm.scalar(1, out="gcol1")   # spotlight the second
    kcol5 = gm.scalar(2, out="kcol5")   # completer

with group("col-3"):
    step5 = gm.scalar(3, out="step5")
    pivotsq5 = gm.scalar(28.0729, out="pivotsq5")
    gcol1 = gm.scalar(0, out="gcol1")
    gcol2 = gm.scalar(1, out="gcol2")
    kcol5 = gm.scalar(3, out="kcol5")   # completer

with group("col-4"):
    step5 = gm.scalar(4, out="step5")
    pivotsq5 = gm.scalar(13.1930, out="pivotsq5")
    gcol2 = gm.scalar(0, out="gcol2")
    gcol3 = gm.scalar(1, out="gcol3")
    kcol5 = gm.scalar(4, out="kcol5")   # completer

with group("col-5"):
    step5 = gm.scalar(5, out="step5")
    pivotsq5 = gm.scalar(0.0487, out="pivotsq5")
    gcol3 = gm.scalar(0, out="gcol3")
    gcol4 = gm.scalar(1, out="gcol4")
    kcol5 = gm.scalar(5, out="kcol5")   # completer: the last column closes Q and R
```

Same Gram–Schmidt step, five times over, one click each:

- {Column 1}(ref:col-1): normalize the first residual to $q_1$; its length
  $\lVert u_1\rVert=\sqrt{15}\approx3.87$ becomes $r_{11}$.
- {Column 2}(ref:col-2): the second residual, with $q_1$ subtracted out,
  normalizes to $q_2$; its overlaps fill $R$'s second column.
- {Column 3}(ref:col-3): $q_3$ is the third residual made orthogonal to
  $q_1,q_2$; its pivot norm is the tallest, $\sqrt{28.07}\approx5.30$.
- {Column 4}(ref:col-4): $q_4$ falls out once $q_1,q_2,q_3$ are projected away.
- {Column 5}(ref:col-5): the last residual is nearly used up, so the final pivot
  $\sqrt{0.049}\approx0.22$ is tiny, and $Q$ and $R$ are complete.

```pygeomatic
qr5 = gm.tex("qr5")
# Q (matrix 1) and R (matrix 2) fill in column by column, in lockstep with kcol5
qr5.reveal(gm.cols < kcol5, matrix=1)
qr5.reveal(gm.cols < kcol5, matrix=2)
# spotlight the active column in both factors (only one gate is 1 per beat)
qr5.highlight((gm.cols == 0).scale(gcol0), color="teal", matrix=1)
qr5.highlight((gm.cols == 1).scale(gcol1), color="teal", matrix=1)
qr5.highlight((gm.cols == 2).scale(gcol2), color="teal", matrix=1)
qr5.highlight((gm.cols == 3).scale(gcol3), color="teal", matrix=1)
qr5.highlight((gm.cols == 4).scale(gcol4), color="teal", matrix=1)
qr5.highlight((gm.cols == 0).scale(gcol0), color="teal", matrix=2)
qr5.highlight((gm.cols == 1).scale(gcol1), color="teal", matrix=2)
qr5.highlight((gm.cols == 2).scale(gcol2), color="teal", matrix=2)
qr5.highlight((gm.cols == 3).scale(gcol3), color="teal", matrix=2)
qr5.highlight((gm.cols == 4).scale(gcol4), color="teal", matrix=2)

# live scalar readouts in NON-matrix slots
piv5 = gm.tex("piv5")
piv5.frac.num.bind(step5, fmt=".0f")     # beat index k, shown as k/5
piv5.sqrt.body.bind(pivotsq5, fmt=".4f") # ‖u_k‖ = sqrt(pivotsq5) updates each click
```

