---
title: "Parameter Golf"
summary: "OpenAI's Model Craft Challenge: train the best language model that fits in a 16 MB artifact and trains in under 10 minutes on 8×H100s, evaluated by compression on the FineWeb validation set. Built with Louie."
order: 2
---

OpenAI's Model Craft Challenge: train the best language model that fits in a 16 MB artifact and trains in under 10 minutes on 8×H100s, evaluated by bits per byte on the FineWeb validation set. Built with Louie.

The challenge is a form of L(N) optimization — minimize loss given a fixed parameter budget, unconstrained by data, compute steps, or architecture. The constraint forces choices most practitioners never encounter: aggressive quantization, non-standard architectures, and training setups tuned to the actual step count the hardware achieves.

<div class="pg-stats">
  <div class="pg-stat"><strong>16 MB</strong><span>Artifact cap</span></div>
  <div class="pg-stat"><strong>10 min</strong><span>Train time</span></div>
  <div class="pg-stat"><strong>8×H100</strong><span>Compute</span></div>
  <div class="pg-stat"><strong>BPB</strong><span>FineWeb eval</span></div>
</div>

<div class="pg-links">
  <a class="pg-link" href="https://github.com/LUIDevo/parameter-golf" target="_blank" rel="noopener">Our submission →</a>
  <a class="pg-link pg-link--ghost" href="https://github.com/openai/parameter-golf" target="_blank" rel="noopener">Challenge repo →</a>
</div>

<div class="pg-diagram">
  <div class="pg-diagram-title">The whole budget, one number</div>
  <div class="pg-bytebar-track">
    <div class="pg-bytebar-fill"></div>
  </div>
  <p class="pg-diagram-caption">Code bytes + compressed model bytes, combined, decimal 16,000,000 bytes flat — architecture, tokenizer, and weights all draw from the same budget.</p>
</div>

## What we worked on

**Quantization.** Explored INT6 and INT8 precision. The central tradeoff: lower bit width frees artifact space for more parameters, but only if rounding error doesn't eat the gain. Per-row scaling, passthrough tensors for precision-sensitive weights (the embedding matrix has a disproportionate quant gap relative to its size), and late-stage QAT all interact — the order matters and the right combination is empirical.

<div class="pg-diagram">
  <div class="pg-diagram-title">Precision vs. bit width</div>
  <div class="pg-ladder">
    <div class="pg-ladder-bar">
      <div class="pg-ladder-fill" style="height: 100%;"></div>
      <div class="pg-ladder-label"><strong>FP32</strong>32 bits/weight</div>
    </div>
    <div class="pg-ladder-bar">
      <div class="pg-ladder-fill" style="height: 25%;"></div>
      <div class="pg-ladder-label"><strong>INT8</strong>8 bits/weight</div>
    </div>
    <div class="pg-ladder-bar">
      <div class="pg-ladder-fill" style="height: 18.75%;"></div>
      <div class="pg-ladder-label"><strong>INT6</strong>6 bits/weight</div>
    </div>
  </div>
  <p class="pg-diagram-caption">Bar heights scaled to relative bit width. Every step down buys artifact space for more parameters — until rounding error eats the gain.</p>
</div>

**Architecture search.** Depth vs. width under a fixed budget has no universal answer. Ran ablations on layer count, model dimension, and MLP expansion ratio. A deeper model runs fewer training steps in the same wallclock window — steps per second is a hidden constraint, and comparing architectures without accounting for throughput is a bad comparison.

**Hyperparameter tuning.** Learning rate is the most sensitive parameter — nearly everything else is secondary. Warmdown duration must match the actual step count the architecture achieves: if warmdown starts before the model has trained at full speed, the model never actually learns at full speed. Sequence length and batch size trade off against GPU memory.

**Advanced quantization.** Investigated GPTQ (second-order compensation using the Hessian) and QAT (simulating quantization during training so the model adapts to precision loss). These compound in non-obvious ways — calibration data quality, QAT timing, and bit width produce an interaction space that only resolves empirically.

**Test-time training.** TTT scales with base model quality. A weak model doesn't have the representational capacity to adapt meaningfully in a small number of gradient steps. It belongs at the end of the pipeline, not the start.

## RunPod

Training ran on cloud GPUs via RunPod. A pod (CPU, GPU, RAM) is ephemeral — cleared when you shut it down. A network volume is persistent external storage, mounted at `/workspace` across sessions. The practical lesson: anything you haven't pushed to a volume or a remote is gone when the pod terminates.

Iterating locally on Apple Silicon with MLX is fast for small tests. Getting to the 10-minutes-on-8×H100 constraint requires renting time on a proper cluster.

## Technical takeaways

- Warmdown should cover the last 20–40% of training steps. Starting warmdown before reaching peak learning rate means the model trains at full speed for almost no time.
- Learning rate is the primary lever. Almost all other hyperparameters are secondary to getting this right.
- Depth vs. width is an empirical question. The right answer depends on bit width, sequence length, and compute window — not theory alone.
- The quant gap is real and measurable. Going to a lower bit width only pays off if rounding error introduced is smaller than performance gain from the added parameters.
- Evaluation methods are part of the score. Sliding window evaluation corrects a measurement artifact in how BPB is computed; it belongs at the end, not the start.

<!-- TODO: list specific tools built with repo links once available -->
