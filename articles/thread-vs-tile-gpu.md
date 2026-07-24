# Thread-based vs tile-based GPU programming: some patterns and high-level ideas

Two mental models dominate the way people write GPU kernels. The older
**thread-based** model, SIMT, asks you to reason about a single scalar thread
that touches a single data element, with thousands of such threads running the
same program. The newer **tile-based** model, the one Triton and modern kernel
DSLs lean on, asks you to reason about one *program* that owns a whole tile of
data at once. This article walks from the first model to the second, one click
at a time.

Everything below runs against a single shared store. Each numbered section calls
`gm.clear()` first, so the five scenes are independent replays: nothing from one
carries into the next.

> *Authoring note: the visuals were designed and verified elsewhere; I only
> grouped the commands into reveal beats and wrote the prose. The one code
> addition is a `gm.clear()` at the top of each scene, flagged in the report,
> which replaces the separate `gm.Store()` contexts the source used.*

## 1. One thread, one element

The hardware launches a **grid** of threads, carved into fixed-size **blocks**.
In this picture the grid is a single row of eight lanes, and each lane's whole
job is to process one slot of a one-dimensional array `A`.

```pygeomatic
def _cell(gm, x, y, w, h):
    """A rectangle by its bottom-left corner; returns the Polygon."""
    bl = gm.point(x, y)
    gm.hide(bl)
    return gm.rectangle(bl, w, h)

gm.clear()
unit = gm.scalar(48, out="unit")
c = gm.load_colors()
n = 8
w = 1.25
xs = [-4.375 + 1.25 * i for i in range(n)]

with group("s1-blocks"):
    for b in range(2):
        x0 = xs[b * 4] - 0.55
        enc = gm.rectangle(gm.point(x0, 1.5), 4.85, 1.2)
        gm.set_stroke(enc, c.AMBER)

with group("s1-threads"):
    for i in range(n):
        sq = _cell(gm, xs[i] - 0.5, 1.6, 1.0, 1.0)
        gm.set_stroke(sq, c.BLUE)

with group("s1-data"):
    for i in range(n):
        dc = _cell(gm, xs[i] - 0.625, -2.6, w, 1.0)
        gm.set_stroke(dc, c.GRAY)

with group("s1-arrows"):
    for i in range(n):
        pt = gm.point(xs[i], 1.55)
        pb = gm.point(xs[i], -1.55)
        gm.hide(pt)
        gm.hide(pb)
        gm.annotate_arrow(pt, pb)

with group("s1-labels"):
    for i in range(n):
        tl = gm.annotate_text_box(f"t{i}", xs[i], 2.1, 13)
        gm.set_fill(tl, c.BLUE)
    for i in range(n):
        gm.annotate_text_box(f"A[{i}]", xs[i], -2.1, 12)
    gm.set_fill(gm.annotate_text_box("block 0", -2.5, 3.35, 14), c.AMBER)
    gm.set_fill(gm.annotate_text_box("block 1", 2.35, 3.35, 14), c.AMBER)

with group("s1-caption"):
    gm.annotate_text_box(
        "Thread-based (SIMT): thousands of scalar threads run the same "
        "program; each thread handles one data element.",
        0, -4.2, 13, 10, -1,
    )
```

- Threads arrive {grouped into two blocks}(ref:s1-blocks) of four lanes each.
- Each lane is a square: {eight threads}(ref:s1-threads), $t_0$ through $t_7$.
- Beneath them lies {one contiguous array}(ref:s1-data) `A` in memory.
- Here is the whole model: {each thread reads one element}(ref:s1-arrows), lane
  $i$ mapping onto $A[i]$.
- We {label every lane}(ref:s1-labels) and the slot it owns.
- Then read off the {SIMT summary}(ref:s1-caption) at the bottom.

That one-to-one arrow is the thread-based contract. Correctness for the whole
kernel reduces to correctness for a single thread, parameterised by its index.

## 2. Where a thread's index comes from

A thread does not know its position in the global array directly. It knows only
its **local** `threadIdx` inside its block, plus which block it is in. The global
index it should touch is arithmetic the kernel does by hand.

```pygeomatic
gm.clear()
unit = gm.scalar(46, out="unit")
c = gm.load_colors()
n = 8
xs = [-4.375 + 1.25 * i for i in range(n)]
hot = 6

with group("s2-blocks"):
    for b in range(2):
        x0 = xs[b * 4] - 0.625 - 0.05
        enc = gm.rectangle(gm.point(x0, -0.15), 5.1, 1.3)
        gm.set_stroke(enc, c.AMBER)

with group("s2-cells"):
    for i in range(n):
        cell = _cell(gm, xs[i] - 0.625, 0.0, 1.25, 1.0)
        if i == hot:
            gm.set_fill(cell, c.VIOLET)
            gm.set_stroke(cell, c.VIOLET)
        else:
            gm.set_stroke(cell, c.GRAY)

with group("s2-labels"):
    for i in range(n):
        gm.annotate_text_box(str(i), xs[i], 0.5, 14)
    gm.set_fill(gm.annotate_text_box("block 0  (blockIdx = 0)", -2.5, -0.75, 12), c.AMBER)
    gm.set_fill(gm.annotate_text_box("block 1  (blockIdx = 1)", 2.5, -0.75, 12), c.AMBER)

with group("s2-pin"):
    tip = gm.point(xs[hot], 1.05)
    gm.hide(tip)
    gm.annotate_pin(tip, "global index = 6")

with group("s2-derivation"):
    gm.annotate_text_box(
        "global index = blockIdx × blockDim + threadIdx = 1 × 4 + 2 = 6",
        0, -2.7, 15, 11, -1,
    )
    gm.annotate_text_box(
        "blockDim = 4 threads per block; here blockIdx = 1 and threadIdx = 2.",
        0, -3.7, 12, 11, -1,
    )
```

- Two {blocks of four}(ref:s2-blocks), `blockIdx` 0 and 1.
- The {eight lanes}(ref:s2-cells), with lane 6 already picked out in violet.
- Number them {zero through seven}(ref:s2-labels) and mark each block.
- {Pin the target index}(ref:s2-pin) we want to solve for.
- {Do the arithmetic}(ref:s2-derivation): $\text{blockIdx}\cdot\text{blockDim} +
  \text{threadIdx} = 1\cdot 4 + 2 = 6$.

Notice the payoff hiding in that formula: because adjacent `threadIdx` values map
to adjacent global indices, neighbouring lanes touch neighbouring addresses. That
is exactly what lets the hardware **coalesce** their loads into one wide memory
transaction, the single most important performance lever in the thread model.

## 3. From elements to tiles

The tile model keeps the same grid-of-programs shape but changes the *unit of
work*. Instead of one thread per element, one program instance owns a whole
rectangular **tile** and computes all of it. Reasoning moves up one level: from a
scalar to a block.

```pygeomatic
gm.clear()
unit = gm.scalar(44, out="unit")
c = gm.load_colors()

with group("s3-elements"):
    for r in range(6):
        for col in range(6):
            el = _cell(gm, -3 + col, -3 + r, 1.0, 1.0)
            gm.set_stroke(el, c["COLOR-GRAY-DARK"])

with group("s3-tiles"):
    owner = (1, 1)
    for ti in range(3):
        for tj in range(3):
            tile = _cell(gm, -3 + 2 * tj, -3 + 2 * ti, 2.0, 2.0)
            if (ti, tj) == owner:
                gm.set_fill(tile, c.VIOLET)
                gm.set_stroke(tile, c.VIOLET)
            else:
                gm.set_stroke(tile, c.WHITE)

with group("s3-labels"):
    owner = (1, 1)
    for ti in range(3):
        for tj in range(3):
            i_top = 2 - ti
            cx, cy = -2 + 2 * tj, -2 + 2 * ti
            lab = gm.annotate_text_box(f"({i_top},{tj})", cx, cy, 13)
            if (ti, tj) == owner:
                gm.set_fill(lab, c.VIOLET)

with group("s3-caption"):
    tip = gm.point(0, 0)
    gm.hide(tip)
    gm.annotate_text_box(
        "Tile-based model: the matrix is partitioned into tiles; each "
        "program instance owns and computes one whole tile (violet) rather "
        "than a single element.",
        0, -4.4, 13, 11, -1,
    )
    gm.annotate_text_box("one program → one tile", -4.9, 3.6, 13, 3.2, -1)
```

- Start with the {full 6×6 matrix}(ref:s3-elements) of faint elements.
- Overlay the {3×3 tiling}(ref:s3-tiles); the tile this program owns is violet.
- Every tile carries its own {program coordinate}(ref:s3-labels) $(i, j)$.
- That is the whole {shift in thinking}(ref:s3-caption): one program, one tile.

You no longer index individual elements at all. Your program's identity is a tile
coordinate, and the compiler is free to schedule the per-element work inside the
tile however it likes.

## 4. Tiled matmul

Matrix multiply is the canonical tiled kernel, and it shows why tiles are the
natural unit. To produce one output tile $C[i,j]$ you do not need all of $A$ and
$B$: you need only **row-strip $i$** of $A$ and **column-strip $j$** of $B$.

```pygeomatic
gm.clear()
unit = gm.scalar(44, out="unit")
c = gm.load_colors()
Ax0, Ay0 = -3.75, -3.5
Bx0, By0 = 0.75, 0.5
Cx0, Cy0 = 0.75, -3.5
hot_i, hot_j = 1, 1

with group("s4-A"):
    for ti in range(3):
        for tk in range(3):
            tile = _cell(gm, Ax0 + tk, Ay0 + ti, 1.0, 1.0)
            if ti == hot_i:
                gm.set_fill(tile, c.TEAL)
                gm.set_stroke(tile, c.TEAL)
            else:
                gm.set_stroke(tile, c["COLOR-GRAY-MID"])

with group("s4-B"):
    for tk in range(3):
        for tj in range(3):
            tile = _cell(gm, Bx0 + tj, By0 + tk, 1.0, 1.0)
            if tj == hot_j:
                gm.set_fill(tile, c.AMBER)
                gm.set_stroke(tile, c.AMBER)
            else:
                gm.set_stroke(tile, c["COLOR-GRAY-MID"])

with group("s4-C"):
    for ti in range(3):
        for tj in range(3):
            tile = _cell(gm, Cx0 + tj, Cy0 + ti, 1.0, 1.0)
            if (ti, tj) == (hot_i, hot_j):
                gm.set_fill(tile, c.BLUE)
                gm.set_stroke(tile, c.BLUE)
            else:
                gm.set_stroke(tile, c["COLOR-GRAY-MID"])

with group("s4-arrows"):
    a_edge = gm.point(Ax0 + 3.0, Ay0 + hot_i + 0.5)
    c_left = gm.point(Cx0 + hot_j, Cy0 + hot_i + 0.5)
    b_edge = gm.point(Bx0 + hot_j + 0.5, By0)
    c_top = gm.point(Cx0 + hot_j + 0.5, Cy0 + hot_i + 1.0)
    for p in (a_edge, c_left, b_edge, c_top):
        gm.hide(p)
    gm.annotate_arrow(a_edge, c_left, 0.06)
    gm.annotate_arrow(b_edge, c_top, 0.06)

with group("s4-labels"):
    gm.set_fill(gm.annotate_text_box("A  (M × K)", Ax0 + 1.5, Ay0 - 0.45, 13), c.TEAL)
    gm.set_fill(gm.annotate_text_box("B  (K × N)", Bx0 + 1.5, By0 + 3.4, 13), c.AMBER)
    gm.set_fill(gm.annotate_text_box("C  (M × N)", Cx0 + 1.5, Cy0 - 0.45, 13), c.BLUE)
    gm.annotate_text_box("row-strip i", Ax0 + 1.5, Ay0 + hot_i + 0.5, 11)
    gm.annotate_text_box("col-strip j", Bx0 + hot_j + 0.5, By0 + 3.9, 11)

with group("s4-formula"):
    gm.annotate_text_box(
        "Tiled matmul: each output tile C[i,j] accumulates the products of "
        "A's row-strip i and B's column-strip j, summed over all K tiles.",
        -2.35, 2.1, 12, 3.0, -1,
    )
    gm.annotate_text_box("C[i,j] = Σₖ A[i,k] · B[k,j]", -2.35, -0.2, 13, 3.0, -1)
```

- The left operand contributes {A's row-strip}(ref:s4-A) $i$, in teal.
- The top operand contributes {B's column-strip}(ref:s4-B) $j$, in amber.
- Those meet at the {owned output tile}(ref:s4-C) $C[i,j]$, in blue.
- {Stream them in}(ref:s4-arrows): the two strips flow into that one tile.
- Name the {three operands}(ref:s4-labels) $A$, $B$, and $C$.
- And state the {accumulation rule}(ref:s4-formula): $C[i,j] = \sum_k A[i,k]\cdot
  B[k,j]$, summed over the $K$ tiles of the strip.

One program keeps that blue tile in fast memory and marches $k$ across the strip,
accumulating. The tile is the granule the whole algorithm is written in.

## 5. The one-line contrast

Side by side, the difference is a single sentence: in the thread model you reason
about one thread and one element; in the tile model you reason about one program
and one tile.

```pygeomatic
gm.clear()
unit = gm.scalar(46, out="unit")
c = gm.load_colors()
sc = 0.7
Lx, Ly = -4.7, -1.4
Rx, Ry = 1.9, -1.4

with group("s5-divider"):
    d1 = gm.point(0, -3.6)
    d2 = gm.point(0, 3.0)
    gm.hide(d1)
    gm.hide(d2)
    div = gm.line(d1, d2)
    gm.set_stroke(div, c["COLOR-GRAY-DARK"])

with group("s5-left"):
    for r in range(4):
        for col in range(4):
            el = _cell(gm, Lx + sc * col, Ly + sc * r, sc, sc)
            if (r, col) == (2, 1):
                gm.set_fill(el, c.BLUE)
                gm.set_stroke(el, c.BLUE)
            else:
                gm.set_stroke(el, c["COLOR-GRAY-MID"])

with group("s5-right"):
    for r in range(4):
        for col in range(4):
            el = _cell(gm, Rx + sc * col, Ry + sc * r, sc, sc)
            gm.set_stroke(el, c["COLOR-GRAY-MID"])
    tile = _cell(gm, Rx + sc, Ry + sc, 2 * sc, 2 * sc)
    gm.set_fill(tile, c.VIOLET)
    gm.set_stroke(tile, c.VIOLET)

with group("s5-arrows"):
    lp1 = gm.point(Lx + sc * 1.5, 2.0)
    lp2 = gm.point(Lx + sc * 1.5, Ly + sc * 3 + 0.05)
    gm.hide(lp1)
    gm.hide(lp2)
    gm.annotate_arrow(lp1, lp2, 0.05, "1 thread")
    rp1 = gm.point(Rx + sc * 2, 2.0)
    rp2 = gm.point(Rx + sc * 2, Ry + sc * 3 + 0.05)
    gm.hide(rp1)
    gm.hide(rp2)
    gm.annotate_arrow(rp1, rp2, 0.05, "1 program")

with group("s5-labels"):
    gm.set_fill(gm.annotate_text_box("one thread → one element", -3.3, 2.6, 13), c.BLUE)
    gm.set_fill(gm.annotate_text_box("one program → one tile", 3.3, 2.6, 13), c.VIOLET)

with group("s5-caption"):
    gm.annotate_text_box(
        "SIMT: you reason about one scalar thread and one element. "
        "Tile model: you reason about one program that owns a whole tile.",
        0, -4.1, 13, 12, -1,
    )
```

- A {dividing line}(ref:s5-divider) separates the two worlds.
- On the left, under one thread, {a single element}(ref:s5-left) lights up blue.
- On the right, under one program, {a whole 2×2 tile}(ref:s5-right) lights up violet.
- Mark {what you reason about}(ref:s5-arrows) in each panel.
- Name the {two mental models}(ref:s5-labels) explicitly.
- And end on the {takeaway}(ref:s5-caption).

Neither model is a subset of the other in practice: the thread model gives you
fine control and coalescing intuition, while the tile model raises the
abstraction so the compiler can own the scheduling. Knowing which unit you are
reasoning in, the scalar thread or the whole tile, is most of what it takes to
read a modern GPU kernel.
