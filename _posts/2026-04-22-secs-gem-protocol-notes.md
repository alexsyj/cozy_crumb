---
title: "SECS/GEM Protocol & Semiconductor Fab Automation Architecture"
author: Youngjin Song
date: 2026-04-22 14:00:00 +0900
categories: [Semiconductor, Fab Automation]
tags: [secs-gem, hsms, equipment-software, linux]
layout: post
---

### SECS Protocol intro and definition:

- Goal: To manage all machines inside the microchip factory so that they work automatically 24/7 without requiring human presence.
- Big problem: Machine makers make and build their tools differently. If every machine spoke their own language, the central computer can't talk to all of them. 
- Solution: SECS

---

## SECS-I

Physical cable connection for older models

---

## SECS-II

Dictionary of words inside the message. Used to control and report errors.
Ex. "Machine #3 finished wafer," "Error: Temperature too high"

---

## Conversation between central computer and a machine in a nutshell

![2026-08-13-021609.png](/assets/images/2026-08-13-021609.png)

Basically central computer and a machine performing health and identity check. 

---

## SECS protocol Layer in a nutshell

![2026-08-13-024443.png](/assets/images/2026-08-13-024443.png)

this is the original with the full concept but i would rather have the one above to less complicate things.

![2026-08-13-025013.png](/assets/images/2026-08-13-025013.png)

---
INCOMPLETE