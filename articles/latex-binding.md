One can assign an `id` to a latex string using the format `[#id] <latex string>`:

$$
%id:integral
\int_a^b x^2 \, dx$$

Then in the `pygeomatic` code fence, one can bind a variable to the latex:

```pygeomatic
b = gm.scalar(3)
integral = gm.tex("integral")
integral.int.upper.bind(b)
```

Update the value of {b = 5}(b = gm.scalar(5))

---

$$
% id:M
\begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}$$

```pygeomatic
r = gm.scalar(0)
c = gm.scalar(0)
M = gm.tex("M")
M.highlight(rows == r)
```

Move it: {row 1}(r = gm.scalar(1)) · {row 2}(r = gm.scalar(2))

---

```pygeomatic
gm.clear()
r = gm.scalar(0)
c = gm.scalar(0)
M = gm.tex("M")
M[r:, c:].highlight(color=blue)
```

