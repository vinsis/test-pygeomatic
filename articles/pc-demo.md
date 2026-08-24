# Click the label

Test

```pygeomatic
w0 = gm.ui.radio([0,1],0,'w0')
w1 = gm.ui.radio([0,1],0,'w1')
w2 = gm.ui.radio([0,1],0,'w2')
sum = gm.add(w0,w1,w2)
text = gm.text(f'sum is {sum}')
gm.md(f'sum is {sum}')
```

{hi}(gm.highlight(gm.p0))


