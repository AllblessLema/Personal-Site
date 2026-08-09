---
title: "LeNet accelerator"
summary: "A CNN inference accelerator written from scratch in SystemVerilog for a Basys 3. MAC unit written, testbench in progress — no synthesis or hardware bring-up yet."
order: 1
---

<figure class="pg-hero pg-hero--sm">
  <img src="/Personal-Site/projects/lenet-accelerator/board.jpg" alt="Digilent Basys 3 FPGA board" width="1600" height="1317" loading="lazy" />
</figure>

A CNN inference accelerator written from scratch in SystemVerilog for a Basys 3 (Artix-7 XC7A35T). A 28×28 image goes in, a predicted digit comes out on the 7-segment display. No HLS, no IP wizards — hand-written RTL, each module checked against a Python reference.

The compute core is an 8×8 weight-stationary systolic array of INT8 MACs. The network is LeNet-5-derived: 44,426 parameters, 281,640 MACs per inference, small enough to fit entirely on-chip.

**Status** — MAC unit written, testbench in progress. No synthesis or hardware bring-up yet.

Design decisions, milestones, and the full technical writeup: [github.com/AllblessLema/lenet-accelerator](https://github.com/AllblessLema/lenet-accelerator)
