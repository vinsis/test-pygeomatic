# My Nova article

Press **Preview**, then click and drag your way through this page. 

- Examples of [how to manipulate LaTex](https://www.tinyvolt.com/nova/articles/51e42f3c-3738-44d2-9332-a93b5ea424ea)

---

## 1. Clicking through a drawing

Code in a `pygeomatic` block runs without the reader seeing it. Name a few lines
with `with group(...)` and they become a link your reader can click.

{}(a = \point 3 0)
{}(walk = \line p0 a)
{}(\hide walk)

Reach the point by {}(\highlight walk) {moving a distance}(\show walk) of $3$ units, then {hide the line again}(\hide walk).

---

## 2. Controls your reader can move

A slider is just a number. Put it in a sentence with an f-string, and use
`gm.md` to write that sentence.

{}(\clear)
{}(r = \scalar 3)

Click to draw the circle: {draw it}(c = \circle p0 r)


Drag to resize the circle: <span class="nova-ui" data-kind="slider" data-node="r" data-initial-value='3.0' data-start='1.0' data-stop='5.0' data-step='0.5' data-label='&quot;r&quot;' data-show-value='true'></span>


Anything built from `r` follows on its own:

{}(side = \mul r 2)

{Add a square}(sq = \square p0 side 0) whose side is $2r$. Changing $r$ also changes the side of this square. 

---

## 3. More controls

{}(\clear)
{}(n = \scalar 4)
{}(mode = \text "sum")
{}(pick = \text "left")
{}(name = \text "world")

### 3.1 Number

<span class="nova-ui" data-kind="number" data-node="n" data-initial-value='4.0' data-start='0.0' data-stop='10.0' data-step='0.5' data-label='&quot;Update n&quot;'></span>

- Draw the {point}(p-0 = \point n 0) $(n,0)$. Update `n` above and the point updates reactively.

### 3.2 Dropdown

<span class="nova-ui" data-kind="dropdown" data-node="mode" data-initial-value='&quot;sum&quot;' data-options='[&quot;sum&quot;,&quot;product&quot;]' data-label='&quot;a dropdown&quot;'></span>

- Show {the chosen option}(t = \annotate-text-box mode 0 0 14).

### 3.3 Radio buttons

<span class="nova-ui" data-kind="radio" data-node="pick" data-initial-value='&quot;left&quot;' data-options='[&quot;left&quot;,&quot;right&quot;]' data-label='&quot;radio buttons&quot;'></span>

- Show {the pick}(t = \annotate-text-box pick 0 0 14).

### 3.4 Text

<span class="nova-ui" data-kind="text" data-node="name" data-initial-value='&quot;world&quot;' data-label='&quot;name&quot;' data-placeholder='&quot;type something&quot;'></span>

- Draw a {text annotation}(t = \annotate-text-box name 0 0 12 4 -1) with a fixed width. Updating the text updates the annotation reactively. 

---

## 4. Text that appears on demand

`with when(...)` hides text until a condition is true. The condition is checked
as your reader moves things, so there is nothing to click.

{}(show = \bool 0)
{}(radius = \scalar 3)
{}(choice = \text "sum")

<span class="nova-ui" data-kind="checkbox" data-node="show" data-initial-value='false' data-label='&quot;Show the note&quot;'></span>

<div class="nova-when" data-when='{&quot;node&quot;:&quot;show&quot;}' style="display:none">

You ticked the box, so this appeared.

</div>

<span class="nova-ui" data-kind="slider" data-node="radius" data-initial-value='3.0' data-start='1.0' data-stop='6.0' data-step='0.5' data-label='&quot;radius&quot;' data-show-value='true'></span>

<div class="nova-when" data-when='{&quot;op&quot;:&quot;ge&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;radius&quot;},&quot;b&quot;:{&quot;const&quot;:4.0}}' style="display:none">

**Large:** the radius is 4 or more.

</div>

<div class="nova-when" data-when='{&quot;op&quot;:&quot;lt&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;radius&quot;},&quot;b&quot;:{&quot;const&quot;:4.0}}'>

**Small:** the radius is under 4.

</div>

<span class="nova-ui" data-kind="dropdown" data-node="choice" data-initial-value='&quot;sum&quot;' data-options='[&quot;sum&quot;,&quot;product&quot;]' data-label='&quot;pick one&quot;'></span>

<div class="nova-when" data-when='{&quot;op&quot;:&quot;eq&quot;,&quot;a&quot;:{&quot;node&quot;:&quot;choice&quot;},&quot;b&quot;:{&quot;const&quot;:&quot;product&quot;}}' style="display:none">

The dropdown is on *product*. Conditions read text too.

</div>


---

## 5. LaTeX formulas 

Give a formula an id with a `%id:` line, then attach a number to a slot inside
it. The formula updates whenever that number does.

### 5.1 Binding values to parameters
$$
%id:area
A = \int_{0}^{b} 2\pi x \, dx
$$

{}(\clear)
{}(b = \scalar 3)

Drag the upper limit: <span class="nova-ui" data-kind="slider" data-node="b" data-initial-value='3.0' data-start='1.0' data-stop='5.0' data-step='0.5' data-label='&quot;b&quot;' data-show-value='true'></span>


Watch the $b$ in the formula follow the slider. Or set it from here:
{make it 5}(b = \scalar 5) · {back to 3}(b = \scalar 3)

### 5.2 Highlighting rows of a matrix

You can also paint parts of a matrix, and move the painted part with a number:

$$
%id:M
\begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}
$$

{}(row = \scalar 0)

Row to highlight: <span class="nova-ui" data-kind="slider" data-node="row" data-initial-value='0.0' data-start='0.0' data-stop='2.0' data-step='1.0' data-label='&quot;row&quot;' data-show-value='true'></span>


### 5.3 Highlighting upper triangular entries
You can also highlight upper triangular entries of a matrix as shown below:
 
$$
%id:U
\begin{pmatrix} a & b & c \\ 0 & d & e \\ 0 & 0 & f \end{pmatrix}
$$


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

{}(band = \scalar 1)

Widen the band: <span class="nova-ui" data-kind="slider" data-node="band" data-initial-value='1.0' data-start='0.0' data-stop='4.0' data-step='1.0' data-label='&quot;band&quot;' data-show-value='true'></span>


At 0 only the diagonal is painted; at 4 the whole matrix is.

<!-- texatlas:v1
{"area":{"values":[{"slot":"int.upper","node":"b"}]},"M":{"highlights":[{"selector":{"op":"eq","axis":{"axis":"row"},"value":{"node":"row"}},"color":"#F472B6"}]},"U":{"highlights":[{"selector":{"op":"ge","axis":{"op":"sub","a":{"axis":"col"},"b":{"axis":"row"}},"value":{"const":0}},"color":"#6aa8ff"}]},"B":{"highlights":[{"selector":{"op":"and","a":{"op":"le","axis":{"op":"sub","a":{"axis":"col"},"b":{"axis":"row"}},"value":{"node":"band"}},"b":{"op":"le","axis":{"op":"sub","a":{"axis":"row"},"b":{"axis":"col"}},"value":{"node":"band"}}},"color":"#F472B6"}]}}
-->
