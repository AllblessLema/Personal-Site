---
title: "LeNet accelerator"
summary: "Hardware accelerator for LeNet-5, the original CNN for handwritten digit classification on MNIST. End-to-end exposure to the full RTL → simulation → synthesis → hardware loop on a workload small enough to finish and real enough to teach the right lessons."
order: 1
---

A SystemVerilog implementation of a CNN inference accelerator for LeNet-5, targeting the Digilent Basys 3 FPGA. Written from scratch at the register-transfer level — no high-level synthesis, no IP wizards, no block-design tools. Every module is hand-written and verified against a Python golden reference.

The scope is a complete, working, end-to-end inference pipeline: a 28×28 grayscale image goes in, a predicted digit (0–9) comes out on the board's 7-segment display. LeNet is the right first target — its ~60K weights and ~282K MACs per inference fit entirely on-chip on a low-end FPGA, making it finishable, but the dataflow is representative of everything larger.

## Architecture

The compute core is an 8×8 systolic array of INT8 multiply-accumulate units. Weights are loaded into the array and held stationary; activations stream in from one side; partial sums accumulate down each column and are read out at the bottom. Weight-stationary dataflow was chosen for its tractable control logic over more efficient but more complex alternatives like row-stationary (Eyeriss).

Surrounding the array:

- **Weight memory** — BRAM-resident, initialized from `.mem` files at synthesis time
- **Activation memory** — BRAM-resident, double-buffered between layers
- **Layer controller** — FSM orchestrating load, compute, and writeback per layer
- **Pooling unit** — 2×2 max-pool implemented as a comparator tree
- **Output decoder** — drives the 7-segment display from the final argmax

All weights are post-training quantized to INT8. Accumulators inside each MAC are INT32 to prevent overflow; re-quantization happens between layers. Expected accuracy degradation versus FP32: under 1% top-1 on MNIST.

## Target network

LeNet-5 (LeCun et al., 1998): C1 (5×5 conv, 6 filters) → S2 (2×2 max-pool) → C3 (5×5 conv, 16 filters) → S4 (2×2 max-pool) → C5 (FC 120) → F6 (FC 84) → Out (FC 10). Total: ~60K weights, ~282K MACs per inference.

## Target hardware

Digilent Basys 3 — AMD/Xilinx Artix-7 XC7A35T. 90 DSP slices (64 used by the 8×8 array), 1,800 Kb BRAM (~600–700 Kb estimated usage). Design clock target: 50 MHz. Performance target: single MNIST inference in under 10 ms.

## Verification

Each module is verified in two stages. First, a standalone self-checking SystemVerilog testbench simulated with Icarus Verilog, with waveforms in Surfer. Then integration: bit-exact match against a cycle-accurate Python golden reference that implements the same dataflow, arithmetic, and fixed-point rounding. The Python reference for each layer is written before its RTL.

## Toolchain

SystemVerilog · Icarus Verilog · Surfer · AMD Vivado 2025.2 · Python + PyTorch + NumPy · Git

Vivado host platform: TBD (x86 only, requires Linux or Windows with USB-JTAG passthrough to the Basys 3).

## Status

**M0 — Foundations ◐** Toolchain installed on macOS. First modules compiled and simulated. No accelerator code committed yet.

Upcoming milestones: M1 hardware bring-up → M2 single MAC unit → M3 8×8 systolic array → M4 memory subsystem → M5 first conv layer (C1) → M6 pooling + C3 → M7 fully-connected layers → M8 system integration → M9 characterization.

## Resources

- Sze, Chen, Yang, Emer. [*Efficient Processing of Deep Neural Networks: A Tutorial and Survey.*](https://arxiv.org/abs/1703.09039) Proceedings of the IEEE, 2017. — Dataflow taxonomy and data-movement-energy framing behind the architectural choices.
- Chen, Krishna, Emer, Sze. [*Eyeriss: A Spatial Architecture for Energy-Efficient Dataflow for CNNs.*](http://www.rle.mit.edu/eems/wp-content/uploads/2016/11/eyeriss_jssc_2017.pdf) IEEE JSSC, 2017. — Row-stationary dataflow in a production CNN accelerator; the contrast with weight-stationary informs the design choices here.
- Jouppi et al. [*In-Datacenter Performance Analysis of a Tensor Processing Unit.*](https://arxiv.org/abs/1704.04760) ISCA, 2017. — Google TPU v1; a much larger systolic array at industrial scale.
- LeCun, Bottou, Bengio, Haffner. [*Gradient-Based Learning Applied to Document Recognition.*](http://yann.lecun.com/exdb/publis/pdf/lecun-01a.pdf) Proceedings of the IEEE, 1998. — The original LeNet-5 paper.
- Harris & Harris. *Digital Design and Computer Architecture,* 2nd ed. — Foundational SystemVerilog and digital design reference.
