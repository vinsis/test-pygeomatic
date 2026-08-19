# Click the label

Test

```pygeomatic
c = gm.circle(gm.p0, 1)
label = gm.annotate_text_box("click me", 2, 2, 18)
```

Let us highlight the {circle}(gm.highlight(c)) so that all previous commands run. 
- this is a bullet point. is it being rendered?

```pygeomatic
with gm.ui.onclick(label):
    p = gm.point(2, 2)
    d = gm.distance(p, gm.p0)
    outside = gm.gt(d, 1)

with gm.when(outside):
    gm.md("The point is outside the circle.")
```

The circle is drawn. Now click **click me** on the canvas.
