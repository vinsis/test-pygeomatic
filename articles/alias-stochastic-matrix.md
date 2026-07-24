# A linear-algebraic treatment of the alias table for O(1) discrete sampling

The alias method is the fastest way to sample from a fixed discrete
distribution. It pays an O(n) cost once to build an *alias table*, and from then
on every draw takes O(1) time, independent of how many outcomes there are. The
usual account of the method is combinatorial: you chop the probability mass into
bins, then pour the surplus of the heavy bins into the deficit of the light
ones. This article takes a different route. It reads the alias table as a
**row-stochastic matrix** $P$, and lets linear algebra explain why the table
works, what its spectrum means, and where the constant-time guarantee comes
from.

## The alias table is a directed graph

The finished table stores two numbers per bin $i$: a threshold $\tau_i$ (the
probability of *keeping* bin $i$) and an alias $j$ (the bin you *jump* to
otherwise). That is exactly a weighted directed graph. Each bin is a node with a
self-loop of weight $\tau_i$ and one outgoing alias edge of weight $1-\tau_i$.

```pygeomatic
with group("dag1"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(55, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    subs = "₀₁₂₃₄₅₆₇₈₉"
    r = 0.6
    xs = [-3.2, 0.0, 3.2]
    taus = [0.9, 1.0, 0.3]
    alias = [2, None, 2]          # 1-based alias bin per source bin
    alias_w = [0.1, None, 0.7]    # 1 - tau_i
    centers = [(x, 0.0) for x in xs]
    for i, (x, y) in enumerate(centers):
        p = gm.point(x, y, out=f"e1-center-{i + 1}")
        gm.hide(p)
        circ = gm.circle(p, r, out=f"e1-node-{i + 1}")
        if taus[i] == 1.0:
            gm.set_fill(circ, c.GREEN)
            gm.set_stroke(circ, c.EMERALD)
        else:
            gm.set_fill(circ, c["COLOR-GRAY-DARK"])
            gm.set_stroke(circ, c.WHITE)
    for i, (x, y) in enumerate(centers):
        a = gm.point(x - 0.3, y + 0.52, out=f"e1-loopa-{i + 1}")
        b = gm.point(x + 0.3, y + 0.52, out=f"e1-loopb-{i + 1}")
        ctl = gm.point(x, y + 1.75, out=f"e1-loopc-{i + 1}")
        gm.hide(a, b, ctl)
        loop = gm.annotate_curved_arrow(a, b, ctl, 0.0, out=f"e1-loop-{i + 1}")
        gm.set_stroke(loop, c.GREEN)
    for i in range(3):
        if alias[i] is None:
            continue
        j = alias[i] - 1
        x1, y1 = centers[i]
        x2, y2 = centers[j]
        dx, dy = x2 - x1, y2 - y1
        d = (dx * dx + dy * dy) ** 0.5
        ux, uy = dx / d, dy / d
        pa = gm.point(x1 + r * ux, y1 + r * uy, out=f"e1-edgea-{i + 1}")
        pb = gm.point(x2 - r * ux, y2 - r * uy, out=f"e1-edgeb-{i + 1}")
        gm.hide(pa, pb)
        arr = gm.annotate_arrow(pa, pb, 0.06, out=f"e1-alias-{i + 1}")
        gm.set_stroke(arr, c.AMBER)
    for i, (x, y) in enumerate(centers):
        idx = gm.annotate_text_box(str(i + 1), x, y, 20, out=f"e1-idx-{i + 1}")
        gm.set_fill(idx, c.WHITE)
        tl = gm.annotate_text_box(
            f"τ{subs[i + 1]} = {taus[i]}", x, y + 1.98, 14, out=f"e1-tau-{i + 1}"
        )
        gm.set_fill(tl, c.GREEN)
    for i in range(3):
        if alias[i] is None:
            continue
        mx = (centers[i][0] + centers[alias[i] - 1][0]) / 2
        wl = gm.annotate_text_box(
            f"1−τ{subs[i + 1]} = {alias_w[i]}  → bin {alias[i]}",
            mx, -0.75, 13, out=f"e1-w-{i + 1}",
        )
        gm.set_fill(wl, c.AMBER)
    abs_lab = gm.annotate_text_box("absorbing (τ=1)", 0.0, -1.05, 12, out="e1-abs")
    gm.set_fill(abs_lab, c.EMERALD)
    gm.annotate_text_box(
        "Example 1 alias DAG. Each bin keeps itself with probability τᵢ "
        "(green self-loop) and jumps to its alias bin with probability 1−τᵢ "
        "(amber edge). Bin 2 (τ=1) is absorbing.",
        0.0, -2.4, 13, 9.4, -1, out="e1-summary",
    )
```

Take a three-bin table with $\tau = (0.9, 1.0, 0.3)$, where bins $1$ and $3$
both alias to bin $2$. Drawn out, that is the {three-bin alias
DAG}(ref:dag1): green self-loops carry the keep-mass, amber arrows carry the
jump-mass, and bin $2$ has no amber arrow at all.

**Structure.** Because the two weights on every node sum to $\tau_i + (1-\tau_i)
= 1$, the graph is *row-stochastic*: the out-weights of each bin form a
probability distribution. And because sampling never revisits a bin (a keep
stops, a jump lands and stops), the reachability relation is acyclic. The alias
table is a **row-stochastic directed acyclic graph**, and that is the object the
rest of this article studies.

## A row-stochastic matrix with two nonzeros per row

Collect those out-weights into a matrix. Row $i$ gets $\tau_i$ on the diagonal
and $1-\tau_i$ in the alias column $j$, and zeros everywhere else.

```pygeomatic
with group("matrix"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(60, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    s = 1.3
    P = [[0.9, 0.1, 0.0], [0.0, 1.0, 0.0], [0.0, 0.7, 0.3]]
    for i in range(3):
        cy = (1 - i) * s
        for j in range(3):
            cx = (j - 1) * s
            bl = gm.point(cx - s / 2, cy - s / 2, out=f"m-bl-{i + 1}-{j + 1}")
            gm.hide(bl)
            cell = gm.square(bl, s, 0, out=f"m-cell-{i + 1}-{j + 1}")
            v = P[i][j]
            if v == 0:
                gm.set_fill(cell, c["COLOR-GRAY-DARK"])
                gm.set_stroke(cell, c["COLOR-GRAY-MID"])
            elif i == j:
                gm.set_fill(cell, c.EMERALD if v == 1.0 else c.GREEN)
                gm.set_stroke(cell, c.WHITE)
            else:
                gm.set_fill(cell, c.AMBER)
                gm.set_stroke(cell, c.WHITE)
    for i in range(3):
        cy = (1 - i) * s
        for j in range(3):
            v = P[i][j]
            if v != 0:
                lab = gm.annotate_text_box(
                    str(v), (j - 1) * s, cy, 15, out=f"m-v-{i + 1}-{j + 1}"
                )
                gm.set_fill(lab, c.WHITE)
    for j in range(3):
        gm.annotate_text_box(
            f"col {j + 1}", (j - 1) * s, 1.3 + s / 2 + 0.38, 11, out=f"m-col-{j + 1}"
        )
    for i in range(3):
        gm.annotate_text_box(
            f"row {i + 1}", -1.3 - s / 2 - 0.6, (1 - i) * s, 11, out=f"m-row-{i + 1}"
        )
    title = gm.annotate_text_box("P   (Example 1)", 0.0, 1.3 + s / 2 + 0.98, 15, out="m-title")
    gm.set_fill(title, c.WHITE)
    gm.annotate_text_box(
        "Every row has exactly two nonzeros: τᵢ on the diagonal (green, "
        "= the eigenvalues of the triangular-ish P) and 1−τᵢ at the alias "
        "column (amber). Row 2 is absorbing (τ=1). Blank cells are 0.",
        0.0, -2.95, 13, 9.4, -1, out="m-summary",
    )
```

Here is {$P$ as a heatmap}(ref:matrix), Example 1 laid out cell by cell. The
two nonzeros per row are unmissable: a green keep-cell on the diagonal, an amber
jump-cell in the alias column. The same matrix, written as a live formula, lets
us paint the two roles directly:

$$
%id:P
P=\begin{bmatrix} 0.9 & 0.1 & 0 \\ 0 & 1 & 0 \\ 0 & 0.7 & 0.3 \end{bmatrix}
$$

```pygeomatic
Ptex = gm.tex("P")
Ptex.highlight(Ptex.diag(), color="GREEN")
Ptex.highlight(Ptex[0, 1] | Ptex[2, 1], color="AMBER")
```

**Spectral insight.** For this table the diagonal $\tau_i$ are not just entries,
they are the *eigenvalues*. Whenever the alias graph can be topologically
ordered, $P$ is (a permutation of) a triangular matrix, and a triangular
matrix's eigenvalues are precisely its diagonal. So the spectrum of the alias
table is the multiset of keep-thresholds $\{0.9, 1.0, 0.3\}$. The green diagonal
you just painted is the spectrum.

## Multiplying by P recovers the target: vP

The point of the construction is that $P$ transports the *uniform* choice of a
bin into the *target* distribution. Let $v = (\tfrac13,\tfrac13,\tfrac13)$ be the
uniform row vector. Then $vP$ is the target.

```pygeomatic
with group("vp"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(60, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    scale = 3.4
    y0 = -1.4
    w = 0.55
    sp = 0.85
    uniform = ["1/3", "1/3", "1/3"]
    uniform_h = [1 / 3, 1 / 3, 1 / 3]
    target = [0.3, 0.6, 0.1]
    lc, rc = -3.3, 3.3
    lx = [lc - sp, lc, lc + sp]
    rx = [rc - sp, rc, rc + sp]
    for i in range(3):
        h = uniform_h[i] * scale
        bl = gm.point(lx[i] - w / 2, y0, out=f"vp-lbl-{i + 1}")
        gm.hide(bl)
        bar = gm.rectangle(bl, w, h, 0, out=f"vp-lbar-{i + 1}")
        gm.set_fill(bar, c.GRAY)
        gm.set_stroke(bar, c.WHITE)
    for i in range(3):
        h = target[i] * scale
        bl = gm.point(rx[i] - w / 2, y0, out=f"vp-rbl-{i + 1}")
        gm.hide(bl)
        bar = gm.rectangle(bl, w, h, 0, out=f"vp-rbar-{i + 1}")
        gm.set_fill(bar, c.BLUE)
        gm.set_stroke(bar, c.WHITE)
    ap = gm.point(-1.5, 0.25, out="vp-ap")
    bp = gm.point(1.5, 0.25, out="vp-bp")
    gm.hide(ap, bp)
    arr = gm.annotate_arrow(ap, bp, 0.02, out="vp-arrow")
    gm.set_stroke(arr, c.WHITE)
    for i in range(3):
        h = uniform_h[i] * scale
        gm.annotate_text_box(uniform[i], lx[i], y0 + h + 0.28, 12, out=f"vp-lv-{i + 1}")
        gm.annotate_text_box(str(i + 1), lx[i], y0 - 0.32, 12, out=f"vp-lidx-{i + 1}")
    for i in range(3):
        h = target[i] * scale
        gm.annotate_text_box(str(target[i]), rx[i], y0 + h + 0.28, 12, out=f"vp-rv-{i + 1}")
        gm.annotate_text_box(str(i + 1), rx[i], y0 - 0.32, 12, out=f"vp-ridx-{i + 1}")
    plab = gm.annotate_text_box("× P", 0.0, 0.78, 16, out="vp-plab")
    gm.set_fill(plab, c.AMBER)
    lg = gm.annotate_text_box("v = [1/3, 1/3, 1/3]", lc, y0 - 0.95, 13, out="vp-lg")
    gm.set_fill(lg, c.GRAY)
    rg = gm.annotate_text_box("vP = [0.3, 0.6, 0.1]", rc, y0 - 0.95, 13, out="vp-rg")
    gm.set_fill(rg, c.BLUE)
    gm.annotate_text_box(
        "Multiplying the uniform row vector v by P moves mass along the alias edges "
        "and recovers the target distribution vP.",
        0.0, -2.8, 13, 10.6, -1, out="vp-summary",
    )
```

Watch how the left-hand uniform bars, pushed through $P$, {recover the
target}(ref:vp) $vP = (0.3, 0.6, 0.1)$ on the right. Each amber edge of the DAG
is one term of the matrix product: a slice of a light bin's mass slides along
its alias arrow and piles onto the heavy bin it points at. The construction of
the table is exactly the condition $vP = \text{target}$.

## A bigger example, and the dominant eigenvalue

Nothing about this is special to three bins. Consider a five-bin table with
$\tau = (0.2, 0.4, 0.6, 1.0, 1.0)$, where bin $1$ aliases to $4$ and bins $2, 3$
alias to $5$.

```pygeomatic
with group("dag2"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(50, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    r = 0.55
    pos = {
        1: (-2.6, 2.3),
        2: (-2.6, 0.0),
        3: (-2.6, -2.3),
        4: (2.6, 1.4),
        5: (2.6, -1.4),
    }
    taus = {1: 0.2, 2: 0.4, 3: 0.6, 4: 1.0, 5: 1.0}
    alias = {1: 4, 2: 5, 3: 5, 4: None, 5: None}
    alias_w = {1: 0.8, 2: 0.6, 3: 0.4, 4: None, 5: None}
    for k in range(1, 6):
        x, y = pos[k]
        p = gm.point(x, y, out=f"e2-center-{k}")
        gm.hide(p)
        circ = gm.circle(p, r, out=f"e2-node-{k}")
        if taus[k] == 1.0:
            gm.set_fill(circ, c.GREEN)
            gm.set_stroke(circ, c.EMERALD)
        else:
            gm.set_fill(circ, c["COLOR-GRAY-DARK"])
            gm.set_stroke(circ, c.WHITE)
    for k in range(1, 6):
        x, y = pos[k]
        left = x < 0
        sx = -1 if left else 1
        a = gm.point(x + sx * 0.5, y + 0.26, out=f"e2-loopa-{k}")
        b = gm.point(x + sx * 0.5, y - 0.26, out=f"e2-loopb-{k}")
        ctl = gm.point(x + sx * 1.5, y, out=f"e2-loopc-{k}")
        gm.hide(a, b, ctl)
        loop = gm.annotate_curved_arrow(a, b, ctl, 0.0, out=f"e2-loop-{k}")
        gm.set_stroke(loop, c.GREEN)
    for k in range(1, 6):
        if alias[k] is None:
            continue
        x1, y1 = pos[k]
        x2, y2 = pos[alias[k]]
        dx, dy = x2 - x1, y2 - y1
        d = (dx * dx + dy * dy) ** 0.5
        ux, uy = dx / d, dy / d
        pa = gm.point(x1 + r * ux, y1 + r * uy, out=f"e2-edgea-{k}")
        pb = gm.point(x2 - r * ux, y2 - r * uy, out=f"e2-edgeb-{k}")
        gm.hide(pa, pb)
        arr = gm.annotate_arrow(pa, pb, 0.05, out=f"e2-alias-{k}")
        gm.set_stroke(arr, c.AMBER)
    for k in range(1, 6):
        x, y = pos[k]
        idx = gm.annotate_text_box(str(k), x, y, 19, out=f"e2-idx-{k}")
        gm.set_fill(idx, c.WHITE)
        sx = -1 if x < 0 else 1
        tl = gm.annotate_text_box(
            f"τ={taus[k]}", x + sx * 1.85, y, 13, out=f"e2-tau-{k}"
        )
        gm.set_fill(tl, c.GREEN)
    edge_label_pos = {1: (0.0, 2.05), 2: (0.15, -0.5), 3: (0.15, -2.05)}
    for k in range(1, 6):
        if alias[k] is None:
            continue
        lx, ly = edge_label_pos[k]
        wl = gm.annotate_text_box(
            f"1−τ = {alias_w[k]}  → bin {alias[k]}",
            lx, ly, 12, out=f"e2-w-{k}",
        )
        gm.set_fill(wl, c.AMBER)
    gm.annotate_text_box(
        "Example 2 alias DAG (acyclic: sources on the left, absorbing sinks on the "
        "right). Bins 4 and 5 have τ=1, so they are absorbing (self-loop only, "
        "no alias edge).",
        0.0, -3.85, 13, 11.0, -1, out="e2-summary",
    )
```

The {five-bin alias DAG}(ref:dag2) draws the sources on the left and the two
$\tau = 1$ bins as absorbing sinks on the right. Its matrix

$$
P=\begin{bmatrix}
0.2 & 0 & 0 & 0.8 & 0 \\
0 & 0.4 & 0 & 0 & 0.6 \\
0 & 0 & 0.6 & 0 & 0.4 \\
0 & 0 & 0 & 1 & 0 \\
0 & 0 & 0 & 0 & 1
\end{bmatrix}
$$

has spectrum $\{0.2, 0.4, 0.6, 1, 1\}$ again read straight off the diagonal.

**The dominant eigenvalue and absorbing states.** Every row of $P$ sums to $1$,
so the all-ones column vector satisfies $P\mathbf{1} = \mathbf{1}$: $\lambda = 1$
is always an eigenvalue, and by row-stochasticity it is the one of largest
modulus. Its multiplicity counts the *absorbing* bins, the ones with $\tau = 1$
and hence a $1$ on the diagonal and no alias edge. Here bins $4$ and $5$ are
absorbing, and $\lambda = 1$ has multiplicity two. In the DAG they are exactly
the sinks that the green self-loops never leave.

**Left eigenvectors and centrality.** The right eigenvector for $\lambda = 1$ is
uninformative (it is $\mathbf{1}$), but the *left* eigenvectors are where the
target lives: a left eigenvector $\pi$ with $\pi P = \pi$ is a stationary
distribution, and its weight concentrates on the absorbing sinks. Reading $\pi$
as graph centrality, the sinks that collect the most alias edges are the most
central nodes, and those are precisely the outcomes the target distribution
favors.

## The trace sets the branch probability

Finally, the constant-time draw. To sample, pick a bin $i$ uniformly, draw
$U \sim \text{Uniform}[0,1)$, and keep $i$ if $U < \tau_i$, otherwise jump to its
alias. Two array lookups and one comparison, no loop: that is the O(1)
guarantee.

```pygeomatic
with group("sampling"):
    gm.clear()
    c = gm.load_colors()
    gm.scalar(68, out="unit")
    gm.scalar(0, out="grid-opacity")
    gm.text("#0b1020", out="grid-bg-color")
    taus = [0.9, 1.0, 0.3]
    H = 3.0
    y0 = -1.5
    w = 1.9
    xs = [-2.0, 0.0, 2.0]
    for i, x in enumerate(xs):
        keep = taus[i] * H
        jump = (1 - taus[i]) * H
        kbl = gm.point(x - w / 2, y0, out=f"s-kbl-{i + 1}")
        gm.hide(kbl)
        kbar = gm.rectangle(kbl, w, keep, 0, out=f"s-keep-{i + 1}")
        gm.set_fill(kbar, c.GREEN)
        gm.set_stroke(kbar, c.WHITE)
        if jump > 1e-9:
            jbl = gm.point(x - w / 2, y0 + keep, out=f"s-jbl-{i + 1}")
            gm.hide(jbl)
            jbar = gm.rectangle(jbl, w, jump, 0, out=f"s-jump-{i + 1}")
            gm.set_fill(jbar, c.AMBER)
            gm.set_stroke(jbar, c.WHITE)
    for i, x in enumerate(xs):
        keep = taus[i] * H
        jump = (1 - taus[i]) * H
        gm.annotate_text_box(f"bin {i + 1}", x, y0 - 0.35, 12, out=f"s-bin-{i + 1}")
        kl = gm.annotate_text_box(
            f"keep τ={taus[i]}", x, y0 + keep / 2, 11, out=f"s-kl-{i + 1}"
        )
        gm.set_fill(kl, c.WHITE)
        if jump > 1e-9:
            jl = gm.annotate_text_box(
                f"jump {round(1 - taus[i], 1)}", x, y0 + keep + jump / 2, 11, out=f"s-jl-{i + 1}"
            )
            gm.set_fill(jl, c.WHITE)
    title = gm.annotate_text_box(
        "P(keep) = Tr(P)/n = 2.2 / 3 ≈ 0.73", 0.0, 2.15, 15, out="s-title"
    )
    gm.set_fill(title, c.GREEN)
    gm.annotate_text_box(
        "O(1) sampling: pick a bin i uniformly, draw U between 0 and 1. Keep i "
        "if U<τᵢ (green), else jump to the alias j (amber). The overall "
        "keep-probability is the green fraction Tr(P)/n.",
        0.0, -2.6, 13, 9.6, -1, out="s-summary",
    )
```

The {keep-jump split}(ref:sampling) stacks each bin's green keep-fraction
$\tau_i$ under its amber jump-fraction $1-\tau_i$. The chance a *random* draw
keeps its first bin is the average of the green heights, and that average is the
normalized **trace**:

$$
P(\text{keep}) = \frac{1}{n}\sum_{i} \tau_i = \frac{\operatorname{Tr}(P)}{n}.
$$

For Example 1, $\operatorname{Tr}(P) = 0.9 + 1.0 + 0.3 = 2.2$, so a draw keeps
its first bin with probability $2.2/3 \approx 0.73$ and follows an alias edge
only about a quarter of the time. The trace, the sum of the very eigenvalues we
read off the diagonal, is the global branch probability of the sampler. Every
piece of the algorithm, its structure, its spectrum, its dominant eigenvalue,
and its running behavior, is a statement about the matrix $P$.
