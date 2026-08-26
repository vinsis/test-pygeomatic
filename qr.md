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

{}(\clear)
{}(unit = \scalar 55)
{}(\load-colors)
{}(pa1 = \point 2 3)
{}(pa2 = \point 4 1)
{}(pq1 = \point 0.5547002 0.8320503)
{}(pq2 = \point 0.8320503 -0.5547002)
{}(pproj = \point 1.6923077 2.5384615)
{}(pu2 = \point 2.3076923 -1.5384615)
{}(\hide pa1 pa2 pq1 pq2 pproj pu2)
{}(anorm = \scalar 3.6055513)
{}(unorm = \scalar 2.773501)
{}(gate-a = \scalar 0)
{}(gate-proj = \scalar 0)
{}(gate-b = \scalar 0)
{}(kq = \scalar 0)

We start with the raw data: the {}(ucirc = \circle p0 1) {}(\set-stroke ucirc COLOR-GRAY) {}(arr-a1 = \annotate-arrow p0 pa1) {}(arr-a2 = \annotate-arrow p0 pa2) {}(\set-stroke arr-a1 COLOR-BLUE) {}(\set-stroke arr-a2 COLOR-CYAN) {}(text-0 = \text "a1") {}(lab-a1 = \annotate-text-box text-0 2.35 3.4 14) {}(text-1 = \text "a2") {}(lab-a2 = \annotate-text-box text-1 4.55 1.25 14) {}(\set-fill lab-a1 COLOR-BLUE) {two original columns}(\set-fill lab-a2 COLOR-CYAN)
$\blue{a_1}=(2,3)$ and $\cyan{a_2}=(4,1)$ drawn from the origin, with the unit
circle as a length gauge. Nothing in $Q$ or $R$ is known yet.

$$
%id:q1def
\begin{aligned}
\amber{q_1} &= \underbrace{\frac{1}{\lVert a_1\rVert}}_{\text{scale to unit length}}\,\blue{\begin{pmatrix} 2 \\ 3 \end{pmatrix}} \\ \\
&= \amber{\begin{pmatrix} 0.5547 \\ 0.8321 \end{pmatrix}}
\end{aligned}
$$

The first column of $Q$ is just $a_1$ rescaled to length one. {}(arr-q1 = \annotate-arrow p0 pq1) {}(\set-stroke arr-q1 COLOR-AMBER) {}(gate-a = \scalar 1) {}(kq = \scalar 1) {}(text-2 = \text "q1") {}(lab-q1 = \annotate-text-box text-2 1.05 0.45 14) {Normalize to
$\amber{q_1}$}(\set-fill lab-q1 COLOR-AMBER): the amber arrow lands exactly on the unit circle, and
$Q$'s first column lights up.

- The norm $\lVert a_1\rVert=\sqrt{13}$ is bound live into the formula, and it
  is $r_{11}$ of $R$.

$$
%id:proj
\teal{\text{proj}}=\teal{\frac{a_2\cdot q_1}{\lVert q_1\rVert^{2}}}\,\amber{q_1},\qquad
\underbrace{\teal{a_2\cdot q_1}}_{=\,\teal{r_{12}}}
$$

Now split $\cyan{a_2}$ into a part along $q_1$ and a part perpendicular to it.
{}(arr-proj = \annotate-arrow p0 pproj) {}(\set-stroke arr-proj COLOR-TEAL) {}(adotq = \scalar 3.050851) {}(gate-proj = \scalar 1) {}(text-3 = \text "proj") {}(lab-proj = \annotate-text-box text-3 0.65 2.75 13) {Project $\cyan{a_2}$ onto $\amber{q_1}$}(\set-fill lab-proj COLOR-TEAL): the teal arrow slides
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

What is left over is orthogonal to $q_1$. The {}(arr-u2res = \annotate-arrow pproj pa2) {}(\set-stroke arr-u2res COLOR-GREEN) {}(arr-u2 = \annotate-arrow p0 pu2) {}(\set-stroke arr-u2 COLOR-GREEN) {}(text-4 = \text "u2 = a2 - proj") {}(lab-u2res = \annotate-text-box text-4 3.55 2 13) {}(text-5 = \text "u2") {}(lab-u2 = \annotate-text-box text-5 2.7 -1.8 14) {}(\set-fill lab-u2res COLOR-GREEN) {residual $\green{u_2}$}(\set-fill lab-u2 COLOR-GREEN)
is $a_2$ minus its projection, drawn both as the drop from $\teal{\text{proj}}$
up to $a_2$ and translated back to the origin. Its length
$\lVert u_2\rVert=2.7735$ is $r_{22}$.

- {}(arr-q2 = \annotate-arrow p0 pq2) {}(\set-stroke arr-q2 COLOR-ORANGE) {}(gate-b = \scalar 1) {}(kq = \scalar 2) {}(text-6 = \text "q2") {}(lab-q2 = \annotate-text-box text-6 1.2 -0.8 14) {Normalize to $\orange{q_2}$}(\set-fill lab-q2 COLOR-ORANGE) to finish the basis: the orange arrow
  is the second unit column of $Q$.
- The {}(q1line = \line p0 pq1) {}(q2line = \line p0 pq2) {}(\hide q1line q2line) {}(text-7 = \text "90") {}(rightangle = \annotate-angle-mark q1line q2line text-7) {right-angle marker}(\set-stroke rightangle COLOR-WHITE) confirms $q_1\cdot q_2=0$, so
  $\{q_1,q_2\}$ really is orthonormal.

**Takeaway.** The two arrows $\amber{q_1}$ and $\orange{q_2}$ you built *are* the
columns of $Q$. Because they are orthonormal, $Q^{\top}Q=I$, so $R=Q^{\top}A$, and
every entry below its diagonal is a dot product of orthogonal columns, which is
$0$. That is why $R$ comes out upper-triangular for free.


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
\blue{0.82} & \pink{-0.29} & \green{0.23} & \amber{0.45} \\
\blue{0.41} & \pink{0.82} & \green{-0.40} & \amber{-0.02} \\
\blue{0.00} & \pink{0.43} & \green{0.89} & \amber{-0.18} \\
\blue{-0.41} & \pink{0.25} & \green{0.06} & \amber{0.88}
\end{pmatrix}}_{\text{orthonormal columns }Q}
     \underbrace{\begin{pmatrix}
\blue{2.45} & \blue{0.41} & \blue{-1.22} & \blue{-0.41} \\
0 & \pink{4.67} & \pink{0.75} & \pink{0.04} \\
0 & 0 & \green{5.29} & \green{-0.48} \\
0 & 0 & 0 & \amber{3.26}
\end{pmatrix}}_{\text{upper-triangular }R}
\end{aligned}
$$

Each column of $Q$ carries its own color, $\blue{q_1}\,\pink{q_2}\,\green{q_3}\,\amber{q_4}$,
and $R$ is tinted to match: entry $R_{ik}=q_i\cdot a_k$ takes the color of $q_i$, so the top
$k$ entries of $R$'s $k$-th column replay, top to bottom, which earlier $q_i$'s the $k$-th
column of $A$ leaned on before it was normalized.

The left factor $Q$ starts blank and the right factor $R$ starts blank. Each click
below builds one column: the pivot norm $\lVert u_k\rVert$ is the live readout, and
the active column of $Q$ and its matching column of $R$ light up in $\teal{teal}$.

$$
%id:piv5
\text{beat}=\frac{0}{4},\qquad \underbrace{\sqrt{0.0000}}_{\teal{\lVert u_k\rVert}}
$$

{}(\clear)
{}(kcol5 = \scalar 0)
{}(step5 = \scalar 0)
{}(pivotsq5 = \scalar 0)
{}(gcol0 = \scalar 0)
{}(gcol1 = \scalar 0)
{}(gcol2 = \scalar 0)
{}(gcol3 = \scalar 0)

Same Gram–Schmidt step, four times over, one click each:

- {}(step5 = \scalar 1) {}(pivotsq5 = \scalar 6) {}(gcol0 = \scalar 1) {Column 1}(kcol5 = \scalar 1): normalize the first residual to $q_1$; its length
  $\lVert u_1\rVert=\sqrt{6}\approx2.45$ becomes $r_{11}$.
- {}(step5 = \scalar 2) {}(pivotsq5 = \scalar 21.8333) {}(gcol0 = \scalar 0) {}(gcol1 = \scalar 1) {Column 2}(kcol5 = \scalar 2): the second residual, with $q_1$ subtracted out,
  normalizes to $q_2$; its overlaps fill $R$'s second column.
- {}(step5 = \scalar 3) {}(pivotsq5 = \scalar 27.9389) {}(gcol1 = \scalar 0) {}(gcol2 = \scalar 1) {Column 3}(kcol5 = \scalar 3): $q_3$ is the third residual made orthogonal to
  $q_1,q_2$; its pivot norm is the tallest, $\sqrt{27.94}\approx5.29$.
- {}(step5 = \scalar 4) {}(pivotsq5 = \scalar 10.6036) {}(gcol2 = \scalar 0) {}(gcol3 = \scalar 1) {Column 4}(kcol5 = \scalar 4): $q_4$ falls out once $q_1,q_2,q_3$ are projected away,
  so the final pivot $\sqrt{10.60}\approx3.26$ closes $Q$ and $R$.



<!-- texatlas:v1
{"aqr":{"highlights":[{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":0}},"by":{"node":"gate-a"}},"color":"#F59E0B","matrix":1},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}},"by":{"node":"gate-b"}},"color":"#F97316","matrix":1},{"selector":{"op":"scale","sel":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":0}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":0}}},"by":{"node":"gate-a"}},"color":"#F59E0B","matrix":2},{"selector":{"op":"scale","sel":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":0}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}}},"by":{"node":"gate-proj"}},"color":"#14B8A6","matrix":2},{"selector":{"op":"scale","sel":{"op":"and","a":{"op":"eq","axis":{"axis":"row"},"value":{"const":1}},"b":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}}},"by":{"node":"gate-b"}},"color":"#F97316","matrix":2}],"reveals":[{"matrix":1,"selector":{"op":"lt","axis":{"axis":"col"},"value":{"node":"kq"}}}]},"q1def":{"values":[{"slot":"frac.denom","node":"anorm","fmt":".4f"}]},"proj":{"values":[{"slot":"frac.num","node":"adotq","fmt":".4f"}],"reveals":[{"slot":"underbrace","selector":{"node":"gate-proj"}}]},"u2def":{"values":[{"slot":"frac.denom","node":"unorm","fmt":".4f"}]},"qr5":{"highlights":[{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":0}},"by":{"node":"gcol0"}},"color":"#14B8A6","matrix":1},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}},"by":{"node":"gcol1"}},"color":"#14B8A6","matrix":1},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":2}},"by":{"node":"gcol2"}},"color":"#14B8A6","matrix":1},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":3}},"by":{"node":"gcol3"}},"color":"#14B8A6","matrix":1},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":0}},"by":{"node":"gcol0"}},"color":"#14B8A6","matrix":2},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":1}},"by":{"node":"gcol1"}},"color":"#14B8A6","matrix":2},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":2}},"by":{"node":"gcol2"}},"color":"#14B8A6","matrix":2},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"col"},"value":{"const":3}},"by":{"node":"gcol3"}},"color":"#14B8A6","matrix":2}],"reveals":[{"matrix":1,"selector":{"op":"lt","axis":{"axis":"col"},"value":{"node":"kcol5"}}},{"matrix":2,"selector":{"op":"lt","axis":{"axis":"col"},"value":{"node":"kcol5"}}}]},"piv5":{"values":[{"slot":"frac.num","node":"step5","fmt":".0f"},{"slot":"sqrt.body","node":"pivotsq5","fmt":".4f"}]}}
-->
