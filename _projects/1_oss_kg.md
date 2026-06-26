---
layout: page
title: Knowledge Graph Extraction
description: Open-source pipeline for dense KG & Dialogue Generation — pre-release
img: assets/img/projects/kg.png
importance: 1
category: research
related_publications: false
---

An open-source project (pre-release, so I'll hold the name and end goal for now) focused on building a high-recall pipeline for generating dense **knowledge graphs from unstructured text**. As a core contributor, my work spans the extraction pipeline, model evaluation, and data engineering.

The core of the system is a custom **Open Information Extraction (OpenIE)** pipeline for high-recall entity and relation extraction. We started with established tools like Neo4j's SimpleKGPipeline and LLMGraphBuilder, but our use case called for denser graphs and a different extraction approach than they were built for — so we moved to a custom pipeline tailored to those needs. Alongside the extraction work, I benchmarked performance across several LLM backbones (Qwen, DeepSeek, Kimi, and others) routed through OpenRouter, assessing the cost-versus-quality tradeoff to pick the right model for the job.

On the data and evaluation side, I generated synthetic multi-turn dialogues directly from KG topologies, with LLM-based filtering to drop low-quality samples. To evaluate the pipeline itself, I used **Label Studio** to design annotation rubrics on an initial subset and established inter-annotator agreement baselines (Cohen's Kappa) — which then grounded an LLM-as-a-judge scoring stage. Reliable evaluation of this kind of open-ended, non-deterministic output turned out to be one of the hardest and most interesting parts of the work.

<div class="caption">
    More details to come once the project is public.
</div>
