# Scaling a vector

{}(\clear)
{}(scale = \scalar 1)
{}(x = \mul scale 3)
{}(y = \mul scale 2)
{}(p-0 = \point 3 2)
{}(v0 = \line p0 p-0)
{}(\hide v0)
{}(p-1 = \point x y)
{}(v1 = \line p0 p-1)
{}(p-text = \point -2 2)
{}(\hide p-text)
{}(text-scale = \text "Scale: ${scale}")
{}(tbox = \annotate-text-box text-scale p-text.x p-text.y 12)

Scaling a vector only changes its norm, not its direction. E.g. given a
{vector}(\highlight v0) $\begin{bmatrix} 3 \\ 2 \end{bmatrix}$ one can modify
the scale and watch the magnitude change:

- {Increase scale}(\animate scale 2)
- {Decrease scale}(\animate scale -2)
- {Set `scale = 0.5`}(scale = \scalar 0.5)

A function to compute the norm looks like this (regular code fences are left
untouched):

```python
def norm(array):
    return sum(i**2 for i in array)**0.5
```

Math regions are never scanned for spans: $\theta = \tan^{-1}(y / x)$ and
$\text{add}(\blue{v_1}, \pink{v_2})$ come through verbatim.
