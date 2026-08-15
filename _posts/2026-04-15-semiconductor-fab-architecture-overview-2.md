---
title: "Understanding baisc Semiconductor Fab Architecture part two"
author: Youngjin Song
date: 2026-04-15 14:00:00 +0900
categories: [DomainKnowledge, Semiconductor]
tags: [mes, eap, mcs, erp, mdm, fab-automation, secs-gem, hsms, semiconductor]
math: true
layout: post
---

### MES (Manufacturing Execution System) - 

* I don't have any hands on experience. This is just from online research and what I've heard from my retired father that worked with MES.

- MES is the masterbrain and the ultimate database of the factory.

* It knows what is working what is broken, what temperature (recipe) machine B needs to use for the specific lot, and where LOT number 402 is for example..................

### Local FAB team (stays inside the factory building)

- MES(Manufacturing execution system): Like i've covered, its the factory floor brain. it tracks lots. it knows machine status for all. 

- EAP(Equipment application program): The software bridge that talks directly to individual machines through SECS/GEM or HSMS protocols.

- MCS(Material control system): It's basically the robot control room. (It directs automated overhead monorails to carry wafer pods around).

### Corporal HQ Team

- ERP(Enterprise Resource Planning): ERP Handles money, sales orders, and big-picture business logistics.
- MDM (Master Data Management): MDM Defines universal rules and official naming IDs (for example it's like standard Machine IDs and Lot IDs) so all global factories speak the exact same language.
- SCM (Supply Chain Management): Manages shipping raw materials into factories and sending finished chips to clients.

---

![2026-08-15-003321.png](/assets/images/2026-08-15-003321.png)

Summary pic for this entire post.