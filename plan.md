# 📋 Plan — Model Architecture Repository

> This document outlines the plan for building a comprehensive model architecture documentation repository.
> Follow this plan to create similar documentation for any model family.

---

## 🎯 Goal

Build a beautifully-formatted, GitHub Pages-ready markdown repository documenting major LLM families across all their generations. Each model family gets a single numbered markdown file (e.g., `01_Qwen.md`, `02_Llama.md`) that traces every version from inception to the latest release.

---

## 📁 Repository Structure

```
models/
├── README.md              # Landing page with table of model families
├── prompt.md              # Original prompt used to create this plan
├── plan.md                # This file
├── 01_Qwen.md             # Qwen model family (1 → 1.5 → 2 → 2.5 → 3)
├── 02_Llama.md            # (future) Llama model family
├── 03_DeepSeek.md         # (future) DeepSeek model family
├── 04_Gemma.md            # (future) Gemma model family
└── assets/                # Images downloaded from papers (fallback)
    ├── qwen/
    ├── llama/
    └── ...
```

---

## 📝 Document Template (Per Model Family)

Each markdown file follows this structure:

### 1. Header & Metadata
- Model family name, emoji badge, last-updated date
- Maintainer / original team

### 2. Table of Contents
- Clickable anchor links to every section
- Nested under version headings

### 3. Executive Summary
- How many versions exist
- What this document covers
- Cross-references to related model families (VL, Audio, Code variants)

### 4. Version Release Timeline Table
- Version, release date, paper link, parameter sizes, tokens trained, headline feature
- Styled with HTML/CSS for visual appeal

### 5. Cross-Version Benchmark Comparison Table
- Flagship model benchmarks across all versions
- Metrics: MMLU, HumanEval, MATH, GSM8K, context length, languages supported, training tokens

### 6. Master Architecture Diagram (HTML+CSS)
- Large visual showing shared Transformer decoder architecture
- Color-coded annotations for what changed per version
- Pure HTML+CSS `<div>` elements, no JavaScript

### 7. Per-Version Sections (repeat for each version)

Each version section contains:
- **Release date badge** (colored inline tag)
- **10 bullet-point summary** — architecture decisions, training scale, novel ideas, industry ideas incorporated
- **Architecture diagram** (HTML+CSS) highlighting what's NEW vs. previous generation
  - Green = new components, Gray = inherited
- **Community perspective** (3–5 bullets)
- **Model variants table** — all sizes (base, instruct, coder, math, MoE, etc.)
- **Key industry ideas incorporated** — cited techniques from other papers

### 8. References Table
- All sources: arXiv papers, blog posts, GitHub repos, HuggingFace collections
- Cited techniques with links

---

## 🎨 Design System

### Color Palette (per Qwen version)
| Version  | Color         | Hex       |
|----------|---------------|-----------|
| Qwen 1   | Violet        | `#7C3AED` |
| Qwen 1.5 | Light Violet  | `#8B5CF6` |
| Qwen 2   | Blue          | `#2563EB` |
| Qwen 2.5 | Cyan          | `#0891B2` |
| Qwen 3   | Emerald       | `#059669` |

### Diagram Rules
- Pure HTML+CSS `<div>` with inline styles
- No JavaScript, no external dependencies
- Rounded boxes (`border-radius: 8px`), subtle shadows
- Responsive widths (`max-width: 100%`)
- Dark backgrounds with white/light text for headers
- Inherited components: gray (`#6B7280`)
- New components: version-specific color

### Typography
- Bullet points preferred over paragraphs
- Tables for structured comparisons
- Inline code for technical terms (e.g., `SwiGLU`, `RoPE`, `GQA`)
- Emoji sparingly for section headers

---

## 🔄 Workflow (For Adding a New Model Family)

1. **Research** — Read all official technical papers and blog posts
2. **Catalog images** — Find all diagrams from papers; prefer hotlinking from official CDN
3. **Create file** — Copy template structure, assign version colors
4. **Fill content** — Work version by version, oldest to newest
5. **Build diagrams** — Create HTML+CSS architecture diagrams per version
6. **Add benchmarks** — Pull numbers directly from papers
7. **References** — Compile all sources at the end
8. **Review** — Proofread benchmark numbers, test HTML rendering, verify image URLs

---

## ✅ Completed

- [x] `01_Qwen.md` — Qwen 1, 1.5, 2, 2.5, 3

## 🔜 Planned

- [ ] `02_Llama.md` — Llama 1, 2, 3, 3.1, 3.2, 3.3, 4
- [ ] `03_DeepSeek.md` — DeepSeek V1, V2, V3, R1
- [ ] `04_Gemma.md` — Gemma 1, 2, 3
- [ ] `05_Mistral.md` — Mistral 7B, Mixtral, Large, Medium
- [ ] `06_Phi.md` — Phi 1, 1.5, 2, 3, 4
