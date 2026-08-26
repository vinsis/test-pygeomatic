# pygeomatic by example

A tour of the features. Every symbol here is a placeholder ($x$, $a$, $\theta$,
$k$) and the scene is deliberately empty of meaning: a point, a segment, a
circle. Each bullet states one thing pygeomatic can do and demonstrates it with
the smallest live example that shows the mechanism.

{}(\load-colors)
{}(unit = \scalar 60)
{}(k = \scalar 1)
{}(x = \mul k 2)
{}(sum-ab = \add k 1)
{}(pt = \point x 1)
{}(seg = \line p0 pt)
{}(circ = \circle p0 k)
{}(caption = \text "k = ${k}")
{}(\hide pt)
{}(\hide seg)
{}(\hide circ)

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

- {}(\set-fill pt COLOR-BLUE) {A point}(\show pt) at $(x, 1)$.
- {}(\set-stroke seg COLOR-TEAL) {A segment}(\show seg) from the origin to it.
- {}(\set-stroke circ COLOR-GRAY) {A circle}(\show circ) of radius $k$.
- An {}(lbl = \annotate-text-box caption -3 3 16) {annotation box}(\set-fill lbl COLOR-AMBER) whose text reads its value back live.
- A {}(text-0 = \text "x") {}(pin-0 = \annotate-pin pt text-0) {pin and a highlight}(\highlight pt) on the point.
- A {trail}(\trail pt) that records where the point has been.

Objects built as scaffolding are hidden at build time with `gm.hide` and brought
back with `gm.show` inside the beat that earns them.

## Inline CommandLinks and reactivity

An inline span carrying a Python statement is a one-off command the reader
triggers.
Article mode is last-write-wins, so re-binding a name is a reassignment.

- Reassign: {set $k$ to $2$}(k = \scalar 2) or {back to $1$}(k = \scalar 1).
- Animate: {sweep $k$}(\animate k 3) walks the value there frame by frame.
- Everything downstream recomputes on its own: $x = 2k$ moves the point, the
  circle radius follows $k$, and the caption interpolates `${k}` in place.
- Any command works inline, not only assignment: {nudge it}(\translate pt 1 0).

## texatlas: value binding

Give a `$$…$$` formula an id with a `%id:` first line, take a handle with
`gm.tex(...)`, and bind a store node into a slot. Binding replaces content, so
every slot needs a placeholder symbol already written in the LaTeX.

$$
%id:sum-demo
\sum_{i=1}^{n} a_i
$$

{}(\clear)

- Legal families and slots come from the `SCHEMA` in `src/pygeomatic/tex.py`:
  `int` / `sum` / `prod` take `lower`, `upper`, `body`; `frac` takes `num` and
  `denom`; `sqrt` takes `body`; `underbrace` / `overbrace` are reveal-only.
- `fmt` is `".2f"` or `"d"`, or omit it to trim to four decimals, and
  `show="symbol"` links a slot without substituting its glyph.
- Driving the bound node drives the formula: {upper limit $3$}(k = \scalar 3).

## texatlas: matrix highlight

A highlight is a predicate over a cell's grid position, never its content.

$$
%id:mat-demo
\begin{pmatrix} a & b \\ c & d \end{pmatrix}
$$

{}(\clear)
{}(r = \scalar 0)
{}(gate = \scalar 0)

- Axes are `gm.rows`, `gm.cols`, `gm.dim(i)`; compare them with `==`, `<`, `>`
  against an int or a node, and combine regions with `&` and `|`.
- Move the row band: {row $0$}(r = \scalar 0) or {row $1$}(r = \scalar 1).
- Open the gated highlight: {turn it on}(gate = \scalar 1).
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

{}(n = \scalar 0)
{}(flag = \bool 0)

- Step the derivation: {one line}(n = \scalar 1), {two}(n = \scalar 2),
  {three}(n = \scalar 3), or {play it}(\animate n 3).
- Flip the brace on: {show the brace}(flag = \bool 1).
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

<!-- texatlas:v1
{"sum-demo":{"values":[{"slot":"sum.upper","node":"k","fmt":"d"},{"slot":"sum.lower","node":"k","show":"symbol"}]},"mat-demo":{"highlights":[{"selector":{"op":"eq","axis":{"axis":"row"},"value":{"node":"r"}},"color":"#F472B6"},{"selector":{"op":"eq","axis":{"op":"sub","a":{"axis":"col"},"b":{"axis":"row"}},"value":{"const":0}},"color":"#14B8A6"},{"selector":{"op":"ge","axis":{"axis":"col"},"value":{"const":1}},"color":"#84CC16"},{"selector":{"op":"scale","sel":{"op":"ge","axis":{"op":"sub","a":{"axis":"col"},"b":{"axis":"row"}},"value":{"const":1}},"by":{"node":"gate"}},"color":"#F59E0B"}]},"deriv-demo":{"reveals":[{"align":0,"selector":{"op":"lt","axis":{"axis":"row"},"value":{"node":"n"}}}]},"brace-demo":{"reveals":[{"slot":"underbrace","selector":{"node":"flag"}}]}}
-->
