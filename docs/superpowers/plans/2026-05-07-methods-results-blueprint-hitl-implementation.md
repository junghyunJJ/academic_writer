# Methods Results Blueprint HITL Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add a strong human-in-the-loop Blueprint gate before Methods and Results prose generation in `academic-writer`.

**Architecture:** This is a protocol/documentation update only. The main behavior lives in `academic-writer/agents/section-writer.md`, with enforcement rules in `academic-writer/agents/section-reviewer.md` and public workflow summaries in `academic-writer/SKILL.md` plus `README.md`.

**Tech Stack:** Markdown skill files, existing `academic-writer` agent protocol structure, shell verification with `rg`, `sed`, and `git diff`.

---

## Review Amendment

Claude Code review identified that the original plan needed an explicit `approved_blueprint` handoff. Implement the reviewed version with these additional requirements:

- `section-writer.md` must emit `approved_blueprint` after Methods/Results Step 2d approval.
- `section-reviewer.md` must accept `approved_blueprint` as optional input and run a separate `Pass 8: Blueprint Alignment` for Methods/Results.
- Methods/Results Lite Mode is allowed only when the user explicitly says "outline only", "skip blueprint", or "lite mode"; then record `blueprint_alignment: skipped_by_user`.
- Step 2 hard gating should happen at final Step 2d approval, not separately at 2a and 2b.
- `SKILL.md` and `README.md` must update architecture diagrams and public workflow wording so they no longer describe Methods/Results as outline-only.

---

### Task 1: Update Section Writer Blueprint Protocol

**Files:**
- Modify: `academic-writer/agents/section-writer.md`

- [ ] **Step 1: Inspect the current Step 2 block**

Run:

```bash
sed -n '516,725p' academic-writer/agents/section-writer.md
```

Expected: Output shows `## Step 2: Interactive Outline`, then separate Introduction, Methods, Results, and Discussion outline blocks.

- [ ] **Step 2: Replace the Methods outline block with a Blueprint-gated block**

In `academic-writer/agents/section-writer.md`, replace the current `### Methods Outline` YAML block with this exact section:

```markdown
### Methods Blueprint (Strong HITL Gate)

Methods uses a stronger pre-draft Blueprint gate instead of the standard outline-only flow. The writer must not proceed to Step 3 prose until the user explicitly approves the complete Methods Blueprint.

```yaml
methods_blueprint:
  step_2a_section_skeleton:
    action: "Propose the Methods section skeleton"
    content:
      - "Target length or journal word budget, if known"
      - "Organization principle: chronological, pipeline, modular, or hierarchical"
      - "Subsection order and titles"
      - "Major procedure/method step under each subsection"
    output_format: |
      ## Methods Blueprint

      **Target length**: [word budget if known]
      **Organization principle**: [chronological / pipeline / modular / hierarchical]

      1. [Subsection title]
         1. [procedure or method step]
         2. [data/tool/parameter note]
      2. [Subsection title]
         1. [procedure or method step]
         2. [data/tool/parameter note]
    requirement: "Collaborate with user and revise as needed; final hard gate occurs at Step 2d"

  step_2b_blueprint_matrix:
    action: "Generate the Methods Blueprint matrix"
    content:
      - "One row per method block or procedure step"
      - "Inputs, tools, versions, parameters, outputs, and reproducibility risks"
      - "Explicit marker for missing values that must be resolved before prose"
    output_format: |
      | Block | Subsection | Procedure/Step | Data/Input | Tool/Version | Parameters | Output | Reproducibility Risk |
      |-------|------------|----------------|------------|--------------|------------|--------|----------------------|
      | M1 | Cohort construction | Select eligible patient records | EHR medication and laboratory tables | SQL query + Python 3.11 | inclusion window, exclusion criteria | analysis-ready cohort table | unresolved exclusion criteria must be confirmed |
    requirement: "Collaborate with user and revise as needed; final hard gate occurs at Step 2d"

  step_2c_gap_and_risk_check:
    action: "Check the Methods Blueprint for missing reproducibility details"
    checks:
      - "Every procedure step has an input and output"
      - "Every software/tool has a version or is flagged for user resolution"
      - "Every key parameter has a value or is flagged for user resolution"
      - "Intermediate data transformations are represented"
      - "Custom scripts, special thresholds, or non-default settings are visible"
    output: "List unresolved gaps and propose specific fixes"
    requirement: "All major reproducibility gaps must be resolved or explicitly accepted by the user"

  step_2d_strong_hitl_gate:
    action: "Request explicit approval of the complete Methods Blueprint"
    approval_rule: "Do not proceed to Step 3 prose until the user clearly approves the complete Blueprint"
    revision_rule: "If the user requests changes, revise the Blueprint and repeat Step 2d"
    contract_rule: |
      After approval, prose generation must not introduce new subsections, method steps,
      tools, parameters, data sources, outputs, or figure/algorithm placements unless the
      user first approves a Blueprint revision.
```
```

- [ ] **Step 3: Replace the Results outline block with a Blueprint-gated block**

In `academic-writer/agents/section-writer.md`, replace the current `### Results Outline` YAML block with this exact section:

```markdown
### Results Blueprint (Strong HITL Gate)

Results uses a stronger pre-draft Blueprint gate instead of the standard outline-only flow. The writer must not proceed to Step 3 prose until the user explicitly approves the complete Results Blueprint.

```yaml
results_blueprint:
  step_2a_section_skeleton:
    action: "Propose the Results section skeleton"
    content:
      - "Target length or journal word budget, if known"
      - "Central narrative arc"
      - "Subsection order and titles"
      - "Planned claim or finding under each subsection"
    output_format: |
      ## Results Blueprint

      **Target length**: [word budget if known]
      **Narrative arc**: [central Results story]

      1. [Subsection title]
         1. [planned claim/finding]
         2. [supporting evidence]
      2. [Subsection title]
         1. [planned claim/finding]
         2. [supporting evidence]
    requirement: "Collaborate with user and revise as needed; final hard gate occurs at Step 2d"

  step_2b_blueprint_matrix:
    action: "Generate the Results Blueprint matrix"
    content:
      - "One row per planned claim or finding"
      - "Evidence source, figure/table mapping, statistics, and scope limits"
      - "Explicit marker for missing values that must be resolved before prose"
    output_format: |
      | Block | Subsection | Claim/Finding | Evidence Source | Figure/Table | Statistics | Scope Limits |
      |-------|------------|---------------|-----------------|--------------|------------|--------------|
      | R1 | Signal detection performance | Proposed signal detects known interaction pairs | gold-standard DDI list + EHR/lab output | Figure 2A, Table 1 | PPV, NPV, sensitivity, specificity, patient count | report detection performance only; save causal interpretation for Discussion |
    requirement: "Collaborate with user and revise as needed; final hard gate occurs at Step 2d"

  step_2c_gap_and_risk_check:
    action: "Check the Results Blueprint for unsupported or risky claims"
    checks:
      - "Every planned claim has an evidence source"
      - "Every figure/table listed by the user is placed or explicitly excluded"
      - "Statistics include sample size, test name, effect estimate, or p-value when applicable"
      - "No row asks the Results prose to interpret beyond the data"
      - "Null or negative findings are represented when the interview identified them"
    output: "List unresolved gaps and propose specific fixes"
    requirement: "All major evidence/statistics gaps must be resolved or explicitly accepted by the user"

  step_2d_strong_hitl_gate:
    action: "Request explicit approval of the complete Results Blueprint"
    approval_rule: "Do not proceed to Step 3 prose until the user clearly approves the complete Blueprint"
    revision_rule: "If the user requests changes, revise the Blueprint and repeat Step 2d"
    contract_rule: |
      After approval, prose generation must not introduce new subsections, claims,
      findings, figure/table placements, statistical comparisons, or scope expansions
      unless the user first approves a Blueprint revision.
```
```

- [ ] **Step 4: Add a shared Step 3 contract note before prose protocols**

Immediately under `## Step 3: Prose with Real-Time RAG Few-Shot References`, add:

```markdown
For Methods and Results, Step 3 is constrained by the approved Blueprint from Step 2. The writer must treat the Blueprint as a contract. If prose generation reveals the need for a new method step, result claim, statistic, figure/table placement, tool, parameter, or subsection, pause prose generation and return to Step 2d for explicit Blueprint revision approval.
```

- [ ] **Step 5: Verify writer updates**

Run:

```bash
rg -n "Methods Blueprint|Results Blueprint|Strong HITL|Blueprint revision|Blueprint as a contract" academic-writer/agents/section-writer.md
```

Expected: Matches appear in the Methods Blueprint block, Results Blueprint block, and Step 3 contract note.

---

### Task 2: Add Reviewer Blueprint Alignment Checks

**Files:**
- Modify: `academic-writer/agents/section-reviewer.md`

- [ ] **Step 1: Inspect current structural and issue-check sections**

Run:

```bash
sed -n '130,170p' academic-writer/agents/section-reviewer.md
sed -n '440,460p' academic-writer/agents/section-reviewer.md
```

Expected: Output shows section-specific structure checks and Results issue checklist.

- [ ] **Step 2: Add Blueprint alignment to reviewer responsibilities**

In the top `Responsibilities` list, add this item after cross-section consistency:

```markdown
10. **Blueprint Alignment** (Methods/Results only): Verify that prose follows the user-approved Blueprint and does not introduce unapproved structure, claims, procedures, statistics, tools, parameters, or figure/table placements
```

- [ ] **Step 3: Add Pass 8 Blueprint Alignment**

After the reproducibility/reporting pass section and before the Discussion over-interpretation pass, add a separate pass:

```markdown
### Pass 8: Blueprint Alignment (Methods/Results only)

This pass runs only when `SECTION_TYPE == "methods"` or `SECTION_TYPE == "results"`.

If `approved_blueprint.approval_status == "skipped_by_user"`, record `blueprint_alignment: skipped_by_user` and do not perform Blueprint-based checks. If Methods or Results is reviewed without `approved_blueprint` and Lite Mode was not explicitly requested, flag this as a major process issue before evaluating prose quality.
```

- [ ] **Step 4: Add issue checklist items**

Under `### Methods Issues`, add:

```markdown
- [ ] Prose introduces a method step not present in the approved Blueprint
- [ ] Tool/version listed in the Blueprint is missing from prose
- [ ] Parameter listed in the Blueprint is missing from prose
- [ ] Blueprint input/output transition is not represented in prose
```

Under `### Results Issues`, add:

```markdown
- [ ] Prose introduces a claim not present in the approved Blueprint
- [ ] Figure/table placement differs from the approved Blueprint
- [ ] Statistic in prose is absent from or unsupported by the Blueprint
- [ ] Scope limit from the Blueprint is violated
```

- [ ] **Step 5: Verify reviewer updates**

Run:

```bash
rg -n "Blueprint Alignment|approved Blueprint|Scope limit|Tool/version listed" academic-writer/agents/section-reviewer.md
```

Expected: Matches appear in responsibilities, pass checks, and issue checklist.

---

### Task 3: Update Orchestrator and README Workflow

**Files:**
- Modify: `academic-writer/SKILL.md`
- Modify: `README.md`

- [ ] **Step 1: Inspect public workflow summaries**

Run:

```bash
rg -n "Interactive Outline|Step 2|Results|Methods|Outline approved|Step 3" academic-writer/SKILL.md README.md
```

Expected: Output shows the existing outline descriptions in both public docs.

- [ ] **Step 2: Update `academic-writer/SKILL.md` Step 2 description**

In `academic-writer/SKILL.md`, update the Step 2 summary so it states:

```markdown
**Step 2: Interactive Outline / Blueprint**
- Introduction and Discussion use the existing interactive outline process.
- Methods and Results use a stronger Blueprint gate before prose.
- The Methods/Results Blueprint combines a hierarchical skeleton with a verification matrix.
- Prose generation is blocked until the user explicitly approves the complete Blueprint.
```

- [ ] **Step 3: Update the Step 2 table in `academic-writer/SKILL.md`**

Replace the Methods and Results cells in the Step 2 table with:

```markdown
| 2a: Overall structure | Funnel: broad -> problem -> gap -> contribution | **Blueprint skeleton**: subsection order, organization principle, method steps | **Blueprint skeleton**: subsection order, narrative arc, planned findings | Interpretive: summary -> comparison -> limitations -> future |
| 2b: Per-unit details | Per-paragraph key points + citations | **Blueprint matrix**: procedure, data/input, tool/version, parameters, output, reproducibility risk | **Blueprint matrix**: claim/finding, evidence source, figure/table, statistics, scope limits | Per-subsection interpretation + citations |
| 2c: Figure placement | Conceptual overview figure (if any) | Gap/risk check: missing versions, parameters, input/output transitions | Gap/risk check: unsupported claims, orphan figures, missing statistics | Back-references to specific Results |
| 2d: Connection plan | Transition leading into Methods/Results | **Strong HITL approval gate** before prose | **Strong HITL approval gate** before prose | Limitation <-> future direction pairing |
```

- [ ] **Step 4: Update `README.md` feature list and workflow**

Add this feature row near the existing feature table:

```markdown
| **Methods/Results Blueprint Gate** | Requires user-approved hierarchical skeleton + verification matrix before Methods or Results prose generation |
```

Update the pipeline description for Step 2 to include:

```markdown
For Methods and Results, Step 2 produces a Blueprint rather than a simple outline. The Blueprint must be explicitly approved before prose begins and constrains all downstream prose and review.
```

- [ ] **Step 5: Verify docs are consistent**

Run:

```bash
rg -n "Blueprint Gate|Methods/Results Blueprint|hierarchical skeleton|verification matrix|explicitly approves" academic-writer/SKILL.md README.md
```

Expected: Both files describe the Blueprint gate and do not claim it applies to Introduction or Discussion.

---

### Task 4: Final Verification and Commit

**Files:**
- Verify: `academic-writer/agents/section-writer.md`
- Verify: `academic-writer/agents/section-reviewer.md`
- Verify: `academic-writer/SKILL.md`
- Verify: `README.md`

- [ ] **Step 1: Check final diff**

Run:

```bash
git diff -- academic-writer/agents/section-writer.md academic-writer/agents/section-reviewer.md academic-writer/SKILL.md README.md
```

Expected: Diff only updates protocol/docs for Methods/Results Blueprint HITL behavior.

- [ ] **Step 2: Run scenario verification searches**

Run:

```bash
rg -n "Do not proceed to Step 3 prose|pause prose generation|new claim|new method step|missing parameter|unsupported claims|orphan figures" academic-writer/agents/section-writer.md academic-writer/agents/section-reviewer.md
```

Expected: Output proves the strong gate, revision rule, and reviewer enforcement are documented.

- [ ] **Step 3: Confirm unrelated worktree changes are not included**

Run:

```bash
git status --short
```

Expected: Modified skill docs appear. Pre-existing `academic-writer/.Rhistory` deletion and `.superpowers/` companion files may still appear but must not be staged.

- [ ] **Step 4: Commit implementation**

Run:

```bash
git add academic-writer/agents/section-writer.md academic-writer/agents/section-reviewer.md academic-writer/SKILL.md README.md
git commit -m "Require approved Blueprints before Methods and Results drafting" \
  -m "Methods and Results need a stronger user-controlled planning gate before prose generation. The writer now builds a hierarchical skeleton plus verification matrix, and the reviewer checks prose against the approved Blueprint." \
  -m "Constraint: Applies only to Methods and Results per approved design" \
  -m "Rejected: Apply to all IMRAD sections | outside approved scope" \
  -m "Rejected: Outline-only approval | insufficient for claim/evidence and reproducibility checks" \
  -m "Confidence: high" \
  -m "Scope-risk: narrow" \
  -m "Directive: Do not bypass the Blueprint gate when writing Methods or Results prose" \
  -m "Tested: rg-based protocol and scenario verification" \
  -m "Not-tested: No executable runtime tests; this is a Markdown skill protocol update"
```

Expected: New commit contains only the four intended documentation/protocol files.
