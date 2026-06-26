---
layout: page
title: English → Indic Translation
description: Building a Transformer NMT system from scratch, and finding where it really breaks
img: assets/img/projects/nmt.png
importance: 3
category: engineering
related_publications: false
---

**The problem.** Translate English editorials into Hindi and Bengali — languages with far richer morphology and different grammatical structure than English. I built almost everything from scratch: tokenization, model, training loop, decoding, and evaluation, training on ~80k English–Hindi and ~69k English–Bengali sentence pairs.

**The turning point: architecture.** I started with GRU-based models, and they failed in a specific, instructive way — they got *stuck in repetition loops*, repeating the same token over and over, and couldn't hold long-range context. No decoding trick fully fixed it (top-k sampling only masked the symptom). The fix wasn't a better search; it was a better model. Switching to a 3-layer Transformer (d_model = 512, 8 heads) more than **doubled** the BLEU score and eliminated the repetition problem entirely — the self-attention mechanism could model the full source sentence at once.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/nmt_evolution.png" title="BLEU progression across model versions" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    GRU models stalled below BLEU 0.06; the Transformer jumped to 0.123 and careful dropout tuning (0.3 → 0.1) carried it to a final 0.166.
</div>

**The real bottleneck wasn't grammar — it was vocabulary.** Once the architecture was right, error analysis revealed that the remaining failures were almost all caused by `<unk>` tokens: any out-of-vocabulary word (proper nouns like "Daggubati", technical terms) became `<unk>`, and that "vocabulary shock" would sometimes destabilize the model into a repetition loop. The grammatical breakdown was a *symptom*; the disease was a fixed vocabulary. (Two subtle bugs I had to fix along the way taught me a lot: I was initially mapping OOV words to `<PAD>`, which the loss ignored — so the model was never penalized for them — and I was computing loss over padding tokens, which trained the model to be very good at predicting `<PAD>`.)

**A novel fix (designed, not yet deployed).** To recover proper nouns without retraining, I designed a post-processing pipeline: when the model emits `<unk>`, use the decoder's **cross-attention weights** to find the aligned source word, verify with POS-tagging that it's a proper noun, then transliterate it and substitute it back in. I didn't get to put this into practice within the project — a reliable transliteration step is its own non-trivial problem, and the competition rules ruled out the specialized pretrained models that would have made it work. But I think the design is sound, and it's something I plan to build out properly in my own time.

<div class="row justify-content-sm-center">
    <div class="col-sm-12 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/nmt_pipeline.png" title="Transliteration post-processing pipeline" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

**Final result & takeaway.** The tuned Transformer reached **BLEU 0.166, chrF 0.430, ROUGE-L 0.446** on the unseen test set. The most useful lesson, and the one I'd carry into any sequence task: *diagnose what's actually breaking before reaching for a bigger hammer.* Here the win came not from scale but from (1) the right architecture and (2) correctly identifying that subword tokenization — splitting "Daggubati" into translatable sub-units rather than discarding it — was the single highest-impact next step. Anyone tackling low-resource translation can likely save themselves a lot of time by treating vocabulary coverage as a first-class problem from the start.

<div class="caption">
    Code and full report: <a href="https://github.com/Gabbar888/NLP-Project">github.com/Gabbar888/NLP-Project</a>
</div>
