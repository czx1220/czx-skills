---
name: czx-code-paper-alignment
description: >
  Organize and align project code for paper submission / open-source release.
  Only triggered by explicit user invocation. Do NOT auto-trigger.
  Extracts key code from the working project into a clean, submission-ready folder.
  Ensures code aligns with paper descriptions (experiment settings, method details),
  discusses discrepancies with author, organizes folder structure, adds representative examples,
  writes README and requirements, and performs security review (no API keys, no local paths).
  Every decision defers to the author.
---

# Code-Paper Alignment

## Language Rule

**All communication with the author must be in Chinese (中文).** This includes: status reports, questions, options, analysis, explanations, and all non-code output.

## Activation Notice

When this skill is triggered, immediately display:

```
========================================
  [Skill Activated] Code-Paper Alignment
  Phase 1 → 9 workflow engaged.
  All decisions defer to the author.
  Currently at: Phase 1 — Paper & Codebase Reading
========================================
```

Then **briefly introduce the complete workflow** so the author knows what to expect:

```
完整流程概览：
  Phase 1: 论文 & 代码阅读 — 理解论文和代码的对应关系
  Phase 2: 范围 & 结构讨论 — 确认关键代码，提出文件夹结构（需作者确认）
  Phase 3: 代码提取 & 组织 — 复制关键代码到新文件夹（需作者确认）
  Phase 4: 论文-代码对齐（粗粒度）— 大方法、大设置是否匹配（需作者确认方向，修改过程自动）
  Phase 5: 论文-代码对齐（细粒度）— 参数设置是否对应
  Phase 6: 代码可运行性检查 — 缺失文件、broken import、明显缺陷
  Phase 7: 代表性示例 — 挑选实验结果放入 examples
  Phase 8: 安全 & 清理审查 — API key、绝对路径、内部 URL
  Phase 9: README & requirements.txt + 全面最终检查
```

Update the "Currently at" line at each phase transition.

## Core Principle

**The submitted code must be a clean, minimal, self-contained package that precisely matches the paper's descriptions.**

Rules:
- **Only key code.** This is not a full project dump — only include code essential to reproducing the paper's results.
- **Align with paper.** Every experiment setting, hyperparameter, method description in the paper must match the code. Discrepancies must be surfaced and resolved with the author.
- **Code > paper is OK.** Code that has MORE than the paper (extra features, higher defaults) does NOT need to be changed. Only code that is MISSING or CONTRADICTS the paper needs fixing.
- **Discuss at checkpoints, execute automatically.** Each phase has a checkpoint where the author confirms direction. Once confirmed, modifications within a phase are executed automatically — only report a summary at the end. Do NOT ask for confirmation on every individual change.
- **Match source style.** All code modifications must match the existing code style and comment style of the source files. Do not impose a different style.
- **Clean and readable.** The released code should be easy for a third party to understand and run.

## Phase 1: Paper & Codebase Reading

**Goal: thoroughly understand both the project codebase and the paper before making any decisions.**

The author will provide:
- The project directory (working codebase)
- The paper directory (LaTeX source / PDF)

Agent must:
1. **Read the paper thoroughly** — understand all method details, experiment settings, datasets, baselines, hyperparameters
2. **Read the project codebase** — understand the full directory structure, identify which files implement which parts of the paper
3. **Map paper sections to code files** — build a mental mapping: "Section 3.1 describes X → implemented in `src/method_a.py`"
4. **Identify what code is paper-relevant vs. exploratory** — not everything in the project directory belongs in the submission

Report to the author:
- "I have read the paper and codebase. Here is my understanding of what code corresponds to what paper content: [mapping]"
- "I believe these files are needed for submission: [list]"
- "I have concerns/questions about: [list]"

Discuss any uncertainties with the author before proceeding.

## Phase 2: Scope & Structure Discussion

**Goal: confirm key code to include and agree on folder structure with the author.**

Based on Phase 1 findings, discuss with the author:
- What are the key components? (method implementation, training scripts, evaluation scripts, data processing)
- What can be excluded? (exploratory code, one-off analysis, internal tools)
- Any code that is ambiguous — discuss whether to include
- What folder structure makes sense?

Propose a folder structure and **get author confirmation before proceeding**.

## Phase 3: Code Extraction & Organization

**Goal: copy key code into the new clean folder, organized by the agreed structure.**

**Checkpoint: confirm the file list with the author before copying.**

- Create the new folder structure
- Copy relevant code files from the working project
- Remove unnecessary imports, debug code, commented-out blocks
- Ensure each file is self-contained and has clear purpose
- Match the original code style and comment style

**Do NOT include:**
- Exploratory/experimental code not related to the paper
- Internal tooling or infrastructure code
- Large data files (provide download instructions instead)
- Logs, checkpoints, or intermediate results

## Phase 4: Paper-Code Alignment — Coarse-Grained

**Goal: verify the major method descriptions and experiment designs in the paper match the code.**

This is the coarse-grained pass — focus on:
- **Algorithm / method structure**: Does the code implement the same algorithm described in the paper?
- **Major components**: Are all components mentioned in the paper present in the code?
- **Experiment design**: Are the right benchmarks, models, and baselines reflected?
- **Missing pieces**: Any method or component described in the paper but absent from the code?

**Checkpoint: report all coarse-grained discrepancies to the author and confirm the fix direction.** Then execute all modifications automatically — report a summary when done.

Code > paper (code has more than paper describes) → no change needed.
Code < paper (code is missing something paper describes) → must fix.
Code ≠ paper (code contradicts paper) → must fix, confirm direction with author.

## Phase 5: Paper-Code Alignment — Fine-Grained

**Goal: verify all hyperparameters, thresholds, default values, and detailed settings match the paper.**

Go through the paper systematically and check:
- Hyperparameter values mentioned in paper ↔ code defaults/configs
- Threshold values, iteration budgets, patience settings
- Dataset sizes, sampling parameters
- Strategy/seed counts, evaluation criteria

Same rules as Phase 4:
- Code > paper → no change needed
- Code < paper or Code ≠ paper → must fix

Execute modifications automatically. Report a summary of all changes at the end.

## Phase 6: Code Runnability Check

**Goal: ensure the code has no obvious defects that would prevent it from running.**

Check:
- **Broken imports**: Any `from X import Y` where the module X doesn't exist in the package?
- **Missing files**: Any file referenced in code (data files, configs) that doesn't exist?
- **Undefined references**: Variables or functions used but never defined?
- **Obvious errors**: Syntax errors, unreachable code, clearly broken logic?

The code must at minimum look runnable — it should NOT look like pseudocode. A reviewer should be able to glance at the code and believe it works.

**Important**: Code that has MORE than the paper (extra features, higher defaults, additional targets) does NOT need to be removed. Only fix things that are broken or missing.

## Phase 7: Representative Examples

**Goal: include representative experiment results for demonstration.**

- Select representative examples that show what the input/output looks like
- These are illustrative (not full datasets) — just enough for a reader to understand the format
- Discuss with author: which examples best demonstrate the method?
- Include in `examples/` or similar directory
- These should be actual data points, not synthetic — showing real behavior
- Clean any sensitive data (API keys, absolute paths) from example files

## Phase 8: Security & Cleanup Review

**Goal: ensure nothing sensitive or broken is in the release.**

**Security check (critical):**
- **No API keys, tokens, or secrets** in any file
- **No local/absolute paths** (e.g., `/nas_mm_2/...`, `/home/user/...`) — all paths must be relative or configurable
- **No internal hostnames or URLs** (intranet addresses, internal services)
- **No personal information** beyond author names in README
- **No credentials in configs** (database passwords, SSH keys, etc.)

**Cleanup check:**
- No `__pycache__`, `.pyc`, `.egg-info` directories
- No `.git` history from the working project (fresh init if needed)
- No large binary files
- No IDE-specific files (`.idea/`, `.vscode/` settings)
- `.gitignore` included with appropriate patterns

Report all issues and fix them. No need for per-item author confirmation — just fix and report.

## Phase 9: README, Requirements & Final Review

**Goal: write documentation, then do a comprehensive final check.**

### Step 1: Write README & requirements.txt

**README.md should include:**
- Paper title and authors (with link to paper if available)
- Brief description (1-2 paragraphs) of what the code does
- **Detailed folder structure** — explain every directory and what each file does
- Environment setup instructions
- How to reproduce main results (step by step)
- Citation (BibTeX)

**requirements.txt:**
- List all Python dependencies with version pins
- Only include actually used packages
- Verify by checking imports across all files

### Step 2: Comprehensive Final Check

After writing README, do a **full review** covering:
1. **Paper-code conflicts**: Any remaining serious discrepancies between paper descriptions and code?
2. **Code defects**: Any broken imports, missing files, obvious errors?
3. **Example data**: Any sensitive data, incorrect formats, or misleading content in examples?
4. **Security**: Final scan for API keys, absolute paths, internal URLs across ALL files (including examples)

If any issues are found: fix them, then update README if the fixes changed the structure.

### Step 3: Report folder size

Report the total folder size and per-directory breakdown.

## Completion Criteria

This skill is DONE when:
- Code is organized in a clean, discussed-and-confirmed folder structure
- All code aligns with paper descriptions (coarse + fine grained, no missing pieces)
- Code is runnable — no broken imports, no missing files
- Representative examples are included
- README and requirements.txt are written and confirmed
- Security review passed — no keys, no local paths, no secrets
- Final comprehensive check passed
- Folder size reported
- The folder is ready for submission or open-source release
