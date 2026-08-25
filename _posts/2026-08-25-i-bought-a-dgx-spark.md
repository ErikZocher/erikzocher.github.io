---
layout: post
title: "I Bought a DGX Spark. Here's What It Actually Is."
date: 2026-08-25 07:00:00 +0200
description: "A practical introduction to the NVIDIA DGX Spark and GB10 superchip: what the 128 GB unified memory actually means, which partner machines exist (ASUS Ascent, Lenovo PGX, HP ZGX, Dell), and what you can — and can't — do with a €4,000 AI mini-PC."
tags: [ai, hardware, dgx-spark, gb10, local-llm, llm, self-hosting]
categories: [technology]
---

# I Bought a DGX Spark. Here's What It Actually Is.

The package was smaller than I expected. A black box roughly the size of a Mac mini arrived, and inside was a machine that Nvidia calls "the world's smallest AI supercomputer." It cost about €4,000. And it can run a 284-billion-parameter language model entirely offline.

This is the first post in a series about living with a DGX Spark-class machine — the ASUS Ascent GX10. I'll cover setup, running models, and what actually works. This one is the "what is this thing" post.

## The chip: GB10 Grace Blackwell

Every DGX Spark-class machine runs the same chip: Nvidia's **GB10 Grace Blackwell Superchip**.

- **20 ARM CPU cores** (10× Cortex-X925 + 10× Cortex-A725) — this is a phone-style chip, not an x86 desktop CPU
- **128 GB LPDDR5x unified memory** at 273 GB/s
- **Blackwell GPU** rated at 1,000 TOPS (FP4)
- **~1.2 kg**, about 25 W at idle
- Runs **Linux only** (DGX OS, Ubuntu-based)

The key spec is the **128 GB unified memory**. That's the whole product. A desktop GPU gives you 8-24 GB of VRAM; this gives you 128 GB that the CPU and GPU share. It's not fast memory by workstation standards (273 GB/s vs. a 5090's 1.8 TB/s), but it is *huge* — and size matters more than speed for running large models.

## What 128 GB actually unlocks

| Task | Typical desktop (8-24 GB VRAM) | DGX Spark (128 GB) |
|---|---|---|
| 7-13B chat models | ✅ | ✅ easily |
| 30B MoE models | ⚠️ tight | ✅ fast |
| 70B+ models | ❌ | ✅ |
| 120-284B MoE (DeepSeek V4 Flash) | ❌ | ✅ (quantized) |
| 1M-token context | ❌ | ✅ |
| FLUX/Wan video generation | ❌ (video) | ✅ |

The honest framing: **the Spark trades speed for capacity.** It won't beat a 5090 in raw token generation. But it can run models that simply don't fit on consumer hardware — and for agent workloads (where prompt processing dominates), the GB10's prefill performance is surprisingly strong.

## The family: which machine did I pick?

Nvidia sells the reference design as the **DGX Spark Founders Edition**, but partners build their own:

| Machine | Typical price (DE, Aug 2026) | Notes |
|---|---|---|
| **ASUS Ascent GX10** (mine) | ~€3,999 | 1 TB SSD, 3 yr warranty |
| Lenovo ThinkStation PGX | ~€4,784 | 3 yr warranty, SSD upgradeable |
| HP ZGX Nano G1n | ~€5,200+ (1 TB AT deal: €3,580) | 37 offers at idealo |
| NVIDIA DGX Spark Founders | ~€5,599 | 4 TB SSD, 1 yr warranty |
| Dell Pro Max with GB10 | ~€5,576+ | Business support |

**Where these prices come from.** All figures are the *lowest listed offers* on German price-comparison sites, checked on **19-20 August 2026**: [Geizhals.de](https://geizhals.de), [idealo.de](https://www.idealo.de), and their Austrian siblings ([Geizhals.at](https://geizhals.at), [idealo.at](https://www.idealo.at)) — plus direct shop listings (e-tec.at, cyberport.de, notebooksbilliger.de, galaxus.at). Prices were pulled from the comparison engines' offer lists, which aggregate shop inventory in real time. A few caveats on the numbers:

- **Availability fluctuates.** Geizhals showed *no offers at all* for the Lenovo PGX on some days, while idealo listed it — inventory comes and goes.
- **The HP 1 TB at €3,580 is an Austrian deal** (idealo.at), not a German one. The same machine lists around €5,200 in Germany. Cross-border shipping usually works, but check VAT and delivery terms.
- **The ASUS Ascent GX10 at €3,999** is the 1 TB model — the 4 TB version costs roughly €5,400.
- Prices move fast on these machines; treat the table as a snapshot, not a promise. This is why I checked multiple sources rather than trusting a single shop.

They're all the same chip. You're choosing SSD size, warranty, and chassis — not performance. I picked the **ASUS Ascent GX10** because it was the cheapest entry point with a 3-year warranty, and I plan to upgrade the storage.

## What you can actually do with it

Realistic use cases, in order of what I've found works well:

1. **Local LLM serving** — Ollama, vLLM, or Nvidia NIM. This is the core use case, and it works great.
2. **Agent workloads** — running an AI agent (like the one helping me write this) entirely offline. No API bills.
3. **Image generation** — ComfyUI runs on it, and can handle models too big for 8 GB GPUs.
4. **Video generation** — possible (FLUX→Wan workflows exist), but plan your memory: it shares the 128 GB with everything else.
5. **Fine-tuning small models** — it has the capacity, though it's not a training powerhouse.

## The honest caveats

- **No Windows.** DGX OS is Linux. If you need x86/Windows software, keep a desktop.
- **Storage fills up fast.** 1 TB is plenty for models if you're disciplined — the 83 GB DeepSeek V4 Flash + ComfyUI models fit fine, but video models accumulate.
- **One GPU, one budget.** LLM + image generation run *in parallel*, but they share the 128 GB. Plan your memory, not just your disk.
- **It's not a gaming PC.** Don't buy one for that.

## What's next in this series

1. ~~I Bought a DGX Spark. Here's What It Actually Is.~~ ← you are here
2. How I Set Up the ASUS Ascent GX10 (First Boot to SSH)
3. Running DeepSeek V4 Flash Locally on a DGX Spark
4. Ollama vs vLLM on a DGX Spark: Real Numbers
5. ComfyUI on the DGX Spark: Images AND Video

*This post was written on the machine it describes — well, almost. The agent helping me draft it runs on DeepSeek V4 Flash today, and will run on my GX10 by the time this series finishes.*
