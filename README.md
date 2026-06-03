# Academic Writer v3.1 — Write Publication-Quality IMRAD Sections

Multi-agent system for writing publication-quality Introduction, Methods, Results, and Discussion sections for academic research articles. Clarifies task scope before drafting, learns writing patterns from direct references and RAG-based dual-layer analysis, gathers section-specific research intent via tiered conversational interviews, generates outlined drafts, and improves continuously through feedback.

**Version**: 3.1 | **Released**: 2026-06-02 | **Skill Invocation**: `/academic-writer`

---

## What's New in v3.1

| Change | Details |
|--------|---------|
| **Deep Interview Gate** | Every invocation now starts by clarifying target result, scope, constraints, completion criteria, and existing context before section selection. |
| **Reference Paper Gate** | After section selection, the workflow always asks separately for run-specific structure references and voice/tone references. |
| **Results Figure/Table Legends** | Results writing now drafts main and supplementary figure/table legends when display metadata is available. |
| **No-Invention Legend Policy** | Partial legends preserve known information and mark unresolved fields as `[needs: ...]` instead of inventing sample sizes, statistics, encodings, scale bars, cohort labels, or abbreviations. |
| **Legend-Aware Analysis and Review** | Section Analyzer, Style Extractor, Section Writer, Section Reviewer, style guide, and Results references now include legend/caption extraction, writing, and validation contracts. |

---

## Table of Contents

1. [System Overview](#1-system-overview)
2. [Architecture](#2-architecture)
3. [Agent Details](#3-agent-details)
4. [RAG Integration](#4-rag-integration)
5. [Pipeline Details (Phase -3 through Phase 4)](#5-pipeline-details)
6. [Usage Modes](#6-usage-modes)
7. [Fallback System](#7-fallback-system)
8. [File Structure](#8-file-structure)
9. [Supported Journals and Guidelines](#9-supported-journals-and-guidelines)
10. [Continuous Learning](#10-continuous-learning)
11. [Quick Reference](#11-quick-reference)

---

## 1. System Overview

### What It Does

Multi-agent system for writing publication-quality IMRAD sections (Introduction, Methods, Results, Discussion) for academic research articles. The user selects one section per invocation and provides data, context, and reference papers. The system clarifies task scope first, learns dual-layer writing patterns from direct references and RAG collections (structure + voice), gathers section-specific intent through a tiered conversational interview, generates an outlined draft, reviews it with pedagogical feedback, and continuously improves through approved outputs.

**Single section per invocation**: Each call to `/academic-writer` handles one IMRAD section completely. To write a full paper, invoke once per section in sequence.

### Core Philosophy

**Copilot, not pilot.** The user owns the narrative; the system executes. Every question has a smart default. Every decision can be overridden. The system gathers intent through conversation, not interrogation.

### Key Features

| Feature | Description |
|---------|-------------|
| **Deep Interview Gate** | Clarifies target result, section scope, constraints, completion criteria, and existing context before the writing pipeline begins |
| **Reference Paper Gate** | Asks separately for run-specific structure references and voice/tone references before RAG selection |
| **Dual-Layer Learning** | Learns section structure from one reference source, voice/tone from another |
| **Tiered Interview** | Four-phase conversational approach (Context Detection → Core Questions → Adaptive Follow-ups → Intent Confirmation) |
| **Methods/Results Blueprint Gate** | Requires user-approved hierarchical skeleton + verification matrix before Methods or Results prose generation, then persists the approved Blueprint for reviewer alignment |
| **Results Closing Takeaways** | Requires empirical/evaluation Results subsections to close with a concise data-backed takeaway while keeping interpretation in Discussion |
| **Results Figure/Table Legends** | Drafts legends for available main and supplementary figures/tables and marks missing legend fields explicitly |
| **User-Specified Keywords** | Tier 1 (must-emphasize) and Tier 2 (natural-include) keywords and key sentences are tracked through writing and verified in review |
| **Section-Specific Configuration** | Writing rules, review weights, and guidance tailored to each IMRAD section |
| **Cross-Section Coherence** | Detects existing sections and enforces consistency (Methods references Results, Discussion references Introduction) |
| **Hourglass Awareness** | Introduction→Discussion symmetry (funnel → inverted funnel) |
| **Pedagogical Feedback** | Review feedback explains the "why" behind each suggestion, not just what to fix |
| **Continuous Learning** | Approved outputs update style guides automatically; patterns become section-specific |
| **Fallback System** | PDF preprocessing when RAG unavailable; dual-layer PDF selection mirrors RAG collections |

---

## 2. Architecture

### Complete Pipeline

```
Phase -3: Deep Interview Gate
┌─────────────────────────────────────┐
│ Clarify target result, scope,       │
│ constraints, done criteria, context │
│ → Emits academic_writer_brief       │
└────────────┬────────────────────────┘
             │
Phase -2: Section Selection
┌─────────────────────────────────────┐
│ "Which section? Intro/Methods/      │
│  Results/Discussion?"               │
│ → Sets SECTION_TYPE using brief     │
└────────────┬────────────────────────┘
             │
Phase -1.5: Reference Paper Gate
┌─────────────────────────────────────┐
│ Ask for structure references        │
│ Ask voice/tone reference policy     │
│ → Emits run_reference_layers        │
└────────────┬────────────────────────┘
             │
RAG Health Check
┌────────────▼────────────────────────┐
│ list_collections() available?        │
│ → Yes: Phase -1  → No: 3 options    │
└────────────┬────────────────────────┘
             │
Phase -1: Collection Selection
┌────────────▼────────────────────────┐
│ Q1: Structure Layer collection      │
│ Q2: Target Voice collection         │
│ (after direct-reference gate)       │
└────────────┬────────────────────────┘
             │
Phase 0a + 0b: Dual-Layer Learning (PARALLEL)
┌──────────────────────┐  ┌──────────────────────┐
│  Direct refs + RAG   │  │  Direct refs + RAG   │
│  (Structure)         │  │  (Voice)             │
│       ↓              │  │       ↓              │
│  Section Analyzer    │  │  Style Extractor     │
│       ↓              │  │       ↓              │
│  Structure Layer     │  │  Target Voice Layer  │
└─────────┬────────────┘  └─────────┬────────────┘
          │                         │
          └────────────┬────────────┘
                       │
Phase 1: Style Merge
┌──────────────────────▼─────────────┐
│ Merge Structure + Voice Layers      │
│ Update style-guide.md               │
└──────────────────────┬──────────────┘
                       │
Phase 2: Section Writer (Tiered Interview + Prose)
┌──────────────────────▼─────────────┐
│ Step 0: Input Collection + brief    │
│ Step 1: Tiered Interview            │
│   Phase A: Context Detection        │
│   Phase B: Core Questions (1×1)     │
│   Phase C: Adaptive Follow-ups      │
│             + Keyword Detection     │
│   Phase D: Intent Confirmation      │
│ Step 2: Outline / Blueprint Gate    │
│   (Methods/Results require approval)│
│ Step 2e: Persist Blueprint to       │
│   paper_context + Reviewer handoff  │
│ Step 3: Prose + RAG few-shot        │
│   (constrained by approved Blueprint│
│    for Methods/Results)             │
│   Results: prose + Figure Legends   │
└──────────────────────┬──────────────┘
                       │
Phase 3: Section Reviewer
┌──────────────────────▼─────────────┐
│ Section-weighted review             │
│ + pedagogical WHY explanations      │
│ + section-specific checks           │
│ + Pass 8: Blueprint Alignment       │
│   (Methods/Results only)            │
│ + Results legend factual checks     │
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

The system maintains two independent learning layers. In v3.1, each layer can be informed by user-provided direct references for the current run before RAG collections are applied.

| Layer | Role | Default Collection | Learning Content |
|-------|------|-------------------|------------------|
| **Structure Layer** | Section architecture and organization | agentpaper | Subsection hierarchies, figure/table integration, legend/caption structure, flow patterns, section-specific structure |
| **Target Voice Layer** | Writing style, tone, and expression | mypaper | Sentence patterns, transitions, hedging frequency, discipline-specific terminology, statistical and legend wording conventions |

**Priority Rules** (applied per section):
- Run-specific structure references → highest priority for section organization and figure/table placement
- Run-specific voice/tone references → highest priority for sentence rhythm, transitions, and wording
- Structure elements (subsection headers, figure placement, legend completeness contracts) → Structure Layer priority
- Voice/tone elements (sentence patterns, hedging, "we" usage) → Target Voice Layer priority
- Statistical reporting formats → Target Voice Layer priority
- Figure/table legend wording → Structure Layer contract with Target Voice overlay
- Terminology and jargon → Target Voice Layer priority
- Transitions and connectives → Target Voice Layer priority

### Cross-Section Coherence

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

All agents are parameterized by `SECTION_TYPE`, enabling a single pipeline to handle all four IMRAD sections.

### 3.1 Section Analyzer

**File**: `agents/section-analyzer.md`

Extracts structural patterns specific to `SECTION_TYPE` from Structure Layer RAG results.

**Section-Specific Analysis Dimensions**:

| Section | Key Dimensions |
|---------|-----------------|
| **Introduction** | Literature review breadth, gap positioning clarity, contribution statement placement, background scope, research question explicitness |
| **Methods** | Pipeline step granularity, reproducibility detail, software version specificity, subsection organization, parameter documentation |
| **Results** | Finding presentation order, figure-result alignment, legend/caption patterns, subsection hierarchy, statistical reporting density, comparison structure |
| **Discussion** | Finding-literature positioning, limitation acknowledgment depth, future direction scope, interpretation hedging, broader impact framing |

**Output**: Updated `data/style-guide.md` Structure Layer section (parameterized by SECTION_TYPE).

### 3.2 Style Extractor

**File**: `agents/style-extractor.md`

Extracts voice/tone from Target Voice Layer, with section-aware focus.

**Section-Specific Voice Extractions**:

| Section | Voice Elements to Extract |
|---------|--------------------------|
| **Introduction** | Field significance framing, "However/Despite" hedging frequency, "We propose/introduce" cadence, citation density, background narrative pacing |
| **Methods** | Procedural language (passive/active ratio), "We used/employed" patterns, parameter specification style, tool attribution conventions |
| **Results** | Finding presentation verbs ("found/demonstrated/showed"), figure reference cadence, legend/caption wording, statistical reporting abbreviations, result-based connectives |
| **Discussion** | "Suggest/indicate/demonstrate" patterns, consistency phrases ("consistent with prior"), limitation disclaimer frequency, interpretation hedging, future direction tone |

**Output**: Updated `data/style-guide.md` Target Voice Layer section (parameterized by SECTION_TYPE).

### 3.3 Section Writer

**File**: `agents/section-writer.md`

Four-step process with tiered conversational interview.

**Input Collection** (Step 0, section-specific):

```yaml
research_materials:
  introduction:
    background_sources: "[key papers, review articles]"
    research_gap_context: "[what's missing or unexplored]"
    preliminary_findings: "[optional: early data motivating this work]"

  methods:
    study_type: "[computational, experimental, mixed, etc.]"
    data_sources: "[where data came from]"
    analysis_pipeline: "[tool names, versions, order of steps]"
    custom_scripts: "[any user-written code or modifications]"

  results:
    data_files: "[CSV, matrices, statistical output]"
    figures:
      - file: "[path or null if description-only]"
        id: "Figure 1|Supplementary Figure 1"
        description: "[what this figure shows]"
        panels: "[panel labels, descriptions, visual encodings, statistics]"
        claim_supported: "[claim/question this figure supports]"
        figure_rationale: "[why this figure is needed]"
        legend_notes: "[known title, abbreviations, caveats, missing fields]"
    tables:
      - file: "[path or null if description-only]"
        id: "Table 1|Supplementary Table 1"
        description: "[what this table contains]"
        row_column_semantics: "[what rows/columns mean]"
        value_semantics: "[counts, percentages, coefficients, p-values, etc.]"
        notation: "[bolding, asterisks, thresholds, NA/dash meaning]"
    analysis_summary: "[.md file with context]"

  discussion:
    key_findings: "[from Results, highlighted]"
    literature_for_comparison: "[papers to position against]"
    limitations_draft: "[preliminary limitations identified]"
    implications: "[applications, broader significance]"
```

**Step 1: Tiered Conversational Interview**:

```
Phase A: Context Detection (automated + 0-1 question)
├─ Load academic_writer_brief from Phase -3
├─ Auto-scan workspace for existing sections
├─ Load paper_context from prior section interviews
├─ Detect target journal, research field, user expertise
└─ If other sections exist →
     "I see you have Results. Should I reference it while writing Methods?"

Phase B: Core Interview (5 section-specific questions, one-at-a-time)
├─ Each answer acknowledged before next Q
├─ Smart defaults for every question (user can skip)
├─ Conversational tone: "Tell me about..." not "Specify the..."
└─ Purpose transparency: brief note on why each Q matters

Phase C: Adaptive Follow-ups (0-3 questions, conditional)
├─ Context-deepening: probe vague Phase B answers
├─ Context-enhancing: introduce considerations user may not have thought of
├─ Keyword Detection: LLM identifies candidate Tier 1/Tier 2 keywords from
│   user responses and presents them for explicit user confirmation
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
| Q4 | How much background knowledge should Introduction assume? | scope-calibration | Adapt to target journal audience |
| Q5 | Key reference papers or positioning papers? | reference-anchoring | Can trigger RAG search |

**Methods (Phase B, 5 core + follow-ups)**:

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What type of study is this? | structural-classifier | Determines section template |
| Q2 | Data/samples: what, how much, from where? | core-content | Follow-up: inclusion/exclusion criteria |
| Q3 | Describe the analysis pipeline step-by-step. | workflow-elicitation | Encourage numbered steps |
| Q4 | Software/tools and versions used? | reproducibility | Follow-up: custom scripts, parameters |
| Q5 | Preferred subsection order for Methods? | structure-preference | Default: pipeline order |

**Results (Phase B, 5 core)**:

| # | Question | Type | Description |
|---|----------|------|-------------|
| Q1 | What is the central narrative of these Results? | narrative-framing | ABT hint: "And..., But..., Therefore..." |
| Q2 | Top 3-5 findings, ranked by importance. | priority-setting | Added ranking + number constraint |
| Q3 | For each Figure/Table: what claim does it support, what does it show, why is it needed, what is the key takeaway, and what legend details are available? | visual-inventory + legend-inventory | Ensures purposeful figure integration and legend readiness |
| Q4 | Preferred subsection order for Results? | structure-preference | Same as pipeline order |
| Q5 | Most important comparison: vs baseline, vs existing methods, across conditions? | comparison-framing | Frames key competitive value |

**Discussion (Phase B, 5 core + follow-ups)**:

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | Most important conclusion from Results? (one sentence) | thesis-statement | Anchors Discussion opening |
| Q2 | Key points to discuss vs. existing literature? | literature-positioning | Follow-up: "2-3 key papers to compare?" |
| Q3 | Main limitations of this study? | limitation-awareness | Follow-up: methodological? data? generalizability? |
| Q4 | Unexpected or hard-to-explain findings? | anomaly-exploration | Shows analytical maturity |
| Q5 | Topics explicitly to avoid in Discussion? | scope-guard | Prevents over-interpretation |

**User-Specified Keywords (all sections)**:

The writer accepts an optional Tier 1 / Tier 2 keyword list at Step 0 or detects candidates during Phase C:

| Tier | Treatment | Placement |
|------|-----------|-----------|
| **Tier 1 (Emphasize)** | Linguistic emphasis markers: "notably", "importantly", "a key finding" | Topic sentences, section opening/closing, near key figures |
| **Tier 2 (Include)** | Woven naturally, no special emphasis | Anywhere appropriate in the flow |

LLM-detected keyword candidates from interview responses are presented to the user for explicit confirmation before being assigned to a tier. The Reviewer's Pass 5 Completeness check verifies all tiers are properly placed.

**Step 2: Interactive Outline / Blueprint Gate**:
- Generate section-appropriate outline structure for Introduction/Discussion
- Generate Methods/Results Blueprint with hierarchical skeleton + verification matrix
- Require explicit Blueprint approval before Methods/Results prose
- Persist approved Methods/Results Blueprint to `paper_context` (Step 2e) and pass to Reviewer as `approved_blueprint`

**Methods/Results Blueprint contract**:

| Section | Matrix contract | Handoff |
|---------|-----------------|---------|
| Methods | `Block`, `Subsection`, `Procedure/Step`, `Data/Input`, `Tool/Version`, `Parameters`, `Output`, `Reproducibility Risk` | Persist to `paper_context.methods_blueprint`; Reviewer receives the same object as `approved_blueprint` |
| Results | `Block`, `Subsection`, `Result Rationale`, `Claim/Finding`, `Evidence Source`, `Figure/Table`, `Figure/Table Rationale`, `Statistics`, `Closing Takeaway`, `Scope Limits`, `Legend Status / Missing Fields` | Persist to `paper_context.results_blueprint`; Reviewer receives the same object as `approved_blueprint` |

Lite Mode (triggered only by explicit user request: "outline only", "skip blueprint", or "lite mode") emits a reduced `approved_blueprint` with `approval_status: skipped_by_user`, so the Reviewer can intentionally skip Blueprint Alignment instead of treating the input as missing.

**Step 3: Prose + RAG few-shot**:
- Extract subsection keywords
- Real-time Target Voice RAG search (2-3 exemplar paragraphs per subsection)
- Prose generation with matched style
- For Methods/Results, constrain prose to the approved Blueprint and return to Blueprint approval before adding new claims, method steps, figures/tables, statistics, tools, parameters, data sources, outputs, figure/algorithm placements, or subsections
- For Results, close each empirical/evaluation subsection with a one-sentence data-backed takeaway; overview, dataset, or benchmark-construction subsections may close with roadmap or evaluation-purpose language
- For Results with available displays, output `## Figure Legends` after prose, separated into Main Figures, Main Tables, Supplementary Figures, and Supplementary Tables as applicable
- Mark unresolved legend-only fields as `[needs: ...]`; never invent sample sizes, p-values, tests, encodings, scale bars, cohort labels, or abbreviations
- Integration pass: terminology, citations, hedging, writing rules compliance, keyword placement verification

**paper_context fields** (accumulated across sections):

```yaml
paper_context:
  academic_writer_brief: "[Phase -3 target result, scope, constraints, completion criteria]"
  run_reference_layers: "[Phase -1.5 structure and voice/tone references for this run]"
  research_gap: "[from Introduction interview]"
  hypotheses: "[from Introduction interview]"
  study_design: "[from Methods interview]"
  key_findings: "[from Results interview]"
  methods_blueprint: "[approved Methods Blueprint object, or skipped_by_user Lite Mode object]"
  results_blueprint: "[approved Results Blueprint object, or skipped_by_user Lite Mode object]"
  target_journal: "[set once, used everywhere]"
  reporting_guideline: "[set once]"
  research_field: "[detected once]"
  user_expertise: "[detected once]"
  existing_sections:
    introduction: "[file path or null]"
    methods: "[file path or null]"
    results: "[file path or null]"
    discussion: "[file path or null]"
```

**Output**: Draft section, `academic_writer_brief` summary, optional `run_reference_layers` summary, `approved_blueprint` metadata for Methods/Results, and Results figure/table legends when available.

### 3.4 Section Reviewer

**File**: `agents/section-reviewer.md`

Multi-pass review with section-specific weights and pedagogical explanations.

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
| 8. Blueprint Alignment | N/A | **1.4** | **1.4** | N/A |

Pass 5 Completeness also verifies all user-specified Tier 1 and Tier 2 keywords are correctly placed, generating a Keyword Coverage Report.

**Pass 9 (Discussion only): Over-interpretation Check** — each interpretation must be traceable to a specific Result; hedging must be appropriate; alternative explanations must be considered; causal language for correlational findings is flagged.

**Revision Loop**: Major/minor revisions → Writer with `revision_diff` + pedagogical WHY and `teaching_note` explaining writing principles (max 3 iterations).

### 3.5 Pattern Learner

**File**: `agents/pattern-learner.md`

Learns from approved sections, with section-specific metrics.

**Learning Sources** (per section):
1. Approved sections (section_type tagged)
2. Reviewer feedback (section-weighted)
3. Reference patterns (from section-specific RAG)
4. RAG effectiveness metrics
5. Style Extractor feedback (voice accuracy per section)

**Output**: Versioned `data/style-guide.md` updates + `data/pattern-history/changelog.md` with section attribution.

### 3.6 Paper Preprocessor

**File**: `agents/paper-preprocessor.md`

Fallback processor when RAG unavailable. Detects and extracts all four IMRAD sections from PDFs.

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
  description: "Reference papers for learning section structure and organization"
  default_collection: "agentpaper"
  default_id: "06bd503e-fd03-4451-8ce7-7c1ee5012584"
  note: "User can select any available collection"

target_voice:
  description: "Papers whose writing style/voice to emulate"
  default_collection: "mypaper"
  default_id: "fd3c6832-12b2-46e6-b724-a2fe62f5da09"
  note: "User can select any available collection; same collection allowed for both layers"
```

### MCP Tools Used

```yaml
rag_tools:
  list_collections: "mcp__langconnect-rag__list_collections"
  search_documents: "mcp__langconnect-rag__search_documents"
  agentic_search: "mcp__langconnect-rag__agentic_search"

search_parameters:
  search_type: "hybrid"
  limit: 5
```

### Section-Specific RAG Query Strategies

**Structure Layer queries** (for Section Analyzer, 4 queries per section):

| Section | Query 1 | Query 2 | Query 3 | Query 4 |
|---------|---------|---------|---------|---------|
| Introduction | "introduction background motivation field significance" | "existing approaches limitations gap" | "we propose we introduce contribution" | "research question aims objectives" |
| Methods | "methods implementation pipeline architecture" | "preprocessing quality control data preparation" | "evaluation benchmark performance validation" | "software tools parameters version" |
| Results | "Results section structure organization subsection" | "findings statistical analysis benchmark performance" | "Figure Table panel results comparison" | "methodology evaluation framework validation" |
| Discussion | "discussion interpretation significance meaning" | "limitation future directions caveats" | "compared to prior work consistent with previous" | "broader impact implications applications" |

**Target Voice Layer queries** (for Style Extractor, 3 section-specific + 1 field-specific):

| Section | Query 1 | Query 2 | Query 3 | Query 4 |
|---------|---------|---------|---------|---------|
| Introduction | "field significance importance relevance" | "however despite gap limitation remains" | "we introduce present propose here" | *field-specific* |
| Methods | "procedures implementation experimental design" | "we used employed applied implemented" | "parameters settings configuration threshold" | *field-specific* |
| Results | "Results section findings statistical analysis" | "we found demonstrated showed revealed indicated" | "Figure Table panel legend caption comparison significantly" | *field-specific* |
| Discussion | "suggest demonstrate indicate consistent with" | "consistent with previous findings prior work" | "limitation future direction caveat" | *field-specific* |

**Real-time few-shot queries** (for Section Writer, during prose):
- Subsection topic keywords → Target Voice collection (primary)
- Structure reference → Structure collection (secondary, when needed)
- Results legend writing → Target Voice query `"figure legend caption panel table notation"` plus `references/legend-patterns.md`

---

## 5. Pipeline Details (Phase -3 through Phase 4)

### Phase -3: Deep Interview Gate

**Purpose**: Clarify the writing task before section selection and prevent premature assumptions.

The orchestrator asks one highest-impact question at a time until it can emit:

```yaml
academic_writer_brief:
  target_result: "[what the user wants produced]"
  likely_section_type: "introduction|methods|results|discussion|unknown"
  scope_included: "[materials/sections/displays included]"
  scope_excluded: "[explicit exclusions or none]"
  constraints: "[journal/style/RAG/language/length/no-assumption constraints]"
  completion_criteria: "[what done means for this run]"
  existing_context: "[prior sections, paper_context, local files, or null]"
  open_questions: "[non-blocking uncertainties]"
```

### Phase -2: Section Selection

**Purpose**: Let the user choose which IMRAD section to write. Sets SECTION_TYPE for all downstream phases.

Orchestrator asks: "Which section would you like to write?" (1. Introduction / 2. Methods / 3. Results / 4. Discussion). If `academic_writer_brief.likely_section_type` is explicit, the orchestrator confirms it briefly or proceeds directly when the user already specified it.

1. User selects one (or states section name directly, including via Phase -3)
2. Set `SECTION_TYPE` = selected section
3. Load `references/{SECTION_TYPE}-patterns.md`
4. Activate the corresponding `section_configs` block
5. Pass both `SECTION_TYPE` and `academic_writer_brief` to downstream agents

### Phase -1.5: Reference Paper Gate

**Purpose**: Ask whether the user has reference papers for this run, then separate structure references from voice/tone references. This complements RAG and remains run-specific unless the user approves long-term learning.

1. Ask for structure reference papers, paths, URLs, or PDFs. If none, record an explicit decline.
2. Always ask the voice/tone reference question. If structure references were provided, the user may reuse the same papers, provide separate voice/tone references, or choose structure only. If no structure reference was provided, the user may still provide voice/tone-only references.
3. Store results as `run_reference_layers.structure_references` and `run_reference_layers.voice_references`.
4. Process structure references through Paper Preprocessor → Section Analyzer.
5. Process voice/tone references through Paper Preprocessor → Style Extractor.
6. Mark unreadable references and continue with RAG/style guide rather than blocking the run.

### RAG Health Check (Before Phase -1)

**Purpose**: Verify that the langconnect-rag MCP server is available before proceeding to collection selection. If unavailable, inform the user and let them decide.

**If RAG is available**: Proceed to Phase -1 (Collection Selection) normally.

**If RAG is unavailable**: Inform user and present 3 options:

1. **Check RAG setup**: Guide user to verify Docker container status and retry
2. **Proceed without RAG**: Skip RAG searches; use Phase -1.5 direct references if provided plus existing style-guide.md
3. **Provide PDFs directly**: Use Paper Preprocessor with dual-layer PDF selection, reusing Phase -1.5 references when already collected

### Phase -1: Collection Selection

**Purpose**: Let the user choose which RAG collections to use for structure learning and voice extraction after the mandatory direct-reference gate. (Skipped if user chose "Proceed without RAG".)

1. Call `mcp__langconnect-rag__list_collections` to get available collections
2. Present collection list with names, document counts, and descriptions
3. Ask: "Which collection to use for the Structure Layer?" (default: agentpaper)
4. Ask: "Which collection to use for the Target Voice Layer?" (default: mypaper)
5. Store selected collection IDs for downstream phases
6. Same collection allowed for both layers. If user says "skip" or "default", use agentpaper + mypaper

### Phase 0a: Structure Pattern Learning (Parallel with 0b)

**Input**: Structure Layer collection + SECTION_TYPE + `run_reference_layers.structure_references`

1. Process `run_reference_layers.structure_references` first when provided
2. Execute 4 section-specific Structure Layer queries against selected collection when RAG is available
3. Pass direct-reference extracts and search results to Section Analyzer agent with SECTION_TYPE
4. Analyzer extracts section-appropriate structural patterns, groups by source document, synthesizes cross-document patterns, and marks direct-reference patterns as run-specific priority
5. Optional: BioMCP enrichment for biological context

**Output**: Run-specific Structure Layer contribution; long-term style-guide.md updates happen only after user approval or Pattern Learner trigger

### Phase 0b: Voice Pattern Learning (Parallel with 0a)

**Input**: Target Voice Layer collection + SECTION_TYPE + `run_reference_layers.voice_references`

1. Process `run_reference_layers.voice_references` first when provided
2. Execute 3 section-specific + 1 field-specific Target Voice Layer queries against selected collection when RAG is available
3. Pass direct-reference extracts and search results to Style Extractor agent with SECTION_TYPE
4. Extractor synthesizes voice/tone patterns, identifies sentence structure distribution, hedging frequency, terminology density, and Results legend/caption wording when applicable
5. Optional: Analyze target journal's house style if collection is journal-specific

**Output**: Run-specific Target Voice Layer contribution; long-term style-guide.md updates happen only after user approval or Pattern Learner trigger

### Phase 1: Style Merge

**Input**: Structure Layer + Target Voice Layer patterns

1. Merge both patterns into unified style-guide.md
2. Apply Priority Rules: resolve conflicts (structure elements prioritize structure layer, voice elements prioritize voice layer)
3. Create merged guide with clear provenance (which patterns came from which layer)

**Output**: Updated `data/style-guide.md` ready for writer and reviewer

### Phase 2: Section Writer (Tiered Interview + Prose)

**Step 0: Input Collection**
Gather section-specific research materials (see Section Writer above), load `academic_writer_brief`, and consume `run_reference_layers` without re-asking decisions already settled upstream.

**Step 1: Tiered Conversational Interview**
- Phase A: Context Detection (auto-scan workspace, detect coherence requirements)
- Phase B: Core Interview (5 section-specific questions, one-at-a-time)
- Phase C: Adaptive Follow-ups (0-3 context-deepening questions) + Keyword Detection
- Phase D: Intent Confirmation (structured summary, user confirms)

**Step 2: Interactive Outline / Blueprint Gate**
- Introduction and Discussion: generate section-appropriate outline
- Methods and Results: generate a Blueprint with hierarchical skeleton + verification matrix
- For Methods/Results, prose is blocked until the user explicitly approves the complete Blueprint
- Lite Mode is allowed only when the user explicitly requests "outline only", "skip blueprint", or "lite mode"

**Methods/Results Blueprint contract**

| Section | Matrix contract | Handoff |
|---------|-----------------|---------|
| Methods | `Block`, `Subsection`, `Procedure/Step`, `Data/Input`, `Tool/Version`, `Parameters`, `Output`, `Reproducibility Risk` | Persist to `paper_context.methods_blueprint`; Reviewer receives the same object as `approved_blueprint` |
| Results | `Block`, `Subsection`, `Result Rationale`, `Claim/Finding`, `Evidence Source`, `Figure/Table`, `Figure/Table Rationale`, `Statistics`, `Closing Takeaway`, `Scope Limits`, `Legend Status / Missing Fields` | Persist to `paper_context.results_blueprint`; Reviewer receives the same object as `approved_blueprint` |

Lite Mode still emits a reduced `approved_blueprint` with `approval_status: skipped_by_user`, so Reviewer can intentionally skip Blueprint Alignment instead of treating the input as missing.

**Step 2e: Blueprint Persistence**
After user approval, the writer persists the Blueprint to `paper_context.methods_blueprint` or `paper_context.results_blueprint` and sends the same object to the Reviewer as `approved_blueprint`. The persisted and handed-off objects must be identical.

**Step 3: Prose + RAG Few-Shot**
- Extract subsection keywords
- Real-time Target Voice RAG search (2-3 exemplar paragraphs per subsection)
- Prose generation with matched style
- For Methods/Results, constrain prose to the approved Blueprint and return to Blueprint approval before adding new claims, method steps, figures/tables, statistics, tools, parameters, data sources, outputs, figure/algorithm placements, or subsections
- For Results, close each empirical/evaluation subsection with a one-sentence data-backed takeaway; overview, dataset, or benchmark-construction subsections may close with roadmap or evaluation-purpose language
- For Results with available displays, generate figure/table legends after prose using `references/legend-patterns.md`
- Integration pass: terminology, citations, hedging compliance, keyword placement verification, and Results legend completeness

**Output**: Draft section (Introduction/Methods/Results/Discussion), `academic_writer_brief` summary, optional `run_reference_layers` summary, plus `approved_blueprint` metadata persisted to `paper_context.methods_blueprint` or `paper_context.results_blueprint` for Methods/Results. Results outputs include `## Figure Legends` when available displays exist.

### Phase 3: Section Reviewer

**Input**: Draft section + source research materials + `approved_blueprint` for Methods/Results + Results-only figure/table legend drafts when generated

- Execute section-weighted review passes (8 passes; Pass 9 for Discussion)
- Pass 1: Factual Accuracy — verify all claims backed by source materials
- Pass 2: Statistical Review — verify statistical reporting, confidence intervals, p-values
- Pass 3: Structural Review — verify subsections follow expected architecture
- Pass 4: Style & Clarity — verify readability, sentence structure, word choice
- Pass 5: Completeness — verify nothing essential omitted; generate Keyword Coverage Report for all user-specified Tier 1/Tier 2 keywords
- Pass 6: Figure/Table Legend Review for Results; Reporting Compliance for all sections
- Pass 7: Reproducibility (Methods/Results) or Interpretation Rigor (Discussion)
- Pass 8: Blueprint Alignment (Methods/Results only, weight 1.4) — verify prose follows the approved Blueprint
  - Methods: checks procedure, tool/version, parameter, input/output, figure/algorithm placement, and subsection alignment
  - Results: checks result rationale, claim, figure/table rationale, statistic, closing takeaway, scope limit, legend status, and subsection alignment
  - If `approved_blueprint.approval_status == "skipped_by_user"`, record `blueprint_alignment: skipped_by_user` and skip checks

Results legend review verifies that generated titles, panels, sample sizes, statistical notes, encodings, abbreviations, and table notation are source-supported or explicitly marked as `[needs: ...]`.

**Pass 9 (Discussion only): Over-interpretation Check** — each interpretation must be traceable to a specific Result; hedging must be appropriate; alternative explanations must be considered; causal language for correlational findings is flagged.

3. Generate revision report with pedagogical WHY (not just what to fix, but why it matters)
4. Suggest revision diffs

**Output**: Review report + revision recommendations

**Revision Loop**: 
- If approved → advance to Phase 4
- If requires revision → return to Writer with revision notes (max 3 iterations)

### Phase 4: Pattern Learner

**Input**: Approved section + reviewer feedback + RAG search history

1. Extract section-tagged patterns from approved output
2. Compare approved patterns against existing style-guide.md
3. Update style-guide.md with new patterns (section-specific)
4. Save paper_context for downstream sections (cross-section consistency)
5. Log pattern-history/changelog with section attribution
6. Learn from feedback: what reviewer feedback was most valuable for this section type

**Output**: 
- Updated `data/style-guide.md`
- Updated `data/pattern-history/` (versioned files + changelog.md, section-tagged)
- Saved `paper_context.yaml` for next section

---

## 6. Usage Modes

### Complete Workflow

Full IMRAD writing with RAG collections available:

```bash
/academic-writer
→ Phase -3: Deep Interview Gate
→ Phase -2: Select section (e.g., Results)
→ Phase -1.5: Reference Paper Gate
→ Phase -1: Select collections (e.g., agentpaper + mypaper)
→ Phase 0: Dual-layer learning from direct references + RAG
→ Phase 1: Style merge
→ Phase 2: Interview + outline/Blueprint + draft (+ Results legends when available)
→ Phase 3: section-weighted review (8 passes + Pass 9 for Discussion)
→ Phase 4: Learn + save style guide
```

### Quick Mode

Skip RAG, use existing style-guide.md:

```bash
/academic-writer
→ Phase -3: Deep Interview Gate
→ Phase -2: Select section
→ Phase -1.5: Reference Paper Gate
→ RAG Health Check: User chooses "Proceed without RAG"
→ Phase 2: Interview + outline/Blueprint + draft (using direct references if provided + existing style-guide.md)
→ Phase 3: Review
→ Phase 4: Learn
```

### PDF Fallback Mode

User provides PDFs instead of RAG:

```bash
/academic-writer
→ Phase -3: Deep Interview Gate
→ Phase -2: Select section
→ Phase -1.5: Reference Paper Gate
→ RAG Health Check: User chooses "Provide PDFs directly"
→ Paper Preprocessor: Extract section/captions from PDFs
→ Phase 0: Learn from preprocessed PDFs
→ Phase 1: Style merge
→ Phase 2-4: Normal workflow
```

### Revision Mode

Continue revising existing draft:

```bash
/academic-writer revision
→ Load existing draft from workspace
→ Skip phases 0-2
→ Proceed to Phase 3 review
→ If revisions needed: return to Phase 2 writer
→ Phase 4: Learn from revisions
```

---

## 7. Fallback System

### When RAG Is Unavailable

If `list_collections()` fails, user sees 3 options:

1. **Check RAG Setup**: Verify Docker container running
2. **Proceed without RAG**: Use Phase -1.5 direct references if provided plus existing style-guide.md
3. **Provide PDFs Directly**: Upload reference PDFs for Paper Preprocessor or reuse references already collected in Phase -1.5

### PDF Dual-Layer Selection

Mirrors RAG collection structure:

**Structure Layer PDFs**: Papers whose section structure, subsection organization, figure/table integration, or legend/caption structure you want to emulate.

**Target Voice Layer PDFs**: Papers whose writing tone, sentence patterns, statistical reporting style, or legend/caption wording you want to emulate.

Rules:
- At least one layer should be provided
- The same PDF can be used for both layers
- Multiple PDFs can be provided per layer
- Only provided layers are processed; unprovided layers retain existing style-guide.md

### Paper Preprocessor Extraction

Paper Preprocessor detects IMRAD sections using structural heuristics:

- **Introduction**: Precedes Methods; contains "Background," "Motivation," "Introduction"
- **Methods**: Between Introduction and Results; contains "Methods," "Materials & Methods," "Approach"
- **Results**: Between Methods and Discussion; contains "Results," "Findings"; starts with data presentation
- **Discussion**: Follows Results; contains "Discussion," "Interpretation," "Implications"

Output: Extracted section text + metadata (page range, confidence, subsection markers, figure/table captions when available).

---

## 8. File Structure

```
academic-writer/
├── README.md                              # This file
├── SKILL.md                               # Orchestrator logic and configuration
├── agents/
│   ├── section-analyzer.md                # Extract structure patterns
│   ├── style-extractor.md                 # Extract voice/tone patterns
│   ├── section-writer.md                  # Tiered interview + prose/legend generation
│   ├── section-reviewer.md                # Section-weighted multi-pass review + legend checks
│   ├── pattern-learner.md                 # Learn from approved sections
│   └── paper-preprocessor.md              # PDF fallback processor
├── data/
│   ├── style-guide.md                     # Merged structure + voice patterns
│   ├── feedback-log.md                    # Reviewer feedback history (section-tagged)
│   ├── section-configs.yaml               # Writing rules + review weights per section
│   ├── paper_context.yaml                 # Cross-section consistency anchors
│   ├── samples/                           # Example sections (by type)
│   ├── approved-sections/                 # User-approved final outputs (by type)
│   └── pattern-history/                   # Versioned style guide snapshots
│       ├── style-guide-v4.0.md            # Baseline version
│       ├── style-guide-v4.1.md            # First learned update
│       └── changelog.md                   # Per-change log with section attribution
├── references/
│   ├── introduction-patterns.md            # Intro-specific reference patterns
│   ├── methods-patterns.md                 # Methods-specific reference patterns
│   ├── results-patterns.md                 # Results-specific reference patterns
│   ├── legend-patterns.md                  # Figure/table legend patterns
│   └── discussion-patterns.md              # Discussion-specific reference patterns
└── tests/
    ├── test-workflow.md                    # Test: complete workflow
    ├── test-pdf-fallback.md                # Test: PDF fallback when RAG unavailable
    ├── test-revision-loop.md               # Test: revision loop (max 3 iterations)
    └── test-cross-coherence.md             # Test: cross-section coherence check
```

---

## 9. Supported Journals and Guidelines

The system adapts to various journal guidelines through the `section_configs` block:

**Target Disciplines**:
- Bioinformatics
- Genomics
- Computational Biology
- Related computational/data science fields

**Journal Compatibility**:
- Journals with IMRAD structure (Introduction, Methods, Results, Discussion)
- Both open-access and subscription journals
- Guidelines loaded from `data/section-configs.yaml`

**Customization**:
1. Add journal-specific `section_configs` block to SKILL.md
2. Create journal-specific style-guide.md variant
3. Update reference patterns (introduction-patterns.md, etc.) with journal examples

---

## 10. Continuous Learning

### How Patterns Are Learned

1. **Approval Trigger**: When user approves a section (Phase 4), Pattern Learner activates
2. **Source Extraction**: Pull patterns from approved section (sentence structures, terminology, hedging frequency)
3. **Section Tagging**: Tag all patterns with SECTION_TYPE for section-specific learning
4. **RAG Effectiveness Scoring**: Score how useful the Structure Layer and Target Voice Layer were
5. **Style Guide Update**: Merge learned patterns back into style-guide.md (section-tagged)
6. **Pattern History**: Record all changes in `data/pattern-history/changelog.md` with approval timestamp and version bump

### Learning Across Sections

When user writes multiple sections (e.g., Results, then Discussion):

1. Results approval → Learn Results-specific patterns → Update style-guide.md
2. Discussion writing → Load paper_context from Results → Enforce coherence → Ask about cross-references
3. Discussion approval → Learn Discussion-specific patterns → Update style-guide.md
4. Next project → User can choose to start from previous style-guide.md or reset to defaults

### Style Guide Structure

```yaml
style-guide.md:
  structure_layer:
    introduction:
      subsection_hierarchy: "[patterns learned from introduction-specific RAG searches]"
      figure_integration: "..."
      flow_patterns: "..."
    methods:
      pipeline_granularity: "[patterns learned from methods-specific RAG searches]"
      subsection_organization: "..."
    results:
      finding_order: "[patterns learned from results-specific RAG searches]"
      figure_alignment: "..."
    discussion:
      interpretation_hedging: "[patterns learned from discussion-specific RAG searches]"
      limitation_framing: "..."

  target_voice_layer:
    introduction:
      sentence_patterns: "[patterns learned from introduction voice collection]"
      hedging_frequency: "..."
    methods:
      procedural_language: "[patterns learned from methods voice collection]"
      passive_ratio: "..."
    results:
      finding_verbs: "[patterns learned from results voice collection]"
      statistical_abbreviations: "..."
    discussion:
      interpretation_patterns: "[patterns learned from discussion voice collection]"
      limitation_tone: "..."

  version: "4.0"                    # data file version; incremented by Pattern Learner
  last_updated: "YYYY-MM-DD"
  learned_from_sections: "[list of approved sections that contributed to this guide]"
```

---

## 11. Quick Reference

### Invocation

```bash
/academic-writer                    # Start fresh workflow
/academic-writer revision           # Revise existing draft
```

### Keyboard Shortcuts (in conversation)

- Type section name directly: "Write Results" instead of "Option 3"
- Answer "없음" when no direct reference paper is available for the current run
- Skip interview questions: Hit Enter to accept smart default
- Defer follow-ups: "I have enough to start"
- Request specific revision: "Fix the statistical reporting"
- Invoke Lite Mode: "outline only" / "skip blueprint" / "lite mode"

### Common Answers by Section

**Introduction Q1** (research question): "We want to understand whether X can improve Y" / "There's a gap in how X is measured"

**Methods Q1** (study type): "computational" / "experimental" / "mixed"

**Results Q2** (top findings): "1) X is significantly higher in treatment group; 2) Y correlates with Z; 3) Method outperforms baseline"

**Discussion Q3** (limitations): "Small sample size" / "Limited generalizability beyond our dataset" / "Computational cost may limit adoption"

### Review Pass Weights Recap

| Section | Emphasis | De-emphasis |
|---------|----------|-------------|
| **Introduction** | Structural clarity (1.2×) | Statistical detail (0.3×) |
| **Methods** | Reproducibility (1.5×), Blueprint Alignment (1.4×) | Statistical review (0.7×) |
| **Results** | Factual accuracy (1.5×), Statistical rigor (1.3×), Blueprint Alignment (1.4×) | N/A |
| **Discussion** | Completeness (1.2×) + Over-interpretation Check | Statistical detail (0.5×) |

### Blueprint Quick Reference (Methods/Results)

| Action | When |
|--------|------|
| Blueprint generated | After tiered interview, before any prose |
| Blueprint approved by user | Required before prose begins (Step 2d) |
| Blueprint persisted | Step 2e → `paper_context.methods_blueprint` or `.results_blueprint` |
| Blueprint handed to Reviewer | As `approved_blueprint` alongside the draft |
| Results closing takeaway planned | Required for empirical/evaluation subsections in the approved Results Blueprint |
| Results legend status planned | Required for available figures/tables in the approved Results Blueprint |
| Results setup exception | Overview, dataset, or benchmark-construction subsections may use roadmap/evaluation-purpose closings |
| Pass 8 skipped | Only when `approval_status: skipped_by_user` (Lite Mode) |
| Blueprint revision required | Any time prose would add content outside the approved Blueprint |

### Results Legend Quick Reference

| Item | Rule |
|------|------|
| Output block | `## Figure Legends` after Results prose |
| Groups | Main Figures, Main Tables, Supplementary Figures, Supplementary Tables |
| Partial legends | Allowed only with inline `[needs: ...]` markers |
| Never invent | Sample sizes, p-values, tests, encodings, scale bars, cohort labels, abbreviations |
| Reference file | `references/legend-patterns.md` |

### RAG Query Cheat Sheet

If user wants to customize RAG searches, these are the default query strategies:

- **Introduction Structure**: "motivation field significance existing approaches limitations"
- **Results Structure**: "Results section findings statistical analysis Figure Table"
- **Results Legend Voice**: "figure legend caption panel table notation"
- **Discussion Voice**: "suggest indicate interpretation limitations future directions"

### Learning Cycle

1. Write section → Interview → Outline/Blueprint → Draft
2. Reviewer → Approved?
3. If yes: Pattern Learner updates style-guide.md, save paper_context for next section
4. If no: Writer revises (max 3 iterations), then Pattern Learner learns from final approved version

---

**For detailed orchestrator logic, see SKILL.md. For agent-specific implementations, see agents/*.md.**
