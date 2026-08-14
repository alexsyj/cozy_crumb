---
title: "SECS/GEM Protocol"
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

In summary Green = What User/Factory sees on screen
SECS 2(RED)= dictionary/grammar used to define meaning of the message
SECS 1(BLUE) = Transport/Delivery truck. Handles raw bytes, wiring, hardware handshakes.

---

## Physical layer

Core Concept: Null Modem Setup (Crossed Wires)

- TX (Transmit / Mouth): Sends data out.

- RX (Receive / Ear): Takes data in.

- GRD (Ground): Equalizes voltage between the devices so signals read correctly.

Basically My mouth (TX) must connect to your ear (RX). If TX goes to TX, neither computer can hear anything...
+-------------------+                      +-------------------+
  |   Host / Equipment|                      |   Equipment / Host|
  |                   |                      |                   |
  |       TX (Mouth)  |--------------------->|  RX (Ear)         |
  |                   |                      |                   |
  |       RX (Ear)    |<---------------------|  TX (Mouth)       |
  |                   |                      |                   |
  |      GND (Ground) |======================| GND (Ground)      |
  +-------------------+                      +-------------------+
---

## Block Layer

You can't just throw raw text across a serial wire. SECS-1 wraps data into a structured "Block Envelope".

The envelope structure: 

- LTH (Length - 1 Byte): Says how big the message is (10 to 254 bytes max per block). Note: LTH doesn't count itself or the checksum in its length!

- HEADER (10 Bytes): Contains routing info (Who sent this? Which stream/function is it?).

- TEXT (0–244 Bytes): The actual message payload (the SECS-2 data).

- CHECKSUM (2 Bytes): Math calculation at the end to ensure zero bytes got corrupted over the wire.

* SECS-1 blocks max out at 254 bytes and use Checksums to prevent data corruption.

+----------+----------------------------------+---------------------+----------+
| LTH (1B) |         HEADER (10 Bytes)        |    TEXT (0-244B)    |  CHK (2B)|
+----------+----------------------------------+---------------------+----------+
| Length   | Routing, Stream/Function,        | Payload             | Math     |
| (10-254) | Device ID & System Bytes         | (SECS-II Data)      | Checksum |
+----------+----------------------------------+---------------------+----------+
|          |<------ LTH Counts This Area ---->|                     |          |
---
## Block protocol

The step-by-step polite conversation required to send one data block over SECS-1.

1. Sender (ENQ / 0x05): "Line Bid! Hey, can I speak?"

2. Receiver (EOT / 0x04): "Accept Bid! Go ahead, line is clear."

3. Sender (MESSAGE): [Sends Header + Text + Checksum]

4. Receiver (ACK / 0x06): "Got it! Checksum matches, thanks!" (Or NAK / 0x15 if data got corrupted).

SENDER                                                     RECEIVER
  |                                                           |
  |--- ENQ (0x05) : "Line Bid! Can I speak?" ---------------->|
  |<-- EOT (0x04) : "Accept Bid! Go ahead." ------------------|
  |                                                           |
  |--- MESSAGE [ Header | Text | Checksum ] ----------------->|
  |<-- ACK (0x06) : "Got it! Checksum matches." --------------|
  v                                                           v
---

## NACK Protocol

What happens when data gets corrupted during transit?

The Flow:

- Sender asks to talk (ENQ), Receiver says go ahead (EOT).

- Sender sends the data block.

- The Catch: The receiver calculates the Checksum, but it doesn't match what the sender sent!

- Receiver replies with NAK (0x15): "Hey, that message was garbled/corrupted, try again!"

Sender Starts from ENQ. RTY = Retry attempts limiting how many times sender retries until it gives up and declare a failure.

SENDER                                                     RECEIVER
  |                                                           |
  |--- ENQ -------------------------------------------------->|
  |<-- EOT ---------------------------------------------------|
  |                                                           |
  |--- MESSAGE (Data Corrupted in Transit) ------------------>|
  |                                                           | [Calculates Checksum]
  |                                                           | [Mismatch Detected!]
  |<-- NAK (0x15) : "Corrupted! Try again." ------------------|
  |                                                           |
  | (Retry #1: Restart from ENQ up to RTY limit)              |
  v                                                           v
---

## T1 Timeout 

Max time allowed between individual bytes within the same msg.

How It Works:
When bytes are streaming in, they must arrive fast and continuously.If byte 1 arrives, but byte 2 takes too long to arrive, T1 Timeout triggers!

Standard T1 Values:
Typical Value: 0.5 -> 1.0 seconds.
Purpose: Prevents a device from waiting forever if the wire gets cut mid-byte.

Trigger T1 timeout if block length <10 OR >254 bytes.
Receiver will send NAK back to signal an error.

BYTE 1       BYTE 2                   BYTE 3 (STUCK / CUT WIRE)
 +---+        +---+       |<--- T1 Timeout Limit --->|
 |   |        |   |       |   (0.5s ~ 1.0s)          |
 +---+        +---+       |                          |  X  [T1 TIMEOUT TRIGGERED]
   |            |         |                          |
---|------------|---------|--------------------------|---------> Time
   <-- OK -->   <-- OK -->                           ^
                                                     |
                                            Receiver sends NAK
---

## T2 Timeout (Protocol timeout)
The max time waiting for a response after sending a signal (ENQ or message).

Case one: Sender sends ENQ: Receiver doesn't send EOT back within T2 time.

Case two: Sender sends Message: Receiver doesn't send ACK/NAK back within T2 time.

Case three: Receiver sends EOT: Sender doesn't start sending the message within T2 time.

Retry Loops: If T2 times out on an ENQ, the sender retries up to RTY times. If it fails all retries -> SEND FAILURE.

Standard T2 Values:Typical Value: 3 to 10 seconds
Typical RTY (Retry Count): 3 attempts

Scenario A: Line Bid Timeout               Scenario B: Message ACK Timeout
SENDER                 RECEIVER            SENDER                RECEIVER
  |                       |                  |                       |
  |--- ENQ -------------->|                  |--- MESSAGE ---------->|
  |                       |                  |                       |
  |   |<-- T2 Timer -->|  |                  |   |<-- T2 Timer -->|  |
  |   | (3s ~ 10s)     |  |                  |   | (3s ~ 10s)     |  |
  |   |                |  |                  |   |                |  |
  |   X [No EOT]       |                  |   X [No ACK/NAK]   |
  |                       |                  |                       |
  |-- (Retry ENQ) ------->|                  |-- (Declare Error) --->|
---

## 10-Byte Block Header 

* I don't really get all of this at the moment so i'm just going to put few points that i simplified.

All SECS-I message block carry 10-byte block header which has a role in routing, transaction tracking, message classification.

![2026-08-14-015031.png](/assets/images/2026-08-14-015031.png)

- Device ID (1-2 byte/s) : identify the equipment on network.
R-bit is included in to indicate the direction.
1 = host to equipment
0 = equipment to host

- Stream and function (3-4 bytes)
Stream (7 bits) : Functional category. 
Stream 1 = equipment status, Stream 5 = alarm, Stream 6 = data collection
Function(8 bits) : Specific msg action in that category
W-bit/Wait bit: If SxFy W, set to 1, if fire and forget, 0....

- Block number and E-bit/End bit (5-6 bytes) : Track individual blocks.
1 for final block. (end bit/E-bit) 
0 if more blocks are coming along the way. 

- System byte (7-10 bytes) : Just put it simply... A unique 4 byte transaction id used to track requests/match primary/secondary messages, and to duplicate blocks....

+-----------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+------------+
|  BYTE 1   |  BYTE 2   |  BYTE 3   |  BYTE 4   |  BYTE 5   |  BYTE 6   |  BYTE 7   |  BYTE 8   |  BYTE 9   |  BYTE 10   |
+-----------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+-----------+------------+
| R |     Device ID     | W |   Stream (S)      |       Function (F)        | E |      Block Number     |      System Bytes  |
| 1 |  (Host -> Eqp)    | 1 | S1: Status        | F1: Are You There         | 1 | Final Block       |  Transaction ID /  |
| 0 |  (Eqp -> Host)    | 0 | S5: Alarms        | F2: On-Line Data          | 0 | More Coming       | Primary-Secondary  |
|   |                   |   | S6: Data Collect  |                           |   |                   |     Matching       |
+---+-------------------+---+---------------+-------------------------------+---+-------------------+--------------------+

---

## Collision Handling & Protocol Timers

- Line collision: When the Host and Equipment bid for the line simultaneously, the Host (Master) takes priority. The Equipment (Slave) yields, waits, and retries.

ALLOWED MAX DELAYS:

- T1(Inter character): 0.5 -> 1.0 sec. Between individual bytes in a block.
- T2(Protocol): 3.0 -> 10.0 sec. Waiting for line response E0T/ACK.
- T3(Reply): 30.0 sec. Waiting for secondary reply after sending W = 1.
- T4(Inter-Block): 10.0 sec. between consecutive blocks in a multi-block transfer..

HOST (Master)                     EQUIPMENT (Slave)
       |                                   |
       |------ ENQ (Bid) ------\ /---------| ENQ (Bid)
       |                       X           |
       |<---------------------/ \--------->|
       |                                   |
       | [Takes Priority]                  | [Yields & Backs Off]
       |                                   |
       |<-- EOT (Yields) ------------------|
       |---------------- MESSAGE --------->|