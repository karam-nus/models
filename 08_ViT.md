---
title: "Vision Transformer (ViT) Family"
---

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:24px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">🖼️ Vision Transformer</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">📅 2020 – 2023</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">⚡ Pure Transformer for Images</div>
</div>
{:/nomarkdown}

# Vision Transformer (ViT) Family

The Vision Transformer (ViT) family represents the paradigm shift from convolutional neural networks to pure Transformer architectures for computer vision. Introduced by Dosovitskiy et al. (2020), the core idea is disarmingly simple: split an image into fixed-size patches, linearly embed each patch, prepend a learnable `[CLS]` token, add positional encodings, and feed the sequence through a standard Transformer encoder. This family has grown into one of the most influential model lineages in AI, spawning specialized variants for data-efficient training (DeiT), masked image modeling (BEiT, MAE), self-supervised dense features (DINOv2), vision-language alignment at scale (EVA-02), and trillion-parameter-class vision models (ViT-22B).

---

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:24px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:14px;">📅 ViT Family Timeline</div>
  <div style="display:flex;flex-wrap:wrap;gap:10px;align-items:center;">
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Oct 2020</div><div>ViT</div><div style="color:#6B7280;font-size:11px;">Dosovitskiy et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Dec 2020</div><div>DeiT</div><div style="color:#6B7280;font-size:11px;">Touvron et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Jun 2021</div><div>BEiT</div><div style="color:#6B7280;font-size:11px;">Bao et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Nov 2021</div><div>MAE</div><div style="color:#6B7280;font-size:11px;">He et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Apr 2023</div><div>DINOv2</div><div style="color:#6B7280;font-size:11px;">Oquab et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Mar 2023</div><div>EVA-02</div><div style="color:#6B7280;font-size:11px;">Fang et al.</div>
    </div>
    <div style="color:#6366F1;font-size:18px;">→</div>
    <div style="background:#fff;border:1px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Feb 2023</div><div>ViT-22B</div><div style="color:#6B7280;font-size:11px;">Dehghani et al.</div>
    </div>
  </div>
</div>
{:/nomarkdown}

## Cross-Family Benchmark Comparison

| Model | Params | ImageNet-1K Top-1 | ImageNet-1K Top-5 | Pre-training Data | Supervision |
|---|---|---|---|---|---|
| ViT-B/16 | 86M | 81.8% | — | JFT-300M | Supervised |
| ViT-L/16 | 307M | 87.8% | — | JFT-300M | Supervised |
| ViT-H/14 | 632M | 88.6% | — | JFT-300M | Supervised |
| DeiT-B/16 | 86M | 81.8% | — | ImageNet-1K | Supervised + Distill |
| DeiT-B/16 distilled | 87M | 83.4% | — | ImageNet-1K | Supervised + Distill |
| BEiT-L/16 | 307M | 88.6% | — | ImageNet-21K | Masked + Fine-tune |
| MAE ViT-H/14 | 632M | 87.8% | — | ImageNet-1K | Masked + Fine-tune |
| DINOv2 ViT-g/14 | 1.1B | 86.5% (linear) | — | LVD-142M | Self-supervised |
| EVA-02-L/14 | 304M | 89.6% | — | Merged-30M | CLIP + MIM |
| ViT-22B | 21.7B | 89.5% | — | JFT-4B | Supervised |

---

## 1. ViT — An Image is Worth 16×16 Words

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Oct 2020</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 86M – 632M params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 88.6% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ Google Brain</div>
</div>
{:/nomarkdown}

The original Vision Transformer (ViT) proved that a pure Transformer encoder, with minimal image-specific inductive bias, can match or exceed convolutional networks on image recognition when pre-trained on sufficiently large datasets. The model sequences non-overlapping image patches as tokens, uses 1D learnable positional embeddings, and appends a `[CLS]` token whose final representation is fed to a classification head. Unlike CNNs, ViT has no built-in translation equivariance or locality bias, making it data-hungry but highly scalable.

- Input image of shape `[B, 3, H, W]` is split into `N = (H/P)²` patches of size `P×P`
- Each patch flattened to `[B, N, P²·3]` then projected to `[B, N, D]` via a learnable linear embedding
- A learnable `[CLS]` token is prepended: sequence length becomes `N+1`
- 1D learnable positional embeddings of shape `[N+1, D]` are added element-wise
- Standard Transformer encoder: Multi-Head Self-Attention (MHSA) + Feed-Forward Network (FFN) × L layers
- Pre-LayerNorm (`Pre-LN`) used in practice for training stability; original paper used Post-LN
- MHSA: `Q, K, V = XW_Q, XW_K, XW_V`; attention scores `= softmax(QKᵀ / √d_k)V`
- FFN: two-layer MLP with GELU, hidden dim = 4 × D
- Classification: `[CLS]` token at final layer → linear classifier → logits `[B, num_classes]`
- Patch embedding is equivalent to a convolution with kernel size P, stride P, no overlap
- Position embeddings learn relative spatial structure implicitly from data; 2D sinusoidal also works
- Fine-tuning at higher resolution: N increases, positional embeddings must be interpolated (2D bicubic)
- Hybrid variant: ResNet feature maps replace raw patch embeddings; ~1/4 sequence length
- Requires JFT-300M or ImageNet-21K for strong performance; ImageNet-1K alone underperforms ResNets
- ViT-B: d=768, L=12, H=12; ViT-L: d=1024, L=24, H=16; ViT-H: d=1280, L=32, H=16
- Self-attention heads each operate on d_head = D/H dimensions, typically 64
- Attention in ViT is O(N²) — quadratic in sequence length — a key scalability bottleneck
- CLS token aggregates global context; alternatively, global average pooling of all tokens also works well
- ViT with enough data shows emergent properties: semantic segmentation of objects in attention maps

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ ViT Architecture — Patch Tokenization &amp; Transformer Encoder</div>
  <div style="display:flex;flex-direction:column;gap:12px;">

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:12px;text-align:center;">
      <div style="font-weight:600;color:#4F46E5;margin-bottom:6px;">Input</div>
      <div style="font-size:12px;color:#374151;">Image <code>[B, 3, H, W]</code></div>
    </div>

    <div style="text-align:center;color:#6366F1;font-size:20px;">↓</div>

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:12px;text-align:center;">
      <div style="font-weight:600;color:#4F46E5;margin-bottom:6px;">Patch Embedding</div>
      <div style="font-size:12px;color:#374151;">Split into N patches → Linear proj → <code>[B, N, D]</code></div>
    </div>

    <div style="text-align:center;color:#6366F1;font-size:20px;">↓</div>

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:12px;text-align:center;">
      <div style="font-weight:600;color:#4F46E5;margin-bottom:6px;">Token Prepend + Pos Embed</div>
      <div style="font-size:12px;color:#374151;">Prepend [CLS] → <code>[B, N+1, D]</code> + learnable pos emb</div>
    </div>

    <div style="text-align:center;color:#6366F1;font-size:20px;">↓</div>

    <div style="background:#EEF2FF;border:2px solid #6366F1;border-radius:8px;padding:12px;">
      <div style="font-weight:700;color:#4F46E5;text-align:center;margin-bottom:10px;">Transformer Encoder Block × L</div>
      <div style="display:flex;gap:10px;flex-wrap:wrap;justify-content:center;">
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">LayerNorm</div>
        </div>
        <div style="color:#6366F1;align-self:center;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">MHSA</div>
          <div style="color:#6B7280;font-size:11px;">H heads, d_head=D/H</div>
        </div>
        <div style="color:#6366F1;align-self:center;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">+ Residual</div>
        </div>
      </div>
      <div style="display:flex;gap:10px;flex-wrap:wrap;justify-content:center;margin-top:10px;">
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">LayerNorm</div>
        </div>
        <div style="color:#6366F1;align-self:center;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">FFN (GELU)</div>
          <div style="color:#6B7280;font-size:11px;">4D hidden dim</div>
        </div>
        <div style="color:#6366F1;align-self:center;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:600;">+ Residual</div>
        </div>
      </div>
    </div>

    <div style="text-align:center;color:#6366F1;font-size:20px;">↓</div>

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:12px;text-align:center;">
      <div style="font-weight:600;color:#4F46E5;margin-bottom:6px;">Classification Head</div>
      <div style="font-size:12px;color:#374151;">Extract [CLS] token → LayerNorm → Linear → <code>[B, num_classes]</code></div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** ViT's release in 2020 was polarizing — many practitioners dismissed it as impractical for small datasets, while researchers immediately recognized its profound implications for scalability. The absence of CNN-style inductive biases was seen simultaneously as a weakness (data hunger) and a strength (greater expressivity and scalability). The community quickly converged on a consensus: ViT is the right architecture for large-scale pre-training, especially with vision-language data, and its simplicity makes it far easier to scale than the heterogeneous stages of ConvNets. Today, ViT backbones underpin nearly every state-of-the-art vision foundation model.

### ViT Model Variants

| Variant | Patch Size | d_model | Layers | Heads | FFN Dim | Params | ImageNet Top-1 |
|---|---|---|---|---|---|---|---|
| ViT-Ti/16 | 16 | 192 | 12 | 3 | 768 | 5.7M | 72.2% |
| ViT-S/16 | 16 | 384 | 12 | 6 | 1536 | 22M | 79.9% |
| ViT-B/16 | 16 | 768 | 12 | 12 | 3072 | 86M | 81.8% |
| ViT-B/32 | 32 | 768 | 12 | 12 | 3072 | 88M | 80.1% |
| ViT-L/16 | 16 | 1024 | 24 | 16 | 4096 | 307M | 87.8% |
| ViT-L/32 | 32 | 1024 | 24 | 16 | 4096 | 307M | 81.5% |
| ViT-H/14 | 14 | 1280 | 32 | 16 | 5120 | 632M | 88.6% |

<details>
<summary><strong>Key Industry Ideas Incorporated into ViT</strong></summary>

- **Transformer self-attention** (Vaswani et al., 2017): full global attention over all tokens, borrowed directly from NLP
- **BERT-style CLS token** (Devlin et al., 2018): prepended learnable token aggregates sequence-level representation
- **Patch tokenization**: discretizes continuous 2D image into 1D token sequence, bridging vision and language modalities
- **Pre-LayerNorm** (Xiong et al., 2020): moves LayerNorm before attention/FFN for more stable gradient flow in deep networks
- **Learnable positional embeddings**: empirically competitive with sinusoidal; enables fine-tuning at different resolutions
- **Hybrid CNN-Transformer**: uses ResNet feature maps as patch tokens, reducing sequence length via spatial downsampling
- **JFT large-scale pre-training**: demonstrated that scale compensates for lack of inductive bias — a finding that guided all subsequent ViT work
- **GELU activation**: used in FFN following GPT/BERT convention; smoother than ReLU and beneficial for Transformer training
- **Dropout + weight decay regularization**: critical for preventing overfitting when fine-tuning large pre-trained ViTs on small datasets

</details>

---

## 2. DeiT — Data-Efficient Image Transformers

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Dec 2020</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 86M – 87M params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 83.4% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ Facebook AI Research</div>
</div>
{:/nomarkdown}

DeiT (Data-efficient Image Transformers) solved ViT's critical Achilles heel — its dependence on massive proprietary datasets like JFT-300M. By introducing a **distillation token** and a strong data augmentation regime (RandAugment, Mixup, CutMix, repeated augmentation), DeiT trains competitive ViT models on ImageNet-1K alone in ~3 days on 8 GPUs. The teacher is a RegNet or EfficientNet CNN that provides soft label supervision; the student ViT learns from both the hard label and the teacher's soft distribution via a dedicated distillation token in the sequence.

- Training entirely on ImageNet-1K (1.28M images); no external pre-training data required
- Adds a second learnable **distillation token** alongside `[CLS]`, processed by all Transformer layers
- At training: distillation token output supervised with teacher soft labels via KL divergence or hard distillation
- At inference: `[CLS]` and distillation token outputs are averaged for final prediction
- Teacher: RegNetY-16GF (84M params) trained on ImageNet with strong augmentation
- Data augmentation pipeline: RandAugment, Mixup, CutMix, random erasing, repeated augmentation
- Label smoothing: cross-entropy loss with ε=0.1 to prevent overconfident predictions
- Optimizer: AdamW with cosine learning rate schedule; 300 epochs of training
- Exponential Moving Average (EMA) of weights used for evaluation
- DeiT-B/16 with distillation matches ViT-B/16 trained on JFT-300M (81.8% vs 81.8%)
- DeiT-B distilled: 83.4% — surpasses ViT-B pre-trained on ImageNet-21K (81.8%)
- Architecture identical to ViT-B/16 except for the additional distillation token
- Throughput: DeiT-B processes ~300 images/sec on a single V100, practical for production
- DeiT-S (22M) and DeiT-Ti (5M) demonstrate strong small-model performance
- Fine-tuning at 384×384 resolution with bicubic interpolation of positional embeddings: +1–2% accuracy
- Distillation token learns to attend to local/high-frequency features (aligning with CNN teacher's bias)
- Hard distillation (argmax of teacher output) performs slightly better than soft distillation in practice
- Established the blueprint for training ViTs efficiently on standard academic datasets

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ DeiT — Distillation Token Architecture</div>
  <div style="display:flex;flex-direction:column;gap:10px;">

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-size:12px;color:#374151;font-weight:600;">Input Image → Patch Embed → N patch tokens</div>
    </div>
    <div style="text-align:center;color:#6366F1;">↓</div>
    <div style="display:flex;gap:10px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#4F46E5;">[CLS]</div>
        <div style="color:#6B7280;font-size:11px;">Class token</div>
      </div>
      <div style="background:#E0F2FE;border:1px solid #0EA5E9;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#0369A1;">[DIST]</div>
        <div style="color:#6B7280;font-size:11px;">Distillation token</div>
      </div>
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;">
        <div style="font-weight:600;">patch₁ … patchₙ</div>
      </div>
    </div>
    <div style="text-align:center;color:#6366F1;">↓</div>
    <div style="background:#fff;border:2px solid #6366F1;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">Transformer Encoder × L</div>
      <div style="font-size:12px;color:#374151;">All N+2 tokens attend to each other</div>
    </div>
    <div style="text-align:center;color:#6366F1;">↓</div>
    <div style="display:flex;gap:10px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:8px;padding:10px 14px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#4F46E5;">CLS → Linear → y_cls</div>
        <div style="color:#6B7280;font-size:11px;">Hard label CE loss</div>
      </div>
      <div style="background:#E0F2FE;border:1px solid #0EA5E9;border-radius:8px;padding:10px 14px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#0369A1;">DIST → Linear → y_dist</div>
        <div style="color:#6B7280;font-size:11px;">Teacher soft label KL/CE loss</div>
      </div>
    </div>
    <div style="text-align:center;color:#6366F1;">↓</div>
    <div style="background:#F0FDF4;border:1px solid #22C55E;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:600;color:#15803D;">Inference: (y_cls + y_dist) / 2</div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** DeiT democratized ViT research by removing the dependency on proprietary web-scale datasets. Its distillation token mechanism elegantly solved the inductive-bias gap: the CNN teacher implicitly transfers local feature priors to the ViT student through soft label supervision. The community adopted DeiT's augmentation recipe almost universally for ViT training, even when not performing knowledge distillation. DeiT-III (2022) further refined this, showing that a simpler supervised training recipe with a strong ResNet teacher can push DeiT-H to 90.1% on ImageNet.

### DeiT Model Variants

| Variant | Patch Size | d_model | Layers | Heads | FFN Dim | Params | IN-1K Top-1 (w/ distill) |
|---|---|---|---|---|---|---|---|
| DeiT-Ti/16 | 16 | 192 | 12 | 3 | 768 | 5.7M | 74.5% |
| DeiT-S/16 | 16 | 384 | 12 | 6 | 1536 | 22M | 81.2% |
| DeiT-B/16 | 16 | 768 | 12 | 12 | 3072 | 86M | 83.4% |
| DeiT-B/16↑384 | 16 | 768 | 12 | 12 | 3072 | 87M | 85.2% |
| DeiT-III-S/16 | 16 | 384 | 12 | 6 | 1536 | 22M | 81.4% |
| DeiT-III-B/16 | 16 | 768 | 12 | 12 | 3072 | 86M | 83.8% |
| DeiT-III-H/14 | 14 | 1280 | 32 | 16 | 5120 | 632M | 90.1% |

<details>
<summary><strong>Key Industry Ideas Incorporated into DeiT</strong></summary>

- **Knowledge Distillation** (Hinton et al., 2015): teacher soft labels as supervision signal; adapted to vision Transformers via a dedicated sequence token
- **Token-based distillation**: novel mechanism to separate classification and distillation objectives within the same sequence, allowing gradient signals to train different attention patterns
- **RandAugment** (Cubuk et al., 2019): stochastic magnitude-based data augmentation that replaced manual augmentation pipelines
- **Mixup** (Zhang et al., 2018): linearly interpolates two images and their labels; encourages smooth decision boundaries
- **CutMix** (Yun et al., 2019): replaces image patches with patches from another image; particularly effective for ViTs due to patch structure
- **Repeated Augmentation** (Hoffer et al., 2020): samples same image multiple times per batch with different augmentations; acts as implicit batch size increase
- **Stochastic Depth / DropPath** (Huang et al., 2016): randomly drops entire residual branches during training; enables training of very deep ViTs
- **AdamW optimizer** (Loshchilov & Hutter, 2017): decoupled weight decay essential for Transformer training stability
- **EMA of weights**: exponential moving average of parameters used only for evaluation; standard practice following Mean Teacher (Tarvainen & Valpola, 2017)

</details>

---

## 3. BEiT — BERT Pre-Training of Image Transformers

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Jun 2021</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 86M – 307M params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 88.6% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ Microsoft Research</div>
</div>
{:/nomarkdown}

BEiT (Bidirectional Encoder representation from Image Transformers) transplants BERT's masked language modeling objective into the visual domain. The key insight is that raw pixel values are poor reconstruction targets — instead, BEiT recovers discrete **visual tokens** produced by DALL-E's dVAE (discrete Variational Autoencoder). This two-stage approach (tokenize → mask → predict tokens) enables ViT pre-training on unlabeled images, yielding features that transfer exceptionally well to downstream tasks with far less labeled data.

- Two-stage pipeline: (1) Train/obtain a visual tokenizer (dVAE); (2) Pre-train ViT with masked token prediction
- **Block-wise masking**: roughly 40% of patches masked with contiguous rectangular blocks (not random); prevents trivial copying from neighbors
- Masked patches are replaced with a learnable `[MASK]` token embedding before the Transformer
- Prediction target: discrete visual token IDs from dVAE codebook (vocabulary size 8192 by default)
- Pre-training objective: cross-entropy loss over visual token vocabulary at masked positions only
- dVAE tokenizer: ELBo-trained VAE from DALL-E, encodes 16×16 patches to one of 8192 discrete codes
- BEiT does NOT use the `[CLS]` token during pre-training; task head is a linear layer over all patch positions
- Fine-tuning: standard ViT fine-tuning with task-specific head; `[CLS]` token added back for classification
- Intermediate fine-tuning on ImageNet-21K before ImageNet-1K gives BEiT-L/16 88.6% Top-1
- Semantic segmentation: BEiT-L outperforms supervised ViT-L by >2 mIoU on ADE20K
- BEiT v2 (2022): uses a CLIP-guided visual tokenizer (VQ-KD) instead of DALL-E dVAE; improved features
- BEiT-3 (2022): multimodal extension — treats image patches and text tokens uniformly, enabling VQA, captioning
- Relative position bias: learnable log-spaced relative position bias added to attention logits (borrowed from Swin)
- Pre-training on ImageNet-21K (14M images) takes ~1 week on 64 V100s; competitive with supervised training
- Key advantage: no labeled data needed for pre-training; fine-tuned features generalize broadly
- Window attention variant: BEiT-L applied with Swin-style windows for dense prediction tasks
- Multi-scale adapter for downstream tasks: interpolate feature maps from multiple Transformer layers

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ BEiT — Masked Image Modeling Pre-training</div>
  <div style="display:flex;flex-direction:column;gap:10px;">

    <div style="display:flex;gap:10px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#F3F4F6;border:1px solid #D1D5DB;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;">
        <div style="font-weight:700;">Image Patches</div>
        <div style="color:#6B7280;font-size:11px;">[B, N, P²·3]</div>
      </div>
      <div style="align-self:center;color:#6366F1;font-size:16px;">⟶ tokenize ⟶</div>
      <div style="background:#FEF9C3;border:1px solid #EAB308;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#92400E;">dVAE Tokens</div>
        <div style="color:#6B7280;font-size:11px;">vocab=8192 ids</div>
      </div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ Block masking ~40%</div>

    <div style="display:flex;gap:6px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;">patch</div>
      <div style="background:#EEF2FF;border:2px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#EF4444;">[MASK]</div>
      <div style="background:#EEF2FF;border:2px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#EF4444;">[MASK]</div>
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;">patch</div>
      <div style="background:#EEF2FF;border:2px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#EF4444;">[MASK]</div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓</div>

    <div style="background:#fff;border:2px solid #6366F1;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:700;color:#4F46E5;">ViT Transformer Encoder × L</div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ at masked positions</div>

    <div style="background:#F0FDF4;border:1px solid #22C55E;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:600;color:#15803D;">Linear → Predict dVAE token ID (cross-entropy over 8192 classes)</div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** BEiT demonstrated that BERT-style pre-training is directly applicable to vision Transformers, opening the door to massive unlabeled pre-training. The community particularly appreciated the elegant two-stage design: by pre-computing discrete tokens offline, the main ViT pre-training loop is decoupled from pixel-level reconstruction, which had proven noisy in earlier generative approaches. BEiT's impact is evident in the proliferation of MIM (Masked Image Modeling) methods that followed, including CAE, PeCo, and ultimately MAE, which simplified the recipe further by removing the discrete tokenizer entirely.

### BEiT Model Variants

| Variant | Patch | d_model | Layers | Heads | FFN | Params | ADE20K mIoU | IN-1K Top-1 |
|---|---|---|---|---|---|---|---|---|
| BEiT-B/16 | 16 | 768 | 12 | 12 | 3072 | 86M | 45.7 | 83.2% |
| BEiT-L/16 | 16 | 1024 | 24 | 16 | 4096 | 307M | 53.3 | 88.6% |
| BEiT-v2-B/16 | 16 | 768 | 12 | 12 | 3072 | 86M | 49.9 | 85.5% |
| BEiT-v2-L/16 | 16 | 1024 | 24 | 16 | 4096 | 307M | 56.7 | 89.0% |
| BEiT-3-B | 16 | 768 | 12 | 12 | 3072 | 86M | — | 85.4% |
| BEiT-3-L | 16 | 1024 | 24 | 16 | 4096 | 307M | — | 88.6% |

<details>
<summary><strong>Key Industry Ideas Incorporated into BEiT</strong></summary>

- **BERT Masked Language Modeling** (Devlin et al., 2018): masked token prediction as a self-supervised pre-training objective; directly adapted here with visual tokens
- **Discrete VAE tokenization** (DALL-E, Ramesh et al., 2021): maps continuous image patches to discrete codes from a learned codebook; provides a richer prediction target than raw pixels
- **Block-wise masking strategy**: contiguous mask blocks prevent neighboring patches from trivially revealing masked content; shown superior to independent random masking
- **Relative position bias** (T5, Raffel et al., 2019; Shaw et al., 2018): improves generalization when fine-tuning at higher resolutions than pre-training
- **Intermediate fine-tuning** (Sun et al., 2019): fine-tuning on ImageNet-21K before ImageNet-1K provides strong weight initialization; adds ~1.5% Top-1 accuracy
- **Learnable MASK token**: single shared embedding replaces all masked patches; model cannot distinguish masked positions individually, forcing holistic reconstruction
- **VQ-KD tokenizer** (BEiT v2): uses CLIP features as distillation targets for the codebook, making tokens semantically meaningful and reducing reconstruction ambiguity
- **Dense prediction adaptation**: BEiT features transferred to ADE20K by taking intermediate layer outputs and applying deconvolution heads; robust to task mismatch

</details>

---

## 4. MAE — Masked Autoencoders Are Scalable Vision Learners

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Nov 2021</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 86M – 632M params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 87.8% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ FAIR / Meta AI</div>
</div>
{:/nomarkdown}

MAE (Masked Autoencoders) by Kaiming He et al. is perhaps the most influential ViT pre-training method for its simplicity and efficiency. The key innovation is **asymmetric encoder-decoder design**: the encoder processes only the ~25% of visible (unmasked) patches, and a small lightweight decoder reconstructs the full image including masked patches from pixel values. By masking 75% of patches (versus BEiT's 40%), MAE forces the model to understand holistic scene structure. The massive reduction in encoder input length (4× fewer tokens) makes pre-training 3× faster than comparable supervised training.

- **Asymmetric design**: encoder sees only visible patches (25%); decoder sees all patch positions (masked tokens added as learned embeddings)
- Encoder: standard ViT with full self-attention, operating on ~N/4 visible token embeddings
- Decoder: shallow Transformer (e.g., 8 layers, 512-dim) operating on all N positions
- Mask tokens: single shared learnable vector added at masked positions before the decoder
- Positional embeddings: sinusoidal 2D added to both encoder inputs and decoder inputs (crucial for decoder to know positions)
- Reconstruction target: **normalized pixel values** of original image at masked patches (mean subtracted, divided by std per patch)
- Loss: MSE over masked patches only; visible patches not included in loss computation
- Masking strategy: uniform random sampling without replacement (no block structure needed due to high masking ratio)
- High masking ratio (75%) is critical: lower ratios (e.g., 40%) reduce representation quality significantly
- Encoder is the transferable component; decoder is discarded after pre-training
- Pre-training: 1600 epochs on ImageNet-1K; ~31 hours on 64 A100s for ViT-L
- Fine-tuning: 50–100 epochs; MAE ViT-L/16 reaches 85.9% on ImageNet-1K
- MAE ViT-H/14 fine-tuned: 87.8% — competitive with supervised ViT-H trained on JFT
- Linear probing performance is lower than DINOv2 (66.8% vs 86.5%) — features are not linearly separable without fine-tuning
- MAE shows strong transfer to COCO detection (54.9 AP_box with ViT-L on COCO) and ADE20K (53.6 mIoU)
- Scaling behavior: MAE ViT-L significantly outperforms ViT-B, showing better scaling efficiency than supervised pre-training
- Pixel reconstruction encourages low-level feature learning; contrast with BEiT's semantic token targets
- Flexible: trivially extends to video (VideoMAE), audio (AudioMAE), point clouds (Point-MAE)

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ MAE — Asymmetric Encoder-Decoder</div>
  <div style="display:flex;flex-direction:column;gap:10px;">

    <div style="display:flex;gap:8px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#DCFCE7;border:1px solid #22C55E;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#15803D;">visible patch</div>
      <div style="background:#DCFCE7;border:1px solid #22C55E;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#15803D;">visible patch</div>
      <div style="background:#FEE2E2;border:1px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#DC2626;">masked</div>
      <div style="background:#FEE2E2;border:1px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#DC2626;">masked</div>
      <div style="background:#DCFCE7;border:1px solid #22C55E;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#15803D;">visible patch</div>
      <div style="background:#FEE2E2;border:1px solid #EF4444;border-radius:6px;padding:6px 10px;font-size:11px;font-weight:600;color:#DC2626;">masked</div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ encoder sees only green (25%)</div>

    <div style="background:#DCFCE7;border:2px solid #22C55E;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:700;color:#15803D;">Encoder (Full ViT, ~N/4 tokens)</div>
      <div style="font-size:12px;color:#374151;">Deep, large capacity — processes visible patches only</div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ add mask tokens at masked positions</div>

    <div style="background:#FEF9C3;border:2px solid #EAB308;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:700;color:#92400E;">Decoder (Lightweight ViT, all N tokens)</div>
      <div style="font-size:12px;color:#374151;">Shallow, small capacity — reconstructs full image</div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ MSE loss at masked positions</div>

    <div style="background:#F0FDF4;border:1px solid #22C55E;border-radius:8px;padding:10px;text-align:center;">
      <div style="font-weight:600;color:#15803D;">Reconstructed normalized pixel values</div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** MAE's release was a watershed moment — it showed that reconstruction-based pre-training (a concept long considered inferior to contrastive and discriminative methods) could produce state-of-the-art visual representations at scale. The community was struck by the simplicity: no discrete tokenizer, no contrastive pairs, no momentum encoder — just masking, encoding visible patches, and predicting pixels. MAE established that the asymmetric encoder-decoder design dramatically improves training efficiency and that high masking ratios are essential for forcing the model to learn semantic structure rather than relying on local texture copying.

### MAE Model Variants

| Variant | Patch | Encoder | Decoder | Masking | Params | IN-1K FT | IN-1K LP |
|---|---|---|---|---|---|---|---|
| MAE ViT-B/16 | 16 | 12L×768D | 8L×512D | 75% | 86M | 83.1% | 67.8% |
| MAE ViT-L/16 | 16 | 24L×1024D | 8L×512D | 75% | 307M | 85.9% | 76.0% |
| MAE ViT-H/14 | 14 | 32L×1280D | 8L×512D | 75% | 632M | 87.8% | 77.2% |
| MAE ViT-H/14↑448 | 14 | 32L×1280D | 8L×512D | 75% | 632M | 87.8% | — |

<details>
<summary><strong>Key Industry Ideas Incorporated into MAE</strong></summary>

- **Denoising Autoencoders** (Vincent et al., 2008): masking as structured noise; model learns to invert corruption, producing robust representations
- **BERT/GPT masked prediction**: masked token prediction generalized from discrete to continuous domain with pixel reconstruction targets
- **Asymmetric encoder-decoder** (U-Net, Ronneberger et al., 2015): deep encoder + shallow decoder decoupling; encoder gets full compute budget, decoder is minimal
- **High masking ratio**: counterintuitive finding that 75% masking outperforms lower ratios; increases task difficulty enough to prevent shortcut learning from neighboring patches
- **Sinusoidal 2D positional embeddings** (Vaswani et al., 2017): added at decoder stage to provide position information for reconstruction; encoder omits unmasked position bias
- **Normalized pixel targets**: per-patch mean/std normalization of reconstruction targets improves learning signal quality vs raw pixel values
- **Transfer learning via fine-tuning**: frozen MAE encoder + new task head fine-tuned end-to-end; decoder fully discarded at transfer time
- **Scalable pre-training**: 3–4× wall-clock speedup over full-sequence ViT pre-training due to reduced encoder sequence length; enables training ViT-H in reasonable time

</details>

---

## 5. DINOv2 — Self-Supervised Vision Features at Scale

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Apr 2023</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 22M – 1.1B params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 86.5% Linear Probe IN-1K</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ Meta AI</div>
</div>
{:/nomarkdown}

DINOv2 produces the first general-purpose visual features that perform competitively on a wide range of tasks with only a **frozen backbone and a simple linear head** — no fine-tuning required. The key contributions are (1) a carefully curated 142M image dataset (LVD-142M) built by deduplication and retrieval from internet images, (2) a combined DINO + iBOT self-distillation loss, (3) **register tokens** to eliminate artifact patches in attention maps, and (4) engineering improvements enabling efficient training of 1.1B parameter models. DINOv2 features exhibit emergent depth estimation, segmentation, and retrieval capabilities without any labeled data.

- LVD-142M dataset: web-crawled images filtered by a custom deduplication + retrieval pipeline; no manual labels
- Image retrieval: SSCD descriptors used to retrieve images similar to curated seeds (ImageNet, ADE20K, COCO, etc.)
- Deduplication: self-supervised hash-based dedup followed by cosine similarity filtering to remove near-duplicates
- Training objective: combines **DINO** (self-distillation of CLS tokens) + **iBOT** (patch-level MIM with online tokenizer)
- DINO loss: student CLS token matches teacher CLS token via centering + sharpening (temperature softmax)
- iBOT loss: student masked patch tokens predict teacher unmasked patch tokens; online teacher updated via EMA
- Teacher network: exponential moving average of student weights; not trained directly
- **Register tokens**: 4 extra learnable tokens appended to sequence; absorb "artifact" global information that otherwise contaminates patch tokens, improving dense prediction
- KoLeo regularization: encourages uniform distribution of CLS features in embedding space; prevents feature collapse
- Training stabilization: gradient clipping, LayerScale initialization, short warm-up then cosine decay
- DINOv2-g: 1.1B params, ViT-g/14 architecture, 1536-dim embedding, 24 attention heads, d_head=64
- Linear probing: 86.5% on ImageNet-1K with frozen backbone; surpasses all prior self-supervised methods
- k-NN classification: 83.5% with k=20 — pure retrieval without any training
- Depth estimation: DPT head on frozen DINOv2 outperforms fine-tuned supervised ViT on NYUd and KITTI
- Semantic segmentation: linear head on frozen backbone achieves 53.0 mIoU on ADE20K
- Video object segmentation on DAVIS: 86.2% J&F with frozen backbone + nearest-neighbor propagation
- Attention maps are semantically coherent: clear object segmentation visible in raw attention without supervision
- Register tokens specifically improve dense tasks by removing high-norm artifact patches observed in ViT attention

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ DINOv2 — Self-Distillation Training Loop</div>
  <div style="display:flex;flex-direction:column;gap:10px;">

    <div style="display:flex;gap:12px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#DCFCE7;border:1px solid #22C55E;border-radius:8px;padding:10px 14px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#15803D;">Student ViT</div>
        <div style="color:#6B7280;font-size:11px;">Trained by backprop</div>
      </div>
      <div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:8px;padding:10px 14px;font-size:12px;text-align:center;">
        <div style="font-weight:700;color:#4F46E5;">Teacher ViT</div>
        <div style="color:#6B7280;font-size:11px;">EMA of student</div>
      </div>
    </div>

    <div style="display:flex;gap:12px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;">DINO Loss</div>
        <div style="color:#6B7280;">CLS_student → CLS_teacher</div>
        <div style="color:#6B7280;">centering + sharpening</div>
      </div>
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;">iBOT Loss</div>
        <div style="color:#6B7280;">masked patch_student → unmasked patch_teacher</div>
        <div style="color:#6B7280;">online visual tokenizer</div>
      </div>
      <div style="background:#fff;border:1px solid #C7D2FE;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;">KoLeo Reg.</div>
        <div style="color:#6B7280;">uniform feature distribution</div>
      </div>
    </div>

    <div style="text-align:center;color:#6366F1;">↓ EMA update: teacher ← m·teacher + (1-m)·student</div>

    <div style="display:flex;gap:8px;justify-content:center;flex-wrap:wrap;">
      <div style="background:#F0FDF4;border:1px solid #22C55E;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;color:#15803D;">Register tokens ×4</div>
        <div style="color:#6B7280;">absorb artifact patches</div>
      </div>
      <div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;color:#4F46E5;">CLS token</div>
        <div style="color:#6B7280;">global representation</div>
      </div>
      <div style="background:#E0F2FE;border:1px solid #0EA5E9;border-radius:6px;padding:8px 12px;font-size:11px;text-align:center;">
        <div style="font-weight:600;color:#0369A1;">Patch tokens</div>
        <div style="color:#6B7280;">dense representations</div>
      </div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** DINOv2 is widely regarded as the first self-supervised ViT model that genuinely replaces supervised pre-training for most downstream vision tasks. Its frozen-backbone linear probe results shocked the community — a feat previously only achieved through supervised training on labeled datasets orders of magnitude larger. The dataset curation pipeline (LVD-142M) is considered as important as the training objective, demonstrating that data quality and diversity matter as much as architectural innovation. Register tokens solved a long-standing artifact problem in ViT attention maps and are now routinely adopted in production vision Transformers.

### DINOv2 Model Variants

| Variant | Patch | d_model | Layers | Heads | Params | IN-1K Linear | IN-1K kNN | ADE20K Lin. |
|---|---|---|---|---|---|---|---|---|
| DINOv2-S/14 | 14 | 384 | 12 | 6 | 22M | 81.1% | 79.0% | 39.3 |
| DINOv2-B/14 | 14 | 768 | 12 | 12 | 86M | 84.5% | 82.1% | 45.5 |
| DINOv2-L/14 | 14 | 1024 | 24 | 16 | 307M | 86.3% | 83.5% | 52.1 |
| DINOv2-g/14 | 14 | 1536 | 40 | 24 | 1.1B | 86.5% | 83.5% | 53.0 |
| DINOv2-g/14+reg | 14 | 1536 | 40 | 24 | 1.1B | 86.7% | — | 54.0 |

<details>
<summary><strong>Key Industry Ideas Incorporated into DINOv2</strong></summary>

- **DINO self-distillation** (Caron et al., 2021): student-teacher framework with EMA teacher, centering and sharpening to prevent collapse; applied to CLS token representations
- **iBOT masked patch modeling** (Zhou et al., 2021): patch-level MIM using online tokenizer (teacher as tokenizer); combined with DINO for richer signal
- **EMA teacher** (Mean Teacher, Tarvainen & Valpola, 2017): teacher weights are exponential moving average of student; more stable than stop-gradient alone
- **Curated web data** (JFT-style): large-scale web data curation pipeline with automatic deduplication and seed-based retrieval; LVD-142M achieves ImageNet-class coverage without manual annotation
- **Register tokens**: extra non-patch tokens that absorb global/artifact information; cleanly separate local feature learning in patch tokens from global context
- **KoLeo regularization** (Sablayrolles et al., 2018): entropy-based spread regularizer encourages feature space to be uniformly occupied; prevents embedding collapse
- **Flash Attention** (Dao et al., 2022): used for efficient attention computation during training of 1.1B model; enables 40-layer 1536-dim ViT training at scale
- **LayerScale** (Touvron et al., 2021): per-layer learnable scalar multipliers on residual contributions; enables training of very deep ViTs (40+ layers) without instability

</details>

---

## 6. EVA-02 — Exploring the Limits of Masked Visual Representation Learning at Scale

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Mar 2023</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 304M – 18B params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 89.6% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ BAAI / Tsinghua</div>
</div>
{:/nomarkdown}

EVA-02 is a high-performance vision Transformer that fuses CLIP-style vision-language pre-training with masked image modeling. The architecture upgrades ViT with three key components: **SwiGLU** FFN (borrowed from LLaMA/PaLM), **2D Rotary Position Embeddings (RoPE-2D)** (replacing 1D learnable embeddings), and **Sub-LayerNorm** before projection layers. Pre-training uses CLIP image features as reconstruction targets (MIM with CLIP targets, following EVA), then fine-tunes on a merged labeled dataset. EVA-02-L achieves 89.6% on ImageNet-1K with only 304M parameters, establishing a new efficiency frontier.

- Architecture additions over standard ViT: SwiGLU FFN, RoPE-2D positional encoding, Sub-LayerNorm
- **SwiGLU FFN**: `FFN(x) = (xW₁ ⊙ Swish(xW₃)) · W₂`; 3 weight matrices, 2/3× hidden dim vs standard FFN to keep params equal
- **RoPE-2D**: rotary positional encodings applied along both H and W axes independently; enables zero-shot resolution generalization without interpolation artifacts
- **Sub-LayerNorm**: extra LayerNorm on Q and K projections inside attention; dramatically improves training stability at scale
- MIM pre-training target: frozen CLIP-L/14 features (768-dim) at corresponding patch positions; model predicts CLIP feature vectors, not pixel values
- Pre-training: EVA-02 pre-trained on merged Conceptual Captions + LAION-400M + ImageNet-21K (dubbed Merged-30M)
- Decoder: 6-layer lightweight Transformer that projects encoder features to CLIP feature space
- Fine-tuning: two-stage — first ImageNet-21K (90 epochs), then ImageNet-1K (20 epochs)
- EVA-02-L/14 (304M): 89.6% Top-1 — best among models <1B params as of publication
- EVA-02-L/14↑448: 90.0% Top-1 with 448×448 fine-tuning resolution
- EVA-02-Ti/16 (5.7M): 80.7% — highly efficient small model with modern architecture
- COCO object detection: EVA-02-L with Cascade Mask R-CNN: 64.5 AP_box — new COCO record
- ADE20K segmentation: EVA-02-L with UperNet: 62.3 mIoU with test-time augmentation
- RoPE-2D generalizes to arbitrary input resolutions; crucial for detection/segmentation without re-training positional embeddings
- SwiGLU provides ~1% accuracy improvement over standard GELU FFN with same parameter count
- Sub-LayerNorm enables training larger models with higher learning rates; gradient norms remain bounded

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ EVA-02 — Architectural Enhancements</div>
  <div style="display:flex;gap:12px;flex-wrap:wrap;justify-content:center;">

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:12px 16px;font-size:12px;min-width:160px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:8px;">Standard ViT FFN</div>
      <div style="color:#374151;font-family:monospace;font-size:11px;">x → Linear(D, 4D)<br/>→ GELU<br/>→ Linear(4D, D)</div>
    </div>

    <div style="align-self:center;color:#6366F1;font-size:24px;">⟹</div>

    <div style="background:#EEF2FF;border:2px solid #6366F1;border-radius:8px;padding:12px 16px;font-size:12px;min-width:180px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:8px;">EVA-02 SwiGLU FFN</div>
      <div style="color:#374151;font-family:monospace;font-size:11px;">gate = Swish(xW₃)<br/>x → Linear(D, 2/3·4D) ⊙ gate<br/>→ Linear(2/3·4D, D)</div>
    </div>

    <div style="background:#E0F2FE;border:2px solid #0EA5E9;border-radius:8px;padding:12px 16px;font-size:12px;min-width:160px;">
      <div style="font-weight:700;color:#0369A1;margin-bottom:8px;">RoPE-2D</div>
      <div style="color:#374151;font-size:11px;">Rotate Q, K by<br/>2D grid position<br/>(row, col) angles</div>
    </div>

    <div style="background:#F0FDF4;border:2px solid #22C55E;border-radius:8px;padding:12px 16px;font-size:12px;min-width:160px;">
      <div style="font-weight:700;color:#15803D;margin-bottom:8px;">Sub-LayerNorm</div>
      <div style="color:#374151;font-size:11px;">LayerNorm on Q and K<br/>before attention scores<br/>stabilizes large scale</div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** EVA-02 demonstrated that architectural modernization of ViT (SwiGLU + RoPE-2D + Sub-LN) yields significant accuracy gains even without increasing parameters, and that CLIP feature targets for MIM provide richer pre-training signal than pixel values. The community noted that RoPE-2D is especially valuable for detection and segmentation tasks where the model encounters images at resolutions different from pre-training — a major pain point with learnable 1D positional embeddings. EVA-02 also proved that 300M-parameter models can compete with billion-parameter models when the architecture and pre-training recipe are carefully optimized.

### EVA-02 Model Variants

| Variant | Patch | d_model | Layers | Heads | FFN | Params | IN-1K Top-1 |
|---|---|---|---|---|---|---|---|
| EVA-02-Ti/16 | 16 | 192 | 12 | 3 | SwiGLU | 5.7M | 80.7% |
| EVA-02-S/16 | 16 | 384 | 12 | 6 | SwiGLU | 22M | 85.7% |
| EVA-02-B/16 | 16 | 768 | 12 | 12 | SwiGLU | 86M | 87.3% |
| EVA-02-L/14 | 14 | 1024 | 24 | 16 | SwiGLU | 304M | 89.6% |
| EVA-02-L/14↑448 | 14 | 1024 | 24 | 16 | SwiGLU | 304M | 90.0% |
| EVA-CLIP-18B | 14 | 5120 | 24 | 64 | SwiGLU | 18B | 80.7% (0-shot) |

<details>
<summary><strong>Key Industry Ideas Incorporated into EVA-02</strong></summary>

- **SwiGLU** (Shazeer, 2020; used in PaLM, LLaMA): gated activation function; empirically outperforms GELU/ReLU in Transformer FFNs by up to 1% on downstream tasks
- **Rotary Position Embeddings (RoPE)** (Su et al., 2021): encodes position by rotating Q/K vectors; relative position naturally encoded in dot products; avoids extrapolation issues of absolute embeddings
- **2D extension of RoPE**: applies independent 1D RoPE along H and W spatial dimensions; enables resolution-agnostic attention for vision tasks
- **CLIP vision features as MIM targets** (EVA, Fang et al., 2022): predicting CLIP embeddings provides semantic supervision richer than pixel values; bridges text-image alignment and MIM objectives
- **Sub-LayerNorm** (Beit v2 / PaLM inspiration): normalizing Q and K before attention computation stabilizes training at large batch sizes and learning rates; critical for 18B EVA-CLIP
- **Masked Image Modeling with offline teacher**: frozen CLIP-L/14 as feature extractor for targets; decoupled from student training, reduces GPU memory requirements
- **Merged-30M training data**: combines Conceptual Captions 3M/12M, COCO, ADE20K, LAION-400M subsets, ImageNet-21K; ensures diverse coverage of concept types
- **Two-stage fine-tuning**: ImageNet-21K warm-up then ImageNet-1K refinement; reduces overfitting for large models while utilizing abundant weakly-labeled data

</details>

---

## 7. ViT-22B — Scaling Vision Transformers to 22 Billion Parameters

{::nomarkdown}
<div style="display:flex;flex-wrap:wrap;gap:8px;margin-bottom:20px;">
  <div style="display:inline-flex;align-items:center;background:#EEF2FF;border:1px solid #6366F1;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#4F46E5;">📅 Feb 2023</div>
  <div style="display:inline-flex;align-items:center;background:#E0F2FE;border:1px solid #0EA5E9;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#0369A1;">🔢 21.7B params</div>
  <div style="display:inline-flex;align-items:center;background:#F0FDF4;border:1px solid #22C55E;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#15803D;">🏆 89.5% ImageNet Top-1</div>
  <div style="display:inline-flex;align-items:center;background:#FFF7ED;border:1px solid #F97316;border-radius:9999px;padding:4px 12px;font-size:13px;font-weight:600;color:#C2410C;">🏛️ Google DeepMind</div>
</div>
{:/nomarkdown}

ViT-22B is the largest dense vision Transformer ever trained, pushing the ViT paradigm to 21.7 billion parameters on JFT-4B (4 billion labeled images). The model introduces **parallel attention+MLP blocks** (attention and FFN computed in parallel and summed, not sequentially), **QK-norm** for training stability, and fixes `head_dim=64` regardless of model scale. Remarkably, scaling to 22B parameters delivers consistent improvements across 2D image, 3D video, depth estimation, and multi-modal tasks. It also exhibits emergent **few-shot** and **fairness** properties not present in smaller variants.

- Architecture: d=6144, 48 layers, 96 heads, head_dim=64 (fixed), FFN dim=24576; patch_size=14
- **Parallel blocks**: `output = x + Attn(LN(x)) + FFN(LN(x))` — attention and FFN applied to same input, summed; reduces sequential depth and improves hardware utilization
- **QK-norm**: LayerNorm applied to Q and K projections before attention; prevents attention entropy collapse ("attention sink") at very large scale
- **Fixed head_dim=64**: keeps memory and compute per head constant as model scales; enables systematic scaling of both depth and width
- Pre-training data: JFT-4B — 4 billion images with ~30,000 classes, semi-noisy labels from hierarchical classifiers
- Training: adafactor optimizer; gradient clipping; model parallelism across 2048 TPUv4 chips
- Performance at ImageNet: 89.5% Top-1 — competitive with best specialist models; linear probe: 86.7%
- Few-shot learning: ViT-22B with frozen features shows emergent 5-shot learning ability not present in ViT-H; scaling directly enables few-shot transfer
- Video (Kinetics-400): 92.2% Top-1 with fine-tuning — sets new record at time of publication
- Depth estimation (NYUd): 0.239 RMSE, competitive with dedicated depth networks
- Fairness: ViT-22B shows improved demographic parity across skin tone and gender attributes relative to smaller ViTs, suggesting scale reduces bias in representation
- Model parallelism: tensor parallelism (weight-split) + pipeline parallelism (stage-split) across TPU pods
- QK-norm is critical at this scale: without it, attention weights collapse to near-uniform in deeper layers due to large Q/K dot products
- Parallel blocks enable 13–15% faster forward pass on hardware due to better pipeline parallelism opportunities
- Context length at pre-training: 256×256 image → P=14 → 331 patches + 1 CLS = 332 tokens (manageable quadratic cost)

{::nomarkdown}
<div style="background:#EEF2FF;border:1px solid #6366F1;border-radius:12px;padding:20px;margin-bottom:20px;">
  <div style="font-weight:700;color:#4F46E5;font-size:15px;margin-bottom:16px;">🏗️ ViT-22B — Parallel Block Architecture</div>
  <div style="display:flex;flex-direction:column;gap:10px;align-items:center;">

    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;width:280px;">
      <div style="font-weight:600;">Input x</div>
    </div>

    <div style="display:flex;gap:20px;flex-wrap:wrap;justify-content:center;">
      <div style="display:flex;flex-direction:column;gap:6px;align-items:center;">
        <div style="color:#6366F1;font-size:14px;">↓</div>
        <div style="background:#EEF2FF;border:2px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:700;color:#4F46E5;">LayerNorm</div>
        </div>
        <div style="color:#6366F1;font-size:14px;">↓</div>
        <div style="background:#EEF2FF;border:2px solid #6366F1;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:700;color:#4F46E5;">MHSA + QK-norm</div>
          <div style="font-size:11px;color:#6B7280;">head_dim=64 fixed</div>
        </div>
      </div>

      <div style="align-self:center;font-size:20px;color:#6366F1;">‖</div>

      <div style="display:flex;flex-direction:column;gap:6px;align-items:center;">
        <div style="color:#6366F1;font-size:14px;">↓</div>
        <div style="background:#E0F2FE;border:2px solid #0EA5E9;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:700;color:#0369A1;">LayerNorm</div>
        </div>
        <div style="color:#0EA5E9;font-size:14px;">↓</div>
        <div style="background:#E0F2FE;border:2px solid #0EA5E9;border-radius:8px;padding:8px 14px;font-size:12px;text-align:center;">
          <div style="font-weight:700;color:#0369A1;">FFN (GELU)</div>
          <div style="font-size:11px;color:#6B7280;">4× hidden dim</div>
        </div>
      </div>
    </div>

    <div style="color:#6366F1;font-size:14px;">↓ sum</div>

    <div style="background:#F0FDF4;border:1px solid #22C55E;border-radius:8px;padding:10px 18px;font-size:12px;text-align:center;width:280px;">
      <div style="font-weight:600;color:#15803D;">x + Attn(LN(x)) + FFN(LN(x))</div>
    </div>

  </div>
</div>
{:/nomarkdown}

**Community Perspective:** ViT-22B proved that pure ViT scaling remains highly effective well into the tens-of-billions parameter regime, challenging the assumption that mixture-of-experts or specialized architectures are necessary for extreme scale. The parallel block design was particularly influential — it has been adopted in several subsequent vision foundation models as it improves hardware efficiency without sacrificing accuracy. The emergent few-shot capabilities and improved fairness metrics at 22B parameters provided important evidence that model scale has positive side effects beyond raw accuracy, fueling continued investment in large-scale vision pre-training infrastructure.

### ViT-22B Architecture Specifications

| Specification | Value |
|---|---|
| Total Parameters | 21.7B |
| d_model | 6144 |
| Number of Layers | 48 |
| Attention Heads | 96 |
| Head Dimension (fixed) | 64 |
| FFN Dimension | 24576 |
| Patch Size | 14 |
| Block Type | Parallel Attn+FFN |
| Positional Encoding | Learnable 2D |
| QK-Normalization | Yes (LayerNorm) |
| Pre-training Dataset | JFT-4B |
| Training Hardware | 2048 × TPUv4 |

<details>
<summary><strong>Key Industry Ideas Incorporated into ViT-22B</strong></summary>

- **Parallel Transformer blocks** (Wang et al., 2022, PaLM): simultaneous attention + FFN computation; reduces sequential depth for hardware pipeline efficiency; adds residual contributions independently
- **QK-normalization**: prevents attention entropy collapse (attention weights becoming too uniform or too peaked) at large scale; stabilizes training without learning rate reduction
- **Fixed head_dim=64**: systematic scaling without changing per-head compute; ensures attention heads always have sufficient capacity regardless of width/depth scaling
- **Adafactor optimizer** (Shazeer & Stern, 2018): memory-efficient second-moment estimation; critical for 22B model training where Adam would require ~44GB just for optimizer state
- **Model parallelism** (Megatron-LM, Shoeybi et al., 2019): combined tensor + pipeline parallelism across TPU pods; essential for fitting 22B parameters across distributed accelerators
- **JFT-4B** (Zhai et al., 2022): 4B semi-automatically labeled internet images; hierarchical label taxonomy with 30,000 classes; noisy but diverse
- **Scaling law analysis** (Zhai et al., 2022): systematic study of ViT performance vs compute budget; 22B model lies on predicted Pareto frontier of accuracy vs FLOPs
- **Emergent few-shot transfer**: at sufficient scale, frozen ViT features enable competitive k-shot classification without task-specific fine-tuning — analogous to GPT-3's emergent in-context learning

</details>

---

## 🏗️ ViT Master Architecture — Full Forward Pass

{::nomarkdown}
<div style="background:#EEF2FF;border:2px solid #4F46E5;border-radius:16px;padding:24px;margin-bottom:28px;">
  <div style="font-weight:800;color:#4F46E5;font-size:17px;margin-bottom:18px;text-align:center;">Vision Transformer — Complete Forward Pass</div>

  <div style="display:flex;flex-direction:column;gap:12px;">

    <!-- Stage 1: Input -->
    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:10px;padding:14px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:8px;">① Input Preprocessing</div>
      <div style="display:flex;gap:8px;flex-wrap:wrap;align-items:center;font-size:12px;">
        <div style="background:#F3F4F6;padding:6px 10px;border-radius:6px;font-family:monospace;">Image [B, 3, H, W]</div>
        <div style="color:#6366F1;">→ normalize →</div>
        <div style="background:#F3F4F6;padding:6px 10px;border-radius:6px;font-family:monospace;">Patches [B, N, P²·C]</div>
        <div style="color:#6366F1;">where N=(H·W)/(P²)</div>
      </div>
    </div>

    <!-- Stage 2: Patch Embedding -->
    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:10px;padding:14px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:8px;">② Patch Embedding</div>
      <div style="font-size:12px;color:#374151;">
        Linear projection W_E ∈ ℝ^(P²C × D): <code>[B, N, P²C] → [B, N, D]</code><br/>
        Prepend CLS token (learned): <code>[B, N+1, D]</code><br/>
        Add positional embedding E_pos ∈ ℝ^((N+1) × D): element-wise add
      </div>
    </div>

    <!-- Stage 3: Transformer Encoder -->
    <div style="background:#EEF2FF;border:2px solid #6366F1;border-radius:10px;padding:14px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:10px;">③ Transformer Encoder Block (repeated L times)</div>
      <div style="display:flex;gap:8px;flex-wrap:wrap;font-size:12px;">
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 12px;">
          <div style="font-weight:600;">Pre-LN</div>
          <div style="font-family:monospace;font-size:11px;">z = LN(x)</div>
        </div>
        <div style="align-self:center;color:#6366F1;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 12px;">
          <div style="font-weight:600;">Multi-Head Attn</div>
          <div style="font-family:monospace;font-size:11px;">Q=zW_Q, K=zW_K, V=zW_V<br/>A=softmax(QKᵀ/√d_k)V<br/>out=concat(heads)W_O</div>
        </div>
        <div style="align-self:center;color:#6366F1;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 12px;">
          <div style="font-weight:600;">Residual</div>
          <div style="font-family:monospace;font-size:11px;">x = x + out</div>
        </div>
        <div style="align-self:center;color:#6366F1;">→</div>
        <div style="background:#fff;border:1px solid #A5B4FC;border-radius:6px;padding:8px 12px;">
          <div style="font-weight:600;">FFN</div>
          <div style="font-family:monospace;font-size:11px;">LN(x)→Linear→GELU<br/>→Linear→Residual</div>
        </div>
      </div>
    </div>

    <!-- Stage 4: Output -->
    <div style="background:#fff;border:1px solid #C7D2FE;border-radius:10px;padding:14px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:8px;">④ Output Head (Classification)</div>
      <div style="font-size:12px;color:#374151;">
        Extract CLS token: <code>h_cls = x[:, 0, :]  # [B, D]</code><br/>
        Final LayerNorm: <code>h = LN(h_cls)</code><br/>
        Linear classifier: <code>logits = h @ W_cls.T  # [B, num_classes]</code>
      </div>
    </div>

    <!-- Tensor shapes summary -->
    <div style="background:#F8FAFF;border:1px dashed #6366F1;border-radius:8px;padding:12px;font-size:11px;font-family:monospace;color:#374151;">
      <div style="font-weight:700;color:#4F46E5;font-family:sans-serif;margin-bottom:6px;">Tensor Shape Flow (ViT-B/16, H=W=224):</div>
      Input:          [B, 3, 224, 224]<br/>
      After patches:  [B, 196, 768]          # N = (224/16)² = 196, D = 768<br/>
      After CLS:      [B, 197, 768]           # prepend CLS token<br/>
      After pos emb:  [B, 197, 768]           # element-wise add<br/>
      After L blocks: [B, 197, 768]           # shape invariant through encoder<br/>
      CLS extracted:  [B, 768]<br/>
      Logits:         [B, 1000]               # ImageNet-1K
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 💻 Code Examples

### 1. Patch Embedding — NumPy Reference Implementation

```python
import numpy as np

def patch_embed(image: np.ndarray, patch_size: int = 16, d_model: int = 768) -> np.ndarray:
    """
    Convert image to patch token sequence.
    
    Args:
        image: [H, W, C] float array
        patch_size: size of each square patch (P)
        d_model: target embedding dimension (D)
    
    Returns:
        tokens: [N, D] where N = (H/P) * (W/P)
    """
    H, W, C = image.shape
    assert H % patch_size == 0 and W % patch_size == 0
    
    # Reshape: [H, W, C] → [N, P*P*C]
    n_h, n_w = H // patch_size, W // patch_size
    # [H, W, C] → [n_h, P, n_w, P, C] → [n_h, n_w, P, P, C] → [N, P²C]
    patches = image.reshape(n_h, patch_size, n_w, patch_size, C)
    patches = patches.transpose(0, 2, 1, 3, 4)          # [n_h, n_w, P, P, C]
    patches = patches.reshape(n_h * n_w, -1)             # [N, P²·C]
    
    # Linear projection W_E: [P²C, D]
    W_E = np.random.randn(patch_size * patch_size * C, d_model) * 0.02
    tokens = patches @ W_E                                # [N, D]
    return tokens                                         # → [196, 768] for ViT-B/16 224×224
```

### 2. Multi-Head Self-Attention — PyTorch

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class MultiHeadSelfAttention(nn.Module):
    """Standard ViT Multi-Head Self-Attention."""
    
    def __init__(self, d_model: int = 768, num_heads: int = 12, dropout: float = 0.0):
        super().__init__()
        assert d_model % num_heads == 0
        self.num_heads = num_heads
        self.d_head = d_model // num_heads   # 64 for ViT-B
        self.scale = math.sqrt(self.d_head)
        
        self.qkv = nn.Linear(d_model, 3 * d_model, bias=True)
        self.proj = nn.Linear(d_model, d_model)
        self.attn_drop = nn.Dropout(dropout)
    
    def forward(self, x: torch.Tensor) -> torch.Tensor:
        """
        Args:
            x: [B, N+1, D]  (N patches + 1 CLS token)
        Returns:
            out: [B, N+1, D]
        """
        B, T, D = x.shape                                   # T = N+1 = 197 for ViT-B/16@224
        
        qkv = self.qkv(x)                                   # [B, T, 3D]
        qkv = qkv.reshape(B, T, 3, self.num_heads, self.d_head)
        qkv = qkv.permute(2, 0, 3, 1, 4)                   # [3, B, H, T, d_head]
        q, k, v = qkv.unbind(0)                             # each: [B, H, T, d_head]
        
        # Scaled dot-product attention: O(T²·d_head·H) = O(N²·D)
        attn = (q @ k.transpose(-2, -1)) / self.scale       # [B, H, T, T]
        attn = F.softmax(attn, dim=-1)                       # normalize over key dim
        attn = self.attn_drop(attn)
        
        out = attn @ v                                       # [B, H, T, d_head]
        out = out.transpose(1, 2).reshape(B, T, D)          # [B, T, D]
        return self.proj(out)                                # [B, T, D]
```

### 3. ViT Block — Full Transformer Encoder Layer

```python
class ViTBlock(nn.Module):
    """Single ViT Transformer Encoder Block (Pre-LN)."""
    
    def __init__(self, d_model: int = 768, num_heads: int = 12,
                 mlp_ratio: float = 4.0, dropout: float = 0.0):
        super().__init__()
        self.norm1 = nn.LayerNorm(d_model)
        self.attn = MultiHeadSelfAttention(d_model, num_heads, dropout)
        self.norm2 = nn.LayerNorm(d_model)
        
        ffn_dim = int(d_model * mlp_ratio)                  # 3072 for ViT-B
        self.ffn = nn.Sequential(
            nn.Linear(d_model, ffn_dim),
            nn.GELU(),
            nn.Dropout(dropout),
            nn.Linear(ffn_dim, d_model),
            nn.Dropout(dropout),
        )
    
    def forward(self, x: torch.Tensor) -> torch.Tensor:
        """
        Args:
            x: [B, T, D]  — T = N+1 tokens (patches + CLS)
        Returns:
            x: [B, T, D]  — shape invariant
        """
        # Pre-LN attention with residual
        x = x + self.attn(self.norm1(x))                    # [B, T, D]
        # Pre-LN FFN with residual
        x = x + self.ffn(self.norm2(x))                     # [B, T, D]
        return x
```

### 4. Positional Embedding Interpolation (for higher-res fine-tuning)

```python
import torch
import torch.nn.functional as F

def interpolate_pos_embed(pos_embed: torch.Tensor,
                           orig_grid: tuple, new_grid: tuple) -> torch.Tensor:
    """
    Bicubic interpolation of ViT positional embeddings for resolution change.
    
    Critical for fine-tuning ViT pre-trained at 224×224 on higher-res inputs (384×384).
    
    Args:
        pos_embed:  [1, N+1, D] — original positional embeddings (N = orig_h * orig_w)
        orig_grid:  (orig_h, orig_w) — e.g., (14, 14) for 224/16
        new_grid:   (new_h, new_w)  — e.g., (24, 24) for 384/16
    
    Returns:
        new_pos_embed: [1, M+1, D] — M = new_h * new_w
    """
    cls_token = pos_embed[:, :1, :]                          # [1, 1, D] — keep as-is
    patch_embed = pos_embed[:, 1:, :]                        # [1, N, D]
    
    orig_h, orig_w = orig_grid
    new_h, new_w = new_grid
    D = pos_embed.shape[-1]
    
    # Reshape to spatial grid for interpolation
    patch_embed = patch_embed.reshape(1, orig_h, orig_w, D)
    patch_embed = patch_embed.permute(0, 3, 1, 2)            # [1, D, orig_h, orig_w]
    
    # Bicubic interpolation to new spatial resolution
    patch_embed = F.interpolate(
        patch_embed,
        size=(new_h, new_w),
        mode='bicubic',
        align_corners=False
    )                                                         # [1, D, new_h, new_w]
    
    patch_embed = patch_embed.permute(0, 2, 3, 1)            # [1, new_h, new_w, D]
    patch_embed = patch_embed.reshape(1, new_h * new_w, D)   # [1, M, D]
    
    return torch.cat([cls_token, patch_embed], dim=1)        # [1, M+1, D]

# Example: ViT-B/16 from 224 → 384
# orig: (224/16)=14, new: (384/16)=24
# pos_embed [1, 197, 768] → interpolated [1, 577, 768]
```

### 5. DINOv2 Feature Extraction — Production Usage

```python
import torch
from torchvision import transforms

def extract_dinov2_features(images: torch.Tensor,
                              model_name: str = 'dinov2_vitl14') -> dict:
    """
    Extract DINOv2 features (CLS + patch tokens) for downstream tasks.
    
    Args:
        images:      [B, 3, H, W] float tensor, normalized to ImageNet stats
        model_name:  one of dinov2_vits14, dinov2_vitb14, dinov2_vitl14, dinov2_vitg14
    
    Returns:
        dict with keys:
            'cls':    [B, D]       — global image representation
            'patch':  [B, N, D]    — dense spatial features (N = (H/14)²)
    """
    model = torch.hub.load('facebookresearch/dinov2', model_name)
    model.eval()
    
    with torch.no_grad():
        # forward_features returns dict with 'x_norm_clstoken', 'x_norm_patchtokens'
        features = model.forward_features(images)
    
    return {
        'cls': features['x_norm_clstoken'],      # [B, 1024] for ViT-L/14
        'patch': features['x_norm_patchtokens'],  # [B, N, 1024] dense features
    }

# Preprocessing for DINOv2
preprocess = transforms.Compose([
    transforms.Resize(224, interpolation=transforms.InterpolationMode.BICUBIC),
    transforms.CenterCrop(224),
    transforms.ToTensor(),
    transforms.Normalize(mean=[0.485, 0.456, 0.406],
                         std=[0.229, 0.224, 0.225]),  # ImageNet stats
])
```

### 6. MAE Masking Strategy

```python
import torch

def random_masking(x: torch.Tensor, mask_ratio: float = 0.75) -> tuple:
    """
    Random masking for MAE — keep only (1-mask_ratio) fraction of patches.
    
    Args:
        x:           [B, N, D]  — full sequence of patch embeddings
        mask_ratio:  fraction of patches to mask (default 0.75 for MAE)
    
    Returns:
        x_masked:    [B, N_keep, D]    — visible patches only
        mask:        [B, N]            — binary mask (1=masked, 0=visible)
        ids_restore: [B, N]            — indices to restore original order
    """
    B, N, D = x.shape
    N_keep = int(N * (1 - mask_ratio))                      # e.g., 49 of 196 patches
    
    # Random shuffle — generate per-sample random permutations
    noise = torch.rand(B, N, device=x.device)               # [B, N] uniform noise
    ids_shuffle = noise.argsort(dim=1)                       # [B, N] sorted indices
    ids_restore = ids_shuffle.argsort(dim=1)                 # [B, N] restore mapping
    
    # Keep first N_keep indices (lowest noise = "visible")
    ids_keep = ids_shuffle[:, :N_keep]                       # [B, N_keep]
    x_masked = torch.gather(x, 1,
                 ids_keep.unsqueeze(-1).expand(-1, -1, D))   # [B, N_keep, D]
    
    # Binary mask: 1 = masked, 0 = visible
    mask = torch.ones(B, N, device=x.device)
    mask[:, :N_keep] = 0
    mask = torch.gather(mask, 1, ids_restore)                # [B, N] restored order
    
    return x_masked, mask, ids_restore                       # x_masked: [B, 49, D]
```

---

## 🚧 Training & Deployment Pitfalls

{::nomarkdown}
<div style="background:#FFF7ED;border:1px solid #F97316;border-radius:12px;padding:20px;margin-bottom:24px;">
  <div style="font-weight:700;color:#C2410C;font-size:15px;margin-bottom:16px;">⚠️ Common ViT Training & Deployment Pitfalls</div>

  <div style="display:flex;flex-direction:column;gap:12px;">

    <div style="background:#fff;border-left:4px solid #EF4444;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#DC2626;margin-bottom:4px;">1. Positional Embedding Resolution Mismatch</div>
      <div style="font-size:13px;color:#374151;">Fine-tuning at higher resolution than pre-training (e.g., 224→384) without interpolating positional embeddings causes severe accuracy degradation (up to 5%). Always bicubic-interpolate patch position embeddings when changing input resolution. For deployment flexibility, consider using RoPE-2D (EVA-02) which generalizes zero-shot.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #F97316;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#C2410C;margin-bottom:4px;">2. CLS Token vs. GAP for Dense Tasks</div>
      <div style="font-size:13px;color:#374151;">Using only the CLS token for segmentation or detection discards rich spatial information. For dense prediction tasks (segmentation, depth), always use patch tokens (possibly from multiple layers). DINOv2 patch tokens are directly usable; for MAE, fine-tune the full network. Global Average Pooling (GAP) of patch tokens often outperforms CLS for some tasks.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #EAB308;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#92400E;margin-bottom:4px;">3. Learning Rate and Batch Size Scaling</div>
      <div style="font-size:13px;color:#374151;">ViT is sensitive to LR × BatchSize scaling. The rule <code>lr = base_lr × batch_size / 256</code> applies; but for batch sizes >2048, linear scaling often breaks. Use warmup (e.g., 5 epochs) and cosine decay. For fine-tuning, use layer-wise LR decay (outer layers get lower LR) — standard in DeiT-III and EVA-02.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #22C55E;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#15803D;margin-bottom:4px;">4. Attention Sink / Artifact Patches</div>
      <div style="font-size:13px;color:#374151;">Large ViTs develop "artifact" patches with anomalously high attention norms that accumulate global information, corrupting dense task features. DINOv2 solves this with register tokens. For models without registers, you can identify artifact patches by their outlier norms and mask them in downstream heads. QK-norm (ViT-22B) also prevents related attention collapse at scale.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #6366F1;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#4F46E5;margin-bottom:4px;">5. Memory: O(N²) Attention</div>
      <div style="font-size:13px;color:#374151;">Self-attention scales quadratically with sequence length. ViT-L at 512×512 with P=16 has N=1024 tokens → 1M attention values per head. Use Flash Attention (Dao et al.) for 3–5× memory reduction and 2–4× speedup. For deployment at very high resolution, window attention (Swin-style) or linear attention variants may be necessary.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #A855F7;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#7C3AED;margin-bottom:4px;">6. Stochastic Depth Scheduling</div>
      <div style="font-size:13px;color:#374151;">DropPath rate should scale with model depth. A standard schedule is <code>drop_rate = depth_index / total_depth × max_drop</code> (linearly increasing from input to output). Max drop rates: 0.1 for ViT-B, 0.2 for ViT-L, 0.3+ for ViT-H. Too high drop rates cause underfitting; too low fail to regularize deep models.</div>
    </div>

    <div style="background:#fff;border-left:4px solid #0EA5E9;border-radius:4px;padding:12px;">
      <div style="font-weight:700;color:#0369A1;margin-bottom:4px;">7. Mixed-Precision Training Instabilities</div>
      <div style="font-size:13px;color:#374151;">Training ViT in FP16/BF16 can cause attention logit overflow at large scale (especially without QK-norm). Always prefer BF16 over FP16 for ViT (larger dynamic range, fewer NaN risks). Use gradient scaling for FP16. For ViT-22B-class models, QK-norm is essential — prevents softmax saturation that appears at scale in FP16/BF16 training.</div>
    </div>

  </div>
</div>
{:/nomarkdown}

---

## 🔢 Quantization

Vision Transformers present unique quantization challenges compared to CNNs: attention softmax outputs have heavy-tailed distributions, and LayerNorm's dynamic range varies significantly across layers. FP8 and INT8 quantization have been successfully applied to ViT with careful calibration.

### Post-Training Quantization (PTQ)

| Method | Bit-width | Accuracy Drop (ViT-B/16) | Notes |
|---|---|---|---|
| Naive INT8 | W8A8 | −2.1% | No calibration; unacceptable |
| SmoothQuant | W8A8 | −0.3% | Migrates quantization difficulty from activations to weights |
| FQ-ViT | W8A8 | −0.1% | Patch-token-aware calibration; log2 quantizer for softmax |
| FQ-ViT | W4A8 | −1.2% | 4-bit weights, 8-bit activations |
| GPTQ (ViT) | W4A16 | −0.5% | Weight-only quantization; no activation quantization |
| FP8 (E4M3) | W8A8 | −0.05% | Near-lossless; requires H100/Ada GPU support |

### Quantization-Aware Training (QAT)

QAT for ViT typically recovers 0.5–1.5% over PTQ. Key considerations:
- Use **straight-through estimator (STE)** for gradients through quantization ops
- Apply **lower learning rate** during QAT (10–100× less than full training LR)
- Quantize attention scores and softmax outputs separately — softmax requires log2 or power-of-2 quantization grids (FQ-ViT insight)
- LayerNorm parameters typically kept in FP32 (cheap, critical for accuracy)
- Mixed-precision: keep first and last layers in FP32/INT8; quantize middle layers to INT4

### Quantization Tools

| Tool | Ecosystem | Supported Precision | ViT Support |
|---|---|---|---|
| PyTorch `torch.ao.quantization` | PyTorch | INT8, FP16 | Static/Dynamic PTQ, QAT |
| NVIDIA TensorRT | NVIDIA GPU | INT8, FP8, INT4 | Full ViT support via ONNX |
| Hugging Face Optimum | HF + ONNX | INT8, INT4 | `AutoModelForImageClassification` |
| Intel Neural Compressor | CPU/GPU | INT8, INT4 | ViT/DeiT/Swin |
| bitsandbytes | PyTorch | INT8, NF4 | LLM-focused; adaptable to ViT |
| llm.int8() | PyTorch | INT8 | CLS + patch token decomposed quant |

---

## 📚 References

### Foundational Papers

| Paper | Authors | Venue | Key Contribution |
|---|---|---|---|
| An Image is Worth 16×16 Words | Dosovitskiy et al. | ICLR 2021 | Original ViT; patch tokenization for images |
| Training data-efficient image transformers | Touvron et al. | ICML 2021 | DeiT; distillation token; ImageNet-1K training |
| BEiT: BERT Pre-Training of Image Transformers | Bao et al. | ICLR 2022 | Masked image modeling with dVAE tokens |
| Masked Autoencoders Are Scalable Vision Learners | He et al. | CVPR 2022 | MAE; asymmetric encoder-decoder; 75% masking |
| DINOv2: Learning Robust Visual Features without Supervision | Oquab et al. | TMLR 2024 | Self-distillation + iBOT + curated LVD-142M |
| EVA-02: A Visual Representation Powerhouse | Fang et al. | arXiv 2023 | SwiGLU + RoPE-2D + CLIP-target MIM |
| Scaling Vision Transformers to 22 Billion Parameters | Dehghani et al. | ICML 2023 | ViT-22B; parallel blocks; QK-norm; JFT-4B |
| Emerging Properties in Self-Supervised Vision Transformers | Caron et al. | ICCV 2021 | DINO; self-distillation; semantic attention maps |
| iBOT: Image BERT Pre-Training with Online Tokenizer | Zhou et al. | ICLR 2022 | Patch-level MIM with EMA online tokenizer |

### Blog Posts & Tutorials

| Resource | Author/Org | Topic |
|---|---|---|
| [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) | Jay Alammar | Step-by-step Transformer walkthrough |
| [DINOv2 Blog](https://ai.meta.com/blog/dino-v2-computer-vision-self-supervised-learning/) | Meta AI | DINOv2 overview and results |
| [MAE Blog](https://ai.facebook.com/blog/self-supervised-learning-masked-autoencoders/) | Meta AI | MAE intuition and results |
| [ViT Tutorial (HuggingFace)](https://huggingface.co/docs/transformers/model_doc/vit) | Hugging Face | ViT API documentation and usage |
| [EVA Project Page](https://baaivision.github.io/eva-clip/) | BAAI | EVA/EVA-02 model cards and checkpoints |
| [Annotated ViT](https://pi-tau.github.io/posts/vit/) | pi-tau | Mathematical annotations of ViT |

### Key GitHub Repositories

| Repository | Organization | Description |
|---|---|---|
| [google-research/vision_transformer](https://github.com/google-research/vision_transformer) | Google | Original ViT JAX implementation |
| [facebookresearch/dino](https://github.com/facebookresearch/dino) | Meta AI | DINO PyTorch implementation |
| [facebookresearch/dinov2](https://github.com/facebookresearch/dinov2) | Meta AI | DINOv2 PyTorch + model hub |
| [facebookresearch/mae](https://github.com/facebookresearch/mae) | Meta AI | MAE PyTorch implementation |
| [microsoft/unilm/beit](https://github.com/microsoft/unilm/tree/master/beit) | Microsoft | BEiT PyTorch implementation |
| [baaivision/EVA](https://github.com/baaivision/EVA) | BAAI | EVA-02 PyTorch implementation |
| [rwightman/pytorch-image-models](https://github.com/huggingface/pytorch-image-models) | Hugging Face | timm: all ViT variants in one library |
| [huggingface/transformers](https://github.com/huggingface/transformers) | Hugging Face | ViT, DeiT, BEiT via AutoModel API |

### Cited Techniques

| Technique | Source Paper | Where Used in ViT Family |
|---|---|---|
| Multi-Head Self-Attention | Attention is All You Need (2017) | Core of all ViT variants |
| LayerNorm | Ba et al. (2016) | Pre-LN in all modern ViT |
| GELU Activation | Hendrycks & Gimpel (2016) | Standard ViT/DeiT FFN |
| SwiGLU | Shazeer (2020) | EVA-02, LLaMA |
| RoPE | Su et al. (2021) | EVA-02 2D extension |
| Knowledge Distillation | Hinton et al. (2015) | DeiT distillation token |
| Stochastic Depth | Huang et al. (2016) | DeiT, BEiT, EVA-02 |
| Flash Attention | Dao et al. (2022) | DINOv2-g, EVA-CLIP-18B |
| Mixup | Zhang et al. (2018) | DeiT training recipe |
| CutMix | Yun et al. (2019) | DeiT training recipe |
| Masked Language Modeling | Devlin et al. (2018) | BEiT, MAE conceptual basis |
| EMA Teacher | Tarvainen & Valpola (2017) | DINOv2, DeiT EMA |
| LayerScale | Touvron et al. (2021) | DINOv2, deep ViT training |
| QK-Normalization | Dehghani et al. (2023) | ViT-22B stability |
| Adafactor | Shazeer & Stern (2018) | ViT-22B optimizer |

---

<p align="center">
  <sub>
    Part of the <a href="index.html">AI Models Reference</a> · Vision Transformer (ViT) Family · 
    ViT · DeiT · BEiT · MAE · DINOv2 · EVA-02 · ViT-22B
  </sub>
</p>
