---
layout: page
title: Bayesian Effect Fusion
description: Reproducing — and stress-testing — a method for sparse categorical regression
img: assets/img/projects/bayesian_effect.png
importance: 2
category: research
related_publications: false
---

**What this was.** A reproduction-and-extension study of Pauger & Wagner's (2019) *Bayesian Effect Fusion for Categorical Predictors* — a course project (MTH442, Prof. Arnab Hazra, IIT Kanpur) done with Yash Bihany, Rythm Kumar, and Devansh Gupta. The point of a reproduction isn't novelty; it's verification: can we take a non-trivial published method, re-derive the theory, implement it from scratch, and confirm its claims actually hold? I find that a genuinely valuable exercise — a lot of methods look great on paper and don't survive independent re-implementation.

**The problem the method solves.** Categorical predictors with many levels blow up a regression's parameter space, and many of those levels often have effectively the same effect (or no effect). Standard tools — lasso, group lasso, elastic net — shrink coefficients but don't *fuse* levels that behave identically. Bayesian effect fusion does both: it can drop irrelevant predictors *and* merge levels with similar effects, giving a sparse, interpretable model. The machinery is a mixture of Normal priors with structured precision matrices, a multi-step MCMC sampler, and model selection via minimizing Binder's loss over a posterior similarity matrix.

**Validating it.** I implemented the method in R and ran a controlled simulation study (100 datasets, 8 categorical covariates, known ground-truth coefficients), benchmarking fusion against six alternatives: a full model, fused-lasso penalty, Bayesian lasso, Bayesian elastic net, group lasso, and an oracle "true" model. The headline result held up: fusion achieved the lowest test-set prediction error and, crucially, dominated on specificity — correctly identifying zero-effect differences (true-negative rate >96%, near-perfect NPV) where competing methods produced large numbers of false positives.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/fusion_mspe.png" title="Test-set MSPE across methods" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    On held-out data, Bayesian effect fusion matched the oracle and beat every practical alternative — the fused-lasso penalty generalized worst.
</div>

**Taking it somewhere new.** Beyond reproducing the paper's simulations, we applied the method to a real dataset it never touched: Kaggle airline-price data, with predictors like airline, route, number of stops, class, flight duration, and booking window. The fusion result was clean and interpretable. For the booking-window predictor ("days left"), ten time bins collapsed into just three distinct price effects:

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/fusion_airline.png" title="Booking-window levels fused into three groups" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Booking 15+ days out gives one price tier, 5–15 days another, last-minute a third — the model discovered this structure automatically.
</div>

The most striking finding: the method fused *all* airline levels and *all* route levels down to the baseline — i.e., it concluded that, once flight duration is accounted for, neither which airline you fly nor which route you take adds information about price. A nice example of effect fusion doing exactly its job: telling you which predictors you can drop entirely.

**My role & takeaway.** Within the group, I focused on the simulation study (alongside Rythm and Yash) and built the visualizations. What I took from it: independently re-implementing a method from its equations is one of the best ways to actually understand it — there's nowhere to hide when you have to make the MCMC sampler converge yourself. It also left me with a healthy respect for *evaluation done carefully*: the reason we could trust the conclusion is that we tested against an oracle and six baselines on ground-truth data, not just eyeballed one result.

<div class="caption">
    Based on Pauger, D. & Wagner, H. (2019), "Bayesian Effect Fusion for Categorical Predictors."
</div>
