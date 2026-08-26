One can assign an `id` to a latex string using the format `[#id] <latex string>`:

$$
%id:integral
\int_a^b x^2 \, dx$$

Then in the `pygeomatic` code fence, one can bind a variable to the latex:

{}(b = \scalar 3)

Update the value of {b = 5}(b = \scalar 5)

---

$$
% id:M
\begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}$$

{}(r = \scalar 0)
{}(c = \scalar 0)

Move it: {row 1}(r = \scalar 1) · {row 2}(r = \scalar 2)

---

{}(\clear)
{}(r1 = \scalar 0)
{}(c1 = \scalar 0)

---


$$
% id: matmul
A = \begin{pmatrix} a & b & c & d \\ c & d & e & f \end{pmatrix}
\quad
B = \begin{bmatrix} e & f & 1 & 2 \\ g & h & 3 & 4 \end{bmatrix}
$$

{}(r2 = \scalar 0)
{}(c3 = \scalar 2)
{}(r3 = \scalar 1)

- Set {r2 = 1}(r2 = \scalar 1)


---

$$
%id:pyth
a^2 + b^2 = \underbrace{c^2}_{\text{hypotenuse}}
$$

{}(\clear)
{}(b = \bool 0)
Click to reveal: {the hypotenuse}(b = \bool 1)

---

$$
%id:deriv
\begin{aligned}
f(x) &= (x+1)^2 \\
     &= x^2 + 2x + 1 \\
     &= x(x+2) + 1
\end{aligned}
$$

{}(\clear)
{}(k = \scalar 0)
Step: {line 1}(k = \scalar 1) · {line 2}(k = \scalar 2) · {line 3}(k = \scalar 3)

---

$$
%id:mat
M = \begin{pmatrix} a & b & c \\ d & e & f \\ g & h & i \end{pmatrix}
$$

{}(\clear)
{}(k = \scalar 0)

Build it up: {1 col}(k = \scalar 1) · {2 cols}(k = \scalar 2) · {3 cols}(k = \scalar 3)

<!-- texatlas:v1
{"integral":{"values":[{"slot":"int.upper","node":"b"}]},"M":{"highlights":[{"selector":{"op":"eq","axis":{"axis":"row"},"value":{"node":"r"}},"color":"#f0e080"},{"selector":{"op":"and","a":{"op":"ge","axis":{"axis":"row"},"value":{"node":"r1"}},"b":{"op":"ge","axis":{"axis":"col"},"value":{"node":"c1"}}},"color":"#6aa8ff"}]},"matmul":{"highlights":[{"selector":{"op":"eq","axis":{"axis":"row"},"value":{"node":"r2"}},"color":"#f0e080"},{"selector":{"op":"eq","axis":{"axis":"row"},"value":{"node":"r3"}},"color":"#f0e080","matrix":1},{"selector":{"op":"lt","axis":{"axis":"col"},"value":{"node":"c3"}},"color":"#6aa8ff","matrix":1}]},"pyth":{"reveals":[{"slot":"underbrace","selector":{"node":"b"}}]},"deriv":{"reveals":[{"align":0,"selector":{"op":"lt","axis":{"axis":"row"},"value":{"node":"k"}}}]},"mat":{"reveals":[{"matrix":0,"selector":{"op":"lt","axis":{"axis":"col"},"value":{"node":"k"}}}]}}
-->
