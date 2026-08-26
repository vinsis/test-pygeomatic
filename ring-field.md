# $\mathbb{Z}/n\mathbb{Z}$ is a field exactly when $n$ is prime

Fix $n$ and wrap the integers onto a dial with $n$ notches. Every integer lands on
its remainder, and the ring $\mathbb{Z}/n\mathbb{Z}$ *is* that dial: addition
turns the hand, and multiplication by $k$ is stepping $k$ notches at a time. The
whole field question is visible in those steps.

{}(\clear)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

## The dial of $\mathbb{Z}/6\mathbb{Z}$

- {}(dial6 = \circle p0 2.5) {Six notches}(\set-stroke dial6 COLOR-GRAY-MID) on a circle and nothing else: $\mathbb{Z}$ folded so
  that $k$ and $k + 6$ coincide.
- {}(six-res-0 = \point 0 2.5) {}(six-res-1 = \point 2.1651 1.25) {}(six-res-2 = \point 2.1651 -1.25) {}(six-res-3 = \point 0 -2.5) {}(six-res-4 = \point -2.1651 -1.25) {}(six-res-5 = \point -2.1651 1.25) {}(\hide six-res-0 six-res-1 six-res-2 six-res-3 six-res-4 six-res-5) {}(text-0 = \text "0") {}(six-lab-0 = \annotate-text-box text-0 0 3.08 17) {}(text-1 = \text "1") {}(six-lab-1 = \annotate-text-box text-1 2.6674 1.54 17) {}(text-2 = \text "2") {}(six-lab-2 = \annotate-text-box text-2 2.6674 -1.54 17) {}(text-3 = \text "3") {}(six-lab-3 = \annotate-text-box text-3 0 -3.08 17) {}(text-4 = \text "4") {}(six-lab-4 = \annotate-text-box text-4 -2.6674 -1.54 17) {}(text-5 = \text "5") {}(six-lab-5 = \annotate-text-box text-5 -2.6674 1.54 17) {}(\set-fill six-lab-0 COLOR-WHITE) {}(\set-fill six-lab-1 COLOR-AMBER) {}(\set-fill six-lab-2 COLOR-GRAY-LIGHT) {}(\set-fill six-lab-3 COLOR-GRAY-LIGHT) {}(\set-fill six-lab-4 COLOR-GRAY-LIGHT) {}(\set-fill six-lab-5 COLOR-GRAY-LIGHT) {}(text-6 = \text "Z/6Z") {}(title6 = \annotate-text-box text-6 0 0 19) {Name the residues}(\set-fill title6 COLOR-WHITE), $0$ through $5$, with $\amber{1}$ marked
  as the multiplicative identity.

Addition just turns the hand. {}(six-hand-from = \annotate-arrow p0 six-res-4 0.04) {}(six-hand-to = \annotate-arrow p0 six-res-1 0.04) {}(\set-stroke six-hand-from COLOR-BLUE) {Start at $\blue{4}$}(\set-stroke six-hand-to COLOR-VIOLET), then
{}(six-add-control = \point -1.75 3.031) {}(\hide six-add-control) {}(six-inner-4 = \point -1.5155 -0.875) {}(six-inner-1 = \point 1.5155 0.875) {}(\hide six-inner-4 six-inner-1) {}(text-7 = \text "+3 ↻") {}(six-add-arc = \annotate-curved-arrow six-inner-4 six-inner-1 six-add-control 0.06 text-7) {}(\set-stroke six-add-arc COLOR-BLUE) {}(text-8 = \text "Addition is rotation. Adding 3 turns the hand 3 notches clockwise (3 × 60°): from 4 it passes 5 and 0 and lands on 1, so 4 + 3 ≡ 1 (mod 6).") {}(six-cap-add = \annotate-text-box text-8 -4.1 4.3 13 3.5 -1) {turn three notches}(\set-fill six-cap-add COLOR-BLUE):

- $4 + 3 = 7 \equiv 1 \pmod 6$, because three notches of $60°$ from $4$ pass $5$,
  cross $0$, and stop on $\violet{1}$.

## What an inverse looks like

A residue $u$ is a **unit** when some $v$ satisfies $u \cdot v \equiv 1 \pmod n$.
Multiplying by $u$ is stepping $u$ notches at a time, so the multiples
$u, 2u, 3u, \dots$ trace an orbit around the dial, and $u$ has an inverse exactly
when that orbit **closes through $\amber{1}$**. The step count that gets there is
the inverse.

- {}(six-orbit-five-a = \annotate-arrow six-res-5 six-res-4 0.1) {}(six-orbit-five-b = \annotate-arrow six-res-4 six-res-3 0.1) {}(six-orbit-five-c = \annotate-arrow six-res-3 six-res-2 0.1) {}(six-orbit-five-d = \annotate-arrow six-res-2 six-res-1 0.1) {}(\set-stroke six-orbit-five-a COLOR-EMERALD) {}(\set-stroke six-orbit-five-b COLOR-EMERALD) {}(\set-stroke six-orbit-five-c COLOR-EMERALD) {}(\set-stroke six-orbit-five-d COLOR-EMERALD) {Step by five}(\set-fill six-lab-5 COLOR-EMERALD): $5, 4, 3, 2, 1$, one notch back each time,
  visiting every residue.
- The fifth step {}(six-hit-one = \point 4.5 2.2) {}(\hide six-hit-one) {}(text-9 = \text "5 · 5 = 25 ≡ 1") {}(six-lead-one = \annotate-leader-line six-res-1 six-hit-one text-9) {}(\set-stroke six-lead-one COLOR-EMERALD) {}(text-10 = \text "Multiples of 5: 5, 10 ≡ 4, 15 ≡ 3, 20 ≡ 2, 25 ≡ 1. Stepping by 5 sweeps every residue and reaches 1, so 5 is a unit with 5⁻¹ = 5.") {}(six-cap-unit = \annotate-text-box text-10 4.1 4.3 13 3.5 -1) {closes on 1}(\set-fill six-cap-unit COLOR-EMERALD), so $\green{5}$ is a unit:
  - $5 \cdot 5 = 25 \equiv 1 \pmod 6$, that is, $5^{-1} = 5$.
- {}(six-orbit-two-a = \annotate-arrow six-res-2 six-res-4 0.08) {}(six-orbit-two-b = \annotate-arrow six-res-4 six-res-0 0.08) {}(six-orbit-two-c = \annotate-arrow six-res-0 six-res-2 0.08) {}(\set-stroke six-orbit-two-a COLOR-RED) {}(\set-stroke six-orbit-two-b COLOR-RED) {}(\set-stroke six-orbit-two-c COLOR-RED) {}(\set-fill six-lab-2 COLOR-RED) {Step by two}(\set-fill six-lab-3 COLOR-RED) instead: $2, 4, 0, 2, 4, 0, \dots$ a closed
  triangle on $\{0, 2, 4\}$ that can never reach $\amber{1}$.
- Stepping by $2$ even lands on $0$ from a nonzero start, a
  {}(text-11 = \text "2 · 3 ≡ 0") {}(six-zero-divisor = \annotate-curly-bracket six-res-3 six-res-2 text-11) {}(\set-stroke six-zero-divisor COLOR-RED) {}(text-12 = \text "Multiples of 2: 2, 4, 6 ≡ 0, and then it repeats. The orbit closes on {0, 2, 4} and never touches 1, so 2 has no inverse. Worse, 2 · 3 ≡ 0 with neither factor 0: a zero divisor, straight from 6 = 2 × 3.") {}(six-cap-nonunit = \annotate-text-box text-12 4.1 -4.3 13 3.5 -1) {zero divisor}(\set-fill six-cap-nonunit COLOR-RED):
  - $2 \cdot 3 \equiv 0 \pmod 6$ with $2 \not\equiv 0$ and $3 \not\equiv 0$, a
    direct print of $6 = 2 \times 3$.
- {}(\set-fill six-lab-4 COLOR-RED) {}(text-13 = \text "Units of Z/6Z are exactly the residues coprime to 6: {1, 5}. The others (2, 3, 4) share a factor with 6, so Z/6Z is a ring but not a field.") {}(six-cap-census = \annotate-text-box text-13 -4.1 -4.3 13 3.5 -1) {The census for 6}(\set-fill six-cap-census COLOR-WHITE): units $\{1, 5\}$, while $\red{2, 3, 4}$ each
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

{}(gate-two = \scalar 0)
{}(gate-five = \scalar 0)

Light up the {row of 2}(gate-two = \scalar 1): no $1$ anywhere,
but a $0$ twice over. Now the {row of 5}(gate-five = \scalar 1):
a permutation of all five residues, so it has to contain $1$.

{}(\clear)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

## Now a prime modulus

- {}(dial7 = \circle p0 2.5) {Seven notches}(\set-stroke dial7 COLOR-GRAY-MID) and the {}(sev-res-0 = \point 0 2.5) {}(sev-res-1 = \point 1.9546 1.5587) {}(sev-res-2 = \point 2.4373 -0.5563) {}(sev-res-3 = \point 1.0847 -2.2524) {}(sev-res-4 = \point -1.0847 -2.2524) {}(sev-res-5 = \point -2.4373 -0.5563) {}(sev-res-6 = \point -1.9546 1.5587) {}(\hide sev-res-0 sev-res-1 sev-res-2 sev-res-3 sev-res-4 sev-res-5 sev-res-6) {}(text-14 = \text "0") {}(sev-lab-0 = \annotate-text-box text-14 0 3.08 17) {}(text-15 = \text "1") {}(sev-lab-1 = \annotate-text-box text-15 2.408 1.9203 17) {}(text-16 = \text "2") {}(sev-lab-2 = \annotate-text-box text-16 3.0028 -0.6854 17) {}(text-17 = \text "3") {}(sev-lab-3 = \annotate-text-box text-17 1.3364 -2.775 17) {}(text-18 = \text "4") {}(sev-lab-4 = \annotate-text-box text-18 -1.3364 -2.775 17) {}(text-19 = \text "5") {}(sev-lab-5 = \annotate-text-box text-19 -3.0028 -0.6854 17) {}(text-20 = \text "6") {}(sev-lab-6 = \annotate-text-box text-20 -2.408 1.9203 17) {}(\set-fill sev-lab-0 COLOR-WHITE) {}(\set-fill sev-lab-1 COLOR-AMBER) {}(\set-fill sev-lab-2 COLOR-GRAY-LIGHT) {}(\set-fill sev-lab-3 COLOR-GRAY-LIGHT) {}(\set-fill sev-lab-4 COLOR-GRAY-LIGHT) {}(\set-fill sev-lab-5 COLOR-GRAY-LIGHT) {}(\set-fill sev-lab-6 COLOR-GRAY-LIGHT) {}(text-21 = \text "Z/7Z") {}(title7 = \annotate-text-box text-21 0 0 19) {same residue labels}(\set-fill title7 COLOR-WHITE),
  $0$ through $6$.
- {}(sev-orbit-three-a = \annotate-arrow sev-res-3 sev-res-6 0.08) {}(sev-orbit-three-b = \annotate-arrow sev-res-6 sev-res-2 0.08) {}(sev-orbit-three-c = \annotate-arrow sev-res-2 sev-res-5 0.08) {}(sev-orbit-three-d = \annotate-arrow sev-res-5 sev-res-1 0.08) {}(\set-stroke sev-orbit-three-a COLOR-VIOLET) {}(\set-stroke sev-orbit-three-b COLOR-VIOLET) {}(\set-stroke sev-orbit-three-c COLOR-VIOLET) {Step by three}(\set-stroke sev-orbit-three-d COLOR-VIOLET): $3, 6, 2, 5, \dots$ a star that skips
  nothing.
- The fourth step {}(sev-hit-one = \point -4.6 -2.3) {}(\hide sev-hit-one) {}(text-22 = \text "3 · 5 = 15 ≡ 1") {}(sev-lead-one = \annotate-leader-line sev-res-5 sev-hit-one text-22) {}(\set-stroke sev-lead-one COLOR-VIOLET) {}(text-23 = \text "Multiples of 3 mod 7: 3, 6, 9 ≡ 2, 12 ≡ 5, 15 ≡ 1. Stepping by 3 visits every nonzero residue and closes through 1, so 3⁻¹ = 5.") {}(sev-cap-orbit = \annotate-text-box text-23 -4.1 4.3 13 3.5 -1) {arrives at 1}(\set-fill sev-cap-orbit COLOR-VIOLET):
  - $3 \cdot 5 = 15 \equiv 1 \pmod 7$, so $3^{-1} = 5$.
- Nothing about $3$ was special, so {}(\set-fill sev-lab-2 COLOR-EMERALD) {}(\set-fill sev-lab-3 COLOR-EMERALD) {}(\set-fill sev-lab-4 COLOR-EMERALD) {}(\set-fill sev-lab-5 COLOR-EMERALD) {}(\set-fill sev-lab-6 COLOR-EMERALD) {}(text-24 = \text "Inverses mod 7: 1⁻¹ = 1, 2⁻¹ = 4, 3⁻¹ = 5, 4⁻¹ = 2, 5⁻¹ = 3, 6⁻¹ = 6. Every nonzero residue is a unit.") {}(sev-cap-inverses = \annotate-text-box text-24 4.1 4.3 13 3.5 -1) {every nonzero residue is a unit}(\set-fill sev-cap-inverses COLOR-EMERALD).
- {}(text-25 = \text "7 is prime, so gcd(k, 7) = 1 for every k = 1 .. 6. No residue can share a factor with 7, so there are no zero divisors and Z/7Z is a field.") {}(sev-cap-prime = \annotate-text-box text-25 4.1 -4.3 13 3.5 -1) {Because 7 is prime}(\set-fill sev-cap-prime COLOR-WHITE): $\gcd(k, 7) = 1$ for every
  $k = 1, \dots, 6$, so no orbit gets trapped in a proper subset and no product of
  nonzero residues can vanish.

{}(\clear)
{}(unit = \scalar 50)
{}(grid-opacity = \scalar 0)
{}(\load-colors)

## Side by side

- {}(left-center = \point -3.2 0.6) {}(right-center = \point 3.2 0.6) {}(\hide left-center right-center) {}(small-dial-six = \circle left-center 1.6) {}(small-dial-sev = \circle right-center 1.6) {}(\set-stroke small-dial-six COLOR-GRAY-MID) {Two dials}(\set-stroke small-dial-sev COLOR-GRAY-MID), $n = 6$ on the left and $n = 7$ on the right.
- Color every residue by its role: {}(small-six-res-2 = \point -1.8144 -0.2) {}(small-six-res-3 = \point -3.2 -1) {}(\hide small-six-res-2 small-six-res-3) {}(text-26 = \text "0") {}(small-six-lab-0 = \annotate-text-box text-26 -3.2 2.7 15) {}(text-27 = \text "1") {}(small-six-lab-1 = \annotate-text-box text-27 -1.3813 1.65 15) {}(text-28 = \text "2") {}(small-six-lab-2 = \annotate-text-box text-28 -1.3813 -0.45 15) {}(text-29 = \text "3") {}(small-six-lab-3 = \annotate-text-box text-29 -3.2 -1.5 15) {}(text-30 = \text "4") {}(small-six-lab-4 = \annotate-text-box text-30 -5.0187 -0.45 15) {}(text-31 = \text "5") {}(small-six-lab-5 = \annotate-text-box text-31 -5.0187 1.65 15) {}(\set-fill small-six-lab-0 COLOR-WHITE) {}(\set-fill small-six-lab-1 COLOR-AMBER) {}(\set-fill small-six-lab-2 COLOR-RED) {}(\set-fill small-six-lab-3 COLOR-RED) {}(\set-fill small-six-lab-4 COLOR-RED) {on the left}(\set-fill small-six-lab-5 COLOR-EMERALD) only $\amber{1}$
  and $\green{5}$ are units, while {}(text-32 = \text "0") {}(small-sev-lab-0 = \annotate-text-box text-32 3.2 2.7 15) {}(text-33 = \text "1") {}(small-sev-lab-1 = \annotate-text-box text-33 4.8418 1.9093 15) {}(text-34 = \text "2") {}(small-sev-lab-2 = \annotate-text-box text-34 5.2473 0.1327 15) {}(text-35 = \text "3") {}(small-sev-lab-3 = \annotate-text-box text-35 4.1112 -1.292 15) {}(text-36 = \text "4") {}(small-sev-lab-4 = \annotate-text-box text-36 2.2888 -1.292 15) {}(text-37 = \text "5") {}(small-sev-lab-5 = \annotate-text-box text-37 1.1527 0.1327 15) {}(text-38 = \text "6") {}(small-sev-lab-6 = \annotate-text-box text-38 1.5582 1.9093 15) {}(\set-fill small-sev-lab-0 COLOR-WHITE) {}(\set-fill small-sev-lab-1 COLOR-AMBER) {}(\set-fill small-sev-lab-2 COLOR-EMERALD) {}(\set-fill small-sev-lab-3 COLOR-EMERALD) {}(\set-fill small-sev-lab-4 COLOR-EMERALD) {}(\set-fill small-sev-lab-5 COLOR-EMERALD) {on the right}(\set-fill small-sev-lab-6 COLOR-EMERALD) every nonzero
  residue is. {}(text-39 = \text "n = 6 = 2 × 3 (composite)") {}(head-six = \annotate-text-box text-39 -3.2 3.4 13 3.8 -1) {}(text-40 = \text "n = 7 (prime)") {}(head-sev = \annotate-text-box text-40 3.2 3.4 13 3.8 -1) {}(\set-fill head-six COLOR-RED) {The reason}(\set-fill head-sev COLOR-EMERALD) sits above each dial:
  $6 = 2 \times 3$ splits, $7$ does not.
- {}(text-41 = \text "2 · 3 ≡ 0") {}(small-six-zero-divisor = \annotate-arrow small-six-res-3 small-six-res-2 0.12 text-41) {}(\set-stroke small-six-zero-divisor COLOR-RED) {}(text-42 = \text "Units {1, 5} only. 2, 3, 4 share a factor with 6 and are zero divisors: their orbits close without ever hitting 1.") {}(cap-left = \annotate-text-box text-42 -3.2 -2.6 13 3.8 -1) {}(\set-fill cap-left COLOR-RED) {}(text-43 = \text "Units {1, 2, 3, 4, 5, 6}: every nonzero residue has an inverse, so each orbit sweeps the whole dial and closes through 1.") {}(cap-right = \annotate-text-box text-43 3.2 -2.6 13 3.8 -1) {The composite penalty}(\set-fill cap-right COLOR-EMERALD): the chord from $3$ to $2$ carries
  $2 \cdot 3 \equiv 0$, so $\mathbb{Z}/6\mathbb{Z}$ has zero divisors and nonzero
  elements with no inverse.
- {}(text-44 = \text "A residue k is invertible mod n exactly when gcd(k, n) = 1. If n = a·b splits, then a and b are nonzero residues with a·b ≡ 0 and no inverses; if n is prime, no nonzero residue shares a factor with it, so Z/nZ is a field exactly when n is prime.") {}(summary = \annotate-text-box text-44 0 -4.5 13 8.4 -1) {The statement}(\set-fill summary COLOR-WHITE): a residue $k$ is invertible mod $n$ iff
  $\gcd(k, n) = 1$.
  - If $n = ab$ with $1 < a, b < n$, then $a$ and $b$ are nonzero with
    $ab \equiv 0$, so neither can be a unit and the dial is not a field.
  - If $n$ is prime, every $k \not\equiv 0$ is coprime to $n$, so every orbit
    sweeps the dial and closes through $1$.
  - Hence $\mathbb{Z}/n\mathbb{Z}$ is a field exactly when $n$ is prime.

<!-- texatlas:v1
{"times-six":{"highlights":[{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"row"},"value":{"const":1}},"by":{"node":"gate-two"}},"color":"#F87171"},{"selector":{"op":"scale","sel":{"op":"eq","axis":{"axis":"row"},"value":{"const":4}},"by":{"node":"gate-five"}},"color":"#34D399"}]}}
-->
