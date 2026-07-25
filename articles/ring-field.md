# $\mathbb{Z}/n\mathbb{Z}$ is a field exactly when $n$ is prime

Fix $n$ and wrap the integers onto a dial with $n$ notches. Every integer lands on
its remainder, and the ring $\mathbb{Z}/n\mathbb{Z}$ *is* that dial: addition
turns the hand, and multiplication by $k$ is stepping $k$ notches at a time. The
whole field question is visible in those steps.

```pygeomatic
gm.clear()

# Residue k of n sits at angle 90 - 360k/n degrees: 0 at the top, k clockwise.
# n = 6: ring radius 2.5, label ring 3.08, inner ring 1.75.
SIX = [(0.0, 2.5), (2.1651, 1.25), (2.1651, -1.25),
       (0.0, -2.5), (-2.1651, -1.25), (-2.1651, 1.25)]
SIX_LAB = [(0.0, 3.08), (2.6674, 1.54), (2.6674, -1.54),
           (0.0, -3.08), (-2.6674, -1.54), (-2.6674, 1.54)]
SIX_IN = [(0.0, 1.75), (1.5155, 0.875), (1.5155, -0.875),
          (0.0, -1.75), (-1.5155, -0.875), (-1.5155, 0.875)]

unit = gm.scalar(50, out="unit")
grid_opacity = gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

with group("six-dial"):
    dial6 = gm.circle(gm.p0, 2.5)
    gm.set_stroke(dial6, c["COLOR-GRAY-MID"])

with group("six-residues"):
    # The residue points are scaffolding for the arrows: hidden, so the dial
    # reads as a ring plus numbers with no node ids drawn over the geometry.
    six0 = gm.point(SIX[0][0], SIX[0][1], out="six-res-0")
    six1 = gm.point(SIX[1][0], SIX[1][1], out="six-res-1")
    six2 = gm.point(SIX[2][0], SIX[2][1], out="six-res-2")
    six3 = gm.point(SIX[3][0], SIX[3][1], out="six-res-3")
    six4 = gm.point(SIX[4][0], SIX[4][1], out="six-res-4")
    six5 = gm.point(SIX[5][0], SIX[5][1], out="six-res-5")
    gm.hide(six0, six1, six2, six3, six4, six5)

    lab60 = gm.annotate_text_box("0", SIX_LAB[0][0], SIX_LAB[0][1], 17, out="six-lab-0")
    lab61 = gm.annotate_text_box("1", SIX_LAB[1][0], SIX_LAB[1][1], 17, out="six-lab-1")
    lab62 = gm.annotate_text_box("2", SIX_LAB[2][0], SIX_LAB[2][1], 17, out="six-lab-2")
    lab63 = gm.annotate_text_box("3", SIX_LAB[3][0], SIX_LAB[3][1], 17, out="six-lab-3")
    lab64 = gm.annotate_text_box("4", SIX_LAB[4][0], SIX_LAB[4][1], 17, out="six-lab-4")
    lab65 = gm.annotate_text_box("5", SIX_LAB[5][0], SIX_LAB[5][1], 17, out="six-lab-5")
    gm.set_fill(lab60, c.WHITE)
    gm.set_fill(lab61, c.AMBER)
    gm.set_fill(lab62, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab63, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab64, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab65, c["COLOR-GRAY-LIGHT"])

    title6 = gm.annotate_text_box("Z/6Z", 0, 0, 19)
    gm.set_fill(title6, c.WHITE)

with group("six-add-hands"):
    hand6from = gm.annotate_arrow(gm.p0, six4, 0.04, out="six-hand-from")
    hand6to = gm.annotate_arrow(gm.p0, six1, 0.04, out="six-hand-to")
    gm.set_stroke(hand6from, c.BLUE)
    gm.set_stroke(hand6to, c.VIOLET)

with group("six-add-arc"):
    ctrl6add = gm.point(-1.75, 3.031, out="six-add-control")
    gm.hide(ctrl6add)
    inner64 = gm.point(SIX_IN[4][0], SIX_IN[4][1], out="six-inner-4")
    inner61 = gm.point(SIX_IN[1][0], SIX_IN[1][1], out="six-inner-1")
    gm.hide(inner64, inner61)
    add6 = gm.annotate_curved_arrow(inner64, inner61, ctrl6add, 0.06, "+3 ↻", out="six-add-arc")
    gm.set_stroke(add6, c.BLUE)

    cap6add = gm.annotate_text_box(
        "Addition is rotation. Adding 3 turns the hand 3 notches clockwise "
        "(3 × 60°): from 4 it passes 5 and 0 and lands on 1, so 4 + 3 ≡ 1 "
        "(mod 6).",
        -4.1, 4.3, 13, 3.5, -1, out="six-cap-add",
    )
    gm.set_fill(cap6add, c.BLUE)

with group("six-orbit-five"):
    orb5a = gm.annotate_arrow(six5, six4, 0.1, out="six-orbit-five-a")
    orb5b = gm.annotate_arrow(six4, six3, 0.1, out="six-orbit-five-b")
    orb5c = gm.annotate_arrow(six3, six2, 0.1, out="six-orbit-five-c")
    orb5d = gm.annotate_arrow(six2, six1, 0.1, out="six-orbit-five-d")
    gm.set_stroke(orb5a, c.EMERALD)
    gm.set_stroke(orb5b, c.EMERALD)
    gm.set_stroke(orb5c, c.EMERALD)
    gm.set_stroke(orb5d, c.EMERALD)
    gm.set_fill(lab65, c.EMERALD)

with group("six-five-inverse"):
    hit6one = gm.point(4.5, 2.2, out="six-hit-one")
    gm.hide(hit6one)
    lead6one = gm.annotate_leader_line(six1, hit6one, "5 · 5 = 25 ≡ 1", out="six-lead-one")
    gm.set_stroke(lead6one, c.EMERALD)

    cap6unit = gm.annotate_text_box(
        "Multiples of 5: 5, 10 ≡ 4, 15 ≡ 3, 20 ≡ 2, 25 ≡ 1. Stepping by 5 "
        "sweeps every residue and reaches 1, so 5 is a unit with 5⁻¹ = 5.",
        4.1, 4.3, 13, 3.5, -1, out="six-cap-unit",
    )
    gm.set_fill(cap6unit, c.EMERALD)

with group("six-orbit-two"):
    orb2a = gm.annotate_arrow(six2, six4, 0.08, out="six-orbit-two-a")
    orb2b = gm.annotate_arrow(six4, six0, 0.08, out="six-orbit-two-b")
    orb2c = gm.annotate_arrow(six0, six2, 0.08, out="six-orbit-two-c")
    gm.set_stroke(orb2a, c.RED)
    gm.set_stroke(orb2b, c.RED)
    gm.set_stroke(orb2c, c.RED)
    gm.set_fill(lab62, c.RED)
    gm.set_fill(lab63, c.RED)

with group("six-zero-divisor"):
    zdiv6 = gm.annotate_curly_bracket(six3, six2, "2 · 3 ≡ 0", out="six-zero-divisor")
    gm.set_stroke(zdiv6, c.RED)

    cap6nonunit = gm.annotate_text_box(
        "Multiples of 2: 2, 4, 6 ≡ 0, and then it repeats. The orbit closes on "
        "{0, 2, 4} and never touches 1, so 2 has no inverse. Worse, 2 · 3 ≡ 0 "
        "with neither factor 0: a zero divisor, straight from 6 = 2 × 3.",
        4.1, -4.3, 13, 3.5, -1, out="six-cap-nonunit",
    )
    gm.set_fill(cap6nonunit, c.RED)

with group("six-census"):
    gm.set_fill(lab64, c.RED)
    cap6census = gm.annotate_text_box(
        "Units of Z/6Z are exactly the residues coprime to 6: {1, 5}. The "
        "others (2, 3, 4) share a factor with 6, so Z/6Z is a ring but not a "
        "field.",
        -4.1, -4.3, 13, 3.5, -1, out="six-cap-census",
    )
    gm.set_fill(cap6census, c.WHITE)
```

## The dial of $\mathbb{Z}/6\mathbb{Z}$

- {Six notches}(ref:six-dial) on a circle and nothing else: $\mathbb{Z}$ folded so
  that $k$ and $k + 6$ coincide.
- {Name the residues}(ref:six-residues), $0$ through $5$, with $\amber{1}$ marked
  as the multiplicative identity.

Addition just turns the hand. {Start at $\blue{4}$}(ref:six-add-hands), then
{turn three notches}(ref:six-add-arc):

- $4 + 3 = 7 \equiv 1 \pmod 6$, because three notches of $60°$ from $4$ pass $5$,
  cross $0$, and stop on $\violet{1}$.

## What an inverse looks like

A residue $u$ is a **unit** when some $v$ satisfies $u \cdot v \equiv 1 \pmod n$.
Multiplying by $u$ is stepping $u$ notches at a time, so the multiples
$u, 2u, 3u, \dots$ trace an orbit around the dial, and $u$ has an inverse exactly
when that orbit **closes through $\amber{1}$**. The step count that gets there is
the inverse.

- {Step by five}(ref:six-orbit-five): $5, 4, 3, 2, 1$, one notch back each time,
  visiting every residue.
- The fifth step {closes on 1}(ref:six-five-inverse), so $\green{5}$ is a unit:
  - $5 \cdot 5 = 25 \equiv 1 \pmod 6$, that is, $5^{-1} = 5$.
- {Step by two}(ref:six-orbit-two) instead: $2, 4, 0, 2, 4, 0, \dots$ a closed
  triangle on $\{0, 2, 4\}$ that can never reach $\amber{1}$.
- Stepping by $2$ even lands on $0$ from a nonzero start, a
  {zero divisor}(ref:six-zero-divisor):
  - $2 \cdot 3 \equiv 0 \pmod 6$ with $2 \not\equiv 0$ and $3 \not\equiv 0$, a
    direct print of $6 = 2 \times 3$.
- {The census for 6}(ref:six-census): units $\{1, 5\}$, while $\red{2, 3, 4}$ each
  share a factor with $6$.

The multiplication table mod $6$ says the same thing row by row. Rows and columns
run over $k, m = 1 \dots 5$, so the row of $k$ is the orbit of $k$, and $k$ is a
unit iff a $1$ appears in its row.

$$
%id:times-six
\begin{pmatrix}
1 & 2 & 3 & 4 & 5 \\
2 & 4 & 0 & 2 & 4 \\
3 & 0 & 3 & 0 & 3 \\
4 & 2 & 0 & 4 & 2 \\
5 & 4 & 3 & 2 & 1
\end{pmatrix}
$$

```pygeomatic
gate_two = gm.scalar(0, out="gate-two")
gate_five = gm.scalar(0, out="gate-five")
tbl = gm.tex("times-six")
tbl.highlight((gm.rows == 1).scale(gate_two), color="red")
tbl.highlight((gm.rows == 4).scale(gate_five), color="emerald")
```

Light up the {row of 2}(gate_two = gm.scalar(1, out="gate-two")): no $1$ anywhere,
but a $0$ twice over. Now the {row of 5}(gate_five = gm.scalar(1, out="gate-five")):
a permutation of all five residues, so it has to contain $1$.

```pygeomatic
gm.clear()

# n = 7: same radii, seven notches.
SEV = [(0.0, 2.5), (1.9546, 1.5587), (2.4373, -0.5563), (1.0847, -2.2524),
       (-1.0847, -2.2524), (-2.4373, -0.5563), (-1.9546, 1.5587)]
SEV_LAB = [(0.0, 3.08), (2.408, 1.9203), (3.0028, -0.6854), (1.3364, -2.775),
           (-1.3364, -2.775), (-3.0028, -0.6854), (-2.408, 1.9203)]

unit = gm.scalar(50, out="unit")
grid_opacity = gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

with group("sev-dial"):
    dial7 = gm.circle(gm.p0, 2.5)
    gm.set_stroke(dial7, c["COLOR-GRAY-MID"])

with group("sev-residues"):
    sev0 = gm.point(SEV[0][0], SEV[0][1], out="sev-res-0")
    sev1 = gm.point(SEV[1][0], SEV[1][1], out="sev-res-1")
    sev2 = gm.point(SEV[2][0], SEV[2][1], out="sev-res-2")
    sev3 = gm.point(SEV[3][0], SEV[3][1], out="sev-res-3")
    sev4 = gm.point(SEV[4][0], SEV[4][1], out="sev-res-4")
    sev5 = gm.point(SEV[5][0], SEV[5][1], out="sev-res-5")
    sev6 = gm.point(SEV[6][0], SEV[6][1], out="sev-res-6")
    gm.hide(sev0, sev1, sev2, sev3, sev4, sev5, sev6)

    lab70 = gm.annotate_text_box("0", SEV_LAB[0][0], SEV_LAB[0][1], 17, out="sev-lab-0")
    lab71 = gm.annotate_text_box("1", SEV_LAB[1][0], SEV_LAB[1][1], 17, out="sev-lab-1")
    lab72 = gm.annotate_text_box("2", SEV_LAB[2][0], SEV_LAB[2][1], 17, out="sev-lab-2")
    lab73 = gm.annotate_text_box("3", SEV_LAB[3][0], SEV_LAB[3][1], 17, out="sev-lab-3")
    lab74 = gm.annotate_text_box("4", SEV_LAB[4][0], SEV_LAB[4][1], 17, out="sev-lab-4")
    lab75 = gm.annotate_text_box("5", SEV_LAB[5][0], SEV_LAB[5][1], 17, out="sev-lab-5")
    lab76 = gm.annotate_text_box("6", SEV_LAB[6][0], SEV_LAB[6][1], 17, out="sev-lab-6")
    gm.set_fill(lab70, c.WHITE)
    gm.set_fill(lab71, c.AMBER)
    gm.set_fill(lab72, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab73, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab74, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab75, c["COLOR-GRAY-LIGHT"])
    gm.set_fill(lab76, c["COLOR-GRAY-LIGHT"])

    title7 = gm.annotate_text_box("Z/7Z", 0, 0, 19)
    gm.set_fill(title7, c.WHITE)

with group("sev-orbit-three"):
    orb3a = gm.annotate_arrow(sev3, sev6, 0.08, out="sev-orbit-three-a")
    orb3b = gm.annotate_arrow(sev6, sev2, 0.08, out="sev-orbit-three-b")
    orb3c = gm.annotate_arrow(sev2, sev5, 0.08, out="sev-orbit-three-c")
    orb3d = gm.annotate_arrow(sev5, sev1, 0.08, out="sev-orbit-three-d")
    gm.set_stroke(orb3a, c.VIOLET)
    gm.set_stroke(orb3b, c.VIOLET)
    gm.set_stroke(orb3c, c.VIOLET)
    gm.set_stroke(orb3d, c.VIOLET)

with group("sev-three-inverse"):
    hit7one = gm.point(-4.6, -2.3, out="sev-hit-one")
    gm.hide(hit7one)
    lead7one = gm.annotate_leader_line(sev5, hit7one, "3 · 5 = 15 ≡ 1", out="sev-lead-one")
    gm.set_stroke(lead7one, c.VIOLET)

    cap7orbit = gm.annotate_text_box(
        "Multiples of 3 mod 7: 3, 6, 9 ≡ 2, 12 ≡ 5, 15 ≡ 1. Stepping by 3 "
        "visits every nonzero residue and closes through 1, so 3⁻¹ = 5.",
        -4.1, 4.3, 13, 3.5, -1, out="sev-cap-orbit",
    )
    gm.set_fill(cap7orbit, c.VIOLET)

with group("sev-units"):
    gm.set_fill(lab72, c.EMERALD)
    gm.set_fill(lab73, c.EMERALD)
    gm.set_fill(lab74, c.EMERALD)
    gm.set_fill(lab75, c.EMERALD)
    gm.set_fill(lab76, c.EMERALD)

    cap7inv = gm.annotate_text_box(
        "Inverses mod 7: 1⁻¹ = 1, 2⁻¹ = 4, 3⁻¹ = 5, 4⁻¹ = 2, 5⁻¹ = 3, 6⁻¹ = 6. "
        "Every nonzero residue is a unit.",
        4.1, 4.3, 13, 3.5, -1, out="sev-cap-inverses",
    )
    gm.set_fill(cap7inv, c.EMERALD)

with group("sev-prime"):
    cap7prime = gm.annotate_text_box(
        "7 is prime, so gcd(k, 7) = 1 for every k = 1 .. 6. No residue can "
        "share a factor with 7, so there are no zero divisors and Z/7Z is a "
        "field.",
        4.1, -4.3, 13, 3.5, -1, out="sev-cap-prime",
    )
    gm.set_fill(cap7prime, c.WHITE)
```

## Now a prime modulus

- {Seven notches}(ref:sev-dial) and the {same residue labels}(ref:sev-residues),
  $0$ through $6$.
- {Step by three}(ref:sev-orbit-three): $3, 6, 2, 5, \dots$ a star that skips
  nothing.
- The fourth step {arrives at 1}(ref:sev-three-inverse):
  - $3 \cdot 5 = 15 \equiv 1 \pmod 7$, so $3^{-1} = 5$.
- Nothing about $3$ was special, so {every nonzero residue is a unit}(ref:sev-units).
- {Because 7 is prime}(ref:sev-prime): $\gcd(k, 7) = 1$ for every
  $k = 1, \dots, 6$, so no orbit gets trapped in a proper subset and no product of
  nonzero residues can vanish.

```pygeomatic
gm.clear()

# Scene 3: compact dials, radius 1.6, label ring 2.1, centers (-3.2, 0.6) / (3.2, 0.6).
SMALL6 = [(-3.2, 2.2), (-1.8144, 1.4), (-1.8144, -0.2),
          (-3.2, -1.0), (-4.5856, -0.2), (-4.5856, 1.4)]
SMALL6_LAB = [(-3.2, 2.7), (-1.3813, 1.65), (-1.3813, -0.45),
              (-3.2, -1.5), (-5.0187, -0.45), (-5.0187, 1.65)]
SMALL7 = [(3.2, 2.2), (4.4509, 1.5976), (4.7599, 0.244), (3.8942, -0.8416),
          (2.5058, -0.8416), (1.6401, 0.244), (1.9491, 1.5976)]
SMALL7_LAB = [(3.2, 2.7), (4.8418, 1.9093), (5.2473, 0.1327), (4.1112, -1.292),
              (2.2888, -1.292), (1.1527, 0.1327), (1.5582, 1.9093)]

unit = gm.scalar(50, out="unit")
grid_opacity = gm.scalar(0, out="grid-opacity")
c = gm.load_colors()

with group("side-dials"):
    left_center = gm.point(-3.2, 0.6, out="left-center")
    right_center = gm.point(3.2, 0.6, out="right-center")
    gm.hide(left_center, right_center)

    small_dial6 = gm.circle(left_center, 1.6, out="small-dial-six")
    small_dial7 = gm.circle(right_center, 1.6, out="small-dial-sev")
    gm.set_stroke(small_dial6, c["COLOR-GRAY-MID"])
    gm.set_stroke(small_dial7, c["COLOR-GRAY-MID"])

with group("side-six"):
    s62 = gm.point(SMALL6[2][0], SMALL6[2][1], out="small-six-res-2")
    s63 = gm.point(SMALL6[3][0], SMALL6[3][1], out="small-six-res-3")
    gm.hide(s62, s63)

    slab60 = gm.annotate_text_box("0", SMALL6_LAB[0][0], SMALL6_LAB[0][1], 15, out="small-six-lab-0")
    slab61 = gm.annotate_text_box("1", SMALL6_LAB[1][0], SMALL6_LAB[1][1], 15, out="small-six-lab-1")
    slab62 = gm.annotate_text_box("2", SMALL6_LAB[2][0], SMALL6_LAB[2][1], 15, out="small-six-lab-2")
    slab63 = gm.annotate_text_box("3", SMALL6_LAB[3][0], SMALL6_LAB[3][1], 15, out="small-six-lab-3")
    slab64 = gm.annotate_text_box("4", SMALL6_LAB[4][0], SMALL6_LAB[4][1], 15, out="small-six-lab-4")
    slab65 = gm.annotate_text_box("5", SMALL6_LAB[5][0], SMALL6_LAB[5][1], 15, out="small-six-lab-5")
    gm.set_fill(slab60, c.WHITE)
    gm.set_fill(slab61, c.AMBER)
    gm.set_fill(slab62, c.RED)
    gm.set_fill(slab63, c.RED)
    gm.set_fill(slab64, c.RED)
    gm.set_fill(slab65, c.EMERALD)

with group("side-sev"):
    slab70 = gm.annotate_text_box("0", SMALL7_LAB[0][0], SMALL7_LAB[0][1], 15, out="small-sev-lab-0")
    slab71 = gm.annotate_text_box("1", SMALL7_LAB[1][0], SMALL7_LAB[1][1], 15, out="small-sev-lab-1")
    slab72 = gm.annotate_text_box("2", SMALL7_LAB[2][0], SMALL7_LAB[2][1], 15, out="small-sev-lab-2")
    slab73 = gm.annotate_text_box("3", SMALL7_LAB[3][0], SMALL7_LAB[3][1], 15, out="small-sev-lab-3")
    slab74 = gm.annotate_text_box("4", SMALL7_LAB[4][0], SMALL7_LAB[4][1], 15, out="small-sev-lab-4")
    slab75 = gm.annotate_text_box("5", SMALL7_LAB[5][0], SMALL7_LAB[5][1], 15, out="small-sev-lab-5")
    slab76 = gm.annotate_text_box("6", SMALL7_LAB[6][0], SMALL7_LAB[6][1], 15, out="small-sev-lab-6")
    gm.set_fill(slab70, c.WHITE)
    gm.set_fill(slab71, c.AMBER)
    gm.set_fill(slab72, c.EMERALD)
    gm.set_fill(slab73, c.EMERALD)
    gm.set_fill(slab74, c.EMERALD)
    gm.set_fill(slab75, c.EMERALD)
    gm.set_fill(slab76, c.EMERALD)

with group("side-heads"):
    head6 = gm.annotate_text_box("n = 6 = 2 × 3 (composite)", -3.2, 3.4, 13, 3.8, -1, out="head-six")
    head7 = gm.annotate_text_box("n = 7 (prime)", 3.2, 3.4, 13, 3.8, -1, out="head-sev")
    gm.set_fill(head6, c.RED)
    gm.set_fill(head7, c.EMERALD)

with group("side-verdict"):
    zdiv_chord = gm.annotate_arrow(s63, s62, 0.12, "2 · 3 ≡ 0", out="small-six-zero-divisor")
    gm.set_stroke(zdiv_chord, c.RED)

    cap_left = gm.annotate_text_box(
        "Units {1, 5} only. 2, 3, 4 share a factor with 6 and are zero "
        "divisors: their orbits close without ever hitting 1.",
        -3.2, -2.6, 13, 3.8, -1, out="cap-left",
    )
    gm.set_fill(cap_left, c.RED)

    cap_right = gm.annotate_text_box(
        "Units {1, 2, 3, 4, 5, 6}: every nonzero residue has an inverse, so "
        "each orbit sweeps the whole dial and closes through 1.",
        3.2, -2.6, 13, 3.8, -1, out="cap-right",
    )
    gm.set_fill(cap_right, c.EMERALD)

with group("closing"):
    summary = gm.annotate_text_box(
        "A residue k is invertible mod n exactly when gcd(k, n) = 1. If n = a·b "
        "splits, then a and b are nonzero residues with a·b ≡ 0 and no "
        "inverses; if n is prime, no nonzero residue shares a factor with it, "
        "so Z/nZ is a field exactly when n is prime.",
        0, -4.5, 13, 8.4, -1,
    )
    gm.set_fill(summary, c.WHITE)
```

## Side by side

- {Two dials}(ref:side-dials), $n = 6$ on the left and $n = 7$ on the right.
- Color every residue by its role: {on the left}(ref:side-six) only $\amber{1}$
  and $\green{5}$ are units, while {on the right}(ref:side-sev) every nonzero
  residue is. {The reason}(ref:side-heads) sits above each dial:
  $6 = 2 \times 3$ splits, $7$ does not.
- {The composite penalty}(ref:side-verdict): the chord from $3$ to $2$ carries
  $2 \cdot 3 \equiv 0$, so $\mathbb{Z}/6\mathbb{Z}$ has zero divisors and nonzero
  elements with no inverse.
- {The statement}(ref:closing): a residue $k$ is invertible mod $n$ iff
  $\gcd(k, n) = 1$.
  - If $n = ab$ with $1 < a, b < n$, then $a$ and $b$ are nonzero with
    $ab \equiv 0$, so neither can be a unit and the dial is not a field.
  - If $n$ is prime, every $k \not\equiv 0$ is coprime to $n$, so every orbit
    sweeps the dial and closes through $1$.
  - Hence $\mathbb{Z}/n\mathbb{Z}$ is a field exactly when $n$ is prime.
