# Click the label

Test

```pygeomatic
with gm.onpageload():
  txt = gm.annotate_text_box('enlarge', 0, 6, 12, -1)
  r = gm.ui.slider(1,10,0.5,2,'radius')
  c = gm.circle(gm.p0, r)
  with gm.ui.onclick(txt):
    r = gm.scalar(3)
```

Let us highlight the {circle}(gm.highlight(c)) so that all previous commands run. 
- this is a bullet point. is it being rendered?

