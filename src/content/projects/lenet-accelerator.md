---
title: "LeNet accelerator"
summary: "A from-scratch RTL accelerator for a LeNet-5-derived CNN on a Basys 3 FPGA. In progress: MAC unit written and awaiting verification, no synthesis or hardware bring-up yet."
order: 1
---

An independent project — no collaborator. I'm a first-year Computer Engineering student with no prior RTL experience before April 2026, building this without HLS, IP wizards, or block-design tools: every module is hand-written SystemVerilog, verified against a Python golden reference before it touches hardware.

## Status

I'm at the foundational stage: the toolchain works end-to-end in simulation, a MAC unit is written but not yet verified, and no hardware bring-up or synthesis has happened. No timing, resource-utilization, throughput, or accuracy numbers exist yet — everything quantitative on this page is either a published FPGA spec or arithmetic I derived from the network definition, labelled as such. Repo: [github.com/AllblessLema/lenet-accelerator](https://github.com/AllblessLema/lenet-accelerator).

## Design and rationale

### The network

The page used to call this "LeNet-5 (LeCun et al., 1998)." It isn't, exactly — it's a LeNet-5-derived network: the original topology with a few 1998-era choices replaced by their modern equivalents.

<table>
<thead><tr><th></th><th>LeCun 1998 LeNet-5</th><th>This design</th></tr></thead>
<tbody>
<tr><td>Input</td><td>32×32</td><td>28×28</td></tr>
<tr><td>Subsampling</td><td>Average pooling, trainable coefficient</td><td>2×2 max-pool</td></tr>
<tr><td>Nonlinearity</td><td>Scaled tanh / sigmoid</td><td>ReLU</td></tr>
<tr><td>C3 connectivity</td><td>Sparse, hand-specified connection table</td><td>Fully connected across all 6 input channels</td></tr>
<tr><td>Output layer</td><td>RBF units</td><td>Linear + argmax</td></tr>
</tbody>
</table>

The sparse C3 connection table in the original existed to save compute on 1998 hardware. At this scale — a network small enough to fit entirely on a Basys 3 regardless — it buys nothing but address-generation complexity, so C3 here is fully connected across all six S2 channels instead.

That changes the parameter and MAC counts from what's usually quoted for LeNet-5. Recomputing from the layer definitions:

<table>
<thead><tr><th>Layer</th><th>Weights</th><th>Biases</th><th>Total</th><th>MACs</th></tr></thead>
<tbody>
<tr><td>C1</td><td>5×5×1×6 = 150</td><td>6</td><td>156</td><td>86,400</td></tr>
<tr><td>C3</td><td>5×5×6×16 = 2,400</td><td>16</td><td>2,416</td><td>153,600</td></tr>
<tr><td>C5</td><td>256×120 = 30,720</td><td>120</td><td>30,840</td><td>30,720</td></tr>
<tr><td>F6</td><td>120×84 = 10,080</td><td>84</td><td>10,164</td><td>10,080</td></tr>
<tr><td>Out</td><td>84×10 = 840</td><td>10</td><td>850</td><td>840</td></tr>
<tr><td><strong>Total</strong></td><td><strong>44,190</strong></td><td><strong>236</strong></td><td><strong>44,426</strong></td><td><strong>281,640</strong></td></tr>
</tbody>
</table>

**44,426 parameters, 281,640 MACs per inference** — not the ~60K/~282K figures the Sze tutorial quotes for the original network. The MAC total is close by coincidence (this design's fully-connected C3 costs more MACs than the original's sparse table, roughly cancelling the smaller 28×28 input); the parameter count is genuinely different, and the earlier "~60K weights" on this page was simply wrong for the network actually being built here.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/lenet-accelerator/diagram-topology.svg" alt="C1 and C3 together account for 85 percent of the network's MACs, while C5 alone holds 69 percent of its parameters — compute pressure and storage pressure sit in different layers." width="900" height="260" loading="lazy" />
  <figcaption>Derived from the layer definitions above. C1 + C3 = 85% of MACs; C5 = 69% of parameters.</figcaption>
</figure>

That asymmetry — convolutions dominate compute, the first FC layer dominates storage — is why the same 8×8 array has to serve both convolution and dense layers well; optimizing only for one would leave the other badly underused.

### Dataflow

The compute core is planned as an 8×8 systolic array of INT8 MAC units using a **weight-stationary** dataflow: each PE holds one weight resident while activations stream past and partial sums accumulate downward. I chose this over output-stationary and row-stationary (Eyeriss) for its simpler control logic — the other two give better energy efficiency on real workloads but cost meaningfully more control complexity than this project's scope justifies.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/lenet-accelerator/diagram-dataflow-comparison.svg" alt="Weight-stationary, output-stationary, and row-stationary dataflows compared; weight-stationary is marked as chosen for its simpler control logic." width="900" height="260" loading="lazy" />
  <figcaption>Three dataflow options considered; weight-stationary chosen. No RTL implements any of them yet.</figcaption>
</figure>

<figure class="pg-chart">
  <img src="/Personal-Site/projects/lenet-accelerator/diagram-dataflow.svg" alt="Planned weight-stationary array: weights resident in each of 64 cells, activations entering from the left, partial sums exiting the bottom." width="560" height="460" loading="lazy" />
  <figcaption>The intended dataflow for the 8×8 array. This is a design diagram, not documentation of a working system — no RTL implementing this array exists yet (see What's built).</figcaption>
</figure>

### Quantization

Weights and activations are planned as INT8, with an INT32 accumulator inside each MAC to avoid overflow, re-quantized to INT8 between layers. Training happens in FP32 on the host; PyTorch training code doesn't exist yet, so this is a plan, not a result.

Expected accuracy degradation versus an FP32 baseline is under 1% top-1 on MNIST — this is a target drawn from the post-training-quantization literature (Jacob et al. 2018), not a measurement. No training or evaluation has been run.

### Compute and memory budget

All of the following is arithmetic from the network definition and the XC7A35T's published specs — no synthesis or hardware run exists to measure any of it.

**Roofline.** At 64 MACs/cycle (the 8×8 array) and a 50 MHz design clock:

```
281,640 MACs ÷ 64 MACs/cycle = 4,400 cycles
4,400 cycles ÷ 50 MHz        = 88 µs
```

Against the 10 ms performance target, that's a **~113× margin** — this design is not compute-bound. Almost all of that margin is budget for control overhead, memory access, and the array sitting underutilized between layers.

Underutilization is itself derivable, without any hardware. For C1: the 25-element reduction (5×5×1) maps onto 8 array rows in 4 passes, filling 25 of 32 available slots (78%); 6 output filters map onto 8 columns (75%). Combined: **~59% utilization from tensor shapes alone**, before counting any control overhead — one of the costs of using a fixed 8×8 array across layers of very different shapes.

**BRAM.** Weights at INT8: 44,426 bytes = 355,408 bits ≈ **347 Kb**. The largest activation tensor is C1's output (24×24×6 = 3,456 bytes); a double-buffered (ping-pong) pair of those is 6,912 bytes ≈ **54 Kb**. Raw total: **≈401 Kb of the 1,800 Kb available (~22%)**.

That's a floor, not the real allocation — Artix-7 BRAM is allocated in 36 Kb blocks (splittable into two 18 Kb halves), so the actual block count will be higher and only a synthesis run will say by how much.

**DSP.** An 8×8 array of INT8 MACs is *intended* to map one-to-one onto 64 of the XC7A35T's 90 DSP48E1 slices. Whether Vivado actually does that is a synthesis outcome, not a decision I get to make directly — it may infer LUT-based multipliers for operands this narrow, or pack two 8-bit multiplies into one DSP48E1's wider multiplier. The real number arrives at M9.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/lenet-accelerator/diagram-resource-budget.svg" alt="DSP and BRAM usage are derived estimates under the XC7A35T's capacity; LUT and flip-flop usage is left explicitly TBD because no synthesis has been run." width="640" height="260" loading="lazy" />
  <figcaption>DSP: intended mapping. BRAM: derived from parameter counts. LUTs and flip-flops: no synthesis run yet, shown as TBD rather than estimated.</figcaption>
</figure>

## What's built

If it isn't committed, it isn't in this list.

- **Toolchain, verified end-to-end on macOS.** The initial commit compiled and simulated an AND gate in Icarus Verilog — proof the SystemVerilog → `iverilog` → `vvp` → Surfer loop works. That file has since been cleaned up as a simulation artifact, but the commit is in the repo's history.
- **Python golden model, MAC level.** [`python/mac_model.py`](https://github.com/AllblessLema/lenet-accelerator/blob/main/python/mac_model.py) implements signed wraparound (`wrap_signed`), a single accumulate step that raises on out-of-range operands (`accumulator`), and a cycle-by-cycle dot product (`dot_product`) — matching the arithmetic contract in `MAC_UNIT_SPEC.md.pdf`. The spec's own self-test harness isn't wired up yet.
- **MAC unit RTL, written, not yet verified.** [`rtl/mac/mac.sv`](https://github.com/AllblessLema/lenet-accelerator/blob/main/rtl/mac/mac.sv) implements the spec'd interface (`clk`, `rst_n`, `clr`, `en`, signed `a`/`b`, signed `acc`) and the required reset > clear > enable > hold priority. Two things the spec calls for aren't done: the four design-rationale questions it requires (`always_ff` vs. `always`, blocking vs. non-blocking, named intermediate vs. inline product, sync vs. async reset) aren't yet written up, and the testbench that would prove this compiles and behaves correctly doesn't exist yet — see below.
- **MAC testbench, started.** [`sim/mac_tb.sv`](https://github.com/AllblessLema/lenet-accelerator/blob/main/sim/mac_tb.sv) is a stub — it declares the clock and control signals but doesn't yet instantiate the DUT or implement any of the 13 required test cases, and doesn't currently compile. "Started" is the accurate word for its state.
- **HDL foundations.** Working through Harris & Harris (*Digital Design and Computer Architecture: ARM Edition*) exercises outside this repo — Chapter 4 complete, Chapter 5 in progress. Deliberate groundwork, not a detour.

## What's next

M1 (hardware bring-up) is blocked: it needs a working x86 Vivado host, and the plan — a dedicated x86 Linux mini PC, chosen because VM passthrough makes USB-JTAG access to the board unreliable — is decided but not yet purchased. Rather than stall on that, M2 (the MAC unit, then the systolic array) is proceeding in simulation with Icarus Verilog, which needs no Vivado at all. That's why M2 is ahead of M1 rather than the reverse.

<figure class="pg-chart">
  <img src="/Personal-Site/projects/lenet-accelerator/diagram-milestones.svg" alt="M0 and M2 are in progress; M1 is blocked on acquiring an x86 Vivado host; M3 through M9 have not started." width="640" height="340" loading="lazy" />
  <figcaption>Status per milestone, from README.md's roadmap.</figcaption>
</figure>

Once the MAC unit passes its testbench (13/13, per the spec), the plan continues: M3 replicates it into the 8×8 array and verifies an INT8 matmul against NumPy; M4 builds the BRAM-backed weight and activation memory; M5–M7 bring up the network layer by layer (C1, then S2+C3, then the fully-connected stack), each gated on a bit-exact match against the Python reference; M8 integrates the full pipeline with UART image streaming from the host; M9 is where synthesis, timing closure, and the resource-utilization numbers this page currently can't show actually get measured.

## Resources

- Sze, Chen, Yang, Emer. [*Efficient Processing of Deep Neural Networks: A Tutorial and Survey.*](https://arxiv.org/abs/1703.09039) Proceedings of the IEEE, 2017. — Dataflow taxonomy and the data-movement-energy framing behind the architectural choices.
- Chen, Krishna, Emer, Sze. [*Eyeriss: An Energy-Efficient Reconfigurable Accelerator for Deep Convolutional Neural Networks.*](http://www.rle.mit.edu/eems/wp-content/uploads/2016/11/eyeriss_jssc_2017.pdf) IEEE Journal of Solid-State Circuits, 2017. — Row-stationary dataflow in a production accelerator; the contrast with weight-stationary informs the design choice here. Not implemented.
- Jouppi et al. [*In-Datacenter Performance Analysis of a Tensor Processing Unit.*](https://arxiv.org/abs/1704.04760) ISCA, 2017. — Google TPU v1; a much larger systolic array at industrial scale.
- LeCun, Bottou, Bengio, Haffner. [*Gradient-Based Learning Applied to Document Recognition.*](http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf) Proceedings of the IEEE, 1998. — The original LeNet-5 paper; see the comparison table above for where this design departs from it.
- Jacob et al. [*Quantization and Training of Neural Networks for Efficient Integer-Arithmetic-Only Inference.*](https://arxiv.org/abs/1712.05877) CVPR, 2018. — Source for the under-1%-top-1 accuracy-degradation target cited above.
- Harris, D., Harris, S. *Digital Design and Computer Architecture: ARM Edition*, 2nd ed. — Foundational SystemVerilog and digital design reference; exercises in progress outside this repo.
