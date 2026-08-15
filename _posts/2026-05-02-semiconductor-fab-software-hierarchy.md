---
title: "Fab Software Architecture: From Enterprise ERP to Semiconductor EAP & MCS"
author: Youngjin Song
date: 2026-04-15 14:00:00 +0900
categories: [DomainKnowledge, Semiconductor]
tags: [mes, eap, mcs, erp, scm, fab-automation, secs-gem, semiconductor]
math: true
layout: post
---

## ERP -> EAP and MCS manufacturing flow

* With examples cuz I'm dumb
# Standard manufacturing flow

1. ERP(Enterprise Resource Planning): How many chips do we need to sell this quarter?
2. SCM(Supply Chain Management): Do we have enough raw silicon wafers and chemicals in stock?
3. Scheduler: Which day and which line processes this batch?

# Middle LAYER MES! (Manufacturing Execution System)

* Recap. Literal motherbrain. 
Handles:
- LOT management
- Recipe management
- process history

# Enter Semiconductor-Specific Automation

- ***EAP*** (Equipment Automation Program): Translates MES commands into machine protocol actions using **SECS/GEM** or **OPC-UA**
EAP IS ***SUPER CRITICAL CUZ*** **EAP plays the role of communicating with and controlling equipment in the middle of equipment automation**.

- Equipment (장비) : processing tools on the fab floor (etchers, deposition chambers, aligners)
- MCS (Material Control System): Transport system. Basically, as an example it directs **OHT - Overhead Hoist Transport** , material storage hub like **Stocker** and sealed wafer containers like **FOUPS** to move wafers from tool -> another tool across the factory without human interaction.....!
![2026-08-15-022854.png](/assets/images/2026-08-15-022854.png)
perhaps its messy. so i got one with an example

![2026-08-15-024633.png](/assets/images/2026-08-15-024633.png)


---