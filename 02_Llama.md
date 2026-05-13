---
title: "Llama Model Family"
---

<h1 align="center">🦙 Llama Model Family — A Comprehensive Technical Reference</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Versions_Covered-Llama_1_→_4-F97316?style=flat-square" alt="Versions Covered"/>
  <img src="https://img.shields.io/badge/Team-Meta_AI_%2F_FAIR-EA580C?style=flat-square" alt="Meta AI"/>
  <img src="https://img.shields.io/badge/Last_Updated-April_2025-C2410C?style=flat-square" alt="Last Updated"/>
</p>

<p align="center"><em>From a fully open-weight decoder-only Transformer to a native multimodal Mixture-of-Experts colossus — the complete evolutionary arc of Meta's Llama series.</em></p>

---

## 📑 Table of Contents

- [Executive Summary](#-executive-summary)
- [Version Release Timeline](#-version-release-timeline)
- [Cross-Version Benchmark Comparison](#-cross-version-benchmark-comparison)
- [Master Architecture Diagram](#️-master-architecture-diagram)
- [Llama 1 — February 2023](#-llama-1--february-2023)
- [Llama 2 — July 2023](#-llama-2--july-2023)
- [Llama 3 — April 2024](#-llama-3--april-2024)
- [Llama 3.1 — July 2024](#-llama-31--july-2024)
- [Llama 3.2 — September 2024](#-llama-32--september-2024)
- [Llama 3.3 — December 2024](#-llama-33--december-2024)
- [Llama 4 — April 2025](#-llama-4--april-2025)
- [References](#-references)

---

## 📋 Executive Summary

The **Llama** (Large Language Model Meta AI) series is Meta's flagship family of open-weight language models, spanning from a pure research release in February 2023 to a production-scale multimodal Mixture-of-Experts system in April 2025.

- **Llama 1** (Feb 2023): Decoder-only Transformer with RoPE, RMSNorm, and SwiGLU; 4 sizes (7B–65B); sparked the open-source LLM revolution.
- **Llama 2** (Jul 2023): Expanded context to 4K, added GQA for larger models, introduced RLHF-tuned Chat variants with Ghost Attention; commercial license.
- **Llama 3** (Apr 2024): GQA universally applied, 128K vocabulary (tiktoken), 15T training tokens; 8B and 70B flagship sizes.
- **Llama 3.1** (Jul 2024): 128K context via RoPE scaling (θ=500,000), new 405B flagship, multilingual capability, tool use, multi-stage post-training pipeline.
- **Llama 3.2** (Sep 2024): Introduced vision models (11B, 90B with cross-attention adapters) and small edge models (1B, 3B via pruning/distillation).
- **Llama 3.3** (Dec 2024): Single drop-in 70B improvement via enhanced post-training — better math, reasoning, and coding at lower compute cost.
- **Llama 4** (Apr 2025): Native early-fusion multimodal MoE (Scout 17B active / Maverick 17B active / Behemoth 288B active); iRoPE positional encoding; up to 10M context.

---

## 📅 Version Release Timeline

<div style="overflow-x: auto;">

| Version | Release | Key Milestone | Parameters | Context |
|:-------:|:-------:|:-------------|:----------:|:-------:|
| <img src="https://img.shields.io/badge/Llama_1-Feb_2023-C2410C" alt="Llama 1"/> | Feb 2023 | First fully open-weight competitive LLM | 7B – 65B | 2K |
| <img src="https://img.shields.io/badge/Llama_2-Jul_2023-EA580C" alt="Llama 2"/> | Jul 2023 | Commercial license, RLHF Chat, GQA (large models) | 7B – 70B | 4K |
| <img src="https://img.shields.io/badge/Llama_3-Apr_2024-F97316" alt="Llama 3"/> | Apr 2024 | 128K vocab, GQA universal, 15T tokens | 8B, 70B | 8K |
| <img src="https://img.shields.io/badge/Llama_3.1-Jul_2024-D97706" alt="Llama 3.1"/> | Jul 2024 | 128K context, 405B flagship, tool use | 8B – 405B | 128K |
| <img src="https://img.shields.io/badge/Llama_3.2-Sep_2024-B45309" alt="Llama 3.2"/> | Sep 2024 | Vision models + edge models (1B/3B) | 1B – 90B | 128K |
| <img src="https://img.shields.io/badge/Llama_3.3-Dec_2024-92400E" alt="Llama 3.3"/> | Dec 2024 | Improved post-training; 70B only | 70B | 128K |
| <img src="https://img.shields.io/badge/Llama_4-Apr_2025-7C2D12" alt="Llama 4"/> | Apr 2025 | Native multimodal MoE, iRoPE, 10M ctx | 109B–~2T total | 10M |

</div>

---

## 📊 Cross-Version Benchmark Comparison

| Benchmark | Llama 1 (65B) | Llama 2 (70B) | Llama 3 (70B) | Llama 3.1 (70B) | Llama 3.3 (70B) |
|:----------|:-------------:|:-------------:|:-------------:|:---------------:|:---------------:|
| **MMLU** | 63.4 | 68.9 | 82.0 | 86.0 | 86.0 |
| **HumanEval** | 23.7 | 29.9 | 81.7 | 80.5 | 88.4 |
| **MATH** | 6.7 | 16.0 | 50.4 | 65.1 | ~77 |
| **GSM8K** | 50.9 | 56.8 | 93.0 | 95.1 | ~95 |
| **Context Window** | 2K | 4K | 8K | 128K | 128K |
| **Training Tokens** | 1.4T | 2T | 15T | 15T | 15T |

---

## 🏗️ Master Architecture Diagram

{::nomarkdown}
<div style="max-width: 900px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 14px;">
  <div style="background: linear-gradient(135deg, #7C2D12 0%, #C2410C 50%, #F97316 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center;">
    <strong style="font-size: 16px;">Llama Series — Architectural Evolution Overview</strong>
    <div style="font-size: 12px; opacity: 0.85; margin-top: 4px;">Feb 2023 (Llama 1) → Apr 2025 (Llama 4)</div>
  </div>
  <div style="background: #FFF7ED; border: 2px solid #FED7AA; border-top: none; border-radius: 0 0 12px 12px; padding: 20px 24px;">

    <!-- Row 1: Input -->
    <div style="display: flex; justify-content: center; margin-bottom: 8px;">
      <div style="background: #C2410C; color: white; padding: 8px 28px; border-radius: 8px; font-weight: bold;">Input Tokens</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 18px; margin-bottom: 8px;">↓</div>

    <!-- Row 2: Embedding -->
    <div style="display: flex; justify-content: center; margin-bottom: 8px;">
      <div style="background: #EA580C; color: white; padding: 8px 28px; border-radius: 8px; font-weight: bold;">Token Embedding + Positional Encoding</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 13px; opacity: 0.7; margin-bottom: 2px;">Llama 1–3.3: RoPE (θ=10K → 500K)&nbsp;&nbsp;|&nbsp;&nbsp;Llama 4: iRoPE (interleaved)</div>
    <div style="text-align: center; color: #9A3412; font-size: 18px; margin-bottom: 8px;">↓</div>

    <!-- Row 3: N Decoder Layers -->
    <div style="position: relative; background: white; border: 2px solid #FED7AA; border-radius: 10px; padding: 16px 20px; margin-bottom: 8px;">
      <div style="position: absolute; top: -10px; left: 50%; transform: translateX(-50%); background: #F97316; color: white; padding: 2px 14px; border-radius: 10px; font-size: 12px; font-weight: bold; white-space: nowrap;">× N Decoder Layers</div>
      <div style="margin-top: 6px; display: flex; flex-wrap: wrap; gap: 10px; justify-content: center;">

        <!-- Sub-box: Pre-Norm RMSNorm -->
        <div style="flex: 1; min-width: 180px; background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 8px; padding: 10px 14px; text-align: center;">
          <div style="font-weight: bold; color: #C2410C; margin-bottom: 4px;">Pre-Norm (RMSNorm)</div>
          <div style="font-size: 12px; color: #78350F;">LayerNorm replaced by RMSNorm<br/>Applied <em>before</em> sub-layers</div>
        </div>

        <!-- Sub-box: Attention -->
        <div style="flex: 1; min-width: 220px; background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 8px; padding: 10px 14px; text-align: center;">
          <div style="font-weight: bold; color: #C2410C; margin-bottom: 4px;">Self-Attention</div>
          <div style="font-size: 12px; color: #78350F;">
            Llama 1: MHA (32/40/52/64 heads)<br/>
            Llama 2–3.3: GQA (Q/KV split)<br/>
            Llama 4: GQA + iRoPE interleaving
          </div>
        </div>

        <!-- Sub-box: FFN -->
        <div style="flex: 1; min-width: 200px; background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 8px; padding: 10px 14px; text-align: center;">
          <div style="font-weight: bold; color: #C2410C; margin-bottom: 4px;">Feed-Forward Network</div>
          <div style="font-size: 12px; color: #78350F;">
            Llama 1–3.3: Dense SwiGLU (W1, W2, W3)<br/>
            Llama 4: SwiGLU MoE (sparse routing)
          </div>
        </div>

      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 18px; margin-bottom: 8px;">↓</div>

    <!-- Row 4: Final Norm -->
    <div style="display: flex; justify-content: center; margin-bottom: 8px;">
      <div style="background: #D97706; color: white; padding: 8px 28px; border-radius: 8px; font-weight: bold;">Final RMSNorm</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 18px; margin-bottom: 8px;">↓</div>

    <!-- Row 5: LM Head -->
    <div style="display: flex; justify-content: center; margin-bottom: 8px;">
      <div style="background: #B45309; color: white; padding: 8px 28px; border-radius: 8px; font-weight: bold;">Language Model Head (Linear + Softmax)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 18px; margin-bottom: 8px;">↓</div>

    <!-- Row 6: Output -->
    <div style="display: flex; justify-content: center; margin-bottom: 12px;">
      <div style="background: #92400E; color: white; padding: 8px 28px; border-radius: 8px; font-weight: bold;">Next-Token Probabilities</div>
    </div>

    <!-- Legend -->
    <div style="border-top: 1px solid #FED7AA; padding-top: 12px; margin-top: 4px; display: flex; flex-wrap: wrap; gap: 8px; justify-content: center; font-size: 12px; color: #78350F;">
      <span style="background: #FEF3C7; border: 1px solid #FDE68A; padding: 3px 10px; border-radius: 6px;">🦙 Shared Core: RoPE + RMSNorm + SwiGLU (all versions)</span>
      <span style="background: #FEF3C7; border: 1px solid #FDE68A; padding: 3px 10px; border-radius: 6px;">🔀 GQA from Llama 2 (large) / Llama 3 (all)</span>
      <span style="background: #FEF3C7; border: 1px solid #FDE68A; padding: 3px 10px; border-radius: 6px;">⚡ MoE + iRoPE: Llama 4 only</span>
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 🟤 Llama 1 — February 2023

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFF7ED; border:1.5px solid #C2410C; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#C2410C;">📅 Feb 2023</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://arxiv.org/abs/2302.13971" style="color:#C2410C; text-decoration:none; font-weight:bold;">arXiv:2302.13971</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI / FAIR</span>
</div>

### Summary

- **Architecture**: Pure decoder-only Transformer; no encoder cross-attention.
- **Positional Encoding**: Rotary Position Embeddings (RoPE) with θ=10,000 applied to every attention layer.
- **Normalization**: Pre-norm RMSNorm (replaces LayerNorm entirely); eliminates mean centering for efficiency.
- **Activation**: SwiGLU feed-forward with three weight matrices (W1, W2, W3); intermediate dim ≈ 2/3 × 4d.
- **Attention**: Multi-Head Attention (MHA) — no grouped-query; full Q/K/V projections per head.
- **Vocabulary**: 32,000 tokens via SentencePiece BPE (byte-level fallback for unknown characters).
- **Context Window**: 2,048 tokens (causal attention mask; no sliding window).
- **Training Data**: Publicly available corpora — CommonCrawl, C4, GitHub, Wikipedia, Books, ArXiv, StackExchange; totaling ~1T (7B/13B) and 1.4T (33B/65B) tokens.
- **Optimizer**: AdamW, β₁=0.9, β₂=0.95, ε=10⁻⁵; cosine LR schedule; weight decay 0.1; gradient clipping 1.0.
- **Training Infrastructure**: 2,048 A100 80GB GPUs (65B model); efficient memory via FlashAttention v1 and activation checkpointing.
- **Open Release**: Weights released under a non-commercial research license; spawned hundreds of community fine-tunes (Alpaca, Vicuna, WizardLM, etc.).

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 680px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #C2410C; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 1 Architecture (Feb 2023)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">7B / 13B / 33B / 65B — Decoder-only Transformer with RoPE + RMSNorm + SwiGLU</div>
  </div>
  <div style="background: white; border: 2px solid #FED7AA; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #C2410C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Input Tokens (vocab: 32K)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #EA580C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Token Embedding + RoPE (θ=10,000)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FED7AA; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFFBEB;">
      <div style="position: absolute; top: -10px; left: 16px; background: #C2410C; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Repeat × N Layers (32/40/60/80)</div>
      <div style="margin-top: 8px;">
        <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 6px;">
          <div style="flex:1; min-width:140px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#C2410C; font-size:12px;">RMSNorm (pre)</div>
            <div style="font-size:11px; color:#78350F;">Pre-norm, no bias</div>
          </div>
          <div style="flex:2; min-width:200px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#C2410C; font-size:12px;">Multi-Head Attention (MHA)</div>
            <div style="font-size:11px; color:#78350F;">Causal · All Q/K/V heads equal · RoPE on Q,K</div>
          </div>
        </div>
        <div style="text-align:center; color:#9A3412; font-size:14px; margin-bottom:5px;">↓ residual add ↓</div>
        <div style="display: flex; gap: 8px; flex-wrap: wrap;">
          <div style="flex:1; min-width:140px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#C2410C; font-size:12px;">RMSNorm (pre)</div>
            <div style="font-size:11px; color:#78350F;">Pre-norm, no bias</div>
          </div>
          <div style="flex:2; min-width:200px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#C2410C; font-size:12px;">SwiGLU FFN</div>
            <div style="font-size:11px; color:#78350F;">W1(SiLU(x))⊙W3(x) → W2 · intermediate ≈ ⅔×4d</div>
          </div>
        </div>
      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #D97706; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Final RMSNorm</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center;">
      <div style="display: inline-block; background: #92400E; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">LM Head → Logits (32K vocab)</div>
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 1 fundamentally democratised large language model research. Within weeks of its release (initially leaked, then officially distributed), the community produced Alpaca (Stanford, Stanford CRFM fine-tune using GPT-4-generated instruction data), Vicuna (LMSYS, ShareGPT conversations), WizardLM, and dozens of quantised variants via llama.cpp and GGUF format. The model demonstrated that publicly available data alone could match or surpass contemporaneous commercial APIs at a fraction of the compute cost, validating scaling law predictions and opening a new era of community-driven alignment research.

### Model Variants

| Model | Parameters | Layers | Heads (Q) | Hidden Dim | Context | Training Tokens |
|:------|:----------:|:------:|:---------:|:----------:|:-------:|:---------------:|
| Llama 1 7B | 6.7B | 32 | 32 | 4,096 | 2,048 | 1T |
| Llama 1 13B | 13.0B | 40 | 40 | 5,120 | 2,048 | 1T |
| Llama 1 33B | 32.5B | 60 | 52 | 6,656 | 2,048 | 1.4T |
| Llama 1 65B | 65.2B | 80 | 64 | 8,192 | 2,048 | 1.4T |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 1 Used It |
|:----------|:-------|:--------------------|
| Rotary Position Embeddings (RoPE) | Su et al., 2021 | Applied to Q and K projections at every layer; θ=10,000 |
| RMSNorm | Zhang & Sennrich, 2019 | Replaces LayerNorm pre-sub-layer; removes mean centering |
| SwiGLU Activation | Shazeer, 2020 | FFN uses gated linear unit with SiLU; three weight matrices |
| Pre-norm Transformer | Xiong et al., 2020 | Norm applied before attention and FFN (vs. post-norm) |
| FlashAttention | Dao et al., 2022 | Tiling-based attention for memory-efficient training on A100s |
| BPE Tokeniser | Sennrich et al., 2016 | 32K token vocab via SentencePiece byte-level BPE |
| Causal LM Objective | GPT series | Standard next-token prediction, no masked LM |

</details>

---

## 🟠 Llama 2 — July 2023

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFF7ED; border:1.5px solid #EA580C; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#EA580C;">📅 Jul 2023</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://arxiv.org/abs/2307.09288" style="color:#EA580C; text-decoration:none; font-weight:bold;">arXiv:2307.09288</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Context Expansion**: Window doubled to 4,096 tokens; trained with a longer document mix.
- **Grouped-Query Attention (GQA)**: Introduced for 34B and 70B models only; 7B and 13B retain full MHA.
- **GQA Configuration (70B)**: 40 query heads, 8 key-value heads (5:1 ratio); dramatically reduces KV-cache memory.
- **GQA Configuration (34B)**: 64 query heads, 8 key-value heads (8:1 ratio).
- **Training Data**: ~2 trillion tokens; updated pretraining mix with additional helpfulness-oriented data.
- **Chat Variants**: First official instruction-tuned release; SFT on >27,500 human-annotated examples.
- **RLHF Pipeline**: Two separate reward models trained — one for helpfulness, one for safety; Proximal Policy Optimisation (PPO) applied iteratively.
- **Ghost Attention (GAtt)**: Synthetic technique to preserve system-prompt adherence across multi-turn dialogue; system instruction replicated at each user turn during training.
- **Safety Measures**: Red-teaming, safety reward model, context distillation, human evaluations; "responsible use guide" released alongside weights.
- **Commercial License**: Llama 2 Community License allows commercial use for organisations with fewer than 700M monthly active users (separate license for larger entities).
- **Code Model**: Code Llama released separately (Aug 2023) on top of Llama 2 base; 7B/13B/34B with 100K context infill.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #EA580C; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 2 Architecture (Jul 2023)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">7B / 13B (MHA) · 34B / 70B (GQA) — Context: 4096 · RLHF Chat Variants</div>
  </div>
  <div style="background: white; border: 2px solid #FED7AA; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #EA580C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Input Tokens (32K vocab, SentencePiece)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #C2410C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Token Embedding + RoPE (θ=10,000) — 4096 ctx</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FED7AA; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFFBEB;">
      <div style="position: absolute; top: -10px; left: 16px; background: #EA580C; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Repeat × N Layers</div>
      <div style="margin-top: 8px;">
        <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 6px;">
          <div style="flex:1; min-width:130px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#EA580C; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; position:relative; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="position:absolute; top:-9px; right:10px; background:#EA580C; color:white; padding:1px 8px; border-radius:6px; font-size:10px; font-weight:bold;">NEW for 34B/70B</div>
            <div style="font-weight:bold; color:#EA580C; font-size:12px;">Grouped-Query Attention (GQA)</div>
            <div style="font-size:11px; color:#78350F;">70B: 40 Q heads / 8 KV heads<br/>7B/13B: MHA (unchanged)</div>
          </div>
        </div>
        <div style="text-align:center; color:#9A3412; font-size:14px; margin-bottom:5px;">↓ residual ↓</div>
        <div style="display: flex; gap: 8px; flex-wrap: wrap;">
          <div style="flex:1; min-width:130px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#EA580C; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#EA580C; font-size:12px;">SwiGLU FFN (Dense)</div>
            <div style="font-size:11px; color:#78350F;">Identical to Llama 1</div>
          </div>
        </div>
      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #D97706; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Final RMSNorm → LM Head</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap;">
      <div style="background: #92400E; color: white; padding: 6px 18px; border-radius: 7px; font-weight: bold; text-align: center;">Base Model</div>
      <div style="position:relative; background: #7C2D12; color: white; padding: 6px 18px; border-radius: 7px; font-weight: bold; text-align: center;">
        <div style="position:absolute; top:-9px; right:8px; background:#EA580C; color:white; padding:1px 7px; border-radius:5px; font-size:10px;">NEW</div>
        Chat (SFT+RLHF+GAtt)
      </div>
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 2's commercial license transformed the ecosystem: enterprises could now legally deploy and fine-tune these weights in production. The 70B Chat model quickly became the reference open-weight instruction-tuned LLM, benchmarked extensively against GPT-3.5. The Ghost Attention mechanism addressed a key weakness in multi-turn instruction following that plagued early RLHF systems. Code Llama (built on Llama 2) became one of the most widely adopted open code generation models, directly influencing Copilot-alternative tooling.

### Model Variants

| Model | Parameters | Layers | Heads (Q/KV) | Hidden Dim | Context | Notes |
|:------|:----------:|:------:|:------------:|:----------:|:-------:|:------|
| Llama 2 7B | 6.7B | 32 | 32/32 (MHA) | 4,096 | 4,096 | Base + Chat |
| Llama 2 13B | 13.0B | 40 | 40/40 (MHA) | 5,120 | 4,096 | Base + Chat |
| Llama 2 34B | 34B | 48 | 64/8 (GQA) | 8,192 | 4,096 | Base only (no Chat) |
| Llama 2 70B | 68.9B | 80 | 64/8 (GQA) | 8,192 | 4,096 | Base + Chat |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 2 Used It |
|:----------|:-------|:--------------------|
| Grouped-Query Attention (GQA) | Ainslie et al., 2023 | Applied to 34B and 70B; reduces KV-cache ~5–8× |
| RLHF with PPO | Stiennon et al., 2020; InstructGPT | Two reward models (helpfulness + safety) trained on 1.4M comparisons |
| Ghost Attention (GAtt) | Meta internal | Synthetic multi-turn training to preserve system-prompt over long conversations |
| Rejection Sampling Fine-tuning | Meta internal | Used between SFT and PPO; sample K responses, keep highest reward |
| Safety Red-teaming | Anthropic, OpenAI tradition | Dedicated red team; safety-specific reward model; context distillation |
| Context Distillation | Askell et al., 2021 | Distilling safety behavior from system-prompted model to base model |

</details>

---

## 🟡 Llama 3 — April 2024

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFF7ED; border:1.5px solid #F97316; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#F97316;">📅 Apr 2024</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://arxiv.org/abs/2407.21783" style="color:#F97316; text-decoration:none; font-weight:bold;">arXiv:2407.21783</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Architecture**: Same decoder-only Transformer skeleton; GQA now applied universally across all model sizes.
- **Vocabulary Jump**: 128,256 tokens using tiktoken BPE (previously 32K SentencePiece); much better multilingual and code tokenisation.
- **Model Sizes**: Two flagship sizes — 8B and 70B; 400B+ in pre-release preview.
- **Context Window**: 8,192 tokens (2× Llama 2); RoPE θ unchanged at 500,000 in the 3.1 release but at 8K here.
- **Training Scale**: 15T tokens (7.5× Llama 2); quality-filtered CommonCrawl + code + multilingual sources; 95% English.
- **8B Config**: 32 layers, 32 Q heads, 8 KV heads (4:1 GQA), hidden dim 4096.
- **70B Config**: 80 layers, 64 Q heads, 8 KV heads (8:1 GQA), hidden dim 8192.
- **Post-Training**: SFT (high-quality human instructions) → Rejection Sampling → PPO → DPO; four distinct stages.
- **Safety Tooling**: Llama Guard 2 and Code Shield released alongside; Meta Prompt Guard for injection detection.
- **FlashAttention 2**: Used throughout training; significantly improves memory efficiency vs v1 used in Llama 1.
- **Instruction-tuned Variants**: Llama 3 Instruct models show substantial jump on HumanEval (81.7 for 70B-Instruct) and MMLU (82.0) over Llama 2 70B.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #F97316; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 3 Architecture (Apr 2024)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">8B / 70B — GQA Universal · 128K Vocab · 15T Training Tokens · 8K Context</div>
  </div>
  <div style="background: white; border: 2px solid #FED7AA; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; position:relative; background: #F97316; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">
        <div style="position:absolute; top:-9px; right:8px; background:#C2410C; color:white; padding:1px 7px; border-radius:5px; font-size:10px; font-weight:bold;">NEW</div>
        Input Tokens (128,256 vocab — tiktoken BPE)
      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #EA580C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Token Embedding + RoPE (θ=500K in 3.1; 8K ctx here)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FED7AA; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFFBEB;">
      <div style="position: absolute; top: -10px; left: 16px; background: #F97316; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Repeat × 32 (8B) / 80 (70B) Layers</div>
      <div style="margin-top: 8px;">
        <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 6px;">
          <div style="flex:1; min-width:130px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#F97316; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; position:relative; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="position:absolute; top:-9px; right:10px; background:#F97316; color:white; padding:1px 8px; border-radius:6px; font-size:10px; font-weight:bold;">Universal GQA</div>
            <div style="font-weight:bold; color:#F97316; font-size:12px;">Grouped-Query Attention (GQA)</div>
            <div style="font-size:11px; color:#78350F;">8B: 32 Q / 8 KV · 70B: 64 Q / 8 KV<br/>FlashAttention 2</div>
          </div>
        </div>
        <div style="text-align:center; color:#9A3412; font-size:14px; margin-bottom:5px;">↓ residual ↓</div>
        <div style="display: flex; gap: 8px; flex-wrap: wrap;">
          <div style="flex:1; min-width:130px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#F97316; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; background:#FFF7ED; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#F97316; font-size:12px;">SwiGLU FFN (Dense)</div>
            <div style="font-size:11px; color:#78350F;">Same gated activation; scaled intermediate dim</div>
          </div>
        </div>
      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #D97706; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Final RMSNorm → LM Head (128,256)</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>
    <div style="text-align: center;">
      <div style="display: inline-block; background: #92400E; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Next-Token Prediction</div>
    </div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 3 represented a watershed moment in open-weight capabilities. The 70B Instruct model surpassed GPT-3.5 on several established benchmarks and competed seriously with early GPT-4 variants on coding tasks. The tiktoken vocabulary change, while breaking compatibility with prior Llama tokenisers, dramatically improved multilingual efficiency. The Llama Guard safety tooling suite became a widely referenced framework for responsible deployment of open models, with organisations like Hugging Face and AI safety researchers publishing extensive evaluations.

### Model Variants

| Model | Parameters | Layers | Heads (Q/KV) | Hidden Dim | FFN Dim | Context |
|:------|:----------:|:------:|:------------:|:----------:|:-------:|:-------:|
| Llama 3 8B | 8.0B | 32 | 32/8 | 4,096 | 14,336 | 8,192 |
| Llama 3 8B Instruct | 8.0B | 32 | 32/8 | 4,096 | 14,336 | 8,192 |
| Llama 3 70B | 70.6B | 80 | 64/8 | 8,192 | 28,672 | 8,192 |
| Llama 3 70B Instruct | 70.6B | 80 | 64/8 | 8,192 | 28,672 | 8,192 |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 3 Used It |
|:----------|:-------|:--------------------|
| tiktoken BPE | OpenAI (GPT-3/4 tokeniser) | 128,256 vocab; byte-level fallback; improved multilingual/code efficiency |
| Universal GQA | Ainslie et al., 2023 | Applied to all model sizes (previously only 34B/70B in Llama 2) |
| FlashAttention 2 | Dao et al., 2023 | 2× speedup vs FA1; used throughout training |
| DPO (Direct Preference Optimisation) | Rafailov et al., 2023 | Added as final post-training stage after SFT + RS + PPO |
| Rejection Sampling Fine-tuning | Meta Llama 2 paper | Multi-stage: SFT → RS → PPO → DPO |
| Llama Guard | Meta, 2023 | Input/output safety classification model; open-sourced alongside Llama 3 |
| Scaling Laws | Hoffmann et al., 2022 (Chinchilla) | 15T tokens selected to over-train per Chinchilla-optimal guidance |

</details>

---

## 🔶 Llama 3.1 — July 2024

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFFBEB; border:1.5px solid #D97706; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#D97706;">📅 Jul 2024</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://arxiv.org/abs/2407.21783" style="color:#D97706; text-decoration:none; font-weight:bold;">arXiv:2407.21783</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Context Explosion**: Long-context variant extends window to **128,192 tokens** via RoPE θ scaling to 500,000; trained with sequences up to 128K.
- **New Flagship — 405B**: 126 layers, 128 Q heads, 8 KV heads, hidden dim 16,384; largest open-weight dense model at time of release.
- **Multilingual**: Official support for 8 languages — English, German, French, Italian, Portuguese, Hindi, Spanish, Thai.
- **Tool Use & Function Calling**: First Llama to support structured JSON tool calls natively in the instruct model; enables agentic pipelines.
- **Multi-Stage Post-Training**: Supervised Fine-Tuning → Rejection Sampling (per-language) → DPO → PPO; each stage builds on the last.
- **Synthetic Data at Scale**: Meta generated large volumes of synthetic instruction and reasoning data (similar to Nemotron-4 approach); key to 405B performance.
- **Distillation from 405B**: The 8B and 70B Instruct models in 3.1 were explicitly improved via knowledge distillation from the 405B teacher.
- **VRAM Requirements**: 405B in BF16 requires ~8 × H100 80GB GPUs; 70B in BF16 requires ~2 × H100.
- **International Safety Standard**: First open model released with a model card addressing EU AI Act risk categories.
- **Benchmark**: 405B scores 88.6 on MMLU, 61.6 on GPQA Diamond, competing with GPT-4o at time of release.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 720px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #D97706; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 3.1 Architecture (Jul 2024)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">8B / 70B / 405B — 128K Context · RoPE θ=500K · Multilingual · Tool Use</div>
  </div>
  <div style="background: white; border: 2px solid #FDE68A; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #D97706; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Input Tokens (128,256 vocab)</div>
    </div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; position:relative; background: #B45309; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">
        <div style="position:absolute; top:-9px; right:8px; background:#D97706; color:white; padding:1px 7px; border-radius:5px; font-size:10px; font-weight:bold;">NEW</div>
        RoPE (θ=500,000) — 128K context window
      </div>
    </div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FDE68A; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFFBEB;">
      <div style="position: absolute; top: -10px; left: 16px; background: #D97706; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Repeat × 32 (8B) / 80 (70B) / 126 (405B) Layers</div>
      <div style="margin-top: 8px;">
        <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 6px;">
          <div style="flex:1; min-width:130px; background:#FFFBEB; border:1.5px solid #FDE68A; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#D97706; font-size:12px;">RMSNorm</div>
          </div>
          <div style="flex:2; min-width:220px; background:#FFFBEB; border:1.5px solid #FDE68A; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#D97706; font-size:12px;">GQA (RoPE θ=500K)</div>
            <div style="font-size:11px; color:#78350F;">8B: 32Q/8KV · 70B: 64Q/8KV · 405B: 128Q/8KV</div>
          </div>
        </div>
        <div style="text-align:center; color:#92400E; font-size:14px; margin-bottom:5px;">↓ residual ↓</div>
        <div style="display: flex; gap: 8px; flex-wrap: wrap;">
          <div style="flex:1; min-width:130px; background:#FFFBEB; border:1.5px solid #FDE68A; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#D97706; font-size:12px;">RMSNorm</div>
          </div>
          <div style="flex:2; min-width:220px; background:#FFFBEB; border:1.5px solid #FDE68A; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#D97706; font-size:12px;">SwiGLU FFN (Dense)</div>
          </div>
        </div>
      </div>
    </div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #92400E; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Final RMSNorm → LM Head</div>
    </div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>
    <div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap;">
      <div style="background: #7C2D12; color: white; padding: 6px 16px; border-radius: 7px; font-weight: bold; font-size:12px;">Base</div>
      <div style="background: #92400E; color: white; padding: 6px 16px; border-radius: 7px; font-weight: bold; font-size:12px;">Instruct (Multi-Stage RLHF)</div>
      <div style="position:relative; background: #B45309; color: white; padding: 6px 16px; border-radius: 7px; font-weight: bold; font-size:12px;">
        <div style="position:absolute; top:-9px; right:6px; background:#D97706; color:white; padding:1px 6px; border-radius:5px; font-size:10px;">NEW</div>
        Tool-Use / JSON Calling
      </div>
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 3.1 405B became a reference point for the entire open-source LLM ecosystem — arguably the first model to make GPT-4-level performance accessible without API dependence for well-resourced organisations. The 128K context window unlocked document-level reasoning tasks that were previously exclusive to commercial APIs. The officially sanctioned distillation policy (using 405B outputs to improve smaller Llama variants) enabled a new class of small-but-capable fine-tunes; community projects like Hermes 3, Nous-Capybara, and dozens of GGUF quantisations proliferated rapidly.

### Model Variants

| Model | Parameters | Layers | Heads (Q/KV) | Hidden Dim | FFN Dim | Context |
|:------|:----------:|:------:|:------------:|:----------:|:-------:|:-------:|
| Llama 3.1 8B | 8.0B | 32 | 32/8 | 4,096 | 14,336 | 131,072 |
| Llama 3.1 8B Instruct | 8.0B | 32 | 32/8 | 4,096 | 14,336 | 131,072 |
| Llama 3.1 70B | 70.6B | 80 | 64/8 | 8,192 | 28,672 | 131,072 |
| Llama 3.1 70B Instruct | 70.6B | 80 | 64/8 | 8,192 | 28,672 | 131,072 |
| Llama 3.1 405B | 405B | 126 | 128/8 | 16,384 | 53,248 | 131,072 |
| Llama 3.1 405B Instruct | 405B | 126 | 128/8 | 16,384 | 53,248 | 131,072 |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 3.1 Used It |
|:----------|:-------|:----------------------|
| RoPE θ Scaling (θ=500K) | Chen et al., 2023; Su et al., 2021 | Extends effective context from 8K to 128K via frequency interpolation |
| Knowledge Distillation | Hinton et al., 2015 | 405B used as teacher; 8B and 70B Instruct improved via distillation |
| Multi-Stage Post-Training | Meta internal | SFT → RS → DPO → PPO; each stage refines the previous |
| Synthetic Instruction Data | Gunasekar et al., 2023 (phi-1) | Large-scale synthetic generations for multilingual and tool-use alignment |
| Function/Tool Calling | OpenAI API design | Structured JSON schemas; models fine-tuned to follow tool schemas |
| Near-Optimal Scaling | Hoffmann et al., 2022 | 15T tokens for 405B; compute-optimal relative to model size |

</details>

---

## 🔷 Llama 3.2 — September 2024

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFFBEB; border:1.5px solid #B45309; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#B45309;">📅 Sep 2024</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/" style="color:#B45309; text-decoration:none; font-weight:bold;">Meta AI Blog</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Two Product Lines**: Vision models (11B, 90B) and edge/mobile text models (1B, 3B).
- **Vision Architecture**: Cross-attention adapter (Flamingo-style) attaching a frozen ViT image encoder to Llama 3.1 text backbone; cross-attention inserted at every fourth transformer layer.
- **Image Encoder**: ViT-H/14 backbone, image resolution 560 × 560 px; tiled to handle higher-resolution inputs.
- **Vision–Language Alignment**: Linear projection of ViT patch embeddings followed by cross-attention; text decoder treats image features as external memory.
- **11B Vision Model**: Llama 3.1 8B text backbone + vision adapter layers; strong single-image understanding, OCR, charts.
- **90B Vision Model**: Llama 3.1 70B text backbone + vision adapter; multi-image reasoning, document analysis, visual QA.
- **1B Edge Model**: Obtained via structured pruning of Llama 3.1 8B followed by knowledge distillation; targets mobile NPUs and CPUs.
- **3B Edge Model**: Pruned/distilled from Llama 3.1 70B with 8B as intermediate teacher; on-device performance near 7B-class.
- **Quantisation**: 1B and 3B released in INT4 SpinQuant and QAT quantised variants for mobile deployment (Apple silicon, Arm, Qualcomm).
- **On-Device Context**: Edge models support 128K context with quantised KV cache; designed for real-time streaming inference.
- **Meta AI Connect**: Announced at Meta Connect 2024 with live demo of on-device inference on Ray-Ban Meta glasses.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 720px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #B45309; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 3.2 Architecture (Sep 2024)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">Vision (11B/90B): Cross-Attention Adapter · Edge (1B/3B): Pruned + Distilled</div>
  </div>
  <div style="background: white; border: 2px solid #FDE68A; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="display: flex; gap: 14px; flex-wrap: wrap; margin-bottom: 8px;">

      <!-- Vision Branch -->
      <div style="flex:1; min-width:280px; border: 2px solid #FED7AA; border-radius: 10px; padding: 12px;">
        <div style="text-align:center; font-weight:bold; color:#B45309; margin-bottom:8px; font-size:13px;">🖼️ Vision Models (11B / 90B)</div>

        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; background:#B45309; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">Image Input (560×560)</div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓</div>
        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; position:relative; background:#92400E; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">
            <div style="position:absolute; top:-8px; right:6px; background:#B45309; color:white; padding:1px 6px; border-radius:4px; font-size:9px; font-weight:bold;">NEW</div>
            Frozen ViT-H/14 (Image Encoder)
          </div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓ patch embeddings ↓</div>
        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; background:#D97706; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">Linear Projection</div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓</div>
        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; position:relative; background:#F97316; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">
            <div style="position:absolute; top:-8px; right:6px; background:#B45309; color:white; padding:1px 6px; border-radius:4px; font-size:9px; font-weight:bold;">NEW</div>
            Cross-Attention Layers (every 4th)
          </div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓</div>
        <div style="text-align: center;">
          <div style="display:inline-block; background:#EA580C; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">Llama 3.1 Text Backbone (8B/70B)</div>
        </div>
      </div>

      <!-- Edge Branch -->
      <div style="flex:1; min-width:240px; border: 2px solid #FDE68A; border-radius: 10px; padding: 12px;">
        <div style="text-align:center; font-weight:bold; color:#B45309; margin-bottom:8px; font-size:13px;">📱 Edge Models (1B / 3B)</div>

        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; background:#F97316; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">Llama 3.1 8B / 70B (teacher)</div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓ structured pruning</div>
        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; position:relative; background:#D97706; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">
            <div style="position:absolute; top:-8px; right:6px; background:#B45309; color:white; padding:1px 6px; border-radius:4px; font-size:9px; font-weight:bold;">NEW</div>
            Pruned Decoder (fewer layers/heads)
          </div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓ knowledge distillation</div>
        <div style="text-align: center; margin-bottom:5px;">
          <div style="display:inline-block; background:#B45309; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">1B / 3B Student Model</div>
        </div>
        <div style="text-align: center; font-size:14px; color:#92400E; margin-bottom:5px;">↓</div>
        <div style="text-align: center;">
          <div style="display:inline-block; background:#92400E; color:white; padding:5px 14px; border-radius:6px; font-size:12px; font-weight:bold;">INT4 QAT / SpinQuant</div>
        </div>
      </div>

    </div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 3.2 marked Meta's entry into the on-device AI race. The 1B and 3B models demonstrated that a well-distilled small model could punch significantly above its weight class, reviving interest in model compression research. The vision models provided the first truly open-weight multimodal alternative to GPT-4V, with the 90B competing on several VQA and document benchmarks. The Flamingo-style cross-attention adapter architecture became a popular blueprint for community vision extensions to other text-only models.

### Model Variants

| Model | Parameters | Architecture | Input | Context | Notes |
|:------|:----------:|:------------|:-----:|:-------:|:------|
| Llama 3.2 1B | 1.24B | Pruned decoder | Text | 128K | Mobile/edge; INT4 quantised variants |
| Llama 3.2 3B | 3.21B | Pruned decoder | Text | 128K | On-device; QAT quantised variants |
| Llama 3.2 11B | 11B | Llama 3.1 8B + Vision Adapter | Text + Image | 128K | Cross-attention, ViT-H/14 |
| Llama 3.2 11B Vision Instruct | 11B | Llama 3.1 8B + Vision Adapter | Text + Image | 128K | Instruction-tuned; OCR, VQA |
| Llama 3.2 90B | 90B | Llama 3.1 70B + Vision Adapter | Text + Image | 128K | Multi-image, document analysis |
| Llama 3.2 90B Vision Instruct | 90B | Llama 3.1 70B + Vision Adapter | Text + Image | 128K | Instruction-tuned; chart/figure QA |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 3.2 Used It |
|:----------|:-------|:----------------------|
| Cross-Attention Vision Adapter | Flamingo (Alayrac et al., 2022) | Frozen ViT cross-attended at every 4th layer; text backbone preserved |
| ViT Image Encoder | Dosovitskiy et al., 2020 | ViT-H/14; 560×560 input with tiling for high-res |
| Structured Pruning | Michel et al., 2019; various | Layer/head/width pruning applied to Llama 3.1 8B → 1B and 70B → 3B |
| Knowledge Distillation | Hinton et al., 2015 | Teacher–student training with Llama 3.1 as teacher for edge models |
| SpinQuant / QAT | Meta internal, 2024 | INT4 quantisation-aware training for mobile deployment |
| LLM-in-a-glasses form factor | Meta Connect 2024 | Demonstrated on Ray-Ban Meta smart glasses for real-time inference |

</details>

---

## 🟫 Llama 3.3 — December 2024

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFFBEB; border:1.5px solid #92400E; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#92400E;">📅 Dec 2024</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://ai.meta.com/blog/llama-3-3/" style="color:#92400E; text-decoration:none; font-weight:bold;">Meta AI Blog</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Single Model Release**: Only one model size — 70B; same architectural specification as Llama 3.1 70B.
- **Enhanced Post-Training**: Entirely revised SFT dataset, new DPO preference pairs, and extended PPO reward modelling; no architectural changes.
- **Math & Reasoning Gains**: MATH benchmark improvement to ~77 (vs Llama 3.1 70B ~65); structured chain-of-thought data incorporated.
- **Coding Improvements**: HumanEval rises to 88.4 (vs 80.5 for 3.1 70B Instruct); new code-specific SFT and DPO data.
- **Instruction Following**: Significantly improved IFEval scores; better adherence to format constraints (JSON, markdown, lists).
- **Multilingual Retention**: All 8 languages from 3.1 maintained; additional data for Hindi and Thai in post-training.
- **Cost–Performance Target**: Designed to offer Llama 3.1 405B-class performance at 70B inference cost; Meta claims parity on many benchmarks.
- **Drop-in Replacement**: Compatible with Llama 3.1 70B serving infrastructure; same tokeniser, same context length, same tool-call schema.
- **Safety Updates**: Updated Llama Guard 3 and Prompt Guard released simultaneously; improved refusal on newer jailbreak patterns.
- **Research Insight**: Demonstrates that high-quality post-training data can yield substantial gains without any pretraining compute.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 680px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: #92400E; color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 3.3 Architecture (Dec 2024)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">70B only — Identical to Llama 3.1 70B · Enhanced Post-Training Pipeline</div>
  </div>
  <div style="background: white; border: 2px solid #FDE68A; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #92400E; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Llama 3.1 70B Base Architecture (unchanged)</div>
    </div>
    <div style="text-align: center; color: #78350F; font-size: 12px; opacity: 0.8; margin-bottom: 6px;">80 layers · 64Q/8KV GQA · RoPE θ=500K · 128K context · 128K vocab</div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FDE68A; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFFBEB;">
      <div style="position: absolute; top: -10px; left: 16px; background: #92400E; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Enhanced Post-Training Pipeline</div>
      <div style="margin-top: 10px; display: flex; flex-wrap: wrap; gap: 8px; justify-content: center;">

        <div style="background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 6px; padding: 8px 14px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-weight: bold; color: #92400E; font-size: 12px;">Stage 1</div>
          <div style="font-size: 11px; color: #78350F;">Revised SFT<br/>(Math + Code + IF)</div>
        </div>

        <div style="font-size: 18px; color: #92400E; display: flex; align-items: center;">→</div>

        <div style="background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 6px; padding: 8px 14px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-weight: bold; color: #92400E; font-size: 12px;">Stage 2</div>
          <div style="font-size: 11px; color: #78350F;">Rejection Sampling<br/>(per capability)</div>
        </div>

        <div style="font-size: 18px; color: #92400E; display: flex; align-items: center;">→</div>

        <div style="background: #FFF7ED; border: 1.5px solid #FED7AA; border-radius: 6px; padding: 8px 14px; text-align: center; flex: 1; min-width: 120px;">
          <div style="font-weight: bold; color: #92400E; font-size: 12px;">Stage 3</div>
          <div style="font-size: 11px; color: #78350F;">DPO (new pairs)<br/>+ PPO</div>
        </div>

      </div>
    </div>
    <div style="text-align: center; color: #92400E; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center;">
      <div style="display: inline-block; position:relative; background: #7C2D12; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">
        <div style="position:absolute; top:-9px; right:8px; background:#92400E; color:white; padding:1px 7px; border-radius:5px; font-size:10px; font-weight:bold;">IMPROVED</div>
        Llama 3.3 70B Instruct
      </div>
    </div>
    <div style="text-align: center; color: #78350F; font-size: 12px; margin-top: 6px; opacity: 0.8;">Drop-in replacement for Llama 3.1 70B Instruct · Same tool schemas · Same serving infrastructure</div>

  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 3.3 illustrated a key principle increasingly understood in the field: post-training data quality can deliver disproportionate gains relative to compute. By releasing a single improved 70B model, Meta offered organisations a free upgrade path from 3.1 70B with no infrastructure changes. The model quickly became the default recommendation for cost-sensitive deployments, with benchmarks showing it frequently matching or exceeding Llama 3.1 405B on reasoning and coding tasks at a fraction of the serving cost. It also sparked renewed interest in post-training research as an alternative to scaling pretraining compute.

### Model Variants

| Model | Parameters | Architecture | Context | Key Improvements |
|:------|:----------:|:------------|:-------:|:-----------------|
| Llama 3.3 70B | 70.6B | Identical to Llama 3.1 70B base | 128K | Better math/reasoning datasets |
| Llama 3.3 70B Instruct | 70.6B | Llama 3.1 70B base + new post-training | 128K | HumanEval 88.4, MATH ~77, IFEval gains |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 3.3 Used It |
|:----------|:-------|:----------------------|
| High-Quality SFT Curation | LIMA (Zhou et al., 2023) | "Less is more" principle applied; curated math/code/IF examples over volume |
| Process Reward Models (PRMs) | Lightman et al., 2023 | Step-level math reward signals incorporated in rejection sampling |
| DPO with Fresh Preference Data | Rafailov et al., 2023 | New human preference labels targeting 3.1 70B failure modes |
| Chain-of-Thought Distillation | Wei et al., 2022 | CoT traces from 405B used to supervise 70B on reasoning |
| IFEval-targeted Training | Zhou et al., 2023 | Explicit instruction-following format tasks in SFT and DPO |
| Capability-specific Rejection Sampling | Meta internal | Separate RS pools for math, code, multilingual, and safety |

</details>

---

## 🔴 Llama 4 — April 2025

<div style="display:inline-flex; align-items:center; gap:8px; background:#FFF7ED; border:1.5px solid #7C2D12; border-radius:8px; padding:6px 14px; margin-bottom:16px; font-size:13px;">
  <strong style="color:#7C2D12;">📅 Apr 2025</strong>
  <span style="color:#78350F;">|</span>
  <a href="https://ai.meta.com/blog/llama-4-multimodal-intelligence/" style="color:#7C2D12; text-decoration:none; font-weight:bold;">Meta AI Blog</a>
  <span style="color:#78350F;">|</span>
  <span style="color:#78350F;">Meta AI</span>
</div>

### Summary

- **Architecture Paradigm Shift**: Native early-fusion multimodal Mixture-of-Experts (MoE); abandons the cross-attention adapter approach of Llama 3.2.
- **Early Fusion**: Images and text tokens processed together from the first layer; no separate vision encoder; unified tokenisation of visual and language tokens.
- **MoE FFN**: Sparse expert routing replaces the dense SwiGLU FFN; only a subset of experts activated per token, dramatically reducing active parameters vs total parameters.
- **iRoPE Positional Encoding**: "interleaved RoPE" — alternating attention layers have no positional encoding (NoPE / infinite context) and layers with standard RoPE; enables extrapolation to very long sequences.
- **Scout (109B total, 17B active)**: 16 experts; 10M token context window; single H100 deployment target; vision + text.
- **Maverick (400B total, 17B active)**: 128 experts per token routing; 1M context; full multimodal; strong benchmark performance; requires ~8× H100.
- **Behemoth (~2T total, 288B active)**: 16 experts; training/teacher model; used for distillation; 16K+ context; not released in April 2025.
- **MetaP Hyperparameter Tuning**: New technique for systematic hyperparameter transfer across model scales; stabilises training of very large MoE models.
- **Training Scale**: 30T+ tokens on multimodal data; largest pretraining budget in the Llama series.
- **Omni Capability**: Scout and Maverick handle image, video, and text inputs; text-only output at release; voice planned.
- **Benchmark**: Maverick achieves 80.5 on MMLU-Pro and competitive results on GPQA Diamond; Scout's 10M context enables retrieval over entire codebases.

### Architecture Diagram

{::nomarkdown}
<div style="max-width: 750px; margin: 1.5em auto; font-family: 'Segoe UI', system-ui, sans-serif; font-size: 13px;">
  <div style="background: linear-gradient(135deg, #7C2D12 0%, #9A3412 100%); color: white; padding: 12px 20px; border-radius: 10px 10px 0 0; text-align: center;">
    <strong style="font-size: 15px;">Llama 4 Architecture (Apr 2025)</strong>
    <div style="font-size: 11px; opacity: 0.85; margin-top: 3px;">Scout (17B active / 109B total) · Maverick (17B active / 400B total) · Early-Fusion MoE · iRoPE</div>
  </div>
  <div style="background: white; border: 2px solid #FED7AA; border-top: none; border-radius: 0 0 10px 10px; padding: 18px 20px;">

    <!-- Input row: text + image side by side -->
    <div style="display: flex; gap: 14px; justify-content: center; margin-bottom: 6px; flex-wrap: wrap;">
      <div style="background: #7C2D12; color: white; padding: 6px 18px; border-radius: 7px; font-weight: bold; font-size:12px;">Text Tokens</div>
      <div style="position:relative; background: #9A3412; color: white; padding: 6px 18px; border-radius: 7px; font-weight: bold; font-size:12px;">
        <div style="position:absolute; top:-9px; right:6px; background:#7C2D12; color:white; padding:1px 6px; border-radius:5px; font-size:10px; font-weight:bold;">NEW</div>
        Visual Tokens (Early Fusion)
      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓ unified tokenisation ↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; position:relative; background: #C2410C; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">
        <div style="position:absolute; top:-9px; right:8px; background:#7C2D12; color:white; padding:1px 7px; border-radius:5px; font-size:10px; font-weight:bold;">NEW</div>
        iRoPE Positional Encoding (interleaved)
      </div>
    </div>
    <div style="text-align: center; color: #78350F; font-size: 11px; margin-bottom: 6px; opacity: 0.8;">Alternating: NoPE layers (no position) + RoPE layers (full position) → enables 10M context</div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="position: relative; border: 2px dashed #FED7AA; border-radius: 8px; padding: 14px 16px; margin-bottom: 6px; background: #FFF7ED;">
      <div style="position: absolute; top: -10px; left: 16px; background: #7C2D12; color: white; padding: 2px 10px; border-radius: 8px; font-size: 11px; font-weight: bold;">Repeat × N Layers</div>
      <div style="margin-top: 8px;">

        <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 6px;">
          <div style="flex:1; min-width:130px; background:white; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#7C2D12; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; background:white; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#7C2D12; font-size:12px;">GQA Self-Attention</div>
            <div style="font-size:11px; color:#78350F;">iRoPE: even layers NoPE / odd layers RoPE<br/>Scout: 16Q/8KV · Maverick: larger config</div>
          </div>
        </div>

        <div style="text-align:center; color:#9A3412; font-size:14px; margin-bottom:5px;">↓ residual ↓</div>

        <div style="display: flex; gap: 8px; flex-wrap: wrap;">
          <div style="flex:1; min-width:130px; background:white; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="font-weight:bold; color:#7C2D12; font-size:12px;">RMSNorm (pre)</div>
          </div>
          <div style="flex:2; min-width:220px; position:relative; background:white; border:1.5px solid #FED7AA; border-radius:6px; padding:7px 10px; text-align:center;">
            <div style="position:absolute; top:-9px; right:10px; background:#7C2D12; color:white; padding:1px 8px; border-radius:6px; font-size:10px; font-weight:bold;">NEW: MoE FFN</div>
            <div style="font-weight:bold; color:#7C2D12; font-size:12px;">Sparse MoE (SwiGLU Experts)</div>
            <div style="font-size:11px; color:#78350F;">Scout: 16 experts (top-k routing)<br/>Maverick: 128 experts (top-k routing)<br/>Behemoth: 16 experts, 288B active</div>
          </div>
        </div>

      </div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>

    <div style="text-align: center; margin-bottom: 6px;">
      <div style="display: inline-block; background: #B45309; color: white; padding: 6px 22px; border-radius: 7px; font-weight: bold;">Final RMSNorm → LM Head</div>
    </div>
    <div style="text-align: center; color: #9A3412; font-size: 16px; margin-bottom: 6px;">↓</div>
    <div style="display: flex; gap: 10px; justify-content: center; flex-wrap: wrap;">
      <div style="background: #7C2D12; color: white; padding: 5px 14px; border-radius: 7px; font-weight: bold; font-size:11px;">Scout (10M ctx, 1×H100)</div>
      <div style="background: #9A3412; color: white; padding: 5px 14px; border-radius: 7px; font-weight: bold; font-size:11px;">Maverick (1M ctx, 8×H100)</div>
      <div style="background: #C2410C; color: white; padding: 5px 14px; border-radius: 7px; font-weight: bold; font-size:11px;">Behemoth (Teacher, ~2T total)</div>
    </div>
  </div>
</div>
{:/nomarkdown}

### Community Perspective

Llama 4 represents the most architecturally ambitious Llama release to date. The shift from cross-attention vision adapters to native early-fusion MoE aligns Meta's approach with GPT-4o and Gemini Ultra in treating multimodality as a first-class concern rather than an add-on. Scout's 10M-token context window is unprecedented in an open-weight model and opens entirely new application categories — whole-repository code analysis, book-length summarisation, and long-horizon agentic tasks. The iRoPE position encoding scheme attracted significant academic interest as a practical solution to the context-length extrapolation problem. The MetaP hyperparameter transfer technique addresses one of the key engineering pain points in training very large MoE models reliably.

### Model Variants

| Model | Total Params | Active Params | Experts | Context | Modality | Notes |
|:------|:------------:|:-------------:|:-------:|:-------:|:--------:|:------|
| Llama 4 Scout | 109B | 17B | 16 | 10M | Text + Image | Single H100 80GB deployment |
| Llama 4 Scout Instruct | 109B | 17B | 16 | 10M | Text + Image | Instruction-tuned |
| Llama 4 Maverick | 400B | 17B | 128 | 1M | Text + Image | ~8× H100 required |
| Llama 4 Maverick Instruct | 400B | 17B | 128 | 1M | Text + Image | Instruction-tuned; MMLU-Pro 80.5 |
| Llama 4 Behemoth | ~2T | 288B | 16 | 16K+ | Text | Teacher model; not released Apr 2025 |

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Llama 4 Used It |
|:----------|:-------|:--------------------|
| Sparse MoE (top-k routing) | Shazeer et al., 2017; Switch Transformer | Each token routes to top-k of N experts in FFN; ~17B active per token |
| Early Fusion Multimodal | Gemini (Google, 2023); Chameleon (Meta, 2024) | Visual and text tokens processed jointly from layer 0; no cross-attention adapter |
| iRoPE (interleaved NoPE + RoPE) | Meta internal; inspired by YaRN, LongRoPE | Alternating position-free and RoPE layers enable 10M+ context extrapolation |
| MetaP Hyperparameter Transfer | Meta internal, 2025 | μP-inspired framework for transferring LR/batch-size across MoE scales |
| Load Balancing Loss (MoE) | Lepikhin et al., 2020; Fedus et al., 2021 | Auxiliary loss to prevent expert collapse during training |
| 30T+ Token Pretraining | Meta internal | Multimodal web-scale corpus; largest single pretraining run in the series |
| Distillation from Behemoth | Hinton et al., 2015 | Scout and Maverick post-training improved via Behemoth teacher |

</details>

---

## 📚 References

### Technical Papers

| Paper | Authors | Year | Topic |
|:------|:--------|:----:|:------|
| [LLaMA: Open and Efficient Foundation Language Models](https://arxiv.org/abs/2302.13971) | Touvron et al. | 2023 | Llama 1 |
| [Llama 2: Open Foundation and Fine-Tuned Chat Models](https://arxiv.org/abs/2307.09288) | Touvron et al. | 2023 | Llama 2 |
| [The Llama 3 Herd of Models](https://arxiv.org/abs/2407.21783) | Meta AI | 2024 | Llama 3 / 3.1 |
| [RoFormer: Enhanced Transformer with Rotary Position Embedding](https://arxiv.org/abs/2104.09864) | Su et al. | 2021 | RoPE |
| [Root Mean Square Layer Normalization](https://arxiv.org/abs/1910.07467) | Zhang & Sennrich | 2019 | RMSNorm |
| [GLU Variants Improve Transformer](https://arxiv.org/abs/2002.05202) | Shazeer | 2020 | SwiGLU |
| [GQA: Training Generalised Multi-Query Transformer Models from Multi-Head Checkpoints](https://arxiv.org/abs/2305.13245) | Ainslie et al. | 2023 | GQA |
| [FlashAttention-2: Faster Attention with Better Parallelism and Work Partitioning](https://arxiv.org/abs/2307.08691) | Dao | 2023 | FlashAttention 2 |
| [Training Language Models to Follow Instructions with Human Feedback](https://arxiv.org/abs/2203.02155) | Ouyang et al. | 2022 | RLHF / InstructGPT |
| [Direct Preference Optimization: Your Language Model is Secretly a Reward Model](https://arxiv.org/abs/2305.18290) | Rafailov et al. | 2023 | DPO |
| [Flamingo: a Visual Language Model for Few-Shot Learning](https://arxiv.org/abs/2204.14198) | Alayrac et al. | 2022 | Cross-Attention Vision |
| [Outrageously Large Neural Networks: The Sparsely-Gated Mixture-of-Experts Layer](https://arxiv.org/abs/1701.06538) | Shazeer et al. | 2017 | MoE |

### Official Blog Posts

| Post | Date | Topic |
|:-----|:----:|:------|
| [Introducing LLaMA: A foundational, 65-billion-parameter language model](https://ai.meta.com/blog/large-language-model-llama-meta-ai/) | Feb 2023 | Llama 1 announcement |
| [Llama 2: Open Foundation and Fine-Tuned Chat Models](https://ai.meta.com/llama/) | Jul 2023 | Llama 2 release |
| [Meta Llama 3](https://ai.meta.com/blog/meta-llama-3/) | Apr 2024 | Llama 3 announcement |
| [Llama 3.1: Our most capable models to date](https://ai.meta.com/blog/meta-llama-3-1/) | Jul 2024 | Llama 3.1 / 405B |
| [Llama 3.2: Revolutionizing edge AI and vision](https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/) | Sep 2024 | Vision + edge models |
| [Llama 3.3: New 70B model with improved performance](https://ai.meta.com/blog/llama-3-3/) | Dec 2024 | Llama 3.3 70B |
| [Llama 4: The next generation of open foundation models](https://ai.meta.com/blog/llama-4-multimodal-intelligence/) | Apr 2025 | Llama 4 Scout/Maverick |

### GitHub Repositories

| Repository | Description |
|:-----------|:------------|
| [meta-llama/llama](https://github.com/meta-llama/llama) | Official Llama 1 weights and inference code |
| [meta-llama/llama2](https://github.com/meta-llama/llama2) | Official Llama 2 repository |
| [meta-llama/llama3](https://github.com/meta-llama/llama3) | Official Llama 3 tokeniser and model card |
| [meta-llama/llama-models](https://github.com/meta-llama/llama-models) | Unified Llama 3.x model cards and configs |
| [ggerganov/llama.cpp](https://github.com/ggerganov/llama.cpp) | C++ inference engine; GGUF quantised formats |
| [huggingface/transformers](https://github.com/huggingface/transformers) | HF integration for all Llama variants |
| [vllm-project/vllm](https://github.com/vllm-project/vllm) | High-throughput inference with continuous batching |
| [meta-llama/PurpleLlama](https://github.com/meta-llama/PurpleLlama) | Llama Guard, Code Shield, Prompt Guard safety tools |

### Cited Techniques

| Technique | Reference | Used In |
|:----------|:----------|:--------|
| Rotary Position Embeddings (RoPE) | Su et al., arXiv:2104.09864 | Llama 1–4 |
| RMSNorm | Zhang & Sennrich, NeurIPS 2019 | Llama 1–4 |
| SwiGLU | Shazeer, arXiv:2002.05202 | Llama 1–4 |
| Grouped-Query Attention | Ainslie et al., arXiv:2305.13245 | Llama 2 (34B/70B), 3–4 |
| FlashAttention 2 | Dao, arXiv:2307.08691 | Llama 3–4 |
| tiktoken BPE | OpenAI, 2022 | Llama 3–4 |
| RLHF / PPO | Stiennon et al.; Ouyang et al. | Llama 2–3.3 |
| DPO | Rafailov et al., arXiv:2305.18290 | Llama 3–3.3 |
| Knowledge Distillation | Hinton et al., arXiv:1503.02531 | Llama 3.1, 3.2, 4 |
| Ghost Attention | Touvron et al., arXiv:2307.09288 | Llama 2 Chat |
| Sparse MoE | Shazeer et al., arXiv:1701.06538 | Llama 4 |
| Early-Fusion Multimodal | Chameleon (Meta); Gemini | Llama 4 |
| iRoPE | Meta internal, 2025 | Llama 4 |
| SpinQuant / QAT | Meta internal, 2024 | Llama 3.2 edge |
| Structured Pruning | Michel et al., 2019 | Llama 3.2 1B/3B |

---

<p align="center">
  <sub>This document covers the Llama model family from Llama 1 (Feb 2023) through Llama 4 (Apr 2025).<br/>
  All benchmark figures are reported as published; instruct vs base distinctions apply where noted.<br/>
  Maintained for educational and research reference purposes.</sub>
</p>

<p align="center"><a href="#">↑ Back to top</a></p>
