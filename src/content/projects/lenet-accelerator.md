---
title: "LeNet accelerator"
summary: "A CNN inference accelerator written from scratch in SystemVerilog for a Basys 3. MAC unit written, testbench in progress — no synthesis or hardware bring-up yet."
order: 1
---

<figure class="pg-hero">
  <img src="/Personal-Site/projects/lenet-accelerator/board.jpg" alt="Digilent Basys 3 FPGA board" width="1600" height="1317" loading="lazy" />
</figure>

A CNN inference accelerator written from scratch in SystemVerilog for a Basys 3 (Artix-7 XC7A35T). A 28×28 image goes in, a predicted digit comes out on the 7-segment display. No HLS, no IP wizards — hand-written RTL, each module checked against a Python reference.

The compute core is an 8×8 weight-stationary systolic array of INT8 MACs. The network is LeNet-5-derived: 44,426 parameters, 281,640 MACs per inference, small enough to fit entirely on-chip.

<div class="pgwf" role="group" aria-label="Weight-stationary wavefront, 8 by 8 array, planned dataflow">
  <input type="radio" name="pgwf-step" id="pgwf-0" class="pgwf-radio" checked>
  <input type="radio" name="pgwf-step" id="pgwf-1" class="pgwf-radio">
  <input type="radio" name="pgwf-step" id="pgwf-2" class="pgwf-radio">
  <input type="radio" name="pgwf-step" id="pgwf-3" class="pgwf-radio">
  <input type="radio" name="pgwf-step" id="pgwf-4" class="pgwf-radio">
  <input type="radio" name="pgwf-step" id="pgwf-5" class="pgwf-radio">
  <input type="radio" name="pgwf-step" id="pgwf-6" class="pgwf-radio">
  <div class="pgwf-stage">
    <svg viewBox="0 0 380 370" width="100%" height="auto" font-family="ui-monospace, 'Courier New', monospace">
      <title>Weight-stationary wavefront across the planned 8x8 array</title>
      <desc>Activity sweeps diagonally: a PE at row i, column j becomes active at step i+j. Even at peak overlap only part of the array is simultaneously active, which is why fill and drain time keep the array below full utilization. This is the intended dataflow; no RTL implementing it exists yet.</desc>
      <g class="pgwf-frame pgwf-frame-0">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-1">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-2">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-3">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-4">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-5">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    </g>
      <g class="pgwf-frame pgwf-frame-6">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#dbe4ef" stroke="#9fb4cf" stroke-width="1"/>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#4a6fa5" stroke="#111" stroke-width="1.6"/>
    </g>
      <g class="pgwf-static">
    <rect x="40" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">0</text>
    <rect x="78" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">1</text>
    <rect x="116" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">2</text>
    <rect x="154" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">3</text>
    <rect x="192" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">4</text>
    <rect x="230" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="268" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="306" y="30" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="51.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="40" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">1</text>
    <rect x="78" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">2</text>
    <rect x="116" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">3</text>
    <rect x="154" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">4</text>
    <rect x="192" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="230" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="268" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="306" y="68" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="89.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="40" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">2</text>
    <rect x="78" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">3</text>
    <rect x="116" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">4</text>
    <rect x="154" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="192" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="230" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="268" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="306" y="106" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="127.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="40" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">3</text>
    <rect x="78" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">4</text>
    <rect x="116" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="154" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="192" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="230" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="268" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="306" y="144" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="165.0" font-size="11" fill="#111" text-anchor="middle">10</text>
    <rect x="40" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">4</text>
    <rect x="78" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="116" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="154" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="192" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="230" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="268" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">10</text>
    <rect x="306" y="182" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="203.0" font-size="11" fill="#111" text-anchor="middle">11</text>
    <rect x="40" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">5</text>
    <rect x="78" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="116" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="154" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="192" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="230" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">10</text>
    <rect x="268" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">11</text>
    <rect x="306" y="220" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="241.0" font-size="11" fill="#111" text-anchor="middle">12</text>
    <rect x="40" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">6</text>
    <rect x="78" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="116" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="154" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="192" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">10</text>
    <rect x="230" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">11</text>
    <rect x="268" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">12</text>
    <rect x="306" y="258" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="279.0" font-size="11" fill="#111" text-anchor="middle">13</text>
    <rect x="40" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="57.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">7</text>
    <rect x="78" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="95.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">8</text>
    <rect x="116" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="133.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">9</text>
    <rect x="154" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="171.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">10</text>
    <rect x="192" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="209.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">11</text>
    <rect x="230" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="247.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">12</text>
    <rect x="268" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="285.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">13</text>
    <rect x="306" y="296" width="34" height="34" rx="3" fill="#fafafa" stroke="#ddd" stroke-width="1"/>
    <text x="323.0" y="317.0" font-size="11" fill="#111" text-anchor="middle">14</text>
    </g>
    </svg>
    <p class="pgwf-caption">
      <span class="pgwf-cap pgwf-cap-0">step 0 / 14 — wavefront begins at the corner PE</span>
      <span class="pgwf-cap pgwf-cap-1">step 2 / 14 — wavefront filling</span>
      <span class="pgwf-cap pgwf-cap-2">step 4 / 14 — wavefront filling</span>
      <span class="pgwf-cap pgwf-cap-3">step 7 / 14 — peak overlap, mid-array (26 of 64 PEs)</span>
      <span class="pgwf-cap pgwf-cap-4">step 10 / 14 — wavefront draining</span>
      <span class="pgwf-cap pgwf-cap-5">step 12 / 14 — draining</span>
      <span class="pgwf-cap pgwf-cap-6">step 14 / 14 — last PE (7,7) finishes; array drains to idle</span>
      <span class="pgwf-cap-static">Each cell labelled with the step at which it activates (row + column). Static view for reduced-motion.</span>
    </p>
  </div>
  <div class="pgwf-controls">
    <label for="pgwf-0" class="pgwf-btn">0</label>
    <label for="pgwf-1" class="pgwf-btn">2</label>
    <label for="pgwf-2" class="pgwf-btn">4</label>
    <label for="pgwf-3" class="pgwf-btn">7</label>
    <label for="pgwf-4" class="pgwf-btn">10</label>
    <label for="pgwf-5" class="pgwf-btn">12</label>
    <label for="pgwf-6" class="pgwf-btn">14</label>
  </div>
</div>
<p class="pgwf-note">Weight-stationary dataflow through the planned 8×8 array.</p>

**Status** — MAC unit written, testbench in progress. No synthesis or hardware bring-up yet.

Design decisions, milestones, and the full technical writeup: [github.com/AllblessLema/lenet-accelerator](https://github.com/AllblessLema/lenet-accelerator)
