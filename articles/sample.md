# pygeomatic by example

A tour of the features. Every symbol here is a placeholder ($x$, $a$, $\theta$,
$k$) and the scene is deliberately empty of meaning: a point, a segment, a
circle. Each bullet states one thing pygeomatic can do and demonstrates it with
the smallest live example that shows the mechanism.

```pygeomatic
c = gm.load_colors()            # palette as referenceable Text nodes
unit = gm.scalar(60)            # system node: pixels per world unit

k = gm.scalar(1)                # the driving scalar everything reads from
x = gm.mul(k, 2)                # one gm. call = exactly one DSL line
sum_ab = gm.add(k, 1)           # underscores in the name become dashes: sum-ab

pt = gm.point(x, 1)
seg = gm.line(gm.p0, pt)
circ = gm.circle(gm.p0, k)
caption = gm.text("k = ${k}")   # plain text, reactive interpolation
gm.hide(pt)
gm.hide(seg)
gm.hide(circ)

with group("show-point"):
    gm.set_fill(pt, c.BLUE)
    gm.show(pt)

with group("show-seg"):
    gm.set_stroke(seg, c.TEAL)
    gm.show(seg)

with group("show-circ"):
    gm.set_stroke(circ, c.GRAY)
    gm.show(circ)

with group("label"):
    lbl = gm.annotate_text_box(caption, -3, 3, 16)
    gm.set_fill(lbl, c.AMBER)

with group("mark"):
    gm.annotate_pin(pt, "x")
    gm.highlight(pt)

with group("track"):
    gm.trail(pt)
```

## The `pygeomatic` fence

- **Python that computes and records.** A fence runs real Python at compile time;
  each `gm.` call both evaluates numerically and appends one DSL line, so the
  reader receives deterministic command text, never Python.
- **One call, one line.** `gm.mul(k, 2)` is exactly `x = \mul k 2`. Repetition is
  ordinary Python loops, not a batch API.
- **Arithmetic.** Infix works on Scalar / Complex / Array nodes (`k + 1` records
  `\add`), but the explicit form is always available and is the only option
  elsewhere: `gm.add(a, b)`, `gm.pow_(a, 2)`, and nothing infix on a Point or a
  Circle. In-place `+=` raises, so assign a new name instead.
- **Id grammar.** Letters, digits and dashes only. An assignment target becomes
  the id with underscores turned into dashes, so `sum_ab` above is the node
  `sum-ab`; `out="my-id"` overrides, and engine shapes like `num0` are refused.
- **System nodes** are already there: `gm.p0` is the origin used above as the
  segment's first endpoint, `unit` is the zoom, `gm.T` / `gm.F` are the literals.
- **Colors** come from `gm.load_colors()` once, then `c.BLUE`, `c.AMBER`, or
  `c["COLOR-TEAL-LIGHT"]` for the hyphenated ramp names.

## Reveal beats

A `with group(...)` block is one beat; a prose span whose target is `ref:name`
reveals it, so the figure builds click by click and each click lands on a fully
set-up scene.

- {A point}(ref:show-point) at $(x, 1)$.
- {A segment}(ref:show-seg) from the origin to it.
- {A circle}(ref:show-circ) of radius $k$.
- An {annotation box}(ref:label) whose text reads its value back live.
- A {pin and a highlight}(ref:mark) on the point.
- A {trail}(ref:track) that records where the point has been.

Objects built as scaffolding are hidden at build time with `gm.hide` and brought
back with `gm.show` inside the beat that earns them.

## Inline CommandLinks and reactivity

An inline span carrying a Python statement is a one-off command the reader
triggers.
Article mode is last-write-wins, so re-binding a name is a reassignment.

- Reassign: {set $k$ to $2$}(k = gm.scalar(2)) or {back to $1$}(k = gm.scalar(1)).
- Animate: {sweep $k$}(gm.animate(k, 3)) walks the value there frame by frame.
- Everything downstream recomputes on its own: $x = 2k$ moves the point, the
  circle radius follows $k$, and the caption interpolates `${k}` in place.
- Any command works inline, not only assignment: {nudge it}(gm.translate(pt, 1, 0)).

## texatlas: value binding

Give a `$$…$$` formula an id with a `%id:` first line, take a handle with
`gm.tex(...)`, and bind a store node into a slot. Binding replaces content, so
every slot needs a placeholder symbol already written in the LaTeX.

$$
%id:sum-demo
\sum_{i=1}^{n} a_i
$$

```pygeomatic
s = gm.tex("sum-demo")
s.sum.upper.bind(k, fmt="d")      # the n slot now shows k's live value
s.sum.lower.bind(k, show="symbol")
```

- Legal families and slots come from the `SCHEMA` in `src/pygeomatic/tex.py`:
  `int` / `sum` / `prod` take `lower`, `upper`, `body`; `frac` takes `num` and
  `denom`; `sqrt` takes `body`; `underbrace` / `overbrace` are reveal-only.
- `fmt` is `".2f"` or `"d"`, or omit it to trim to four decimals, and
  `show="symbol"` links a slot without substituting its glyph.
- Driving the bound node drives the formula: {upper limit $3$}(k = gm.scalar(3)).

## texatlas: matrix highlight

A highlight is a predicate over a cell's grid position, never its content.

$$
%id:mat-demo
\begin{pmatrix} a & b \\ c & d \end{pmatrix}
$$

```pygeomatic
r = gm.scalar(0)
gate = gm.scalar(0, out="gate")
M = gm.tex("mat-demo")
M.highlight(gm.rows == r, color="pink")          # reactive: r drives the row
M.diag().highlight(color="teal")                 # also .triu() / .tril()
M[:, 1:].highlight(color="lime")                 # numpy-style region
M.triu(1).scale(gate).highlight(color="amber")   # gated: shows once gate = 1
```

- Axes are `gm.rows`, `gm.cols`, `gm.dim(i)`; compare them with `==`, `<`, `>`
  against an int or a node, and combine regions with `&` and `|`.
- Move the row band: {row $0$}(r = gm.scalar(0)) or {row $1$}(r = gm.scalar(1)).
- Open the gated highlight: {turn it on}(gate = gm.scalar(1)).
- `matrix=N` picks the $N$-th matrix in a multi-matrix formula, counting real
  matrices in source order and skipping `aligned`-style layout blocks.

## texatlas: reveal

The same selector machine, painting opacity instead of color. Use strict `<` so
the gate counts how many parts show and $0$ shows nothing.

$$
%id:deriv-demo
\begin{aligned}
x &= a \\
x &= a + b \\
x &= \theta
\end{aligned}
$$

$$
%id:brace-demo
\underbrace{a + b}_{\text{label}}
$$

```pygeomatic
n = gm.scalar(0, out="n")
flag = gm.bool_(False)
d = gm.tex("deriv-demo")
d.rows().reveal(gm.rows < n)      # n lines shown; n = 0 shows none
br = gm.tex("brace-demo")
br.underbrace.reveal(flag)        # brace glyph and label, all or nothing
M.reveal(M.cols() < n)            # matrix columns fade in on the same gate
```

- Step the derivation: {one line}(n = gm.scalar(1)), {two}(n = gm.scalar(2)),
  {three}(n = gm.scalar(3)), or {play it}(gm.animate(n, 3)).
- Flip the brace on: {show the brace}(flag = gm.bool_(True)).
- Matrix reveal is fade-only; `mode="collapse"` is available for lines and slots
  but would break a grid.

## Compiling

Bindings never become DSL: they ride a separate channel and land in a trailing
`<!-- texatlas:v1 … -->` comment. The compiler replays the whole document
through `parse_dsl` in order, so a broken article fails the build, not the
reader:

```sh
uv run python scripts/compile_article.py examples/agent-output.md
```
