---
layout: page
title: Clinical Trial Design from Cancer Data
description: Survival analysis, power simulation, and Bayesian interim analysis on ~25,000 patients
img: assets/img/projects/cancer_trial_thumbnail.png
importance: 2
category: engineering
related_publications: false
---

**Context.** A group research project mentored by Prof. Dootika Vats (Dept. of Statistics, IIT Kanpur), in collaboration with **Johnson & Johnson**. Working from clinical and genomic records for ~25,000 cancer patients, the goal was to design a well-powered oncology clinical trial end-to-end — from finding a target mutation, to sizing the trial, to deciding when it could be stopped early.

**Finding a target.** We started by identifying a mutation worth building a trial around: one both clinically meaningful and common across cancer types. A prevalence analysis across five cancers pointed to the SNP (single-nucleotide polymorphism) mutation as the most widespread, so we profiled that subgroup's demographics and survival — establishing, for example, that smoking status and prior-treatment history were strongly associated with survival outcomes, and that pancreatic cancer carried the shortest survival.

**Sizing the trial.** Focusing on non-small-cell lung cancer, we estimated a median survival of **39.95 months** via Kaplan–Meier, which (under an exponential model) gives a hazard rate of ~0.0174/month. We then ran power simulations: for a target effect, generate control and treatment survival times, apply a log-rank test, repeat 1000×, and read off statistical power. Sweeping the hazard ratio revealed the central tension of trial design — *the smaller the effect you want to detect, the disproportionately more patients you need.*

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/trial_samplesize.png" title="Minimum sample size vs hazard ratio" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Detecting a hazard ratio of 0.90 (a subtle benefit) needs ~1940 patients per group; a strong 0.60 effect needs just 85. Small effects are expensive to prove.
</div>

**Adding real-world complexity.** A real trial doesn't recruit everyone at once, and patients drop out. We extended the simulation to model staggered recruitment (20 patients/month) and dropout, then measured how long a study must follow patients to stay adequately powered — again as a function of effect size.

**Stopping early — Bayesian interim analysis.** The final piece asks: can we end a trial early once the evidence is clear? We implemented a Bayesian hierarchical model (following Berry et al.) across two indications, modeling each response rate with a shared prior so the two arms could *borrow strength* from each other. Using MCMC (5000 posterior samples), we computed the posterior probability of clinical benefit across a grid of true response rates.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/trial_bayes.png" title="Posterior probabilities with hierarchical borrowing" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The shared prior is visible in the highlighted cell: when indication 1 is clearly effective (θ₁ = 0.45), it pulls indication 2's posterior up to 0.92 even at a borderline θ₂ = 0.4 — strength borrowed across arms.
</div>

**What I took from it.** This project connected a whole chain of statistical ideas — survival estimation, hypothesis testing, Monte Carlo power analysis, and Bayesian inference — into a single, decision-oriented workflow. The throughline that stuck with me: statistical design is fundamentally about *spending a finite resource (patients) wisely*, and methods like hierarchical borrowing exist because that resource is precious. It's also where I got comfortable thinking in terms of priors, posteriors, and what it means to update beliefs from data.
