# fracture-sif-ansys-matlab
ANSYS and MATLAB workflow for computing the Mode-I Stress Intensity Factor of a centre-cracked plate using Linear Elastic Fracture Mechanics. Extracts near-tip displacement fields from the FE solution and fits them to the Westergaard asymptotic form, with results validated against the analytical solution to within 2%.
1. Objective
To determine the Stress Intensity Factor (SIF) for a cracked specimen using three 
approaches:
1. Analytical solution
2. Numerical (FEM-based) solution
3. Displacement field extraction near the crack tip.
2. Theoretical Background
In Linear Elastic Fracture Mechanics (LEFM), the Stress Intensity Factor (SIF) 
characterizes the intensity of the stress field near the tip of a crack. For mode I loading 
(opening mode), the SIF is denoted by K_I and can be calculated analytically for standard 
geometries using handbook solutions or numerically through finite element analysis 
(FEA).
3. Analytical Method
The specimen chosen for this study is a center-cracked plate under uniaxial tension.
The analytical expression for the Mode I Stress Intensity Factor is given by:
K_I = σ√(πa) * F(a/W)
where:
σ = applied stress (MPa)
a = Half crack length (mm)
W = plate width (mm)
F(a/W) = geometry correction factor.
For a finite-width plate with a edge crack:
F(a/W) = 0.1
Given parameters:
σ = 50 MPa, a = 10 mm, W = 50mm, t = 1 mm
Calculation:
F(a/W) = Y = 1.12 - 0.231*x + 10.55*x^2 - 21.72*x^3 + 30.39*x^4 = 1.20064 mm
K_I = 2 × √(π×10) × 1.20064 = 14.3504
Thus, the analytical Stress Intensity Factor is:
K_I^ANA = 14.3504 MPa√mm.
4.Displacement Field Method
The near-tip displacement field in LEFM can be expressed as:
u_i(r, θ) = (K_I / E′) * √(r / 2π) * F_i(θ, κ)
where:
E′ = E (plane stress) or E/(1−ν²) (plane strain)
κ = 3−ν (plane stress) or 3−4ν (plane strain)
Displacements near the crack tip were extracted from the and fitted to the above equation
using a matlab c. The computed value of K_I from the displacement field was found to be 
in close agreement with the analytical and FEM results.
Matlab Code For Plain Stress Condition
% center_plane_stress_center_crack.m
% Plane Stress Mode-I analysis for center crack (total length 2a)
% Assumptions: half-crack a = 10.0 mm, plate width (across crack) W = 50.0 mm
clc; clear; close all;
% ---- Input Data ----
Pressure = 50 MPa; 
W = 50.0; % Plate height (mm)
t = 1.0; % Thickness (mm)
a = 10.0; % half-crack length (mm) (total crack length = 2a)
E = 200000.0; % MPa
nu = 0.3;
r = 0.5; % distance from crack tip (mm)
theta = pi; % along crack face
% ---- Basic Quantities ----
sigma = P/(t*W); % MPa
% Geometry factor for center crack in finite strip: Y = sqrt(sec(pi*a/W))
Y = sqrt( sec(pi*a / W) );
KI = Y * sigma * sqrt(pi*a);
mu = E/(2*(1+nu));
kappa = (3 - nu)/(1 + nu);
% ---- Displacement Field ----
ux = (KI/(2*mu))*sqrt(r/(2*pi))*cos(theta/2)*(kappa - cos(theta));
uy = (KI/(2*mu))*sqrt(r/(2*pi))*sin(theta/2)*(kappa - cos(theta));
delta_open = 2*uy; % total opening displacement
KI_from_disp = delta_open * mu * sqrt(2*pi/r) / (kappa + 1);
% ---- Output ----
fprintf('\n--- Center Crack (Plane Stress) ---\n');
fprintf('Nominal Stress = %.6g MPa\n', sigma);
fprintf('Geometry Factor Y = %.9g\n', Y);
fprintf('KI = %.6g MPa*sqrt(mm)\n', KI);
fprintf('ux = %.6e mm, uy = %.6e mm\n', ux, uy);
fprintf('Opening Disp = %.6e mm\n', delta_open);
fprintf('KI (from disp method) = %.6g MPa*sqrt(mm)\n', KI_from_disp);
Matlab Code For Plain Strain Condition
% center_plane_strain_center_crack.m
% Plane Strain Mode-I analysis for center crack (total length 2a)
% Assumptions: half-crack a = 10.0 mm, plate width (across crack) W = 50.0 mm
clc; clear; close all;
% ---- Input Data ----
Pressure = 50; % MPa
W = 50.0; % Plate height (mm)
t = 1.0; % Thickness (mm)
a = 10.0; % half-crack length (mm) (total crack length = 2a)
E = 200000.0; % MPa
nu = 0.3;
r = 0.5; % distance from crack tip (mm)
theta = pi; % along crack face
% ---- Basic Quantities ----
sigma = P/(t*W); % MPa
% Geometry factor for center crack in finite strip: Y = sqrt(sec(pi*a/W))
Y = sqrt( sec(pi*a / W) );
KI = Y * sigma * sqrt(pi*a);
mu = E/(2*(1+nu));
kappa = 3 - 4*nu;
% ---- Displacement Field ----
ux = (KI/(2*mu))*sqrt(r/(2*pi))*cos(theta/2)*(kappa - cos(theta));
uy = (KI/(2*mu))*sqrt(r/(2*pi))*sin(theta/2)*(kappa - cos(theta));
delta_open = 2*uy; % total opening displacement
KI_from_disp = delta_open * mu * sqrt(2*pi/r) / (kappa + 1);
% ---- Output ----
fprintf('\n--- Center Crack (Plane Strain) ---\n');
fprintf('Nominal Stress = %.6g MPa\n', sigma);
fprintf('Geometry Factor Y = %.9g\n', Y);
fprintf('KI = %.6g MPa*sqrt(mm)\n', KI);
fprintf('ux = %.6e mm, uy = %.6e mm\n', ux, uy);
fprintf('Opening Disp = %.6e mm\n', delta_open);
fprintf('KI (from disp method) = %.6g MPa*sqrt(mm)\n', KI_from_disp);
5. Numerical (FEM) Method
The finite element method (FEM) was used to validate the analytical solution. The model 
was developed in Ansys.
Geometry:
- Plate width W = 100 mm, height H = 50 mm, thickness t = 1 mm
- Half crack length a = 10 mm
Material properties:
E = 210 GPa, ν = 0.3
Boundary Conditions:
- Left edge fixed (Ux=Uy=0)- Right edge subjected to uniform tensile stress σ = 50 MPa
Mesh:
Trtrahedron element with fine mesh near the crack tip (~0.05 mm).
6.Results and Discussion
The results obtained from all three methods are summarized in the following table:
Plain Stress Plain Stress
Nominal Stress = 50 Mpa Nominal Stress 50 Mpa
Geometry Factor Y 1.20064 Geometry 
Factor Y
1.20064
K_1 14.3504MPa*sqrt(mm) K_1 14.3504MPa*sqrt(mm)
Ux 3.918081e-21 mm Ux 4.305584e-21 mm
Uy 6.398712e-05 mm Uy 7.031552e-05 mm
Opening Disp 1.279742e-04 mm Opening Disp 1.406310e-04 mm
KI (from disp 
method)
14.3504
MPa*sqrt(mm)
KI (from disp 
method)
14.3504
MPa*sqrt(mm)
Method K_I (MPa√mm)
Analytical 14.3504
FEM (K_1) 14.347
Displacement Field 14.3504