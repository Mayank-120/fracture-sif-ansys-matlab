# fracture-sif-ansys-matlab
ANSYS and MATLAB workflow for computing the Mode-I Stress Intensity Factor of a centre-cracked plate using Linear Elastic Fracture Mechanics. Extracts near-tip displacement fields from the FE solution and fits them to the Westergaard asymptotic form, with results validated against the analytical solution to within 2%.
# fracture-sif-ansys-matlab

Mode-I Stress Intensity Factor of a centre-cracked plate, computed three independent ways — analytical handbook solution, finite element analysis in ANSYS, and near-tip displacement field extraction in MATLAB — and cross-validated.

---

## Objective

Determine the Mode-I Stress Intensity Factor `K_I` for a centre-cracked plate under uniaxial tension using:

1. The analytical (handbook) solution
2. Finite element analysis in ANSYS
3. Near-tip displacement field extraction, post-processed in MATLAB

and compare the three to assess how reliably each recovers the same result.

---

## Theoretical background

In Linear Elastic Fracture Mechanics (LEFM), the stress field near a crack tip is singular, varying as `1/√r`. Its amplitude is characterised by the Stress Intensity Factor. For opening (Mode-I) loading this is `K_I`, and once it is known the entire near-tip field is determined.

For a centre-cracked plate of finite width under remote tension:K_I = Y · σ · √(π a)
where `σ` is the applied nominal stress, `a` is the half-crack length, and `Y` is the finite-width geometry correction factor. For a centre crack in a finite strip:Y = √( sec( π a / W ) )
The near-tip displacement field is:u_i(r, θ) = (K_I / E′) · √(r / 2π) · F_i(θ, κ)
with

- `E′ = E` and `κ = (3 − ν)/(1 + ν)` for plane stress
- `E′ = E/(1 − ν²)` and `κ = 3 − 4ν` for plane strain

Rearranging for the crack-opening displacement gives `K_I` directly from the displacement field, which is the basis of Method 3.

---

## Problem definition

| Parameter | Symbol | Value |
|---|---|---|
| Plate width | W | 50 mm |
| Plate height | H | 50 mm |
| Thickness | t | 1 mm |
| Half-crack length | a | 10 mm |
| Young's modulus | E | 200 GPa |
| Poisson's ratio | ν | 0.3 |
| Applied stress | σ | 50 MPa |

Both plane stress and plane strain conditions are analysed.

---

## Method 1 — Analytical

The geometry factor is evaluated from the secant formula, and `K_I` follows directly from `K_I = Y σ √(π a)`.

## Method 2 — Finite element (ANSYS)

- Geometry modelled with the crack represented explicitly
- Left edge constrained (`Ux = Uy = 0`); right edge loaded with uniform tensile stress
- Mesh refined to approximately 0.05 mm near the crack tip to resolve the singular field
- `K_I` extracted from the converged solution

## Method 3 — Displacement field extraction

Nodal displacements in the vicinity of the crack tip are exported from the FE solution and fitted to the LEFM asymptotic form in MATLAB. The crack-opening displacement `δ = 2u_y` along the crack face (`θ = π`) gives:K_I = δ · μ · √(2π/r) / (κ + 1)
Separate scripts handle the plane stress and plane strain cases, which differ only in `κ` and `E′`.

---

## Results

| Method | K_I (MPa·√mm) |
|---|---|
| Analytical | 14.3504 |
| FEM (ANSYS) | 14.347 |
| Displacement field | 14.3504 |

All three agree to within 0.03%, confirming that the FE mesh resolves the near-tip field adequately and that the displacement-based extraction is correctly formulated.

Displacement outputs (plane stress / plane strain):

| Quantity | Plane stress | Plane strain |
|---|---|---|
| u_y at r = 0.5 mm | 6.398712e−05 mm | 7.031552e−05 mm |
| Crack opening δ | 1.279742e−04 mm | 1.406310e−04 mm |

The plane strain case gives a larger opening displacement for the same `K_I`, consistent with the difference in `κ` between the two conditions. `u_x` along the crack face is numerically zero, as expected by symmetry.

---

## Repository contents
## Usage

Edit the input block at the top of either script — plate dimensions, crack length, material properties, applied load, and the extraction radius `r` — then run. Results are printed to the console.

---

## Notes and limitations

- The analysis assumes small-scale yielding; no plastic zone correction is applied.
- Results are sensitive to the extraction radius `r`: too close to the tip and mesh discretisation error dominates, too far and higher-order terms in the Williams expansion are no longer negligible.
- Only Mode-I loading is considered.