---
title: "Solve Yield Loss -> Advanced Process Control: Run-to-Run (R2R) and EWMA Algorithms"
date: 2026-05-10 21:00:00 +0900
categories: [Semiconductor, Process Control]
tags: [cmp, r2r, run-to-run, ewma, recipe-control, fab-automation]
math: true
---

### Advanced Process Control: Run-to-Run (R2R) & EWMA Algorithms

In semiconductor manufacturing, static recipes cause yield loss over time due to machine wear and pad degradation. Fab automation solves this using **Run-to-Run (R2R)** closed-loop control.

#### Key Concepts
* **CMP (Chemical Mechanical Planarization):** Equipment used to physically grind down and flatten wafer surfaces.
* **Run-to-Run (R2R) Control:** A feedback framework that measures post-process wafer thickness and dynamically updates recipe parameters (e.g., polishing time) for the next batch.
* **EWMA (Exponentially Weighted Moving Average):** A filtering algorithm used by R2R. Unlike standard Moving Averages (MA), EWMA applies higher mathematical weight to the most recent runs, allowing the system to react instantly to sudden tool drift.

![2026-08-15-081730.png](/assets/images/2026-08-15-081730.png)
