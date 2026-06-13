
# Performance Evaluation of CPI for Different Instruction Mixes in a RISC Processor

A mini project analyzing how instruction mix affects processor performance in a 5-stage pipelined RISC-V architecture, using the **Ripes** visual simulator.

## Overview

Modern processors rely on instruction pipelining to overlap execution across stages (IF, ID, EX, MEM, WB). While pipelining boosts throughput, its efficiency depends heavily on the type of instructions being executed — arithmetic, memory, or control-flow.

This project measures **Cycles Per Instruction (CPI)** and **Instructions Per Cycle (IPC)** for three custom RISC-V assembly programs, each isolating a specific instruction category, and analyzes the resulting pipeline hazards.

## Objective

Quantify how arithmetic-heavy, memory-heavy, and control-heavy instruction mixes affect CPI on a 5-stage RISC-V pipeline (RV32IMC), and relate the results to known pipeline hazard types (data, load-use, and control hazards).

## Tools & Setup

- **Simulator:** [Ripes](https://github.com/mortbopet/Ripes) — RISC-V Visual Simulator
- **Processor Model:** 5-Stage Pipeline (RV32IMC)
- **Hazard Handling:** Data Forwarding + Pipeline Stalls
- **Memory Model:** Harvard architecture (separate instruction & data memory)

## Programs

| Program | Instructions | Cycles | CPI | IPC | Efficiency | Final Register |
|---|---|---|---|---|---|---|
| Arithmetic-Heavy | 16 | 20 | 1.25 | 0.800 | 80% | x16 = 0x9 |
| Memory-Heavy | 13 | 21 | 1.62 | 0.619 | 62% | x3 = 0x32 (50) |
| Control-Heavy | 9 | 21 | 2.33 | 0.429 | 43% | x3 = 0x1 |

### 1. Arithmetic-Heavy
Exclusively register-to-register operations (`ADDI`, `ADD`, `SUB`, `MUL`, `DIV`), no memory access, no branches. Data forwarding resolves most RAW hazards — pipeline stays nearly full.

### 2. Memory-Heavy
Uses a `.data` array `{5, 10, 15, 20}`. Each `LW` is immediately followed by a dependent `ADD`, creating load-use hazards that require a 1-cycle stall (forwarding cannot reach back from MEM in time). Final sum (50) verified correct.

### 3. Control-Heavy
Chains four branch types — `BEQ`, `BNE`, `BLT`, `BGE` — across labels L1–L4. Each taken branch flushes 1–2 incorrectly fetched instructions, dominating the cycle count (branches = 44% of the instruction stream).

## Key Findings

```
Arithmetic CPI (1.25) < Memory CPI (1.62) < Control CPI (2.33)
```

- Control-heavy programs are **86.4% slower** (CPI) than arithmetic-heavy programs.
- Memory-heavy programs are **29.6% slower** than arithmetic-heavy programs.
- Branch flushes are the most severe source of pipeline inefficiency, followed by load-use stalls, with RAW hazards (resolved via forwarding) having minimal impact.

All CPI values were manually verified using `CPI = Total Clock Cycles / Instructions Retired` and matched the Ripes Execution Info panel output exactly.

## Repository Contents

- Full mini project report (introduction, literature review, methodology, results, analysis, conclusion)
- Presentation slides summarizing the project
- Assembly source files for each instruction-mix program (Arithmetic, Memory, Control)

## How to Reproduce

1. Install/open [Ripes](https://github.com/mortbopet/Ripes)
2. Select **5-Stage Pipeline Processor (RV32IMC)** with data forwarding enabled
3. Load one of the assembly programs into the source editor
4. Run to completion and record **Cycles**, **Instructions Retired**, **CPI**, and **IPC** from the Execution Info panel
5. Verify CPI manually: `CPI = Total Cycles / Instructions Retired`

## Limitations

- Programs are small (9–16 instructions), so pipeline fill-up overhead influences CPI
- Ripes does not model branch prediction or a cache hierarchy
- Structural hazards aren't demonstrated (separate I/D memory by design)

## Future Work

- Branch prediction (2-bit saturating counter / BTB)
- Cache integration (I-cache / D-cache)
- Larger, mixed-instruction programs approximating real workloads
- Superscalar and out-of-order execution (Tomasulo's algorithm)

## Team

This was a collaborative team mini project completed as part of the B.Tech AI & Data Science curriculum.

**Guide:** Sherik K. K., Assistant Professor, Dept. of AI & Data Science

## Institution

MES College of Engineering, Kuttippuram
Department of Artificial Intelligence and Data Science
Affiliated to APJ Abdul Kalam Technological University
Mini Project Report, 2025–2026

---

*This project was developed for academic purposes.*
