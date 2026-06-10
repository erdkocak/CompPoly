# Poster Layout Plan

## Source Guidance From `How to prepare a poster.pdf`

The department guide is the constraint document.

- Format: A0, landscape/horizontal orientation.
- Readability: important content should be readable from about 10 feet away.
- Suggested type sizes: about 85 pt title, 36 pt headings, 24 pt body, 18 pt captions.
- Word count: stay around 300 to 800 words.
- Layout: clean grid, consistent headings, strong visual hierarchy.
- Content: use bullets, diagrams, and compact statements instead of long paragraphs.
- Printing: avoid pixelated images and verify spelling/grammar before printing.
- Header: include authors, advisor, institution, and logos.

The poster should not read like a generated summary. It should feel like a compact technical artifact: visual first, concise text, with one highlighted Lean theorem.

## Title and Header

Poster title:

```text
Formal Verification of Number-Theoretic Transform in Lean
```

Header content:

```text
Salih Erdem Kocak, Doran Pamukcu
Advisor: Dogan Ulus
Department of Computer Engineering, Bogazici University
```

Logos:

- Bogazici University logo on the left.
- Verified zkEVM GitHub organization logo on the right.

The title should dominate the header. Logos should be visible but secondary.

## Target Audience

Primary audience: mixed CMPE 492 audience.

Assume readers know algorithms and basic undergraduate mathematics, but not Lean, CompPoly, or root-of-unity NTT details.

Consequences:

- Explain the NTT idea visually.
- Show exactly one Lean theorem statement.
- Keep Lean/module details precise but sparse.
- Emphasize the verified implementation and proof architecture.
- Include benchmark evidence, but do not turn the poster into a performance-only poster.

## Visual Style

Use a clean academic blue/white style.

- White or very light background.
- Bogazici-compatible blue headings and accents.
- Restrained theorem/result boxes.
- No dark theme, neon palette, heavy gradients, or decorative clutter.
- Diagrams should be vector graphics in TikZ.

## Three-Column Structure

The 3-column layout is good and should be kept.

### Columns 1-2: Algorithm, Implementation, and Main Result

Goal: make the computational idea clear while keeping the formal contribution visible.

The left two-thirds of the poster should be arranged as a two-column working area:

- Top row: **Motivation** on the left, **Implementation Surface** on the right.
- Middle row: **NTT Multiplication** spanning both columns.
- Bottom row: **Benchmark Behavior** on the left, **Main Theorem** on the right.

The spanning NTT row is intentional. The diagram needs enough width to read as a
domain-changing pipeline, not as a cramped vertical stack.

Sections:

1. **Motivation**
   - Say that polynomial multiplication is a core component of cryptographic
     protocols, especially proof systems.
   - Mention concrete places it appears: evaluation/interpolation routines,
     polynomial commitments, and quotient-polynomial constructions.
   - Explain that naive multiplication is quadratic, while the NTT avoids
     direct convolution by evaluating on roots of unity, multiplying pointwise,
     and transforming back.
   - State the verification contribution directly: the Lean proof connects the
     executable pipeline to the algebraic specification and shows that the fast
     path is correct.
   - Add a compact, booktabs-style comparison below the text:
     naive coefficient-pair summation is `O(n^2)`, while the NTT route is
     `O(n log n)`.

2. **NTT Multiplication**
   Use a mathematical domain-changing diagram that spans the first two columns.

   Required visual:

   ```text
   coefficient inputs                  evaluation domain                 coefficient output

   p(x)=a0+a1 x+...       NTT        [p(omega^i)]_{i<n}
                                                   \
                                                    pointwise multiply    inverse NTT     c(x)=p(x)q(x)
                                                   /
   q(x)=b0+b1 x+...       NTT        [q(omega^i)]_{i<n}
   ```

   Keep separate NTT boxes for `p` and `q`; the two transformed vectors should merge
   into a single pointwise-product box, followed by inverse NTT and the coefficient
   output.

   Use `omega^i`, not ordinary points like `0, 1, ...`, because this is an NTT over roots of unity.

3. **Benchmark Plot**
   Include a plot from the manual KoalaBear benchmark sweep in
   `tests/CompPolyTests/Univariate/NTT/Benchmark.lean`.

   Plot:

   - x-axis: operand size.
   - y-axis: average milliseconds, ordinary linear scale.
   - two curves: naive raw multiplication and NTT multiplication.
   - use sizes from 64 upward to avoid timer-resolution noise at tiny sizes.

   Measured data from the local run:

   ```text
   n     NTT ms   raw ms
   64    0.25     0.35
   96    0.55     0.85
   128   0.60     1.50
   192   5.00     12.80
   256   4.80     22.80
   384   10.40    51.00
   512   10.40    91.00
   768   55.50    518.00
   1024  59.00    917.00
   1536  121.50   2047.00
   2048  241.00   7323.00
   2560  526.00   11532.00
   3000  539.00   15756.00
   ```

   Caption should say the benchmark is illustrative and machine-dependent.

4. **Implementation Surface**
   Show the CompPoly implementation as a compact module diagram:

   ```text
   Domain + Transform
          |
      Forward / Inverse
          |
        FastMul
          |
   CPolynomial fast multiplication
   ```

   This should not include a full "domain assumptions" bullet list.

5. **Main Theorem**
   Use exactly one Lean statement.

   Use a syntax-highlighted Lean-style code box, preferably via LaTeX `listings` with a VS Code-like Lean palette. Do not use a screenshot unless source highlighting becomes unusable.

   The theorem:

   ```lean
   theorem fastMulImpl_eq_mul
       (D : Domain R)
       (p q : CPolynomial R)
       (hfit : Domain.fits D p.val q.val) :
       fastMulImpl D p q = p * q
   ```

   Plain-language caption:

   ```text
   If the NTT domain is large enough for the convolution, the fast implementation equals ordinary polynomial multiplication.
   ```

   Add a slightly more explicit explanation under the theorem:

   - `Domain.fits D p.val q.val` is the only correctness precondition shown.
   - It means the selected transform domain is large enough for ordinary convolution.
   - Therefore the fast executable path can be used without changing the mathematical result.

   Do not include a separate contribution-summary block or a mini theorem
   flowchart.

### Column 3: Proof Architecture, References, and Links

Goal: make the verification contribution visually strong.

Sections:

1. **Proof Architecture**
   This should be larger and more elegant than in the first draft.

   Preferred visual: a refinement ladder or layered proof diagram:

   ```text
   executable loops
        |
        v
   forwardImpl / inverseImpl
        |
        v
   forwardSpec / inverseSpec
        |
        v
   pointwise multiplication in evaluation domain
        |
        v
   coefficient equality with ordinary convolution
        |
        v
   CPolynomial equality by extensionality
   ```

   Add small side labels such as:

   - "implementation"
   - "specification"
   - "algebra"
   - "canonical polynomial result"

   This should be the visual centerpiece of the right column.

2. **References**
   Add a references section. Keep it compact.

   Required references:

   - Trieu, A., "Formally Verified Number-Theoretic Transform." IACR
     Communications in Cryptology, vol. 2, no. 4, Jan. 2026. DOI:
     `10.62056/ahbn-4tw9`.
   - Capretta, V., "Certified Fast Fourier Transform." Theorem Proving in
     Higher Order Logics. TPHOLs 2001. Springer, 2001.
   - Ballarin, C., "Fast Fourier Transform." Archive of Formal Proofs,
     Oct. 2005. `https://isa-afp.org/entries/FFT.html`.
   - Truth Research ZK, "AMO-Lean", 2026,
     `https://github.com/lambdaclass/amo-lean`.

3. **QR Codes**
   Include both QR codes:

   - Merged PR #174: `https://github.com/Verified-zkEVM/CompPoly/pull/174`
   - CompPoly repository: `https://github.com/Verified-zkEVM/CompPoly`

   Place them near the references section or at the bottom-right of Column 3.

## Content To Remove From The First Draft

Remove or heavily compress:

- The separate "Key identity" block.
- The separate "Domain Assumptions" block.
- The separate "Raw and Canonical Polynomials" block.
- Generic text that sounds like a summary instead of a technical poster.

The poster can still mention domain fit and primitive roots, but only where they support the theorem or the NTT diagram.

## Main Message

Use this as the guiding sentence:

```text
We formalized an NTT-based multiplication algorithm for computable univariate polynomials and proved in Lean that the executable implementation agrees with ordinary polynomial multiplication whenever the NTT domain fits the product.
```

## Build Notes

Compile from `poster/`:

```sh
latexmk -xelatex -interaction=nonstopmode -halt-on-error main.tex
```

Current source should remain portable:

- TikZ for diagrams.
- PGFPlots for the benchmark plot.
- `qrcode` for QR codes.
- `listings` for Lean-style syntax highlighting.
