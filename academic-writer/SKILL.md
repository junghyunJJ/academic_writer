---
name: academic-writer
description: Write publication-quality sections (Introduction, Methods, Results, Discussion) for academic research articles in Bioinformatics, Genomics, and Computational Biology. User selects one IMRAD section per invocation. Requires reference papers (via RAG or PDF) for dual-layer style learning. Activation is explicit only - do not auto-trigger.
---

# Academic Writer

Multi-agent system for writing publication-quality IMRAD sections by learning from reference papers via RAG-based dual-layer style analysis, guided by a tiered conversational interview, and continuously improving through feedback. Replaces the single-section `academic-results-writer` with full coverage of Introduction, Methods, Results, and Discussion.

**Design philosophy**: Copilot, not pilot. The user owns the narrative; AI executes. Every question has a smart default. Every decision can be overridden. The system gathers intent through conversation, not interrogation.

## Architecture

```
┌───────────────────────────────────────────────────────────────────┐
│                     ORCHESTRATOR (SKILL.md)                       │
│                                                                   │
│  Phase -2: Section Selection                                      │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │  "Which section would you like to write?"                    │  │
│  │  1. Introduction  2. Methods  3. Results  4. Discussion     │  │
│  │  → Sets SECTION_TYPE → loads references/{section}-patterns  │  │
│  └──────────────────────────┬──────────────────────────────────┘  │
│                              │                                    │
│  RAG Health Check                                                 │
│  ┌──────────────────────────▼──────────────────────────────────┐  │
│  │  Attempt list_collections()                                  │  │
│  │  → Success: Proceed to Phase -1                              │  │
│  │  → Failure: Present 3 options to user                       │  │
│  │    ① Check RAG setup  ② Proceed without RAG  ③ Provide PDF │  │
│  └──────────────────────────┬──────────────────────────────────┘  │
│                              │                                    │
│  Phase -1: Collection Selection (when RAG is available)            │
│  ┌──────────────────────────▼──────────────────────────────────┐  │
│  │  list_collections() → User selects:                         │  │
│  │    Q1: Structure Layer collection (default: agentpaper)     │  │
│  │    Q2: Target Voice collection (default: mypaper)           │  │
│  └──────────────────────────┬──────────────────────────────────┘  │
│                              │                                    │
│  Phase 0a + 0b: Dual-Layer Learning (PARALLEL)                    │
│  ┌───────────────────┐    ┌───────────────────┐                   │
│  │   RAG Search       │    │   RAG Search       │                  │
│  │  (Structure)       │    │  (Voice)           │                  │
│  │      ↓             │    │      ↓             │                  │
│  │   Section         │    │    Style           │                  │
│  │   Analyzer        │    │   Extractor        │                  │
│  │      ↓             │    │      ↓             │                  │
│  │  Structure        │    │ Target Voice       │                  │
│  │   Layer           │    │    Layer           │                  │
│  └─────────┬─────────┘    └─────────┬─────────┘                   │
│            │                        │                             │
│            └────────────┬───────────┘                             │
│                         │                                         │
│  Phase 1: Style Merge   │                                         │
│  ┌──────────────────────▼──────────────────────┐                  │
│  │  Structure Layer + Voice Layer               │                  │
│  │  + Priority Rules → Merged Guide             │                  │
│  └──────────────────────┬──────────────────────┘                  │
│                         │                                         │
│  Phase 2: Section Writer (Tiered Conversational Interview)        │
│  ┌───────────────┐ ┌────▼──────────┐                              │
│  │  User Data    │→│   Section     │  Step 0: Input Collection    │
│  │  (per section)│ │   Writer      │  Step 1: Tiered Interview    │
│  └───────────────┘ │               │    A: Context Detection      │
│                    │               │    B: Core Interview (1×1)   │
│                    │               │    C: Adaptive Follow-ups    │
│                    │               │    D: Intent Confirmation    │
│                    │               │  Step 2: Interactive Outline  │
│                    │               │  Step 3: Prose + RAG         │
│                    └───────┬───────┘                              │
│                            │                                      │
│  Phase 3: Section Reviewer │                                      │
│  ┌───────────────┐ ┌──────▼───────┐                               │
│  │  Source Data   │→│   Section    │  7-pass review               │
│  └───────────────┘ │   Reviewer   │  + pedagogical WHY           │
│                    └───────┬───────┘  + section-weighted passes   │
│                            │                                      │
│                   ┌────────┴────────┐                              │
│                   │ Approved?       │                              │
│                   ├─ No → Writer    │                              │
│                   ├─ Yes ↓          │                              │
│  Phase 4: Learn   │                │                              │
│  ┌───────────────┐│                │                              │
│  │   Pattern     │◀┘               │                              │
│  │   Learner     │ (section-tagged)│                              │
│  └───────────────┘                                                │
│                                                                   │
│  Fallback: RAG failure → Paper Preprocessor → User input          │
└───────────────────────────────────────────────────────────────────┘
```

## Agents

| Agent | Role | Input | Output |
|-------|------|-------|--------|
| **Section Analyzer** | Extract structure patterns for SECTION_TYPE | RAG search results + reference patterns | Structure Layer for style guide |
| **Style Extractor** | Extract voice/tone from Target Voice collection | RAG search results | Target Voice Layer for style guide |
| **Section Writer** | Write section via tiered interview process | User data + merged style guide + RAG few-shot | Interview -> Outline -> Draft section |
| **Section Reviewer** | Section-weighted multi-pass review | Draft + source data | Review report + revision diffs + WHY |
| **Pattern Learner** | Learn from feedback, section-tagged | Approved sections + feedback | Style guide updates (both layers) |
| **Paper Preprocessor** | *Fallback*: Extract section from PDFs | Reference PDFs + SECTION_TYPE | Extracted section text + metadata |

Agent definitions: `agents/section-analyzer.md`, `agents/style-extractor.md`, `agents/section-writer.md`, `agents/section-reviewer.md`, `agents/pattern-learner.md`, `agents/paper-preprocessor.md`

---

## Section Configuration

The `SECTION_TYPE` variable propagates to every downstream phase and agent, controlling RAG queries, interview questions, review weights, writing rules, and reference pattern loading.

```yaml
section_configs:
  introduction:
    reference_file: "references/introduction-patterns.md"
    writing_rules:
      tense: "present tense for established facts, past for specific studies"
      voice: "active preferred, 'we' for contributions"
      interpretation: "allowed — contextual framing only"
      citations: "heavy — establish field context"
      key_constraint: "must have clear gap statement and contribution statement"
    review_weight_overrides:
      factual_accuracy: 0.8
      statistical_review: 0.3
      structural_review: 1.2
      style_clarity: 1.0
      completeness: 1.0
      reporting_compliance: 0.5
      reproducibility: 0.3
    word_count_typical: "800-1500"

  methods:
    reference_file: "references/methods-patterns.md"
    writing_rules:
      tense: "past tense dominant"
      voice: "passive preferred for procedures, active for decisions"
      interpretation: "not allowed — describe, don't interpret"
      citations: "moderate — cite tools, databases, algorithms"
      key_constraint: "must be reproducible; sufficient detail for replication"
    review_weight_overrides:
      factual_accuracy: 1.0
      statistical_review: 0.7
      structural_review: 0.8
      style_clarity: 1.0
      completeness: 1.2
      reporting_compliance: 1.0
      reproducibility: 1.5
    word_count_typical: "1000-3000"

  results:
    reference_file: "references/results-patterns.md"
    writing_rules:
      tense: "past tense for analyses, present for figure descriptions"
      voice: "active for findings, passive for methods-brief"
      interpretation: "not allowed — save for Discussion"
      citations: "minimal — only for method references"
      key_constraint: "every claim backed by data; no over-interpretation"
    review_weight_overrides:
      factual_accuracy: 1.5
      statistical_review: 1.3
      structural_review: 1.0
      style_clarity: 1.0
      completeness: 1.2
      reporting_compliance: 1.0
      reproducibility: 1.0
    word_count_typical: "1500-4000"

  discussion:
    reference_file: "references/discussion-patterns.md"
    writing_rules:
      tense: "present tense for interpretations, past for recapping results"
      voice: "active preferred, hedging appropriate"
      interpretation: "required — this is where interpretation belongs"
      citations: "heavy — compare with literature"
      key_constraint: "must acknowledge limitations; no new data introduced"
    review_weight_overrides:
      factual_accuracy: 1.0
      statistical_review: 0.5
      structural_review: 1.0
      style_clarity: 1.0
      completeness: 1.2
      reporting_compliance: 0.5
      reproducibility: 0.3
    review_additions:
      - "over-interpretation check"
      - "limitation completeness"
      - "no new data introduced"
    word_count_typical: "1000-2500"
```

### Writing Rules Summary

| Rule | Introduction | Methods | Results | Discussion |
|------|-------------|---------|---------|------------|
| Tense | Present (field), Past (studies) | Past | Past (analyses), Present (figures) | Present (interpretations), Past (recaps) |
| Voice | Active preferred | Passive preferred | Active for findings | Active preferred |
| Interpretation | Contextual framing only | Not allowed | Not allowed | Required |
| Citations | Heavy | Moderate (tools) | Minimal | Heavy |
| Key constraint | Clear gap + contribution | Reproducible detail | Data-backed claims | Limitations acknowledged |

### Review Weight Summary

| Pass | Intro | Methods | Results | Discussion |
|------|-------|---------|---------|------------|
| 1. Factual Accuracy | 0.8 | 1.0 | **1.5** | 1.0 |
| 2. Statistical Review | 0.3 | 0.7 | **1.3** | 0.5 |
| 3. Structural Review | **1.2** | 0.8 | 1.0 | 1.0 |
| 4. Style & Clarity | 1.0 | 1.0 | 1.0 | 1.0 |
| 5. Completeness | 1.0 | 1.2 | 1.2 | 1.2 |
| 6. Reporting Compliance | 0.5 | 1.0 | 1.0 | 0.5 |
| 7. Reproducibility | 0.3 | **1.5** | 1.0 | 0.3 |

Discussion adds: Over-interpretation Check (extra pass).

### Hourglass Model Awareness

Introduction and Discussion are mirror opposites:
- **Introduction**: Funnel (broad context -> specific question)
- **Discussion**: Inverted Funnel (specific findings -> broad significance)

When both sections exist, the section-writer enforces this symmetry:
- Discussion opening references the Introduction's gap statement
- Discussion closing returns to the broad context established in the Introduction
- The reviewer performs a cross-reference check between the two sections

---

## RAG Configuration

### Collections

The system uses two RAG collections via the `langconnect-rag` MCP server:

```yaml
rag_collections:
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
| Results | "Results section findings statistical analysis" | "we found demonstrated showed revealed indicated" | "Figure Table panel comparison significantly" | *field-specific* |
| Discussion | "suggest demonstrate indicate consistent with" | "consistent with previous findings prior work" | "limitation future direction caveat" | *field-specific* |

**Real-time few-shot queries** (for Section Writer, during prose):
- Subsection topic keywords -> Target Voice collection (primary)
- Structure reference -> Structure collection (secondary, when needed)

---

## Workflow

### Phase -2: Section Selection (NEW)

**Purpose**: Let the user choose which IMRAD section to write. Sets SECTION_TYPE for all downstream phases.

Orchestrator asks: "Which section would you like to write?" (1. Introduction / 2. Methods / 3. Results / 4. Discussion)

1. User selects one (or states section name directly)
2. Set `SECTION_TYPE` = selected section
3. Load `references/{SECTION_TYPE}-patterns.md`
4. Activate the corresponding `section_configs` block
5. All downstream agents receive SECTION_TYPE as a parameter

### RAG Health Check (Before Phase -1)

**Purpose**: Verify that the langconnect-rag MCP server is available before proceeding to collection selection. If unavailable, inform the user and let them decide whether to configure it or proceed without RAG.

```yaml
rag_health_check:
  step_1: "Attempt to call mcp__langconnect-rag__list_collections"

  if_success:
    action: "Proceed to Phase -1 (Collection Selection) normally"
    message: "RAG server connection confirmed. Proceeding to collection selection."

  if_failure:
    action: "Inform user and present options"
    message: |
      ⚠️ Cannot connect to the langconnect-rag MCP server.
      Without RAG, reference paper-based style learning (Phase 0) and
      real-time few-shot references (Step 3) will not be available.
      Writing will proceed using only the existing style-guide.md patterns.

    options:
      1:
        label: "Check RAG setup"
        description: "Check Docker container status and retry the connection"
        action:
          - "Guide user: 'Please verify that the langconnect-rag Docker container is running'"
          - "Suggest: 'You can check with: ! docker ps | grep langconnect'"
          - "After user confirms running → retry list_collections"
          - "If still fails → return to options"

      2:
        label: "Proceed without RAG"
        description: "Write using only the existing style-guide.md patterns"
        action:
          - "Skip Phase -1, Phase 0a, Phase 0b, Phase 1"
          - "Proceed directly to Phase 2 (Writer) using existing style-guide.md"
          - "Real-time RAG few-shot in Step 3 also disabled"
          - "Set rag_available: false for downstream agents"

      3:
        label: "Provide PDFs directly"
        description: "Provide PDF files directly instead of RAG to process via Paper Preprocessor"
        action:
          - "Ask user to provide PDFs with dual-layer distinction (see below)"
          - "Activate Paper Preprocessor for each PDF"
          - "Preprocessor extracts section content → passes to appropriate agent"
          - "Proceed with Phase 0 using preprocessed content instead of RAG"

        # Dual-Layer PDF Selection (mirrors RAG collection selection)
        pdf_dual_layer:
          message: |
            PDFs follow the same two-layer distinction as RAG collections:

            1️⃣ Structure Layer (for structure/pattern learning):
               → Papers whose section structure, subsection organization, or figure integration you want to emulate
               Example: "I want to follow the Results structure of this paper"

            2️⃣ Target Voice Layer (for writing style/tone learning):
               → Papers whose writing tone, sentence patterns, or statistical reporting style you want to emulate
               Example: "Write in the English tone of my previous paper"

            Providing just one layer is fine. Only the provided layer will be learned.

          input_format:
            structure_pdfs:
              description: "Paper PDF(s) to learn structure/patterns from (optional)"
              required: false
              action: "Paper Preprocessor → Section Analyzer → Structure Layer update"
            voice_pdfs:
              description: "Paper PDF(s) to learn writing style/tone from (optional)"
              required: false
              action: "Paper Preprocessor → Style Extractor → Target Voice Layer update"

          rules:
            - "At least one layer must be provided (if neither is provided, equivalent to option ②)"
            - "The same PDF can be used for both layers simultaneously"
            - "Multiple PDFs can be provided per layer"
            - "Only provided layers are processed in Phase 0 (unprovided layers retain existing style-guide.md)"
```

### Phase -1: Collection Selection

**Purpose**: Let the user choose which RAG collections to use for structure learning and voice extraction. (Skipped if user chose "Proceed without RAG" in Health Check.)

1. Call `mcp__langconnect-rag__list_collections` to get available collections
2. Present collection list with names, document counts, and descriptions
3. Ask: "Which collection to use for the Structure Layer (structure/pattern learning)?" (default: agentpaper)
4. Ask: "Which collection to use for the Target Voice Layer (style/tone learning)?" (default: mypaper)
5. Store selected collection IDs for downstream phases
6. Same collection allowed for both layers. If user says "skip" or "default", use agentpaper + mypaper

### Phase 0a: Structure Pattern Learning (Parallel with 0b)

**Input**: Structure Layer collection + SECTION_TYPE

1. Execute 4 section-specific Structure Layer queries (see RAG Query Strategies) against selected collection
2. Pass search results to Section Analyzer agent with SECTION_TYPE
3. Analyzer extracts section-appropriate structural patterns, groups by source document, synthesizes cross-document patterns
4. Optional: BioMCP enrichment for biological context
5. Update `data/style-guide.md` Structure Layer section
6. Update `references/{SECTION_TYPE}-patterns.md` if new templates found

### Phase 0b: Target Voice Learning (Parallel with 0a)

**Input**: Target Voice collection + SECTION_TYPE

1. Execute 3 section-specific + 1 field-specific voice queries against selected collection
2. Pass search results to Style Extractor agent with SECTION_TYPE
3. Extractor analyzes sentence patterns, transitions, statistical reporting style, logic flow, tone profile
4. Update `data/style-guide.md` Target Voice Layer section

### Phase 1: Style Merge

**Input**: Structure Layer + Target Voice Layer from Phase 0a/0b

**Priority Rules** (conflict resolution):
- Structure -> Structure Layer
- Voice/tone -> Target Voice Layer
- Statistics -> Target Voice Layer
- Transitions -> Target Voice Layer
- Figure refs -> Structure Layer (with Voice overlay)

The merged guide is the complete `data/style-guide.md` -- no separate file needed. The writer reads the complete style guide and applies priority rules during writing.

### Phase 2: Content Creation (Section Writer)

**Input**: User's research materials + merged style guide + real-time RAG + SECTION_TYPE

#### Step 0: Input Collection (section-specific)

| Section | Required Inputs | Optional Inputs |
|---------|----------------|-----------------|
| Introduction | Research topic, research gap, contribution, key references | Background notes (.md), related work notes, target journal |
| Methods | Pipeline description, tools/versions, datasets, parameters | Pseudocode, code repo URL, pipeline figure, reporting guideline |
| Results | CSV data + descriptions, figures + findings + stats, experimental context | Tables, results context (.md), target journal, reporting guideline |
| Discussion | Key findings summary, Results section text, limitations | Related work comparison, future directions, broader impact, target journal |

Full input specification with Korean prompts: see `agents/section-writer.md` Step 0.

#### Step 1: Tiered Conversational Interview (MAJOR UPGRADE)

**Architecture**: 4-phase conversational flow. One question at a time with acknowledgment. Smart defaults for every question. Conversational tone throughout.

**Research backing**: 86% higher completion vs front-loaded questionnaires (CHI 2024); ~50% of effective questions are dynamic follow-ups (LLMREI arXiv 2025); 97.6% satisfaction with adaptive follow-ups (Anthropic Interviewer).

```
Phase A: Context Detection (0-1 questions, mostly automated)
  - Auto-scan workspace for existing sections (populate paper_context)
  - Detect target journal, research field, user expertise level
  - Load paper_context from prior section interviews
  - Active Cross-Section Coherence Check (see below)

Phase B: Core Interview (5 questions, section-specific, one-at-a-time)
  - Each answer acknowledged before next question
    ("Got it — so the gap is X...")
  - Smart defaults for every question (user can skip)
  - Conversational tone: "Tell me about..." not "Specify the..."
  - Purpose transparency: brief note on why each question matters

Phase C: Adaptive Follow-ups (0-3 questions, conditional)
  - Context-deepening: probe vague Phase B answers
  - Context-enhancing: introduce considerations user may not have thought of
  - Can be deferred: "I have enough to start. I'll ask more during outline review."

Phase D: Intent Confirmation (1 interaction)
  - Structured summary of understood intent
  - User confirms or corrects
  - Update paper_context for downstream sections
```

##### Phase A: Context Detection with Active Cross-Section Coherence Check

When existing sections are detected, Phase A performs an active coherence check -- not just passive context reuse, but explicit user-driven cross-referencing:

```
Step 1: Auto-scan workspace for existing sections
Step 2: If existing sections found → Ask user:
  "You have a previously written [Results] section.
   Would you like to reference it while writing [Methods]?"
   → Yes: Read section, extract coherence points
   → No: Write independently

Step 3: If Yes → Auto-extract cross-reference points
        (see Cross-Reference Extraction Table)
```

**Cross-Reference Extraction Table** (Writing Now x Existing Section -> Auto-Extracted Elements):

| Writing Now | Existing Section | Auto-Extracted Elements |
|-------------|-----------------|------------------------|
| Methods | Results | Methods/tools mentioned in Results, statistical tests used, pipeline steps implied by figure order |
| Discussion | Results | Key findings to recap, statistics to reference, unexpected results flagged |
| Discussion | Introduction | Gap statement to address, hypotheses to confirm/refute, broad context to mirror |
| Results | Methods | Analysis pipeline order, tool names for consistency, parameter values |
| Introduction | Results | Key findings to preview (optional), scope of contribution |
| Methods | Introduction | Study design previewed in Intro, approach mentioned |

**How extracted elements are used**:
1. Pre-populate interview questions (e.g., Methods Q4 "tools used" auto-filled from Results mentions)
2. Generate coherence checklist for the reviewer (e.g., "Results mentions 'Leiden clustering' -- verify Methods describes it")
3. Inform outline structure (e.g., Methods subsection order matches Results presentation order)
4. Writer uses extracted elements as consistency anchors during prose generation

**Integration with Reviewer (Phase 3)**: When cross-section reference is active, the reviewer adds a Cross-Section Consistency Pass:
- Terminology consistency (same terms in both sections)
- Method-Result alignment (every method mentioned in Results is described in Methods)
- Intro-Discussion mirror check (gap addressed, hypothesis answered)
- Figure reference consistency (figure numbers match across sections)

##### Phase B: Section-Specific Core Questions

**Introduction** (5 core + follow-ups):

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What is the core motivation and research question? | gap-elicitation | Follow-up: "knowledge gap? methodology gap? application gap?" |
| Q2 | What is the most important limitation (gap) of prior work? | contrastive-probe | "What was tried before, why insufficient?" |
| Q3 | Summarize the paper's main contribution in one sentence | intent-crystallization | Forces core message articulation |
| Q4 | How broad should the background be? (minimal <-> extensive) | scope-calibration | Auto-adapt to target journal audience |
| Q5 | Key references or papers to position against? | reference-anchoring | Can trigger RAG search |

**Methods** (5 core + follow-ups):

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What type of study is this? (computational, experimental, etc.) | structural-classifier | Determines section template |
| Q2 | Data/samples: what, how many, from where? | core-content | Follow-up: inclusion/exclusion criteria |
| Q3 | Describe the analysis pipeline step by step (raw data -> final result) | workflow-elicitation | Encourage numbered steps |
| Q4 | What software/tools and versions were used? | reproducibility | Follow-up: custom scripts, parameters |
| Q5 | Preferred subsection order for Methods? | structure-preference | Default: pipeline order |

**Results** (5 core, enhanced):

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What is the central narrative of this Results? (What story does the data tell?) | narrative-framing | ABT hint: "And..., But..., Therefore..." |
| Q2 | Top 3-5 most important findings in order of importance? | priority-setting | Added ranking + number constraint |
| Q3 | For each Figure/Table: what does it show, and what is the key takeaway? | visual-inventory | Ensures purposeful figure integration |
| Q4 | Preferred subsection order? | structure-preference | Default: progressive complexity |
| Q5 | What is the most important comparison? (vs baseline, vs existing, across conditions) | comparison-framing | Anchors comparative analysis |

**Discussion** (5 core + follow-ups):

| # | Question | Type | Smart Default |
|---|----------|------|---------------|
| Q1 | What is the most important conclusion from Results? (one sentence) | thesis-statement | Anchors Discussion opening |
| Q2 | Key points to discuss in comparison with existing literature? | literature-positioning | Follow-up: "2-3 key papers to compare?" |
| Q3 | What are the main limitations of this study? | limitation-awareness | Follow-up: methodological? data? generalizability? |
| Q4 | Any unexpected results or findings that need explanation? | anomaly-exploration | Shows analytical maturity |
| Q5 | Topics to explicitly avoid in the Discussion? (scope boundary) | scope-guard | Prevents over-interpretation |

##### paper_context (Cross-Section Accumulation)

```yaml
paper_context:
  research_gap: "[from Introduction interview]"
  hypotheses: "[from Introduction interview]"
  study_design: "[from Methods interview]"
  key_findings: "[from Results interview]"
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

Each section interview checks paper_context first, skips already-answered questions, and adds new information for downstream sections.

#### Step 2: Interactive Outline (each step requires USER APPROVAL)

| Step | Introduction | Methods | Results | Discussion |
|------|-------------|---------|---------|------------|
| 2a: Overall structure | Funnel: broad -> problem -> gap -> contribution | Procedural: subsection order matching pipeline | Narrative arc: subsection count, titles | Interpretive: summary -> comparison -> limitations -> future |
| 2b: Per-unit details | Per-paragraph key points + citations | Per-subsection tools, params, details | Per-subsection findings + statistics | Per-subsection interpretation + citations |
| 2c: Figure placement | Conceptual overview figure (if any) | Pipeline/algorithm/pseudocode figure | Figure/Table placement per subsection | Back-references to specific Results |
| 2d: Connection plan | Transition leading into Methods/Results | Cross-reference to Results | Transition and connection plan | Limitation <-> future direction pairing |

#### Step 3: Prose + RAG Few-Shot

Per subsection: extract topic keywords -> RAG search Target Voice collection -> use as style exemplars -> write prose matching merged style guide.

**Section-specific paragraph flow**:

| Section | Paragraph Flow | Key Writing Rules |
|---------|---------------|-------------------|
| Introduction | Broad context -> narrow to problem -> existing approaches -> gap statement -> contribution -> roadmap | Funnel structure; citation placeholders [Author, Year]; explicit gap; "we introduce/present/propose"; no over-promising |
| Methods | Overview -> data description -> step-by-step procedure -> tools/versions -> evaluation | Past tense; passive preferred; every parameter stated; software versions explicit; no results or interpretation |
| Results | Opening goal -> method-brief -> primary finding + stats -> supporting details -> figure reference -> transition | No interpretation (save for Discussion); statistics inline; figures described not just referenced |
| Discussion | Recap finding -> interpretation -> literature comparison -> implications -> limitations -> future | Interpretation required; compare with literature [citations]; no new data; appropriate hedging; end with broader impact |

**Integration pass** (after prose, section-specific checks):
- **Introduction**: citation placeholder completeness, gap statement presence, contribution clarity
- **Methods**: reproducibility detail check, parameter completeness, version numbers
- **Results**: terminology consistency, figure refs, statistics, flow, word count, voice
- **Discussion**: no-new-data check, limitation presence, over-interpretation scan

Full prose protocols with paragraph templates: see `agents/section-writer.md` Step 3.

### Phase 3: Quality Review (Section Reviewer)

**Input**: Draft section + source data + SECTION_TYPE + section_configs

**7-Pass Review** (weighted by SECTION_TYPE via `review_weight_overrides`):

| Pass | Focus | Weights vary by section |
|------|-------|------------------------|
| 1. Factual Accuracy | Claims match source data | Highest for Results (1.5) |
| 2. Statistical Review | Complete, correct reporting | Highest for Results (1.3) |
| 3. Structural Review | Section-appropriate organization | Highest for Introduction (1.2) |
| 4. Style & Clarity | Voice matches style guide | Uniform (1.0) |
| 5. Completeness | All required elements present | Higher for Methods/Results/Discussion (1.2) |
| 6. Reporting Compliance | STROBE/CONSORT/PRISMA/etc. | Higher for Methods/Results (1.0) |
| 7. Reproducibility | Sufficient detail for section type | Highest for Methods (1.5) |

Discussion adds: **Over-interpretation Check** (each interpretation traceable to specific Result, appropriate hedging, alternative explanations considered).

**Pedagogical WHY**: Every revision includes `teaching_note` explaining the writing principle, not just what to change.

```yaml
revision_diff_enhanced:
  - location: "subsection [X], paragraph [N]"
    original: "[exact text]"
    suggested: "[improved text]"
    reason: "[why this improves the draft]"
    teaching_note: "[what writing principle this reflects]"
    pass: "[which review pass]"
```

**Revision loop**: If assessment is "major_revisions" or "minor_revisions", the draft returns to the Writer with specific revision_diff instructions. Maximum 3 revision cycles before presenting to user for decision.

### Phase 4: Continuous Learning (Pattern Learner)

**Input**: Approved sections + reviewer feedback + style accuracy data, all tagged with SECTION_TYPE.

**Process**: Extract success patterns, analyze corrections, track reporting gaps, track RAG effectiveness, track Voice Layer accuracy, update style guide, version control. All evidence is section-tagged for independent per-section improvement.

**Outputs**: `data/style-guide.md` (updated, section-tagged) + `data/pattern-history/` (versioned)

**Learning triggers**:
- User approves final section
- Significant reviewer corrections (>3)
- Periodic review (every 10 outputs)
- Style Extractor voice accuracy below threshold

---

## Fallback Chain

When RAG is unavailable or returns insufficient results:

**Primary** -> RAG Search (3+ relevant chunks) -> Continue pipeline
**Fallback 1** -> Paper Preprocessor (PDF direct processing, section-aware) -> Section Analyzer
**Fallback 2** -> User Input (paste text, provide page ranges) -> Continue

**Fallback triggers**: RAG MCP server unavailable, collection empty/unconfigured, fewer than 3 relevant chunks returned, or user explicitly provides PDFs.

When fallback activates:
1. Inform user: "RAG search did not return sufficient results. Switching to direct PDF processing."
2. Activate Paper Preprocessor with SECTION_TYPE for section-aware extraction
3. Pass preprocessed content to Section Analyzer (same downstream pipeline)
4. If PDF also unavailable, ask user for direct text input

Section boundary patterns for the Paper Preprocessor are defined in `agents/paper-preprocessor.md`.

---

## Data Directory Structure

```
data/
├── style-guide.md          # Living style guide — Dual-Layer architecture
│                           #   Structure Layer (per-section subsections)
│                           #   Target Voice Layer
│                           #   Priority Rules
├── feedback-log.md         # Reviewer feedback history (section-tagged)
├── samples/                # Example sections (organized by type)
├── approved-sections/      # User-approved final outputs (organized by type)
└── pattern-history/        # Style guide versions
    ├── style-guide-v1.md
    ├── style-guide-v2.md
    └── changelog.md
```

---

## Usage

### Complete Workflow (Full Pipeline)

```
/academic-writer

Phase -2: Select section type (Introduction / Methods / Results / Discussion)
Phase -1: Select RAG collections
          → Choose Structure Layer collection (default: agentpaper)
          → Choose Target Voice collection (default: mypaper)
Phase 0:  Dual-layer learning (parallel)
          → RAG search + Section Analyzer → Structure Layer
          → RAG search + Style Extractor → Target Voice Layer
Phase 1:  Style merge (Priority Rules applied)
Phase 2:  Provide your materials (section-specific inputs)
          → Tiered Conversational Interview (Phase A-D)
          → Interactive Outline (user approves each step)
          → Prose + real-time RAG few-shot references
Phase 3:  Review generated draft
          → Section Reviewer runs 7-pass review (section-weighted)
          → Pedagogical revision diffs with WHY
          → Auto-revision if issues found (max 3 cycles)
Phase 4:  Approve or request revisions
Phase 5:  (Automatic) Patterns learned for future use (section-tagged)
```

### Quick Mode (Skip Phase 0-1)

When you already have a style guide or want to write without reference paper analysis:

```
Phase -2: Section selection (always required)
Phase -1: Collection selection (preserved for real-time RAG in Phase 2)
Phase 2:  Writer (uses existing style-guide.md + real-time RAG few-shot)
Phase 3:  Reviewer (section-weighted 7-pass review)
Phase 4:  Pattern Learner (section-tagged)
```

**When to use**: Style guide already populated from previous analysis, fast turnaround needed, or reference papers unavailable but user knows the target style.

**To invoke**: Provide your data and materials directly without reference papers. The system detects the absence of reference input and proceeds to Phase 2. Collection Selection is still performed for real-time RAG few-shot during writing.

### Style Learning Only

```
Phase -2: Section selection (determines which patterns to learn)
Phase -1: Collection selection
Phase 0a: RAG search + Section Analyzer → Structure Layer
Phase 0b: RAG search + Style Extractor → Target Voice Layer
Phase 1:  Style Merge
→ Style guide updated for selected section type
```

### Writing Only (Using Existing Patterns)

```
Provide your research materials (section-specific):
• Introduction: research topic, gap, contribution, key references
• Methods: pipeline, tools/versions, datasets, parameters
• Results: CSV data, figures, experimental context
• Discussion: key findings summary, Results text, limitations

Phase -2: Section selection
Phase -1: Collection selection (for real-time RAG)
Phase 2:  Tiered Interview → Interactive Outline → Prose + RAG
Phase 3:  Section Reviewer checks quality (section-weighted 7 passes)
```

### Journal Target Setting

Specify a target journal to enforce formatting constraints:

```yaml
journal_target: "Nature Methods"  # or Bioinformatics, Genome Biology, etc.
```

Effects:
- Word limit awareness in Writer and integration pass
- Figure limit enforcement
- Journal-specific style conventions applied
- Reviewer checks against journal requirements

---

## Output Format

Word-ready markdown:
- `##` for main section heading
- `###` for subsections
- Figure references: `(Figure X)`, `(Table Y)`
- Statistics inline: `(p < 0.05)`, `(n = 100)`
- Citation placeholders: `[Author, Year]`
- Professional academic tone matching Target Voice Layer
- Section-appropriate tense, voice, and hedging per `section_configs`

---

## Quality Checklist

Before final approval:

**All Sections**:
- [ ] Section type selected (Phase -2)
- [ ] Collection selection completed (Phase -1)
- [ ] Structure Layer populated from reference papers
- [ ] Target Voice Layer populated from target voice papers
- [ ] Tiered interview completed (Phases A-D)
- [ ] Outline approved by user (Steps 2a-2d)
- [ ] Structure matches learned patterns
- [ ] Voice matches Target Voice Layer profile
- [ ] Consistent terminology throughout
- [ ] Appropriate voice and tense per section_configs
- [ ] Reporting guideline items addressed (if applicable)
- [ ] Word count within journal limits (if specified)
- [ ] Revision diff items resolved

**Introduction-specific**:
- [ ] Funnel structure (broad -> specific) maintained
- [ ] Gap statement explicit and positioned correctly
- [ ] Contribution statement present and matched to actual Results
- [ ] All existing work claims have citation placeholders
- [ ] Background scope appropriate for target audience

**Methods-specific**:
- [ ] All tools and software versions stated
- [ ] All parameters explicitly specified (not "default")
- [ ] Sufficient detail for replication
- [ ] No results or interpretation present
- [ ] Data sources and access described
- [ ] Code/data availability mentioned

**Results-specific**:
- [ ] All figures/tables referenced and described
- [ ] Statistics complete (values, tests, p-values, n)
- [ ] No interpretation beyond data
- [ ] Transitions connect subsections
- [ ] Narrative arc maintains progressive complexity

**Discussion-specific**:
- [ ] Key findings summarized (no new data)
- [ ] Interpretations traceable to specific Results
- [ ] Literature comparison with citations
- [ ] Limitations explicitly acknowledged
- [ ] Future directions mentioned
- [ ] Appropriate hedging language used
- [ ] Hourglass closure (returns to broad context from Introduction)

---

## Continuous Improvement

The system improves over time:

1. **More reference papers in RAG** -> Better pattern diversity (per section)
2. **More approved outputs** -> Refined style guide (both layers, section-tagged)
3. **Reviewer feedback** -> Eliminated common errors (tracked per section)
4. **Pattern versioning** -> Trackable improvement
5. **Reporting compliance tracking** -> Fewer guideline gaps over time
6. **RAG query refinement** -> Better retrieval quality
7. **Voice accuracy tracking** -> More faithful style emulation
8. **Cross-section coherence** -> Better consistency across IMRAD sections

Check improvement metrics in `data/feedback-log.md`.

---

## Files

- `SKILL.md` -- This orchestrator
- `agents/section-analyzer.md` -- Structure pattern extraction agent (parameterized by SECTION_TYPE)
- `agents/style-extractor.md` -- Target Voice extraction agent (section-aware queries)
- `agents/section-writer.md` -- Multi-stage content creation agent (Tiered Interview -> Outline -> Prose + RAG)
- `agents/section-reviewer.md` -- Section-weighted multi-pass review agent (7 passes + pedagogical WHY)
- `agents/pattern-learner.md` -- Continuous learning agent (section-tagged, both layers)
- `agents/paper-preprocessor.md` -- Fallback PDF preprocessing agent (section-aware extraction)
- `references/introduction-patterns.md` -- Introduction structure patterns (funnel, gap, contribution)
- `references/methods-patterns.md` -- Methods structure patterns (procedural, reproducibility)
- `references/results-patterns.md` -- Results structure patterns (evidence-based, figure integration)
- `references/discussion-patterns.md` -- Discussion structure patterns (interpretation, limitations)
- `data/style-guide.md` -- Living style guide (Dual-Layer: Structure + Target Voice + Priority Rules)
- `data/feedback-log.md` -- Feedback history (section-tagged)
