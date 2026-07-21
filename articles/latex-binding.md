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
M.highlight(gm.rows == r)
```

Move it: {row 1}(r = gm.scalar(1)) · {row 2}(r = gm.scalar(2))

---

```pygeomatic
gm.clear()
r1 = gm.scalar(0)
c1 = gm.scalar(0)
M = gm.tex("M")
M[r1:, c1:].highlight(color="blue")
```

---


$$
% id: matmul
A = \begin{pmatrix} a & b & c & d \\ c & d & e & f \end{pmatrix}
\quad
B = \begin{bmatrix} e & f & 1 & 2 \\ g & h & 3 & 4 \end{bmatrix}
$$

```pygeomatic
f = gm.tex("matmul")
r2 = gm.scalar(0)
c3 = gm.scalar(2)
r3 = gm.scalar(1)
f.highlight(gm.rows == r2)
f.highlight(gm.rows == r3, matrix = 1)
f[:, :c3].highlight(color="blue", matrix = 1)
```

- Set {r2 = 1}(r2 = gm.scalar(1))

