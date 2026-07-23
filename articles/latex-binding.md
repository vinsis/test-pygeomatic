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


---

$$
%id:pyth
a^2 + b^2 = \underbrace{c^2}_{\text{hypotenuse}}
$$

```python
gm.clear()
b = gm.bool_(False)             # false gate
t = gm.tex("pyth")
t.underbrace.reveal(b)          # brace + "hypotenuse" hidden until b; c^2 stays
```

Click to reveal: {the hypotenuse}(\set-bool b true)

---

$$
%id:deriv
\begin{aligned}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1 \\
     &= x(x+2) + 1
\end{aligned}
$$

```python
gm.clear()
k = gm.scalar(0)
d = gm.tex("deriv")
d.rows().reveal(rows < k)        # k = number of lines shown; k = 0 shows nothing
```

Step: {line 1}(k = \scalar 1) · {line 2}(k = \scalar 2) · {line 3}(k = \scalar 3)

---

$$
%id:mat
M = \begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}
$$

```python
gm.clear()
k = gm.scalar(0)
M = gm.tex("mat")
M.reveal(M.cols() < k)           # k = number of columns shown; col j shows when k > j
```

Build it up: {1 col}(k = \scalar 1) · {2 cols}(k = \scalar 2) · {3 cols}(k = \scalar 3)

```
M.reveal(M.rows() < k)                   # row by row; {Fill}(\animate k 3)
M.reveal((M.rows() == 1) & b)            # a single row, gated by a bool
M.reveal((cols - rows) <= k)             # diagonal wavefront (grow the upper triangle)
M.reveal(M.cols() < k, matrix=1)         # the 2nd matrix in a multi-matrix formula
```