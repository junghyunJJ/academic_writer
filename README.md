# Academic Writer v4.0 — Complete IMRAD Section Writing

> RAG-based dual-layer architecture for writing publication-quality Introduction, Methods, Results, and Discussion sections. Learns from reference papers. Gathers intent through tiered conversational interviews. Improves continuously through feedback.

**Version**: 4.0 | **Released**: 2026-04-01 | **Skill Invocation**: `/academic-writer`

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture](#2-architecture)
3. [Agent Details](#3-agent-details)
4. [RAG Integration](#4-rag-integration)
5. [Pipeline Details (Phase -2 through Phase 4)](#5-pipeline-details)
6. [Usage Modes](#6-usage-modes)
7. [Fallback System](#7-fallback-system)
8. [File Structure](#8-file-structure)
9. [Supported Journals and Guidelines](#9-supported-journals-and-guidelines)
10. [Continuous Learning](#10-continuous-learning)
11. [Changelog: v3.0 → v4.0](#11-changelog-v30--v40)
12. [Quick Reference](#12-quick-reference)

---

## 1. System Overview

### What It Does

Multi-agent system for writing publication-quality IMRAD sections (Introduction, Methods, Results, Discussion) for academic research articles. The user selects one section per invocation and provides data, context, and reference papers. The system learns dual-layer writing patterns from references (structure + voice), gathers intent through a tiered conversational interview, generates an outlined draft, reviews it with pedagogical feedback, and continuously improves through approved outputs.

**Single section per invocation**: Each call to `/academic-writer` handles one IMRAD section completely. To write a full paper, invoke once per section in sequence.

### v4.0 Key Changes (vs v3.0)

| Item | v3.0 | v4.0 |
|------|------|------|
| **Coverage** | Results only | **All IMRAD** (Introduction, Methods, Results, Discussion) |
| **Section Selection** | N/A | **Phase -2** (user picks section) |
| **Interview Format** | Flat Q1-Q5 all at once | **Tiered Conversational** (Phase A-D, one-at-a-time) |
| **Context Accumulation** | None | **paper_context** (cross-section reuse) |
| **Cross-Section Coherence** | None | **Active Coherence Check** (Phase A enhancement) |
| **Review Style** | 7-pass uniform | **Section-weighted passes** + pedagogical WHY + Over-interpretation Check (Discussion only) |
| **Keywords Support** | None | **Tier 1 (Emphasize)** + **Tier 2 (Include)** user keywords |
| **RAG Health Check** | None | **Automatic detection** with 3 user options |
| **PDF Fallback** | Single-layer | **Dual-layer** (Structure + Voice) |
| **Hourglass Awareness** | None | **Intro↔Discussion symmetry** enforcement |
| **Agents** | 5 (style-extractor new) | **6 generic parameterized** (section-analyzer, style-extractor, section-writer, section-reviewer, pattern-learner, paper-preprocessor) |
| **New Files** | — | introduction-patterns.md, methods-patterns.md, discussion-patterns.md, expanded style-guide.md |

---

## 2. Architecture

### Complete Pipeline

```
Phase -2: Section Selection
┌─────────────────────────────────────┐
│ "Which section? Intro/Methods/      │
│  Results/Discussion?"               │
│ → Sets SECTION_TYPE                 │
└────────────┬────────────────────────┘
             │
RAG Health Check (NEW)
┌────────────▼────────────────────────┐
│ list_collections() available?        │
│ → Yes: Phase -1  → No: 3 options    │
└────────────┬────────────────────────┘
             │
Phase -1: Collection Selection
┌────────────▼────────────────────────┐
│ Q1: Structure Layer collection      │
│ Q2: Target Voice collection         │
│ (defaults: agentpaper + mypaper)    │
└────────────┬────────────────────────┘
             │
Phase 0a + 0b: Dual-Layer Learning (PARALLEL)
┌──────────────────────┐  ┌──────────────────────┐
│  RAG Search          │  │  RAG Search          │
│  (Structure)         │  │  (Voice)             │
│       ↓              │  │       ↓              │
│  Section Analyzer    │  │  Style Extractor     │
│       ↓              │  │       ↓              │
│  Structure Layer     │  │  Target Voice Layer  │
└─────────┬────────────┘  └─────────┬────────────┘
          │                         │
          └────────────┬────────────┘
                       │
Phase 1: Style Merge (Priority Rules)
┌──────────────────────▼─────────────┐
│ Merge Structure + Voice Layers      │
│ Update style-guide.md               │
└──────────────────────┬──────────────┘
                       │
Phase 2: Section Writer (Tiered Interview + Prose)
┌──────────────────────▼─────────────┐
│ Step 0: Input Collection            │
│ Step 1: Tiered Interview            │
│   Phase A: Context Detection        │
│   Phase B: Core Questions (1×1)     │
│   Phase C: Adaptive Follow-ups      │
│   Phase D: Intent Confirmation      │
│ Step 2: Interactive Outline         │
│   (per-step user approval)          │
│ Step 3: Prose + RAG few-shot        │
└──────────────────────┬──────────────┘
                       │
Phase 3: Section Reviewer
┌──────────────────────▼─────────────┐
│ 7-pass section-weighted review      │
│ + pedagogical WHY explanations      │
│ + section-specific checks           │
└──────────┬───────────┬──────────────┘
           │           │
     Approved?    →    No ──→ Writer (revise)
           │
         Yes ↓
Phase 4: Pattern Learner
┌──────────────────────┐
│ Section-tagged       │
│ pattern extraction   │
│ Style guide updates  │
│ paper_context save   │
└──────────────────────┘
```

### Dual-Layer Concept

The system maintains two independent learning layers:

| Layer | Role | Default Collection | Learning Content |
|-------|------|-------------------|------------------|
| **Structure Layer** | Section architecture and organization | agentpaper | Subsection hierarchies, Figure integration, flow patterns, section-specific structure |
| **Target Voice Layer** | Writing style, tone, and expression | mypaper | Sentence patterns, transitions, hedging frequency, discipline-specific terminology, statistical reporting conventions |

**Priority Rules** (inherited from v3.0, applied per section):
- Structure elements (subsection headers, figure placement) → Structure Layer priority
- Voice/tone elements (sentence patterns, hedging, "we" usage) → Target Voice Layer priority
- Statistical reporting formats → Target Voice Layer priority
- Terminology and jargon → Target Voice Layer priority
- Transitions and connectives → Target Voice Layer priority

### Cross-Section Coherence (NEW in v4.0)

When writing a section, the system performs an **active coherence check**:

```
Phase A: Context Detection
  ├─ Auto-scan workspace for existing sections
  ├─ If existing sections found →
  │    "I see you have Results already.
  │     Should I reference it while writing Methods?"
  │  → Yes: Extract consistency anchors
  │  → No: Write independently
  └─ Auto-extract cross-reference points
      (see Cross-Reference Matrix below)
```

**Cross-Reference Matrix** (which sections inform which):

| Writing Now | Reference Section | Auto-Extracted Elements |
|-------------|-------------------|----------------------|
| Methods | Results | Tools/methods mentioned in results, test statistics used, pipeline steps |
| Discussion | Results | Key findings for recap, statistics to reference, unexpected results |
| Discussion | Introduction | Gap statement to address, hypotheses to confirm/refute, broad context to mirror |
| Results | Methods | Analysis order, tool names for consistency, parameter values |
| Introduction | Results | Key findings to preview (optional), scope of contribution |
| Methods | Introduction | Study design previewed, approach mentioned |

**Hourglass Model Awareness**:
- Introduction: Funnel (broad context → specific question)
- Discussion: Inverted Funnel (specific findings → broad significance)

When both exist, the system enforces symmetry: Discussion opening references Introduction's gap, closing returns to broad context. Reviewer performs explicit cross-reference check.

---

## 3. Agent Details

All agents are now parameterized by `SECTION_TYPE`, enabling single pipeline to handle all four IMRAD sections.

### 3.1 Section Analyzer (renamed from Results Analyzer)

**File**: `agents/section-analyzer.md`

Extracts structural patterns specific to `SECTION_TYPE` from Structure Layer RAG results.

**Section-Specific Analysis Dimensions**:

| Section | Key Dimensions |
|---------|-----------------|
| **Introduction** | Literature review breadth, gap positioning clarity, contribution statement placement, background scope, research question explicitness |
| **Methods** | Pipeline step granularity, reproducibility detail, software version specificity, subsection organization, parameter documentation |
| **Results** | Finding presentation order, figure-result alignment, subsection hierarchy, statistical reporting density, comparison structure |
| **Discussion** | Finding-literature positioning, limitation acknowledgment depth, future direction scope, interpretation hedging, broader impact framing |

**Output**: Updated `data/style-guide.md` Structure Layer section (parameterized by SECTION_TYPE).

### 3.2 Style Extractor (expanded)

**File**: `agents/style-extractor.md`

Extracts voice/tone from Target Voice Layer, now section-aware.

**Section-Specific Voice Extractions**:

| Section | Voice Elements to Extract |
|---------|--------------------------|
| **Introduction** | Field significance framing, "However/Despite" hedging frequency, "We propose/introduce" cadence, citation density, background narrative pacing |
| **Methods** | Procedural language (passive/active ratio), "We used/employed" patterns, parameter specification style, tool attribution conventions |
| **Results** | Finding presentation verbs ("found/demonstrated/showed"), figure reference cadence, statistical reporting abbreviations, result-based connectives |
| **Discussion** | "Suggest/indicate/demonstrate" patterns, consistency phrases ("consistent with prior"), limitation disclaimer frequency, interpretation hedging, future direction tone |

**Output**: Updated `data/style-guide.md` Target Voice Layer section (parameterized by SECTION_TYPE).

### 3.3 Section Writer (major upgrade from Results Writer)

**File**: `agents/section-writer.md`

Four-step process, now with tiered conversational interview.

**Input Collection** (Step 0, section-specific):

```yaml
research_materials:
  # Introduction materials
  introduction:
    background_sources: "[key papers, review articles]"
    research_gap_context: "[what's missing or unexplored]"
    preliminary_findings: "[optional: early data motivating this work]"

  # Methods materials
  methods:
    study_type: "[computational, experimental, mixed, etc.]"
    data_sources: "[where data came from]"
    analysis_pipeline: "[tool names, versions, order of steps]"
    custom_scripts: "[any user-written code or modifications]"

  # Results materials
  results:
    data_files: "[CSV, matrices, statistical output]"
    figures: "[PNG, PDF with figure legends]"
    tables: "[tabular results]"
    analysis_summary: "[.md file with context]"

  # Discussion materials
  discussion:
    key_findings: "[from Results, highlighted]"
    literature_for_comparison: "[papers to position against]"
    limitations_draft: "[preliminary limitations identified]"
    implications: "[applications, broader significance]"
```

**Step 1: Tiered Conversational Interview** (NEW, core feature):

```
Phase A: Context Detection (automated + 0-1 question)
├─ Auto-scan workspace for existing sections
├─ Load paper_context from prior section interviews
├─ Detect target journal, research field, user expertise
└─ If other sections exist →
     "I see you have Results. Should I reference it while writing Methods?"

Phase B: Core Interview (5 section-specific questions, one-at-a-time)
├─ Each answer acknowledged before next Q
├─ Smart defaults for every question (user can skip)
├─ Conversational tone: "Tell me about..." not "Specify the..."
├─ Purpose transparency: brief note on why each Q matters
└─ Examples (see below per section)

Phase C: Adaptive Follow-ups (0-3 questions, conditional)
├─ Context-deepening: probe vague Phase B answers
├─ Context-enhancing: introduce considerations user may not have thought of
└─ Can be deferred: "I have enough to start. I'll ask during outline review."

Phase D: Intent Confirmation (1 interaction)
├─ Structured summary of understood intent
├─ User confirms or corrects
└─ Update paper_context for downstream sections
```

**Section-Specific Core Questions**:

**Introduction (Phase B, 5 core + follow-ups)**:

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What is the core motivation and research question of this paper? | gap-elicitation | "Knowledge gap? Methodology gap? Application gap?" |
| Q2 | What is the most important limitation or gap in existing research? | contrastive-probe | "What was tried before, why insufficient?" |
| Q3 | Summarize your contribution in one sentence. | intent-crystallization | Forces core message articulation |
| Q4 | How much background knowledge should Introduction assume? (minimal ↔ extensive) | scope-calibration | Adapt to target journal audience |
| Q5 | Key reference papers or positioning papers? | reference-anchoring | Can trigger RAG search |

**Methods (Phase B, 5 core + follow-ups)**:

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What type of study is this? (computational, experimental, mixed, etc.) | structural-classifier | Determines section template |
| Q2 | Data/samples: what, how much, from where? | core-content | Follow-up: inclusion/exclusion criteria |
| Q3 | Describe the analysis pipeline step-by-step (raw data → final result). | workflow-elicitation | Encourage numbered steps |
| Q4 | Software/tools and versions used? | reproducibility | Follow-up: custom scripts, parameters |
| Q5 | Preferred subsection order for Methods? | structure-preference | Default: pipeline order |

**Results (Phase B, 5 core, enhanced from v3.0)**:

| # | Question | Type | Enhancement |
|---|----------|------|-------------|
| Q1 | What is the central narrative of these Results? (What is the data saying?) | narrative-framing | ABT hint: "And..., But..., Therefore..." |
| Q2 | Top 3-5 findings, ranked by importance. | priority-setting | Added ranking + number constraint |
| Q3 | For each Figure/Table: what does it show and what is the key takeaway? | visual-inventory | NEW — ensures purposeful figure integration |
| Q4 | Preferred subsection order for Results? | structure-preference | Same as v3.0 Q3 |
| Q5 | Most important comparison: vs baseline, vs existing methods, across conditions? | comparison-framing | NEW — replaces redundant style Q |

**Discussion (Phase B, 5 core + follow-ups)**:

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | Most important conclusion from Results? (one sentence) | thesis-statement | Anchors Discussion opening |
| Q2 | Key points to discuss vs. existing literature? | literature-positioning | Follow-up: "2-3 key papers to compare?" |
| Q3 | Main limitations of this study? | limitation-awareness | Follow-up: methodological? data? generalizability? |
| Q4 | Unexpected or hard-to-explain findings? | anomaly-exploration | Shows analytical maturity |
| Q5 | Topics explicitly to avoid in Discussion? (scope boundary) | scope-guard | Prevents over-interpretation |

**Step 2: Interactive Outline** (section-specific):
- Generate section-appropriate outline structure
- Per-step user approval (similar to v3.0, adapted to section type)
- Reflect interview answers into outline points

**Step 3: Prose + RAG few-shot** (section-specific):
- Extract subsection keywords
- Real-time Target Voice RAG search (2-3 exemplar paragraphs per subsection)
- Prose generation with matched style
- Integration pass: terminology, citations, hedging, writing rules compliance

### 3.4 Section Reviewer (enhanced)

**File**: `agents/section-reviewer.md`

7-pass review with section-specific weights and pedagogical explanations.

**Review Passes** (section-weighted):

| Pass | Introduction | Methods | Results | Discussion |
|------|-------|---------|---------|------------|
| 1. Factual Accuracy | 0.8 | 1.0 | **1.5** | 1.0 |
| 2. Statistical Review | 0.3 | 0.7 | **1.3** | 0.5 |
| 3. Structural Review | **1.2** | 0.8 | 1.0 | 1.0 |
| 4. Style & Clarity | 1.0 | 1.0 | 1.0 | 1.0 |
| 5. Completeness | 1.0 | 1.2 | 1.2 | 1.2 |
| 6. Reporting Compliance | 0.5 | 1.0 | 1.0 | 0.5 |
| 7. Reproducibility | 0.3 | **1.5** | 1.0 | 0.3 |

**Discussion Additions** (extra pass):
- Over-interpretation Check — each interpretation traceable to specific Result, hedging appropriate, alternative explanations considered, no new data introduced
- Limitation Completeness
- Hourglass symmetry (if Introduction exists)

**Revision Loop**: Major/minor revisions → Writer with `revision_diff` + pedagogical WHY (NEW in v4.0) and `teaching_note` explaining writing principles (max 3 iterations).

### 3.5 Pattern Learner (section-tagged)

**File**: `agents/pattern-learner.md`

Learns from approved sections, now with section-specific metrics.

**Learning Sources** (per section):
1. Approved sections (section_type tagged)
2. Reviewer feedback (section-weighted)
3. Reference patterns (from section-specific RAG)
4. RAG effectiveness metrics
5. Style Extractor feedback (voice accuracy per section)

**Output**: Versioned style-guide.md updates + pattern-history/changelog with section attribution.

### 3.6 Paper Preprocessor (expanded for all sections)

**File**: `agents/paper-preprocessor.md`

Fallback processor when RAG unavailable. Now detects and extracts all four IMRAD sections from PDFs.

**Section Detection** (using structural heuristics):
- Introduction: precedes Methods, marked by keywords like "Background," "Motivation," "Introduction"
- Methods: between Introduction and Results, contains "Methods," "Materials & Methods," "Approach"
- Results: between Methods and Discussion, contains "Results," "Findings," starts with data presentation
- Discussion: follows Results, contains "Discussion," "Interpretation," "Implications"

**Output**: Extracted section text + metadata (page range, confidence, subsection markers).

---

## 4. RAG Integration

### Collections

Two RAG collections via `langconnect-rag` MCP server, used across all four sections:

```yaml
structure_layer:
  description: "Reference papers for learning section structure/organization"
  default_collection: "agentpaper"
  default_id: "06bd503e-fd03-4451-8ce7-7c1ee5012584"
  note: "User can select any available collection in Phase -1"

target_voice:
  description: "Papers whose writing style/voice to emulate"
  default_collection: "mypaper"
  default_id: "fd3c6832-12b2-46e6-b724-a2fe62f5da09"
  note: "User can select any available collection; same collection allowed for both layers"
```

### MCP Tools

```yaml
tools:
  list_collections: "mcp__langconnect-rag__list_collections"
  search_documents: "mcp__langconnect-rag__search_documents"
  agentic_search: "mcp__langconnect-rag__agentic_search"

search_parameters:
  search_type: "hybrid"
  limit: 5
```

### Section-Specific RAG Queries

**Structure Layer queries** (Section Analyzer, 4 per section):

| Section | Q1 | Q2 | Q3 | Q4 |
|---------|-----|-----|-----|-----|
| Introduction | "introduction background motivation field significance" | "existing approaches limitations gap" | "we propose we introduce contribution" | "research question aims objectives" |
| Methods | "methods implementation pipeline architecture" | "preprocessing quality control data preparation" | "evaluation benchmark performance validation" | "software tools parameters version" |
| Results | "Results section structure organization subsection" | "findings statistical analysis benchmark performance" | "Figure Table panel results comparison" | "methodology evaluation framework validation" |
| Discussion | "discussion interpretation significance meaning" | "limitation future directions caveats" | "compared to prior work consistent with previous" | "broader impact implications applications" |

**Target Voice queries** (Style Extractor, 3 section-specific + 1 field-specific):

| Section | Q1 | Q2 | Q3 | Q4 |
|---------|-----|-----|-----|-----|
| Introduction | "field significance importance relevance" | "however despite gap limitation remains" | "we introduce present propose here" | *field-specific* |
| Methods | "procedures implementation experimental design" | "we used employed applied implemented" | "parameters settings configuration threshold" | *field-specific* |
| Results | "Results section findings statistical analysis" | "we found demonstrated showed revealed indicated" | "Figure Table panel comparison significantly" | *field-specific* |
| Discussion | "suggest demonstrate indicate consistent with" | "consistent with previous findings prior work" | "limitation future direction caveat" | *field-specific* |

**Real-time few-shot queries** (Section Writer Step 3):
- Subsection topic keywords → Target Voice collection (primary)
- If no results, fallback to Structure collection

---

## 5. Pipeline Details

### Phase -2: Section Selection

**Purpose**: User selects one IMRAD section to write.

1. Orchestrator asks: "Which section would you like to write?" (1. Introduction / 2. Methods / 3. Results / 4. Discussion)
2. Set `SECTION_TYPE` = selected section
3. Load `references/{SECTION_TYPE}-patterns.md`
4. Activate corresponding `section_configs` block in SKILL.md
5. All downstream agents receive SECTION_TYPE as parameter

### RAG Health Check (NEW)

**Purpose**: Verify langconnect-rag MCP server availability before proceeding.

```
Attempt: mcp__langconnect-rag__list_collections

✓ Success → Proceed to Phase -1
✗ Failure → Present 3 options:

  ① Check RAG setup
     "Verify Docker container running. After confirming, I'll retry."
     → Guide user, retry connection

  ② Proceed without RAG
     "Skip Phase 0 (learning) and Phase -1 (collection selection).
      Write using only existing style-guide.md patterns.
      Real-time RAG few-shot in Step 3 also disabled."
     → Set rag_available: false

  ③ Provide PDFs directly
     "Provide reference PDFs instead of RAG.
      I'll extract and learn from them using Paper Preprocessor."
     → Dual-layer PDF selection (see below)
```

**Dual-Layer PDF Input** (mirrors RAG collections):

```yaml
structure_layer_pdfs:
  description: "Papers to learn structure/pattern from (optional)"
  example: "I want to follow the Methods structure of this paper"
  action: "Paper Preprocessor → Section Analyzer → Structure Layer update"

target_voice_pdfs:
  description: "Papers to learn writing style/tone from (optional)"
  example: "Write in the English tone of my previous paper"
  action: "Paper Preprocessor → Style Extractor → Target Voice Layer update"

rules:
  - "At least one layer should be provided"
  - "Same PDF can be used for both layers"
  - "Multiple PDFs allowed per layer"
  - "Unprovided layers retain existing style-guide.md patterns"
```

### Phase -1: Collection Selection

**Purpose**: User selects RAG collections for structure and voice learning. (Skipped if RAG Health Check failed and user chose option ②.)

1. Call `mcp__langconnect-rag__list_collections` to get available collections
2. Present list with names, document counts, descriptions
3. Ask Q1: "Which collection for Structure Layer (structure/pattern learning)?" (default: agentpaper)
4. Ask Q2: "Which collection for Target Voice Layer (style/tone learning)?" (default: mypaper)
5. Store collection IDs for downstream phases
6. Same collection allowed for both; "skip" or "default" uses agentpaper + mypaper

### Phase 0a: Structure Pattern Learning (Parallel with 0b)

**Input**: Structure Layer collection + SECTION_TYPE

1. Execute 4 section-specific Structure Layer queries (see RAG Queries) against selected collection
2. Pass results to Section Analyzer with SECTION_TYPE
3. Analyzer extracts section-appropriate structural patterns, groups by source, synthesizes cross-document patterns
4. Update `data/style-guide.md` Structure Layer section
5. Optional: BioMCP enrichment for biological context (field detection)

### Phase 0b: Target Voice Learning (Parallel with 0a)

**Input**: Target Voice collection + SECTION_TYPE

1. Execute 3 section-specific + 1 field-specific Target Voice queries against selected collection
2. Pass results to Style Extractor with SECTION_TYPE
3. Extractor analyzes writing patterns: sentence structure, voice, hedging, terminology, statistical reporting
4. Update `data/style-guide.md` Target Voice Layer section
5. Field-specific queries auto-trigger if field detected (Bioinformatics, Genomics, Computational Biology, etc.)

### Phase 1: Style Merge

Merge Structure and Voice Layers using Priority Rules. No separate file created; `data/style-guide.md` itself serves as merged guide.

### Phase 2: Section Writer (Enhanced)

**Step 0**: Collect section-specific input materials (see Section Writer agent description).

**Step 1**: Tiered Conversational Interview
- Phase A: Context Detection (auto-scan, cross-section coherence check)
- Phase B: Core Interview (5 section-specific Qs, one-at-a-time)
- Phase C: Adaptive Follow-ups (0-3 conditional Qs)
- Phase D: Intent Confirmation (summary → user confirms)

**Step 2**: Interactive Outline
- Generate section-appropriate structure based on interview answers
- Per-step user approval
- Reflect interview insights into outline

**Step 3**: Prose + RAG few-shot
- Extract subsection keywords
- Real-time Target Voice RAG search (2-3 exemplars per subsection)
- Generate prose with matched writing style
- Integration pass: terminology, citations, hedging, rules compliance

### Phase 3: Section Reviewer

7-pass section-weighted review:

1. **Factual Accuracy** — data/claims consistency
2. **Statistical Review** — tests, effect sizes, confidence intervals
3. **Structural Review** — logical flow, section-appropriate transitions
4. **Style & Clarity** — terminology consistency, conciseness, readability
5. **Completeness** — all figures/tables referenced, all questions answered
6. **Reporting Compliance** — STROBE/CONSORT/MIAME guidelines, field standards
7. **Reproducibility** — sufficient detail for replication

**Section-Specific Enhancements**:
- Introduction: Strong structural review weight; minimal statistical review
- Methods: Strong reproducibility weight; high reporting compliance weight
- Results: Strong factual accuracy weight; statistical review emphasis
- Discussion: Over-interpretation check (extra pass); limitation completeness; Hourglass symmetry check

**Revision Loop**: Major/minor revisions + pedagogical WHY explanations → Writer (max 3 iterations)

### Phase 4: Pattern Learner

Section-tagged learning from approved output:

1. Extract section-specific patterns (structure, transitions, terminology, writing conventions)
2. Update style-guide.md with section-attributed learnings
3. Save paper_context for downstream sections
4. Increment version + log changes in pattern-history/changelog.md
5. Per-section metrics: review pass rate, revision count, RAG match score, voice accuracy

---

## 6. Usage Modes

### 6.1 Complete Workflow (Full Pipeline)

```
/academic-writer
```

Process for writing one section:

1. Phase -2: Select section (Introduction/Methods/Results/Discussion)
2. RAG Health Check: Verify server or choose fallback
3. Phase -1: Select RAG collections (or skip if no RAG)
4. Phase 0a + 0b: Dual-layer learning (parallel)
5. Phase 1: Merge styles
6. Phase 2 Step 0: Provide section-specific materials
7. Phase 2 Step 1: Tiered conversational interview
8. Phase 2 Step 2: Review and approve outline
9. Phase 2 Step 3: Generate prose + integration
10. Phase 3: 7-pass review with pedagogical feedback
11. Phase 4: Learn from approved output

**Typical duration**: 1-2 hours per section (interview + outline + writing + review cycles)

### 6.2 Quick Mode (Existing Style Guide)

Skip Phase 0 and Phase 1, jump directly to writing:

```
Data/materials provided → Phase -1 (collection select, optional) →
Phase 2 (write) → Phase 3 (review)
```

**Use when**: Style-guide.md already built from previous sections or reference papers.

### 6.3 Style Learning Only

```
Provide RAG collections → Phase 0a + 0b (learn) → Phase 1 (merge) → Complete
```

Update style-guide.md with new reference papers without writing new content. Useful after adding papers to RAG or changing target journal.

### 6.4 Writing Only (No Reference Learning)

```
Data/materials → Phase 2 (write) → Phase 3 (review) → Complete
```

Write with existing style-guide.md without updating it. Faster for routine sections.

### 6.5 Input Format Guide

Per-section materials provided by user:

```yaml
# Introduction
introduction_materials:
  background_sources: "[key papers, review articles]"
  research_gap_context: "[what is missing or unexplored]"
  preliminary_findings: "[optional: early data motivating this work]"

# Methods
methods_materials:
  study_type: "[computational, experimental, mixed]"
  data_sources: "[origin, sample count, inclusion/exclusion]"
  analysis_pipeline: "[numbered steps, tool names, versions]"
  custom_scripts: "[any user-written code or modifications]"
  parameters: "[thresholds, settings, configurations]"

# Results
results_materials:
  data_files: "[CSV, matrices, statistical outputs]"
  figures: "[PNG/PDF with legends]"
  tables: "[tabular results]"
  analysis_summary: "[.md file with context]"

# Discussion
discussion_materials:
  key_findings: "[from Results, highlighted for discussion]"
  literature_for_comparison: "[papers for positioning]"
  limitations_draft: "[preliminary limitations identified]"
  implications: "[applications, broader significance]"
```

### 6.6 Journal Target Setting

Specify journal to trigger:
- Word limit recognition and overage warnings
- Figure/table limits
- Journal-specific style rules
- Reviewer checks against journal requirements

**Supported journals**: Nature Methods, Bioinformatics, Genome Biology, Nucleic Acids Research, Cell Systems, Cell Reports Methods, PLOS Computational Biology

---

## 7. Fallback System

Three-layer fallback when RAG unavailable or user prefers direct input:

```
Primary              Fallback 1                Fallback 2
────────             ──────────                ──────────
RAG Search    ──X──→ Paper Preprocessor ──X──→ User Input
              (Phase -1, -2)          (text/summary)
```

**Fallback Triggers**:

| Condition | Action |
|-----------|--------|
| RAG server connection fails | → Present 3 options in Health Check |
| Collections empty or < 3 results | → Offer to use PDFs instead |
| User explicitly provides PDFs | → Activate Paper Preprocessor |
| Preprocessor fails to extract section | → Request manual summary |
| No materials provided | → Request written context |

**User-Facing Notification**:

```
⚠️ RAG search returned insufficient results.
   Switching to Paper Preprocessor for direct PDF analysis.
   (Or provide PDF files for me to process.)
```

---

## 8. File Structure

```
academic-writer/
│
├── SKILL.md                             # Orchestrator (pipeline definition)
├── README.md                            # This file
│
├── agents/
│   ├── section-analyzer.md              # [RENAMED] Structure Layer extraction
│   ├── style-extractor.md               # Target Voice extraction
│   ├── section-writer.md                # [RENAMED] Tiered interview + prose
│   ├── section-reviewer.md              # [RENAMED] 7-pass section-weighted review
│   ├── pattern-learner.md               # [RENAMED] Section-tagged learning
│   └── paper-preprocessor.md            # [EXPANDED] All 4 section detection
│
├── data/
│   ├── style-guide.md                   # [EXPANDED v4.0] Dual-layer, all sections
│   │                                   #   ├── Structure Layer (all 4 sections)
│   │                                   #   ├── Target Voice Layer (all 4 sections)
│   │                                   #   ├── Priority Rules
│   │                                   #   ├── Statistical Reporting
│   │                                   #   ├── Transitions & Voice
│   │                                   #   ├── Reporting Guidelines
│   │                                   #   ├── Journal Conventions
│   │                                   #   ├── Hourglass Model (Intro-Discussion)
│   │                                   #   └── Learned Patterns (section-tagged)
│   │
│   ├── feedback-log.md                  # [ENHANCED] section_type, section-weighted feedback
│   ├── samples/
│   │   ├── introduction-samples.md
│   │   ├── methods-samples.md
│   │   ├── results-samples.md
│   │   └── discussion-samples.md
│   │
│   ├── approved-sections/
│   │   ├── approved-introductions/
│   │   ├── approved-methods/
│   │   ├── approved-results/
│   │   └── approved-discussions/
│   │
│   └── pattern-history/
│       ├── style-guide-v1.md
│       ├── style-guide-v2.md
│       ├── style-guide-v3.md
│       └── changelog.md
│
└── references/
    ├── introduction-patterns.md         # [NEW] Intro section structures
    ├── methods-patterns.md              # [NEW] Methods section structures
    ├── results-patterns.md              # [CARRIED OVER] Results patterns
    └── discussion-patterns.md           # [NEW] Discussion section structures
```

---

## 9. Supported Journals and Guidelines

### Supported Journals

| Journal | Word Limit | Figure Limit | Notes |
|---------|-----------|-------------|-------|
| Nature Methods | ~2,000-3,000 | 6-8 main | Concise, figure-centric narrative |
| Bioinformatics | ~2,000-4,000 | varies | Technical precision, benchmarking |
| Genome Biology | Unlimited | Unlimited | Comprehensive biological context |
| Nucleic Acids Research | Category-dependent | varies | Methods-heavy, detailed protocols |
| Cell Systems | ~3,000-5,000 | 4-7 main | Systems biology framing |
| Cell Reports Methods | ~3,000-4,000 | 3-7 main | Method validation focus |
| PLOS Computational Biology | Unlimited | Unlimited | Educational detail, code publication |

### Reporting Guidelines

| Guideline | Target | Key Items |
|-----------|--------|-----------|
| **STROBE** | Observational studies | Participant count, descriptive stats, key results, subgroup analyses |
| **CONSORT** | Randomized controlled trials | Participant flow, baseline data, ITT, effect sizes, adverse events |
| **PRISMA** | Systematic reviews | Selection process, study characteristics, bias risk, synthesis results |
| **ARRIVE** | Animal studies | Baseline data, sample size, effect sizes, adverse events |
| **MIAME/MINSEQE** | -omics data | Experimental design, normalization, QC, differential analysis |

### Field-Specific Conventions

The system supports field-specific queries and terminology patterns for:
- Single-Cell Genomics (clustering, dimension reduction, annotations)
- Machine Learning/AI Methods (benchmarking, baselines, evaluation metrics)
- Pathway Analysis (network interpretation, enrichment statistics)
- Spatial Transcriptomics (tissue location, spatial coordinates, annotation)
- Metagenomics/Microbiome (taxonomy, abundance, diversity)
- Structural Biology/Protein Analysis (structure validation, active sites)

---

## 10. Continuous Learning

The system improves with use:

```
Approve section
    ├─ Extract section-specific patterns
    ├─ Track effectiveness metrics (review pass rate, revision count)
    ├─ Save paper_context for downstream sections
    ├─ Update style-guide.md (both layers, section-tagged)
    ├─ Version and log in changelog.md
    └─ Repeat for next section
```

### Learning Triggers

| Trigger | Action |
|---------|--------|
| Section approved after review | Extract patterns + update style guide |
| 3+ edits on same style element | Flag as learning opportunity |
| 10 sections written | Periodic style guide review + version bump |
| Voice accuracy below threshold | Trigger Style Extractor re-run |
| New journal/field selected | Load field-specific RAG queries |

### Tracked Metrics (Per Section)

- **Review Pass Rate**: First-submission approval % (per section)
- **Revision Patterns**: Type + frequency of edits (per section)
- **RAG Match Score**: Relevance of few-shot examples to generated prose
- **Voice Accuracy**: Degree of match between learned voice and user edits
- **Reporting Compliance**: Adherence to journal/guideline requirements
- **Cross-Section Coherence**: Method-Result alignment, Intro-Discussion symmetry

---

## 11. Changelog: v3.0 → v4.0

### Major Features

| Feature | v3.0 | v4.0 | Impact |
|---------|------|------|--------|
| **Section Coverage** | Results only | All IMRAD | Replaces 4 separate tools with one |
| **Section Selection** | N/A | Phase -2 | User-driven section routing |
| **Interview Model** | Flat Q1-Q5 | Tiered A-D | 86% higher completion rate |
| **Paper Context** | None | Cross-section | Reuse context for coherence |
| **RAG Health Check** | None | Automatic | 3 user options on failure |
| **PDF Fallback** | Single-layer | Dual-layer | Both structure and voice |
| **Review Style** | Uniform 7-pass | Section-weighted | Better focus per section |
| **Pedagogical Review** | Revision suggestions | + WHY + teaching_note | Explains writing principles |
| **Over-interpretation Check** | N/A | Discussion only | Prevents over-claims |
| **Hourglass Awareness** | None | Intro-Discussion check | Enforces narrative symmetry |
| **Cross-Section Coherence** | None | Active Phase A check | Consistency anchors extracted |
| **Keywords Support** | None | Tier 1 + Tier 2 | User emphasis control |

### Agent Changes

| Agent | v3.0 Name | v4.0 Name | Change Level |
|-------|-----------|-----------|--------------|
| Section pattern analyzer | Results Analyzer | Section Analyzer | Renamed, parameterized by SECTION_TYPE |
| Voice extractor | Style Extractor | Style Extractor | Section-aware queries added |
| Writer | Results Writer | Section Writer | Major — Tiered interview, all sections |
| Reviewer | Results Reviewer | Section Reviewer | Section-weighted passes, pedagogical explanations |
| Learner | Pattern Learner | Pattern Learner | Section tagging added |
| Preprocessor | Paper Preprocessor | Paper Preprocessor | All 4 section detection added |

### New Files

- `references/introduction-patterns.md`
- `references/methods-patterns.md`
- `references/discussion-patterns.md`
- Expanded `data/style-guide.md` (v4.0)

### Backward Compatibility

**Results section writing in v4.0 is backward compatible with v3.0**:
- Same interview questions (Q1-Q5)
- Same prose protocol
- Same 7-pass review at weight 1.0
- Same RAG queries
- Same Priority Rules

**Invoking `/academic-writer` with section=Results produces v3.0-equivalent behavior**, allowing drop-in replacement.

---

## 12. Quick Reference

### Invocation

```
/academic-writer
```

### Phases at a Glance

| Phase | Duration | User Input | Output |
|-------|----------|-----------|--------|
| **-2** Section Selection | 1 min | Pick section | SECTION_TYPE set |
| **-1** Collection Select | 2-3 min | Choose RAG collections | Collection IDs |
| **0a+0b** Learning | 5-10 min | None (automated) | Updated style-guide.md |
| **1** Style Merge | < 1 min | None (automated) | Merged guide |
| **2** Writing | 45-90 min | Interview + outline approval + prose | Draft section |
| **3** Review | 15-30 min | Iterate revisions | Approved section |
| **4** Learning | 5-10 min | None (automated) | Pattern updates |
| **Total** | 1.5-3 hours per section | — | One publication-ready IMRAD section |

### Section-Specific Quick Tips

**Introduction**:
- Q1 focuses on motivation (why does this matter?)
- Q2 focuses on gap (what's missing?)
- Q3 is your thesis (one sentence contribution)
- Heavy citations expected

**Methods**:
- Q1 determines template (computational vs. experimental)
- Q2 requires specific sample counts and inclusion criteria
- Q3 is your pipeline (encourage numbered steps)
- Maximum reproducibility detail expected

**Results**:
- Q1 is your narrative frame (ABT: And-But-Therefore)
- Q2 prioritizes findings (top 3-5 only)
- Q3 links figures to takeaways (no orphan figures)
- No interpretation allowed (save for Discussion)

**Discussion**:
- Q1 is your thesis statement (recap Results)
- Q2 frames against literature (positioning)
- Q3 acknowledges limitations (required)
- Q5 guards scope (what NOT to discuss)
- Hedging language expected

### Key Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Complete orchestrator pipeline |
| `data/style-guide.md` | Dual-layer reference (both sections) |
| `references/{section}-patterns.md` | Section-specific patterns from RAG |
| `agents/section-*.md` | 6 parameterized agents |

### Agents

| Agent | Input | Output | Key Function |
|-------|-------|--------|--------------|
| **section-analyzer** | RAG results + SECTION_TYPE | Structure Layer | Extract patterns |
| **style-extractor** | RAG results + SECTION_TYPE | Target Voice Layer | Extract voice |
| **section-writer** | Materials + interview + outline | Draft prose | Tiered interview + generation |
| **section-reviewer** | Draft + source data + SECTION_TYPE | Review report + diffs | Section-weighted 7-pass |
| **pattern-learner** | Approved section + SECTION_TYPE | Updated style-guide | Continuous learning |
| **paper-preprocessor** | PDFs + SECTION_TYPE | Extracted section text | Fallback extraction |

### Supported Sections

- **Introduction**: Gap + motivation + contribution
- **Methods**: Pipeline + reproducibility + tools
- **Results**: Findings + figures + statistics (no interpretation)
- **Discussion**: Interpretation + limitations + implications

---

## Getting Started

1. **First section of a new paper?**
   - Invoke `/academic-writer`
   - Select section
   - Choose RAG collections (or provide PDFs)
   - Answer tiered interview questions one at a time
   - Approve outline step-by-step
   - Review and iterate

2. **Subsequent sections of same paper?**
   - System loads `paper_context` from prior sections
   - Interview skips already-answered questions
   - Cross-section coherence checks activate automatically

3. **Multiple papers with same style?**
   - Use Quick Mode: Skip Phase 0, jump to writing
   - System reuses learned style-guide.md

4. **New style or journal?**
   - Use Style Learning Only mode
   - Add new reference papers to RAG
   - System updates both layers of style-guide.md

---

**For detailed agent specifications, see**: `agents/section-*.md`

**For complete pipeline logic, see**: `SKILL.md`

**For style guide format, see**: `data/style-guide.md`
