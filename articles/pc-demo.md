# Vector Field I: Gradient of a Scalar Function

A **vector field** can be derived from a scalar function by computing its
gradient. In this example, we define a scalar function:

**$f(x, y) = ax^2 - y^2$**

$$\begin{aligned}
\nabla f &= \left( \blue{\frac{\partial f}{\partial x}}, \pink{\frac{\partial f}{\partial y}} \right) \\
&= \left( \blue{2ax}, \pink{-2y} \right)
\end{aligned}$$

Let's visualize this vector field and see how changing the parameter $a$
affects it.

```pygeomatic
a = gm.scalar(1)
p = gm.point(0, 0)
fxy = a * p.x * p.x - p.y * p.y
```

---

### Visualizing the vector field

Now we can visualize the vector field using `gm.vector_field`, which
automatically computes the gradient at each point:
- {Draw vector field}(gm.vector_field(p, fxy))

---

### Reactivity

Try changing the value of `a` to see how the vector field transforms.

Set {a = -2}(a = gm.scalar(-2)), or {a = 2}(a = gm.scalar(2)).
