---
title: "Gemma — Model Architecture Across Generations"
---

<h1 align="center">🔵 Gemma — Model Architecture Across Generations</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Versions_Covered-4-0EA5E9?style=for-the-badge" alt="Versions: 4"/>
  <img src="https://img.shields.io/badge/Team-Google_DeepMind-0284C7?style=for-the-badge" alt="Team: Google DeepMind"/>
  <img src="https://img.shields.io/badge/Last_Updated-April_2026-4F46E5?style=for-the-badge" alt="Updated: April 2026"/>
</p>

<p align="center"><i>From Google DeepMind's first open-weight Gemini descendant to a fully Apache-licensed multimodal family — tracing four generations of architecture innovation.</i></p>

---

## 📑 Table of Contents

- [Executive Summary](#-executive-summary)
- [Version Release Timeline](#-version-release-timeline)
- [Cross-Version Benchmark Comparison](#-cross-version-benchmark-comparison)
- [Master Architecture Diagram](#-master-architecture-diagram)
- [Gemma 1 (February 2024)](#-gemma-1--february-2024)
- [Gemma 2 (June 2024)](#-gemma-2--june-2024)
- [Gemma 3 (March 2025)](#-gemma-3--march-2025)
- [Gemma 4 (April 2026)](#-gemma-4--april-2026)
- [References](#-references)

---

## 📋 Executive Summary

This document covers **four generations** of the Gemma large language model family developed by Google DeepMind:

- **Gemma 1** — The foundation: decoder-only Transformer distilled from Gemini, GeGLU activation, logit soft-capping, 256K BPE vocab, 6T tokens, 2B & 7B sizes
- **Gemma 2** — Architecture leap: alternating Sliding Window + Global attention, knowledge distillation from 27B teacher, double normalization, GQA for all sizes, 8K context
- **Gemma 3** — Multimodal expansion: SigLIP vision encoder, 128K long context, hybrid 5:1 local/global attention ratio, 256K vocab, 140+ languages, QAT support
- **Gemma 4** — Open-weight frontier: Apache 2.0 license, MoE variant (26B A4B), Per-Layer Embeddings (E2B/E4B), SigLIP 2 vision + native audio (E2B/E4B), 256K context, thinking mode, agentic tool use

> **📝 Note:** The Gemma family also includes specialized variants — **CodeGemma** (code generation), **PaliGemma** (vision-language), **RecurrentGemma** (linear recurrent), and **ShieldGemma** (safety classifier) — which are documented separately. This document focuses on the core Gemma LLM architecture.

---

## 📅 Version Release Timeline

<div style="overflow-x: auto;">

| Version | Release Date | Paper / Blog | Flagship Size | Training Tokens | Context Length | Headline Feature |
|:-------:|:----------:|:------------|:------------:|:--------------:|:------------:|:----------------|
| <img src="https://img.shields.io/badge/Gemma_1-Feb_2024-0EA5E9" alt="Gemma 1"/> | Feb 21, 2024 | [arXiv:2403.08295](https://arxiv.org/abs/2403.08295) | 7B | 6T | 8K | First open Gemini-based model |
| <img src="https://img.shields.io/badge/Gemma_2-Jun_2024-0284C7" alt="Gemma 2"/> | Jun 27, 2024 | [arXiv:2408.00118](https://arxiv.org/abs/2408.00118) | 27B | 13T | 8K | SWA + Global attn + KD |
| <img src="https://img.shields.io/badge/Gemma_3-Mar_2025-0369A1" alt="Gemma 3"/> | Mar 12, 2025 | [arXiv:2503.19786](https://arxiv.org/abs/2503.19786) | 27B | 12T | 128K | Multimodal + 140 languages |
| <img src="https://img.shields.io/badge/Gemma_4-Apr_2026-4F46E5" alt="Gemma 4"/> | Apr 2, 2026 | [Model Card](https://ai.google.dev/gemma/docs/core/model_card_4) · [Blog](https://opensource.googleblog.com/2026/04/gemma-4-expanding-gemmaverse-apache-2.html) | 31B | — | 256K | Apache 2.0 · MoE · PLE · Audio · Thinking |

</div>

---

## 📊 Cross-Version Benchmark Comparison

> Numbers reflect the best comparable model size across generations. Sources: official technical papers.

| Benchmark | Gemma 1 (7B) | Gemma 2 (9B) | Gemma 2 (27B) | Gemma 3 (12B) | Gemma 3 (27B) | Gemma 4 (26B MoE) | Gemma 4 (31B) |
|:----------|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **MMLU** | 64.3 | 71.3 | 75.2 | 74.0 | 81.0 | 82.6 | 85.2 |
| **HumanEval** | 32.3 | 40.2 | 51.8 | 57.9 | 69.7 | ~87.0 | 91.4 |
| **MATH** | 24.3 | 36.7 | 42.4 | 43.3 | 67.6 | ~88.0 | 92.1 |
| **GSM8K** | 46.4 | 68.6 | 74.0 | 79.6 | 89.7 | 88.4 | 91.2 |
| **Context Length** | 8K | 8K | 8K | 128K | 128K | 256K | 256K |
| **Training Tokens** | 6T | 8T | 13T | 12T | 12T | — | — |
| **Multimodal** | ❌ | ❌ | ❌ | ✅ | ✅ | ✅ (img) | ✅ (img) |
| **Vocabulary** | 256,128 | 256,128 | 256,128 | 262,144 | 262,144 | 262,144 | 262,144 |

<sub>*Gemma 1–3 base-model numbers sourced from official technical reports. Gemma 4 numbers are instruct-model results from the official model card and Google Open Source blog (Apr 2026). MMLU column for Gemma 4 reflects MMLU Pro. HumanEval/MATH for Gemma 4 26B MoE are approximate ranges from multiple evaluations.</sub>

---

## 🏗️ Master Architecture Diagram

> This diagram shows the core Transformer decoder architecture shared across all Gemma versions, with color-coded annotations indicating which generation introduced each component.

{::nomarkdown}
<div style="max-width: 780px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <!-- Title -->
  <div style="background: linear-gradient(135deg, #0EA5E9 0%, #0369A1 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center; font-size: 18px; font-weight: 700; letter-spacing: 0.5px;">
    Gemma Architecture — Component Evolution Across Generations
  </div>

  <div style="background: #F0F9FF; border: 2px solid #BAE6FD; border-top: none; border-radius: 0 0 12px 12px; padding: 24px;">

    <!-- Legend -->
    <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 20px; font-size: 12px;">
      <span style="background: #0EA5E9; color: white; padding: 3px 10px; border-radius: 12px;">🔵 Gemma 1</span>
      <span style="background: #0284C7; color: white; padding: 3px 10px; border-radius: 12px;">🔷 Gemma 2</span>
      <span style="background: #0369A1; color: white; padding: 3px 10px; border-radius: 12px;">🟦 Gemma 3</span>
      <span style="background: #4F46E5; color: white; padding: 3px 10px; border-radius: 12px;">🟪 Gemma 4</span>
    </div>

    <!-- Input Layer -->
    <div style="background: #0EA5E9; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 8px; text-align: center;">
      <strong>Token Embedding</strong> — SentencePiece BPE
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">256,128 vocab (Gemma 1 &amp; 2) → 262,144 vocab (Gemma 3) • Tied embeddings (input = output weights)</div>
    </div>

    <!-- Vision Input (Gemma 3 only) -->
    <div style="background: #0369A1; color: white; padding: 10px 16px; border-radius: 8px; margin-bottom: 8px; text-align: center; font-size: 13px;">
      <strong>SigLIP Vision Encoder</strong> <span style="opacity: 0.7;">(Gemma 3) / SigLIP 2 (Gemma 4)</span>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">ViT-based, 896×896px, Pan &amp; Scan multi-crop → 256 soft image tokens interleaved with text</div>
    </div>

    <!-- Audio Input (Gemma 4 E2B/E4B only) -->
    <div style="background: #4F46E5; color: white; padding: 10px 16px; border-radius: 8px; margin-bottom: 8px; text-align: center; font-size: 13px;">
      <strong>Audio Encoder</strong> <span style="opacity: 0.7;">(Gemma 4 E2B / E4B only)</span>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">Native audio input; audio tokens interleaved with text tokens in the decoder</div>
    </div>

    <div style="text-align: center; font-size: 20px; color: #7DD3FC;">↓</div>

    <!-- Transformer Block -->
    <div style="border: 2px solid #BAE6FD; border-radius: 10px; padding: 16px; margin-bottom: 8px; background: white;">
      <div style="text-align: center; font-size: 13px; font-weight: 600; color: #6B7280; margin-bottom: 12px;">× N TRANSFORMER LAYERS</div>

      <!-- RMSNorm Pre -->
      <div style="background: #0EA5E9; color: white; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 13px;">
        <strong>RMSNorm</strong> (Pre-Normalization) <span style="opacity: 0.7;">— Gemma 1</span>
      </div>

      <!-- Attention Block -->
      <div style="border: 2px dashed #BAE6FD; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Self-Attention Block</div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #0EA5E9; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>RoPE</strong> θ=10,000</span>
          <span style="background: #0EA5E9; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>MHA</strong> (no QKV bias)</span>
          <span style="background: #0EA5E9; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>Logit soft-cap</strong> ±30</span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #0284C7; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>GQA</strong> replaces MHA</span>
          <span style="background: #0284C7; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>SWA</strong> (odd layers, win=4096)</span>
          <span style="background: #0284C7; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>Global attn</strong> (even layers)</span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px;">
          <span style="background: #0369A1; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>RoPE</strong> θ=1,000,000</span>
          <span style="background: #0369A1; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>5:1</strong> local/global ratio</span>
          <span style="background: #0369A1; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>128K</strong> context</span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-top: 6px;">
          <span style="background: #4F46E5; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>p-RoPE</strong> p=0.25 (256K ctx)</span>
          <span style="background: #4F46E5; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>K=V trick</strong> (global attn)</span>
          <span style="background: #4F46E5; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>Shared KV cache</strong></span>
        </div>
      </div>

      <!-- Residual -->
      <div style="text-align: center; font-size: 11px; color: #9CA3AF; margin-bottom: 6px;">+ Residual Connection</div>

      <!-- Post-Norm (Gemma 2+) -->
      <div style="background: #0284C7; color: white; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 13px;">
        <strong>RMSNorm</strong> (Post-Normalization) <span style="opacity: 0.7;">— Added in Gemma 2 (double norm)</span>
      </div>

      <!-- FFN -->
      <div style="border: 2px dashed #BAE6FD; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Feed-Forward Block</div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 8px;">
          <span style="background: #0EA5E9; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>GeGLU</strong> Activation</span>
          <span style="background: #0EA5E9; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;">Dense FFN (no MoE)</span>
        </div>

        <div style="display: flex; flex-wrap: wrap; gap: 6px;">
          <span style="background: #4F46E5; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px;"><strong>MoE</strong> 128+1 experts, top-8 (Gemma 4 26B)</span>
          <span style="background: #6B7280; color: white; padding: 4px 10px; border-radius: 6px; font-size: 12px; opacity: 0.8;">GeGLU retained across all generations</span>
        </div>
      </div>

      <!-- Residual -->
      <div style="text-align: center; font-size: 11px; color: #9CA3AF;">+ Residual Connection</div>
    </div>

    <div style="text-align: center; font-size: 20px; color: #7DD3FC;">↓</div>

    <!-- Logit Soft-Capping -->
    <div style="background: #0284C7; color: white; padding: 10px 16px; border-radius: 8px; margin-bottom: 8px; text-align: center; font-size: 13px;">
      <strong>Final Logit Soft-Capping</strong> <span style="opacity: 0.7;">— tanh(x/cap)×cap</span>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">Gemma 1: cap=30 &nbsp;|&nbsp; Gemma 2: cap=50 &nbsp;|&nbsp; Applied before softmax to stabilize training</div>
    </div>

    <div style="text-align: center; font-size: 20px; color: #7DD3FC;">↓</div>

    <!-- Output -->
    <div style="background: #374151; color: white; padding: 12px 16px; border-radius: 8px; text-align: center;">
      <strong>LM Head</strong> — Tied with input embeddings → Next-token prediction
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">
        <span style="background: #0369A1; padding: 2px 6px; border-radius: 4px; font-size: 10px;">Multimodal output (Gemma 3)</span>
        <span style="background: #0284C7; padding: 2px 6px; border-radius: 4px; font-size: 10px; margin-left: 4px;">KD from 27B teacher (Gemma 2)</span>
        <span style="background: #4F46E5; padding: 2px 6px; border-radius: 4px; font-size: 10px; margin-left: 4px;">Thinking mode / tool use (Gemma 4)</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 🔵 Gemma 1 — February 2024

{::nomarkdown}
<div style="display: inline-block; background: #0EA5E9; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: February 21, 2024 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2403.08295" style="color: #BAE6FD;">arXiv:2403.08295</a>
</div>
{:/nomarkdown}

### Summary

- **Google DeepMind's first open-weight model** built directly on the Gemini architecture, making the Gemini-family techniques publicly accessible for research and commercial use — a landmark moment for the open AI ecosystem
- Built on a **decoder-only Transformer** with **causal attention masks**, following the now-standard autoregressive language modeling paradigm established by GPT and refined through LLaMA and Gemini
- Uses **Multi-Head Attention (MHA)** without Grouped Query Attention — all query, key, and value heads are independent — with **no QKV bias**, a deliberate design choice to reduce parameter overhead while maintaining expressivity
- Adopts **Rotary Positional Embeddings (RoPE)** with a base frequency of **θ=10,000**, the same default as LLaMA — providing relative position encoding that generalizes well to sequences up to the training length
- Employs **GeGLU activation** in the FFN layers: `GeGLU(x) = GELU(xW₁) ⊗ (xW₂)` — this is distinct from SwiGLU used by LLaMA/Qwen, using GELU (Gaussian Error Linear Unit) as the gate rather than Swish, providing a smoother activation landscape
- Introduces **logit soft-capping** at the attention layer: `softcap(x) = tanh(x/30) × 30` — applied to attention logits **before** the softmax operation, preventing extreme values and stabilizing training without a hard clip that would destroy gradients
- Applies **RMSNorm** with both **pre-normalization** (before attention and FFN sub-layers) — following the LLaMA convention — and also uses **post-normalization** on the final output, borrowing the double-norm strategy from Gemini
- Uses a **SentencePiece BPE tokenizer** with a vocabulary of **256,128 tokens** — dramatically larger than LLaMA's 32K and Mistral's 32K, providing far superior multilingual tokenization efficiency and byte-level coverage for rare characters
- **Ties input embeddings to output projection weights** — the embedding matrix doubles as the LM head, halving the number of parameters in those layers and improving parameter efficiency especially at the 2B scale
- Trained on **6 trillion tokens** (both 2B and 7B models) sourced from web text, code repositories, and mathematical content — filtered and deduplicated using quality heuristics similar to those used for Gemini's training pipeline
- Released in **two sizes**: **2B** (18 layers, 8 heads, head_dim=256, d_model=2048, intermediate_size=16,384) and **7B** (28 layers, 16 heads, head_dim=256, d_model=3072, intermediate_size=24,576)
- The **head_dim=256** is notably larger than the typical 128 used by LLaMA at equivalent scales — this larger per-head dimension was carried over from Gemini's design and allows each attention head to capture richer representations
- Accompanied by instruction-tuned variants (**Gemma 1-IT**) aligned via **Supervised Fine-Tuning (SFT)** and **Reinforcement Learning from Human Feedback (RLHF)** using a reward model trained on human preference data
- Established the Gemma naming convention and the foundational technical choices — GeGLU, logit soft-capping, 256K vocabulary, tied embeddings — that persist as defining characteristics of the entire Gemma family

### Architecture Diagram — Gemma 1

{::nomarkdown}
<div style="max-width: 660px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #0EA5E9; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Gemma 1 — Transformer Decoder Block (per layer)
  </div>
  <div style="background: #F0F9FF; border: 2px solid #BAE6FD; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Input -->
    <div style="background: #E0F2FE; border: 1px solid #7DD3FC; padding: 10px; border-radius: 6px; text-align: center; margin-bottom: 6px;">
      Input Token Embeddings (256,128 vocab) + <strong>RoPE</strong> (θ=10,000) — <em>Tied with LM Head</em>
    </div>
    <div style="text-align: center; color: #38BDF8; font-size: 18px;">↓</div>

    <!-- RMSNorm pre -->
    <div style="background: #0EA5E9; color: white; padding: 6px 12px; border-radius: 6px; margin-bottom: 4px; font-size: 13px; text-align: center;">
      <strong>RMSNorm</strong> (Pre-Norm — before attention)
    </div>
    <div style="text-align: center; color: #38BDF8; font-size: 18px;">↓</div>

    <!-- Attention -->
    <div style="background: white; border: 2px solid #0EA5E9; border-radius: 8px; padding: 14px; margin-bottom: 4px;">
      <div style="font-weight: 700; color: #0EA5E9; margin-bottom: 8px;">Multi-Head Attention (MHA)</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px; margin-bottom: 8px;">
        <span style="background: #0EA5E9; color: white; padding: 3px 8px; border-radius: 4px;">RoPE Positional Encoding</span>
        <span style="background: #0EA5E9; color: white; padding: 3px 8px; border-radius: 4px;">No QKV Bias</span>
        <span style="background: #0EA5E9; color: white; padding: 3px 8px; border-radius: 4px;">Causal Attention Mask</span>
      </div>
      <div style="background: #FFF7ED; border: 1px solid #FED7AA; border-radius: 6px; padding: 8px; font-size: 12px;">
        <strong>⚡ Logit Soft-Capping:</strong> &nbsp;
        <code style="background: #FEF3C7; padding: 2px 6px; border-radius: 3px;">attn_logit = tanh(logit / 30) × 30</code>
        &nbsp; (before softmax)
      </div>
      <div style="margin-top: 8px; font-size: 11px; color: #6B7280;">
        2B: 8 Q heads, head_dim=256, d_model=2048 &nbsp;|&nbsp; 7B: 16 Q heads, head_dim=256, d_model=3072
      </div>
    </div>
    <div style="text-align: center; color: #38BDF8; font-size: 12px;">+ Residual Connection → RMSNorm (Pre-Norm before FFN)</div>
    <div style="text-align: center; color: #38BDF8; font-size: 18px;">↓</div>

    <!-- FFN -->
    <div style="background: white; border: 2px solid #0EA5E9; border-radius: 8px; padding: 14px; margin-bottom: 4px;">
      <div style="font-weight: 700; color: #0EA5E9; margin-bottom: 8px;">Feed-Forward Network (Dense)</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px; margin-bottom: 8px;">
        <span style="background: #0EA5E9; color: white; padding: 3px 8px; border-radius: 4px;"><strong>GeGLU</strong> Activation</span>
        <span style="background: #E0F2FE; color: #0369A1; padding: 3px 8px; border-radius: 4px;">GELU(xW₁) ⊗ xW₂</span>
      </div>
      <div style="font-size: 11px; color: #6B7280;">
        2B: intermediate=16,384 (8× d_model) &nbsp;|&nbsp; 7B: intermediate=24,576 (8× d_model)
      </div>
    </div>
    <div style="text-align: center; color: #38BDF8; font-size: 12px;">+ Residual Connection</div>
    <div style="text-align: center; color: #38BDF8; font-size: 18px;">↓</div>

    <!-- Final logit softcap -->
    <div style="background: #FFF7ED; border: 1px solid #FED7AA; padding: 8px 12px; border-radius: 6px; text-align: center; margin-bottom: 4px; font-size: 12px;">
      <strong>Final Logit Soft-Cap:</strong> &nbsp;<code>tanh(logit / 30) × 30</code> &nbsp; (after final RMSNorm, before softmax)
    </div>
    <div style="text-align: center; color: #38BDF8; font-size: 18px;">↓</div>

    <!-- Output -->
    <div style="background: #E0F2FE; border: 1px solid #7DD3FC; padding: 10px; border-radius: 6px; text-align: center;">
      LM Head (= Embedding matrix, tied) → Next Token Prediction
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

- Received **enormous enthusiasm** from the open-source AI community — it was the first time Google had released open-weight models derived from the Gemini architecture, democratizing access to Gemini-class techniques
- The **GeGLU activation** (vs. SwiGLU in LLaMA) sparked debate; empirically the performance gap is small, but the theoretical distinction (GELU gate vs. Swish gate) was noted by researchers as a deliberate Gemini heritage choice
- The **logit soft-capping** mechanism was widely praised as an elegant alternative to gradient clipping — it prevents attention score explosion without the discrete cutoff of hard capping, and practitioners noted more stable fine-tuning behavior
- The **256K vocabulary** was a major talking point — far larger than LLaMA's 32K, it enables superior tokenization of non-Latin scripts, code, and mathematical notation, though it also means larger embedding layers
- The **7B model** was benchmarked as competitive with Mistral-7B and LLaMA-2-13B despite being released months later — the community quickly integrated Gemma into tools like LM Studio, Ollama, and llama.cpp
- Some criticism around the **relatively small training dataset** (6T tokens) compared to contemporaries like Mistral (estimated 3T but much smaller model), and the limited context window of 8K vs. Mistral's 32K
- The **tied embeddings** design was appreciated for memory efficiency, particularly beneficial for the 2B model on consumer hardware
- **Commercial usability** under the Gemma Terms of Use (not fully open-source Apache 2.0) was noted as a limitation for some use cases, compared to LLaMA's or Mistral's licenses

### Model Variants

| Model | Parameters | Layers | Q Heads | head_dim | d_model | Intermediate | Context | Training Tokens |
|:------|:---------:|:------:|:-------:|:--------:|:-------:|:-----------:|:-------:|:--------------:|
| Gemma-2B | 2B | 18 | 8 | 256 | 2048 | 16,384 | 8K | 6T |
| Gemma-7B | 7B | 28 | 16 | 256 | 3072 | 24,576 | 8K | 6T |
| Gemma-2B-IT | 2B | 18 | 8 | 256 | 2048 | 16,384 | 8K | 6T + SFT/RLHF |
| Gemma-7B-IT | 7B | 28 | 16 | 256 | 3072 | 24,576 | 8K | 6T + SFT/RLHF |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Gemma 1 Used It |
|:----------|:-------|:--------------------|
| GeGLU Activation | Dauphin et al. (2017), Noam Shazeer (2020) | FFN gating function: GELU gate replaces standard ReLU or GELU, inherited from Gemini |
| RoPE | Su et al., "RoFormer" (2021) | Relative positional encoding applied to Q and K projections, θ=10,000 |
| RMSNorm | Zhang & Sennrich (2019) | Pre-normalization before each sub-layer for stable gradient flow |
| Logit Soft-Capping | Google DeepMind / Gemini (2023) | tanh-based attention logit capping at ±30 to prevent score explosion |
| SentencePiece BPE | Kudo & Richardson (2018) | 256,128-token vocabulary with byte-level fallback for rare chars |
| Tied Embeddings | Press & Wolf (2017) | Input embedding matrix reused as output projection for parameter efficiency |
| SFT + RLHF | Ziegler et al. (2019), Ouyang et al. InstructGPT (2022) | Standard instruction tuning + human preference alignment pipeline |

</details>

---

## 🔷 Gemma 2 — June 2024

{::nomarkdown}
<div style="display: inline-block; background: #0284C7; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: June 27, 2024 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2408.00118" style="color: #BAE6FD;">arXiv:2408.00118</a>
</div>
{:/nomarkdown}

### Summary

- **Most architecturally innovative Gemma release**: introduced **alternating Sliding Window Attention (SWA) and Global Attention** — odd-indexed layers use local sliding window attention (window_size=4096) while even-indexed layers use full global attention, allowing the model to capture both local context and long-range dependencies efficiently within an 8K context window
- **Logit soft-capping refined** with two separate caps: **±30 for attention logits** (same as Gemma 1) and a new **±50 for the final output logits** — the asymmetric capping acknowledges that final logit distributions require wider dynamic range than within-layer attention scores
- **Grouped Query Attention (GQA)** replaces MHA for all model sizes — the 2B model uses 8 Q heads / 4 KV heads, the 9B uses 16 Q heads / 8 KV heads, and the 27B uses 32 Q heads / 16 KV heads — dramatically reducing KV cache memory at inference time
- **Knowledge distillation** is central to the training methodology: smaller models (2B and 9B) are distilled from the 27B teacher model using both **token-level KL-divergence minimization** and next-token prediction loss — achieving significantly better performance than training from scratch at equivalent compute
- Expands to **three model sizes**: **2B**, **9B**, and **27B** — the 27B represents a 4× scale-up from Gemma 1's 7B, enabling much stronger reasoning and knowledge retention
- Adds **post-normalization** (in addition to the existing pre-normalization from Gemma 1): both the output of attention and the output of the FFN are normalized before the residual addition — creating a **double normalization** scheme that significantly improves training stability at larger scales
- **GeGLU activation** is retained across all layers and model sizes — a deliberate architectural continuity choice that preserves the Gemini heritage and avoids the overhead of architecture search
- **Context window of 8,192 tokens** (8K) — unchanged from Gemma 1, but the alternating SWA pattern means local layers only attend to the nearest 4,096 tokens, while global layers have full 8K visibility, providing effective hierarchical attention coverage
- The **2B model architecture** is substantially larger than Gemma 1's 2B: 26 layers (vs. 18), d_model=2304 (vs. 2048), intermediate=9,216 — reflecting the distillation-boosted efficiency allowing more capacity at equivalent parameter count
- **9B model dimensions**: 42 layers, d_model=3,584, intermediate=14,336 — the 42-layer depth is notable as a non-standard choice (most 9B models use 32-36 layers), reflecting the trade-off toward depth over width in Gemma's design philosophy
- **27B model dimensions**: 46 layers, d_model=4,608, intermediate=36,864 — trained on a full 13T tokens, making it the most data-rich Gemma model at launch
- **Training data scales** with model size: 2B trained on 2T tokens, 9B on 8T tokens, 27B on 13T tokens — demonstrating Google's compute-optimal scaling approach inspired by Chinchilla
- **Post-training alignment** uses a combination of **SFT, RLHF, and distillation-based alignment**: the instruction-tuned models inherit behavioral priors from the 27B-IT teacher via distillation, enabling smaller instruct models to match the alignment quality of larger ones
- **Significantly outperforms Gemma 1** across all benchmarks: GSM8K improves from 46.4 (7B) to 68.6 (9B), MMLU from 64.3 to 71.3 — demonstrating that the SWA + GQA + distillation innovations translate directly to capability improvements

### Architecture Diagram — Gemma 2

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #0284C7; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Gemma 2 — Alternating Attention + Knowledge Distillation
  </div>
  <div style="background: #F0F9FF; border: 2px solid #7DD3FC; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Alternating attention diagram -->
    <div style="border: 2px solid #0284C7; border-radius: 8px; padding: 14px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0284C7; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ KEY INNOVATION</div>
      <div style="font-weight: 700; color: #0284C7; margin-bottom: 10px;">Alternating Attention Pattern</div>

      <div style="display: flex; gap: 6px; flex-wrap: wrap; margin-bottom: 10px;">
        <div style="flex: 1; min-width: 180px; background: #DBEAFE; border: 2px solid #0284C7; border-radius: 6px; padding: 10px; text-align: center;">
          <div style="font-weight: 700; color: #0284C7; font-size: 13px;">ODD Layers (1,3,5...)</div>
          <div style="font-size: 12px; color: #1E40AF; margin-top: 4px;"><strong>Local SWA</strong></div>
          <div style="font-size: 11px; color: #374151; margin-top: 4px;">Window = 4,096 tokens<br/>Only attends to recent tokens<br/>O(n × w) complexity</div>
        </div>
        <div style="display: flex; align-items: center; font-size: 20px; color: #6B7280;">⇄</div>
        <div style="flex: 1; min-width: 180px; background: #EFF6FF; border: 2px solid #93C5FD; border-radius: 6px; padding: 10px; text-align: center;">
          <div style="font-weight: 700; color: #1D4ED8; font-size: 13px;">EVEN Layers (2,4,6...)</div>
          <div style="font-size: 12px; color: #1E40AF; margin-top: 4px;"><strong>Global Attention</strong></div>
          <div style="font-size: 11px; color: #374151; margin-top: 4px;">Full 8,192-token context<br/>All-to-all attention<br/>O(n²) complexity</div>
        </div>
      </div>

      <div style="background: #F0F9FF; border: 1px solid #BAE6FD; border-radius: 6px; padding: 8px; font-size: 12px; text-align: center;">
        📐 Local layers: fast, O(n×w) — Global layers: expressive, O(n²) — Alternation balances efficiency vs. recall
      </div>
    </div>

    <!-- GQA configs -->
    <div style="background: white; border: 2px solid #0284C7; border-radius: 8px; padding: 14px; margin-bottom: 12px;">
      <div style="font-weight: 700; color: #0284C7; margin-bottom: 8px; font-size: 13px;">Grouped Query Attention (GQA) — All Sizes</div>
      <table style="width: 100%; font-size: 12px; border-collapse: collapse;">
        <tr style="background: #DBEAFE;">
          <th style="padding: 6px 8px; text-align: left; border-bottom: 1px solid #BAE6FD;">Model</th>
          <th style="padding: 6px 8px; text-align: center; border-bottom: 1px solid #BAE6FD;">Q Heads</th>
          <th style="padding: 6px 8px; text-align: center; border-bottom: 1px solid #BAE6FD;">KV Heads</th>
          <th style="padding: 6px 8px; text-align: center; border-bottom: 1px solid #BAE6FD;">GQA Ratio</th>
          <th style="padding: 6px 8px; text-align: center; border-bottom: 1px solid #BAE6FD;">Layers</th>
          <th style="padding: 6px 8px; text-align: center; border-bottom: 1px solid #BAE6FD;">d_model</th>
        </tr>
        <tr><td style="padding: 5px 8px;">Gemma 2 — 2B</td><td style="padding: 5px 8px; text-align: center;">8</td><td style="padding: 5px 8px; text-align: center;">4</td><td style="padding: 5px 8px; text-align: center;">2:1</td><td style="padding: 5px 8px; text-align: center;">26</td><td style="padding: 5px 8px; text-align: center;">2,304</td></tr>
        <tr style="background: #F0F9FF;"><td style="padding: 5px 8px;">Gemma 2 — 9B</td><td style="padding: 5px 8px; text-align: center;">16</td><td style="padding: 5px 8px; text-align: center;">8</td><td style="padding: 5px 8px; text-align: center;">2:1</td><td style="padding: 5px 8px; text-align: center;">42</td><td style="padding: 5px 8px; text-align: center;">3,584</td></tr>
        <tr><td style="padding: 5px 8px;">Gemma 2 — 27B</td><td style="padding: 5px 8px; text-align: center;">32</td><td style="padding: 5px 8px; text-align: center;">16</td><td style="padding: 5px 8px; text-align: center;">2:1</td><td style="padding: 5px 8px; text-align: center;">46</td><td style="padding: 5px 8px; text-align: center;">4,608</td></tr>
      </table>
    </div>

    <!-- KD pipeline -->
    <div style="border: 2px solid #0284C7; border-radius: 8px; padding: 14px; background: white; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0284C7; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ DISTILLATION STRATEGY</div>
      <div style="font-weight: 700; color: #0284C7; margin-bottom: 10px;">Knowledge Distillation Pipeline</div>

      <div style="display: flex; gap: 8px; flex-wrap: wrap; align-items: center;">
        <div style="background: #0284C7; color: white; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px; flex: 0 0 auto; min-width: 90px;">
          <strong>Teacher</strong><br/>Gemma 2 27B<br/><span style="font-size: 10px; opacity: 0.8;">Trained from scratch</span>
        </div>
        <div style="font-size: 20px; color: #0284C7; font-weight: 900;">→</div>
        <div style="flex: 1; min-width: 100px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px; color: #1E40AF;">
          <strong>Student: 9B</strong><br/>KL(teacher ∥ student)<br/><span style="font-size: 10px;">+ NTP loss</span>
        </div>
        <div style="font-size: 20px; color: #0284C7; font-weight: 900;">→</div>
        <div style="flex: 1; min-width: 100px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 10px; border-radius: 6px; text-align: center; font-size: 12px; color: #1E40AF;">
          <strong>Student: 2B</strong><br/>Distilled from 27B<br/><span style="font-size: 10px;">+ NTP loss</span>
        </div>
      </div>

      <div style="margin-top: 10px; font-size: 11px; color: #6B7280; text-align: center;">
        Token-level KL divergence + standard cross-entropy. Smaller models learn soft probability distributions from teacher, not just hard labels.
      </div>
    </div>

    <!-- Double norm -->
    <div style="margin-top: 12px; background: white; border: 1px solid #BAE6FD; border-radius: 8px; padding: 12px;">
      <div style="font-weight: 600; color: #0284C7; margin-bottom: 6px; font-size: 13px;">⚙️ Double Normalization (Pre + Post)</div>
      <div style="display: flex; gap: 8px; flex-wrap: wrap; font-size: 12px;">
        <div style="background: #E0F2FE; color: #0369A1; padding: 6px 10px; border-radius: 4px;">Pre-Norm (before attn/FFN)</div>
        <div style="font-size: 16px; display: flex; align-items: center; color: #0284C7;">+</div>
        <div style="background: #0284C7; color: white; padding: 6px 10px; border-radius: 4px;"><strong>Post-Norm</strong> (NEW in Gemma 2)</div>
        <div style="font-size: 16px; display: flex; align-items: center; color: #0284C7;">=</div>
        <div style="background: #DBEAFE; color: #1E40AF; padding: 6px 10px; border-radius: 4px;">Stable large-scale training</div>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

- The **alternating SWA + global attention** pattern was seen as an elegant engineering solution — it halves the computational cost of attention layers by making half of them local while preserving long-range modeling through interleaved global layers
- **Knowledge distillation from 27B** gave the 2B and 9B models capabilities well above their parameter count would suggest — the community quickly noticed that Gemma 2 2B outperformed many 7B models from competing families
- The **double normalization** (pre + post) was unusual and prompted analysis — researchers found it improves gradient flow significantly at 27B scale, but adds marginal overhead at 2B
- The **GQA ratio of 2:1** (e.g., 16 Q heads, 8 KV heads) is more conservative than Qwen2's 7:1 or 8:1 ratios — indicating Google's preference for maintaining attention quality over maximizing KV cache reduction
- **Gemma 2 27B surpassing Llama 3 70B** on several benchmarks despite being less than half the size was a landmark result that cemented Gemma 2 as a top-tier open-weight family
- The soft-capping of **final logits at ±50** (larger than ±30 for attention) was noted as a practical detail — preventing overconfident probability distributions at the output while allowing more dynamic range than the attention layers
- Community noted that the **8K context window** felt limiting given that contemporaries like LLaMA 3 and Qwen 2 were pushing to 128K — this became a major point of improvement for Gemma 3

### Model Variants

| Model | Parameters | Layers | Q Heads / KV Heads | d_model | Intermediate | Context | Training Tokens |
|:------|:---------:|:------:|:------------------:|:-------:|:-----------:|:-------:|:--------------:|
| Gemma2-2B | 2B | 26 | 8 / 4 | 2,304 | 9,216 | 8K | 2T |
| Gemma2-9B | 9B | 42 | 16 / 8 | 3,584 | 14,336 | 8K | 8T |
| Gemma2-27B | 27B | 46 | 32 / 16 | 4,608 | 36,864 | 8K | 13T |
| Gemma2-2B-IT | 2B | 26 | 8 / 4 | 2,304 | 9,216 | 8K | 2T + SFT/RLHF/KD |
| Gemma2-9B-IT | 9B | 42 | 16 / 8 | 3,584 | 14,336 | 8K | 8T + SFT/RLHF/KD |
| Gemma2-27B-IT | 27B | 46 | 32 / 16 | 4,608 | 36,864 | 8K | 13T + SFT/RLHF |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Gemma 2 Used It |
|:----------|:-------|:--------------------|
| Sliding Window Attention | Longformer (Beltagy et al., 2020), Mistral (2023) | Odd-indexed layers use SWA with window_size=4096 for O(n×w) local attention |
| Global Attention Interleaving | LongT5 (Guo et al., 2022), BigBird (Zaheer et al., 2020) | Even-indexed layers use full global attention to maintain long-range coherence |
| Knowledge Distillation | Hinton et al. (2015), DistilBERT (2019) | Soft-label KL divergence from 27B teacher to 2B and 9B students during pre-training |
| GQA | Ainslie et al. (2023) | Replaces MHA for all sizes; 2:1 ratio balances quality vs. KV cache efficiency |
| Double Normalization (Pre + Post) | Gemini (Google, 2023) | Applies RMSNorm both before and after attention and FFN sub-layers |
| Logit Soft-Capping | Gemini (Google, 2023) | Separate caps for attention (±30) vs. final output logits (±50) |
| RLHF with KD | Anthropic, OpenAI | Instruction-tuned variants combine human preference data with distillation from 27B-IT |

</details>

---

## 🟦 Gemma 3 — March 2025

{::nomarkdown}
<div style="display: inline-block; background: #0369A1; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: March 12, 2025 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2503.19786" style="color: #BAE6FD;">arXiv:2503.19786</a>
</div>
{:/nomarkdown}

### Summary

- **Major multimodal expansion**: introduces a native **SigLIP-based vision encoder** for 4B, 12B, and 27B models — images are processed at **896×896 pixel resolution** through a ViT (Vision Transformer) backbone that produces **256 soft image tokens**, which are directly interleaved with text tokens in the decoder, enabling unified multimodal understanding without a separate cross-attention module
- **Pan & Scan multi-crop strategy** handles variable-resolution images: the input image is divided into multiple overlapping crops at the native resolution, each processed independently by the SigLIP encoder, then concatenated — this allows arbitrary-aspect-ratio inputs and high-fidelity processing of text in images, charts, and photographs
- **Context window expands dramatically**: 128K tokens for the 4B, 12B, and 27B models (a 16× increase from Gemma 2's 8K), and 32K for the 1B text-only model — enabling long document analysis, multi-turn conversations, and extended code context
- **RoPE base frequency scaled to θ=1,000,000** (up from 10,000 in Gemma 1 and 2) — this 100× increase is critical for supporting 128K context, as higher base frequencies allow RoPE to represent much larger relative position differences without frequency aliasing
- **Hybrid attention with 5:1 local-to-global ratio**: five consecutive sliding window attention layers are followed by one full global attention layer — compared to Gemma 2's 1:1 alternating pattern, this 5:1 ratio dramatically reduces the computational cost of attention for long sequences while the periodic global layers maintain coherence
- Local SWA layers use a sliding window of **1,024 tokens** (4B/12B) or **4,096 tokens** (27B) — smaller windows than Gemma 2's 4,096, reflecting the 5:1 ratio design where local layers prioritize speed
- **Upgraded tokenizer** derived from the Gemini 2.0 tokenizer with an expanded **262,144-token vocabulary** (up from 256,128 in Gemma 1/2) — supporting 140+ languages with improved tokenization efficiency for multilingual text, including better coverage for CJK characters and low-resource languages
- Expands to **four model sizes**: **1B** (text-only), **4B**, **12B**, and **27B** — the 1B model fills the on-device/mobile niche while the 12B fills the gap between 9B (Gemma 2) and 27B for production inference
- **GQA across all sizes** with consistent 2:1 Q-to-KV head ratios: 1B (8Q/4KV), 4B (8Q/4KV), 12B (16Q/8KV), 27B (32Q/16KV) — maintaining the Gemma 2 convention
- **Gemma 3 4B-IT matches Gemma 2 27B-IT** in performance — a remarkable 6.75× parameter efficiency improvement attributed to 12T training tokens, knowledge distillation, and improved architecture (hybrid attention + long context)
- **Training data scaled to 12T tokens per model** (vs. 2-13T varying in Gemma 2), covering **140+ languages** — a dramatic multilingual expansion compared to Gemma 2's primarily English-focused pre-training corpus
- **Post-training pipeline** includes: (1) large-scale SFT on instruction-following, code, math, and tool use; (2) RLHF with human preference data; (3) knowledge distillation from the 27B-IT model to smaller models — the same three-stage approach as Gemma 2 but at larger scale
- **Quantization-Aware Training (QAT)** support is built into the training pipeline — official INT4 and INT8 quantized variants are released alongside the float16 checkpoints, enabling deployment on mobile and edge devices without significant quality degradation
- **ShieldGemma 3** companion safety classifier is released alongside — trained specifically to identify harmful content in Gemma 3 inputs and outputs, providing an integrated responsible AI toolkit for production deployments
- **Knowledge distillation continues** from 27B to smaller models, with the 4B and 12B distilled models benefiting from both the 27B pre-training teacher and the 27B-IT alignment teacher

### Architecture Diagram — Gemma 3

{::nomarkdown}
<div style="max-width: 750px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #0369A1; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Gemma 3 — Multimodal Architecture with Hybrid Attention
  </div>
  <div style="background: #F0F9FF; border: 2px solid #7DD3FC; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Vision pathway -->
    <div style="border: 2px solid #0369A1; border-radius: 8px; padding: 14px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0369A1; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NEW: VISION</div>
      <div style="font-weight: 700; color: #0369A1; margin-bottom: 10px;">SigLIP Vision Encoder (4B / 12B / 27B only)</div>

      <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 10px; align-items: stretch;">
        <div style="flex: 1; min-width: 120px; background: #E0F2FE; border: 1px solid #7DD3FC; padding: 10px; border-radius: 6px; text-align: center; font-size: 11px; color: #0369A1;">
          <strong>Input Image</strong><br/>Any resolution<br/>Pan &amp; Scan crops
        </div>
        <div style="display: flex; align-items: center; color: #0369A1; font-size: 20px;">→</div>
        <div style="flex: 1; min-width: 120px; background: #0369A1; color: white; padding: 10px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>SigLIP ViT</strong><br/>896×896px<br/>per crop
        </div>
        <div style="display: flex; align-items: center; color: #0369A1; font-size: 20px;">→</div>
        <div style="flex: 1; min-width: 120px; background: #E0F2FE; border: 1px solid #7DD3FC; padding: 10px; border-radius: 6px; text-align: center; font-size: 11px; color: #0369A1;">
          <strong>256 image tokens</strong><br/>per crop<br/>interleaved with text
        </div>
      </div>

      <div style="background: #F0F9FF; border: 1px solid #BAE6FD; border-radius: 6px; padding: 8px; font-size: 11px; color: #374151; text-align: center;">
        Image tokens and text tokens are concatenated in the same sequence — no separate cross-attention module needed
      </div>
    </div>

    <!-- 5:1 hybrid attention -->
    <div style="border: 2px solid #0369A1; border-radius: 8px; padding: 14px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0369A1; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ 5:1 HYBRID ATTENTION</div>
      <div style="font-weight: 700; color: #0369A1; margin-bottom: 10px;">Hybrid Attention Pattern (5 Local + 1 Global)</div>

      <div style="display: flex; gap: 4px; flex-wrap: nowrap; margin-bottom: 10px; overflow-x: auto;">
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #0369A1; color: white; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Global</strong><br/>Full<br/>128K ctx
        </div>
        <div style="display: flex; align-items: center; font-size: 14px; color: #6B7280; padding: 0 4px;">...</div>
      </div>

      <div style="display: flex; gap: 8px; flex-wrap: wrap; font-size: 12px;">
        <div style="background: #DBEAFE; color: #1E40AF; padding: 5px 10px; border-radius: 4px;">🔵 5× Local SWA: O(n × w), fast, captures syntax</div>
        <div style="background: #0369A1; color: white; padding: 5px 10px; border-radius: 4px;">🟦 1× Global: O(n²), slow, captures semantics</div>
      </div>
    </div>

    <!-- Long context + RoPE -->
    <div style="background: white; border: 1px solid #BAE6FD; border-radius: 8px; padding: 12px; margin-bottom: 12px;">
      <div style="font-weight: 600; color: #0369A1; margin-bottom: 6px; font-size: 13px;">🔭 Long Context Engineering</div>
      <div style="display: flex; gap: 8px; flex-wrap: wrap; font-size: 12px;">
        <div style="background: #0369A1; color: white; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-size: 18px; font-weight: 700;">128K</div>
          <div>Max context (4B/12B/27B)</div>
        </div>
        <div style="background: #0369A1; color: white; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-size: 18px; font-weight: 700;">θ=10⁶</div>
          <div>RoPE base freq (100× Gemma 2)</div>
        </div>
        <div style="background: #0369A1; color: white; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-size: 18px; font-weight: 700;">5:1</div>
          <div>Local : Global attn ratio</div>
        </div>
      </div>
    </div>

    <!-- Model size configs -->
    <div style="background: white; border: 2px solid #0369A1; border-radius: 8px; padding: 14px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #0369A1; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">4 SIZES</div>
      <div style="font-weight: 700; color: #0369A1; margin-bottom: 8px; font-size: 13px;">Model Size Configurations</div>
      <table style="width: 100%; font-size: 11px; border-collapse: collapse;">
        <tr style="background: #DBEAFE;">
          <th style="padding: 5px 6px; text-align: left; border-bottom: 1px solid #BAE6FD;">Model</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">Layers</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">d_model</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">Q/KV Heads</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">Intermediate</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">Context</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #BAE6FD;">Vision</th>
        </tr>
        <tr><td style="padding: 4px 6px;">Gemma 3 — 1B</td><td style="padding: 4px 6px; text-align: center;">18</td><td style="padding: 4px 6px; text-align: center;">1,152</td><td style="padding: 4px 6px; text-align: center;">8 / 4</td><td style="padding: 4px 6px; text-align: center;">6,912</td><td style="padding: 4px 6px; text-align: center;">32K</td><td style="padding: 4px 6px; text-align: center;">❌</td></tr>
        <tr style="background: #F0F9FF;"><td style="padding: 4px 6px;">Gemma 3 — 4B</td><td style="padding: 4px 6px; text-align: center;">34</td><td style="padding: 4px 6px; text-align: center;">2,560</td><td style="padding: 4px 6px; text-align: center;">8 / 4</td><td style="padding: 4px 6px; text-align: center;">10,240</td><td style="padding: 4px 6px; text-align: center;">128K</td><td style="padding: 4px 6px; text-align: center;">✅</td></tr>
        <tr><td style="padding: 4px 6px;">Gemma 3 — 12B</td><td style="padding: 4px 6px; text-align: center;">48</td><td style="padding: 4px 6px; text-align: center;">3,840</td><td style="padding: 4px 6px; text-align: center;">16 / 8</td><td style="padding: 4px 6px; text-align: center;">15,360</td><td style="padding: 4px 6px; text-align: center;">128K</td><td style="padding: 4px 6px; text-align: center;">✅</td></tr>
        <tr style="background: #F0F9FF;"><td style="padding: 4px 6px;">Gemma 3 — 27B</td><td style="padding: 4px 6px; text-align: center;">46</td><td style="padding: 4px 6px; text-align: center;">4,608</td><td style="padding: 4px 6px; text-align: center;">32 / 16</td><td style="padding: 4px 6px; text-align: center;">36,864</td><td style="padding: 4px 6px; text-align: center;">128K</td><td style="padding: 4px 6px; text-align: center;">✅</td></tr>
      </table>
    </div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

- The **4B-IT matching 27B-IT from Gemma 2** was the headline result that shocked the community — a 6.75× parameter efficiency jump in one generation, attributed to the 12T training corpus, improved hybrid attention, and aggressive distillation
- The **SigLIP vision integration** was welcomed as a clean multimodal architecture — interleaving image tokens directly with text tokens (rather than a separate cross-attention module as in LLaVA-style models) is simpler and scales better
- **Pan & Scan** image processing was praised for its practical handling of high-resolution and variable-aspect-ratio images — crucial for document understanding, screenshots, and diagrams
- The **5:1 local/global attention ratio** represents a different architectural bet than Gemma 2's 1:1 alternating pattern — community experiments showed 5:1 is more compute-efficient for long sequences while maintaining comparable quality
- The **128K context window** finally brought Gemma up to parity with LLaMA 3.1 and Qwen 2 on long-context tasks — a relief for practitioners who found Gemma 2's 8K limiting
- **QAT support** with officially released INT4/INT8 quantized models was praised as a responsible release practice — many users immediately deployed the 4B INT4 model on consumer laptops and mobile devices
- **ShieldGemma** as a companion safety classifier was lauded as part of a responsible AI deployment toolkit — though some noted that bundling safety tools separately from the model itself leaves room for misuse
- **140+ language support** represented a massive step forward for multilingual use cases, with the community noting improved tokenizer efficiency and generation quality for low-resource languages compared to Gemma 2
- The **1B text-only model** with 32K context was positioned as the best-in-class on-device LLM, fitting comfortably in 4-bit quantized form on smartphones — sparking discussions about on-device AI applications

### Model Variants

| Model | Parameters | Layers | Q Heads / KV Heads | d_model | Intermediate | Context | Vision | Training Tokens |
|:------|:---------:|:------:|:------------------:|:-------:|:-----------:|:-------:|:------:|:--------------:|
| Gemma3-1B | 1B | 18 | 8 / 4 | 1,152 | 6,912 | 32K | ❌ | 12T |
| Gemma3-4B | 4B | 34 | 8 / 4 | 2,560 | 10,240 | 128K | ✅ | 12T |
| Gemma3-12B | 12B | 48 | 16 / 8 | 3,840 | 15,360 | 128K | ✅ | 12T |
| Gemma3-27B | 27B | 46 | 32 / 16 | 4,608 | 36,864 | 128K | ✅ | 12T |
| Gemma3-1B-IT | 1B | 18 | 8 / 4 | 1,152 | 6,912 | 32K | ❌ | 12T + SFT/RLHF |
| Gemma3-4B-IT | 4B | 34 | 8 / 4 | 2,560 | 10,240 | 128K | ✅ | 12T + SFT/RLHF/KD |
| Gemma3-12B-IT | 12B | 48 | 16 / 8 | 3,840 | 15,360 | 128K | ✅ | 12T + SFT/RLHF/KD |
| Gemma3-27B-IT | 27B | 46 | 32 / 16 | 4,608 | 36,864 | 128K | ✅ | 12T + SFT/RLHF |
| Gemma3-1B-PT (QAT) | 1B | 18 | 8 / 4 | 1,152 | 6,912 | 32K | ❌ | INT4/INT8 quantized |
| Gemma3-4B-PT (QAT) | 4B | 34 | 8 / 4 | 2,560 | 10,240 | 128K | ✅ | INT4/INT8 quantized |

> **Companion models:** ShieldGemma 3 (27B safety classifier), ShieldGemma 3 (4B) — released alongside Gemma 3 for responsible deployment.

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Gemma 3 Used It |
|:----------|:-------|:--------------------|
| SigLIP Vision Encoder | Zhai et al., "Sigmoid Loss for Language Image Pre-Training" (2023) | ViT-based image encoder replacing CLIP for image-text alignment; produces 256 tokens per 896×896 crop |
| Pan & Scan Multi-Crop | Google (PaLI-X, 2023) | Variable-resolution image tiling strategy: crop image into overlapping sub-images at native resolution |
| 5:1 Local/Global Hybrid Attention | Gemma 2 (alternating), Longformer (2020) | Extended to 5:1 ratio for more efficient long-context processing; 1M-token experiments |
| High RoPE Base Frequency (θ=10⁶) | LLaMA 3 (Meta, 2024), Code LLaMA (2023) | Scaling θ from 10,000 to 1,000,000 for faithful 128K context without frequency aliasing |
| Knowledge Distillation (pre-training + alignment) | Hinton et al. (2015), Gemma 2 (2024) | Multi-stage KD: 27B teacher used for both pre-training token distribution and RLHF alignment of smaller models |
| Quantization-Aware Training (QAT) | GPTQ (Frantar et al., 2023), LLM.int8() (Dettmers et al., 2022) | INT4/INT8 quantization awareness built into training for official quantized releases without quality degradation |
| SFT + RLHF + KD Alignment Pipeline | InstructGPT (2022), Gemma 2 (2024) | Three-stage post-training: SFT → RLHF → knowledge distillation from 27B-IT teacher |
| ShieldGemma Safety Classifier | LlamaGuard (Meta, 2024) | Companion safety model trained specifically on Gemma 3's output distribution for content moderation |
| Expanded Multilingual Tokenizer | Gemini 2.0 tokenizer (Google, 2024) | 262,144-token vocabulary from Gemini 2.0, covering 140+ languages with improved subword compression |

</details>

---

## 🟪 Gemma 4 — April 2026

{::nomarkdown}
<div style="display: inline-block; background: #4F46E5; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: April 2, 2026 &nbsp;|&nbsp; 📄 <a href="https://ai.google.dev/gemma/docs/core/model_card_4" style="color: #C7D2FE;">Model Card</a> &nbsp;|&nbsp; 🔗 <a href="https://opensource.googleblog.com/2026/04/gemma-4-expanding-gemmaverse-apache-2.html" style="color: #C7D2FE;">Google Open Source Blog</a>
</div>
{:/nomarkdown}

### Summary

- **Historic license change to Apache 2.0**: for the first time in the Gemma family, all four Gemma 4 model variants are released under the fully permissive **Apache 2.0 license** — replacing the custom Gemma Terms of Use from all previous generations — enabling unrestricted commercial use, fine-tuning, redistribution, and enterprise deployment without additional agreements
- **Four-model family spanning from on-device to workstation**: **E2B** (~2.3B effective / 5.1B total), **E4B** (~4.5B effective / 8B total), **26B A4B** (Mixture-of-Experts, ~4B active / 25.2B total), and **31B** dense — covering the full deployment spectrum from smartphones to data center GPUs
- **Mixture-of-Experts (MoE) architecture in the 26B A4B**: introduces Gemma's first MoE model, with **128 regular experts plus 1 shared expert** per MoE layer, routing **8 experts per token** — enabling near-27B-quality output at roughly 4B-equivalent inference cost
- **Per-Layer Embeddings (PLE) for E2B and E4B**: instead of a single shared embedding table, each transformer layer has its own lightweight embedding lookup (PLE dimensions: vocab × 256 × n_layers), injected after each attention/FFN block — enabling very high intelligence-per-parameter on edge hardware; PLE tables are designed to reside in flash memory rather than VRAM
- **Native audio input for E2B and E4B**: the smallest two models accept audio tokens directly (in addition to text and images), making Gemma 4 the first core Gemma generation with audio-native edge models
- **Upgraded SigLIP 2 vision encoder** across all model sizes: variable-resolution tile processing (up to 896×896 per tile, multi-tile per prompt) — building on Gemma 3's SigLIP backbone with improved alignment training
- **Thinking mode** via a `<|think|>` special token: models can be prompted to produce step-by-step chain-of-thought reasoning traces before the final answer, enabling stronger performance on math and reasoning benchmarks without a separate reasoning model
- **Agentic capabilities**: native support for function calling, tool use, planning, and system prompts (`<|system|>` role), enabling Gemma 4 models as drop-in agents in orchestration frameworks
- **Context window extends to 256K tokens** for 26B A4B and 31B models (128K for E2B/E4B) — up from 128K in Gemma 3's largest models — enabled by Proportional RoPE (p-RoPE)
- **Hybrid local/global attention continues the Gemma 3 5:1 pattern** with refinements: local window shrinks to **512 tokens** for E2B (4 local + 1 global per block) and **1,024 tokens** for 26B/31B (5:1); global layers use the **K=V trick** (Keys = Values, halving KV cache at those layers) and **Shared KV Cache** (last N layers reuse K/V from previous same-type layers)
- **Proportional RoPE (p-RoPE)**: for long-context (256K) global attention layers, only a fraction p=0.25 of the RoPE coordinate pairs receive positional encoding — limiting positional noise in long sequences while preserving semantic tracking
- **GQA pattern refined**: local layers use 2 Q heads per 1 KV head; global layers can use up to 8 Q heads per 1 KV head — further reducing KV cache memory
- **MMLU Pro 85.2% (31B)** — state-of-the-art among Apache 2.0 open models at launch, outperforming Llama 4 Scout and Qwen 3 32B on several reasoning benchmarks; MATH 500 92.1% (31B); HumanEval 91.4% (31B)

### Architecture Diagram — Gemma 4

{::nomarkdown}
<div style="max-width: 750px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #4F46E5; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Gemma 4 — Multi-Modal Architecture with MoE, PLE, and Long Context
  </div>
  <div style="background: #F5F3FF; border: 2px solid #A5B4FC; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Inputs row -->
    <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 12px;">
      <div style="flex: 1; min-width: 120px; border: 2px solid #4F46E5; border-radius: 8px; padding: 10px; background: white; text-align: center; font-size: 11px; color: #4F46E5;">
        <strong>Text Tokens</strong><br/>All variants<br/>262,144 vocab
      </div>
      <div style="flex: 1; min-width: 120px; border: 2px solid #4F46E5; border-radius: 8px; padding: 10px; background: white; text-align: center; font-size: 11px; color: #4F46E5;">
        <strong>SigLIP 2 Vision</strong><br/>All variants<br/>896×896 multi-tile
      </div>
      <div style="flex: 1; min-width: 120px; border: 2px solid #4F46E5; border-radius: 8px; padding: 10px; background: #EEF2FF; text-align: center; font-size: 11px; color: #4F46E5;">
        <strong>Audio</strong><br/>E2B / E4B only<br/>native audio tokens
      </div>
    </div>

    <!-- PLE block (E models) -->
    <div style="border: 2px solid #4F46E5; border-radius: 8px; padding: 12px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #4F46E5; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NEW: PLE (E2B / E4B)</div>
      <div style="font-weight: 700; color: #4F46E5; margin-bottom: 8px; font-size: 13px;">Per-Layer Embeddings (PLE)</div>
      <div style="font-size: 12px; color: #374151; line-height: 1.5;">Each transformer layer fetches a layer-specific token embedding from a flash-resident PLE table (vocab × 256 × n_layers) and injects it after attention/FFN via a lightweight residual block — boosting intelligence-per-parameter on edge hardware without increasing active VRAM requirements.</div>
    </div>

    <!-- Hybrid attention with p-RoPE -->
    <div style="border: 2px solid #4F46E5; border-radius: 8px; padding: 12px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #4F46E5; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ 5:1 + K=V + Shared KV</div>
      <div style="font-weight: 700; color: #4F46E5; margin-bottom: 8px; font-size: 13px;">Hybrid Attention (5 Local + 1 Global) with Memory Optimizations</div>
      <div style="display: flex; gap: 4px; flex-wrap: nowrap; margin-bottom: 8px; overflow-x: auto;">
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #DBEAFE; border: 1px solid #93C5FD; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px; color: #1E40AF;">
          <strong>SWA</strong><br/>Local<br/>win=1K
        </div>
        <div style="flex: 1; min-width: 55px; background: #4F46E5; color: white; padding: 6px; border-radius: 6px; text-align: center; font-size: 11px;">
          <strong>Global</strong><br/>K=V trick<br/>Shared KV
        </div>
        <div style="display: flex; align-items: center; font-size: 14px; color: #6B7280; padding: 0 4px;">...</div>
      </div>
      <div style="display: flex; gap: 8px; flex-wrap: wrap; font-size: 12px;">
        <div style="background: #DBEAFE; color: #1E40AF; padding: 5px 10px; border-radius: 4px;">🔵 5× Local SWA: GQA 2:1 Q/KV, fast</div>
        <div style="background: #4F46E5; color: white; padding: 5px 10px; border-radius: 4px;">🟪 1× Global: K=V, Shared KV, p-RoPE (p=0.25 for 256K)</div>
      </div>
    </div>

    <!-- MoE block -->
    <div style="border: 2px solid #4F46E5; border-radius: 8px; padding: 12px; background: white; margin-bottom: 12px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #4F46E5; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NEW: MoE (26B A4B)</div>
      <div style="font-weight: 700; color: #4F46E5; margin-bottom: 8px; font-size: 13px;">Mixture-of-Experts FFN (26B A4B only)</div>
      <div style="display: flex; gap: 8px; flex-wrap: wrap; font-size: 12px; align-items: center;">
        <div style="background: #EEF2FF; border: 1px solid #A5B4FC; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 100px;">
          <strong>128 experts</strong><br/>+ 1 shared
        </div>
        <div style="font-size: 18px; color: #4F46E5;">→</div>
        <div style="background: #4F46E5; color: white; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 100px;">
          <strong>Top-8 routing</strong><br/>per token
        </div>
        <div style="font-size: 18px; color: #4F46E5;">→</div>
        <div style="background: #EEF2FF; border: 1px solid #A5B4FC; padding: 8px 12px; border-radius: 6px; text-align: center; flex: 1; min-width: 100px;">
          ~4B active<br/>25.2B total
        </div>
      </div>
    </div>

    <!-- Context / sizing stats -->
    <div style="background: white; border: 1px solid #A5B4FC; border-radius: 8px; padding: 12px;">
      <div style="font-weight: 600; color: #4F46E5; margin-bottom: 6px; font-size: 13px;">📐 Model Size Configurations</div>
      <table style="width: 100%; font-size: 11px; border-collapse: collapse;">
        <tr style="background: #EEF2FF;">
          <th style="padding: 5px 6px; text-align: left; border-bottom: 1px solid #A5B4FC;">Model</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #A5B4FC;">Layers</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #A5B4FC;">Effective Params</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #A5B4FC;">Context</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #A5B4FC;">Modalities</th>
          <th style="padding: 5px 6px; text-align: center; border-bottom: 1px solid #A5B4FC;">PLE/MoE</th>
        </tr>
        <tr><td style="padding: 4px 6px;">Gemma 4 E2B</td><td style="padding: 4px 6px; text-align: center;">35</td><td style="padding: 4px 6px; text-align: center;">~2.3B (5.1B total)</td><td style="padding: 4px 6px; text-align: center;">128K</td><td style="padding: 4px 6px; text-align: center;">Text + Image + Audio</td><td style="padding: 4px 6px; text-align: center;">PLE</td></tr>
        <tr style="background: #F5F3FF;"><td style="padding: 4px 6px;">Gemma 4 E4B</td><td style="padding: 4px 6px; text-align: center;">42</td><td style="padding: 4px 6px; text-align: center;">~4.5B (8B total)</td><td style="padding: 4px 6px; text-align: center;">128K</td><td style="padding: 4px 6px; text-align: center;">Text + Image + Audio</td><td style="padding: 4px 6px; text-align: center;">PLE</td></tr>
        <tr><td style="padding: 4px 6px;">Gemma 4 26B A4B</td><td style="padding: 4px 6px; text-align: center;">—</td><td style="padding: 4px 6px; text-align: center;">~4B active (25.2B total)</td><td style="padding: 4px 6px; text-align: center;">256K</td><td style="padding: 4px 6px; text-align: center;">Text + Image</td><td style="padding: 4px 6px; text-align: center;">MoE (128+1 exp, top-8)</td></tr>
        <tr style="background: #F5F3FF;"><td style="padding: 4px 6px;">Gemma 4 31B</td><td style="padding: 4px 6px; text-align: center;">—</td><td style="padding: 4px 6px; text-align: center;">30.7B dense</td><td style="padding: 4px 6px; text-align: center;">256K</td><td style="padding: 4px 6px; text-align: center;">Text + Image</td><td style="padding: 4px 6px; text-align: center;">Dense</td></tr>
      </table>
    </div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

- **The Apache 2.0 license change was the biggest headline**: the developer community immediately highlighted this as a watershed moment for Google's open-source AI strategy — previous Gemma generations used a custom Terms of Use that prohibited certain commercial uses, and many organizations had avoided Gemma for that reason
- **MoE in the 26B A4B was welcomed as a practical breakthrough**: running a 25B-parameter model at 4B inference cost on consumer hardware made Gemma 4 immediately accessible to individual researchers with a single 16–18 GB GPU — previously a workstation-class requirement
- **Per-Layer Embeddings (PLE) generated significant interest**: the idea of offloading per-layer token embeddings to flash memory as a way to dramatically boost intelligence-per-active-parameter was novel and sparked architectural discussions across the ML community
- **Thinking mode adoption was rapid**: the `<|think|>` token mechanism was straightforward to use via standard chat templates, and benchmarks quickly showed 5–10% gains on AIME and LiveCodeBench when thinking was enabled — making it a go-to feature for technical users
- **Audio support in edge models (E2B/E4B)** was noted as a significant practical advantage for mobile and IoT voice applications, though the community awaited more detailed audio benchmark comparisons
- **The K=V trick and Shared KV Cache** were praised by inference engineers as elegant solutions to KV cache memory pressure in long-context scenarios, reducing global attention memory overhead by up to 50%
- **AIME 2026 ~89.2%** was the standout math benchmark result, placing Gemma 4 31B above many closed models on competition-level mathematics — validating the thinking mode + improved pre-training
- **Comparison to Llama 4**: the community broadly assessed Gemma 4 as competing favorably with Llama 4 Scout (active parameter count) and Llama 4 Maverick for reasoning-heavy tasks, while the Apache 2.0 license gave Gemma 4 an advantage in enterprise adoption settings

### Model Variants

| Model | Effective Params | Total Params | Layers | Context | Modalities | License | Architecture |
|:------|:---------------:|:----------:|:------:|:-------:|:----------:|:-------:|:------------|
| Gemma4-E2B | ~2.3B | 5.1B | 35 | 128K | Text + Image + Audio | Apache 2.0 | Dense + PLE |
| Gemma4-E2B-IT | ~2.3B | 5.1B | 35 | 128K | Text + Image + Audio | Apache 2.0 | Dense + PLE + SFT/RLHF |
| Gemma4-E4B | ~4.5B | 8B | 42 | 128K | Text + Image + Audio | Apache 2.0 | Dense + PLE |
| Gemma4-E4B-IT | ~4.5B | 8B | 42 | 128K | Text + Image + Audio | Apache 2.0 | Dense + PLE + SFT/RLHF |
| Gemma4-26B-A4B | ~4B active | 25.2B | — | 256K | Text + Image | Apache 2.0 | MoE (128+1 exp, top-8) |
| Gemma4-26B-A4B-IT | ~4B active | 25.2B | — | 256K | Text + Image | Apache 2.0 | MoE + SFT/RLHF |
| Gemma4-31B | 30.7B | 30.7B | — | 256K | Text + Image | Apache 2.0 | Dense |
| Gemma4-31B-IT | 30.7B | 30.7B | — | 256K | Text + Image | Apache 2.0 | Dense + SFT/RLHF |

> **Companion model:** ShieldGemma 4 safety classifier released alongside for responsible deployment.

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Gemma 4 Used It |
|:----------|:-------|:--------------------|
| Mixture-of-Experts (MoE) | Shazeer et al., "Outrageously Large Neural Networks" (2017); Mixtral (2023) | 26B A4B: 128+1 experts per MoE layer, top-8 routing — full MoE debut in the Gemma family |
| Per-Layer Embeddings (PLE) | Gemma 4 (Google DeepMind, 2026) | Layer-specific embedding tables fetched from flash memory, injected residually after each layer for edge intelligence-per-parameter boost |
| Proportional RoPE (p-RoPE) | Gemma 4 (Google DeepMind, 2026) | Only 25% of RoPE coordinate pairs encoded in global attention layers for 256K contexts, reducing positional noise |
| K=V Trick (Keys = Values) | Gemma 4 (Google DeepMind, 2026) | Global attention layers set K=V, collapsing KV cache to a single cache and halving memory requirements |
| Shared KV Cache | Gemma 4 (Google DeepMind, 2026) | Last N layers of the same attention type share K/V, reducing redundant memory across layers |
| Chain-of-Thought / Thinking Mode | Wei et al., "Chain-of-Thought Prompting" (NeurIPS 2022); DeepSeek-R1 (2025) | `<|think|>` token activates step-by-step reasoning traces before final output |
| SigLIP 2 (upgraded vision encoder) | Zhai et al., SigLIP (2023) + Google improvements | Enhanced alignment training over Gemma 3's SigLIP; variable-resolution multi-tile input |
| Function Calling / Tool Use | Toolformer (2023), GPT-4 function calling (2023) | Native tool use and function calling via chat templates for agentic deployments |
| Apache 2.0 Open Licensing | OSI (Open Source Initiative) | First Gemma generation fully open; previous versions used custom Gemma Terms of Use |

</details>

---

## 📚 References

### Technical Papers

| Version | Title | Link | Date |
|:--------|:------|:-----|:-----|
| Gemma 1 | Gemma: Open Models Based on Gemini Research and Technology | [arXiv:2403.08295](https://arxiv.org/abs/2403.08295) | Mar 2024 |
| Gemma 2 | Gemma 2: Improving Open Language Models at a Practical Size | [arXiv:2408.00118](https://arxiv.org/abs/2408.00118) | Aug 2024 |
| Gemma 3 | Gemma 3 Technical Report | [arXiv:2503.19786](https://arxiv.org/abs/2503.19786) | Mar 2025 |
| Gemma 4 | Gemma 4 Model Card (Google AI for Developers) | [ai.google.dev/gemma/docs/core/model_card_4](https://ai.google.dev/gemma/docs/core/model_card_4) | Apr 2026 |

### Official Blog Posts

| Title | Link |
|:------|:-----|
| Gemma: Introducing New State-of-the-Art Open Models | [blog.google/technology/developers/gemma-open-models/](https://blog.google/technology/developers/gemma-open-models/) |
| Gemma 2: Advancing Frontier AI Responsibly | [blog.google/technology/developers/google-gemma-2/](https://blog.google/technology/developers/google-gemma-2/) |
| Gemma 3 — The Developer Guide | [developers.googleblog.com/en/introducing-gemma3/](https://developers.googleblog.com/en/introducing-gemma3/) |
| Gemma 4: Expanding the Gemmaverse with Apache 2.0 | [opensource.googleblog.com/2026/04/gemma-4-expanding-gemmaverse-apache-2.html](https://opensource.googleblog.com/2026/04/gemma-4-expanding-gemmaverse-apache-2.html) |
| Google DeepMind Gemma Page | [deepmind.google/models/gemma/](https://deepmind.google/models/gemma/) |

### GitHub & Model Repositories

| Resource | Link |
|:---------|:-----|
| Gemma GitHub (google-deepmind) | [github.com/google-deepmind/gemma](https://github.com/google-deepmind/gemma) |
| Gemma on Hugging Face | [huggingface.co/google/gemma-7b](https://huggingface.co/google/gemma-7b) |
| Gemma 2 on Hugging Face | [huggingface.co/google/gemma-2-27b](https://huggingface.co/google/gemma-2-27b) |
| Gemma 3 on Hugging Face | [huggingface.co/google/gemma-3-27b-it](https://huggingface.co/google/gemma-3-27b-it) |
| Gemma 4 on Hugging Face | [huggingface.co/google/gemma-4-31b-it](https://huggingface.co/google/gemma-4-31b-it) |
| Gemma on Kaggle | [kaggle.com/models/google/gemma](https://www.kaggle.com/models/google/gemma) |
| Keras NLP Gemma | [keras.io/api/keras_nlp/models/gemma/](https://keras.io/api/keras_nlp/models/gemma/) |

### Cited Techniques

| Technique | Paper | Link |
|:----------|:------|:-----|
| GeGLU Activation | Shazeer, "GLU Variants Improve Transformer" (2020) | [arXiv:2002.05202](https://arxiv.org/abs/2002.05202) |
| RoPE | Su et al., "RoFormer: Enhanced Transformer with Rotary Position Embedding" (2021) | [arXiv:2104.09864](https://arxiv.org/abs/2104.09864) |
| RMSNorm | Zhang & Sennrich, "Root Mean Square Layer Normalization" (2019) | [arXiv:1910.07467](https://arxiv.org/abs/1910.07467) |
| GQA | Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models" (EMNLP 2023) | [arXiv:2305.13245](https://arxiv.org/abs/2305.13245) |
| Sliding Window Attention | Beltagy et al., "Longformer: The Long-Document Transformer" (2020) | [arXiv:2004.05150](https://arxiv.org/abs/2004.05150) |
| Knowledge Distillation | Hinton et al., "Distilling the Knowledge in a Neural Network" (2015) | [arXiv:1503.02531](https://arxiv.org/abs/1503.02531) |
| SigLIP | Zhai et al., "Sigmoid Loss for Language Image Pre-Training" (2023) | [arXiv:2303.15343](https://arxiv.org/abs/2303.15343) |
| SentencePiece BPE | Kudo & Richardson, "SentencePiece: A simple and language independent subword tokenizer" (2018) | [arXiv:1808.06226](https://arxiv.org/abs/1808.06226) |
| Tied Embeddings | Press & Wolf, "Using the Output Embedding to Improve Language Models" (EACL 2017) | [arXiv:1608.05859](https://arxiv.org/abs/1608.05859) |
| QAT (INT4/INT8) | Frantar et al., "GPTQ: Accurate Post-Training Quantization" (ICLR 2023) | [arXiv:2210.17323](https://arxiv.org/abs/2210.17323) |
| InstructGPT / RLHF | Ouyang et al., "Training language models to follow instructions with human feedback" (NeurIPS 2022) | [arXiv:2203.02155](https://arxiv.org/abs/2203.02155) |
| Gemini Architecture | Gemini Team, "Gemini: A Family of Highly Capable Multimodal Models" (2023) | [arXiv:2312.11805](https://arxiv.org/abs/2312.11805) |
| LLaMA 3 (RoPE scaling) | Meta AI, "The Llama 3 Herd of Models" (2024) | [arXiv:2407.21783](https://arxiv.org/abs/2407.21783) |
| Mixture-of-Experts | Shazeer et al., "Outrageously Large Neural Networks: The Sparsely-Gated MoE Layer" (2017) | [arXiv:1701.06538](https://arxiv.org/abs/1701.06538) |
| Chain-of-Thought Prompting | Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" (NeurIPS 2022) | [arXiv:2201.11903](https://arxiv.org/abs/2201.11903) |

---

<p align="center">
  <sub>Built with data from official Gemma technical reports, Google DeepMind blog posts, and the Gemma 4 model card (April 2026). All benchmark numbers sourced directly from the referenced publications.</sub>
</p>

<p align="center">
  <a href="README.md">← Back to Index</a>
</p>
