---
title: "Parameter Golf"
summary: "OpenAI's Parameter Golf: fit the best language model you can into 16 MB. Our best packed result is 1.3781 BPB at 15.1 of 16 MB, from single-GPU smoke tests — we never ran the full 8×H100 config. Done in collaboration with Louis Berenyi."
order: 2
---

<p class="pg-subtitle">Done in collaboration with <a href="https://luidevo.github.io/portfolio/">Louis Berenyi</a></p>

<figure class="pg-hero">
  <img src="/Personal-Site/projects/parameter-golf/banner.jpg" alt="Official Parameter Golf challenge banner from OpenAI" width="1600" height="533" loading="lazy" />
  <figcaption>OpenAI's own banner, self-hosted here — not our artwork. From <a href="https://github.com/openai/parameter-golf">github.com/openai/parameter-golf</a>.</figcaption>
</figure>

OpenAI's Parameter Golf asks for the best language model you can fit in a 16 MB artifact, trained in ten minutes on eight H100s. The score is bits-per-byte on held-out FineWeb text: how many bits the model needs to encode each byte it has never seen. Lower is better. A model that predicts well is a model that compresses well — BPB is next-token loss in different units, not a separate capability.

We started from the challenge's baseline, which already shipped Muon, RoPE, GQA, tied embeddings, and int8 + zlib packing — a 17,059,912-parameter model. Everything below is what we changed or added on top of it, run single-GPU in smoke tests of 100 to 1,000 iterations. We never got a config onto the real 8×H100, ten-minute clock the challenge actually scores on.

Our best packed result: an EMA-averaged model using a 6-bit weight-packing scheme we wrote ourselves — 21,778,504 parameters, 1.3781 BPB, packed into 15,124,287 of the 16,000,000-byte budget. A later pass added quantization-aware training on top and pushed raw loss lower, to 1.3622 BPB — but the packing for that path was never finished, so it fell back toward int8-sized output and landed at 18,698,710 bytes, over the cap. We stopped before fixing it.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-bpb-vs-step.svg" alt="Val BPB falls steadily with more training steps for smoke_005, smoke_006, and smoke_007; smoke_008, running at double the sequence length, is cut off by the 600-second wallclock cap at step 386 of 500 and never catches up." width="640" height="380" loading="lazy" />
  <figcaption>Source: <code>logs/smoke_005.txt</code>, <code>smoke_006.txt</code>, <code>smoke_007.txt</code>, <code>smoke_008.txt</code>.</figcaption>
</figure>

The clearest lesson about the clock came from doubling sequence length to 2048. Better on paper: step time went from roughly 530–650 ms to 1556 ms, and the run was killed by the 600-second wallclock cap at step 386 of 500. Slower and smarter loses to faster and dumber when the clock is the binding constraint.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-step-time.svg" alt="Baseline smoke configurations train at roughly 530 to 650 milliseconds per step. Doubling sequence length to 2048 raises step time to 1556 milliseconds, which is why that run hit the wallclock cap before finishing." width="640" height="300" loading="lazy" />
  <figcaption>Source: <code>step_avg</code> field, final logged step of each file in <code>logs/</code>.</figcaption>
</figure>

Before trusting a delta, we ran one config twice: 1.4532 and 1.4526 BPB, a spread of 0.0006 — small enough that most of what looked like a difference between single runs wasn't one. On the packing side, replacing int8 with our own 6-bit-per-row scheme — four values packed into three bytes, with per-row scale factors — raised the zlib payload ratio from ~3.9× to ~5.2×, real headroom in the same byte cap.

The rest was tooling: a log parser, a run-diff CLI, an HTML report generator, an interactive budget simulator, and an Optuna harness for hyperparameter search. Plus the habit of testing an idea cheaply on a single GPU before deciding whether it earned more time.

<div class="pg-iframe-wrap">
  <iframe src="/Personal-Site/projects/parameter-golf/simulator.html" title="Parameter Golf budget simulator" loading="lazy"></iframe>
</div>

We entered this to learn how models like this actually get built, and we did. That was the whole point of joining, and we've moved on to other things.

Repo: [github.com/LUIDevo/parameter-golf](https://github.com/LUIDevo/parameter-golf)
