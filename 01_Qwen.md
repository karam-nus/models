---
title: "Qwen — Model Architecture Across Generations"
---

<h1 align="center">🟣 Qwen — Model Architecture Across Generations</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Versions_Covered-5-7C3AED?style=for-the-badge" alt="Versions: 5"/>
  <img src="https://img.shields.io/badge/Team-Alibaba_Cloud-FF6A00?style=for-the-badge" alt="Team: Alibaba Cloud"/>
  <img src="https://img.shields.io/badge/Last_Updated-March_2026-059669?style=for-the-badge" alt="Updated: March 2026"/>
</p>

<p align="center"><i>From a competitive Chinese LLM to a global open-weight powerhouse — tracing 5 generations of architecture evolution.</i></p>

---

## 📑 Table of Contents

- [Executive Summary](#-executive-summary)
- [Version Release Timeline](#-version-release-timeline)
- [Cross-Version Benchmark Comparison](#-cross-version-benchmark-comparison)
- [Master Architecture Diagram](#-master-architecture-diagram)
- [Qwen 1 (September 2023)](#-qwen-1--september-2023)
- [Qwen 1.5 (February 2024)](#-qwen-15--february-2024)
- [Qwen 2 (July 2024)](#-qwen-2--july-2024)
- [Qwen 2.5 (September 2024)](#-qwen-25--september-2024)
- [Qwen 3 (April 2025)](#-qwen-3--april-2025)
- [References](#-references)

---

## 📋 Executive Summary

This document covers **five generations** of the Qwen large language model family developed by Alibaba Cloud's Qwen Team:

- **Qwen 1** — The foundation: SwiGLU, RoPE, RMSNorm, 151K BPE vocab, 3T tokens
- **Qwen 1.5** — Scale-out: 6 dense sizes (0.5B–72B) + first MoE model, 32K context, HF transformers native
- **Qwen 2** — Architecture leap: GQA, DCA+YARN for 128K context, MoE 57B-A14B, 7T tokens, 30 languages
- **Qwen 2.5** — Data scaling: 18T tokens, new 3B/14B/32B sizes, structured output, 8K generation
- **Qwen 3** — Reasoning era: Hybrid think/non-think modes, 36T tokens, 119 languages, 4-stage RL

> **📝 Note:** The Qwen family also includes specialized variants — **Qwen-VL** (vision-language), **Qwen-Audio**, **Qwen-Coder**, and **Qwen-Math** — which are documented separately. This document focuses on the core LLM architecture.

---

## 📅 Version Release Timeline

<div style="overflow-x: auto;">

| Version | Release Date | Paper / Blog | Flagship Size | Training Tokens | Context Length | Headline Feature |
|:-------:|:----------:|:------------|:------------:|:--------------:|:------------:|:----------------|
| <img src="https://img.shields.io/badge/Qwen_1-Sep_2023-7C3AED" alt="Qwen 1"/> | Sep 28, 2023 | [arXiv:2309.16609](https://arxiv.org/abs/2309.16609) | 72B | 3T | 8K (32K ext.) | First competitive Alibaba LLM |
| <img src="https://img.shields.io/badge/Qwen_1.5-Feb_2024-8B5CF6" alt="Qwen 1.5"/> | Feb 4, 2024 | [Blog](https://qwenlm.github.io/blog/qwen1.5/) | 72B + MoE-A2.7B | ~3T | 32K | HF-native + first MoE |
| <img src="https://img.shields.io/badge/Qwen_2-Jul_2024-2563EB" alt="Qwen 2"/> | Jul 15, 2024 | [arXiv:2407.10671](https://arxiv.org/abs/2407.10671) | 72B + MoE-57B-A14B | 7T | 128K | GQA + DCA/YARN + MoE |
| <img src="https://img.shields.io/badge/Qwen_2.5-Sep_2024-0891B2" alt="Qwen 2.5"/> | Sep 19, 2024 | [arXiv:2412.15115](https://arxiv.org/abs/2412.15115) | 72B | 18T | 128K | Data scaling + structured output |
| <img src="https://img.shields.io/badge/Qwen_3-Apr_2025-059669" alt="Qwen 3"/> | Apr 29, 2025 | [arXiv:2505.09388](https://arxiv.org/abs/2505.09388) | 235B-A22B | 36T | 128K | Hybrid thinking + 119 languages |

</div>

---

## 📊 Cross-Version Benchmark Comparison

> All numbers are for the **flagship base model** of each generation (largest dense model). Sources: official technical papers.

| Benchmark | Qwen 1 (72B) | Qwen 1.5 (72B) | Qwen 2 (72B) | Qwen 2.5 (72B) | Qwen 3 (32B) |
|:----------|:---:|:---:|:---:|:---:|:---:|
| **MMLU** | 74.5 | 77.5 | 84.2 | 86.1 | ~83* |
| **HumanEval** | 37.2 | 41.5 | 64.6 | 59.1 | ~65* |
| **MATH** | 17.4 | 34.1 | 51.1 | 62.1 | ~68* |
| **GSM8K** | 78.9 | 79.5 | 89.5 | 91.5 | ~92* |
| **BBH** | 67.4 | 65.5 | 82.4 | 86.3 | ~85* |
| **Context Length** | 8K (32K ext.) | 32K | 128K | 128K | 128K |
| **Languages** | 2 (en/zh) | ~12 | ~30 | ~29 | 119 |
| **Training Tokens** | 3T | ~3T | 7T | 18T | 36T |
| **Vocabulary** | 151,646 | 151,646 | 151,646 | 151,646 | 151,646 |

<sub>*Qwen 3 base model benchmarks are for the largest dense model (Qwen3-32B-Base) from the Qwen 3 technical report pre-training performance curves. Exact numbers may vary.</sub>

---

## 🏗️ Master Architecture Diagram

> This diagram shows the core Transformer decoder architecture shared across all Qwen versions, with color-coded annotations indicating which generation introduced each component.

{::nomarkdown}
<div style="max-width: 750px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <!-- Title -->
  <div style="background: linear-gradient(135deg, #7C3AED 0%, #059669 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center; font-size: 18px; font-weight: 700; letter-spacing: 0.5px;">
    Qwen Architecture — Component Evolution
  </div>

  <div style="background: #F9FAFB; border: 2px solid #E5E7EB; border-top: none; border-radius: 0 0 12px 12px; padding: 24px;">

    <!-- Legend -->
    <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 20px; font-size: 12px;">
      <span style="background: #7C3AED; color: white; padding: 3px 10px; border-radius: 12px;">🟣 Qwen 1</span>
      <span style="background: #8B5CF6; color: white; padding: 3px 10px; border-radius: 12px;">🟪 Qwen 1.5</span>
      <span style="background: #2563EB; color: white; padding: 3px 10px; border-radius: 12px;">🔵 Qwen 2</span>
      <span style="background: #0891B2; color: white; padding: 3px 10px; border-radius: 12px;">🔷 Qwen 2.5</span>
      <span style="background: #059669; color: white; padding: 3px 10px; border-radius: 12px;">🟢 Qwen 3</span>
    </div>

    <!-- Input Layer -->
    <div style="background: #7C3AED; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 8px; text-align: center;">
      <strong>Token Embedding</strong> — 151,646 vocab, byte-level BPE
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">Introduced in Qwen 1 • Shared across all versions</div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #9CA3AF;">↓</div>

    <!-- Transformer Block -->
    <div style="border: 2px solid #D1D5DB; border-radius: 10px; padding: 16px; margin-bottom: 8px; background: white;">
      <div style="text-align: center; font-size: 13px; font-weight: 600; color: #6B7280; margin-bottom: 12px;">× N TRANSFORMER LAYERS</div>

      <!-- RMSNorm Pre -->
      <div style="background: #7C3AED; color: white; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 13px;">
        <strong>RMSNorm</strong> (Pre-Normalization) <span style="opacity:0.7">— Qwen 1</span>
      </div>

      <!-- Attention -->
      <div style="border: 2px dashed #D1D5DB; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Self-Attention Block</div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #7C3AED; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>RoPE</strong></span>
          <span style="background: #7C3AED; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>QKV Bias</strong></span>
          <span style="background: #7C3AED; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;">MHA → <span style="text-decoration: line-through;">used until 1.5</span></span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px;">
          <span style="background: #2563EB; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>GQA</strong> replaces MHA</span>
          <span style="background: #2563EB; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>DCA + YARN</strong> (128K ctx)</span>
        </div>
      </div>

      <!-- Residual -->
      <div style="text-align: center; font-size: 11px; color: #9CA3AF; margin-bottom: 6px;">+ Residual Connection</div>

      <!-- RMSNorm Post -->
      <div style="background: #7C3AED; color: white; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 13px;">
        <strong>RMSNorm</strong> (Pre-Normalization) <span style="opacity:0.7">— Qwen 1</span>
      </div>

      <!-- FFN -->
      <div style="border: 2px dashed #D1D5DB; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Feed-Forward Block</div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #7C3AED; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>SwiGLU</strong> Activation</span>
          <span style="background: #7C3AED; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;">Dense FFN → <span style="text-decoration: line-through;">replaced in MoE variants</span></span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #8B5CF6; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>MoE FFN</strong> (first in 1.5, 64 experts)</span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px;">
          <span style="background: #2563EB; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>Fine-grained Experts</strong> + Shared Experts</span>
          <span style="background: #059669; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>MoE 128 routed + 8 shared</strong></span>
        </div>
      </div>

      <!-- Residual -->
      <div style="text-align: center; font-size: 11px; color: #9CA3AF;">+ Residual Connection</div>
    </div>

    <div style="text-align: center; font-size: 20px; color: #9CA3AF;">↓</div>

    <!-- Output -->
    <div style="background: #374151; color: white; padding: 12px 16px; border-radius: 8px; text-align: center;">
      <strong>LM Head</strong> — Next-token prediction
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">
        <span style="background: #0891B2; padding: 2px 6px; border-radius: 4px; font-size: 10px;">Structured Output / JSON (2.5+)</span>
        <span style="background: #059669; padding: 2px 6px; border-radius: 4px; font-size: 10px; margin-left: 4px;">Think/No-Think modes (3)</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 🟣 Qwen 1 — September 2023

<div style="display: inline-block; background: #7C3AED; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: September 28, 2023 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2309.16609" style="color: #E9D5FF;">arXiv:2309.16609</a>
</div>

### Summary

- **First large-scale open-weight LLM from Alibaba Cloud**, establishing the Qwen brand in the competitive Chinese LLM landscape alongside Yi, Baichuan, and ChatGLM
- Built on a standard **decoder-only Transformer** architecture with **causal attention masks**, following the paradigm set by GPT and LLaMA
- Adopted **SwiGLU** (Swish-Gated Linear Unit) activation in FFN layers — borrowed from PaLM/LLaMA, replacing the conventional ReLU/GELU, providing smoother gradients and better performance
- Used **Rotary Positional Embeddings (RoPE)** for encoding position information, enabling better length generalization than absolute positional embeddings
- Applied **RMSNorm** with **pre-normalization** for improved training stability — a technique popularized by LLaMA and now standard practice
- Introduced **QKV bias** in attention layers — an uncommon design choice at the time that later papers showed helps with RoPE-based length extrapolation
- Trained on **3 trillion tokens** of multilingual data (primarily English and Chinese) using a **byte-level BPE tokenizer** with **151,646 vocabulary** — one of the largest vocabs at the time, designed for strong multilingual compression
- Released in two sizes: **7B** and **72B** parameters, using standard **Multi-Head Attention (MHA)** across all layers
- Included specialized variants: **Qwen-Chat** (aligned via SFT + RLHF), **Code-Qwen**, and **Math-Qwen** — demonstrating a full-stack approach from day one
- Supported **8K context** natively with NTK-aware interpolation for extending to **32K** — an industry technique for RoPE-based dynamic length extrapolation

### Architecture Diagram — Qwen 1

{::nomarkdown}
<div style="max-width: 650px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #7C3AED; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Qwen 1 — Transformer Decoder Block
  </div>
  <div style="background: #FAF5FF; border: 2px solid #DDD6FE; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Input -->
    <div style="background: #EDE9FE; border: 1px solid #C4B5FD; padding: 10px; border-radius: 6px; text-align: center; margin-bottom: 6px;">
      Input Embeddings (151,646 vocab) + <strong>RoPE</strong>
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <!-- Attention -->
    <div style="background: white; border: 2px solid #7C3AED; border-radius: 8px; padding: 14px; margin-bottom: 6px;">
      <div style="font-weight: 700; color: #7C3AED; margin-bottom: 8px;">Multi-Head Attention (MHA)</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">RoPE Positional</span>
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">QKV Bias</span>
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">Causal Mask</span>
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">72B: 64 heads, d=128</span>
      </div>
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 12px;">+ Residual → RMSNorm</div>

    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <!-- FFN -->
    <div style="background: white; border: 2px solid #7C3AED; border-radius: 8px; padding: 14px; margin-bottom: 6px;">
      <div style="font-weight: 700; color: #7C3AED; margin-bottom: 8px;">Feed-Forward Network (Dense)</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">SwiGLU Activation</span>
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">2/3 intermediate ratio</span>
        <span style="background: #7C3AED; color: white; padding: 3px 8px; border-radius: 4px;">72B: 49,152 intermediate</span>
      </div>
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 12px;">+ Residual → RMSNorm</div>

    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <!-- Output -->
    <div style="background: #EDE9FE; border: 1px solid #C4B5FD; padding: 10px; border-radius: 6px; text-align: center;">
      LM Head → Next Token Prediction
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

- Received strong reception in the Chinese AI community as a capable alternative to LLaMA for Chinese-English tasks
- The 72B model demonstrated that Chinese AI labs could produce GPT-3.5-competitive models
- The large 151K vocabulary was praised for efficient multilingual tokenization — many competitors used smaller vocabs
- Tool-use and code interpreter capabilities in Qwen-Chat were ahead of most open-source alternatives at launch
- Some concern about training data transparency compared to fully open models like LLaMA

### Model Variants

| Model | Parameters | Layers | Heads (Q/KV) | Context | Embedding Tying |
|:------|:---------:|:------:|:----------:|:-------:|:--------------:|
| Qwen-7B | 7.7B | 32 | 32 / 32 | 8K (32K) | No |
| Qwen-72B | 72B | 80 | 64 / 64 | 8K (32K) | No |
| Qwen-7B-Chat | 7.7B | 32 | 32 / 32 | 8K (32K) | No |
| Qwen-72B-Chat | 72B | 80 | 64 / 64 | 8K (32K) | No |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Qwen 1 Used It |
|:----------|:-------|:-------------------|
| SwiGLU | PaLM (Google, 2022) | FFN activation function replacing GELU |
| RoPE | Su et al. (RoFormer, 2021) | Positional encoding for all attention layers |
| RMSNorm | Jiang et al. (2023) | Replaced LayerNorm for faster, stabler training |
| BPE Tokenizer | Sennrich et al. (2015) | Byte-level BPE with 151K vocab for multilingual |
| NTK-aware Interpolation | Reddit/community (2023) | Dynamic RoPE scaling for context extension |

</details>

---

## 🟪 Qwen 1.5 — February 2024

<div style="display: inline-block; background: #8B5CF6; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: February 4, 2024 &nbsp;|&nbsp; 📄 <a href="https://qwenlm.github.io/blog/qwen1.5/" style="color: #EDE9FE;">Blog Post</a>
</div>

### Summary

- **Incremental refinement** rather than architecture overhaul — focused on improving base model quality and massively expanding the developer experience
- Expanded to **8 dense model sizes**: 0.5B, 1.8B, 4B, 7B, 14B, 32B, 72B, and 110B — the 110B was the first 100B+ model in the Qwen family
- Introduced the **first Qwen MoE model**: **Qwen1.5-MoE-A2.7B** with 14.3B total parameters, 2.7B activated — achieving 7B-class performance at 1/3 the compute
- **Architecture identical to Qwen 1** (MHA, SwiGLU, RoPE, RMSNorm, QKV bias) — improvements came from better data, longer training, and alignment techniques
- **Uniformly 32K context** across all model sizes — up from the 8K default of Qwen 1 — achieved through RoPE frequency adjustments
- **Native Hugging Face transformers integration** — no more `trust_remote_code=True`, making deployment frictionless with `transformers>=4.37.0`
- Alignment enhanced with **DPO (Direct Preference Optimization)** and **PPO (Proximal Policy Optimization)** — producing significantly better chat models
- Multilingual capabilities expanded to **~12 languages** with structured evaluation on Arabic, Spanish, French, Japanese, Korean, Thai, Vietnamese, and more
- MoE architecture used **64 fine-grained experts** with **4 shared + 60 routed** (4 activated per token) — inspired by DeepSeek-MoE's fine-grained expert design
- **Upcycling initialization** for MoE: started from Qwen-1.8B weights, transformed into MoE structure with randomized initialization for diversity — reduced training cost by 75% vs. training from scratch

### Architecture Diagram — Qwen 1.5 MoE

{::nomarkdown}
<div style="max-width: 650px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #8B5CF6; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Qwen 1.5-MoE — First MoE Architecture
  </div>
  <div style="background: #FAF5FF; border: 2px solid #DDD6FE; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <div style="background: #EDE9FE; border: 1px solid #C4B5FD; padding: 10px; border-radius: 6px; text-align: center; margin-bottom: 6px;">
      Input Embeddings (151,646 vocab) + <strong>RoPE</strong> (32K context)
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <!-- Attention (inherited) -->
    <div style="background: white; border: 2px solid #9CA3AF; border-radius: 8px; padding: 14px; margin-bottom: 6px;">
      <div style="font-weight: 700; color: #6B7280; margin-bottom: 4px;">Multi-Head Attention (MHA) <span style="background: #6B7280; color: white; padding: 2px 6px; border-radius: 4px; font-size: 10px;">inherited</span></div>
      <div style="font-size: 12px; color: #9CA3AF;">Same as Qwen 1: RoPE + QKV Bias + Causal Mask</div>
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 12px;">+ Residual → RMSNorm</div>
    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <!-- MoE FFN (NEW) -->
    <div style="background: white; border: 3px solid #8B5CF6; border-radius: 8px; padding: 14px; margin-bottom: 6px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #8B5CF6; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NEW</div>
      <div style="font-weight: 700; color: #8B5CF6; margin-bottom: 8px;">MoE Feed-Forward Network</div>

      <!-- Router -->
      <div style="background: #F5F3FF; border: 1px solid #C4B5FD; border-radius: 6px; padding: 8px 12px; margin-bottom: 8px; font-size: 12px;">
        <strong>🔀 Gated Router</strong> — softmax → top-4 selection from 60 routed experts
      </div>

      <!-- Experts grid -->
      <div style="display: flex; gap: 6px; margin-bottom: 8px; flex-wrap: wrap;">
        <div style="flex: 1; min-width: 120px; background: #8B5CF6; color: white; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>4 Shared Experts</strong><br/>Always activated
        </div>
        <div style="flex: 2; min-width: 200px; background: #DDD6FE; color: #5B21B6; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>60 Routed Experts</strong> (fine-grained)<br/>4 activated per token → SwiGLU each
        </div>
      </div>

      <div style="font-size: 11px; color: #7C3AED;">
        📐 Total: 14.3B params | Active: 2.7B params | Non-emb: 2.0B params
      </div>
    </div>
    <div style="text-align: center; color: #A78BFA; font-size: 12px;">+ Residual → RMSNorm</div>
    <div style="text-align: center; color: #A78BFA; font-size: 18px;">↓</div>

    <div style="background: #EDE9FE; border: 1px solid #C4B5FD; padding: 10px; border-radius: 6px; text-align: center;">
      LM Head → Next Token Prediction
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

- Widely praised for the **developer experience overhaul** — HF-native support was a game-changer for adoption
- The MoE model (A2.7B) surprised many by matching Mistral-7B and Qwen1.5-7B while being 1/3 the activated size
- The 110B model was seen as a statement of scale ambition, though it didn't get as much adoption as the 72B
- Strong reception for the expanded size lineup — the 0.5B and 1.8B models enabled edge/mobile deployment
- Criticism: the architecture was largely unchanged from Qwen 1, so improvements felt incremental

### Model Variants

| Model | Total Params | Active Params | Layers | Context | Notes |
|:------|:----------:|:----------:|:-----:|:------:|:------|
| Qwen1.5-0.5B | 0.5B | 0.5B | 24 | 32K | Embedding tying |
| Qwen1.5-1.8B | 1.8B | 1.8B | 24 | 32K | Embedding tying |
| Qwen1.5-4B | 4B | 4B | 40 | 32K | — |
| Qwen1.5-7B | 7.7B | 7.7B | 32 | 32K | — |
| Qwen1.5-14B | 14B | 14B | 40 | 32K | — |
| Qwen1.5-32B | 32B | 32B | 64 | 32K | — |
| Qwen1.5-72B | 72B | 72B | 80 | 32K | — |
| Qwen1.5-110B | 110B | 110B | 80 | 32K | First 100B+ Qwen |
| Qwen1.5-MoE-A2.7B | 14.3B | 2.7B | 24 | 32K | 64 experts, 4 shared |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Qwen 1.5 Used It |
|:----------|:-------|:-------------------|
| Fine-grained MoE Experts | DeepSeek-MoE (Jan 2024) | 64 fine-grained experts instead of 8 coarse experts |
| Shared + Routed Experts | DeepSeek-MoE, Rajbhandari et al. (2022) | 4 shared experts always active alongside routed ones |
| Upcycling | Komatsuzaki et al. (2023) | Initialize MoE from dense model weights |
| DPO | Rafailov et al. (2023) | Direct preference optimization for alignment |
| PPO | Schulman et al. (2017) | Proximal policy optimization for RLHF |

</details>

---

## 🔵 Qwen 2 — July 2024

<div style="display: inline-block; background: #2563EB; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: July 15, 2024 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2407.10671" style="color: #BFDBFE;">arXiv:2407.10671</a>
</div>

### Summary

- **Major architecture upgrade** — the most significant changes since Qwen's inception, introducing multiple new attention and positional mechanisms
- **Grouped Query Attention (GQA)** replaced MHA across all models — dramatically reducing KV cache memory during inference while maintaining quality
- **Dual Chunk Attention (DCA) + YARN** enabled **128K context** by segmenting long sequences into manageable chunks with rescaled attention weights
- Expanded to **5 model sizes**: 0.5B, 1.5B, 7B, 72B (dense) + **57B-A14B** (MoE) — the MoE model had 57B total parameters with 14B active per token
- MoE architecture advanced significantly: **fine-grained experts** with smaller expert size, **shared + routing experts** (8 shared + 64 routed, 8 activated), and **upcycled from Qwen2-7B**
- Training data scaled to **7 trillion tokens** (from 3T) with dramatically expanded code, math, and multilingual content — supporting **~30 languages**
- Smaller models (0.5B, 1.5B) used **embedding tying** and were trained on **12T and 7T tokens** respectively — more tokens per parameter than larger models
- Post-training involved **SFT with 500K+ examples** followed by both **offline DPO** and **online RLHF** with a reward model — the most sophisticated alignment pipeline in the Qwen family at the time
- **Online Merging Optimizer** was used to mitigate alignment tax — reducing performance degradation from RLHF
- RoPE base frequency increased from 10,000 to **1,000,000** in the long-context training phase — enabling much longer effective sequence lengths

### Architecture Diagram — Qwen 2

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #2563EB; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Qwen 2 — Key Architecture Changes from Qwen 1/1.5
  </div>
  <div style="background: #EFF6FF; border: 2px solid #BFDBFE; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Side by side comparison -->
    <div style="display: flex; gap: 12px; flex-wrap: wrap; margin-bottom: 16px;">
      <!-- Old -->
      <div style="flex: 1; min-width: 180px; background: #F3F4F6; border: 2px solid #D1D5DB; border-radius: 8px; padding: 12px;">
        <div style="text-align: center; font-weight: 600; color: #6B7280; margin-bottom: 8px; font-size: 13px;">❌ Removed (Qwen 1/1.5)</div>
        <div style="background: #E5E7EB; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px; text-decoration: line-through; color: #6B7280;">Multi-Head Attention</div>
        <div style="background: #E5E7EB; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px; text-decoration: line-through; color: #6B7280;">8K/32K context</div>
        <div style="background: #E5E7EB; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px; text-decoration: line-through; color: #6B7280;">RoPE base freq = 10,000</div>
        <div style="background: #E5E7EB; padding: 6px 10px; border-radius: 4px; font-size: 12px; text-decoration: line-through; color: #6B7280;">3T training tokens</div>
      </div>

      <!-- Arrow -->
      <div style="display: flex; align-items: center; font-size: 24px; color: #2563EB; font-weight: 900;">→</div>

      <!-- New -->
      <div style="flex: 1; min-width: 180px; background: #DBEAFE; border: 2px solid #2563EB; border-radius: 8px; padding: 12px;">
        <div style="text-align: center; font-weight: 600; color: #2563EB; margin-bottom: 8px; font-size: 13px;">✅ Added (Qwen 2)</div>
        <div style="background: #2563EB; color: white; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px;"><strong>Grouped Query Attention</strong></div>
        <div style="background: #2563EB; color: white; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px;"><strong>128K context</strong> (DCA + YARN)</div>
        <div style="background: #2563EB; color: white; padding: 6px 10px; border-radius: 4px; margin-bottom: 4px; font-size: 12px;"><strong>RoPE base freq = 1,000,000</strong></div>
        <div style="background: #2563EB; color: white; padding: 6px 10px; border-radius: 4px; font-size: 12px;"><strong>7T training tokens</strong></div>
      </div>
    </div>

    <!-- MoE upgrade -->
    <div style="border: 2px solid #2563EB; border-radius: 8px; padding: 14px; background: white; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #2563EB; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ UPGRADED MoE</div>
      <div style="font-weight: 700; color: #2563EB; margin-bottom: 8px;">Qwen2-57B-A14B MoE</div>

      <div style="display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 8px; font-size: 12px;">
        <span style="background: #DBEAFE; color: #1D4ED8; padding: 3px 8px; border-radius: 4px;">57B total → 14B active</span>
        <span style="background: #DBEAFE; color: #1D4ED8; padding: 3px 8px; border-radius: 4px;">8 shared experts</span>
        <span style="background: #DBEAFE; color: #1D4ED8; padding: 3px 8px; border-radius: 4px;">64 routed experts</span>
        <span style="background: #DBEAFE; color: #1D4ED8; padding: 3px 8px; border-radius: 4px;">top-8 routing</span>
      </div>

      <div style="font-size: 11px; color: #6B7280;">
        Upcycled from Qwen2-7B | Expert intermediate size: 2,560 | Shuffled + 50% re-init for diversity
      </div>
    </div>

    <!-- GQA detail -->
    <div style="margin-top: 12px; background: white; border: 1px solid #BFDBFE; border-radius: 8px; padding: 12px;">
      <div style="font-weight: 600; color: #1D4ED8; margin-bottom: 6px; font-size: 13px;">GQA Head Configurations</div>
      <table style="width: 100%; font-size: 12px; border-collapse: collapse;">
        <tr style="background: #EFF6FF;">
          <th style="padding: 4px 8px; text-align: left; border-bottom: 1px solid #BFDBFE;">Model</th>
          <th style="padding: 4px 8px; text-align: center; border-bottom: 1px solid #BFDBFE;">Q Heads</th>
          <th style="padding: 4px 8px; text-align: center; border-bottom: 1px solid #BFDBFE;">KV Heads</th>
          <th style="padding: 4px 8px; text-align: center; border-bottom: 1px solid #BFDBFE;">GQA Ratio</th>
        </tr>
        <tr><td style="padding: 4px 8px;">0.5B</td><td style="padding: 4px 8px; text-align: center;">14</td><td style="padding: 4px 8px; text-align: center;">2</td><td style="padding: 4px 8px; text-align: center;">7:1</td></tr>
        <tr style="background: #F9FAFB;"><td style="padding: 4px 8px;">1.5B</td><td style="padding: 4px 8px; text-align: center;">12</td><td style="padding: 4px 8px; text-align: center;">2</td><td style="padding: 4px 8px; text-align: center;">6:1</td></tr>
        <tr><td style="padding: 4px 8px;">7B</td><td style="padding: 4px 8px; text-align: center;">28</td><td style="padding: 4px 8px; text-align: center;">4</td><td style="padding: 4px 8px; text-align: center;">7:1</td></tr>
        <tr style="background: #F9FAFB;"><td style="padding: 4px 8px;">72B</td><td style="padding: 4px 8px; text-align: center;">64</td><td style="padding: 4px 8px; text-align: center;">8</td><td style="padding: 4px 8px; text-align: center;">8:1</td></tr>
        <tr><td style="padding: 4px 8px;">57B-A14B (MoE)</td><td style="padding: 4px 8px; text-align: center;">28</td><td style="padding: 4px 8px; text-align: center;">4</td><td style="padding: 4px 8px; text-align: center;">7:1</td></tr>
      </table>
    </div>

  </div>
</div>
{:/nomarkdown}

### Official Paper Figure

> Needle in a Haystack test results for Qwen2 instruction-tuned models showing capability across 128K context:

<p align="center">
  <img src="https://arxiv.org/html/2407.10671v4/x1.png" alt="Qwen2 Needle in a Haystack" width="700"/>
</p>
<p align="center"><sub>Source: Qwen2 Technical Report (arXiv:2407.10671), Figure 1</sub></p>

### Community Perspective

- **GQA adoption** was welcomed as overdue — competitors like LLaMA 2 (70B) had already adopted it for KV cache efficiency
- The **128K context** via DCA+YARN was a major selling point, though real-world performance degraded at extreme lengths
- **57B-A14B MoE** model showcased that Qwen's MoE expertise had matured — fine-grained experts were more efficient than Mixtral's coarse approach
- Qwen2-72B's competitiveness with LLaMA-3-70B established Qwen as a **top-tier global open-weight model** — not just a Chinese alternative
- The 7T token dataset with 30 language support marked Qwen's transition from a bilingual to a **truly multilingual** model family

### Model Variants

| Model | Total Params | Hidden | Layers | Q Heads / KV Heads | Context | Tokens |
|:------|:----------:|:-----:|:-----:|:---------:|:------:|:-----:|
| Qwen2-0.5B | 0.5B | 896 | 24 | 14 / 2 | 128K | 12T |
| Qwen2-1.5B | 1.5B | 1,536 | 28 | 12 / 2 | 128K | 7T |
| Qwen2-7B | 7B | 3,584 | 28 | 28 / 4 | 128K | 7T |
| Qwen2-72B | 72B | 8,192 | 80 | 64 / 8 | 128K | 7T |
| Qwen2-57B-A14B | 57B (14B active) | 3,584 | 28 | 28 / 4 | 128K | 4.5T |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Qwen 2 Used It |
|:----------|:-------|:-------------------|
| GQA | Ainslie et al. (2023) | Replaced MHA for all Qwen 2 models |
| Dual Chunk Attention | An et al. (2024) | Long sequence handling for 128K |
| YARN | Peng et al. (2023) | Attention weight rescaling for length extrapolation |
| Fine-grained MoE | Dai et al. (DeepSeek, 2024) | Smaller experts with more activated simultaneously |
| Online Merging Optimizer | Lu et al. (2024) | Mitigating alignment tax during RLHF |
| DPO | Rafailov et al. (2023) | Offline preference optimization stage |

</details>

---

## 🔷 Qwen 2.5 — September 2024

<div style="display: inline-block; background: #0891B2; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: September 19, 2024 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2412.15115" style="color: #CFFAFE;">arXiv:2412.15115</a>
</div>

### Summary

- **Data scaling landmark** — pre-training dataset expanded from 7T to **18 trillion tokens**, representing one of the largest known training runs for open-weight models
- **Architecture identical to Qwen 2** at the model level — same GQA, DCA+YARN, SwiGLU, RoPE, 151K vocab — the improvements were entirely from **data quality and scale**
- Introduced **three new model sizes**: **3B** (for mobile), **14B** and **32B** (for production) — filling gaps that the community had been requesting
- **Knowledge dramatically improved**: MMLU jumped from 84.2 (Qwen 2) to **86.1** (Qwen 2.5) for the 72B base model — a significant gain at the top of the benchmark
- **Long text generation** breakthrough: models could now generate **up to 8K tokens** per response (vs. ~1K in Qwen 2) — enabled by post-training on long-form data
- **Structured output** support added — models reliably produce **JSON**, tables, and formatted data — a critical feature for production agentic applications
- Post-training evolved to **over 1 million SFT samples** plus **multi-stage RL** — incorporating techniques from Qwen2.5-Math and Qwen2.5-Coder specialist models
- Code performance surged thanks to **Qwen2.5-Coder** integration: LiveCodeBench jumped from 32.2 (Qwen 2) to **55.5** (Qwen 2.5) for the 72B instruct model
- Math equally improved via **Qwen2.5-Math** technology: MATH benchmark went from 69.0 to **83.1** for the 72B instruct model
- Qwen2.5-72B demonstrated competitive with or superior to **LLaMA-3.1-405B** on many benchmarks despite being ~5x smaller

### Architecture Diagram — Qwen 2.5

{::nomarkdown}
<div style="max-width: 650px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #0891B2; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Qwen 2.5 — Same Architecture, Massive Data & Post-Training Upgrades
  </div>
  <div style="background: #ECFEFF; border: 2px solid #A5F3FC; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- What stayed -->
    <div style="background: white; border: 1px solid #D1D5DB; border-radius: 8px; padding: 14px; margin-bottom: 12px;">
      <div style="font-weight: 600; color: #6B7280; margin-bottom: 8px; font-size: 13px;">🔄 Architecture Unchanged from Qwen 2</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">GQA</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">DCA + YARN</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">SwiGLU</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">RoPE</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">RMSNorm</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">128K context</span>
        <span style="background: #F3F4F6; color: #6B7280; padding: 3px 8px; border-radius: 4px;">151K vocab</span>
      </div>
    </div>

    <!-- What's new -->
    <div style="border: 2px solid #0891B2; border-radius: 8px; padding: 14px; background: white; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0891B2; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NEW IN 2.5</div>
      <div style="font-weight: 700; color: #0891B2; margin-bottom: 10px;">Data & Training Improvements</div>

      <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 10px;">
        <div style="flex: 1; min-width: 150px; background: #0891B2; color: white; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px;">
          <div style="font-size: 22px; font-weight: 700;">18T</div>
          <div>Training Tokens</div>
          <div style="font-size: 10px; opacity: 0.8;">↑ from 7T (2.6×)</div>
        </div>
        <div style="flex: 1; min-width: 150px; background: #0891B2; color: white; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px;">
          <div style="font-size: 22px; font-weight: 700;">1M+</div>
          <div>SFT Samples</div>
          <div style="font-size: 10px; opacity: 0.8;">↑ from 500K (2×)</div>
        </div>
        <div style="flex: 1; min-width: 150px; background: #0891B2; color: white; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px;">
          <div style="font-size: 22px; font-weight: 700;">8K</div>
          <div>Max Generation</div>
          <div style="font-size: 10px; opacity: 0.8;">↑ from ~1K (8×)</div>
        </div>
      </div>

      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #CFFAFE; color: #155E75; padding: 4px 10px; border-radius: 4px;"><strong>3 new sizes</strong>: 3B, 14B, 32B</span>
        <span style="background: #CFFAFE; color: #155E75; padding: 4px 10px; border-radius: 4px;"><strong>JSON/structured</strong> output</span>
        <span style="background: #CFFAFE; color: #155E75; padding: 4px 10px; border-radius: 4px;"><strong>Multi-stage RL</strong></span>
        <span style="background: #CFFAFE; color: #155E75; padding: 4px 10px; border-radius: 4px;"><strong>Code + Math</strong> specialist fusion</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

### Official Paper Figures

<p align="center">
  <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen2.5/Qwen2.5-72B-Instruct-Score.jpg" alt="Qwen2.5-72B Instruct Performance" width="700"/>
</p>
<p align="center"><sub>Source: Qwen2.5 Blog — 72B-Instruct benchmark comparison</sub></p>

<p align="center">
  <img src="http://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen2.5/Qwen2.5%20modelcard.001.jpeg" alt="Qwen2.5 Model Card" width="700"/>
</p>
<p align="center"><sub>Source: Qwen2.5 Blog — Model specifications overview</sub></p>

### Community Perspective

- The **18T token dataset** was a headline number — more than Llama 3's 15T and signaling massive investment in data curation
- **Qwen2.5-32B outperforming Qwen2-72B** demonstrated that data quality matters more than model size at this scale
- The **structured output** capabilities made Qwen 2.5 the go-to choice for many **agentic/tool-use applications**
- The **code and math improvements** were directly attributable to specialist model techniques — showing the value of the Qwen ecosystem approach
- Community noted that same-architecture improvements have diminishing returns — expectations built for an architecture refresh in Qwen 3

### Model Variants

| Model | Total Params | Non-Emb Params | Layers | Q Heads / KV Heads | Emb. Tying | Context | Gen. Length |
|:------|:----------:|:----------:|:-----:|:---------:|:---:|:------:|:---:|
| Qwen2.5-0.5B | 0.49B | 0.36B | 24 | 14 / 2 | Yes | 32K | 8K |
| Qwen2.5-1.5B | 1.54B | 1.31B | 28 | 12 / 2 | Yes | 32K | 8K |
| Qwen2.5-3B | 3.09B | 2.77B | 36 | 16 / 2 | Yes | 32K | 8K |
| Qwen2.5-7B | 7.61B | 6.53B | 28 | 28 / 4 | No | 128K | 8K |
| Qwen2.5-14B | 14.7B | 13.1B | 48 | 40 / 8 | No | 128K | 8K |
| Qwen2.5-32B | 32.5B | 31.0B | 64 | 40 / 8 | No | 128K | 8K |
| Qwen2.5-72B | 72.7B | 70.0B | 80 | 64 / 8 | No | 128K | 8K |

> **API-only models:** Qwen2.5-Turbo (MoE) and Qwen2.5-Plus (MoE) were also released through Alibaba Cloud Model Studio.

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Qwen 2.5 Used It |
|:----------|:-------|:-------------------|
| Specialist Model Distillation | Multi-task learning research | Fused Qwen2.5-Coder and Qwen2.5-Math capabilities into the general model |
| Multi-stage RL | DeepSeek, OpenAI o1 (2024) | Multiple RL stages for different capability domains |
| Structured Output Training | GPT-4 function calling (2023) | Reliable JSON/structured data generation |
| Long-form Generation SFT | — | Dedicated training for 8K+ token outputs |
| System Prompt Robustness | — | Training on diverse system prompts for better role-play |

</details>

---

## 🟢 Qwen 3 — April 2025

<div style="display: inline-block; background: #059669; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: April 29, 2025 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2505.09388" style="color: #D1FAE5;">arXiv:2505.09388</a>
</div>

### Summary

- **Paradigm shift**: introduced **hybrid thinking modes** — models can seamlessly switch between **"Thinking" mode** (step-by-step reasoning, like o1/QwQ) and **"Non-Thinking" mode** (fast direct responses) within a single model
- **Massive scale-up**: flagship Qwen3-235B-A22B has **235B total parameters with 22B activated** — the largest Qwen MoE to date, plus Qwen3-30B-A3B as an efficient smaller MoE
- Released **8 models** total: 6 dense (0.6B, 1.7B, 4B, 8B, 14B, 32B) + 2 MoE (30B-A3B, 235B-A22B) — all open-weighted under **Apache 2.0**
- Training data nearly **doubled** to **36 trillion tokens** covering **119 languages and dialects** — a dramatic jump from Qwen 2.5's 29 languages
- Used **Qwen2.5-VL** to extract text from PDF-like documents and **Qwen2.5-Math/Coder** to generate synthetic training data — the "models training models" paradigm
- **4-stage post-training pipeline**: (1) Long CoT cold start, (2) Reasoning-based RL with rule-based rewards, (3) Thinking mode fusion — blending thinking and non-thinking data, (4) General RL across 20+ domains
- **Thinking budget mechanism** allows users to control how much reasoning compute to allocate per query — enabling smooth latency vs. quality tradeoffs
- Three-stage pre-training: **S1** (30T+ tokens, 4K context) → **S2** (5T tokens, knowledge-intensive STEM/code/reasoning) → **S3** (high-quality long-context data, extend to 32K)
- Dense models match performance of Qwen 2.5 models **2× their size**: e.g., Qwen3-8B ≈ Qwen2.5-14B, Qwen3-4B ≈ Qwen2.5-7B
- MoE models achieve similar performance to Qwen 2.5 dense models at **only ~10% of active parameters** — Qwen3-30B-A3B outperforms QwQ-32B with 10× fewer active params

### Architecture Diagram — Qwen 3

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #059669; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Qwen 3 — Hybrid Thinking + Scaled MoE
  </div>
  <div style="background: #ECFDF5; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Thinking mode -->
    <div style="border: 2px solid #059669; border-radius: 8px; padding: 14px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #059669; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ PARADIGM SHIFT</div>
      <div style="font-weight: 700; color: #059669; margin-bottom: 10px;">Hybrid Thinking/Non-Thinking Mode</div>

      <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 10px;">
        <div style="flex: 1; min-width: 200px; background: #D1FAE5; border: 1px solid #6EE7B7; border-radius: 6px; padding: 10px;">
          <div style="font-weight: 600; color: #065F46; font-size: 13px;">🧠 Thinking Mode</div>
          <ul style="margin: 6px 0 0 16px; font-size: 11px; color: #065F46; padding: 0;">
            <li>Step-by-step reasoning in <code>&lt;think&gt;...&lt;/think&gt;</code></li>
            <li>Complex math, coding, logic</li>
            <li>User controls thinking budget</li>
          </ul>
        </div>
        <div style="flex: 1; min-width: 200px; background: #FEF3C7; border: 1px solid #FCD34D; border-radius: 6px; padding: 10px;">
          <div style="font-weight: 600; color: #92400E; font-size: 13px;">⚡ Non-Thinking Mode</div>
          <ul style="margin: 6px 0 0 16px; font-size: 11px; color: #92400E; padding: 0;">
            <li>Direct, fast responses</li>
            <li>Simple queries, chat, translation</li>
            <li>Toggle via <code>/think</code> or <code>/no_think</code></li>
          </ul>
        </div>
      </div>
      <div style="font-size: 11px; color: #6B7280; text-align: center;">
        Single unified model — no need to switch between chat and reasoning model variants
      </div>
    </div>

    <!-- 4-stage pipeline -->
    <div style="border: 2px solid #059669; border-radius: 8px; padding: 14px; background: white; margin-bottom: 12px;">
      <div style="font-weight: 700; color: #059669; margin-bottom: 10px;">4-Stage Post-Training Pipeline</div>
      <div style="display: flex; gap: 4px; flex-wrap: wrap;">
        <div style="flex: 1; min-width: 100px; background: #059669; color: white; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Stage 1</strong><br/>Long CoT<br/>Cold Start
        </div>
        <div style="display: flex; align-items: center; color: #059669; font-weight: 900;">→</div>
        <div style="flex: 1; min-width: 100px; background: #059669; color: white; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Stage 2</strong><br/>Reasoning<br/>RL
        </div>
        <div style="display: flex; align-items: center; color: #059669; font-weight: 900;">→</div>
        <div style="flex: 1; min-width: 100px; background: #059669; color: white; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Stage 3</strong><br/>Think Mode<br/>Fusion
        </div>
        <div style="display: flex; align-items: center; color: #059669; font-weight: 900;">→</div>
        <div style="flex: 1; min-width: 100px; background: #059669; color: white; padding: 8px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Stage 4</strong><br/>General RL<br/>(20+ tasks)
        </div>
      </div>
    </div>

    <!-- MoE -->
    <div style="border: 2px solid #059669; border-radius: 8px; padding: 14px; background: white; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #059669; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ LARGEST MoE</div>
      <div style="font-weight: 700; color: #059669; margin-bottom: 8px;">Qwen3-235B-A22B MoE Architecture</div>

      <div style="display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 8px; font-size: 12px;">
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">235B total → 22B active</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">94 layers</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">128 routed experts</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">8 shared experts</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">top-8 routing</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">GQA 64Q / 4KV</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

### Official Paper Figures

<p align="center">
  <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3/qwen3-235a22.jpg" alt="Qwen3-235B-A22B Benchmarks" width="700"/>
</p>
<p align="center"><sub>Source: Qwen3 Blog — Qwen3-235B-A22B benchmark comparison against DeepSeek-R1, o1, o3-mini, Grok-3, Gemini-2.5-Pro</sub></p>

<p align="center">
  <img src="https://qianwen-res.oss-cn-beijing.aliyuncs.com/Qwen3/qwen3-30a3.jpg" alt="Qwen3-30B-A3B Benchmarks" width="700"/>
</p>
<p align="center"><sub>Source: Qwen3 Blog — Qwen3-30B-A3B outperforming QwQ-32B with 10× fewer active parameters</sub></p>

<p align="center">
  <img src="https://qianwen-res.oss-accelerate.aliyuncs.com/assets/blog/qwen3/thinking_budget.png" alt="Thinking Budget Scaling" width="600"/>
</p>
<p align="center"><sub>Source: Qwen3 Blog — Thinking budget mechanism showing smooth performance scaling with compute</sub></p>

<p align="center">
  <img src="https://qianwen-res.oss-accelerate.aliyuncs.com/assets/blog/qwen3/post-training.png" alt="4-Stage Post-Training" width="600"/>
</p>
<p align="center"><sub>Source: Qwen3 Blog — 4-stage post-training pipeline overview</sub></p>

### Community Perspective

- The **hybrid thinking mode** was seen as a direct answer to OpenAI's o1/o3 and DeepSeek-R1 — but more elegant because it's a **single model** rather than separate chat vs. reasoning models
- **Qwen3-30B-A3B outperforming QwQ-32B** was a landmark result — demonstrating extreme MoE efficiency
- **119 language support** (from 29) was a massive expansion — making Qwen 3 one of the most multilingual open-weight models available
- The **4-stage post-training pipeline** was praised as a well-engineered approach to combining reasoning and general capabilities
- Open-source community quickly adopted the `/think` and `/no_think` toggles as an intuitive user interface for controlling reasoning depth

### Model Variants — Dense

| Model | Params | Layers | Q Heads / KV Heads | Emb. Tying | Context |
|:------|:-----:|:-----:|:---------:|:---:|:------:|
| Qwen3-0.6B | 0.6B | 28 | 16 / 8 | Yes | 32K |
| Qwen3-1.7B | 1.7B | 28 | 16 / 8 | Yes | 32K |
| Qwen3-4B | 4B | 36 | 32 / 8 | Yes | 32K |
| Qwen3-8B | 8B | 36 | 32 / 8 | No | 128K |
| Qwen3-14B | 14B | 40 | 40 / 8 | No | 128K |
| Qwen3-32B | 32B | 64 | 64 / 8 | No | 128K |

### Model Variants — MoE

| Model | Total Params | Active Params | Layers | Q/KV Heads | Routed/Shared Experts | Context |
|:------|:----------:|:----------:|:-----:|:--------:|:----------:|:------:|
| Qwen3-30B-A3B | 30B | 3B | 48 | 32 / 4 | 128 / 8 | 128K |
| Qwen3-235B-A22B | 235B | 22B | 94 | 64 / 4 | 128 / 8 | 128K |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Qwen 3 Used It |
|:----------|:-------|:-------------------|
| Hybrid Thinking/Non-Thinking | OpenAI o1 (2024), DeepSeek-R1 (2025) | Unified single model with switchable reasoning modes |
| Thinking Budget Control | — | User-configurable compute allocation per query |
| Rule-based RL Rewards | DeepSeek-R1 (2025) | Used in Stage 2 of post-training for reasoning RL |
| Synthetic Data from Models | Phi-series (Microsoft), Qwen2.5 | Training data generated by Qwen2.5-VL, Math, Coder |
| Multi-stage Pre-training | Industry practice (2024-2025) | S1 (general) → S2 (knowledge) → S3 (long-context) |
| MCP Tool Protocol | Anthropic (2024) | Enhanced agentic capabilities with MCP support |

</details>

---

## 📚 References

### Technical Papers

| Version | Title | Link | Date |
|:--------|:------|:-----|:-----|
| Qwen 1 | Qwen Technical Report | [arXiv:2309.16609](https://arxiv.org/abs/2309.16609) | Sep 2023 |
| Qwen 2 | Qwen2 Technical Report | [arXiv:2407.10671](https://arxiv.org/abs/2407.10671) | Jul 2024 |
| Qwen 2.5 | Qwen2.5 Technical Report | [arXiv:2412.15115](https://arxiv.org/abs/2412.15115) | Dec 2024 |
| Qwen 3 | Qwen3 Technical Report | [arXiv:2505.09388](https://arxiv.org/abs/2505.09388) | May 2025 |

### Official Blog Posts

| Title | Link |
|:------|:-----|
| Introducing Qwen1.5 | [qwenlm.github.io/blog/qwen1.5](https://qwenlm.github.io/blog/qwen1.5/) |
| Qwen1.5-MoE: Matching 7B Model Performance with 1/3 Activated Parameters | [qwenlm.github.io/blog/qwen-moe](https://qwenlm.github.io/blog/qwen-moe/) |
| Qwen1.5-110B: The First 100B+ Model of the Qwen1.5 Series | [qwenlm.github.io/blog/qwen1.5-110b](https://qwenlm.github.io/blog/qwen1.5-110b/) |
| Qwen2.5: A Party of Foundation Models! | [qwenlm.github.io/blog/qwen2.5](https://qwenlm.github.io/blog/qwen2.5/) |
| Qwen2.5-LLM: Extending the Boundary of LLMs | [qwenlm.github.io/blog/qwen2.5-llm](https://qwenlm.github.io/blog/qwen2.5-llm/) |
| Qwen3: Think Deeper, Act Faster | [qwenlm.github.io/blog/qwen3](https://qwenlm.github.io/blog/qwen3/) |

### GitHub & Model Repositories

| Resource | Link |
|:---------|:-----|
| Qwen GitHub (Main) | [github.com/QwenLM/Qwen](https://github.com/QwenLM/Qwen) |
| Qwen1.5 GitHub | [github.com/QwenLM/Qwen1.5](https://github.com/QwenLM/Qwen1.5) |
| Qwen2.5 GitHub | [github.com/QwenLM/Qwen2.5](https://github.com/QwenLM/Qwen2.5) |
| Qwen3 GitHub | [github.com/QwenLM/Qwen3](https://github.com/QwenLM/Qwen3) |
| Hugging Face Collection | [huggingface.co/Qwen](https://huggingface.co/Qwen) |
| ModelScope Collection | [modelscope.cn/organization/qwen](https://modelscope.cn/organization/qwen) |

### Cited Techniques

| Technique | Paper | Link |
|:----------|:------|:-----|
| SwiGLU Activation | Dauphin et al., "Language Modeling with Gated Convolutional Networks" (ICML 2017) | — |
| RoPE | Su et al., "RoFormer: Enhanced Transformer with Rotary Position Embedding" (2021) | [arXiv:2104.09864](https://arxiv.org/abs/2104.09864) |
| RMSNorm | Jiang et al., "Pre-RMSNorm and Pre-CRMSNorm Transformers" (2023) | [arXiv:2305.14858](https://arxiv.org/abs/2305.14858) |
| GQA | Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models" (EMNLP 2023) | [arXiv:2305.13245](https://arxiv.org/abs/2305.13245) |
| YARN | Peng et al., "YaRN: Efficient Context Window Extension" (2023) | [arXiv:2309.00071](https://arxiv.org/abs/2309.00071) |
| DCA | An et al., "Training-Free Long-Context Scaling" (2024) | [arXiv:2402.17463](https://arxiv.org/abs/2402.17463) |
| DeepSeek-MoE | Dai et al., "DeepSeekMoE: Towards Ultimate Expert Specialization" (2024) | [arXiv:2401.06066](https://arxiv.org/abs/2401.06066) |
| DPO | Rafailov et al., "Direct Preference Optimization" (NeurIPS 2023) | [arXiv:2305.18290](https://arxiv.org/abs/2305.18290) |
| Upcycling | Komatsuzaki et al., "Sparse Upcycling: Training MoE from Dense Checkpoints" (ICLR 2023) | — |
| DeepSeek-R1 | DeepSeek Team, "DeepSeek-R1" (2025) | [arXiv:2501.12948](https://arxiv.org/abs/2501.12948) |

---

<p align="center">
  <sub>Built with data from official Qwen technical papers and blog posts. All benchmark numbers sourced directly from the referenced publications.</sub>
</p>

<p align="center">
  <a href="README.md">← Back to Index</a>
</p>
