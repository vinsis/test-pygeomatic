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
adotq = gm.scalar(3.0508510, out="adotq")   # a2·q1 = 11/√13
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
    gm.set_stroke(arr_proj, c.EMERALD)
    gate_proj = gm.scalar(1, out="gate-proj")  # reveal a2·q1, light R's (1,2)
    lab_proj = gm.annotate_text_box("proj", 0.65, 2.75, 13)
    gm.set_fill(lab_proj, c.EMERALD) # completer

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
\text{proj}=(a_2\!\cdot q_1)\,q_1,\qquad
\underbrace{a_2\!\cdot q_1}_{3.05}
$$

Now split $\cyan{a_2}$ into a part along $q_1$ and a part perpendicular to it.
{Project $\cyan{a_2}$ onto $\amber{q_1}$}(ref:projbeat): the emerald arrow slides
along $q_1$, and the live inner product $\emerald{a_2\!\cdot q_1}$ appears, this
is $r_{12}$.

$$
%id:u2def
u_2=a_2-\text{proj},\qquad
q_2=\frac{1}{\lVert u_2\rVert}\begin{pmatrix} 2.3077 \\ -1.5385 \end{pmatrix}
=\begin{pmatrix} 0.8321 \\ -0.5547 \end{pmatrix}
$$

What is left over is orthogonal to $q_1$. The {residual $\green{u_2}$}(ref:u2beat)
is $a_2$ minus its projection, drawn both as the drop from $\emerald{\text{proj}}$
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
aqr.highlight(((gm.rows == 0) & (gm.cols == 1)).scale(gate_proj), color="emerald", matrix=2)
aqr.highlight(((gm.rows == 1) & (gm.cols == 1)).scale(gate_b), color="orange", matrix=2)

# live values bound into the definition formulas
q1def = gm.tex("q1def")
q1def.frac.denom.bind(anorm, fmt=".4f")   # ‖a1‖ = √13

proj = gm.tex("proj")
proj.underbrace.label.bind(adotq, fmt=".4f")   # a2·q1 = 11/√13
proj.underbrace.reveal(gate_proj)              # appears at the projection beat

u2def = gm.tex("u2def")
u2def.frac.denom.bind(unorm, fmt=".4f")   # ‖u2‖
```
