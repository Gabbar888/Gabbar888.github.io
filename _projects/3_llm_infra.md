---
layout: page
title: LLM Fine-tuning & Compression Pipeline
description: Reusable multi-GPU infrastructure for training and quantizing large models
img: assets/img/projects/llm_compress.png
importance: 2
category: engineering
related_publications: false
---

**The goal.** During my summer 2025 internship at Samsung Research, I built a reusable pipeline that let different team members fine-tune and compress large open LLMs for their own downstream projects — without each person having to solve the hard infrastructure problems from scratch. The aim was reproducibility and reuse: set up the scaffolding once, correctly, so the whole team could build on it.

**Scale.** The pipeline handles full fine-tuning of models at the 14B and 32B parameter range — roughly 30GB and 70GB in full precision. Getting models this size to train at all (rather than immediately running out of memory) is the central challenge, and it's where most of the engineering went.

<div class="row justify-content-sm-center">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/llm_pipeline_detailed.png" title="End-to-end fine-tuning and compression pipeline" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

**What I built.** A multi-GPU training pipeline on AWS (p5 instances) using **DeepSpeed ZeRO-2/3** to shard optimizer state and parameters across GPUs, which is what makes 30–70GB models fit in memory in the first place. On the inference side, I quantized a fine-tuned 32B model from ~70GB down to ~20GB using **AWQ** — roughly a 3.5× reduction in footprint, which is the difference between "needs a cluster to serve" and "practical to run." The whole thing was packaged in a custom Docker environment (Ubuntu, CUDA) on AWS SageMaker, with Spot Instances for cost efficiency, so any team member could spin up the same reproducible setup.

**The hardest problem — and my favorite.** The win I'm proudest of was untangling **Docker–NVMe storage mismatches**. When you run a custom Docker image for AWS training, the hardware expects certain files and directories to live in specific places — but inside the container, the filesystem can look completely different. Training jobs would fail in confusing ways because the storage the hardware expected wasn't actually mapped to where the container thought it was. Figuring out *which* physical components needed to be attached to *which* paths — and getting the right high-speed NVMe storage wired to the right mount points — was a genuinely tricky debugging problem with no error message coming up and got identified becasue of slowness of transfer speed present in it. Solving it was what turned the pipeline from "works on my machine, sometimes" into something reliable and reusable. (Alongside this, I worked through the usual gauntlet of CUDA out-of-memory errors that come with models this size.)

**What I took from it.** Beyond the technical specifics, this was my first real experience of structured engineering at scale — how a larger project gets executed. Building something that *other people* would depend on changes how you think about correctness and reproducibility: it's not enough for it to work once for you; it has to work every time, for everyone.
