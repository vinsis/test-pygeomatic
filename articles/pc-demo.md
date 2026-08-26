# My Nova article

Press **Preview**, then click and drag your way through this page. 

- Examples of [how to manipulate LaTex](https://www.tinyvolt.com/nova/articles/51e42f3c-3738-44d2-9332-a93b5ea424ea)

---

## 1. Clicking through a drawing

Code in a `pygeomatic` block runs without the reader seeing it. Name a few lines
with `with group(...)` and they become a link your reader can click.

```pygeomatic
origin = gm.p0
a = gm.point(3, 0)
walk = gm.line(origin, a)
gm.hide(walk)

with group("walk-x"):
    gm.highlight(walk)
    gm.show(walk)
```

Reach the point by {moving a distance}(ref:walk-x) of $3$ units, then {hide the line again}(gm.hide(walk)).

---

## 2. Controls your reader can move

A slider is just a number. Put it in a sentence with an f-string, and use
`gm.md` to write that sentence.

```pygeomatic
gm.clear()
r = gm.ui.slider(1, 5, step=0.5, value=3, label="r")

with group("draw"):
    c = gm.circle(gm.p0, r)
```

Click to draw the circle: {draw it}(ref:draw)

```pygeomatic
gm.md(f"Drag to resize the circle: {r}")
```

Anything built from `r` follows on its own:

```pygeomatic
side = r * 2

with group("square"):
    sq = gm.square(gm.p0, side, 0)
```

{Add a square}(ref:square) whose side is $2r$. Changing $r$ also changes the side of this square. 

---

## 3. More controls

```pygeomatic
gm.clear()
n = gm.ui.number(0, 10, value=4, step=0.5, label="Update n")
mode = gm.ui.dropdown(["sum", "product"], label="a dropdown")
pick = gm.ui.radio(["left", "right"], label="radio buttons")
name = gm.ui.text("world", label="name", placeholder="type something")
```

### 3.1 Number
```pygeomatic
gm.md(f"{n}")
```
- Draw the {point}(gm.point(n,0)) $(n,0)$. Update `n` above and the point updates reactively.

### 3.2 Dropdown
```pygeomatic
gm.md(f"{mode}")
```
- Show {the chosen option}(t = gm.annotate_text_box(mode, 0, 0, 14)).

### 3.3 Radio buttons
```pygeomatic
gm.md(f"{pick}")
```
- Show {the pick}(t = gm.annotate_text_box(pick, 0, 0, 14)).

### 3.4 Text
```pygeomatic
gm.md(f"{name}")
```
- Draw a {text annotation}(t = gm.annotate_text_box(name, 0, 0, 12, 4, -1)) with a fixed width. Updating the text updates the annotation reactively. 

---

## 4. Text that appears on demand

`with when(...)` hides text until a condition is true. The condition is checked
as your reader moves things, so there is nothing to click.

```pygeomatic
show = gm.ui.checkbox(False, label="Show the note")
radius = gm.ui.slider(1, 6, 0.5, 3, label="radius")
gm.md(f"{show}")

with when(show):
    gm.md("You ticked the box, so this appeared.")

gm.md(f"{radius}")

with when(gm.cond.ge(radius, 4)):
    gm.md("**Large:** the radius is 4 or more.")
with when(gm.cond.lt(radius, 4)):
    gm.md("**Small:** the radius is under 4.")

choice = gm.ui.dropdown(["sum", "product"], label="pick one")
gm.md(f"{choice}")

with when(gm.cond.eq(choice, "product")):
    gm.md("The dropdown is on *product*. Conditions read text too.")
```

---

## 5. LaTeX formulas 

Give a formula an id with a `%id:` line, then attach a number to a slot inside
it. The formula updates whenever that number does.

### 5.1 Binding values to parameters
$$
%id:area
A = \int_{0}^{b} 2\pi x \, dx
$$

```pygeomatic
gm.clear()
b = gm.ui.slider(1, 5, step=0.5, value=3, label="b")
area = gm.tex("area")
area.int.upper.bind(b)
gm.md(f"Drag the upper limit: {b}")
```

Watch the $b$ in the formula follow the slider. Or set it from here:
{make it 5}(b = gm.scalar(5)) · {back to 3}(b = gm.scalar(3))

### 5.2 Highlighting rows of a matrix

You can also paint parts of a matrix, and move the painted part with a number:

$$
%id:M
\begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}
$$

```pygeomatic
row = gm.ui.slider(0, 2, step=1, value=0, label="row")
M = gm.tex("M")
M.highlight(M.rows() == row, color="pink")
gm.md(f"Row to highlight: {row}")
```

### 5.3 Highlighting upper triangular entries
You can also highlight upper triangular entries of a matrix as shown below:
 
$$
%id:U
\begin{pmatrix} a & b & c \\ 0 & d & e \\ 0 & 0 & f \end{pmatrix}
$$

```pygeomatic
U = gm.tex("U")
U.triu().highlight(color="blue")
```

### 5.4 Sophisticated highlighting patterns
Conditions can do more than pick a row. `M.rows()` and `M.cols()` are the row and
column number of each cell, so you can do arithmetic on them and join the results
with `&` and `|`. Each cell is painted only where the condition holds.

The slider below sets how far from the diagonal a cell may sit. The two
conditions are `cols - rows <= band` and `rows - cols <= band`, which together
mean "no further than `band` steps either side of the diagonal":

$$
%id:B
\begin{pmatrix}
a & b & c & d & e \\
f & g & h & i & j \\
k & l & m & n & o \\
p & q & r & s & t \\
u & v & w & x & y
\end{pmatrix}
$$

```pygeomatic
band = gm.ui.slider(0, 4, step=1, value=1, label="band")
B = gm.tex("B")
B.highlight((B.cols() - B.rows() <= band) & (B.rows() - B.cols() <= band), color="pink")
gm.md(f"Widen the band: {band}")
```

At 0 only the diagonal is painted; at 4 the whole matrix is.
