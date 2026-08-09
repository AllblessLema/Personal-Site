---
title: "Parameter Golf"
summary: "OpenAI's Model Craft Challenge: train the best language model that fits in a 16 MB artifact and trains in under 10 minutes on 8×H100s, evaluated by compression on the FineWeb validation set. Done in collaboration with Louis Berenyi."
order: 2
---

Done in collaboration with [Louis Berenyi](https://luidevo.github.io/portfolio/).

This was built by a first-year engineering student and Louis, starting from the challenge's provided `train_gpt.py` baseline — which already included Muon, RoPE, GQA, tied embeddings, and int8 + zlib quantization. Everything below is what we changed or added on top of that baseline; the rest was inherited.

OpenAI's Model Craft Challenge: train the best language model that fits in a 16 MB artifact and trains in under 10 minutes on 8×H100s, evaluated by bits per byte (BPB) on the FineWeb validation set. The challenge is a form of L(N) optimization — minimize loss given a fixed parameter budget, unconstrained by data, compute steps, or architecture. That constraint forces choices most practitioners never encounter: aggressive quantization, non-standard architectures, and training setups tuned to the actual step count the hardware achieves.

<figure class="pg-hero">
  <img src="/Personal-Site/projects/parameter-golf/banner.jpg" alt="Official Parameter Golf challenge banner from OpenAI" width="1600" height="533" loading="lazy" />
  <figcaption>The challenge's own banner, self-hosted here. Not our artwork — from <a href="https://github.com/openai/parameter-golf" target="_blank" rel="noopener">github.com/openai/parameter-golf</a>.</figcaption>
</figure>

<div class="pg-stats">
  <div class="pg-stat"><strong>16 MB</strong><span>Artifact cap</span></div>
  <div class="pg-stat"><strong>10 min</strong><span>Train time</span></div>
  <div class="pg-stat"><strong>8×H100</strong><span>Compute</span></div>
  <div class="pg-stat"><strong>BPB</strong><span>FineWeb eval</span></div>
</div>

<div class="pg-links">
  <a class="pg-link" href="https://github.com/LUIDevo/parameter-golf" target="_blank" rel="noopener">Our repo →</a>
  <a class="pg-link pg-link--ghost" href="https://github.com/openai/parameter-golf" target="_blank" rel="noopener">Challenge repo →</a>
</div>

A note on scope: `parameter-golf` is a shared, forked repo — its git history includes the challenge's own leaderboard of records submitted by other participants and by OpenAI. We never touched that `records/` directory. Everything we claim below traces to commits we actually made, in `logs/`, `analysis/`, and `train_gpt.py` — verifiable at the links throughout.

## What we did

**Analysis tooling, written from scratch.** None of this shipped with the challenge repo:

- [`parse_logs.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/parse_logs.py) — parses `train_gpt.py` stdout into structured run records
- [`analyze.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/analyze.py) — CLI for run summaries, run diffs, overfitting checks, plots
- [`report.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/report.py) / [`report.html`](/Personal-Site/projects/parameter-golf/report.html) — self-contained HTML report with embedded charts over every run in `logs/`
- [`visualize.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/visualize.py) — loss/BPB curve overlays across runs
- [`simulator.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/simulator.py) / [`simulator.html`](/Personal-Site/projects/parameter-golf/simulator.html) — interactive parameter-budget simulator, embedded below
- [`bo_tune.py`](https://github.com/LUIDevo/parameter-golf/blob/main/analysis/bo_tune.py) — Optuna TPE Bayesian-optimisation harness for cheap-proxy hyperparameter search

**Methodology: cheap iteration before expensive iteration.** Every architecture or hyperparameter idea was run first as a single-GPU smoke test — 100 to 1,000 iterations, a few minutes each — before spending any 8×H100 time on it. All fourteen smoke tests referenced on this page are in [`logs/`](https://github.com/LUIDevo/parameter-golf/tree/main/logs).

**Architecture ablations.** We varied layer count and model dimension under a fixed 100–500 iteration smoke budget: 9-layer/512-dim (17.06M params, `smoke_001`), 12-layer/544-dim (25.45M, `smoke_003`), 14-layer/352-dim (12.53M, `smoke_007`), 18-layer/352-dim at 2048 sequence length (16.00M, `smoke_008`). Results below.

**Quantization: int6 packing, implemented and measured.** The provided baseline exported weights as int8 + zlib. We replaced that with a 6-bit per-row scheme — [`pack_int6` / `unpack_int6`](https://github.com/LUIDevo/parameter-golf/blob/main/train_gpt.py#L347-L369) pack four 6-bit values into three bytes, with per-row scale factors and an fp16 passthrough for small tensors. Six smoke runs (`smoke_int_6_01`, `smoke_int6_10_layers`, `smoke_int6_11_layers`, `smoke_int6_11_layers_MLP3`, `smoke_int6_11_layers_MLP3_EMA`, `smoke_int6_ema_qat`) confirm it round-trips correctly. On top of that we added an EMA of the weights and an early, still-experimental quantization-aware-training pass — the commit that added it is literally titled "temporary QAT," and that's an accurate description of its state.

**Run-to-run measurement, where we had it.** We didn't run repeated 8×H100 full-constraint trials of our own, so we can't report a noise floor at that scale. What we can report: within our own single-GPU smoke tests, an EMA + QAT variant (`smoke_int6_ema_qat`) beat a non-EMA int6 variant at the same parameter count (`smoke_int6_11_layers_MLP3_EMA`) by 0.0159 BPB at the same 1,000 iterations — see the takeaways below for both numbers.

**Environment work on RunPod.** `requirements.txt` ships a bare, unpinned `torch`, which is a real fragility point in an ephemeral-pod workflow. Louis moved attention from PyTorch's `scaled_dot_product_attention` to `flash_attn_func` directly. That introduced a real compatibility problem: `torch._dynamo`'s DDP optimizer conflicted with `flash_attn` under distributed training, which is what [this fix](https://github.com/LUIDevo/parameter-golf/commit/aec305f) — `torch._dynamo.config.optimize_ddp = False` — addresses.

### Val BPB vs. training step, four smoke configurations

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-bpb-vs-step.svg" alt="Val BPB falls steadily with more training steps for smoke_005, smoke_006, and smoke_007; smoke_008, running at double the sequence length, is cut off by the 600-second wallclock cap at step 386 of 500 and never catches up." width="640" height="380" loading="lazy" />
  <figcaption>Source: <code>logs/smoke_005.txt</code>, <code>smoke_006.txt</code>, <code>smoke_007.txt</code>, <code>smoke_008.txt</code>. Step 0 (random-init loss, ≈4.1 BPB) is omitted from the plot for scale. Generated from the same values <code>report.py</code> and <code>visualize.py</code> parse from these logs.</figcaption>
</figure>

### Val BPB vs. iteration count, same architecture

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-iterations-vs-bpb.svg" alt="At the same 25.45-million-parameter architecture, training for 500 iterations instead of 100 moves val BPB from 1.9706 to 1.4391 — a bigger swing than any architecture change tested." width="420" height="300" loading="lazy" />
  <figcaption>Source: <code>logs/smoke_003.txt</code> (100 it) and <code>logs/smoke_004.txt</code> (500 it), 12-layer/544-dim, single H100, exploratory only — not leaderboard-legal.</figcaption>
</figure>

### Step time vs. configuration

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-step-time.svg" alt="Baseline smoke configurations train at roughly 530 to 650 milliseconds per step. Doubling sequence length to 2048 in smoke_008 raises step time to 1556 milliseconds, which is why that run hit the wallclock cap before finishing." width="640" height="300" loading="lazy" />
  <figcaption>Source: <code>step_avg</code> field, final logged step of each file in <code>logs/</code>.</figcaption>
</figure>

### Artifact budget, one smoke run

<figure class="pg-chart">
  <img src="/Personal-Site/projects/parameter-golf/chart-budget-bar.svg" alt="A 21.78-million-parameter model with EMA weights, int6 packing, and code fits into 15,124,287 of the 16,000,000-byte cap in a single-GPU smoke test." width="640" height="90" loading="lazy" />
  <figcaption>Source: <code>logs/smoke_int6_11_layers_MLP3_EMA.txt</code>. This is a 1,000-iteration single-GPU smoke test, not a full submission run — it demonstrates the packing scheme's byte accounting, not a leaderboard-legal result.</figcaption>
</figure>

### The interactive simulator

<div class="pg-iframe-wrap">
  <iframe src="/Personal-Site/projects/parameter-golf/simulator.html" title="Parameter Golf budget simulator" loading="lazy"></iframe>
</div>

Built to explore how architecture choices trade against the byte budget before spending GPU time on them. [Open full-screen →](/Personal-Site/projects/parameter-golf/simulator.html) · [Full analysis report →](/Personal-Site/projects/parameter-golf/report.html)

## What's next

Each of these changes the budget the next one operates under, which is why the order matters — we're listing it as a roadmap, not a list of completed work.

1. **Make QAT permanent, not temporary.** The current pass is a rough first cut (`smoke_QAT_NO_SWIGELU`, `smoke_int6_ema_qat`). It needs to be tuned and integrated before anything downstream can be measured against it fairly.
2. **GPTQ-style second-order compensation on top of int6.** We've read the technique — using the Hessian to compensate for rounding error — but haven't implemented it. It only makes sense once the int6 + QAT baseline above is stable, since GPTQ compensates for a specific quantization error and that error changes if QAT changes first.
3. **Vocabulary expansion.** A larger tokenizer lowers bytes-per-token but grows the embedding table, which competes with the rest of the model for the same byte cap — has to be weighed against whatever the quantization work above frees up.
4. **Sliding-window evaluation.** Other leaderboard entries in the shared repo (e.g. Matthew Li's sliding-window submission) use this to correct a measurement artifact in flat-stream BPB. We haven't implemented it. It's an eval-time change, so it belongs after the training-side work above, not before — otherwise we'd be tuning against a metric we're about to change.
5. **Test-time training.** We studied `samacqua`'s published LoRA TTT leaderboard entry in the shared repo — per-document LoRA adapters trained at eval time — but have not implemented or reproduced it ourselves. It's placed last deliberately: TTT scales with base-model quality, so it only pays off once the base model above is as good as we can make it.

## Technical takeaways

Restricted to things we measured ourselves, each with the run it came from.

- **Training duration dominated every architecture choice we tested at this scale.** Same 25.45M-parameter architecture, 100 vs. 500 iterations: 1.9706 → 1.4391 BPB (`smoke_003` → `smoke_004`). No architecture change we tried came close to that effect size.
- **At a fixed, very short step count, more parameters measured worse** — 17.06M → 1.9362, 25.45M → 1.9706, 35.10M → 1.9895 BPB, all at 100 iterations (`smoke_001`, `smoke_003`, `smoke_002`). This says the smoke tests were too short to rank model sizes, not that smaller models are actually better — 100 iterations is nowhere near convergence.
- **int6 packing raised the measured zlib payload ratio from ~3.9x to ~5.2x** — `smoke_001` (int8 baseline): payload_ratio 3.91x; `smoke_int_6_01`, `smoke_int6_10_layers`, `smoke_int6_11_layers`, `smoke_int6_11_layers_MLP3`, `smoke_int6_11_layers_MLP3_EMA`: 5.20–5.24x. That's real headroom freed up in the same byte cap.
- **Sequence length is a hard throughput tax, not a footnote.** Doubling sequence length to 2048 (`smoke_008`) raised step time from ~530–650ms to 1556ms and made the run hit the 600-second wallclock cap at step 386 of 500 — it never finished. A better architecture that trains slower can simply lose.
- **EMA + QAT produced the best BPB in any of our own logs: 1.3622** (`smoke_int6_ema_qat`, 1,000 iterations, single GPU) — 0.0159 BPB better than the same int6 setup without QAT (`smoke_int6_11_layers_MLP3_EMA`, 1.3781). Both are smoke-scale, single-GPU numbers; neither has been validated under the real 8×H100 / 10-minute constraint.
