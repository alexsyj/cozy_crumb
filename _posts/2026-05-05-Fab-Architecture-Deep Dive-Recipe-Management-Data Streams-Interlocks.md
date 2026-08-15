---
title: "Fab Architecture Deep Dive: Recipe Management, Data Streams, and Interlocks"
date: 2026-08-15 18:00:00 +0900
categories: [Semiconductor, Fab Architecture]
tags: [rms, fdc, tracking-data, interlock, middleware, mes, eap]
math: true
---

### Recipes & RMS (Recipe Management System):

Every wafer product needs a specific process recipe. Imagine making instant ramen back in 2025 october, following specific instructions. Amount of water, temperature and for how long. 

* RMS: Recipe Management System, stores recipes. 
1. EAP requests recipe Identification from MES
2. MES gets the recipe from RMS (only if mes doesn't already have it)
3. MES gives the recipe to EAP.
4. EAP now uses the **SECS/GEMS command (ex. S7F23)** to download to machine.

![2026-08-15-072211.png](/assets/images/2026-08-15-072211.png)

### Data coming out of machine.

* **Tracking Data (Milestones)**: Event notifications like Track In (Job Start) and Track Out (Job End) used to update real-time database tables in MES.
* **Tracing Data (High-Volume Sensors)**: Continuous sensor streams (temperatures, pressure, gas flows) fed into **FDC** (Fault Detection & Classification) and **SPC** to detect machine anomalies immediately.

### Middleware Highway.

* **Middleware Highway**: Fabs rely on Enterprise Service Bus messaging (TIBCO / Rendezvous / JMS) so MES, EAP, MCS, and Dispatcher can communicate seamlessly across the network.

### Interlock Systems (ILS).

* Safety lockouts (Q-Time Interlock, FDC/SPC Interlock) that automatically freeze equipment to prevent defective processing.

![2026-08-15-072304.png](/assets/images/2026-08-15-072304.png)

---