---
layout: post
title: "A Lean-Verified Fresh-Sample Stochastic Extension of Negative-Weighted Distributionally Robust Optimization"
card_title: "Lean-Verified Stochastic NegDRO"
date: 2026-09-19
description: "A formalized research note on projected stochastic primal descent, exponentiated-gradient dual ascent, and finite-time expected squared-error bounds for NegDRO. The complete conditional proof chain is verified in Lean 4."
reading_time: 14
tags: [Optimization, DRO, Lean]
categories: [research-notes]
related_posts: false
featured: false
published: true
toc:
  sidebar: left
  collapse: auto
---

<style>
  .negdro-note {
    --note-ink: var(--global-text-color);
    --note-muted: var(--global-text-color-light);
    --note-accent: #2f6b62;
    --note-accent-soft: rgba(47, 107, 98, 0.09);
    --note-rule: var(--global-divider-color);
    max-width: 820px;
    margin: 0 auto;
  }
  .negdro-note .note-kicker {
    color: var(--note-accent);
    font-size: 0.78rem;
    font-weight: 700;
    letter-spacing: 0.11em;
    margin-bottom: 0.55rem;
    text-transform: uppercase;
  }
  .negdro-note .note-meta {
    color: var(--note-muted);
    display: flex;
    flex-wrap: wrap;
    font-size: 0.88rem;
    gap: 0.35rem 0.7rem;
    margin: 0.65rem 0 1.1rem;
  }
  .negdro-note .note-meta span:not(:last-child)::after {
    content: "·";
    margin-left: 0.7rem;
  }
  .negdro-note .note-lead {
    font-size: 1.08rem;
    line-height: 1.72;
  }
  .negdro-note .note-actions {
    display: flex;
    flex-wrap: wrap;
    gap: 0.65rem;
    margin: 1.25rem 0 2.25rem;
  }
  .negdro-note .note-button {
    border: 1px solid var(--note-accent);
    border-radius: 0.35rem;
    color: var(--note-accent);
    display: inline-flex;
    font-size: 0.88rem;
    font-weight: 650;
    padding: 0.58rem 0.85rem;
    text-decoration: none;
  }
  .negdro-note .note-button.primary {
    background: var(--note-accent);
    color: #fff;
  }
  .negdro-note .note-button:focus-visible,
  .negdro-note summary:focus-visible {
    outline: 3px solid rgba(47, 107, 98, 0.35);
    outline-offset: 3px;
  }
  .negdro-note .note-callout,
  .negdro-note .note-theorem,
  .negdro-note .note-verification,
  .negdro-note .note-source {
    border-left: 4px solid var(--note-accent);
    margin: 1.4rem 0;
    padding: 1rem 1.15rem;
  }
  .negdro-note .note-callout,
  .negdro-note .note-verification {
    background: var(--note-accent-soft);
  }
  .negdro-note .note-theorem {
    background: var(--global-card-bg-color);
    border-bottom: 1px solid var(--note-rule);
    border-right: 1px solid var(--note-rule);
    border-top: 1px solid var(--note-rule);
  }
  .negdro-note .note-source {
    border-color: #a66b2f;
    color: var(--note-muted);
    font-size: 0.94rem;
  }
  .negdro-note .note-label {
    color: var(--note-accent);
    display: block;
    font-size: 0.78rem;
    font-weight: 750;
    letter-spacing: 0.08em;
    margin-bottom: 0.45rem;
    text-transform: uppercase;
  }
  .negdro-note .equation-scroll {
    overflow-x: auto;
    padding: 0.25rem 0;
  }
  .negdro-note mjx-container[display="true"],
  .negdro-note table {
    display: block;
    max-width: 100%;
    overflow-x: auto;
    overflow-y: hidden;
  }
  .negdro-note .mobile-toc {
    display: none;
  }
  .negdro-note .update-map {
    display: grid;
    gap: 0.75rem;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    margin: 1.4rem 0;
  }
  .negdro-note .update-source {
    background: var(--note-accent-soft);
    border: 1px solid var(--note-rule);
    border-radius: 0.45rem;
    font-weight: 650;
    grid-column: 1 / -1;
    padding: 0.8rem;
    text-align: center;
  }
  .negdro-note .update-track {
    border: 1px solid var(--note-rule);
    border-radius: 0.45rem;
    padding: 0.9rem;
  }
  .negdro-note .update-track strong {
    color: var(--note-accent);
  }
  .negdro-note .proof-flow {
    counter-reset: proof-step;
    display: grid;
    gap: 0.65rem;
    grid-template-columns: repeat(4, minmax(0, 1fr));
    margin: 1.5rem 0 2rem;
  }
  .negdro-note .proof-node {
    background: var(--global-card-bg-color);
    border-top: 3px solid var(--note-accent);
    box-shadow: 0 1px 5px rgba(0, 0, 0, 0.06);
    min-height: 6.3rem;
    padding: 0.8rem;
  }
  .negdro-note .proof-node::before {
    color: var(--note-accent);
    content: counter(proof-step, decimal-leading-zero);
    counter-increment: proof-step;
    display: block;
    font-size: 0.73rem;
    font-weight: 750;
    letter-spacing: 0.08em;
    margin-bottom: 0.4rem;
  }
  .negdro-note .proof-node:last-child {
    grid-column: 2 / 4;
  }
  .negdro-note .term-grid,
  .negdro-note .scope-grid {
    display: grid;
    gap: 1rem;
    grid-template-columns: repeat(2, minmax(0, 1fr));
    margin: 1.2rem 0;
  }
  .negdro-note .term-grid > div,
  .negdro-note .scope-grid > div {
    border: 1px solid var(--note-rule);
    border-radius: 0.4rem;
    padding: 0.9rem 1rem;
  }
  .negdro-note .scope-grid h3 {
    font-size: 1rem;
    margin-top: 0;
  }
  .negdro-note .appendix-boundary {
    border-top: 2px solid var(--note-accent);
    margin-top: 3.5rem;
    padding-top: 1.5rem;
  }
  .negdro-note details {
    border-bottom: 1px solid var(--note-rule);
    padding: 0.65rem 0;
  }
  .negdro-note summary {
    color: var(--note-ink);
    cursor: pointer;
    font-size: 1.05rem;
    font-weight: 700;
    padding: 0.5rem 0;
  }
  .negdro-note details[open] summary {
    color: var(--note-accent);
    margin-bottom: 0.8rem;
  }
  .negdro-note table {
    font-size: 0.88rem;
  }
  .negdro-note code {
    overflow-wrap: anywhere;
  }
  @media (max-width: 760px) {
    .negdro-note .proof-flow,
    .negdro-note .term-grid,
    .negdro-note .scope-grid,
    .negdro-note .update-map {
      grid-template-columns: 1fr;
    }
    .negdro-note .proof-node:last-child,
    .negdro-note .update-source {
      grid-column: 1;
    }
    .negdro-note .note-meta span::after {
      display: none;
    }
    .negdro-note .note-actions {
      align-items: stretch;
      flex-direction: column;
    }
    .negdro-note .note-button {
      justify-content: center;
    }
    .negdro-note .mobile-toc {
      background: var(--note-accent-soft);
      border-left: 3px solid var(--note-accent);
      display: block;
      font-size: 0.9rem;
      margin: 0 0 2rem;
      padding: 0.85rem 1rem;
    }
    .negdro-note .mobile-toc strong {
      display: block;
      margin-bottom: 0.35rem;
    }
    .negdro-note .mobile-toc a {
      display: inline-block;
      margin: 0.15rem 0.55rem 0.15rem 0;
    }
  }
</style>

<div class="negdro-note" markdown="1">

<div class="note-kicker">Research Note · Formalized</div>

<div class="note-meta" aria-label="Article metadata">
  <span>Eleanore Wu</span><span>September 2026</span><span>Optimization · Distributional Robustness · Formal Verification</span><span>14 min read</span>
</div>

<p class="note-lead"><strong>Abstract.</strong> This note develops an online stochastic extension of Negative-Weighted Distributionally Robust Optimization (NegDRO). Primal projected SGD and dual exponentiated-gradient ascent use fresh observations, and both oracle updates may use the same round sample. Under a corrected realized additive-intervention reduced form, a universal fresh conditional law, fourth-moment bounds, bounded closed convex geometry, and a fixed spectral heterogeneity witness, the analysis gives finite-time expected squared-error bounds. Equal steps $\eta_b=\eta_w=T^{-1/2}$ produce an inverse-square-root optimization term; the penalized theorem adds the structural bias $2\mu/\lambda$. Lean verifies the complete conditional proof chain. This is a fresh-sample stochastic extension, not a literal formalization of official v3 Algorithm&nbsp;1.</p>

<div class="note-actions" aria-label="Primary article links">
  <a class="note-button primary" href="{{ '/assets/pdf/negdro-formalization-note.pdf' | relative_url }}">Read the PDF</a>
  <a class="note-button" href="https://github.com/Eleanoreee/NegDROFormalization">View formalization <span aria-label="release candidate">&nbsp;· private release candidate</span></a>
</div>

<nav class="mobile-toc" aria-label="Article contents">
  <strong>On this page</strong>
  <a href="#the-problem">Problem</a>
  <a href="#model-and-corrected-reduced-form">Model</a>
  <a href="#the-stochastic-algorithm">Algorithm</a>
  <a href="#main-result">Result</a>
  <a href="#proof-idea">Proof idea</a>
  <a href="#what-lean-verifies">Lean</a>
  <a href="#use-of-ai-and-formal-verification">AI use</a>
  <a href="#scope">Scope</a>
  <a href="#references-and-citation">References</a>
  <a href="#appendix">Appendix</a>
</nav>

The mathematical note is the detailed source; this page is a guided account of the model, algorithm, result, proof architecture, and formal verification boundary.

## 1. The problem

Multi-environment data can help separate a stable causal relationship from environment-specific predictive associations. NegDRO encodes this idea through a minimax objective whose effective environmental weights may be negative. Those weights penalize variation in risk across environments, so sufficiently heterogeneous environments can identify the invariant outcome coefficient $\beta^\star$.

The official algorithms in [arXiv:2412.11850v3](https://arxiv.org/abs/2412.11850v3) optimize empirical risks from a fixed data set. In particular, official Algorithm 1 exactly maximizes the penalized empirical objective over the environmental weights before taking a primal step. Here the question is different: can one draw one fresh environmental observation per round, update the primal and dual variables simultaneously, and still obtain a direct population error bound?

That move is nontrivial. The iterates are adaptive, the primal and dual gradients may share the same sample, the dual update must preserve the simplex, and the proof must translate a static heterogeneity condition into curvature along a stochastic trajectory.

<div class="note-callout">
<span class="note-label">Result in one sentence</span>
Projected stochastic primal descent coupled with exponentiated-gradient dual ascent admits explicit finite-time and $T^{-1/2}$ expected squared-error bounds under a fresh conditional sampling law, with separate identification and penalization biases—and the full conditional chain is checked in Lean.
</div>

## 2. Model and corrected reduced form

For environment $e$, the realized additive-intervention model is

<div class="equation-scroll">

$$
X^e=G^\top(B_{YX}\eta_Y+\eta_X+\delta_e),
\qquad
Y^e=\beta^{\star\top}X^e+\eta_Y.
$$

</div>

It comes from the structural equations $Y=\beta^{\star\top}X+\varepsilon_Y$ and $X=B_{YX}Y+B_{XX}X+\varepsilon_X$. Direct elimination gives

$$
\bigl((I-B_{XX})-B_{YX}\beta^{\star\top}\bigr)X
=B_{YX}\varepsilon_Y+\varepsilon_X.
$$

Let $\Sigma_e=\mathbb E[X^eX^{e\top}]$, $\sigma_Y^2=\mathbb E[\eta_Y^2]$, and

$$
v_{\rm SEM}:=-G^\top\mathbb E[(B_{YX}\eta_Y+\eta_X)\eta_Y].
$$

The squared-loss population risk then expands as

<div class="equation-scroll">

$$
R_e(b)
=\sigma_Y^2
+2v_{\rm SEM}^{\top}(b-\beta^\star)
+(b-\beta^\star)^\top\Sigma_e(b-\beta^\star).
$$

</div>

The vector $v_{\rm SEM}$ is the common linear term left after the environmental intervention cross-moments vanish. Its norm controls the persistent identification contribution: even when the optimization error decays, this linear mismatch is balanced against spectral curvature through a vector Young inequality.

<div class="note-source">
<span class="note-label">Source note</span>
Equation (10) of the official v3 PDF forces the positive reduced-form term $+G^\top B_{YX}\varepsilon_Y$. Appendix E.1 Eq. (97) prints the opposite lower-left sign, and its later negative cross terms conflict with the displayed positive-block factorization. This project verifies the corrected identity forced by Eq. (10); it does not claim to verify the inconsistent printed Eq. (97).
</div>

The final public Lean theorem starts from the realized maps $G^\top,B_{YX},\eta_Y,\eta_X,\delta_e$. The inverse calculation is motivation and bridge material: the final assumption bundle does not reconstruct $G^\top$ from $B_{XX}$, nor does it assume acyclicity.

## 3. The stochastic algorithm

Let $C\subseteq\mathbb R^p$ be nonempty, closed, convex, and bounded, with $b_{\rm Init},\beta^\star\in C$. The algorithm uses the actual Euclidean nearest-point projection $P_C$. The dual variable lies in the simplex $\Delta_m$ and starts from the uniform weight $m^{-1}\mathbf 1$.

For a fresh round sample $(E_t,X_t,Y_t)$, the coordinate oracles are

<div class="equation-scroll">

$$
\widehat g_{t,i}^{b}
=2m\left(w_{t,E_t}-\frac{\gamma}{1+\gamma m}\right)
(X_t^\top b_t-Y_t)X_{t,i},
$$

$$
\widehat g_{t,j}^{w,\mu}
=m(Y_t-X_t^\top b_t)^2\mathbf 1_{\{j=E_t\}}
-2\mu w_{t,j}.
$$

</div>

The coupled recursion is

<div class="equation-scroll">

$$
b_{t+1}=P_C\!\left(b_t-\eta_b\widehat g_t^b\right),
\qquad
w_{t+1,j}=
\frac{w_{t,j}\exp(\eta_w\widehat g_{t,j}^{w,\mu})}
{\sum_k w_{t,k}\exp(\eta_w\widehat g_{t,k}^{w,\mu})}.
$$

</div>

<div class="update-map" role="img" aria-label="A shared fresh observation feeds the primal descent and dual ascent updates">
  <div class="update-source">Fresh observation $(E_t,X_t,Y_t)$ + current state $(b_t,w_t)$</div>
  <div class="update-track"><strong>Primal track</strong><br>Form $\widehat g_t^b$ → descend → project onto $C$</div>
  <div class="update-track"><strong>Dual track</strong><br>Form $\widehat g_t^{w,\mu}$ → ascend → normalize by exponentiated gradient</div>
</div>

Both oracles are evaluated at the same current state and may share the same fresh observation. No primal–dual oracle independence is needed. The fixed $w^0\in\Delta_m$ used below is a deterministic spectral witness and analytical comparator; it is **not** the algorithmic initialization.

## 4. Main result

We use Lean's zero-based convention: rounds are $t=0,\ldots,T-1$. Define

$$
D_T=\frac1T\sum_{t=0}^{T-1}\mathbb E\|b_t-\beta^\star\|_2^2,
\qquad
\bar b_T=\frac1T\sum_{t=0}^{T-1}b_t.
$$

The complete assumption interface is recorded in [Appendix A](#appendix-a--exact-assumption-interface).

<div class="note-theorem">
<span class="note-label">Theorem · unpenalized finite-time bound</span>
Under the corrected realized model, universal fresh conditional law, fourth-moment bounds, bounded closed convex feasible geometry, and fixed spectral witness with curvature $\lambda>0$,

$$
\begin{aligned}
D_T\le{}&
\frac{\|b_{\rm Init}-\beta^\star\|_2^2}{2\lambda\eta_bT}
+\frac{\|v_{\rm SEM}\|_2^2}{\lambda^2(1+\gamma m)^2}
+\frac{2\log m}{\lambda\eta_wT}\\
&+\frac{\eta_wG_{w,0}^2}{\lambda}
+\frac{\eta_bG_b^2}{2\lambda}.
\end{aligned}
$$

</div>

The five terms are, in order:

1. **Initialization:** the starting distance divided by primal step, curvature, and horizon.
2. **Identification bias:** the common SEM linear term balanced against the fixed-witness curvature.
3. **Initial entropy potential:** the uniform dual initialization contributes at most $\log m$.
4. **Dual stochastic error:** controlled by the dual oracle's squared moment bound.
5. **Primal stochastic error:** controlled by the primal oracle's squared moment bound.

For the penalized objective, replace $G_{w,0}^2$ by $G_{w,\mu}^2$ and add **exactly** $2\mu/\lambda$. The averaged-iterate theorem bounds $\mathbb E\lVert\bar b_T-\beta^\star\rVert_2^2$ by the same right-hand side.

With $\eta_b=\eta_w=T^{-1/2}$, the public rate theorems become

$$
D_T\le B_{\rm id}+\frac{C_0}{\sqrt T},
\qquad
D_T\le B_{\rm id}+\frac{2\mu}{\lambda}+\frac{C_\mu}{\sqrt T}.
$$

### How to read the rate

<div class="term-grid">
  <div><strong>Identification bias</strong><br>$B_{\rm id}$ persists with $T$ and comes from $v_{\rm SEM}$.</div>
  <div><strong>Penalization bias</strong><br>$2\mu/\lambda$ appears only in the penalized theorem.</div>
  <div><strong>Optimization error</strong><br>$C_0/\sqrt T$ or $C_\mu/\sqrt T$ vanishes with the horizon.</div>
  <div><strong>Output type</strong><br>The result controls a time-average and an averaged iterate, not the last iterate.</div>
</div>

## 5. Proof idea

The proof is a seven-step pipeline. The diagram is deliberately compact; the key mathematical interfaces appear immediately below it.

<div class="proof-flow" aria-label="Seven-step proof architecture">
  <div class="proof-node">Risk expansion</div>
  <div class="proof-node">Spectral curvature</div>
  <div class="proof-node">Fresh-law oracles</div>
  <div class="proof-node">Projected recursion</div>
  <div class="proof-node">Entropy regret</div>
  <div class="proof-node">Telescope terms</div>
  <div class="proof-node">Jensen averaging</div>
</div>

### Step 1 — Corrected population-risk expansion

Expanding $(Y^e-b^\top X^e)^2$ around $\beta^\star$, then using the corrected reduced form and the systematic/intervention orthogonality conditions, yields the common linear term and environment-specific quadratic term in Section 2. Lean derives this equality from actual integrals and second moments rather than identifying two expressions by name.

### Step 2 — Fixed-witness spectral curvature

For a fixed simplex witness $w^0$, the weighted heterogeneity matrix has every eigenvalue at least $\lambda$. The resulting direction bound is

$$
(b-\beta^\star)^\top\nabla_b\mathcal L(b,w^0)
\ge \lambda\|b-\beta^\star\|_2^2-\varepsilon_{\rm id},
\qquad
\varepsilon_{\rm id}=\frac{\|v_{\rm SEM}\|_2^2}{\lambda(1+\gamma m)^2}.
$$

Lean connects Mathlib's Hermitian eigenvalues to this coordinate quadratic curvature inequality.

### Step 3 — Fresh-law oracle identities and moments

The universal conditional law is tested against the selected $XX$, $XY$, $Y^2$, and fourth-degree monomials. Predictability of $(b_t,w_t)$ then yields conditional unbiasedness and explicit moment bounds for the concrete oracles. The same observation may feed both oracles: only their separate conditional identities are used.

### Step 4 — Euclidean projection and the primal recursion

Because $\beta^\star\in C$, nearest-point projection gives the Fejér inequality

$$
\|b_{t+1}-\beta^\star\|_2^2
\le \|b_t-\eta_b\widehat g_t^b-\beta^\star\|_2^2.
$$

After expansion and conditional expectation, the cross term becomes the population direction and the square term is bounded by $G_b^2$. Lean formalizes the actual nearest-point projection, including existence, uniqueness, nonexpansiveness, and measurability.

### Step 5 — Exponentiated-gradient entropy regret

With the **comparator-minus-current** convention

$$
\operatorname{Reg}_{t,\mu}^0
=\mathcal L_\mu(b_t,w^0)-\mathcal L_\mu(b_t,w_t),
$$

the entropy argument gives

$$
\sum_{t=0}^{T-1}\mathbb E[\operatorname{Reg}_{t,\mu}^0]
\le \frac{\log m}{\eta_w}+\frac{\eta_wT G_{w,\mu}^2}{2}.
$$

No claim is made that instantaneous regret is nonnegative. Lean proves the entropy three-point identity, finite Pinsker inequality, mirror remainder bound, simplex preservation, initial potential bound, and terminal potential nonnegativity.

### Step 6 — Comparator reduction, telescoping, and coefficients

The current dual iterate is connected to the fixed witness by

$$
(b_t-\beta^\star)^\top\nabla_b\mathcal L(b_t,w_t)
\ge \lambda\|b_t-\beta^\star\|_2^2
-\varepsilon_{\rm id}-2\operatorname{Reg}_{t,0}^0
$$

in the unpenalized case. In the penalized case the right-hand side loses exactly one additional $2\mu$. This comes from bounding $\lVert w^0\rVert_2^2-\lVert w_t\rVert_2^2\le1$, not from a $2\mu T$ term in the final average. Summing the primal recursion, inserting cumulative dual regret, telescoping distances, and dividing by $2\lambda\eta_bT$ produces the stated coefficients.

### Step 7 — Finite Jensen for the averaged iterate

Convexity of squared Euclidean distance gives

$$
\mathbb E\|\bar b_T-\beta^\star\|_2^2
\le \frac1T\sum_{t=0}^{T-1}\mathbb E\|b_t-\beta^\star\|_2^2.
$$

Lean proves this finite-dimensional Jensen step and composes it with the trajectory theorem; it does not infer a last-iterate result.

## 6. What Lean verifies

<div class="note-verification">
<span class="note-label">Checked development</span>
Lean <code>v4.34.0-rc2</code> · Mathlib <code>85e3a25e006c35636f0e53b0e9296caca2685bc0</code> · 50/50 root imports · local and GitHub Actions builds successful · <code>Build completed successfully (8814 jobs)</code>
</div>

The development derives the selected moments from the universal fresh conditional law, then proves concrete oracle conditional unbiasedness, integrability, and moment bounds. It also formalizes actual Euclidean projection, entropy/Pinsker geometry and mirror regret, spectral curvature, the primal recursion, telescoping convergence, and finite Jensen averaging.

A source audit found no genuine occurrences of <code>sorry</code>, <code>admit</code>, <code>sorryAx</code>, custom <code>axiom</code> declarations, <code>unsafe</code>, <code>opaque</code>, <code>native_decide</code>, <code>implemented_by</code>, or <code>extern</code>. Local <code>#print axioms</code> checks for the central projection, oracle-integrability, and final convergence theorems report only <code>propext</code>, <code>Classical.choice</code>, and <code>Quot.sound</code>, the standard kernel dependencies reached through Mathlib.

The six final projected public entry points are listed exactly in [Appendix B](#appendix-b--constants-and-all-theorem-variants). These facts describe a checked conditional development; they are not a claim of peer review or independent third-party reproduction.

## 7. Use of AI and formal verification

AI tools assisted with library API discovery, translation of paper arguments into Lean statements, refactoring, debugging, and adversarial correspondence audits. Model output was never treated as mathematical evidence by itself.

Claims were retained only after Lean elaboration and kernel checking, successful full builds, axiom inspection, source scans, and repeated comparisons between the source semantics, the mathematical note, and the public Lean declarations. The mathematical note and kernel-checked terms—not an AI transcript—are the evidence for the claims on this page.

## 8. Scope

<div class="scope-grid">
  <div>
    <h3>This result is</h3>
    <ul>
      <li>a finite-dimensional conditional convergence proof;</li>
      <li>a fresh-sample stochastic extension;</li>
      <li>projected primal SGD with dual EG ascent;</li>
      <li>an expected squared-error result;</li>
      <li>a time-average and averaged-iterate result.</li>
    </ul>
  </div>
  <div>
    <h3>This result is not</h3>
    <ul>
      <li>official v3 Algorithm 1;</li>
      <li>a last-iterate or high-probability theorem;</li>
      <li>an expected-norm theorem;</li>
      <li>a product-space or infinite i.i.d. stream construction;</li>
      <li>assumption-free convergence;</li>
      <li>verification of the inconsistent printed Eq. (97).</li>
    </ul>
  </div>
</div>

No <code>Asymptotics.IsBigO</code> wrapper is claimed. The formalization provides explicit finite-time inequalities and their exact $1/\sqrt T$ specializations.

## 9. References and citation

1. Zhenyu Wang, Yifan Hu, Peter Bühlmann, and Zijian Guo. _Causal Invariance Learning via Efficient Nonconvex Optimization_. [arXiv:2412.11850v3](https://arxiv.org/abs/2412.11850v3), 2026.
2. Shai Shalev-Shwartz. _Online Learning and Online Convex Optimization_. Foundations and Trends in Machine Learning, 4(2):107–194, 2012.
3. Leonardo de Moura and Sebastian Ullrich. _The Lean 4 Theorem Prover and Programming Language_. CADE 28, 2021.
4. The Mathlib Community. _The Lean Mathematical Library_. CPP 2020.

<div class="note-source">
<span class="note-label">Provisional citation</span>
Eleanore Wu. “A Lean-Verified Fresh-Sample Stochastic Extension of Negative-Weighted Distributionally Robust Optimization.” Version 1.0, September 2026. Research note accompanying the Lean formalization. Citation metadata remains provisional until authorship and <code>CITATION.cff</code> are finalized.
</div>

## Appendix

{: .appendix-boundary}

### Appendix A — Exact assumption interface

<details open markdown="1">
<summary>30 fields, grouped by mathematical role</summary>

The six final projected theorems take one <code>AdditiveInterventionFreshSamplingProjectedAssumptions</code> bundle, plus <code>[IsProbabilityMeasure mu]</code>. Penalized results also take $0\le\mu$.

**Eight model/reduced-form fields.** <code>hInt</code> supplies integrability of the coordinate products used in the corrected SEM calculation; <code>hZero</code> supplies systematic/intervention orthogonality; <code>hEnvXMeas</code> and <code>hEnvYMeas</code> give environmental measurability; <code>hEnvX4Int</code> and <code>hEnvY4Int</code> give fourth-moment integrability; <code>hEnvX4</code> and <code>hEnvY4</code> impose the quantitative bounds $K_X,K_Y$.

**Five sampling fields.** <code>hMono</code> makes the filtration increasing; <code>hmCond</code> embeds conditioning sigma-algebras in the ambient one; <code>hLaw</code> supplies the universal fresh law; <code>hPrimalMeas</code> and <code>hDualMeas</code> give next-round measurability of the concrete oracles.

**Seventeen geometry/optimization fields.** <code>hCNonempty</code>, <code>hCClosed</code>, and <code>hCConvex</code> support nearest-point projection; <code>hm</code>, <code>hT</code>, <code>hetaB</code>, <code>hetaW</code>, <code>hB</code>, <code>hKX</code>, <code>hKY</code>, <code>hgamma</code>, and <code>hlam</code> impose the required positivity/sign conditions; <code>hInit</code> and <code>hbetaStar</code> put the initialization and target in $C$; <code>hCBall</code> bounds $C$; <code>hw0</code> makes the witness a simplex point; and <code>hEigen</code> supplies the genuine Hermitian eigenvalue lower bound.

Two indexing details matter: the fresh law is required only for the used rounds $t<T$, while concrete primal and dual next-round measurability are assumed for every natural $t$.

Former technical fields for raw moments, oracle unbiasedness, oracle integrability, projection measurability/feasibility, quadratic nonnegativity, and coordinate curvature are derived rather than supplied by the caller. See the private release-candidate [assumption ledger](https://github.com/Eleanoreee/NegDROFormalization/blob/main/docs/ASSUMPTION_LEDGER.md).

</details>

### Appendix B — Constants and all theorem variants

<details markdown="1">
<summary>Exact moment and rate constants</summary>

The symbols below already denote **squared** moment bounds:

$$
G_b^2=4m\left(2\sqrt{K_YK_X}+2B^2K_X\right),
$$

$$
G_{w,0}^2=m^2\left(8K_Y+8B^4K_X\right),
\qquad
G_{w,\mu}^2=2m^2\left(8K_Y+8B^4K_X\right)+8\mu^2.
$$

The inverse-square-root constants are

$$
B_{\rm id}=\frac{\|v_{\rm SEM}\|_2^2}{\lambda^2(1+\gamma m)^2},
$$

$$
C_0=\frac{\|b_{\rm Init}-\beta^\star\|_2^2}{2\lambda}
+\frac{2\log m}{\lambda}
+\frac{G_{w,0}^2}{\lambda}
+\frac{G_b^2}{2\lambda},
$$

$$
C_\mu=\frac{\|b_{\rm Init}-\beta^\star\|_2^2}{2\lambda}
+\frac{2\log m}{\lambda}
+\frac{G_{w,\mu}^2}{\lambda}
+\frac{G_b^2}{2\lambda}.
$$

</details>

<details markdown="1">
<summary>Six projected public Lean entry points</summary>

| Lean declaration                                                                          | Mathematical conclusion                                      |
| ----------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| <code>stochasticNegDRO_additiveSampling_projected_unpenalized_convergence</code>          | Unpenalized finite-time bound for $D_T$                      |
| <code>stochasticNegDRO_additiveSampling_projected_unpenalized_averaged_convergence</code> | Same RHS for $\mathbb E\lVert\bar b_T-\beta^\star\rVert_2^2$ |
| <code>stochasticNegDRO_additiveSampling_projected_unpenalized_invSqrt_rate</code>         | $B_{\rm id}+C_0/\sqrt T$ for $D_T$                           |
| <code>stochasticNegDRO_additiveSampling_projected_penalized_convergence</code>            | Penalized finite-time bound for $D_T$                        |
| <code>stochasticNegDRO_additiveSampling_projected_penalized_averaged_convergence</code>   | Same penalized RHS for the averaged iterate                  |
| <code>stochasticNegDRO_additiveSampling_projected_penalized_invSqrt_rate</code>           | $B_{\rm id}+2\mu/\lambda+C_\mu/\sqrt T$ for $D_T$            |

All six are in the private release-candidate module [<code>AdditiveInterventionSamplingProjectedConvergence.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/AdditiveInterventionSamplingProjectedConvergence.lean).

</details>

### Appendix C — Technical proof ingredients

<details markdown="1">
<summary>Fresh law, moment bridges, projection, entropy, recursion, and averaging</summary>

| Ingredient               | What is proved                                                                   | Principal module                                                                                                                                                                                                                                                                 |
| ------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Fresh conditional law    | Selected population monomials and fourth-moment consequences                     | [<code>FreshSamplingLaw.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/FreshSamplingLaw.lean)                                                                                                                                      |
| Raw/adaptive moments     | Fresh selected moments become bounds along predictable trajectories              | [<code>OracleIntegrabilityBridge.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/OracleIntegrabilityBridge.lean)                                                                                                                    |
| Oracle integrability     | Primal coordinates/squared norm and dual coordinates/bound square are integrable | [<code>OracleIntegrabilityBridge.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/OracleIntegrabilityBridge.lean)                                                                                                                    |
| Second moments           | Symmetry and quadratic nonnegativity of actual uncentered second moments         | [<code>SecondMomentMatrixBridge.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/SecondMomentMatrixBridge.lean)                                                                                                                      |
| Spectral bridge          | Hermitian eigenvalue lower bounds imply coordinate curvature                     | [<code>SpectralCurvatureBridge.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/SpectralCurvatureBridge.lean)                                                                                                                        |
| Nearest-point projection | Existence, uniqueness, Fejér inequality, nonexpansiveness, measurability         | [<code>EuclideanProjectionBridge.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/EuclideanProjectionBridge.lean)                                                                                                                    |
| Entropy and Pinsker      | Three-point identity, finite Pinsker, mirror remainder, EG regret                | [<code>EntropyGeometry.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/EntropyGeometry.lean), [<code>FinitePinsker.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/FinitePinsker.lean) |
| Primal recursion         | Projected one-step recursion and stochastic expectation bridge                   | [<code>ExpectedPrimalRecursion.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/ExpectedPrimalRecursion.lean)                                                                                                                        |
| Averaged iterate         | Finite-dimensional squared-distance Jensen                                       | [<code>AveragedPrimalIterate.lean</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/NegDROFormalization/AveragedPrimalIterate.lean)                                                                                                                            |

The complete hierarchy is in the private release-candidate [theorem map](https://github.com/Eleanoreee/NegDROFormalization/blob/main/docs/THEOREM_MAP.md).

</details>

### Appendix D — Formalization map

<details markdown="1">
<summary>Modules grouped by proof responsibility</summary>

- **Model and SEM:** <code>AdditiveInterventionPopulationBridge</code>, <code>AdditiveInterventionPopulationInterfaces</code>, <code>PopulationRiskMomentBridge</code>, <code>PopulationExpansion</code>.
- **Sampling and moments:** <code>FreshSamplingLaw</code>, <code>FreshFiniteEnvironmentMoments</code>, <code>FreshSamplingConditionalOracles</code>, <code>ConcreteStochasticOracles</code>, <code>ConcreteOracleMomentBounds</code>, <code>OracleIntegrabilityBridge</code>.
- **Geometry and projection:** <code>SecondMomentMatrixBridge</code>, <code>SpectralCurvatureBridge</code>, <code>SpectralWitnessInterface</code>, <code>EuclideanProjectionBridge</code>, <code>PrimalProjectionRecursion</code>.
- **Entropy and regret:** <code>EntropyGeometry</code>, <code>FinitePinsker</code>, <code>ExponentiatedGradient</code>, <code>MirrorRemainderBound</code>, <code>EGTrajectoryRegret</code>, <code>ExpectedEGTrajectoryRegret</code>, <code>StochasticDualRegret</code>.
- **Convergence and rates:** <code>ExpectedPrimalRecursion</code>, <code>StochasticNegDROConvergence</code>, <code>StochasticNegDROExplicitConstants</code>, <code>AveragedPrimalIterate</code>, <code>StochasticNegDRORates</code>.
- **Final adapters and entry points:** <code>AdditiveInterventionSamplingBridge</code>, <code>AdditiveInterventionSamplingConvergence</code>, <code>AdditiveInterventionSamplingProjectedConvergence</code>.

This grouping is intentionally higher-level than the 50-module root import. Exact declaration-level edges are listed in [<code>docs/THEOREM_MAP.md</code>](https://github.com/Eleanoreee/NegDROFormalization/blob/main/docs/THEOREM_MAP.md).

</details>

### Appendix E — Reproducibility

<details markdown="1">
<summary>Pinned toolchain, build commands, and release-candidate artifacts</summary>

<pre><code>lake exe cache get
lake build</code></pre>

- Lean: <code>v4.34.0-rc2</code>
- Mathlib: <code>85e3a25e006c35636f0e53b0e9296caca2685bc0</code>
- Local build record: <code>Build completed successfully (8814 jobs)</code>

Artifacts: [PDF]({{ '/assets/pdf/negdro-formalization-note.pdf' | relative_url }}) · [README](https://github.com/Eleanoreee/NegDROFormalization/blob/main/README.md) · [Theorem map](https://github.com/Eleanoreee/NegDROFormalization/blob/main/docs/THEOREM_MAP.md) · [Assumption ledger](https://github.com/Eleanoreee/NegDROFormalization/blob/main/docs/ASSUMPTION_LEDGER.md)

The GitHub links above are **private release-candidate links**. They work for authorized collaborators in this local draft and must be reviewed before any public deployment.

</details>

</div>
