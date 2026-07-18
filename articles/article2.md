# Dot product

```pygeomatic
a = gm.la_vec2d(3, 2)          # \la-vec2d — dashes become underscores
b = gm.la_vec2d(-1, 4)
arr_a = gm.array(3, 2)
arr_b = gm.array(-1, 4)

with group("show-dot"):
    d = gm.la_dot(arr_a, arr_b)   # \la-dot from the manifest
```

Two vectors {`a` and `b`}(gm.highlight(a, b)) have a {dot product}(ref:show-dot) of $3(-1) + 2(4) = 5$.
