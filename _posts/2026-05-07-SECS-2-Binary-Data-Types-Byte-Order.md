---
title: "SECS-2 Data Representation: Data Formats, Binary Encoding, and Endianness"
date: 2026-05-07 20:00:00 +0900
categories: [Semiconductor, Low Level Engineering]
tags: [secs-2, data-types, endianness, binary-encoding, network-sockets]
math: true
---

### SECS-2 Data Types & Byte Order (Endianness)

When semiconductor tools send sensor and process data back to **EAP**, the binary data must be parsed accurately using strict SECS-2 standard data types and byte-order rules.

#### 1. SECS-2 Data Types
To prevent memory mismatches, every variable sent across the network is tagged with a specific data format and byte size:
* **Signed Integers (`I1` to `I8`):** Whole numbers that can be positive or negative. The number denotes byte size (e.g., `I1` = 1 byte, `I4` = 4 bytes).
* **Unsigned Integers (`U1` to `U8`):** Positive-only whole numbers (e.g., `U1` ranges from 0 to 255).
* **Floating Points (`F4`, `F8`):** Decimal numbers used for precise environmental readings like chamber temperature and pressure (`F4` = single precision, `F8` = double precision).

#### 2. Byte Order & Endianness
Different hardware architectures store multi-byte numbers in different byte orders:
* **Big-Endian:** Stores the most significant byte (MSB) first (left-to-right). Standard for network transmissions and many Linux controllers.
* **Little-Endian:** Stores the least significant byte (LSB) first (right-to-left). Standard for x86 Intel/AMD processors.

> **Key Rule:** When decoding socket data sent from a Big-Endian tool to a Little-Endian EAP server, bytes must be swapped in code. Failing to handle byte order turns a simple value like `2` into a corrupted number like `33,554,432`, triggering false system alarms!

![2026-08-15-080259.png](/assets/images/2026-08-15-080259.png)