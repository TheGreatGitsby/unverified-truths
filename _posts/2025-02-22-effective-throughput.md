---
title: "Effective Throughput: The Subsystem Conspiracy"
date: 2025-02-22
layout: post
---

We’ve all been fed the gospel of throughput in digital design: string some subsystems together, slap on basic flow control, and the slowest bitrate limits the system. It’s the bottleneck dogma—simple, intuitive, and trustworthy. If Subsystem A churns at 10 Gbps, B at 5 Gbps, and C at 8 Gbps, you’re capped at 5 Gbps. End of story. Yawn.

But what if the subsystems aren’t cant operate in parallel? What if one has to finish its entire job—completely drain its buffer, wave goodbye—before the next one even begins? That’s where the architects trip over their own flip-flops, and I’m here to whisper an unverified truth: *effective throughput in this case isn’t the slowest link—it’s actually calculated with the inverse sum rule, like total resistance of resistors in parallel.*

## The Slowest-Link Myth

Picture the standard setup: Subsystem A hands off data to B, B to C, all with lightweight flow control (a “ready-valid” handshake, if you’re feeling Verilog-y). No one waits for a full packet; data trickles through as soon as it’s ready. The throughput? Min(A, B, C). It’s the weakest-link philosophy—designers nod sagely, optimize the laggard, and call it a day. Fair enough. It’s true when the system’s a leaky pipe: the narrowest section sets the flow.

But now imagine a different beast. Subsystem A processes a chunk of data at 10 Gbps but hoards it until the full block’s done—say, 100 bits, taking 10 ns—before dumping it to B. B, at 5 Gbps, needs 20 ns to chew through that 100 bits and pass it to C, who at 8 Gbps takes 12.5 ns. Each subsystem insists on finishing its task entirely before the next one starts. This isn’t a pipeline anymore; it’s a relay race where everyone’s waiting for the baton. Think of a situation of needing to authenticate a block before starting the decryption.

## The Parallel Resistor Revelation

Here’s the kicker: the effective throughput isn’t 5 Gbps (the slowest bitrate). It’s worse—and weirder. To figure it out, think time, not rate. Each subsystem’s “cycle” time for that 100-bit block is:  
- A: 10 ns (100 bits / 10 Gbps)  
- B: 20 ns (100 bits / 5 Gbps)  
- C: 12.5 ns (100 bits / 8 Gbps)  

Total time for one block to slog through the system? 10 + 20 + 12.5 = 42.5 ns. Effective throughput = bits per time = 100 bits / 42.5 ns ˜ 2.35 Gbps. Huh. That’s not 5 Gbps. That’s not even close.

Now, flip it: throughput is bits per second, so effective throughput (Teff) is 1 / (sum of times per bit). For each subsystem, time per bit is 1/bitrate:  
- A: 1 / 10 Gbps = 0.1 ns/bit  
- B: 1 / 5 Gbps = 0.2 ns/bit  
- C: 1 / 8 Gbps = 0.125 ns/bit  

Total time per bit = 0.1 + 0.2 + 0.125 = 0.425 ns/bit. Teff = 1 / 0.425 ns/bit ˜ 2.35 Gbps. Look familiar? It’s the harmonic mean of the bitrates—or, if you squint, it’s bandwidth behaving like conductance, the inverse of resistance. That’s right: *subsystems in series with full-block handoffs calculate like effective resistance of resistors in parallel.*

## Why This Matters (and Why It’s Funny)

Philosophically, it’s an insult to linearity. We assume systems stack neatly—add times, pick minimums—but here, reciprocity sneaks in. Resistors in parallel? In my RTL? It’s absurdity meets elegance.

Next time you’re verifying a chain of subsystems, ask: is my throughput a min() function, or am I secretly summing inverses? The answer might just fry your FPGA—
