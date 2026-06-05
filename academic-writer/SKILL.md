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
│  Phase -3: Deep Interview Gate                                    │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │  One question at a time: goal, scope, constraints, done      │  │
│  │  → Emits academic_writer_brief for all downstream phases     │  │
│  └──────────────────────────┬──────────────────────────────────┘  │
│                              │                                    │
│  Phase -2: Section Selection                                      │
│  ┌─────────────────────────────────────────────────────────────┐  │
│  │  "Which section would you like to write?"                    │  │
│  │  1. Introduction  2. Methods  3. Results  4. Discussion     │  │
│  │  → Sets SECTION_TYPE → loads references/{section}-patterns  │  │
│  └──────────────────────────┬──────────────────────────────────┘  │
│                              │                                    │
│  Phase -1.5: Reference Paper Gate (mandatory)                    │
│  ┌──────────────────────────▼──────────────────────────────────┐  │
│  │  Ask for structure reference papers, then always ask         │  │
│  │  separate voice/tone reference question                      │  │
│  │  → Emits run_reference_layers                                │  │
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
│                    │               │  Step 2: Outline / Blueprint Gate│
│                    │               │  Step 3: Prose + RAG         │
│                    └───────┬───────┘                              │
│                            │                                      │
│  Phase 3: Section Reviewer │                                      │
│  ┌───────────────┐ ┌──────▼───────┐                               │
│  │  Source Data   │→│   Section    │  section-weighted review     │
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
| **Section Writer** | Write section via tiered interview process | User data + merged style guide + RAG few-shot | Interview -> Outline -> Draft section; Markdown figure embeds/captions for all sections; full Results figure/table legends when available; hybrid supplementary materials Markdown plus linked CSV/TSV artifacts |
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
    legend_reference_file: "references/legend-patterns.md"
    writing_rules:
      tense: "past tense for analyses, present for figure descriptions"
      voice: "active for findings, passive for methods-brief"
      interpretation: "not allowed — save for Discussion"
      citations: "minimal — only for method references"
      key_constraint: "every claim backed by data; each subsection states why the result is needed, why the figure/table is the right evidence, closes with a data-backed takeaway, generates figure/table legends for available displays, and uses hybrid supplementary materials handling for large supplementary tables/data; no over-interpretation"
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
| Results | "Results section findings statistical analysis" | "we found demonstrated showed revealed indicated" | "Figure Table panel legend caption comparison significantly" | *field-specific* |
| Discussion | "suggest demonstrate indicate consistent with" | "consistent with previous findings prior work" | "limitation future direction caveat" | *field-specific* |

**Real-time few-shot queries** (for Section Writer, during prose):
- Subsection topic keywords -> Target Voice collection (primary)
- Structure reference -> Structure collection (secondary, when needed)
- Results legend writing -> Target Voice query `"figure legend caption panel table notation"` plus `references/legend-patterns.md`

---

## Workflow

### Phase -3: Deep Interview Gate (Built-in)

**Purpose**: Clarify the writing task before section selection and prevent premature assumptions. This embeds the `$deep-interview` questioning style inside `$academic-writer`; it does not require activating the separate `$deep-interview` runtime.

Run this gate at the start of every `$academic-writer` invocation. Ask one question at a time, using this exact shape:

```md
현재 이해: {요청을 한 문장으로 요약}
막힌 결정: {가장 중요한 불확실성}
추천 답안: {있으면 제시}
질문: {한 가지 질문}
```

Question axes, in priority order:
1. **Goal**: what output the user wants from this run.
2. **Scope / exclusions**: which section(s), figures, tables, references, datasets, or tasks are in or out.
3. **Constraints**: target journal, style source, RAG/PDF availability, language, length, deadline, no-assumption boundaries.
4. **Completion criteria**: what counts as acceptable output.
5. **Existing context and impact**: prior sections, paper context, downstream consistency needs.

Rules:
- Ask only the single highest-impact unresolved question per round.
- Do not ask for information that can be discovered from local files, provided materials, `paper_context`, or RAG/PDF metadata.
- If the user's opening request already resolves the axes, emit the brief and proceed without adding unnecessary questions.
- Stop the gate when the core request is actionable; leave non-blocking uncertainties in `open_questions`.

Gate output:

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
  handoff: "Proceed to Phase -2 Section Selection"
```

### Phase -2: Section Selection (NEW)

**Purpose**: Let the user choose which IMRAD section to write. Sets SECTION_TYPE for all downstream phases.

Orchestrator asks: "Which section would you like to write?" (1. Introduction / 2. Methods / 3. Results / 4. Discussion). If `academic_writer_brief.likely_section_type` is already explicit, confirm it briefly or proceed directly when the user has clearly specified it.

1. User selects one (or states section name directly, including via Phase -3)
2. Set `SECTION_TYPE` = selected section
3. Load `references/{SECTION_TYPE}-patterns.md`
4. Activate the corresponding `section_configs` block
5. Pass both `SECTION_TYPE` and `academic_writer_brief` to downstream agents

### Phase -1.5: Reference Paper Gate (MANDATORY)

**Purpose**: Always ask whether the user has reference paper(s) they want to use for this run, then separate structure references from voice/tone references. This gate complements RAG; it does not replace RAG unless the user asks to skip RAG.

This step always runs after `SECTION_TYPE` is known and before the RAG Health Check.

#### Step 1: Structure Reference Question (always ask)

Ask:

```md
구조를 참고하고 싶은 reference paper가 있나요?
있으면 PDF/Markdown/path/URL 위치를 알려주세요. 여러 개도 가능합니다.
없으면 "없음"이라고 답해 주세요.
```

If the user provides one or more structure references, store them as `run_reference_layers.structure_references`.

#### Step 2: Voice/Tone Reference Question (always ask)

If the user provides any structure reference paper, ask:

```md
이 reference paper들의 voice/tone까지 따라할까요?
아니면 voice/tone을 따라하고 싶은 다른 reference paper가 있나요?
없으면 구조만 참고하고, 별도 voice/tone은 기존 style guide/RAG를 사용하겠습니다.
```

If the user provides no structure reference paper, still ask:

```md
voice/tone을 따라하고 싶은 reference paper가 있나요?
있으면 PDF/Markdown/path/URL 위치를 알려주세요. 여러 개도 가능합니다.
없으면 별도 voice/tone reference 없이 기존 style guide/RAG를 사용하겠습니다.
```

Supported answers:
- **same_as_structure**: use the same paper(s) for structure and voice/tone.
- **separate_voice_references**: user provides one or more separate voice/tone reference paper locations.
- **voice_only**: user provides one or more voice/tone reference paper locations but no structure reference.
- **structure_only**: use user references for structure only; keep voice/tone from existing style guide/RAG.
- **no_direct_references**: use existing RAG/style guide only.

Both `structure_references` and `voice_references` may contain one or more papers. The same paper may appear in both layers only when the user explicitly says to use it for both.

#### Gate output

```yaml
run_reference_layers:
  structure_references:
    - source: "[path|URL|uploaded file identifier]"
      role: "structure"
      status: "provided|none|unreadable"
      notes: "[user preference, target section, or page range]"
  voice_references:
    - source: "[path|URL|uploaded file identifier]"
      role: "voice_tone"
      status: "provided|none|unreadable"
      notes: "[same_as_structure|separate_voice_reference|voice_only|no_direct_references|style notes]"
  merge_policy:
    structure_priority: "user_provided_structure_refs > RAG structure layer > references/{SECTION_TYPE}-patterns.md > data/style-guide.md"
    voice_priority: "user_provided_voice_refs > RAG target voice layer > data/style-guide.md"
    persistence: "run_specific_by_default; update data/style-guide.md only after user approval or Pattern Learner trigger"
  open_reference_questions: "[missing paths, unreadable files, unresolved page ranges, or none]"
```

#### Processing rules

1. Process `structure_references` with Paper Preprocessor -> Section Analyzer -> run-specific Structure Layer.
2. Process `voice_references` with Paper Preprocessor -> Style Extractor -> run-specific Target Voice Layer.
3. Do not require structure references in order to use voice/tone references; the two layers are independent except when `same_as_structure` is explicitly selected.
4. If a reference is unreadable, ask for a corrected location once; if still unavailable, mark `unreadable` and continue with RAG/style guide.
5. Keep user-provided reference layers run-specific unless the user approves long-term learning.
6. Use direct references as priority guidance for organization, outline shape, figure/table integration, and section-specific rhetorical structure; use voice references for sentence rhythm, tone, transitions, and statistical/legend wording.
7. For manuscript bibliography, do not automatically add structure or voice/tone reference papers to the manuscript `## References` section unless that paper is explicitly cited as evidence for a claim, method, dataset, tool, or prior-work comparison.
8. For manuscript bibliography, do not add local files, relative paths, absolute paths, repository artifacts, generated outputs, code files, figures, CSV/TSV/JSON/JSONL files, or local Markdown artifacts to `## References`.

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
      Without RAG, RAG-based style learning (Phase 0) and
      real-time few-shot references (Step 3) will not be available.
      Writing will proceed using Phase -1.5 direct references if provided,
      plus existing style-guide.md patterns.

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
        description: "Write using Phase -1.5 direct references if provided, plus existing style-guide.md patterns"
        action:
          - "Skip Phase -1 and RAG searches in Phase 0a/0b"
          - "Process Phase -1.5 direct references if available; otherwise use existing style-guide.md"
          - "Proceed to Phase 2 (Writer) using run_reference_layers if available and existing style-guide.md"
          - "Real-time RAG few-shot in Step 3 also disabled"
          - "Set rag_available: false for downstream agents"

      3:
        label: "Provide PDFs directly"
        description: "Provide PDF files directly instead of RAG to process via Paper Preprocessor"
        action:
          - "Use any Phase -1.5 run_reference_layers already provided, or ask user to provide PDFs with dual-layer distinction (see below)"
          - "Activate Paper Preprocessor for each PDF"
          - "Preprocessor extracts section content → passes to appropriate agent"
          - "Proceed with Phase 0 using preprocessed content instead of RAG"

        # Dual-Layer PDF Selection (mirrors RAG collection selection)
        pdf_dual_layer:
          message: |
            PDFs follow the same two-layer distinction as Phase -1.5 direct references and RAG collections.
            If Phase -1.5 already collected these references, do not ask again; reuse run_reference_layers.

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

**Purpose**: Let the user choose which RAG collections to use for structure learning and voice extraction after the mandatory direct-reference gate. (Skipped if user chose "Proceed without RAG" in Health Check.)

1. Call `mcp__langconnect-rag__list_collections` to get available collections
2. Present collection list with names, document counts, and descriptions
3. Ask: "Which collection to use for the Structure Layer (structure/pattern learning)?" (default: agentpaper)
4. Ask: "Which collection to use for the Target Voice Layer (style/tone learning)?" (default: mypaper)
5. Store selected collection IDs for downstream phases
6. Same collection allowed for both layers. If user says "skip" or "default", use agentpaper + mypaper

### Phase 0a: Structure Pattern Learning (Parallel with 0b)

**Input**: Structure Layer collection + SECTION_TYPE + `run_reference_layers.structure_references`

1. If `run_reference_layers.structure_references` exists, preprocess each reference and pass extracted target section/captions to Section Analyzer first.
2. Execute 4 section-specific Structure Layer queries (see RAG Query Strategies) against selected collection when RAG is available.
3. Pass search results and direct-reference extracts to Section Analyzer agent with SECTION_TYPE.
4. Analyzer extracts section-appropriate structural patterns, groups by source document, synthesizes cross-document patterns, and marks direct-reference patterns as run-specific priority.
5. Optional: BioMCP enrichment for biological context.
6. Update the run-specific Structure Layer for this invocation. Update `data/style-guide.md` or `references/{SECTION_TYPE}-patterns.md` only after user approval or Pattern Learner trigger.

### Phase 0b: Target Voice Learning (Parallel with 0a)

**Input**: Target Voice collection + SECTION_TYPE + `run_reference_layers.voice_references`

1. If `run_reference_layers.voice_references` exists, preprocess each reference and pass extracted section/caption text to Style Extractor first.
2. Execute 3 section-specific + 1 field-specific voice queries against selected collection when RAG is available.
3. Pass search results and direct-reference extracts to Style Extractor agent with SECTION_TYPE.
4. Extractor analyzes sentence patterns, transitions, statistical reporting style, logic flow, tone profile, and legend/caption wording where relevant.
5. Update the run-specific Target Voice Layer for this invocation. Update `data/style-guide.md` only after user approval or Pattern Learner trigger.

### Phase 1: Style Merge

**Input**: Structure Layer + Target Voice Layer from Phase 0a/0b

**Priority Rules** (conflict resolution):
- Structure -> user-provided Structure Layer first, then RAG/static Structure Layer
- Voice/tone -> user-provided Target Voice Layer first, then RAG/static Target Voice Layer
- Statistics -> Target Voice Layer unless a user-provided structure reference imposes a journal/reporting convention
- Transitions -> Target Voice Layer
- Figure refs -> Structure Layer (with Voice overlay)
- Figure/table legends -> Structure Layer + `references/legend-patterns.md` (with Target Voice overlay)

The merged guide is the complete `data/style-guide.md` -- no separate file needed. The writer reads the complete style guide and applies priority rules during writing.

When `run_reference_layers` exists, the merged guide for that invocation is:

```yaml
merged_invocation_guide:
  structure:
    priority_1: "run_reference_layers.structure_references"
    priority_2: "RAG Structure Layer"
    priority_3: "references/{SECTION_TYPE}-patterns.md and data/style-guide.md"
  voice_tone:
    priority_1: "run_reference_layers.voice_references"
    priority_2: "RAG Target Voice Layer"
    priority_3: "data/style-guide.md"
  persistence: "Do not write run-specific direct-reference patterns into data/style-guide.md unless the user approves learning from this output."
```

### Phase 2: Content Creation (Section Writer)

**Input**: User's research materials + merged style guide + real-time RAG + SECTION_TYPE

#### Step 0: Input Collection (section-specific)

| Section | Required Inputs | Optional Inputs |
|---------|----------------|-----------------|
| Introduction | Research topic, research gap, contribution, key references | Background notes (.md), related work notes, conceptual overview figure, target journal |
| Methods | Pipeline description, tools/versions, datasets, parameters | Pseudocode, code repo URL, pipeline/architecture figure, reporting guideline |
| Results | CSV data + descriptions, figures/tables + findings + stats + legend-ready display metadata, experimental context | Results context (.md), supplementary materials/data, target journal, reporting guideline |
| Discussion | Key findings summary, Results section text, limitations | Related work comparison, future directions, broader impact, synthesis/model figure, target journal |

Full input specification with Korean prompts: see `agents/section-writer.md` Step 0.

For every section, a user-provided figure with an actual file path should be embedded in the saved Markdown using `![Figure X](path/to/file.png)` near its first substantive reference. Description-only figures are referenced and captioned without inventing an image path. For Results, a figure/table is "available" for legend drafting when either an actual file exists or the user provides sufficient description, panel, table, and statistical metadata for a partial or complete legend.

#### Supplementary Materials Contract

Supplementary outputs use a hybrid policy:
- **Supplementary Figures**: embed file-backed figures with `![Supplementary Figure X](path)` and write legends/captions. Description-only supplementary figures receive legend text without invented paths.
- **Supplementary Tables**: write a legend in `## Figure Legends` under `### Supplementary Tables`. Small tables may be rendered inline in Markdown when readable. Large tables are not inlined.
- **Supplementary Data**: always represent as separate data files or linked existing files, with a short Markdown description. Never paste full supplementary data payloads into the section body.

Large supplementary table threshold:
- A supplementary table is large if it has `>20 rows` or more than `8 columns`.
- Large supplementary tables are written or linked as separate CSV/TSV artifacts instead of full Markdown tables.
- Default generated target: `supplementary/supplementary_table_X.csv`.
- Use TSV only when the user provides TSV or explicitly requests TSV; otherwise generate CSV.
- Never invent supplementary table or supplementary data values. Missing values, columns, units, row labels, or source paths must be marked as `[needs: ...]`.

When supplementary files exist, add a `## Supplementary Materials` block after Results `## Figure Legends`. Each item must include:
- Identifier (`Supplementary Figure X`, `Supplementary Table X`, `Supplementary Data X`)
- File link or generated target path
- Short description
- Value semantics (what rows, columns, or values mean)
- Missing-field markers such as `[needs: column units]` when required

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
| Q2 | Top 3-5 most important findings in order of importance, and why is each result needed for the paper's argument? | priority-setting | For each finding: claim/question answered + evidence + rationale |
| Q3 | For each Figure/Table: what claim does it support, what does it show, why is this figure/table needed, what is the key takeaway, and what legend details are available? | visual-inventory + legend-inventory | Ensures claim-driven figure integration and legend readiness |
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
  methods_blueprint: "[approved Methods Blueprint object from Step 2d, or skipped_by_user Lite Mode object]"
  results_blueprint: "[approved Results Blueprint object from Step 2d, or skipped_by_user Lite Mode object]"
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

#### Step 2: Interactive Outline / Blueprint Gate

Introduction and Discussion use the existing interactive outline process. Methods and Results use a stronger Blueprint gate before prose: a hierarchical skeleton plus verification matrix is drafted with the user, gap/risk issues are resolved or explicitly accepted, and prose generation is blocked until the user approves the complete Blueprint.

Methods/Results Lite Mode is available only when the user explicitly says "outline only", "skip blueprint", or "lite mode". In that case, record `approved_blueprint.approval_status: skipped_by_user` and skip Blueprint Alignment review.

| Step | Introduction | Methods | Results | Discussion |
|------|-------------|---------|---------|------------|
| 2a: Skeleton | Funnel: broad -> problem -> gap -> contribution | **Blueprint skeleton**: subsection order, organization principle, method steps | **Blueprint skeleton**: subsection order, narrative arc, planned findings | Interpretive: summary -> comparison -> limitations -> future |
| 2b: Details / Matrix | Per-paragraph key points + citations | **Blueprint matrix**: block, subsection, procedure, data/input, tool/version, parameters, output, reproducibility risk | **Blueprint matrix**: block, subsection, result rationale, claim/finding, evidence source, figure/table, figure rationale, statistics, closing takeaway, scope limits, legend status/missing fields | Per-subsection interpretation + citations |
| 2c: Verification | Conceptual figure placement (if any) | Gap/risk check: missing versions, parameters, input/output transitions | Gap/risk check: missing result rationale, unsupported claims, orphan or decorative figures, missing statistics, missing closing takeaway, missing legend fields | Back-references to specific Results |
| 2d: Approval / Connection | Transition leading into Methods/Results | **Strong HITL approval gate** before prose; emits `approved_blueprint` | **Strong HITL approval gate** before prose; emits `approved_blueprint` | Limitation <-> future direction pairing |

For Methods and Results, the approved Blueprint is passed to the reviewer as `approved_blueprint`:

```yaml
approved_blueprint:
  section_type: "methods|results"
  approval_status: "approved|skipped_by_user"
  approved_at_stage: "Step 2d"
  source: "paper_context.methods_blueprint OR paper_context.results_blueprint"
  skeleton: "[approved hierarchical skeleton]"
  matrix: "[approved Blueprint matrix]"
  gap_risk_decisions: "[resolved or user-accepted gaps]"
  revision_history: "[summary of user-requested changes before approval]"
```

The writer must persist the same object in `paper_context.methods_blueprint` or `paper_context.results_blueprint` before prose generation and reviewer handoff.

#### Step 3: Prose + RAG Few-Shot

Per subsection: extract topic keywords -> RAG search Target Voice collection -> use as style exemplars -> write prose matching merged style guide.

For Methods and Results, Step 3 is constrained by `approved_blueprint`. The writer must pause and return to Step 2d for Blueprint revision approval before adding any new method step, result claim, statistic, figure/table placement, tool, parameter, or subsection that is not represented in the approved Blueprint.

**Section-specific paragraph flow**:

| Section | Paragraph Flow | Key Writing Rules |
|---------|---------------|-------------------|
| Introduction | Broad context -> narrow to problem -> existing approaches -> gap statement -> contribution -> roadmap | Funnel structure; numeric citations `[1]`; explicit gap; "we introduce/present/propose"; no over-promising; optional conceptual figures embedded and captioned when provided |
| Methods | Overview -> data description -> step-by-step procedure -> tools/versions -> evaluation | Past tense; passive preferred; every parameter stated; software versions explicit; no results or interpretation; optional workflow/pipeline figures embedded and captioned with reproducibility-relevant context |
| Results | Result rationale -> method-brief -> primary finding + stats -> figure evidence/rationale -> closing takeaway -> transition -> figure/table legends for available displays -> Supplementary Materials links when files exist | No interpretation (save for Discussion); statistics inline; figures described as evidence, not just referenced; empirical subsections close with one data-backed takeaway; legends use `references/legend-patterns.md`; large supplementary tables/data are linked as artifacts |
| Discussion | Recap finding -> interpretation -> literature comparison -> implications -> limitations -> future | Interpretation required; compare with literature using numeric citations; no new data; appropriate hedging; end with broader impact; optional synthesis/model figures embedded and captioned without introducing new data |

**Integration pass** (after prose, section-specific checks):
- **Introduction**: numeric citation completeness, gap statement presence, contribution clarity, conceptual figure embed/caption check when provided
- **Methods**: reproducibility detail check, parameter completeness, version numbers, workflow/pipeline figure embed/caption check when provided
- **Results**: terminology consistency, Markdown figure embeds, figure refs, statistics, flow, word count, voice, figure/table legend completeness, supplementary artifact links
- **Discussion**: no-new-data check, limitation presence, over-interpretation scan, synthesis/model figure embed/caption check when provided

Full prose protocols with paragraph templates: see `agents/section-writer.md` Step 3.

### Phase 3: Quality Review (Section Reviewer)

**Input**: Draft section + source data + SECTION_TYPE + section_configs + `approved_blueprint` for Methods/Results

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
| 8. Blueprint Alignment | Prose matches approved Blueprint | Methods/Results only (1.4) |

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

Phase -3: Deep Interview Gate
          → Clarify target result, scope, constraints, completion criteria
Phase -2: Select section type (Introduction / Methods / Results / Discussion)
Phase -1.5: Ask for structure reference papers, then always ask separate voice/tone reference question
Phase -1: Select RAG collections
          → Choose Structure Layer collection (default: agentpaper)
          → Choose Target Voice collection (default: mypaper)
Phase 0:  Dual-layer learning (parallel)
          → RAG search + Section Analyzer → Structure Layer
          → RAG search + Style Extractor → Target Voice Layer
Phase 1:  Style merge (Priority Rules applied)
Phase 2:  Provide your materials (section-specific inputs)
          → Tiered Conversational Interview (Phase A-D)
          → Interactive Outline / Blueprint Gate
          → Prose + real-time RAG few-shot references
Phase 3:  Review generated draft
          → Section Reviewer runs section-weighted review
          → Pedagogical revision diffs with WHY
          → Auto-revision if issues found (max 3 cycles)
Phase 4:  Approve or request revisions
Phase 5:  (Automatic) Patterns learned for future use (section-tagged)
```

### Quick Mode (Skip Phase 0-1)

When you already have a style guide or want to write without reference paper analysis:

```
Phase -3: Deep Interview Gate (always required)
Phase -2: Section selection (always required)
Phase -1.5: Reference Paper Gate (always required; direct references optional)
Phase -1: Collection selection (preserved for real-time RAG in Phase 2)
Phase 2:  Writer (uses existing style-guide.md + real-time RAG few-shot)
Phase 3:  Reviewer (section-weighted review)
Phase 4:  Pattern Learner (section-tagged)
```

**When to use**: Style guide already populated from previous analysis, fast turnaround needed, or reference papers unavailable but user knows the target style.

**To invoke**: Provide your data and materials directly without reference papers. The system detects the absence of reference input and proceeds to Phase 2. Collection Selection is still performed for real-time RAG few-shot during writing.

### Style Learning Only

```
Phase -3: Deep Interview Gate (determine learning goal and scope)
Phase -2: Section selection (determines which patterns to learn)
Phase -1.5: Reference Paper Gate (collect structure and/or voice/tone references)
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

Phase -3: Deep Interview Gate (clarify writing target and constraints before selection)
Phase -2: Section selection
Phase -1.5: Reference Paper Gate (ask for structure refs, then always ask voice/tone refs)
Phase -1: Collection selection (for real-time RAG)
Phase 2:  Tiered Interview → Interactive Outline / Blueprint Gate → Prose + RAG
Phase 3:  Section Reviewer checks quality (section-weighted review)
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

Markdown-first, Word-ready output:
- `academic_writer_brief` summary from Phase -3 before section-specific output
- `run_reference_layers` summary from Phase -1.5 when direct references are provided or explicitly declined
- `##` for main section heading
- `###` for subsections
- Figure references: `(Figure X)`, `(Table Y)`
- File-backed figures in any section: embed with Markdown image syntax, e.g. `![Figure 1](figures/figure1.png)`, placed near the first substantive reference or immediately before the relevant caption/legend. Use the user-provided path unless the user asks for path normalization.
- For Results with available displays: separate `## Figure Legends` output with `Main Figures`, `Main Tables`, `Supplementary Figures`, and `Supplementary Tables` subsections as applicable
- For Results with supplementary files: after `## Figure Legends`, include `## Supplementary Materials` with one entry per supplementary figure, supplementary table artifact, and Supplementary Data file
- Large supplementary tables (`>20 rows` or more than `8 columns`) are linked as `supplementary/supplementary_table_X.csv` by default; use `.tsv` only for user-provided TSV or explicit TSV requests
- Supplementary Data is always linked as separate files or existing artifacts with a short Markdown description and value semantics
- For Introduction, Methods, and Discussion figures: provide section-appropriate captions or short legends adjacent to the embed; do not force Results-style full legends unless the user requests them.
- Statistics inline: `(p < 0.05)`, `(n = 100)`
- Numeric citation policy: use first-use ordered numeric citations in prose, e.g. `[1]`, `[2]`; use `[1,2]` for multiple non-consecutive citations and `[1-3]` for consecutive ranges.
- Add a final `## References` section to each generated Markdown section when at least one cited paper, dataset, tool, or link has source metadata.
- Reference entries use the same numeric order as first citation and this identifier-first format: `[1] DOI: 10.xxxx/yyyy. "Full title."`, `[2] arXiv: 2603.22455. "Full title."`, or `[3] URL: https://example.org/page. "Full title." Optional source note.`
- Reference identifier priority is `DOI > arXiv > URL`: for papers, use DOI whenever known; if DOI is unavailable, use arXiv when available; otherwise use URL.
- Only external citable sources belong in `## References`: DOI, arXiv ID, or a public URL.
- Public URL means an `http://` or `https://` URL that readers can resolve outside the local repository.
- Do not use `URL:` for local files, relative paths, absolute paths, repository artifacts, generated outputs, code files, figures, CSV/TSV/JSON/JSONL files, or local Markdown artifacts.
- Mention local artifacts inline as code paths or list them under `## Source Artifacts`, `## Reproducibility Artifacts`, or `## Supplementary Materials` when they are useful for reproducibility.
- Put the full title in double quotes immediately after the identifier sentence. Keep the period inside the closing quote when the title is complete.
- Use `[needs: citation]` for claims that need support but do not yet have a source, and `[needs: reference metadata]` when a cited source lacks enough metadata for the `## References` entry.
- Never invent authors, titles, years, DOIs, or URLs. If metadata is unavailable, preserve the citation marker and flag the missing field.
- For manuscript bibliography, do not automatically add structure or voice/tone reference papers from `run_reference_layers` to `## References` unless they are explicitly cited as evidence for a manuscript claim, method, dataset, tool, or prior-work comparison.
- Professional academic tone matching Target Voice Layer
- Section-appropriate tense, voice, and hedging per `section_configs`
- For Methods/Results: `approved_blueprint` metadata passed to Reviewer; Lite Mode uses `approval_status: skipped_by_user`
- Default saved artifact: Markdown file (`.md`). Save or convert to DOCX, PDF, HTML, or other formats only when the user explicitly requests that format or a downstream converter is invoked.

---

## Quality Checklist

Before final approval:

**All Sections**:
- [ ] Deep Interview Gate completed and `academic_writer_brief` emitted; if the opening request was already complete, no extra question was asked
- [ ] Section type selected (Phase -2)
- [ ] Reference Paper Gate completed (Phase -1.5): structure reference question asked and voice/tone reference question asked
- [ ] Direct structure and voice/tone references stored separately in `run_reference_layers`, with one or more references allowed per layer and same-paper reuse allowed only when explicit
- [ ] Collection selection completed (Phase -1)
- [ ] Structure Layer populated from reference papers
- [ ] Target Voice Layer populated from target voice papers
- [ ] Tiered interview completed (Phases A-D)
- [ ] Outline approved by user (Introduction/Discussion) or Blueprint approved by user (Methods/Results)
- [ ] Structure matches learned patterns
- [ ] Voice matches Target Voice Layer profile
- [ ] Numeric citations are ordered by first use and each cited source with available metadata appears in `## References`
- [ ] `## References` entries use identifier-first format with DOI > arXiv > URL priority and double-quoted titles
- [ ] `## References` excludes structure-only and voice/tone-only reference papers unless they are explicitly cited as evidence
- [ ] `## References` excludes local files, relative paths, absolute paths, repository artifacts, generated outputs, and local Markdown artifacts
- [ ] Unsupported claims are marked `[needs: citation]`; incomplete reference entries are marked `[needs: reference metadata]`
- [ ] Consistent terminology throughout
- [ ] Appropriate voice and tense per section_configs
- [ ] Reporting guideline items addressed (if applicable)
- [ ] Word count within journal limits (if specified)
- [ ] Revision diff items resolved

**Introduction-specific**:
- [ ] Funnel structure (broad -> specific) maintained
- [ ] Gap statement explicit and positioned correctly
- [ ] Contribution statement present and matched to actual Results
- [ ] All existing work claims have numeric citations or `[needs: citation]`
- [ ] Background scope appropriate for target audience

**Methods-specific**:
- [ ] Approved Methods Blueprint exists, or Lite Mode reduced Blueprint records `approval_status: skipped_by_user`
- [ ] All tools and software versions stated
- [ ] All parameters explicitly specified (not "default")
- [ ] Sufficient detail for replication
- [ ] No results or interpretation present
- [ ] Data sources and access described
- [ ] Code/data availability mentioned

**Results-specific**:
- [ ] Approved Results Blueprint exists, or Lite Mode reduced Blueprint records `approval_status: skipped_by_user`
- [ ] All figures/tables referenced and described
- [ ] Each subsection explains why the result is needed for the paper's argument
- [ ] Each figure/table has an explicit evidentiary role for a claim, not just a descriptive placement
- [ ] Available main and supplementary figure/table legends drafted, or explicitly omitted because no display metadata exists
- [ ] Partial legends preserve known information and mark missing values as `[needs: ...]` without inventing statistics, sample sizes, encodings, or scale bars
- [ ] Large supplementary tables (`>20 rows` or more than `8 columns`) are saved or linked as CSV/TSV artifacts rather than inlined in Markdown
- [ ] Supplementary Data files are linked with short descriptions, value semantics, and `[needs: ...]` markers for unresolved fields
- [ ] When supplementary files exist, `## Supplementary Materials` appears after Results `## Figure Legends`
- [ ] Each empirical/evaluation subsection ends with a one-sentence data-backed closing takeaway
- [ ] Statistics complete (values, tests, p-values, n)
- [ ] No interpretation beyond data
- [ ] Transitions connect subsections
- [ ] Narrative arc maintains progressive complexity

**Figure handling across all sections**:
- [ ] Every user-provided figure with an actual file path is embedded in Markdown as `![Figure X](path/to/file.png)`
- [ ] Every embedded figure is also referenced in prose with `(Figure X)` or an equivalent target-journal style
- [ ] Description-only figures are not given invented image paths
- [ ] Introduction/Methods/Discussion figure captions fit the section purpose; Results figures receive full legends when display metadata is available

**Discussion-specific**:
- [ ] Key findings summarized (no new data)
- [ ] Interpretations traceable to specific Results
- [ ] Literature comparison uses numeric citations and corresponding `## References` entries
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
- `references/legend-patterns.md` -- Figure/table legend patterns learned from DeepMAST reference papers
- `references/discussion-patterns.md` -- Discussion structure patterns (interpretation, limitations)
- `data/style-guide.md` -- Living style guide (Dual-Layer: Structure + Target Voice + Priority Rules)
- `data/feedback-log.md` -- Feedback history (section-tagged)
