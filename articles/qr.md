# QR decomposition via Gram–Schmidt (2D)

Every matrix with independent columns factors as $A = QR$: an **orthonormal**
matrix $Q$ (its columns are unit vectors at right angles) times an
**upper-triangular** $R$. Gram–Schmidt builds $Q$ one column at a time, straight
from the columns of $A$. We take the two columns $\blue{a_1}=(2,3)$ and
$\cyan{a_2}=(4,1)$ and watch $Q$ and $R$ fill in click by click.

$$
%id:aqr
\begin{aligned}
A &= \begin{pmatrix} 2 & 4 \\ 3 & 1 \end{pmatrix} \\ \\
  &= \underbrace{\begin{pmatrix} q_{11} & q_{21} \\ q_{12} & q_{22} \end{pmatrix}}_{\text{orthonormal }Q}
     \underbrace{\begin{pmatrix} r_{11} & r_{12} \\ 0 & r_{22} \end{pmatrix}}_{\text{upper-triangular }R}
\end{aligned}
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
unorm = gm.scalar(2.7735010, out="unorm")   # ‖u2‖
# NB: adotq (a2·q1) is intentionally NOT created here. It first exists at the
# projection beat, so until then the bound numerator has no env value and the
# slot shows its authored symbol a2·q1 — not a misleading 0.0000. (Binding a
# value node that reads 0 before it is meaningful is the trap; see projbeat.)

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
    adotq = gm.scalar(3.0508510, out="adotq")  # a2·q1 first defined here → numerator flips from symbol to value
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
\begin{aligned}
\amber{q_1} &= \underbrace{\frac{1}{\lVert a_1\rVert}}_{\text{scale to unit length}}\,\blue{\begin{pmatrix} 2 \\ 3 \end{pmatrix}} \\ \\
&= \amber{\begin{pmatrix} 0.5547 \\ 0.8321 \end{pmatrix}}
\end{aligned}
$$

The first column of $Q$ is just $a_1$ rescaled to length one. {Normalize to
$\amber{q_1}$}(ref:q1beat): the amber arrow lands exactly on the unit circle, and
$Q$'s first column lights up.

- The norm $\lVert a_1\rVert=\sqrt{13}$ is bound live into the formula, and it
  is $r_{11}$ of $R$.

$$
%id:proj
\teal{\text{proj}}=\teal{\frac{a_2\cdot q_1}{\lVert q_1\rVert^{2}}}\,\amber{q_1},\qquad
\underbrace{\teal{a_2\cdot q_1}}_{=\,\teal{r_{12}}}
$$

Now split $\cyan{a_2}$ into a part along $q_1$ and a part perpendicular to it.
{Project $\cyan{a_2}$ onto $\amber{q_1}$}(ref:projbeat): the teal arrow slides
along $q_1$, and the live inner product $\teal{a_2\cdot q_1}$ fills into the
numerator, this is $r_{12}$.

$$
%id:u2def
\begin{aligned}
\green{u_2} &= \cyan{a_2}-\teal{\text{proj}} \\ \\
\orange{q_2} &= \green{\frac{1}{\lVert u_2\rVert}}\,\green{\begin{pmatrix} 2.3077 \\ -1.5385 \end{pmatrix}} \\ \\
&= \orange{\begin{pmatrix} 0.8321 \\ -0.5547 \end{pmatrix}}
\end{aligned}
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

# QR at full size: a 4×4 column-by-column build

The 2D picture was the whole algorithm in miniature. Gram–Schmidt does not care
how many columns there are: it normalizes the first residual to $q_1$, subtracts
that direction out of the rest, and repeats. Here is the identical move on a
concrete $4\times4$ matrix $A$, whose real `numpy` factorization is

$$
%id:qr5
\begin{aligned}
A &= \begin{pmatrix}
2 & -1 & 0 & 1 \\
1 & 4 & -2 & 0 \\
0 & 2 & 5 & -1 \\
-1 & 1 & 1 & 3
\end{pmatrix} \\ \\
  &= \underbrace{\begin{pmatrix}
0.82 & -0.29 & 0.23 & 0.45 \\
0.41 & 0.82 & -0.40 & -0.02 \\
0.00 & 0.43 & 0.89 & -0.18 \\
-0.41 & 0.25 & 0.06 & 0.88
\end{pmatrix}}_{\text{orthonormal columns }Q}
     \underbrace{\begin{pmatrix}
2.45 & 0.41 & -1.22 & -0.41 \\
0 & 4.67 & 0.75 & 0.04 \\
0 & 0 & 5.29 & -0.48 \\
0 & 0 & 0 & 3.26
\end{pmatrix}}_{\text{upper-triangular }R}
\end{aligned}
$$

The left factor $Q$ starts blank and the right factor $R$ starts blank. Each click
below builds one column: the pivot norm $\lVert u_k\rVert$ is the live readout, and
the active column of $Q$ and its matching column of $R$ light up in $\teal{teal}$.

$$
%id:piv5
\text{beat}=\frac{0}{4},\qquad \underbrace{\sqrt{0.0000}}_{\teal{\lVert u_k\rVert}}
$$

```pygeomatic
gm.clear()                          # ADDED: wipe the 2D canvas for the 4×4 view

# counters and gates driving the progressive reveal (all ADDED for this section)
kcol5 = gm.scalar(0, out="kcol5")       # how many columns of Q and R are revealed
step5 = gm.scalar(0, out="step5")       # beat index 0→4, shown as k/4
pivotsq5 = gm.scalar(0, out="pivotsq5") # ‖u_k‖² of the active column (under the sqrt)
gcol0 = gm.scalar(0, out="gcol0")       # per-column spotlight gates, one lit at a time
gcol1 = gm.scalar(0, out="gcol1")
gcol2 = gm.scalar(0, out="gcol2")
gcol3 = gm.scalar(0, out="gcol3")

with group("col-1"):
    step5 = gm.scalar(1, out="step5")
    pivotsq5 = gm.scalar(6.0000, out="pivotsq5")
    gcol0 = gm.scalar(1, out="gcol0")   # spotlight the first column
    kcol5 = gm.scalar(1, out="kcol5")   # completer: Q's and R's column 1 fade in

with group("col-2"):
    step5 = gm.scalar(2, out="step5")
    pivotsq5 = gm.scalar(21.8333, out="pivotsq5")
    gcol0 = gm.scalar(0, out="gcol0")   # dim the previous column
    gcol1 = gm.scalar(1, out="gcol1")   # spotlight the second
    kcol5 = gm.scalar(2, out="kcol5")   # completer

with group("col-3"):
    step5 = gm.scalar(3, out="step5")
    pivotsq5 = gm.scalar(27.9389, out="pivotsq5")
    gcol1 = gm.scalar(0, out="gcol1")
    gcol2 = gm.scalar(1, out="gcol2")
    kcol5 = gm.scalar(3, out="kcol5")   # completer

with group("col-4"):
    step5 = gm.scalar(4, out="step5")
    pivotsq5 = gm.scalar(10.6036, out="pivotsq5")
    gcol2 = gm.scalar(0, out="gcol2")
    gcol3 = gm.scalar(1, out="gcol3")
    kcol5 = gm.scalar(4, out="kcol5")   # completer: the last column closes Q and R
```

Same Gram–Schmidt step, four times over, one click each:

- {Column 1}(ref:col-1): normalize the first residual to $q_1$; its length
  $\lVert u_1\rVert=\sqrt{6}\approx2.45$ becomes $r_{11}$.
- {Column 2}(ref:col-2): the second residual, with $q_1$ subtracted out,
  normalizes to $q_2$; its overlaps fill $R$'s second column.
- {Column 3}(ref:col-3): $q_3$ is the third residual made orthogonal to
  $q_1,q_2$; its pivot norm is the tallest, $\sqrt{27.94}\approx5.29$.
- {Column 4}(ref:col-4): $q_4$ falls out once $q_1,q_2,q_3$ are projected away,
  so the final pivot $\sqrt{10.60}\approx3.26$ closes $Q$ and $R$.

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
qr5.highlight((gm.cols == 0).scale(gcol0), color="teal", matrix=2)
qr5.highlight((gm.cols == 1).scale(gcol1), color="teal", matrix=2)
qr5.highlight((gm.cols == 2).scale(gcol2), color="teal", matrix=2)
qr5.highlight((gm.cols == 3).scale(gcol3), color="teal", matrix=2)

# live scalar readouts in NON-matrix slots
piv5 = gm.tex("piv5")
piv5.frac.num.bind(step5, fmt=".0f")     # beat index k, shown as k/4
piv5.sqrt.body.bind(pivotsq5, fmt=".4f") # ‖u_k‖ = sqrt(pivotsq5) updates each click
```

