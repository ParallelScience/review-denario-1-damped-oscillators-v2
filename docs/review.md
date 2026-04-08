# **_Skepthical_** review: *Quantifying the Temporal Limits of Parameter Identifiability in Damped Harmonic Oscillators*

## Summary

The paper studies how the *time window of observation* affects which parameters of an underdamped harmonic oscillator are most influential/“identifiable,” using the total mechanical energy signal \(E(t)=\tfrac12 m v(t)^2+\tfrac12 k x(t)^2\) (Sec. 1–2, Eq. (1)). Across 20 simulated oscillators, the authors compute Jacobian-based local sensitivities of \(E(t)\) with respect to mass \(m\) and damping \(b\) (Sec. 2.2), define an “Information Horizon” \(T_H\) as the first time the damping sensitivity overtakes the mass sensitivity via \(R(t)=S_b(t)/S_m(t)=1\) (Sec. 2.3), and report that \(T_H\) occurs early (mean \(\approx 0.76\,\text{s}\)), with larger damping ratio \(\zeta\) yielding earlier \(T_H\) and lower peak sensitivity (Sec. 3.1–3.3, Sec. 4). The overall idea—making explicit that parameter influence can be time-dependent and thus relevant to experiment design—is well-motivated and clearly presented. However, several core elements are currently under-specified or conceptually overstated: the dynamical/simulation setup and sensitivity computation (especially \(\partial E/\partial b\)) are not reproducible as written; the sensitivity measures and the horizon criterion are dimensionally inconsistent without normalization; and the manuscript’s use of “identifiability/information” is not yet tied to a measurement/noise model or an inference procedure. Addressing these points (and clarifying what is assumed measured: \(E\) vs \(x,v\)) would substantially strengthen both rigor and practical relevance.

## Strengths

- Well-motivated focus on *time-dependent* parameter influence/identification windows, with a clear narrative in the Introduction (Sec. 1) and a coherent end-to-end workflow (Sec. 2 → Sec. 3).
- The “Information Horizon” concept is intuitive and potentially useful as an experiment-design diagnostic (Sec. 2.3, Sec. 3.1).
- Using time-resolved sensitivities to illustrate early-transient vs late-time dominance is pedagogically effective and aligns with physical intuition for decaying oscillations (Sec. 3.1–3.2).
- Figures 1–2 convey the qualitative trends (early large sensitivity, transition from mass- to damping-dominated behavior), and the damping-ratio trend analysis is a reasonable first attempt at summarizing across systems (Sec. 3.3).
- The paper is generally readable and consistent in high-level notation for \(E(t)\) and the Jacobian idea (Eqs. (1)–(4)), making the approach accessible to a broad audience.

## Major issues

1.  **Reproducibility gap: key dynamical model and simulation details are missing or ambiguous (Sec.** 2.1), making headline quantitative claims (e.g., mean \(T_H\approx 0.76\,\text{s}\), range \([0.60,0.92]\,\text{s}\), regression/correlation in Sec. 3.3) non-auditable. The manuscript does not clearly specify the governing equation (e.g., \(m\ddot x+b\dot x+kx=0\) vs forcing), the exact parameterization used to generate the 20 oscillators (ranges/distributions for \(m,b,k\), any constraints such as fixed \(\omega_n\)), initial conditions \(x(0),v(0)\), solver/integration method, time step/sampling rate, and number of discrete time points used in sensitivity calculations.
    
    *Recommendation:* In Sec. 2.1, explicitly state: (a) the ODE (and whether any forcing/noise is present); (b) the initial conditions and how they are chosen; (c) whether solutions are analytic or numerical and, if numerical, the solver, \(\Delta t\), and simulation duration; (d) the discrete sampling grid for \(E(t)\) and sensitivities; and (e) how the 20 oscillator parameter sets are generated (ranges/distributions, any coupling/constraints, and resulting ranges of \(\omega_n\) and \(\zeta\)). Provide a parameter table (main text or supplement) so Sec. 3.1–3.3 statistics can be independently reproduced.

2.  **Central methodological ambiguity: \(\partial E/\partial b\) (and in general “Jacobian of \(E\)”) is not defined/computed in a mathematically checkable way (Sec.** 2.2–2.3). As written, Eq. (1) has no explicit dependence on \(b\), so \(\partial E/\partial b\neq 0\) only through the implicit dependence \(x(t;b),v(t;b)\). The paper does not state whether derivatives are computed via sensitivity ODEs, closed-form differentiation, automatic differentiation, or finite differences; nor does it discuss numerical error, step-size selection, smoothing, or stability. This affects \(S_b(t)\), \(R(t)\), and therefore \(T_H\) (Sec. 3.1–3.2).
    
    *Recommendation:* In Sec. 2.2, define \(E(t;m,b,k)=\tfrac12 m v(t;m,b,k)^2+\tfrac12 k x(t;m,b,k)^2\) and explicitly state that derivatives are total derivatives through the state: e.g., \(\tfrac{\partial E}{\partial b}=m v\,\tfrac{\partial v}{\partial b}+k x\,\tfrac{\partial x}{\partial b}\). Then specify how \(\tfrac{\partial x}{\partial b},\tfrac{\partial v}{\partial b}\) (and similarly for \(m\)) are obtained: (i) forward sensitivity equations (include the augmented ODEs in-text or appendix), or (ii) finite differences (scheme, perturbation size, convergence checks), or (iii) AD (tooling + validation). Also clarify whether \(\partial E/\partial m\) includes both the explicit term \(\tfrac12 v^2\) and the implicit dependence of \(x,v\) on \(m\).

3.  **Dimension/unit inconsistency: the sensitivity norm \(S(t)=\|J(t)\|_2\) and the ratio \(R(t)=S_b(t)/S_m(t)\) compare/aggregate derivatives with different physical units (Sec.** 2.2–2.3; Eq. (3)/(6), Eq. (4)/(5); Figures 1–2). As a result, both the magnitude of \(S(t)\) and the threshold \(R(t)=1\) are parameterization- and unit-dependent, undermining the quantitative meaning of “dominance” and comparability across oscillators.
    
    *Recommendation:* Replace raw derivatives with dimensionless or uncertainty-weighted quantities. Options include: (a) relative sensitivities \(\tilde S_m=\left|\tfrac{m}{E}\tfrac{\partial E}{\partial m}\right|\), \(\tilde S_b=\left|\tfrac{b}{E}\tfrac{\partial E}{\partial b}\right|\); (b) log-sensitivities \(\left|\partial \ln E/\partial \ln \theta\right|\); or (c) error-propagation/FIM-style weighting with parameter prior uncertainties \(\sigma_m,\sigma_b\): \(\mathrm{Var}[E(t)]\approx J(t)\Sigma_\theta J(t)^\top\). Redefine \(T_H\) using equality of *comparable* (dimensionless/weighted) contributions and update Figures 1–2 axes/units accordingly.

4.  **“Identifiability/information” claims are not yet supported by an explicit measurement model, noise model, or estimation procedure (Sec.** 1, Sec. 2.2–2.4, Sec. 3.1, Sec. 4). Local sensitivity magnitude is related to parameter influence but is not equivalent to structural/practical identifiability, nor to “information” in the Fisher sense. Moreover, the discussion does not address that later times may have higher *relative* damping sensitivity but lower SNR due to decay, which can reduce practical information under noise (big-picture concern raised by the current framing in Sec. 4).
    
    *Recommendation:* Either (A) reframe the paper consistently as *time-varying local sensitivity of the energy output* and soften language in Abstract/Sec. 1/Sec. 4 (avoid “fundamental limit,” “operational limit,” etc.), or (B) add a minimal inference layer: assume a noise model for the observable (\(E\) or \(x,v\)), compute a time-window Fisher information (or cumulative information) for \(m\) and \(b\), and/or run a parameter-estimation experiment (fit \(m,b\) on early vs late windows) showing estimation variance/bias changes around \(T_H\). If retaining the term “Information Horizon,” justify it via an information measure (even in a simplified setting).

5.  **Observability/measurement realism: it is unclear what is assumed measured and how \(E(t)\) is formed when \(m\) is unknown (Sec.** 1–2). In many setups, \(E(t)\) is not directly measured; it is derived from \(x(t),v(t)\) and parameters (including \(m\) and \(k\)). If \(m\) is the unknown, constructing \(\tfrac12 m v^2\) already requires \(m\), creating a circularity unless \(E\) is treated as a model-predicted quantity rather than a direct measurement.
    
    *Recommendation:* Clarify the observation model in Sec. 2.1–2.2: is the ‘output’ assumed to be (i) directly measured energy (with what sensor/proxy), (ii) energy computed from measured \(x,v\) using nominal parameters, or (iii) purely model-based analysis of \(E\) as a derived signal? If the goal is system identification, consider also presenting results for more standard observables (\(x(t)\), \(v(t)\), or envelope/decay rate) or discuss explicitly why energy is the chosen output and what is gained/lost compared to displacement-based identification.

6.  **The operational definition of \(T_H\) and the definitions of \(S_m,S_b\) are inconsistent/underspecified (Sec.** 2.2 vs Sec. 3.1), affecting the existence and timing of the horizon. The manuscript alternates between absolute-value and signed derivatives for \(S_m,S_b\); does not specify how \(T_H\) is extracted on a discrete time grid; and does not describe handling for multiple crossings, non-crossing cases, or numerical oscillations in \(R(t)\).
    
    *Recommendation:* Unify the definition of \(S_m,S_b\) across Sec. 2.2–2.3 and Sec. 3.1 (preferably magnitudes if the goal is “dominance”). In Sec. 2.3, provide an explicit algorithm for \(T_H\): e.g., “the smallest sampled time where \(R(t)\ge 1\), with linear interpolation between neighboring samples,” plus rules for multi-crossings (first sustained crossing; smoothing/hysteresis), and for non-crossing trajectories (report as missing and discuss). Report variability for \(T_H\) (SD/CI) in Sec. 3.1 and mention any atypical cases.

7.  **Scope limitations are not sufficiently reflected in conclusions: the study uses 20 noise-free simulations and focuses on only \(m\) and \(b\) while stiffness \(k\) is present in both dynamics and energy (Sec.** 2.1–2.2, Sec. 3, Sec. 4). The exclusion of \(k\) is not justified, and robustness to parameter ranges, initial conditions, or measurement noise is not examined, yet conclusions are phrased broadly.
    
    *Recommendation:* In Sec. 2.2 and Sec. 4, explicitly justify excluding \(k\) (e.g., assumed known/calibrated; focus on inertial vs dissipative). Ideally add \(\partial E/\partial k\) and discuss whether multiple crossovers/horizons appear when \(k\) is included. Add at least one robustness check: more oscillators (\(n\gg 20\)), alternative parameter distributions (including very low and near-critical damping), alternative initial conditions/amplitudes, and a simple measurement-noise experiment to test whether \(T_H\) remains meaningful when amplitude decays.

8.  **Positioning within existing literature is thin, making novelty and interpretation of “Information Horizon” unclear (Sec.** 1–4). There is no dedicated Related Work section tying the approach to established sensitivity analysis for ODEs, structural/practical identifiability, observability windows, or Fisher-information-based time-window design.
    
    *Recommendation:* Add a short Related Work subsection (end of Sec. 1 or as Sec. 2.5) covering: (i) local/global sensitivity analysis in dynamical systems, (ii) identifiability/observability for oscillatory second-order systems, and (iii) time-window selection/optimal experimental design via Fisher information. In Sec. 1 and Sec. 4, position \(T_H\) explicitly as a heuristic crossover metric (unless upgraded to an information-based measure per above).

## Minor issues

1.  Definition of damping ratio \(\zeta\) is not stated, and its interaction with \(\omega_n\) is unclear (Sec. 2.1, Sec. 2.4, Sec. 3.3). This complicates interpretation of the reported linear relationship between \(T_H\) (or \(S_{\max}\)) and \(\zeta\).
    
    *Recommendation:* In Sec. 2.1, define \(\zeta\) explicitly (e.g., \(\zeta=b/(2\sqrt{km})\)) and state whether \(\omega_n=\sqrt{k/m}\) is held fixed or varies. In Sec. 3.3, briefly discuss whether the trend persists when controlling for \(\omega_n\) or using a different parameterization (e.g., fixed \(\omega_n\), varying \(\zeta\)).

2.  Statistical reporting for regressions/correlations is incomplete given \(n=20\) (Sec. 2.4, Sec. 3.3). The paper reports slope, \(R^2\), and Pearson \(\rho\) but not uncertainty or assumption checks.
    
    *Recommendation:* Add confidence intervals/standard errors and p-values for slopes and correlations (Sec. 3.3), and include the scatter plots used for the reported fits. Note explicitly that results are indicative due to small \(n\), or increase \(n\) to stabilize estimates.

3.  Figures: Figure 1 is visually cluttered and does not directly substantiate the \(T_H\) claim (crossings are not annotated); Figure 2 heatmap lacks ordering/annotations that would reveal cohort structure (Sec. 3.1–3.2).
    
    *Recommendation:* In Figure 1, add vertical markers for \(T_H\) per trajectory (or show median/quantiles with shaded bands) and/or include an inset histogram of \(T_H\). Consider an early-time zoom (e.g., 0–2 s). In Figure 2, order oscillators by \(\zeta\) or \(T_H\) and overlay \(T_H\) markers to make patterns interpretable.

4.  Key reported numeric claims are not easily auditable (Sec. 3.1–3.3): the list of per-oscillator \(T_H\) values and the per-oscillator pairs used for regressions/correlations are not provided.
    
    *Recommendation:* Provide a table or supplement with per-oscillator parameters (\(m,b,k,\zeta,\omega_n\)), computed \(T_H\), \(S_{\max}\), and any regression inputs so readers can reproduce Sec. 3.1–3.3.

5.  Terminology: “energy manifold” is used though \(E(t)\) is treated as a scalar time series (Sec. 1, Sec. 2.2, Sec. 3.2, Sec. 4).
    
    *Recommendation:* Replace with “energy trajectory/signal” or define precisely what manifold is meant (e.g., surface over \((t,\theta)\)).

6.  Notation consistency: \(S(t)\) is defined in Sec. 2.2 and reintroduced in Sec. 3.2; \(S_m,S_b\) switch between absolute and signed forms (Sec. 2.2 vs Sec. 3.1).
    
    *Recommendation:* Cross-reference earlier definitions when restating (e.g., “recalling Eq. (3)…”), and enforce one convention for signed vs magnitude sensitivities throughout equations, text, and captions.

## Very minor issues

1.  Presentation/typography inconsistencies (headings, quotation marks for “Information Horizon,” unit spacing such as “1s” vs “1 s,” and \(R^2\) formatting) across Sec. 2–4 and figure captions.
    
    *Recommendation:* Standardize section heading styles, quotation mark style, and SI unit formatting (e.g., “\(t<1\,\text{s}\)”). Use consistent mathematical typesetting for \(T_H\), \(\zeta\), and \(R^2\) in text, tables, and captions.

2.  Norm notation \(\|\cdot\|_2\) is used without explicitly stating it is the Euclidean norm (Sec. 2.2).
    
    *Recommendation:* Add a brief note after Eq. (3) that \(\|\cdot\|_2\) denotes the Euclidean (L2) norm.

3.  Figure labeling polish: legends/units are not always explicit (e.g., Figure 1 y-axis units/dimensionless status; Figure 2 colorbar tick labeling).
    
    *Recommendation:* Ensure all axes and colorbars clearly state units or explicitly state nondimensionalization; add in-plot legends and meaningful colorbar tick values.


## Mathematical consistency audit

This section audits **symbolic/analytic** mathematical consistency (algebra, derivations, dimensional/unit checks, definition consistency).

**Maths relevance:** substantial

The paper’s analytic core is a Jacobian-based sensitivity analysis of oscillator energy with respect to parameters (mass and damping), and a derived crossover time (“Information Horizon”) defined when the damping sensitivity overtakes mass sensitivity. The main mathematical concerns are (i) missing definitions/derivations for parameter derivatives through the implicit dependence of x(t), v(t) on parameters, especially for damping b, and (ii) dimensional inconsistency in taking Euclidean norms and ratios of derivatives taken with respect to parameters of different units.

### Checked items

1.  ✔ **Total mechanical energy definition** (Eq. (1), Sec. 2.1, p.2)
    
    - **Claim:** Total mechanical energy is E(t)=1/2 m v(t)^2 + 1/2 k x(t)^2.
    - **Checks:** symbol/notation consistency, dimensional/units sanity
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** x(t) is displacement, v(t)=dx/dt is velocity, m is mass, k is stiffness
    - **Notes:** Standard kinetic + potential energy expression; units are consistent if v is velocity and x is displacement.

2.  ⚠ **Jacobian definition with respect to parameters** (Eq. (2), Sec. 2.2, p.3)
    
    - **Claim:** Define J(t)=[∂E/∂m, ∂E/∂b]^T.
    - **Checks:** definition consistency, implicit-dependence check
    - **Verdict:** UNCERTAIN; confidence: medium; impact: critical
    - **Assumptions/inputs:** E depends on parameters through x(t), v(t) and possibly explicitly, Partial derivatives are evaluated at each time t
    - **Notes:** Given Eq. (1), E has no explicit b-dependence; ∂E/∂b is only nonzero if x(t),v(t) depend on b and the chain rule is used. The paper does not specify whether derivatives treat x,v as fixed or include state sensitivities, nor does it provide the needed sensitivity equations/closed-form expressions.

3.  ✖ **Sensitivity index as Euclidean norm** (Eq. (3), Sec. 2.2, p.3)
    
    - **Claim:** Overall sensitivity is S(t)=||J(t)||2.
    - **Checks:** dimensional/units consistency, definition adequacy
    - **Verdict:** FAIL; confidence: high; impact: critical
    - **Assumptions/inputs:** J(t) is treated as a vector in a metric space with standard Euclidean metric
    - **Notes:** The components ∂E/∂m and ∂E/∂b have different physical units (energy per mass vs energy per damping). A Euclidean norm implicitly assumes commensurate units/scales; without normalization/weighting, S(t) is not dimensionally meaningful and depends on parameter units/parameterization.

4.  ✔ **Partial sensitivities as magnitudes** (Sec. 2.2, p.3)
    
    - **Claim:** Define Sm(t)=|∂E/∂m| and Sb(t)=|∂E/∂b|.
    - **Checks:** notation consistency
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** Absolute value is intended to measure magnitude of sensitivity
    - **Notes:** Definition is internally clear, but later sections drop absolute values (see separate item).

5.  ✖ **Sensitivity ratio definition** (Eq. (4), Sec. 2.3, p.3)
    
    - **Claim:** Define R(t)=Sb(t)/Sm(t).
    - **Checks:** dimensional/units consistency, well-posedness (scale invariance)
    - **Verdict:** FAIL; confidence: high; impact: critical
    - **Assumptions/inputs:** Sm,Sb are comparable quantities
    - **Notes:** R(t) is not dimensionless because Sb and Sm are derivatives with respect to parameters of different units; thus the ratio depends on the chosen units/parameter scaling. The criterion R(t)=1 is arbitrary unless a normalization (e.g., relative or uncertainty-weighted sensitivities) is introduced.

6.  ✖ **Information Horizon definition via R(t)=1** (Sec. 2.3, p.3)
    
    - **Claim:** Define TH such that R(TH)=1 (crossover where damping sensitivity exceeds mass sensitivity).
    - **Checks:** definition adequacy, units/scale invariance, edge-case sanity
    - **Verdict:** FAIL; confidence: high; impact: critical
    - **Assumptions/inputs:** R(t) is dimensionless and scale-invariant, R(t) is well-defined (Sm(t)≠0 at crossover), R(t) crosses 1 (monotonicity not required but crossing must exist)
    - **Notes:** Because R(t) is not dimensionless, the horizon time TH is not invariant to unit changes (e.g., rescaling b or m units) and is therefore not a mathematically well-defined, physical crossover without normalization. Also, existence/uniqueness conditions for TH are not discussed (e.g., if Sm(t)=0 at some t or multiple crossings occur).

7.  ✔ **Reuse of ratio in Results** (Eq. (5), Sec. 3.1, p.4)
    
    - **Claim:** Restates R(t)=Sb(t)/Sm(t) for the horizon discussion.
    - **Checks:** definition consistency
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** Same R(t) as in Methods
    - **Notes:** Consistent restatement, though it inherits the dimensional inconsistency noted for Eq. (4).

8.  ✔ **Expanded norm formula** (Eq. (6), Sec. 3.2, p.4)
    
    - **Claim:** S(t)=sqrt(Sm(t)^2+Sb(t)^2).
    - **Checks:** algebra between definitions, notation consistency
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** S(t)=||J||2 and Sm,Sb are the two components (or magnitudes) used in the norm
    - **Notes:** Algebra matches the 2-norm for a 2D vector. However, the dimensional inconsistency of mixing units remains (see Eq. (3) item).

9.  ⚠ **Inconsistency of absolute values in Results** (Sec. 3.1, p.4)
    
    - **Claim:** States Sm(t)=∂E/∂m and Sb(t)=∂E/∂b (without absolute values) after earlier defining magnitudes.
    - **Checks:** definition consistency
    - **Verdict:** UNCERTAIN; confidence: high; impact: moderate
    - **Assumptions/inputs:** Either signed or magnitude convention is intended
    - **Notes:** If Sm,Sb are meant to be magnitudes, dropping |·| is a notation error; if signed derivatives are intended, then R(t) could be negative and the horizon criterion needs revision. The manuscript does not clarify which is used in computations/figures.

10.  ✔ **Peak sensitivity definition** (Sec. 2.4, p.3)
    
    - **Claim:** Define Smax = max(S(t)).
    - **Checks:** definition consistency
    - **Verdict:** PASS; confidence: high; impact: minor
    - **Assumptions/inputs:** S(t) is defined for all sampled times and bounded
    - **Notes:** Well-defined as a functional of S(t) over a finite time window.

11.  ⚠ **Damping ratio symbol usage** (Sec. 2.1 and Sec. 3.3, pp.2 and 5-6)
    
    - **Claim:** Uses ζ as a damping ratio to characterize the damping regime and relate it statistically to TH and Smax.
    - **Checks:** missing definition check, symbol consistency
    - **Verdict:** UNCERTAIN; confidence: high; impact: moderate
    - **Assumptions/inputs:** ζ is computed from (m,b,k) in a consistent way across oscillators
    - **Notes:** ζ is never defined mathematically in the text provided, preventing verification of any analytic linkages or constraints (e.g., underdamped condition).

### Limitations

- The provided PDF text does not include the governing differential equation for the damped oscillator or any explicit solution x(t), v(t); without this, ∂x/∂m, ∂x/∂b, ∂v/∂m, ∂v/∂b (and thus ∂E/∂m, ∂E/∂b under implicit dependence) cannot be analytically verified.
- Figures are referenced but not analytically checkable from text alone; no explicit formulas for Sm(t), Sb(t) trajectories are given.
- No parameter-uncertainty model (e.g., σm, σb) is specified, which is necessary to make cross-parameter comparisons dimensionally meaningful.


## Numerical results audit

This section audits **numerical/empirical** consistency: reported metrics, experimental design, baseline comparisons, statistical evidence, leakage risks, and reproducibility.

No executable numeric checks were completed (execution error). Several internal-consistency targets were identified (ranges/thresholds and cross-referenced constants), but regression/correlation and distributional claims remain unverified without underlying data.

### Checked items

1.  ⚠ **C1** (Page 4 (Results §3.1): 'mean time of 0.76 s ... between 0.60 s and 0.92 s')
    
    - **Claim:** Across the population of oscillators, the Information Horizon occurred at a mean time of 0.76 s, with all instances occurring between 0.60 s and 0.92 s.
    - **Checks:** range_contains_mean
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: verify min_TH <= mean_TH <= max_TH using the stated values (0.60, 0.76, 0.92). Not executed due to runner error.

2.  ⚠ **C2** (Page 6 (Table 1) vs Page 4 (§3.1) vs Page 7 (Conclusions): Mean TH)
    
    - **Claim:** Mean Information Horizon (TH) is reported as 0.76 s in multiple sections (Results/Table 1/Conclusions).
    - **Checks:** cross_reference_constant_match
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: confirm all reported mean TH values match within rounding tolerance. Not executed due to runner error.

3.  ⚠ **C3** (Page 2 (§2.1) vs Page 4/6: simulation window)
    
    - **Claim:** The paper states a population of 20 oscillators simulated over a 20-second window/interval, repeated across sections/captions.
    - **Checks:** cross_reference_integer_match
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: equality of oscillator count and simulation duration across references. Not executed due to runner error.

4.  ⚠ **C4** (Page 4 (Eq. 6) vs Page 3 (§2.2): definition of S(t))
    
    - **Claim:** S(t) is defined as the Euclidean norm of the Jacobian vector; later written explicitly as S(t) = sqrt(Sm(t)^2 + Sb(t)^2).
    - **Checks:** algebraic_equivalence
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned symbolic consistency check (||J||2 vs sqrt(Sm^2+Sb^2) given Sm=|∂E/∂m| and Sb=|∂E/∂b|). Not executed due to runner error.

5.  ⚠ **C5** (Page 3 (§2.3) vs Page 4 (§3.1): Information Horizon condition)
    
    - **Claim:** Information Horizon TH is defined where R(TH)=1 with R(t)=Sb(t)/Sm(t).
    - **Checks:** definition_consistency
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned definition match check across sections (R(t)=Sb/Sm and horizon condition R(TH)=1). Not executed due to runner error.

6.  ⚠ **C6** (Page 5 (§3.3) vs Page 6 (Table 1): regression/correlation values)
    
    - **Claim:** The slope, R^2, and correlation are stated in text and repeated in Table 1; they should match numerically.
    - **Checks:** cross_reference_constant_match
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: compare text vs Table 1 for slope −0.8347, R2 0.3326, ρ −0.5221 within tolerance. Not executed due to runner error.

7.  ⚠ **C7** (Page 5 (Figure 1 caption) vs Page 4 (§3.1): timing claim)
    
    - **Claim:** Information Horizon consistently occurs early: Figure 1 caption says t < 1 s; Results give range 0.60 s to 0.92 s.
    - **Checks:** inequality_consistency
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: verify 0.92 < 1.0. Not executed due to runner error.

8.  ⚠ **C8** (Page 4 (§3.2) vs Page 6 (Figure 2 caption): transient-phase timing)
    
    - **Claim:** Sensitivity is highest during the initial transient phase, described as typically within the first two seconds / (t < 2 s).
    - **Checks:** threshold_phrase_consistency
    - **Verdict:** UNCERTAIN
    - **Notes:** Planned check: confirm both thresholds are 2 s. Not executed due to runner error.

### Limitations

- Only parsed text was available; no access to underlying numeric datasets for the 20 oscillators, so regressions/correlations and distributional claims cannot be recomputed.
- Figure-based numeric verification is excluded (no plot-pixel extraction), so time-series claims shown only in figures cannot be quantitatively checked.
- The dynamical equations of motion and parameter distributions for the simulated oscillators are not provided, limiting verification of sensitivity derivatives and any physics-based recomputation beyond algebraic identity checks.
- Execution error prevented running the planned checks: "Sandbox policy violation: from-import of 'typing' is not allowed".
