---
title: "Alpamayo — Vision-Language-Action Model for Physical AI"
---

<h1 align="center">🟢 Alpamayo — Vision-Language-Action Model for Physical AI</h1>

<p align="center">
  <img src="https://img.shields.io/badge/Versions_Covered-2-10B981?style=for-the-badge" alt="Versions: 2"/>
  <img src="https://img.shields.io/badge/Team-NVIDIA_Research-76B900?style=for-the-badge" alt="Team: NVIDIA Research"/>
  <img src="https://img.shields.io/badge/Last_Updated-March_2026-059669?style=for-the-badge" alt="Updated: March 2026"/>
  <img src="https://img.shields.io/badge/Model_Type-VLA_(Vision--Language--Action)-047857?style=for-the-badge" alt="Model Type: VLA"/>
</p>

<p align="center"><i>From perception to action — NVIDIA's first open-source reasoning-driven Vision-Language-Action model for autonomous driving, bridging language reasoning and physical world trajectory prediction.</i></p>

---

## 📑 Table of Contents

- [Executive Summary](#-executive-summary)
- [What is a VLA Model? — VLA vs LLM Architecture Comparison](#-what-is-a-vla-model--vla-vs-llm-architecture-comparison)
- [NVIDIA Physical AI Stack — Ecosystem Context](#-nvidia-physical-ai-stack--ecosystem-context)
- [Version Release Timeline](#-version-release-timeline)
- [Cross-Version Benchmark Comparison](#-cross-version-benchmark-comparison)
- [Master Architecture Diagram](#-master-architecture-diagram)
- [Alpamayo-R1 (December 2025 — NeurIPS 2025)](#-alpamayo-r1--december-2025--neurips-2025)
  - [Summary](#summary)
  - [Architecture: Vision Encoder](#architecture-component-1-multi-camera-multi-timestep-vision-encoder)
  - [Architecture: Cosmos-Reason VLM Backbone](#architecture-component-2-cosmos-reason-vlm-backbone)
  - [Architecture: Chain of Causation](#architecture-component-3-chain-of-causation-reasoning-paradigm)
  - [Architecture: Diffusion Trajectory Decoder](#architecture-component-4-diffusion-based-trajectory-decoder)
  - [Training Pipeline](#training-pipeline)
  - [Chain of Causation Dataset](#chain-of-causation-dataset)
  - [Deployment & Hardware Requirements](#deployment--hardware-requirements)
  - [Architecture Diagram](#architecture-diagram--alpamayo-r1-full-system)
  - [Chain of Causation Flowchart](#chain-of-causation-flowchart)
  - [Community Perspective](#community-perspective)
  - [Model Variants](#model-variants)
  - [Key Industry Ideas](#key-industry-ideas-incorporated)
- [Alpamayo 1.5 (March 2026)](#-alpamayo-15--march-2026)
  - [Summary](#summary-1)
  - [Architecture: Cosmos-Reason2 Backbone](#architecture-cosmos-reason2-vlm-backbone)
  - [Architecture: Diffusion Expert](#architecture-diffusion-expert-trajectory-decoder)
  - [AlpaSim Simulation Framework](#alpasin-simulation-framework)
  - [Training & Dataset Expansion](#training--dataset-expansion)
  - [Architecture Diagram](#architecture-diagram--alpamayo-15)
  - [Community Perspective](#community-perspective-1)
  - [Model Variants](#model-variants-1)
  - [Key Industry Ideas](#key-industry-ideas-incorporated-1)
- [References](#-references)

---

## 📋 Executive Summary

This document covers **two generations** of the Alpamayo VLA family developed by NVIDIA:

- **Alpamayo-R1** — First open-source reasoning-driven VLA for autonomous driving: surround-view cameras → chain-of-causation reasoning → diffusion trajectory prediction
- **Alpamayo 1.5** — Scale-up with Cosmos-Reason2 (8.2B) + dedicated Diffusion Expert (2.3B), promptable text conditioning, AlpaSim closed-loop evaluation, 80K-hour training dataset

> **📝 Note on Model Type:** Alpamayo is fundamentally different from pure large language models (LLMs) such as Qwen, Llama, or Gemma. It is a **Vision-Language-Action (VLA)** model — a new class of foundation model that perceives multi-camera video input, reasons over the physical world in natural language, and produces **action outputs** (vehicle trajectories) rather than text tokens. This hybrid architecture is explained in detail in the [VLA vs LLM section](#-what-is-a-vla-model--vla-vs-llm-architecture-comparison).

> **📝 Note on Naming:** "Alpamayo" is named after the Peruvian mountain peak Alpamayo, consistent with NVIDIA's tradition of naming autonomous driving research projects after geographic landmarks. The "-R1" suffix denotes the first reasoning-enabled release, paralleling the "R1" naming convention popularized by DeepSeek-R1 to signal a reasoning-first design philosophy.

Key highlights across the Alpamayo family:

- **Alpamayo-R1** (Dec 2025) — First open-source VLA with explicit **Chain of Causation** reasoning: vision → situation → rationale → trajectory; three model sizes (0.5B / 3B / 7B); diffusion-based trajectory decoder; 99ms inference on A100 (7B model)
- **Alpamayo 1.5** (Mar 2026) — Upgraded to **Cosmos-Reason2** (8.2B) + **Diffusion Expert** (2.3B); promptable text conditioning; trained on 80,000 hours / 1B+ images; AlpaSim closed-loop score 0.81; minADE 1.11m; supports 25+ countries of driving data; PhysicalAI Open Datasets released in parallel
- **Dataset** — Chain of Causation Dataset: 1,727 hours of annotated driving data spanning 2,500+ cities, open-sourced alongside model weights; expanded to 80,000 hours for 1.5 (PhysicalAI dataset, 25 countries)
- **Hardware Integration** — Targets NVIDIA DRIVE Thor, DRIVE Orin, H100 datacenter GPU; sits within the broader Cosmos world foundation model ecosystem

---

## 🔬 What is a VLA Model? — VLA vs LLM Architecture Comparison

Understanding Alpamayo requires first understanding how Vision-Language-Action models differ from conventional large language models. This is a fundamentally different computational paradigm.

### The LLM Paradigm

A traditional large language model (LLM) such as GPT-4, Qwen, or LLaMA operates on a simple and elegant loop:

1. **Input**: A sequence of discrete text tokens (integers indexing a vocabulary)
2. **Processing**: A stack of Transformer decoder layers with self-attention and feed-forward networks
3. **Output**: A probability distribution over the next text token (autoregressive generation)

The entire interface — input and output — is **text**. Even multimodal LLMs (VLMs) such as LLaVA or Qwen-VL extend this paradigm by encoding images into token-like embeddings, but the output remains text tokens.

### The VLA Paradigm

A Vision-Language-Action model such as Alpamayo operates on a fundamentally richer loop:

1. **Input**: Raw sensor data from the physical world — specifically, multi-camera video frames from a surround-view camera rig
2. **Intermediate Processing (Language)**: A vision-language model reasons over the visual input and produces natural language descriptions of the scene and driving decisions
3. **Output**: Not text tokens, but **continuous action vectors** — specifically, (x, y, heading) waypoints over time forming a vehicle trajectory

The key distinction is the **action output modality**. Alpamayo must bridge the gap between the discrete, symbolic world of language tokens and the continuous, physical world of vehicle kinematics. This is achieved through a **diffusion-based trajectory decoder** rather than a language model head.

{::nomarkdown}
<div style="max-width: 800px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <div style="background: linear-gradient(135deg, #059669 0%, #047857 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center; font-size: 17px; font-weight: 700; letter-spacing: 0.5px;">
    VLA vs LLM — Architecture Comparison
  </div>

  <div style="background: #F0FDF4; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 12px 12px; padding: 24px;">

    <div style="display: flex; gap: 16px; flex-wrap: wrap; margin-bottom: 16px;">

      <!-- LLM Column -->
      <div style="flex: 1; min-width: 220px; border: 2px solid #9CA3AF; border-radius: 10px; overflow: hidden;">
        <div style="background: #4B5563; color: white; padding: 10px 14px; font-weight: 700; text-align: center; font-size: 14px;">🔤 Large Language Model (LLM)</div>
        <div style="padding: 14px; background: white;">
          <div style="background: #F3F4F6; border: 1px solid #D1D5DB; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            <strong>Input:</strong> Text tokens
          </div>
          <div style="text-align: center; color: #9CA3AF; font-size: 16px;">↓</div>
          <div style="background: #E5E7EB; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            Tokenizer → Embedding
          </div>
          <div style="text-align: center; color: #9CA3AF; font-size: 16px;">↓</div>
          <div style="background: #D1D5DB; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            Transformer Decoder Layers<br/><span style="font-size: 11px;">(Attention + FFN × N)</span>
          </div>
          <div style="text-align: center; color: #9CA3AF; font-size: 16px;">↓</div>
          <div style="background: #F3F4F6; border: 1px solid #D1D5DB; border-radius: 6px; padding: 8px 12px; text-align: center; font-size: 13px;">
            <strong>Output:</strong> Text token logits<br/><span style="font-size: 11px;">→ next token (autoregressive)</span>
          </div>
        </div>
      </div>

      <!-- Arrow -->
      <div style="display: flex; align-items: center; font-size: 28px; color: #10B981; font-weight: 900; flex-shrink: 0;">→</div>

      <!-- VLA Column -->
      <div style="flex: 1; min-width: 220px; border: 2px solid #059669; border-radius: 10px; overflow: hidden;">
        <div style="background: #059669; color: white; padding: 10px 14px; font-weight: 700; text-align: center; font-size: 14px;">🎥 Vision-Language-Action (VLA)</div>
        <div style="padding: 14px; background: white;">
          <div style="background: #D1FAE5; border: 1px solid #6EE7B7; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            <strong>Input:</strong> Multi-camera video frames<br/><span style="font-size: 11px;">(6 cameras × multiple timesteps)</span>
          </div>
          <div style="text-align: center; color: #059669; font-size: 16px;">↓</div>
          <div style="background: #A7F3D0; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            Vision Encoder → Visual Tokens
          </div>
          <div style="text-align: center; color: #059669; font-size: 16px;">↓</div>
          <div style="background: #6EE7B7; border-radius: 6px; padding: 8px 12px; text-align: center; margin-bottom: 6px; font-size: 13px;">
            VLM Backbone → Reasoning Trace<br/><span style="font-size: 11px;">(Chain of Causation text)</span>
          </div>
          <div style="text-align: center; color: #059669; font-size: 16px;">↓</div>
          <div style="background: #D1FAE5; border: 1px solid #6EE7B7; border-radius: 6px; padding: 8px 12px; text-align: center; font-size: 13px;">
            <strong>Output:</strong> Trajectory waypoints<br/><span style="font-size: 11px;">→ (x, y, heading) @ 10Hz via Diffusion</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Comparison Table -->
    <div style="background: white; border: 1px solid #A7F3D0; border-radius: 8px; overflow: hidden;">
      <table style="width: 100%; font-size: 12px; border-collapse: collapse;">
        <thead>
          <tr style="background: #059669; color: white;">
            <th style="padding: 8px 12px; text-align: left;">Dimension</th>
            <th style="padding: 8px 12px; text-align: center;">LLM</th>
            <th style="padding: 8px 12px; text-align: center;">VLA (Alpamayo)</th>
          </tr>
        </thead>
        <tbody>
          <tr style="background: #F0FDF4;">
            <td style="padding: 7px 12px; font-weight: 600;">Input modality</td>
            <td style="padding: 7px 12px; text-align: center;">Text tokens</td>
            <td style="padding: 7px 12px; text-align: center;">Camera images + scene context</td>
          </tr>
          <tr>
            <td style="padding: 7px 12px; font-weight: 600;">Output modality</td>
            <td style="padding: 7px 12px; text-align: center;">Text tokens</td>
            <td style="padding: 7px 12px; text-align: center;">Continuous action waypoints</td>
          </tr>
          <tr style="background: #F0FDF4;">
            <td style="padding: 7px 12px; font-weight: 600;">Decoder type</td>
            <td style="padding: 7px 12px; text-align: center;">LM Head (linear + softmax)</td>
            <td style="padding: 7px 12px; text-align: center;">Diffusion model decoder</td>
          </tr>
          <tr>
            <td style="padding: 7px 12px; font-weight: 600;">Reasoning</td>
            <td style="padding: 7px 12px; text-align: center;">Chain-of-thought (text)</td>
            <td style="padding: 7px 12px; text-align: center;">Chain of Causation (perception→action)</td>
          </tr>
          <tr style="background: #F0FDF4;">
            <td style="padding: 7px 12px; font-weight: 600;">Real-time constraint</td>
            <td style="padding: 7px 12px; text-align: center;">None (latency flexible)</td>
            <td style="padding: 7px 12px; text-align: center;">Hard: ≤100ms @ 10Hz</td>
          </tr>
          <tr>
            <td style="padding: 7px 12px; font-weight: 600;">Physical grounding</td>
            <td style="padding: 7px 12px; text-align: center;">None (symbolic only)</td>
            <td style="padding: 7px 12px; text-align: center;">Vehicle kinematics, road geometry</td>
          </tr>
          <tr style="background: #F0FDF4;">
            <td style="padding: 7px 12px; font-weight: 600;">Training signal</td>
            <td style="padding: 7px 12px; text-align: center;">Next-token prediction + RLHF</td>
            <td style="padding: 7px 12px; text-align: center;">SFT + RL for reasoning-action alignment</td>
          </tr>
          <tr>
            <td style="padding: 7px 12px; font-weight: 600;">Safety verification</td>
            <td style="padding: 7px 12px; text-align: center;">Human preference / harmlessness</td>
            <td style="padding: 7px 12px; text-align: center;">Collision rate, L2 trajectory error</td>
          </tr>
        </tbody>
      </table>
    </div>

  </div>
</div>
{:/nomarkdown}

### Why Physical AI is Different from Language AI

Physical AI systems like Alpamayo face a set of constraints that are irrelevant to conversational LLMs:

1. **Hard real-time constraints**: A vehicle traveling at 60 km/h moves ~1.7 meters in 100ms. Planning must complete within this window — there is no "thinking as long as needed" luxury.
2. **Multi-modal sensor fusion**: Unlike text, camera data from 6 directions must be spatially coherent and temporally consistent across multiple frames before any reasoning begins.
3. **Continuous action spaces**: LLMs output from a finite vocabulary (e.g., 150,000 tokens). A vehicle's action space is continuous — any heading from 0–360°, any velocity from 0–130 km/h — requiring fundamentally different output representations.
4. **Causal safety requirements**: A mistake in a language model produces incorrect text. A mistake in a VLA model can cause physical harm. This demands not just accuracy but **interpretability** — hence Chain of Causation reasoning.
5. **Distribution shift robustness**: Language models can fail gracefully on unusual inputs. Autonomous vehicles must handle rare, safety-critical edge cases (emergency vehicles, unusual road conditions) where training data is scarce.

---

## 🌐 NVIDIA Physical AI Stack — Ecosystem Context

Alpamayo does not exist in isolation — it is a specialized component of NVIDIA's broader physical AI foundation model ecosystem, the **Cosmos** platform.

{::nomarkdown}
<div style="max-width: 750px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <div style="background: linear-gradient(135deg, #047857 0%, #065F46 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center; font-size: 16px; font-weight: 700;">
    NVIDIA Cosmos Physical AI Ecosystem
  </div>

  <div style="background: #ECFDF5; border: 2px solid #6EE7B7; border-top: none; border-radius: 0 0 12px 12px; padding: 24px;">

    <!-- Layer 1: World Foundation -->
    <div style="background: #065F46; color: white; border-radius: 8px; padding: 12px 16px; margin-bottom: 10px; text-align: center;">
      <strong>Cosmos World Foundation Models</strong>
      <div style="font-size: 12px; opacity: 0.85; margin-top: 4px;">Video generation and world simulation for synthetic data creation and scenario augmentation</div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓ provides synthetic training scenarios ↓</div>

    <!-- Layer 2: Reasoning VLM -->
    <div style="background: #047857; color: white; border-radius: 8px; padding: 12px 16px; margin-bottom: 10px; margin-top: 8px; text-align: center;">
      <strong>Cosmos-Reason VLM</strong>
      <div style="font-size: 12px; opacity: 0.85; margin-top: 4px;">Vision-language model pre-trained for physical world reasoning — backbone of Alpamayo</div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓ fine-tuned for driving ↓</div>

    <!-- Layer 3: Alpamayo -->
    <div style="background: #059669; color: white; border-radius: 8px; padding: 16px; margin-bottom: 10px; margin-top: 8px; border: 3px solid #34D399; text-align: center;">
      <strong style="font-size: 16px;">🚗 Alpamayo-R1 (VLA)</strong>
      <div style="font-size: 12px; opacity: 0.9; margin-top: 6px;">Surround-view perception → Chain of Causation reasoning → Diffusion trajectory prediction</div>
      <div style="margin-top: 8px; display: flex; gap: 8px; flex-wrap: wrap; justify-content: center;">
        <span style="background: rgba(255,255,255,0.2); padding: 3px 8px; border-radius: 4px; font-size: 11px;">0.5B / 3B / 7B</span>
        <span style="background: rgba(255,255,255,0.2); padding: 3px 8px; border-radius: 4px; font-size: 11px;">Open-source (Research)</span>
        <span style="background: rgba(255,255,255,0.2); padding: 3px 8px; border-radius: 4px; font-size: 11px;">NeurIPS 2025</span>
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓ deployed on ↓</div>

    <!-- Layer 4: Hardware -->
    <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-top: 8px;">
      <div style="flex: 1; min-width: 130px; background: #1F2937; color: white; border-radius: 6px; padding: 10px; text-align: center; font-size: 12px;">
        <strong>DRIVE Thor</strong><br/><span style="opacity: 0.7; font-size: 10px;">Automotive SoC<br/>Automotive deployment</span>
      </div>
      <div style="flex: 1; min-width: 130px; background: #1F2937; color: white; border-radius: 6px; padding: 10px; text-align: center; font-size: 12px;">
        <strong>DRIVE Orin</strong><br/><span style="opacity: 0.7; font-size: 10px;">Current-gen SoC<br/>254 TOPS</span>
      </div>
      <div style="flex: 1; min-width: 130px; background: #1F2937; color: white; border-radius: 6px; padding: 10px; text-align: center; font-size: 12px;">
        <strong>H100 / A100</strong><br/><span style="opacity: 0.7; font-size: 10px;">Datacenter GPU<br/>Development & training</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

The three pillars of the Cosmos ecosystem that directly support Alpamayo are:

- **Cosmos World Foundation Model**: A generative video model that can synthesize photo-realistic driving scenarios. This provides infinite synthetic training data for rare edge cases (e.g., debris on highway, emergency vehicles, unusual weather), dramatically reducing the need for expensive real-world data collection.
- **Cosmos-Reason**: A vision-language model specifically pre-trained on physical world data — not just internet text and images, but video of objects moving, interacting, and obeying physical laws. This provides Alpamayo's VLM backbone with strong priors about how physical objects behave.
- **NVIDIA DRIVE Platform**: The end-to-end enterprise AV stack (software + hardware) into which Alpamayo is integrated. The DRIVE platform handles sensor interfaces, redundancy, safety monitoring, and integration with mapping, localization, and prediction modules.

---

## 📅 Version Release Timeline

<div style="overflow-x: auto;">

| Version | Release Date | Paper / Blog | Flagship Size | Dataset | Context | Headline Feature |
|:-------:|:----------:|:------------|:------------:|:--------------:|:------------:|:----------------|
| <img src="https://img.shields.io/badge/Alpamayo--R1-Dec_2025-059669" alt="Alpamayo-R1"/> | Dec 2025 | [arXiv (NeurIPS 2025)](https://arxiv.org/abs/2511.00088) | 7B (VLA) | 1,727 hrs / 2,500+ cities | Multi-cam video | First open-source reasoning VLA for AD |
| <img src="https://img.shields.io/badge/Alpamayo_1.5-Mar_2026-047857" alt="Alpamayo 1.5"/> | Mar 19, 2026 | [HF Model Card](https://huggingface.co/nvidia/Alpamayo-1.5-10B) · [GitHub](https://github.com/NVlabs/alpamayo1.5) | 10B (8.2B+2.3B) | 80,000 hrs / 25 countries | Multi-cam + egomotion + text | Cosmos-Reason2 backbone · Diffusion Expert · AlpaSim · promptable |

</div>

---

## 📊 Cross-Version Benchmark Comparison

> All numbers are for the Alpamayo-R1 and Alpamayo 1.5 releases. Open-loop: nuScenes planning benchmark. Closed-loop: AlpaSim. L2 Error = average displacement error. Sources: Alpamayo-R1 technical paper (NeurIPS 2025); Alpamayo 1.5 HF model card (Mar 2026).

| Benchmark / Metric | Alpamayo-0.5B | Alpamayo-3B | Alpamayo-7B | **Alpamayo 1.5 (10B)** |
|:-------------------|:---:|:---:|:---:|:---:|
| **L2 Error @ 2s (m)** | 0.42 | 0.31 | 0.21 | **0.18** |
| **L2 Error @ 6s (m)** | 1.87 | 1.43 | 0.98 | **0.82** |
| **Collision Rate (%)** | 2.1 | 1.4 | 0.8 | **0.5** |
| **Reasoning Quality (ROUGE-L)** | 0.41 | 0.53 | 0.67 | **0.74** |
| **Inference Latency (ms)** | 18 | 45 | 99 | **~120** |
| **VRAM Required** | ≥8 GB | ≥16 GB | ≥24 GB | **≥40 GB** |
| **Planning Frequency (Hz)** | 10 | 10 | 10 | **10** |
| **Trajectory Horizon (s)** | 6 | 6 | 6 | **6** |
| **AlpaSim Score** | — | — | — | **0.81 ± 0.01** |
| **minADE (m)** | — | — | — | **1.11** |
| **Training Data (hours)** | 1,727 | 1,727 | 1,727 | **80,000** |

### Comparison with Prior Autonomous Driving Models

| Model | Architecture Type | Open Source | Reasoning | Latency | L2 @ 2s |
|:------|:-----------------|:-----------:|:---------:|:-------:|:--------:|
| UniAD | Pure end-to-end DL | Partial | No | 300ms | 0.57m |
| DriveLM | VLM-based | Yes | Yes | 850ms | 0.38m |
| DriveVLM | VLM-based | No | Yes | 1200ms | 0.31m |
| **Alpamayo-R1 (7B)** | **VLA + Diffusion** | **Yes** | **Yes (CoC)** | **99ms** | **0.21m** |
| **Alpamayo 1.5 (10B)** | **VLA + Diffusion Expert** | **Yes** | **Yes (CoC v2)** | **~120ms** | **0.18m** |

<sub>Latency measured on NVIDIA A100 80GB. L2 error on nuScenes validation set open-loop planning. UniAD partial open-source refers to model weights without full training code. Alpamayo 1.5 latency measured on H100.</sub>

---

## 🏗️ Master Architecture Diagram

> This diagram shows the full Alpamayo-R1 system architecture — from raw surround-view cameras to vehicle trajectory — with color-coded components indicating their role in the perception → reasoning → action pipeline.

{::nomarkdown}
<div style="max-width: 780px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <!-- Title -->
  <div style="background: linear-gradient(135deg, #10B981 0%, #047857 100%); color: white; padding: 16px 24px; border-radius: 12px 12px 0 0; text-align: center; font-size: 18px; font-weight: 700; letter-spacing: 0.5px;">
    Alpamayo-R1 — Full VLA System Architecture
  </div>

  <div style="background: #F0FDF4; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 12px 12px; padding: 24px;">

    <!-- Legend -->
    <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 20px; font-size: 12px;">
      <span style="background: #065F46; color: white; padding: 3px 10px; border-radius: 12px;">📷 Perception</span>
      <span style="background: #047857; color: white; padding: 3px 10px; border-radius: 12px;">🧠 Reasoning (VLM)</span>
      <span style="background: #059669; color: white; padding: 3px 10px; border-radius: 12px;">💬 Chain of Causation</span>
      <span style="background: #10B981; color: white; padding: 3px 10px; border-radius: 12px;">🎯 Action (Diffusion)</span>
    </div>

    <!-- Stage 1: Cameras -->
    <div style="background: #065F46; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 8px;">
      <strong>📷 Stage 1 — Surround-View Camera Input</strong>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 6px; display: flex; flex-wrap: wrap; gap: 6px;">
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Front</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Front-Left</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Front-Right</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Back</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Back-Left</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">Back-Right</span>
        <span style="background: rgba(255,255,255,0.15); padding: 2px 8px; border-radius: 4px;">× Multiple timesteps</span>
      </div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #059669;">↓</div>

    <!-- Stage 2: Vision Encoder -->
    <div style="background: white; border: 2px solid #065F46; border-radius: 8px; padding: 14px; margin-bottom: 8px;">
      <div style="font-weight: 700; color: #065F46; margin-bottom: 8px;">📷 Multi-Camera Vision Encoder</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #065F46; color: white; padding: 3px 8px; border-radius: 4px;">Per-camera ViT patch encoding</span>
        <span style="background: #065F46; color: white; padding: 3px 8px; border-radius: 4px;">Temporal multi-frame fusion</span>
        <span style="background: #065F46; color: white; padding: 3px 8px; border-radius: 4px;">Cross-camera attention</span>
        <span style="background: #065F46; color: white; padding: 3px 8px; border-radius: 4px;">→ Visual token sequence</span>
      </div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #059669;">↓ visual tokens ↓</div>

    <!-- Stage 3: VLM Backbone -->
    <div style="border: 2px solid #047857; border-radius: 10px; padding: 16px; margin-bottom: 8px; background: white;">
      <div style="font-weight: 700; color: #047857; margin-bottom: 8px;">🧠 Cosmos-Reason VLM Backbone</div>
      <div style="text-align: center; font-size: 13px; font-weight: 600; color: #6B7280; margin-bottom: 12px;">× N TRANSFORMER LAYERS (0.5B / 3B / 7B)</div>

      <div style="background: #D1FAE5; border: 1px solid #6EE7B7; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 12px;">
        <strong>RMSNorm</strong> (Pre-Normalization)
      </div>

      <div style="border: 2px dashed #A7F3D0; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Self-Attention Block</div>
        <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
          <span style="background: #047857; color: white; padding: 3px 8px; border-radius: 4px;">RoPE Positional</span>
          <span style="background: #047857; color: white; padding: 3px 8px; border-radius: 4px;">GQA (visual + text tokens)</span>
          <span style="background: #047857; color: white; padding: 3px 8px; border-radius: 4px;">Causal Mask</span>
        </div>
      </div>

      <div style="text-align: center; font-size: 11px; color: #9CA3AF; margin-bottom: 6px;">+ Residual Connection</div>

      <div style="background: #D1FAE5; border: 1px solid #6EE7B7; padding: 8px 12px; border-radius: 6px; margin-bottom: 8px; font-size: 12px;">
        <strong>RMSNorm</strong> (Pre-Normalization)
      </div>

      <div style="border: 2px dashed #A7F3D0; border-radius: 8px; padding: 12px; margin-bottom: 8px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 8px;">Feed-Forward Block (SwiGLU)</div>
        <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
          <span style="background: #059669; color: white; padding: 3px 8px; border-radius: 4px;">SwiGLU Activation</span>
          <span style="background: #059669; color: white; padding: 3px 8px; border-radius: 4px;">Dense FFN</span>
        </div>
      </div>

      <div style="text-align: center; font-size: 11px; color: #9CA3AF;">+ Residual Connection</div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #059669;">↓ reasoning tokens ↓</div>

    <!-- Stage 4: Chain of Causation -->
    <div style="background: white; border: 2px solid #059669; border-radius: 8px; padding: 14px; margin-bottom: 8px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #059669; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ KEY INNOVATION</div>
      <div style="font-weight: 700; color: #059669; margin-bottom: 8px;">💬 Chain of Causation (CoC) Head</div>
      <div style="background: #ECFDF5; border-left: 4px solid #10B981; padding: 10px 14px; border-radius: 4px; font-size: 12px; font-style: italic; margin-bottom: 8px;">
        "I observe a pedestrian crossing at the intersection ahead → The traffic light is red → I must decelerate and prepare to stop → Trajectory: smooth braking to standstill within 15 meters"
      </div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px;">
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">Perception summary</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">Situation assessment</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">Action rationale</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 3px 8px; border-radius: 4px;">Trajectory intent</span>
      </div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #059669;">↓ CoC embeddings ↓</div>

    <!-- Stage 5: Diffusion Decoder -->
    <div style="background: white; border: 2px solid #10B981; border-radius: 8px; padding: 14px; margin-bottom: 8px; position: relative;">
      <div style="position: absolute; top: -10px; right: 12px; background: #10B981; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">✨ NOVEL DECODER</div>
      <div style="font-weight: 700; color: #065F46; margin-bottom: 8px;">🎯 Diffusion-Based Trajectory Decoder</div>
      <div style="display: flex; flex-wrap: wrap; gap: 6px; font-size: 12px; margin-bottom: 8px;">
        <span style="background: #10B981; color: white; padding: 3px 8px; border-radius: 4px;">DDPM-style denoising</span>
        <span style="background: #10B981; color: white; padding: 3px 8px; border-radius: 4px;">Conditioned on CoC features</span>
        <span style="background: #10B981; color: white; padding: 3px 8px; border-radius: 4px;">Multi-modal output</span>
        <span style="background: #10B981; color: white; padding: 3px 8px; border-radius: 4px;">6-second horizon @ 10Hz</span>
      </div>
      <div style="font-size: 11px; color: #6B7280;">
        Noise → denoising steps → (x, y, heading) waypoints × 60 timesteps
      </div>
    </div>
    <div style="text-align: center; font-size: 20px; color: #059669;">↓</div>

    <!-- Output -->
    <div style="background: #047857; color: white; padding: 12px 16px; border-radius: 8px; text-align: center;">
      <strong>🚗 Vehicle Trajectory Output</strong>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">
        <span style="background: rgba(255,255,255,0.2); padding: 2px 6px; border-radius: 4px; font-size: 10px;">60 waypoints (x, y, heading)</span>
        <span style="background: rgba(255,255,255,0.2); padding: 2px 6px; border-radius: 4px; font-size: 10px; margin-left: 4px;">6 seconds ahead</span>
        <span style="background: rgba(255,255,255,0.2); padding: 2px 6px; border-radius: 4px; font-size: 10px; margin-left: 4px;">→ Vehicle controller</span>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 🟢 Alpamayo-R1 — December 2025 — NeurIPS 2025

{::nomarkdown}
<div style="display: inline-block; background: #059669; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: December 2025 &nbsp;|&nbsp; 📄 <a href="https://arxiv.org/abs/2025.alpamayo" style="color: #D1FAE5;">arXiv (NeurIPS 2025)</a> &nbsp;|&nbsp; 💻 <a href="https://github.com/NVlabs/alpamayo" style="color: #D1FAE5;">NVlabs/alpamayo</a>
</div>
{:/nomarkdown}

### Summary

- **World's first open-source reasoning-driven Vision-Language-Action model for autonomous driving**, released by NVIDIA Research at NeurIPS 2025 — establishing a new paradigm that combines the interpretability of language reasoning with the action-generation capability of end-to-end neural planners
- Introduces **Chain of Causation (CoC)** — a novel reasoning paradigm that explicitly links perceptual observations to situation assessments to action rationales to trajectory predictions, going beyond reactive end-to-end prediction to produce fully interpretable driving decisions
- Built on **Cosmos-Reason**, NVIDIA's vision-language model pre-trained for physical world understanding, which provides a strong foundation for understanding physical causality — not just visual appearance — in driving scenarios
- Processes **surround-view camera input from 6 directions** (front, front-left, front-right, back, back-left, back-right) across **multiple timesteps**, giving the model complete 360° situational awareness and temporal context for understanding dynamic scenes
- Generates driving trajectories through a **diffusion-based decoder** that produces dynamically feasible 6-second waypoint sequences at 10Hz, handling multi-modal trajectory distributions (e.g., deciding whether to turn left or go straight at an ambiguous intersection) with principled probabilistic sampling
- Released at **three parameter scales** — 0.5B (edge deployment), 3B (efficient inference), 7B (best performance) — enabling deployment across the full range of automotive compute platforms from embedded DRIVE Orin to datacenter H100
- Achieves **industry-leading performance** on nuScenes autonomous driving benchmarks: 0.21m L2 error at 2 seconds and 0.8% collision rate for the 7B model, outperforming all prior open-source autonomous driving models while maintaining real-time 99ms latency
- **Open-sources both model weights and the Chain of Causation Dataset** — 1,727 hours of annotated driving data across 2,500+ cities worldwide — representing the largest openly released reasoning-annotated autonomous driving dataset to date
- Training pipeline incorporates **three stages**: (1) VLM pretraining on Cosmos-Reason for physical world priors, (2) supervised fine-tuning on Chain of Causation driving data, (3) reinforcement learning to align reasoning traces with trajectory quality — making it the first AV model to use RL for reasoning-action consistency
- Demonstrates **12× latency advantage over prior VLM-based planners** (99ms vs 1200ms for DriveVLM) while matching or exceeding their accuracy, achieved through architectural innovations in the diffusion decoder and efficient visual tokenization
- The **Chain of Causation reasoning traces are human-interpretable** and have been validated as useful for post-incident analysis and safety auditing — a critical requirement for regulatory approval of autonomous vehicles in major markets
- Designed for integration with the **NVIDIA DRIVE platform** including DRIVE Orin and the next-generation DRIVE Thor automotive compute platforms, with a quantization-aware training path targeting INT8 inference on embedded hardware

---

### Architecture Component 1: Multi-Camera, Multi-Timestep Vision Encoder

The vision encoder is the sensory front-end of Alpamayo, responsible for converting raw pixel data from a surround-view camera rig into a compact, semantically rich token sequence that the VLM backbone can reason over.

**Camera Configuration:**

A standard automotive sensor rig contains 6 cameras arranged to provide 360° coverage around the vehicle. Each camera covers a specific field of view and overlapping regions provide redundancy:
- **Front camera**: 120° FoV, primary forward perception, traffic light detection
- **Front-left / Front-right cameras**: ~70° FoV each, critical for intersection navigation and lane changes
- **Back camera**: 120° FoV, reversing and rear traffic awareness  
- **Back-left / Back-right cameras**: ~70° FoV each, blind spot monitoring and parking

**Temporal Encoding:**

Unlike a single-frame vision encoder, Alpamayo processes multiple consecutive timesteps of camera data. The default configuration processes 4 timesteps at 250ms intervals (spanning 1 second of history), giving the model crucial temporal context for:
- Estimating the velocity and acceleration of surrounding vehicles
- Understanding the trajectory of pedestrians and cyclists
- Detecting traffic light state changes
- Disambiguating stationary from slow-moving objects

**Visual Tokenization Pipeline:**

1. Each camera image (typically 1600×900 pixels) is divided into non-overlapping patches of 16×16 pixels, yielding 5,625 patches per camera image
2. A Vision Transformer (ViT) encoder processes each camera's patches independently, producing per-patch feature embeddings
3. A learnable **cross-camera attention module** allows features from different cameras to interact, enabling the model to reason about objects that appear in multiple camera views and about spatial relationships across the vehicle's full surround field
4. A **temporal attention module** aggregates features across timesteps, producing temporally-aware embeddings that encode how the scene has changed
5. The resulting multi-camera, multi-timestep features are flattened and projected into the VLM's token embedding space, producing a sequence of visual tokens consumed by the Cosmos-Reason backbone

This tokenization approach is inspired by the BEV (Bird's Eye View) representation paradigm popular in autonomous driving, but instead of explicitly constructing a spatial BEV map, the cross-camera attention implicitly learns to aggregate spatial information — a more flexible and scalable approach.

---

### Architecture Component 2: Cosmos-Reason VLM Backbone

The Cosmos-Reason VLM backbone is the cognitive core of Alpamayo — the component that transforms raw visual tokens into structured reasoning about the driving scene.

**Cosmos-Reason Pre-training:**

Cosmos-Reason is a vision-language model pre-trained by NVIDIA on a curated dataset of physical world video paired with causal explanations. Unlike standard VLMs trained on image-caption pairs from the internet, Cosmos-Reason's training data emphasizes:
- **Physical causality**: explanations of why objects move the way they do
- **Temporal dynamics**: understanding of motion, velocity, and trajectories
- **Spatial reasoning**: 3D layout understanding from 2D images
- **Predictive reasoning**: what will happen next given the current state

This pre-training produces a VLM with unusually strong priors about physical world behavior — a key advantage over adapting internet-trained LLMs (like LLaMA or Qwen) for driving applications.

**Architecture Details:**

The Cosmos-Reason backbone follows a standard transformer decoder architecture adapted for VLA:

- **Normalization**: RMSNorm with pre-normalization, consistent with modern LLM best practices
- **Attention**: Grouped Query Attention (GQA) for efficient KV cache usage during inference — critical for the 10Hz real-time planning requirement
- **Position encoding**: Rotary Positional Embeddings (RoPE) applied to text tokens; visual tokens use learned positional encodings with spatial bias
- **Feed-forward**: SwiGLU activation function, matching the configuration of Cosmos-Reason's pre-training
- **Token mixing**: Visual tokens and text tokens (including system prompt, map information, and prior CoC trace) are concatenated and processed jointly through the transformer layers

**Input Sequence Structure:**

The VLM backbone receives a mixed token sequence:
```
[System Prompt] [Map Context Tokens] [Visual Tokens (6 cams × T frames)] [Prior CoC Trace] → [CoC Output Tokens]
```

The system prompt encodes static context such as the ego vehicle's goal destination, speed limit, and current map lane graph. Map context tokens encode a compressed representation of the HD map around the vehicle, providing road topology information the model needs for planning.

---

### Architecture Component 3: Chain of Causation Reasoning Paradigm

Chain of Causation (CoC) is Alpamayo's most novel contribution — a structured reasoning paradigm specifically designed for physical AI systems that must make safety-critical decisions.

**Motivation: Why Reasoning Matters for Physical AI**

Traditional end-to-end autonomous driving models (like UniAD) make trajectory predictions directly from sensor inputs without any intermediate reasoning. This creates two critical problems:

1. **Black-box decisions**: When an end-to-end model makes an error (e.g., fails to yield to a pedestrian), it is nearly impossible to determine why — was it a perception failure? A planning failure? A representation issue? This makes debugging and safety auditing extremely difficult.

2. **Poor generalization to rare events**: End-to-end models struggle with long-tail scenarios (e.g., a mattress falling off a truck) because they can't leverage common-sense knowledge about what the object is and how to respond. A model that can reason "I see an unusual object blocking the lane → this may be road debris → I should slow down and change lanes" can handle such scenarios without having seen them in training.

Chain of Causation addresses both problems by requiring the model to produce an explicit, structured natural language trace of its reasoning before generating a trajectory.

**CoC Structure:**

Each Chain of Causation trace consists of four mandatory components:

1. **Perception Summary**: A natural language description of the current scene — what objects are present, where they are, and how they are moving.
   - *Example*: "I observe: (1) a red traffic light 40m ahead, (2) a cyclist in the right lane 15m ahead moving at ~15 km/h, (3) three vehicles stopped at the intersection, (4) clear left lane."

2. **Situation Assessment**: An interpretation of the current scene in terms of driving context — what are the relevant constraints, risks, and opportunities?
   - *Example*: "Situation: The red traffic light requires me to stop. The cyclist in the right lane is moving slower than me and may impede my path if I continue straight. The intersection is occupied and safe gap is unavailable."

3. **Action Rationale**: A causal explanation linking the situation assessment to the chosen driving action.
   - *Example*: "Decision: I should decelerate smoothly due to the red light. Stopping 3m behind the cyclist provides safe following distance. Changing lanes is unnecessary as I will stop regardless."

4. **Trajectory Intent**: A high-level description of the intended trajectory that the diffusion decoder will then realize in continuous waypoints.
   - *Example*: "Trajectory: Decelerate from 40 km/h to 0 km/h over 4 seconds, maintaining current lane, stopping 3m behind the cyclist."

**CoC vs LLM Chain-of-Thought:**

While superficially similar to LLM chain-of-thought (CoT) reasoning, CoC has important distinctions:

| Dimension | LLM Chain-of-Thought | Chain of Causation (CoC) |
|:----------|:---------------------|:------------------------|
| Purpose | Improve text answer accuracy | Ground trajectory prediction |
| Structure | Free-form | 4-part structured (P→S→A→T) |
| Grounding | Symbolic / abstract | Physical world causality |
| Output | Better text tokens | Better continuous waypoints |
| Evaluation | Answer correctness | Trajectory L2 error + collision rate |
| RL signal | Human preference | Trajectory quality + safety |

**CoC Training with Reinforcement Learning:**

A key insight in Alpamayo-R1 is that simply training the model to produce high-quality CoC traces (via SFT) is insufficient — the traces must also be **consistent with the subsequent trajectory**. A model could produce a plausible-sounding CoC trace but then generate a trajectory that doesn't follow its stated intent.

To address this, Alpamayo uses RL to train the model to maximize the consistency between its CoC trace and its trajectory prediction. The RL reward has two components:
- **Trajectory quality reward**: L2 error and collision rate against ground-truth expert trajectories
- **CoC-trajectory alignment reward**: A learned discriminator that scores how well the trajectory matches the stated intent in the CoC trace

This RL training stage is the third and final stage of Alpamayo's training pipeline and is critical for achieving the model's strong real-world performance.

{::nomarkdown}
<div style="max-width: 750px; margin: 2em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">

  <div style="background: #059669; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700; font-size: 15px;">
    Chain of Causation — Reasoning Flowchart
  </div>
  <div style="background: #ECFDF5; border: 2px solid #6EE7B7; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Step 1: Perception -->
    <div style="background: #065F46; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 4px;">
      <div style="font-weight: 700; font-size: 14px;">① PERCEPTION SUMMARY</div>
      <div style="font-size: 12px; opacity: 0.9; margin-top: 4px;">What objects are visible? Their positions, velocities, classifications</div>
      <div style="background: rgba(255,255,255,0.12); padding: 6px 10px; border-radius: 4px; margin-top: 6px; font-size: 11px; font-style: italic;">
        "Red traffic light 40m ahead. Cyclist in right lane 15m ahead at 15 km/h. 3 vehicles stopped at intersection."
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 20px; font-weight: 700; line-height: 1.2;">↓<br/><span style="font-size: 11px; color: #6B7280;">visual → scene understanding</span></div>

    <!-- Step 2: Situation -->
    <div style="background: #047857; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 4px;">
      <div style="font-weight: 700; font-size: 14px;">② SITUATION ASSESSMENT</div>
      <div style="font-size: 12px; opacity: 0.9; margin-top: 4px;">What does this scene mean? Constraints, risks, obligations, opportunities</div>
      <div style="background: rgba(255,255,255,0.12); padding: 6px 10px; border-radius: 4px; margin-top: 6px; font-size: 11px; font-style: italic;">
        "Must stop for red light. Cyclist ahead requires safe following gap. Intersection blocked — no safe gap available."
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 20px; font-weight: 700; line-height: 1.2;">↓<br/><span style="font-size: 11px; color: #6B7280;">scene → driving context</span></div>

    <!-- Step 3: Rationale -->
    <div style="background: #059669; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 4px;">
      <div style="font-weight: 700; font-size: 14px;">③ ACTION RATIONALE</div>
      <div style="font-size: 12px; opacity: 0.9; margin-top: 4px;">Why is this the correct action? Causal link from situation to decision</div>
      <div style="background: rgba(255,255,255,0.12); padding: 6px 10px; border-radius: 4px; margin-top: 6px; font-size: 11px; font-style: italic;">
        "Decelerate due to red light obligation. Maintain lane — no lane change needed since stopping. Stop 3m behind cyclist for safety."
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 20px; font-weight: 700; line-height: 1.2;">↓<br/><span style="font-size: 11px; color: #6B7280;">context → decision</span></div>

    <!-- Step 4: Trajectory Intent -->
    <div style="background: #10B981; color: white; padding: 12px 16px; border-radius: 8px; margin-bottom: 4px;">
      <div style="font-weight: 700; font-size: 14px;">④ TRAJECTORY INTENT</div>
      <div style="font-size: 12px; opacity: 0.9; margin-top: 4px;">High-level motion plan that the diffusion decoder will realize as waypoints</div>
      <div style="background: rgba(255,255,255,0.12); padding: 6px 10px; border-radius: 4px; margin-top: 6px; font-size: 11px; font-style: italic;">
        "Decelerate from 40 → 0 km/h over 4s, maintain current lane, stop 3m behind cyclist."
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 20px; font-weight: 700; line-height: 1.2;">↓<br/><span style="font-size: 11px; color: #6B7280;">intent → continuous action</span></div>

    <!-- Step 5: Diffusion Output -->
    <div style="background: white; border: 2px solid #10B981; border-radius: 8px; padding: 12px 16px;">
      <div style="font-weight: 700; color: #047857; font-size: 14px;">⑤ DIFFUSION TRAJECTORY DECODER</div>
      <div style="font-size: 12px; color: #374151; margin-top: 4px;">CoC embeddings condition the diffusion model → outputs 60 continuous (x, y, θ) waypoints</div>
      <div style="display: flex; gap: 4px; flex-wrap: wrap; margin-top: 8px;">
        <div style="background: #D1FAE5; color: #065F46; padding: 4px 8px; border-radius: 4px; font-size: 11px; text-align: center; flex: 1; min-width: 60px;">t=0.1s<br/>x₁,y₁,θ₁</div>
        <div style="background: #A7F3D0; color: #065F46; padding: 4px 8px; border-radius: 4px; font-size: 11px; text-align: center; flex: 1; min-width: 60px;">t=0.2s<br/>x₂,y₂,θ₂</div>
        <div style="background: #6EE7B7; color: #065F46; padding: 4px 8px; border-radius: 4px; font-size: 11px; text-align: center; flex: 1; min-width: 60px;">t=0.5s<br/>x₅,y₅,θ₅</div>
        <div style="color: #9CA3AF; display: flex; align-items: center; font-size: 14px;">...</div>
        <div style="background: #059669; color: white; padding: 4px 8px; border-radius: 4px; font-size: 11px; text-align: center; flex: 1; min-width: 60px;">t=6.0s<br/>x₆₀,y₆₀,θ₆₀</div>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

### Architecture Component 4: Diffusion-Based Trajectory Decoder

The diffusion-based trajectory decoder is the action output module that converts the VLM's reasoning embeddings into continuous vehicle trajectories. This is one of the most technically novel aspects of Alpamayo.

**Why Diffusion for Trajectory Prediction?**

Traditional autonomous driving planners use one of three approaches for trajectory generation:
1. **Regression head**: Directly predict waypoints as the mean of a Gaussian — fails to capture multi-modal distributions (e.g., at a fork in the road)
2. **Classification over anchors**: Discretize the action space and classify over pre-defined trajectory templates — loses precision and fails for unseen road geometries
3. **Sampling from a learned distribution**: The principled approach — model the full distribution over possible trajectories

Diffusion models provide an elegant solution to the third approach. By learning to denoise Gaussian noise into feasible trajectories (conditioned on scene context and CoC reasoning), the diffusion decoder:
- Naturally captures **multi-modal distributions** — different modes correspond to different driving decisions (turn left vs. go straight)
- Generates **dynamically feasible trajectories** — because the training data consists of real vehicle trajectories, the diffusion model learns the kinematic constraints implicitly
- Provides **uncertainty quantification** — by running multiple denoising chains, the model can produce multiple trajectory samples, with the spread indicating uncertainty
- Allows **conditioning on CoC reasoning** — the CoC trace embeddings serve as a conditioning signal that steers the diffusion process toward trajectories consistent with the stated intent

**Diffusion Decoder Architecture:**

The trajectory diffusion decoder follows the DDPM (Denoising Diffusion Probabilistic Model) framework with modifications for real-time inference:

1. **Trajectory representation**: A trajectory is represented as a sequence of 60 waypoints, each consisting of (x, y, heading θ) in ego-vehicle coordinates, covering 6 seconds at 10Hz
2. **Forward process**: During training, clean ground-truth trajectories are corrupted by adding Gaussian noise over T=100 timesteps: `τ_t = √(ᾱ_t) τ_0 + √(1-ᾱ_t) ε`
3. **Reverse process (denoising)**: A learned U-Net-style denoiser predicts the noise `ε_θ(τ_t, t, c)` at each diffusion step, where `c` are the conditioning features from the CoC embeddings
4. **Conditioning**: The CoC feature vector (extracted from the last hidden state of the VLM backbone's final layer) is injected into every layer of the denoiser via cross-attention — ensuring trajectory generation is fully conditioned on the model's reasoning

**Real-Time Inference via DDIM:**

The standard DDPM reverse process requires 100 denoising steps, which would be far too slow for real-time planning. Alpamayo uses **DDIM (Denoising Diffusion Implicit Models)** sampling, which achieves comparable quality with only 10 denoising steps — reducing the diffusion decoder's latency contribution from ~90ms to ~9ms.

The total 99ms inference budget for the 7B model breaks down approximately as:
- Vision encoder: ~25ms
- VLM backbone (autoregressive CoC generation): ~55ms
- Diffusion decoder (10 DDIM steps): ~9ms
- Pre/post processing: ~10ms

**Trajectory Feasibility:**

The diffusion decoder is trained with an auxiliary **kinematic feasibility loss** that penalizes trajectories violating vehicle physics constraints:
- Maximum acceleration and deceleration limits
- Maximum steering angle (curvature) constraints
- Maximum jerk (rate of change of acceleration) for passenger comfort
- Minimum stopping distance based on current velocity

This ensures that even under distribution shift (unusual road scenarios), the generated trajectories remain physically executable by the vehicle's low-level controller.

---

### Training Pipeline

Alpamayo-R1 is trained in three sequential stages, each building on the previous:

{::nomarkdown}
<div style="max-width: 750px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #047857; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Alpamayo-R1 — Three-Stage Training Pipeline
  </div>
  <div style="background: #ECFDF5; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <div style="display: flex; gap: 6px; flex-wrap: wrap; align-items: stretch;">

      <!-- Stage 1 -->
      <div style="flex: 1; min-width: 160px; background: #065F46; color: white; padding: 14px; border-radius: 8px; text-align: center;">
        <div style="font-size: 22px; margin-bottom: 4px;">①</div>
        <div style="font-weight: 700; margin-bottom: 6px; font-size: 13px;">VLM Pre-training</div>
        <div style="font-size: 11px; opacity: 0.85; margin-bottom: 8px;">Cosmos-Reason base model trained on physical world video + causal text pairs</div>
        <div style="font-size: 10px; background: rgba(255,255,255,0.15); padding: 3px 6px; border-radius: 3px;">Physical world priors</div>
      </div>

      <div style="display: flex; align-items: center; color: #059669; font-size: 20px; font-weight: 900; flex-shrink: 0;">→</div>

      <!-- Stage 2 -->
      <div style="flex: 1; min-width: 160px; background: #047857; color: white; padding: 14px; border-radius: 8px; text-align: center;">
        <div style="font-size: 22px; margin-bottom: 4px;">②</div>
        <div style="font-weight: 700; margin-bottom: 6px; font-size: 13px;">SFT on CoC Driving Data</div>
        <div style="font-size: 11px; opacity: 0.85; margin-bottom: 8px;">Supervised fine-tuning on Chain of Causation Dataset: reasoning traces + trajectory pairs</div>
        <div style="font-size: 10px; background: rgba(255,255,255,0.15); padding: 3px 6px; border-radius: 3px;">1,727 hrs / 2,500+ cities</div>
      </div>

      <div style="display: flex; align-items: center; color: #059669; font-size: 20px; font-weight: 900; flex-shrink: 0;">→</div>

      <!-- Stage 3 -->
      <div style="flex: 1; min-width: 160px; background: #059669; color: white; padding: 14px; border-radius: 8px; text-align: center; position: relative;">
        <div style="position: absolute; top: -8px; right: 8px; background: #34D399; color: #065F46; padding: 2px 6px; border-radius: 4px; font-size: 9px; font-weight: 700;">KEY</div>
        <div style="font-size: 22px; margin-bottom: 4px;">③</div>
        <div style="font-weight: 700; margin-bottom: 6px; font-size: 13px;">RL for CoC-Trajectory Alignment</div>
        <div style="font-size: 11px; opacity: 0.85; margin-bottom: 8px;">Reward: trajectory quality + CoC-action consistency discriminator</div>
        <div style="font-size: 10px; background: rgba(255,255,255,0.15); padding: 3px 6px; border-radius: 3px;">L2 error + collision avoidance</div>
      </div>

    </div>

    <div style="margin-top: 14px; background: white; border: 1px solid #A7F3D0; border-radius: 6px; padding: 12px; font-size: 12px; color: #374151;">
      <strong style="color: #047857;">RL Reward Function:</strong> R = λ₁ · R_trajectory + λ₂ · R_alignment<br/>
      <span style="color: #6B7280;">R_trajectory = -(L2_error) - α·(collision_rate) &nbsp;|&nbsp; R_alignment = D(CoC_embedding, trajectory) via learned discriminator</span>
    </div>

  </div>
</div>
{:/nomarkdown}

**Stage 1 — Cosmos-Reason VLM Pre-training:**

Before any driving-specific training, the Cosmos-Reason backbone is pre-trained by NVIDIA on a massive dataset of physical world video paired with causal explanations. This stage is not performed by researchers reproducing Alpamayo — the Cosmos-Reason weights are provided as a pre-trained checkpoint. The pre-training emphasizes:
- Object permanence and physical continuity across video frames
- Causal relationships between events ("the ball fell because it was hit")
- Motion prediction ("the car will turn left because its blinker is on")
- Spatial layout reasoning from monocular images

**Stage 2 — Supervised Fine-Tuning on CoC Driving Data:**

In Stage 2, the full Alpamayo model (VLM backbone + vision encoder + diffusion decoder) is jointly trained on the Chain of Causation Dataset. For each training example:
- **Input**: 6-camera multi-frame image sequence + HD map context + system prompt
- **Target (VLM)**: The 4-part Chain of Causation reasoning trace (annotated by expert human drivers with LLM assistance)
- **Target (Diffusion)**: The ground-truth expert trajectory driven by the human

The vision encoder and diffusion decoder are trained from scratch in this stage; the VLM backbone is fine-tuned from the Cosmos-Reason checkpoint. Training uses a combined loss: cross-entropy on CoC tokens + diffusion DDPM loss on trajectory.

**Stage 3 — Reinforcement Learning for Reasoning-Action Consistency:**

Stage 3 uses proximal policy optimization (PPO) to refine the model's behavior based on reward signals that capture both trajectory quality and the alignment between stated reasoning and executed trajectory. The key insight motivating this stage is that SFT alone trains the model to produce plausible-looking reasoning and plausible trajectories separately — but doesn't enforce that they must be consistent with each other.

The RL stage starts from the Stage 2 checkpoint and optimizes:
1. **Trajectory reward**: Measured against withheld expert trajectories using L2 displacement error and collision rate
2. **Alignment reward**: A separately trained discriminator model that takes (CoC text, trajectory) pairs and scores how consistent they are — high score when the trajectory matches the stated intent, low score when they contradict

---

### Chain of Causation Dataset

The Chain of Causation Dataset is a major research contribution released alongside the Alpamayo-R1 model weights.

**Scale and Coverage:**

| Attribute | Value |
|:----------|:-----:|
| Total Duration | 1,727 hours |
| Number of Cities | 2,500+ |
| Countries Covered | 40+ |
| Camera Views per Clip | 6 (surround-view) |
| Annotation Type | CoC reasoning trace + trajectory |
| Annotation Method | Expert annotation + LLM assistance + human review |
| Clip Duration | 8–30 seconds |
| Frame Rate | 10 Hz (annotations) |
| Ego Trajectory Format | (x, y, heading) in ego coordinates |
| License | Research non-commercial |

**Geographic Diversity:**

The dataset was specifically curated to cover a wide range of driving environments:
- **Urban environments**: Dense city centers, complex intersections, pedestrian zones
- **Suburban environments**: Residential streets, school zones, parking lots
- **Highway environments**: Merging, lane changes, high-speed following
- **Rural environments**: Country roads, gravel paths, unmarked intersections
- **Adverse conditions**: Rain, fog, night driving, glare, construction zones
- **Geographic diversity**: North America, Europe, Asia, Middle East — covering right-hand and left-hand traffic

**Annotation Pipeline:**

Raw driving data was collected by NVIDIA's fleet of research vehicles equipped with 6-camera surround-view rigs. The annotation pipeline:

1. **Automated pre-annotation**: A large VLM (GPT-4V) generates initial CoC traces for each clip based on the camera images and ego trajectory
2. **Expert review and correction**: Human expert drivers review and correct the automated annotations, focusing on safety-critical scenarios where the LLM may have made errors
3. **Quality filtering**: Clips where human reviewers disagreed are escalated for consensus or discarded — only high-agreement annotations are included
4. **Diversity sampling**: Systematic sampling ensures geographic, scenario-type, and weather-condition diversity — preventing over-representation of common easy scenarios

**Rare Event Augmentation:**

A known challenge in autonomous driving datasets is the underrepresentation of rare but safety-critical events (near-misses, emergency vehicle encounters, unusual road debris). The dataset includes a dedicated "long-tail" subset of 127 hours of such events, collected through targeted data collection campaigns and augmented using Cosmos World Foundation Model synthetic generation.

---

### Deployment & Hardware Requirements

Alpamayo-R1 is designed for deployment on NVIDIA hardware across a range of compute tiers:

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #047857; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Deployment Hardware Requirements
  </div>
  <div style="background: #F0FDF4; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <div style="display: flex; flex-direction: column; gap: 10px;">

      <!-- 0.5B row -->
      <div style="background: white; border: 1px solid #6EE7B7; border-radius: 8px; padding: 12px 16px; display: flex; flex-wrap: wrap; gap: 12px; align-items: center;">
        <div style="background: #065F46; color: white; padding: 6px 12px; border-radius: 6px; font-weight: 700; font-size: 13px; white-space: nowrap;">0.5B</div>
        <div style="flex: 1; min-width: 200px;">
          <div style="font-size: 12px; color: #374151;"><strong>VRAM:</strong> ≥8 GB &nbsp;|&nbsp; <strong>Latency:</strong> 18ms &nbsp;|&nbsp; <strong>Target:</strong> DRIVE Orin embedded</div>
          <div style="font-size: 11px; color: #6B7280; margin-top: 3px;">Suitable for edge deployment. INT8 quantization reduces to ≥4 GB VRAM. Ideal for ADAS (L2/L3).</div>
        </div>
      </div>

      <!-- 3B row -->
      <div style="background: white; border: 1px solid #34D399; border-radius: 8px; padding: 12px 16px; display: flex; flex-wrap: wrap; gap: 12px; align-items: center;">
        <div style="background: #047857; color: white; padding: 6px 12px; border-radius: 6px; font-weight: 700; font-size: 13px; white-space: nowrap;">3B</div>
        <div style="flex: 1; min-width: 200px;">
          <div style="font-size: 12px; color: #374151;"><strong>VRAM:</strong> ≥16 GB &nbsp;|&nbsp; <strong>Latency:</strong> 45ms &nbsp;|&nbsp; <strong>Target:</strong> DRIVE Thor, RTX 4090</div>
          <div style="font-size: 11px; color: #6B7280; margin-top: 3px;">Balance of performance and efficiency. Suitable for robotaxi development platforms. INT4 path available.</div>
        </div>
      </div>

      <!-- 7B row -->
      <div style="background: white; border: 2px solid #059669; border-radius: 8px; padding: 12px 16px; display: flex; flex-wrap: wrap; gap: 12px; align-items: center; position: relative;">
        <div style="position: absolute; top: -9px; right: 12px; background: #059669; color: white; padding: 2px 8px; border-radius: 4px; font-size: 10px; font-weight: 600;">RECOMMENDED</div>
        <div style="background: #059669; color: white; padding: 6px 12px; border-radius: 6px; font-weight: 700; font-size: 13px; white-space: nowrap;">7B</div>
        <div style="flex: 1; min-width: 200px;">
          <div style="font-size: 12px; color: #374151;"><strong>VRAM:</strong> ≥24 GB &nbsp;|&nbsp; <strong>Latency:</strong> 99ms &nbsp;|&nbsp; <strong>Target:</strong> A100 / H100 datacenter, future DRIVE</div>
          <div style="font-size: 11px; color: #6B7280; margin-top: 3px;">Best performance. Industry-level accuracy. Recommended for production robotaxi / Level 4+ development.</div>
        </div>
      </div>

    </div>

    <div style="margin-top: 14px; background: #D1FAE5; border-radius: 6px; padding: 10px 14px; font-size: 12px; color: #065F46;">
      <strong>Quantization:</strong> All variants support FP16 (default), BF16, INT8 (via TensorRT), and INT4 (via TensorRT-LLM). INT8 reduces VRAM requirements by ~2× with ≤5% performance degradation. INT4 reduces by ~4× with ≤10% degradation — suitable for embedded automotive chips.
    </div>

  </div>
</div>
{:/nomarkdown}

**Software Requirements:**

```
CUDA >= 12.1
Python >= 3.10
PyTorch >= 2.2.0
transformers >= 4.40.0
diffusers >= 0.27.0
NVIDIA TensorRT >= 9.0 (for optimized inference)
```

**Inference Optimization:**

For production deployment, NVIDIA provides TensorRT engine conversion scripts in the NVlabs/alpamayo repository. Key optimizations:

1. **Vision encoder**: Compiled to TensorRT with static input shapes (per camera configuration)
2. **VLM backbone**: Quantized to INT8 using TensorRT-LLM with KV cache management for the CoC generation loop
3. **Diffusion decoder**: The 10 DDIM denoising steps are compiled as a single fused TensorRT graph, avoiding Python overhead per step
4. **End-to-end pipeline**: CUDA graphs capture the full forward pass, minimizing CPU-GPU synchronization overhead

With all optimizations applied on NVIDIA DRIVE Thor, the 3B model achieves <50ms end-to-end latency, comfortably meeting the 100ms real-time planning requirement.

---

### Architecture Diagram — Alpamayo-R1 Full System

{::nomarkdown}
<div style="max-width: 700px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: #059669; color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700;">
    Alpamayo-R1 — Component Detail with Data Flow
  </div>
  <div style="background: #ECFDF5; border: 2px solid #A7F3D0; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Input cameras -->
    <div style="background: #EDE9FE; border: 1px solid #C4B5FD; padding: 10px; border-radius: 6px; text-align: center; margin-bottom: 6px; font-size: 12px;">
      <strong>6 Camera Images</strong> (1600×900) × <strong>4 timesteps</strong> = 24 frames per inference step
    </div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓</div>

    <!-- Vision Encoder -->
    <div style="background: white; border: 2px solid #065F46; border-radius: 8px; padding: 14px; margin-bottom: 6px; font-size: 12px;">
      <div style="font-weight: 700; color: #065F46; margin-bottom: 8px;">📷 Multi-Camera Vision Encoder</div>
      <div style="display: flex; flex-wrap: wrap; gap: 5px;">
        <span style="background: #065F46; color: white; padding: 3px 7px; border-radius: 4px;">ViT patch encoder (per cam)</span>
        <span style="background: #065F46; color: white; padding: 3px 7px; border-radius: 4px;">Temporal attention (4 frames)</span>
        <span style="background: #065F46; color: white; padding: 3px 7px; border-radius: 4px;">Cross-camera attention</span>
        <span style="background: #065F46; color: white; padding: 3px 7px; border-radius: 4px;">Linear projector → VLM dim</span>
      </div>
      <div style="font-size: 11px; color: #6B7280; margin-top: 6px;">Output: ~2,048 visual tokens per inference</div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 12px;">visual tokens + HD map tokens + system prompt tokens</div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓</div>

    <!-- VLM Backbone -->
    <div style="border: 2px solid #047857; border-radius: 10px; padding: 16px; margin-bottom: 6px; background: white;">
      <div style="text-align: center; font-size: 13px; font-weight: 600; color: #6B7280; margin-bottom: 10px;">COSMOS-REASON VLM BACKBONE — × N LAYERS (0.5B / 3B / 7B)</div>

      <div style="background: #D1FAE5; border: 1px solid #6EE7B7; padding: 7px 12px; border-radius: 5px; margin-bottom: 6px; font-size: 12px;">
        <strong>RMSNorm</strong> (Pre-Norm) — matching Cosmos-Reason pre-training config
      </div>
      <div style="border: 2px dashed #A7F3D0; border-radius: 7px; padding: 10px; margin-bottom: 6px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 6px;">Grouped Query Attention (GQA)</div>
        <div style="display: flex; flex-wrap: wrap; gap: 5px; font-size: 11px;">
          <span style="background: #047857; color: white; padding: 2px 7px; border-radius: 4px;">RoPE (text tokens)</span>
          <span style="background: #047857; color: white; padding: 2px 7px; border-radius: 4px;">Learned pos. embed (visual tokens)</span>
          <span style="background: #047857; color: white; padding: 2px 7px; border-radius: 4px;">Causal mask</span>
        </div>
      </div>
      <div style="text-align: center; font-size: 11px; color: #9CA3AF; margin-bottom: 4px;">+ Residual → RMSNorm</div>
      <div style="border: 2px dashed #A7F3D0; border-radius: 7px; padding: 10px; margin-bottom: 4px;">
        <div style="font-size: 12px; font-weight: 600; color: #374151; margin-bottom: 6px;">SwiGLU Feed-Forward</div>
        <div style="display: flex; flex-wrap: wrap; gap: 5px; font-size: 11px;">
          <span style="background: #059669; color: white; padding: 2px 7px; border-radius: 4px;">SwiGLU activation</span>
          <span style="background: #059669; color: white; padding: 2px 7px; border-radius: 4px;">Dense FFN</span>
        </div>
      </div>
      <div style="text-align: center; font-size: 11px; color: #9CA3AF;">+ Residual Connection</div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 12px;">CoC text tokens (autoregressive) + final hidden states</div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓</div>

    <!-- Dual output heads -->
    <div style="display: flex; gap: 10px; flex-wrap: wrap; margin-bottom: 6px;">
      <!-- CoC Text Head -->
      <div style="flex: 1; min-width: 200px; background: white; border: 2px solid #059669; border-radius: 8px; padding: 12px;">
        <div style="font-weight: 700; color: #059669; font-size: 12px; margin-bottom: 6px;">💬 CoC Text Head (LM Head)</div>
        <div style="font-size: 11px; color: #374151;">Linear + Softmax → next CoC token<br/>Generates 4-part reasoning trace autoregressively</div>
      </div>
      <!-- Diffusion Head -->
      <div style="flex: 1; min-width: 200px; background: white; border: 2px solid #10B981; border-radius: 8px; padding: 12px;">
        <div style="font-weight: 700; color: #065F46; font-size: 12px; margin-bottom: 6px;">🎯 Diffusion Decoder</div>
        <div style="font-size: 11px; color: #374151;">U-Net denoiser conditioned on CoC features<br/>10 DDIM steps → 60 trajectory waypoints</div>
      </div>
    </div>
    <div style="text-align: center; color: #059669; font-size: 18px;">↓</div>

    <!-- Output -->
    <div style="background: #047857; color: white; padding: 12px 16px; border-radius: 8px; text-align: center; font-size: 13px;">
      <strong>🚗 Output: CoC Reasoning Text + 60-point Trajectory (x, y, θ) over 6s @ 10Hz</strong>
    </div>

  </div>
</div>
{:/nomarkdown}

---

### Chain of Causation Flowchart

> See the [Chain of Causation Reasoning Paradigm](#architecture-component-3-chain-of-causation-reasoning-paradigm) section above for the full reasoning flowchart diagram.

---

### Community Perspective

- **Landmark open-source release**: The AV research community received Alpamayo-R1 as the most significant open-source release in autonomous driving since nuScenes, combining a strong model with a large annotated dataset — lowering the barrier to entry for academic VLA research dramatically
- **Chain of Causation as a new standard**: Many in the safety community argued that CoC-style explicit reasoning should be a regulatory requirement for autonomous vehicles, and Alpamayo-R1 was cited as the first production-viable demonstration that real-time explicit reasoning is achievable (99ms)
- **Skepticism about generalization**: Critics noted that nuScenes is a relatively controlled benchmark and that real-world deployment requires testing across a far wider distribution of scenarios, adverse weather conditions, and regulatory environments — the 2,500 cities dataset helps but doesn't fully address this concern
- **NVIDIA ecosystem lock-in debate**: Some in the open-source community noted that full performance requires NVIDIA hardware (TensorRT optimization, DRIVE platform integration), raising questions about portability to other automotive silicon vendors
- **Diffusion decoder as a breakthrough**: Robotics researchers outside AV were particularly excited about the diffusion trajectory decoder as a technique applicable to robot manipulation, drone navigation, and other physical AI domains — Alpamayo's approach was quickly adapted in subsequent robotics papers
- **Dataset quality praised**: Independent evaluators noted that the CoC dataset's annotation methodology — combining LLM pre-annotation with human expert review — struck a good balance between scale and annotation quality, and the 127-hour long-tail subset was specifically called out as a major contribution
- **RL training transparency**: The paper's detailed description of the RL training stage for CoC-trajectory alignment was praised as one of the first transparent accounts of how to train VLA models with RL — filling a significant gap in the literature

---

### Model Variants

| Model | Parameters | VLM Backbone | Vision Encoder | VRAM (FP16) | Latency (A100) | Target Platform | License |
|:------|:----------:|:------------:|:--------------:|:-----------:|:--------------:|:---------------:|:-------:|
| Alpamayo-0.5B | 0.5B | Cosmos-Reason-0.5B | ViT-S/16 | 8 GB | 18ms | DRIVE Orin, edge | Non-commercial |
| Alpamayo-3B | 3B | Cosmos-Reason-3B | ViT-B/16 | 16 GB | 45ms | DRIVE Thor, RTX 4090 | Non-commercial |
| Alpamayo-7B | 7B | Cosmos-Reason-7B | ViT-L/16 | 24 GB | 99ms | A100 / H100 | Non-commercial |

> All variants share the same diffusion trajectory decoder architecture and Chain of Causation reasoning structure. The differences are in the VLM backbone size and vision encoder capacity. All variants output 60-waypoint trajectories at 10Hz over a 6-second planning horizon.

---

### Key Industry Ideas Incorporated

<details>
<summary><strong>Key Industry Ideas Incorporated</strong></summary>

| Technique | Origin | How Alpamayo-R1 Used It |
|:----------|:-------|:------------------------|
| Diffusion Trajectory Prediction | Ho et al. DDPM (NeurIPS 2020); Song et al. DDIM (ICLR 2021) | DDPM-style denoiser for trajectory generation; DDIM for 10-step real-time inference |
| Vision-Language Models for Driving | DriveLM (CVPR 2024); DriveVLM (2024) | Extended VLM approach with diffusion action head rather than text trajectory output |
| Chain-of-Thought Reasoning | Wei et al. (NeurIPS 2022) | Adapted as Chain of Causation with physical causality structure for driving decisions |
| Reinforcement Learning for Reasoning | DeepSeek-R1 (2025); OpenAI o1 (2024) | RL to align VLM reasoning traces with trajectory prediction quality |
| Grouped Query Attention (GQA) | Ainslie et al. EMNLP 2023 | GQA in VLM backbone for efficient KV cache management during real-time CoC generation |
| RoPE | Su et al. RoFormer (2021) | Positional encoding for text tokens in the VLM backbone |
| SwiGLU | Dauphin et al. (2017); PaLM (2022) | Feed-forward activation in Cosmos-Reason backbone |
| RMSNorm | Zhang & Sennrich (2019) | Pre-normalization for stable training of the VLM backbone |
| Surround-View Camera Fusion | BEVFormer (ECCV 2022); Tesla FSD | Cross-camera attention for 360° scene understanding without explicit BEV projection |
| End-to-End Autonomous Driving | UniAD (CVPR 2023) | Inspiration for unified perception-to-planning pipeline; CoC adds interpretability layer |
| ViT Vision Encoder | Dosovitskiy et al. ICLR 2021 | Per-camera patch encoding in the vision encoder |
| DDIM Sampling | Song et al. ICLR 2021 | Accelerates diffusion decoder from 100 to 10 steps for real-time inference |
| Upcycling / Transfer Learning | Standard practice | Cosmos-Reason weights initialized from broader physical AI pre-training |
| PPO | Schulman et al. (2017) | Proximal Policy Optimization for Stage 3 RL training |

</details>

---

## 🌿 Alpamayo 1.5 — March 2026

<div style="display: inline-block; background: #047857; color: white; padding: 4px 14px; border-radius: 20px; font-size: 13px; font-weight: 600; margin-bottom: 12px;">
  📅 Released: March 19, 2026 &nbsp;|&nbsp; 🤗 <a href="https://huggingface.co/nvidia/Alpamayo-1.5-10B" style="color: #A7F3D0;">nvidia/Alpamayo-1.5-10B</a> &nbsp;|&nbsp; 💻 <a href="https://github.com/NVlabs/alpamayo1.5" style="color: #A7F3D0;">NVlabs/alpamayo1.5</a>
</div>

### Summary

- **Major scale-up**: Alpamayo 1.5 replaces the monolithic 7B model with a **dual-module architecture**: a **Cosmos-Reason2 VLM backbone** (8.2B parameters) for scene understanding and causal reasoning, paired with a dedicated **Diffusion Expert** (2.3B parameters) for trajectory generation — totalling ~10.5B parameters
- **Cosmos-Reason2 backbone**: The upgraded VLM backbone is specifically designed for **physical AI reasoning** — trained on a mixture of driving footage, scientific documents, and simulation outputs to understand causality in the physical world beyond just language patterns
- **Dedicated Diffusion Expert**: Unlike R1's shared backbone approach, the 2.3B diffusion expert is a standalone module conditioned solely on the Cosmos-Reason2 latent, enabling independent scaling and optimization of the action generation component
- **Promptable text conditioning**: Operators can now modulate model behavior at runtime via free-text prompts — e.g., *"drive more conservatively in fog"*, *"maintain 3-second following distance"*, *"prefer the right lane"* — without retraining; prompts are encoded into the Cosmos-Reason2 conditioning token stream
- **AlpaSim integration**: Alpamayo 1.5 is the first version to be natively evaluated in **AlpaSim**, NVIDIA's open-source closed-loop simulation framework; AlpaSim Score of **0.81 ± 0.01** and minADE of **1.11m** are the primary reported metrics
- **80,000-hour training dataset**: Training data expanded from 1,727 hours to **80,000 hours** of multi-camera annotated driving data across **25 countries**, with over **1 billion images** and **3 million reasoning annotations** — a ~46× expansion
- **PhysicalAI Open Datasets**: Released in parallel with Alpamayo 1.5 at CES 2026; provides a large, openly licensed benchmark dataset for AV research including driving from 25 countries with diverse road conditions, weather, and traffic patterns
- **Egomotion conditioning**: The model now accepts **ego-motion history** (past vehicle speed, steering, acceleration) alongside camera frames — providing temporal kinematic context that helps with trajectory continuity and smoothness
- **Navigation instruction conditioning**: Natural language navigation goals (e.g., *"turn right at the next intersection"*, *"take the highway on-ramp"*) are accepted as an additional input modality, enabling goal-conditioned trajectory planning
- **Reasoning trace v2 (Chain of Causation v2)**: The reasoning traces are longer, more structured, and now include **counterfactual reasoning** — e.g., *"if the pedestrian had continued walking, I would have braked harder"* — improving safety auditability
- **AlpaSim simulation framework**: An open-source companion tool released alongside 1.5 for closed-loop simulation, capable of reproducing difficult driving scenarios at scale for continuous model validation and safety testing
- **Improved long-tail handling**: The combination of larger training data, AlpaSim synthetic augmentation, and counterfactual reasoning in CoC v2 dramatically improves behavior on rare edge cases (construction zones, emergency vehicles, adverse weather, unusual road markings)
- **Hardware requirements**: The dual-module 10B architecture targets **H100 / A100 80GB** in FP16; INT8 quantized variants support deployment on **DRIVE Thor** with ~60ms latency at 10Hz

---

### Architecture: Cosmos-Reason2 VLM Backbone

Cosmos-Reason2 is an 8.2B parameter vision-language transformer purpose-built for **physical AI reasoning**. Unlike generic VLMs (e.g., LLaVA, InternVL), Cosmos-Reason2 is pre-trained on:
- **Driving footage** with annotated physical events (collisions, near-misses, right-of-way scenarios)
- **Physics simulation outputs** from Isaac Sim and Omniverse
- **Scientific documents** covering vehicle dynamics, road physics, traffic engineering
- **Chain of Causation v1 traces** from Alpamayo-R1 (self-distillation)

Key architectural properties:
- **Grouped Query Attention** (GQA): 32 Q heads, 8 KV heads for memory-efficient KV cache during long reasoning trace generation
- **RoPE** (θ = 500,000) for extended context (up to 32K tokens to handle long reasoning traces + image tokens)
- **SwiGLU** FFN activation
- **RMSNorm** (pre-normalization)
- Vision encoder: upgraded **SigLIP ViT-H/14** (1B parameters) — processes 6-camera frames at 448×448 resolution each, producing 256 tokens per camera (1,536 visual tokens total per timestep)
- Temporal fusion: 3 past timesteps stacked → 4,608 total visual tokens input to reasoning backbone

---

### Architecture: Diffusion Expert Trajectory Decoder

The 2.3B **Diffusion Expert** is a standalone denoising transformer conditioned on Cosmos-Reason2's output embedding. Key details:

- **Architecture**: Diffusion Transformer (DiT-style) — 24 transformer layers with cross-attention to Cosmos-Reason2 conditioning tokens
- **Conditioning**: Receives (a) Cosmos-Reason2 scene embedding, (b) egomotion history tokens, (c) navigation goal tokens, (d) text prompt tokens (when provided)
- **Noise schedule**: DDIM with **10 inference steps** (down from DDPM 100 steps in R1) — critical for real-time ≤120ms latency
- **Output**: 60 waypoints × (x, y, heading) = 180-dimensional trajectory vector over 6 seconds at 10Hz
- **Multi-modal output**: Samples **8 candidate trajectories** per inference; final trajectory selected by minimum cost under a safety-aware cost function (collision probability, comfort, deviation from navigation goal)
- **Physically constrained decoding**: Vehicle kinematic model (bicycle model) applied as post-processing to ensure all sampled trajectories are physically realizable given current speed and steering limits

---

### AlpaSim Simulation Framework

AlpaSim is an open-source closed-loop simulation environment released alongside Alpamayo 1.5:

- **Scenario reproduction**: Automatically reconstructs challenging real-world scenarios from the PhysicalAI dataset for repeatable testing
- **Adversarial agents**: Configurable adversarial vehicles and pedestrians to stress-test edge case handling
- **Metrics**: AlpaSim Score (composite safety + comfort + goal achievement metric), minADE, collision rate, traffic violation rate
- **Integration**: Native plugin for NVIDIA Isaac Sim and Omniverse; Python API for headless evaluation
- **Alpamayo 1.5 baseline**: AlpaSim Score **0.81 ± 0.01** on 910 challenging scenarios from PhysicalAI-AV-NuRec evaluation set

---

### Training & Dataset Expansion

| Stage | Data | Method | Purpose |
|:------|:-----|:-------|:--------|
| Cosmos-Reason2 Pre-training | Physics documents + simulation + web | Next-token prediction | Physical world understanding |
| Supervised Fine-tuning (SFT) | 80,000 hrs multi-camera driving + CoC v2 traces | Cross-entropy on reasoning + trajectory | Scene understanding + CoC generation |
| Diffusion Expert Training | 80,000 hrs trajectory data | DDPM loss conditioned on VLM embedding | Trajectory prediction quality |
| RL Alignment | AlpaSim closed-loop rollouts | PPO: AlpaSim Score as reward | Closed-loop safety optimization |
| Adversarial Augmentation | AlpaSim synthetic scenarios | SFT on augmented data | Long-tail edge case robustness |

**PhysicalAI Open Dataset** (released Mar 2026):
- 80,000 hours total; ~1 billion images
- 25 countries, 3,000+ cities
- 3 million CoC v2 reasoning annotations
- Weather diversity: clear, rain, fog, snow, night, dusk/dawn
- Road type diversity: highway, urban, rural, parking, construction zones
- 910 curated challenging evaluation scenarios (PhysicalAI-AV-NuRec)

---

### Architecture Diagram — Alpamayo 1.5

{::nomarkdown}
<div style="max-width: 780px; margin: 1.5em auto; font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;">
  <div style="background: linear-gradient(135deg, #047857 0%, #065F46 100%); color: white; padding: 14px 20px; border-radius: 10px 10px 0 0; text-align: center; font-weight: 700; font-size: 16px;">
    Alpamayo 1.5 — Dual-Module VLA Architecture
  </div>
  <div style="background: #F0FDF4; border: 2px solid #6EE7B7; border-top: none; border-radius: 0 0 10px 10px; padding: 20px;">

    <!-- Legend -->
    <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-bottom: 18px; font-size: 12px;">
      <span style="background: #065F46; color: white; padding: 3px 10px; border-radius: 12px;">📷 Perception</span>
      <span style="background: #047857; color: white; padding: 3px 10px; border-radius: 12px;">🧠 Cosmos-Reason2 (8.2B)</span>
      <span style="background: #10B981; color: white; padding: 3px 10px; border-radius: 12px;">🎯 Diffusion Expert (2.3B)</span>
      <span style="background: #34D399; color: #065F46; padding: 3px 10px; border-radius: 12px; font-weight: 600;">✨ New in 1.5</span>
    </div>

    <!-- Input Row -->
    <div style="display: flex; gap: 8px; flex-wrap: wrap; margin-bottom: 8px;">
      <div style="flex: 2; min-width: 200px; background: #065F46; color: white; border-radius: 8px; padding: 10px 14px;">
        <div style="font-weight: 700; font-size: 13px; margin-bottom: 4px;">📷 Surround-View Cameras (×6)</div>
        <div style="font-size: 11px; opacity: 0.85;">448×448 px each · 3 past timesteps<br/>→ SigLIP ViT-H/14 encoder → 256 tokens/cam<br/><strong>4,608 visual tokens total</strong></div>
      </div>
      <div style="flex: 1; min-width: 130px; background: #065F46; color: white; border-radius: 8px; padding: 10px 14px; position: relative;">
        <div style="position: absolute; top: -8px; right: 8px; background: #34D399; color: #065F46; padding: 1px 7px; border-radius: 4px; font-size: 10px; font-weight: 700;">✨ NEW</div>
        <div style="font-weight: 700; font-size: 13px; margin-bottom: 4px;">🚗 Egomotion History</div>
        <div style="font-size: 11px; opacity: 0.85;">Speed · Steering · Accel<br/>Past 3 timesteps → tokens</div>
      </div>
      <div style="flex: 1; min-width: 130px; background: #065F46; color: white; border-radius: 8px; padding: 10px 14px; position: relative;">
        <div style="position: absolute; top: -8px; right: 8px; background: #34D399; color: #065F46; padding: 1px 7px; border-radius: 4px; font-size: 10px; font-weight: 700;">✨ NEW</div>
        <div style="font-weight: 700; font-size: 13px; margin-bottom: 4px;">💬 Text Prompt / Nav Goal</div>
        <div style="font-size: 11px; opacity: 0.85;">"drive conservatively"<br/>"turn right ahead" → tokens</div>
      </div>
    </div>

    <div style="text-align: center; color: #059669; font-size: 20px;">↓</div>

    <!-- Cosmos-Reason2 -->
    <div style="background: white; border: 3px solid #047857; border-radius: 8px; padding: 14px; margin-bottom: 8px; position: relative;">
      <div style="position: absolute; top: -12px; left: 16px; background: #047857; color: white; padding: 2px 10px; border-radius: 6px; font-size: 11px; font-weight: 700;">MODULE 1 — Cosmos-Reason2 VLM Backbone · 8.2B params</div>
      <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px; font-size: 12px;">
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>GQA</strong> 32Q / 8KV heads</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>RoPE</strong> θ=500K (32K ctx)</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>SwiGLU</strong> FFN</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>RMSNorm</strong></span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;">Physical AI pre-training</span>
        <span style="background: #6EE7B7; color: #065F46; padding: 4px 10px; border-radius: 6px; font-weight: 600;">CoC v2 + counterfactuals</span>
      </div>
      <div style="margin-top: 10px; background: #F0FDF4; border: 1px solid #A7F3D0; border-radius: 6px; padding: 8px 12px; font-size: 12px; color: #065F46;">
        <strong>Output A:</strong> Chain of Causation v2 reasoning trace (text tokens) — incl. counterfactuals<br/>
        <strong>Output B:</strong> Scene latent embedding → Diffusion Expert conditioning
      </div>
    </div>

    <div style="text-align: center; color: #059669; font-size: 20px;">↓</div>

    <!-- Diffusion Expert -->
    <div style="background: white; border: 3px solid #10B981; border-radius: 8px; padding: 14px; margin-bottom: 8px; position: relative;">
      <div style="position: absolute; top: -12px; left: 16px; background: #10B981; color: white; padding: 2px 10px; border-radius: 6px; font-size: 11px; font-weight: 700;">MODULE 2 — Diffusion Expert · 2.3B params · ✨ New in 1.5</div>
      <div style="display: flex; flex-wrap: wrap; gap: 8px; margin-top: 8px; font-size: 12px;">
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>DiT</strong> 24 transformer layers</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>Cross-attn</strong> → Cosmos-Reason2</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;"><strong>DDIM 10-step</strong> inference</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;">8 candidate trajectories</span>
        <span style="background: #D1FAE5; color: #065F46; padding: 4px 10px; border-radius: 6px;">Bicycle model constraint</span>
      </div>
      <div style="margin-top: 10px; background: #F0FDF4; border: 1px solid #A7F3D0; border-radius: 6px; padding: 8px 12px; font-size: 12px; color: #065F46;">
        <strong>Output:</strong> Best trajectory from 8 candidates — 60 waypoints × (x, y, heading) @ 10Hz, 6-second horizon
      </div>
    </div>

    <div style="text-align: center; color: #059669; font-size: 20px;">↓</div>

    <!-- Final Output -->
    <div style="background: #065F46; color: white; padding: 12px 16px; border-radius: 8px; text-align: center;">
      <strong>🚗 Vehicle Trajectory Output</strong>
      <div style="font-size: 11px; opacity: 0.85; margin-top: 4px;">
        60 waypoints · 10Hz · 6-second horizon · (x, y, heading) in ego-vehicle frame · Kinematically feasible
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

### Community Perspective

- **Scaling validated**: The 46× expansion in training data (1,727h → 80,000h) was seen as a clear signal that NVIDIA is serious about Alpamayo as a production-track product, not just a research demo — a sentiment reinforced by the CES 2026 announcement context
- **Promptable driving praised**: The ability to steer the model's behavior with natural language prompts was called out as a transformative feature for fleet operators, regulators, and accessibility use cases — it makes tuning AV behavior without retraining practical for the first time
- **AlpaSim as community infrastructure**: The release of AlpaSim as an open-source evaluation framework was arguably as significant as the model itself — researchers now have a standardized closed-loop benchmark, filling a major gap in reproducibility for AV model comparisons
- **Dual-module design as a template**: The separation of reasoning (Cosmos-Reason2) and action (Diffusion Expert) into independently trainable modules was praised in the robotics community as a reusable design pattern for physical AI systems beyond autonomous driving
- **Counterfactual reasoning breakthrough**: The CoC v2 counterfactual traces were highlighted as a step toward formal safety verification — if a model can reason about what it *would have done*, its decision boundaries become auditable
- **Compute cost concern**: At 10B parameters (FP16: ~20GB minimum, recommended 40GB for stable inference), deployment on automotive edge hardware requires aggressive quantization — some practitioners noted that DRIVE Thor's 256-TOPS compute budget is tight for 10B at 10Hz without INT4 quantization
- **Data diversity recognized**: The 25-country training dataset was praised for covering a far wider distribution of road rules, driving styles, and infrastructure types than any previous public AV dataset — a key step toward globally generalizable autonomous driving

---

### Model Variants

| Model | Total Parameters | VLM Backbone | Vision Encoder | Diffusion Expert | VRAM (FP16) | Latency (H100) | Quantized (INT8) | License |
|:------|:----------:|:------------:|:--------------:|:----------------:|:-----------:|:--------------:|:----------------:|:-------:|
| Alpamayo-1.5-10B | ~10.5B | Cosmos-Reason2 (8.2B) | SigLIP ViT-H/14 | 2.3B DiT | ~40 GB | ~120ms | ✅ (20 GB, ~65ms) | Non-commercial |

> Alpamayo 1.5 is released as a single 10B model (unlike R1's 0.5B/3B/7B family). Smaller edge variants are planned for a future Alpamayo 2.0 release. INT8 quantized weights are available via `nvidia/Alpamayo-1.5-10B-INT8` on Hugging Face.

---

### Key Industry Ideas Incorporated

<details>
<summary><strong>Key Industry Ideas Incorporated — Alpamayo 1.5</strong></summary>

| Technique | Origin | How Alpamayo 1.5 Used It |
|:----------|:-------|:-------------------------|
| Cosmos-Reason2 VLM Backbone | NVIDIA Cosmos platform (2025) | Physical-AI-specialized VLM for scene understanding and CoC v2 reasoning |
| Diffusion Transformer (DiT) | Peebles & Xie, "Scalable Diffusion Models with Transformers" (ICCV 2023) | 24-layer DiT as standalone trajectory decoder with cross-attention conditioning |
| SigLIP Vision Encoder | Zhai et al. "Sigmoid Loss for Language Image Pre-Training" (ICCV 2023) | ViT-H/14 SigLIP encoder replacing earlier ViT for stronger visual grounding |
| Counterfactual Reasoning | Lewis & Vaswani (2021); Robustness literature | CoC v2 traces include counterfactual branches for improved safety auditability |
| DDIM Sampling | Song et al. (ICLR 2021) | 10-step DDIM for real-time diffusion trajectory decoding |
| Multi-candidate trajectory selection | Diverse trajectory prediction (Trajectron++, 2020) | 8 candidates per inference → safety cost function selection |
| Closed-loop RL training | Waymax / nuPlan RL baselines (2023-2024) | PPO on AlpaSim closed-loop rollouts for safety reward |
| Egomotion conditioning | DriveDreamer, MUVO (2024) | Past vehicle kinematics as temporal context for smoother trajectory continuity |
| Promptable behavior control | InstructPix2Pix (2023); Text-conditioned robotics | Free-text prompts modulate trajectory style without retraining |
| Knowledge Distillation | Hinton et al. (2015) | Cosmos-Reason2 distills physical AI knowledge from larger Cosmos World Foundation models |
| GQA | Ainslie et al. (EMNLP 2023) | Efficient KV cache for Cosmos-Reason2 backbone during long CoC trace generation |
| AlpaSim Evaluation | nuPlan / Waymo Open Dataset evaluation paradigm | Closed-loop benchmark metric (AlpaSim Score 0.81) standardizing AV model comparison |

</details>

---



### Technical Papers

| Model / Topic | Title | Link | Venue |
|:------|:------|:-----|:------|
| Alpamayo-R1 | Alpamayo-R1: Bridging Reasoning and Action Prediction for Generalizable Autonomous Driving | [arXiv:2511.00088](https://arxiv.org/abs/2511.00088) | NeurIPS 2025 |
| Alpamayo 1.5 | Alpamayo 1.5 — Model Card | [HuggingFace](https://huggingface.co/nvidia/Alpamayo-1.5-10B) | Mar 2026 |
| Cosmos World Foundation | NVIDIA Cosmos: World Foundation Models for Physical AI | [NVIDIA Research](https://research.nvidia.com/cosmos) | Jan 2025 |
| UniAD | Planning-oriented Autonomous Driving (UniAD) | [arXiv:2212.10156](https://arxiv.org/abs/2212.10156) | CVPR 2023 |
| DriveLM | DriveLM: Driving with Graph Visual Question Answering | [arXiv:2312.14150](https://arxiv.org/abs/2312.14150) | CVPR 2024 |
| DriveVLM | DriveVLM: The Convergence of Autonomous Driving and Large Vision-Language Models | [arXiv:2402.12289](https://arxiv.org/abs/2402.12289) | 2024 |
| BEVFormer | BEVFormer: Learning Bird's-Eye-View Representation from Multi-Camera Images | [arXiv:2203.17270](https://arxiv.org/abs/2203.17270) | ECCV 2022 |
| DDPM | Denoising Diffusion Probabilistic Models | [arXiv:2006.11239](https://arxiv.org/abs/2006.11239) | NeurIPS 2020 |
| DDIM | Denoising Diffusion Implicit Models | [arXiv:2010.02502](https://arxiv.org/abs/2010.02502) | ICLR 2021 |
| Chain-of-Thought | Chain-of-Thought Prompting Elicits Reasoning in Large Language Models | [arXiv:2201.11903](https://arxiv.org/abs/2201.11903) | NeurIPS 2022 |
| DeepSeek-R1 | DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via RL | [arXiv:2501.12948](https://arxiv.org/abs/2501.12948) | 2025 |

### Official Resources

| Resource | Link |
|:---------|:-----|
| Alpamayo GitHub R1 (NVlabs) | [github.com/NVlabs/alpamayo](https://github.com/NVlabs/alpamayo) |
| Alpamayo 1.5 GitHub (NVlabs) | [github.com/NVlabs/alpamayo1.5](https://github.com/NVlabs/alpamayo1.5) |
| Alpamayo 1.5 Hugging Face | [huggingface.co/nvidia/Alpamayo-1.5-10B](https://huggingface.co/nvidia/Alpamayo-1.5-10B) |
| AlpaSim Framework | [github.com/NVlabs/alpamayo1.5](https://github.com/NVlabs/alpamayo1.5) |
| NVIDIA Cosmos Platform | [research.nvidia.com/cosmos](https://research.nvidia.com/cosmos) |
| NVIDIA DRIVE Platform | [developer.nvidia.com/drive](https://developer.nvidia.com/drive) |
| Chain of Causation Dataset | [github.com/NVlabs/alpamayo/dataset](https://github.com/NVlabs/alpamayo) |
| Hugging Face Model Hub | [huggingface.co/nvidia/alpamayo](https://huggingface.co/nvidia) |

### Cited Techniques

| Technique | Paper | Link |
|:----------|:------|:-----|
| DiT | Peebles & Xie, "Scalable Diffusion Models with Transformers" (ICCV 2023) | [arXiv:2212.09748](https://arxiv.org/abs/2212.09748) |
| SigLIP | Zhai et al., "Sigmoid Loss for Language Image Pre-Training" (ICCV 2023) | [arXiv:2303.15343](https://arxiv.org/abs/2303.15343) |
| DDPM | Ho et al., "Denoising Diffusion Probabilistic Models" (NeurIPS 2020) | [arXiv:2006.11239](https://arxiv.org/abs/2006.11239) |
| DDIM | Song et al., "Denoising Diffusion Implicit Models" (ICLR 2021) | [arXiv:2010.02502](https://arxiv.org/abs/2010.02502) |
| GQA | Ainslie et al., "GQA: Training Generalized Multi-Query Transformer Models" (EMNLP 2023) | [arXiv:2305.13245](https://arxiv.org/abs/2305.13245) |
| RoPE | Su et al., "RoFormer: Enhanced Transformer with Rotary Position Embedding" (2021) | [arXiv:2104.09864](https://arxiv.org/abs/2104.09864) |
| RMSNorm | Zhang & Sennrich, "Root Mean Square Layer Normalization" (NeurIPS 2019) | [arXiv:1910.07467](https://arxiv.org/abs/1910.07467) |
| SwiGLU | Dauphin et al., "Language Modeling with Gated Convolutional Networks" (ICML 2017) | — |
| ViT | Dosovitskiy et al., "An Image is Worth 16x16 Words" (ICLR 2021) | [arXiv:2010.11929](https://arxiv.org/abs/2010.11929) |
| PPO | Schulman et al., "Proximal Policy Optimization Algorithms" (2017) | [arXiv:1707.06347](https://arxiv.org/abs/1707.06347) |
| Chain-of-Thought | Wei et al., "Chain-of-Thought Prompting" (NeurIPS 2022) | [arXiv:2201.11903](https://arxiv.org/abs/2201.11903) |
| BEVFormer | Li et al., "BEVFormer" (ECCV 2022) | [arXiv:2203.17270](https://arxiv.org/abs/2203.17270) |
| UniAD | Hu et al., "Planning-oriented Autonomous Driving" (CVPR 2023) | [arXiv:2212.10156](https://arxiv.org/abs/2212.10156) |

---

<p align="center">
  <sub>Built with data from the Alpamayo-R1 technical paper (NeurIPS 2025), Alpamayo 1.5 Hugging Face model card and GitHub (Mar 2026), NVIDIA Cosmos platform announcements, and cited autonomous driving research. All benchmark numbers sourced from the referenced publications. Model weights and dataset are subject to NVIDIA non-commercial research license.</sub>
</p>

<p align="center">
  <a href="README.md">← Back to Index</a>
</p>
