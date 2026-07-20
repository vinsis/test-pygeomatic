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