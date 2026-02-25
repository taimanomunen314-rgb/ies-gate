# IES-GATE Specification v1.0 (Frozen)
Continuity-aware Aggregation and Differential Evaluation Legitimacy

## 0. Scope / Non-Scope (Hard)
### 0.1 Scope
IES-GATE defines a protocol to **permit** aggregation of observations only on intervals judged to share a common causal structure, i.e.,
\[
T(x) \;\to\; T(x \mid \text{continuity})
\]
It is a **meta-specification** for legitimacy of aggregation/evaluation, not a model of the world.

### 0.2 Non-Scope (MUST NOT)
- MUST NOT be interpreted as physics, state-control, or a generative theory.
- MUST NOT be used to tune or justify an external physical theory by changing gate choices.
- MUST NOT claim to identify “true causality”; it only enforces auditable aggregation legitimacy.

---

## 1. Data Model
Let the observed sequence be
\[
\mathcal{D}=\{(t_i, x_i, z_i)\}_{i=1}^n,
\]
where \(t_i\) is an index (time or ordered index), \(x_i\in\mathbb{R}^d\) is the target signal, and \(z_i\) are optional covariates/metadata.

We consider intervals (windows) as index sets:
\[
\mathcal{I}=[i:j] := \{i,i+1,\dots,j\}.
\]

---

## 2. Partition / Window Family (Fixed in v1.0)
IES-GATE v1.0 requires selecting **exactly one** of the following window constructions **ex ante** and reporting it as part of the protocol:

### Option S (Fixed-Length Sliding Windows) — Canonical in v1.0
A fixed length \(L\) and stride \(s\):
\[
\mathfrak{P}=\{[1: L], [1+s: L+s], \dots \}.
\]

**Fixed parameters (v1.0):**
- \(L=256\), \(s=64\).

**Constraint (v1.0):**
- The window construction and its parameters MUST be pre-registered and MUST NOT be data-adapted.

---

## 3. Continuity Predicate (Gate)
Define a continuity predicate
\[
C(\mathcal{I})\in\{0,1\},
\]
interpreted as: “interval \(\mathcal{I}\) is eligible for aggregation under the continuity criterion.”

### 3.1 Allowed Form (v1.0)
v1.0 permits only **auditable, explicit** predicates. The predicate MUST be specified as a deterministic function of \(\{(t_i,x_i,z_i)\}_{i\in\mathcal{I}}\) and fixed thresholds.

**Prohibited in v1.0:** opaque/learned classifiers whose decision boundary is not fully specified and reproducible.

### 3.2 Reference Predicate (C_ref) — Fixed in v1.0
For a 1D signal \(x\), define \(C_{\text{ref}}(\mathcal{I})=1\) iff all conditions hold:

1) In-window scale stable:  
- \(\mathrm{MAD}(x_{\mathcal{I}})\ge m_{\min}\), with \(m_{\min}=10^{-12}\).

2) Boundary change weak:  
Let \(q=0.2\). Let \(x_{\text{first}}\) be the first \(q\) fraction of \(\mathcal{I}\) and \(x_{\text{last}}\) the last \(q\) fraction.  
\[
\left|\mathrm{median}(x_{\text{first}})-\mathrm{median}(x_{\text{last}})\right|\le \delta_{\mathrm{cp}},
\quad
\delta_{\mathrm{cp}}=\lambda\cdot \mathrm{MAD}(x_{\mathcal{I}}),\ \lambda=1.0.
\]

3) Rough whiteness of first-difference:  
Let \(\Delta x_t=x_t-x_{t-1}\) within \(\mathcal{I}\).  
\[
\left|\mathrm{corr}(\Delta x_t,\Delta x_{t-1})\right|\le \rho_{\max},\quad \rho_{\max}=0.2.
\]

---

## 4. Gate Operator and Coverage
Given \(\mathfrak{P}\), define the accepted subset:
\[
G(\mathfrak{P}) := \{\mathcal{I}\in\mathfrak{P}: C(\mathcal{I})=1\}.
\]

Define coverage:
\[
\mathrm{coverage}(\mathfrak{P})
=\frac{\sum_{\mathcal{I}\in G(\mathfrak{P})}|\mathcal{I}|}
       {\sum_{\mathcal{I}\in \mathfrak{P}}|\mathcal{I}|}.
\]

---

## 5. Continuity-Aware Aggregation
Let \(T\) be a target statistic (mean, variance, correlation, regression coefficient, etc.).

IES-GATE defines the gate-aware statistic
\[
T_{\text{gate}} := T\Big(\{x_i\}_{i\in \cup_{\mathcal{I}\in G(\mathfrak{P})}\mathcal{I}}\Big),
\]
with a fully specified weighting/handling rule.

### 5.1 Weighted Mean (Canonical Example)
Let weights \(w_i>0\) be fixed or derived deterministically from metadata \(z_i\).
\[
\widehat{\mu}_{\text{gate}}=
\frac{\sum_{\mathcal{I}\in G(\mathfrak{P})}\sum_{i\in\mathcal{I}} w_i x_i}
     {\sum_{\mathcal{I}\in G(\mathfrak{P})}\sum_{i\in\mathcal{I}} w_i}.
\]

**Constraint:** \(w_i\) construction MUST be fixed and reported.

---

## 6. Invalidity Rules (Fixed)
IES-GATE v1.0 enforces invalidity outputs:

- Aggregation invalid if \(\mathrm{coverage} < \tau_{\min}\), with \(\tau_{\min}=0.30\).  
  Output MUST be **“aggregation invalid”** and MUST NOT emit numeric \(T_{\text{gate}}\).

---

## 7. ΔGATE: Legitimacy of Differential Evaluation (Fixed)
For comparing two conditions/datasets \(A,B\), define separate accepted sets \(G_A, G_B\).

**ΔGATE rule (v1.0):**
Differential evaluation is permitted only on the common accepted domain:
\[
\Delta T_{\text{gate}}
=
T_A\big(G_A\cap G_B\big)
-
T_B\big(G_A\cap G_B\big).
\]

### 7.1 Invalidity (Fixed)
- Comparison invalid if common accepted coverage \(<\tau_{\min}^{\Delta}\), with \(\tau_{\min}^{\Delta}=0.20\).  
  Output MUST be **“comparison invalid”** and MUST NOT emit numeric \(\Delta T_{\text{gate}}\).

---

## 8. Required Reporting Checklist (MUST)
1. Window construction and fixed parameters: Option S, \(L=256\), \(s=64\).
2. Exact definition of \(C\) (here: \(C_{\text{ref}}\)) and all thresholds.
3. Weighting/missing-data rules (if any).
4. coverage value and invalidity thresholds \(\tau_{\min}\), \(\tau_{\min}^{\Delta}\).
5. Sensitivity curve over \(\rho_{\max}\): \(T_{\text{gate}}(\rho_{\max})\) and coverage(\(\rho_{\max}\)).
6. For ΔGATE: report sizes/coverages of \(G_A\), \(G_B\), \(G_A\cap G_B\), and whether comparison is invalid.

---

## 9. Failure Modes / Misuse (MUST DISCLOSE)
- FM1 (p-hacking): adapting thresholds to obtain preferred results.
- FM2 (selection bias): overly strict gate yields low coverage and biased estimates.
- FM3 (proxy gating): \(C\) reacts to measurement artifacts rather than structural continuity.
- FM4 (ΔGATE collapse): \(G_A\cap G_B\) becomes too small → unstable deltas.

---

## 10. Versioning / Freeze Policy
This document is IES-GATE v1.0 (Frozen).
- No backwards-incompatible changes under v1.x.
- Extensions MUST be published as a separate spec (v2.0+) with explicit deltas.
