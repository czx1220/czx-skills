---
name: czx-image2plot
description: >
  Generate image-generation prompts for academic paper figures (intro figures, method pipeline diagrams, etc.).
  Only triggered by explicit user invocation. Do NOT auto-trigger.
  This skill does NOT generate images directly — it produces structured markdown prompts to be fed to image generation tools.
  Covers: understanding what the figure needs to convey (from code, description, or discussion with author),
  designing layout and visual elements, writing detailed prompts with style specifications,
  and outputting the prompt as a markdown file.
  Every decision defers to the author.
---

# Image2Plot (Prompt Generation)

## Language Rule

**All communication with the author must be in Chinese (中文).** This includes: status reports, questions, options, analysis, and explanations. Prompt output content may remain in English as needed.

## Activation Notice

When this skill is triggered, immediately display:

```
========================================
  [Skill Activated] Image2Plot
  Phase 1 → 4 prompt writing workflow engaged.
  All decisions defer to the author.
  Currently at: Phase 1 — Understanding the Figure
========================================
```

Update the "Currently at" line at each phase transition.

## Core Principle

**This skill writes structured prompts for image generation tools — not code-based plots.**

Use cases: Introduction overview figures, method pipeline/framework diagrams, conceptual illustrations, architecture diagrams — figures that are drawn (not plotted from data).

Rules:
- **Understand before writing.** Fully understand what the figure needs to convey before writing any prompt.
- **Discuss with author.** The author may provide code, verbal descriptions, or reference images. Discuss and align on what to include.
- **One figure at a time.** Each prompt targets one figure.
- **Output as markdown.** Each prompt is saved as a `.md` file in the project's docs directory.
- **Academic, professional, clean.** The prompt must specify a top-venue publication style — clean, minimal, not cluttered, informative.

## Phase 1: Understanding the Figure

**Goal: fully understand what the figure needs to show and convey to readers.**

Information sources (one or more):
- **Code**: author provides relevant code, agent extracts the algorithmic flow/architecture
- **Description**: author verbally describes what message the figure should deliver to readers — the author may NOT have a clear picture of the full information flow, only the high-level intent
- **Existing framework skeleton**: from `czx-paper-framework`, the figure's purpose is already defined
- **Reference images**: author shows examples of similar figures they like

Discuss with the author (one question at a time):
- What is the figure's purpose? (explain method? show comparison? illustrate a phenomenon?)
- What message should a reader take away from this figure?
- What are the key components/elements?
- What level of detail? (high-level overview vs. step-by-step pipeline)
- What is NOT needed? (what to simplify or omit?)

**When the author only has a high-level intent (no code):**
The author may only be able to say "I want readers to understand X through this figure." In this case, agent must actively propose the complete information flow:
- What elements are needed to convey this message?
- What is the logical order/flow between elements?
- What comparisons or contrasts make the point clear?
- Propose a concrete information flow, discuss with the author, iterate until aligned

## Phase 2: Layout & Structure Design

**Goal: decide the spatial arrangement and information flow.**

Discuss with the author:
- **Overall layout**: left-to-right flow? top-to-bottom? parallel rows for comparison? split panels?
- **Aspect ratio**: 2:1 wide? 16:9? Square?
- **Main sections/panels**: what goes where?
- **Information flow**: arrows, connections, data flow direction
- **Comparison structure**: if comparing multiple things, how to arrange (side-by-side, stacked, before/after)

Propose a rough layout description and get author confirmation before proceeding.

## Phase 3: Prompt Writing

**Goal: write the complete, detailed prompt.**

The prompt markdown file should follow this structure:

```markdown
# [Figure Name] Prompt

[1-2 sentence overview of what to create]

## Overall Layout
- Aspect ratio, background, flow direction
- Panel/section arrangement

## [Section/Panel 1]
- Detailed description of elements
- Text labels, formulas, annotations
- Specific positioning

## [Section/Panel 2]
...

## Key Visual Contrasts / Messages
- What should visually stand out
- What comparison the viewer should see immediately

## Style Requirements
- Colors (specific hex codes)
- Typography (font family, sizes)
- Design style (flat 2D, minimal, clean lines)
- Border/line styles
```

### Style Principles for Academic Figures

**Colors:**
- Use a restrained, academic palette — 3-5 colors max
- Each color should carry semantic meaning (e.g., orange = payload/highlight, blue = context/model, green = positive, red = negative)
- Provide specific hex codes
- White or very light background
- Avoid garish or overly saturated colors

**Typography:**
- Sans-serif (Helvetica/Arial) for labels and text
- Monospace for code/formula text where appropriate
- Clear hierarchy: title > labels > content text

**Layout:**
- Clean, minimal, white background
- Flat 2D design — no 3D effects, no gradients unless essential
- Thin borders and lines (1px or 1.5px)
- Generous spacing between elements — avoid clutter
- Clear arrow connectors for flow
- Symmetric spacing and alignment

**Formulas:**
- Use proper mathematical notation (Unicode or LaTeX-style)
- Keep formulas concise — only show key steps, not full derivations

**Overall:**
- Publication-ready for top venues (NeurIPS, ICML, ACL, etc.)
- A reader should understand the figure's message within 5 seconds of looking at it
- Every element must serve a purpose — if it doesn't inform, remove it

## Phase 4: Review & Refinement

**Goal: review the prompt with the author and iterate.**

- Present the full prompt to the author
- **Explain the information flow clearly**: walk the author through what a reader will see — what elements, in what order, what flow, what message
- Discuss: is anything missing? Too much detail? Wrong emphasis? Wrong flow?
- **Naming**: discuss the figure name and file name with the author — ensure it's descriptive and aligned with the paper structure
- Author may request changes to layout, elements, colors, content, or flow
- **Iterate**: this is a discussion process — the prompt may go through multiple rounds before the author is satisfied
- Save final prompt as `[figure_name]_prompt.md` in the project docs directory

## Output Convention

**File naming:** `[descriptive_name]_prompt.md`
- e.g., `pipeline_image2_prompt.md`, `intro_figure_prompt.md`, `benchmark_pipeline_image2_prompt.md`

**Storage location:** project's `docs/` directory (or as specified by author)

**The prompt file is the final deliverable** — it will be copied directly to an image generation tool by the author.

## Completion Criteria

This skill is DONE when:
- The figure's purpose and content are fully aligned with the author
- The prompt is detailed enough for an image generation tool to produce the desired figure
- Layout, colors, typography, and style are all specified
- The prompt is saved as a markdown file
- The author confirms the prompt is ready to use
