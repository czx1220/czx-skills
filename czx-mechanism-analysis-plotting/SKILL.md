---
name: czx-mechanism-analysis-plotting
description: >
  Mechanism analysis plotting for ML/NLP research papers.
  Only triggered by explicit user invocation (e.g., /mechanism-analysis-plotting or direct request to use this skill). Do NOT auto-trigger.
  Covers: analyzing model internals (attention, hidden states, logits, PPL, entropy) from contrastive groups,
  creating publication-quality figures, exploring data to form mechanistic hypotheses, iterating on figure design with authors, and proposing intervention experiments.
  Full pipeline: author requirement → inference data collection → data exploration → hypothesis generation → author alignment → figure type selection → style polishing → naming/layout → intervention suggestions.
---

# Mechanism Analysis Plotting

## Language Rule

**All communication with the author must be in Chinese (中文).** This includes: status reports, questions, options, analysis, explanations, and all non-code output.

## Activation Notice

When this skill is triggered, immediately display:

```
========================================
  [Skill Activated] Mechanism Analysis Plotting
  Phase 0 → 8 workflow engaged.
  All decisions defer to the author.
  Currently at: Phase 0 — Author Requirement
========================================
```

Update the "Currently at" line at each phase transition.

## Core Principle: Author-Driven, Agent-Advisory

The author makes all decisions. The agent provides multiple options and suggestions, but **every choice defers to the author**. At every stage, clearly communicate:
- What you are doing and why
- What data you are using
- How you are visualizing it
- What claim/story each figure supports

Never proceed to the next phase without author confirmation.

## Workflow

### Phase 0: Author Requirement & Inference Data Collection

The workflow starts from the **author's analysis need**, not from existing data.

**Understand the author's goal:**
- What phenomenon or comparison does the author want to investigate?
- What are the contrastive groups? (e.g., correction vs compliance, success vs fail, with/without intervention)
- What is the input data? (prompt pairs, case sets, experimental conditions)

**Design and run inference to collect internal data.** Record as comprehensively as possible — it is much cheaper to save extra data during inference than to re-run later. Recommended data to extract:

Per-sample, per-layer:
- **Hidden states** (last token position, and optionally key token positions like payload region)
- **Attention weights** (full attention layers: last token → all positions, per head)
- **Logits** (final layer output, top-k token IDs and values)
- **Logit entropy** (scalar per sample)

Per-sample metadata:
- **Token spans**: which positions correspond to payload, context, system prompt, template, etc.
- **Sequence length**
- **Group label**: which contrastive group this sample belongs to
- **Outcome metric**: score, classification, or other quantitative outcome
- **Case ID / iteration index**: for tracking paired or multi-iteration samples
- **Category labels**: attack type, error type, disguise method, etc.

Storage format:
- `.npz` for large numerical arrays (attention, hidden states)
- `.json` for metadata (token spans, labels, scores)
- `.pt` for per-case PyTorch tensors (when structure varies per case)

Confirm with the author: "I will run inference on [N] cases, extracting [list of signals]. This will enable analysis of [potential directions]. Proceed?"

### Phase 1: Data Inventory

Inspect available intermediate data (`.pt`, `.json`, `.npz`, `.jsonl`, etc.). Report to the author:
- What variables are available (attention weights, hidden states, logits, PPL, entropy, etc.)
- Data shape and structure (per-layer, per-token, per-head, per-case)
- Contrastive group structure: binary (A vs B) or multi-group (success/partial/fail, etc.)
- Token span metadata if available (which positions correspond to payload, context, system prompt, etc.)
- Any missing or anomalous data

### Phase 2: Exploratory Plotting

Generate many exploratory plots from different analytical angles. Each plot should have a **title** during this phase (for easy communication with the author).

Produce an exploratory script where **each plot has its own dedicated plotting function** (not inline one-off code). All plotting functions live in a shared directory (e.g., `scripts/plot/` or `plots/`). This ensures every figure is reproducible and modifiable by adjusting its function.

Suggest multiple visualization directions, for example:
- Representation space: PCA/t-SNE of hidden states across layers
- Similarity: cosine similarity between group centroids across layers
- Separability: inter-group distance / intra-group spread per layer
- Attention patterns: per-layer attention to specific token regions (payload, context, etc.)
- Attention heatmaps: layer x head, per group or difference maps
- Output signals: PPL, entropy, logit differences, top-k token distributions
- Layer-wise trends: how any metric evolves across layers
- Per-head analysis: which heads show largest group differences
- Correlation: scatter of metric vs outcome variable (e.g., attention vs score)
- Per-category breakdowns: by attack type, disguise, error category, etc.

For each plot, explain to the author:
- What data it uses
- What it shows
- What potential claim it could support

### Phase 3: Hypothesis Generation

Based on exploratory plots, propose **multiple** mechanistic stories/hypotheses. Present each as:
- Story summary (1-2 sentences)
- Which plots support it
- What additional plots might strengthen or refute it

The author will:
- Select one hypothesis, OR
- Propose their own hypothesis after seeing the plots

### Phase 4: Author Alignment on Story

After the author confirms the story direction:
- Map out which figures are needed, each supporting one specific claim
- Identify gaps: what additional plots are needed
- Identify redundancy: which plots carry overlapping information
- Propose the full figure chain and get author confirmation

Always confirm: "We are now building figures for the story: [summary]. The chain is: Fig A shows X, Fig B shows Y, Fig C shows Z. Correct?"

### Phase 5: Figure Type Exploration

For each confirmed figure, after the author specifies **what information to present and what data to use**:
- Try multiple chart types and present options to the author with brief rationale

Common academic chart types for mechanism analysis:
- **Line + shaded CI**: per-layer trends with error bands (mean +/- SEM)
- **Violin + box + strip combo**: distribution comparison across groups
- **KDE density**: overlaid distributions with vertical mean lines
- **Heatmap**: layer x head attention, with highlighted cells for top-k
- **Scatter + regression**: metric correlation with outcome
- **Bar + error bar**: per-layer or per-region comparisons
- **Grouped strip/swarm**: categorical comparison with individual points

### Phase 6: Polish to Publication Quality

Apply academic style. See [references/style_guide.md](references/style_guide.md) for full specifications.

Key rules:
- **Exploration phase**: keep titles (for communication)
- **Final version**: remove titles (caption goes in LaTeX)
- Output both PNG (dpi>=150, for preview) and PDF (for paper)
- Each sub-figure is an **independent PDF file** (composition via LaTeX `subfigure`)
- Write a separate export script for final figures (distinct from the exploratory script)
- **Each final figure must have its own dedicated plotting function** — no one-off inline code. Modifying a figure = modifying its function. All plotting functions live in one directory.

Annotation best practices for final figures:
- `axvspan` to highlight critical layer regions (e.g., "Amplification Zone", "Divergence Zone")
- Arrow annotations (`ax.annotate`) for peaks, key values, or notable patterns
- Diamond markers for group means with value labels
- Statistical annotations where relevant (r, p-value, effect size)

### Phase 7: Naming, Layout & Composition Advice

**File naming convention** — the filename should encode:
- Figure group: which paper Figure it belongs to (e.g., `fig3`)
- Sub-figure position: `a`, `b`, `c`, ... (e.g., `fig3a`)
- Content hint: what it shows (e.g., `pca_layer_separation`)
- Full example: `fig3a_pca_layer_separation.pdf`, `fig3b_attention_divergence.pdf`

From the filename alone, one should be able to infer:
- What the caption will describe
- How the figures are grouped and ordered

**Composition advice** — suggest to the author:
- Which sub-figures form one Figure
- Layout arrangement (1xN horizontal, Nx1 vertical, 2x2 grid, etc.)
- Relative sizing (equal or weighted)
- Whether figures share axes or legends

Do not combine figures in code. Each sub-figure stays as an independent PDF.

### Phase 8: Intervention Experiment Suggestions

After the analysis story is established, optionally propose causal intervention experiments that could strengthen the claims:
- **Attention knockout**: zero out attention to specific regions/heads and measure outcome change
- **Activation patching**: swap hidden states between groups at specific layers
- **Threshold sweep**: vary intervention parameters to map dose-response curves
- **Ablation studies**: remove specific components to test necessity

Present these as suggestions for the author to consider. Each suggestion should include:
- What causal question it answers
- Which claim it would strengthen
- Brief description of the experimental setup

## Interaction Protocol

At each phase transition, summarize progress and get author confirmation before proceeding. Example:

```
Phase 2 complete. I generated 9 exploratory plots:
- attention_to_injection_heatmap.png: layer x head attention, success vs fail vs difference
- per_layer_attention.png: per-layer mean attention with error bands, success vs fail
- hidden_state_projections.png: PCA + t-SNE across 5 layers, colored by score
- layer_divergence.png: cosine similarity and L2 distance between group centroids
- logit_analysis.png: entropy distribution, entropy vs score, top-1 tokens
- ...

Based on these, I see 3 possible stories:
1. [Story A]: Attack success is driven by attention amplification in mid-late layers
2. [Story B]: Representation divergence in late layers determines outcome
3. [Story C]: ...

Which direction would you like to pursue? Or do you see a different story?
```
