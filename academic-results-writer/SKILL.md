---
name: academic-results-writer
description: Write publication-quality Results sections for academic research articles in Bioinformatics, Genomics, and Computational Biology. Use when the user needs to write Results sections based on their experimental data, figures, and analysis outputs. Requires reference papers (via RAG or PDF) to learn structure patterns AND user's own data/figures. Activation is explicit only - do not auto-trigger.
---

# Academic Results Writer

Multi-agent system for writing publication-quality Results sections by learning from reference papers via RAG-based dual-layer style analysis and continuously improving through feedback.

## Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    ORCHESTRATOR (SKILL.md)                    │
│                                                              │
│  Phase -1: Collection Selection                              │
│  ┌──────────────────────────────────────────────────────┐    │
│  │  list_collections() → User selects:                  │    │
│  │    Q1: Structure Layer collection (default: agentpaper)│   │
│  │    Q2: Target Voice collection (default: mypaper)    │    │
│  └──────────────────────┬───────────────────────────────┘    │
│                         │                                    │
│  Phase 0a + 0b: Dual-Layer Learning (PARALLEL)               │
│  ┌──────────────┐    ┌──────────────┐                        │
│  │   RAG Search  │    │   RAG Search  │                       │
│  │  (Structure)  │    │  (Voice)     │                        │
│  │      ↓        │    │      ↓        │                       │
│  │   Results    │    │    Style     │                        │
│  │   Analyzer   │    │   Extractor  │                        │
│  │      ↓        │    │      ↓        │                       │
│  │  Structure   │    │ Target Voice │                        │
│  │   Layer      │    │    Layer     │                        │
│  └──────┬───────┘    └──────┬───────┘                        │
│         │                   │                                │
│         └─────────┬─────────┘                                │
│                   │                                          │
│  Phase 1: Style Merge                                        │
│  ┌────────────────▼─────────────────┐                        │
│  │  Structure Layer + Voice Layer   │                        │
│  │  + Priority Rules → Merged Guide │                        │
│  └────────────────┬─────────────────┘                        │
│                   │                                          │
│  Phase 2: Writing (Enhanced)                                 │
│  ┌──────────────┐ ┌──────▼───────┐                           │
│  │  User Data   │→│   Results    │  Step 0: Input Collection │
│  │  CSV+Fig+MD  │ │   Writer     │  Step 1: Intent Interview │
│  └──────────────┘ │              │  Step 2: Interactive Outline│
│                   │              │  Step 3: Prose + RAG       │
│                   └──────┬───────┘                           │
│                          │                                   │
│  Phase 3: Review                                             │
│  ┌──────────────┐ ┌──────▼───────┐                           │
│  │  Source Data  │→│   Results    │  7-pass review            │
│  └──────────────┘ │   Reviewer   │                           │
│                   └──────┬───────┘                           │
│                          │                                   │
│                 ┌────────┴────────┐                           │
│                 │ Approved?       │                           │
│                 ├─ No → Writer    │                           │
│                 ├─ Yes ↓          │                           │
│  Phase 4: Learn │                │                           │
│  ┌──────────────┐│                │                           │
│  │   Pattern    │◀┘               │                           │
│  │   Learner    │                                            │
│  └──────────────┘                                            │
│                                                              │
│  Fallback: RAG failure → Paper Preprocessor → User input     │
└──────────────────────────────────────────────────────────────┘
```

## Agents

| Agent | Role | Input | Output |
|-------|------|-------|--------|
| **Style Extractor** | Extract voice/tone from Target Voice collection | RAG search results | Target Voice Layer for style guide |
| **Results Analyzer** | Extract structure patterns from reference papers | RAG search results | Structure Layer for style guide |
| **Results Writer** | Write Results using multi-stage process | User's data + merged style guide + RAG few-shot | Interview → Outline → Draft Results section |
| **Results Reviewer** | 7-pass review for accuracy and quality | Draft + source data | Review report + revision diffs |
| **Pattern Learner** | Learn from feedback, improve patterns | Approved sections + feedback | Style guide updates (both layers) |
| **Paper Preprocessor** | *Fallback*: Extract Results from PDFs | Reference PDFs | Extracted Results text + metadata |

Agent definitions: `agents/style-extractor.md`, `agents/results-analyzer.md`, `agents/results-writer.md`, `agents/results-reviewer.md`, `agents/pattern-learner.md`, `agents/paper-preprocessor.md`

---

## RAG Configuration

### Collections

The system uses two RAG collections via the `langconnect-rag` MCP server:

```yaml
rag_collections:
  structure_layer:
    description: "Reference papers for learning Results section structure and organization"
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
  search_type: "hybrid"  # Combines semantic + keyword search
  limit: 5               # Per query
```

### Query Strategies

**Structure Layer queries** (for Results Analyzer):
1. `"Results section structure organization subsection"` — section hierarchy
2. `"findings statistical analysis benchmark performance"` — statistical patterns
3. `"Figure Table panel results comparison"` — figure integration
4. `"methodology evaluation framework validation"` — structure conventions

**Target Voice queries** (for Style Extractor):
1. `"Results section findings statistical analysis"` — general Results voice
2. `"we found demonstrated showed revealed indicated"` — result-reporting verbs
3. `"Figure Table panel comparison significantly"` — figure/stat language
4. Field-specific query adapted to user's research area

**Real-time few-shot queries** (for Results Writer, during prose):
- Subsection topic keywords → Target Voice collection (primary)
- Structure reference → Structure collection (secondary, when needed)

---

## Workflow

### Phase -1: Collection Selection

**Purpose**: Let the user choose which RAG collections to use for structure learning and voice extraction.

```
Orchestrator calls list_collections()
         │
         ▼
┌─────────────────────────────────────────────┐
│  Display available collections to user      │
│                                             │
│  Q1: "Which collection for the Structure     │
│       Layer (structure/pattern learning)?"  │
│       → Show list (default: agentpaper)     │
│                                             │
│  Q2: "Which collection for the Target Voice │
│       Layer (style/tone learning)?"         │
│       → Show list (default: mypaper)        │
│                                             │
│  ※ Same collection allowed for both layers  │
│  ※ User can skip → use defaults             │
└─────────────────────┬───────────────────────┘
                      │
                      ▼
         Selected collection IDs
         passed to Phase 0a/0b
```

**Process**:
1. Call `mcp__langconnect-rag__list_collections` to get available collections
2. Present collection list with names, document counts, and descriptions
3. Ask user to select Structure Layer collection (default: agentpaper)
4. Ask user to select Target Voice collection (default: mypaper)
5. Store selected collection IDs for downstream phases
6. If user says "skip" or "default", use agentpaper + mypaper

### Phase 0a: Structure Pattern Learning (Parallel with 0b)

**Input**: RAG search results from user-selected Structure Layer collection

```
Selected Structure collection
         │
         ▼
┌─────────────────────────────────┐
│  RAG Search (4 queries)         │
│  mcp__langconnect-rag__         │
│  search_documents               │
│  (hybrid, limit=5 per query)    │
└─────────────┬───────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│    Results Analyzer             │
│  • Analyze retrieved chunks     │
│  • Group by source document     │
│  • Extract structure patterns   │
│  • Synthesize cross-document    │
│  • (Optional) BioMCP enrichment │
└─────────────┬───────────────────┘
              │
              ▼
   Update style-guide.md
   → Structure Layer section
   Update results-patterns.md
```

**Process**:
1. Execute 4 structure-focused RAG queries against selected collection
2. Pass search results to Results Analyzer agent
3. Analyzer extracts structural patterns, statistics format, figure integration
4. Update `data/style-guide.md` Structure Layer section
5. Update `references/results-patterns.md` if new templates found

### Phase 0b: Target Voice Learning (Parallel with 0a)

**Input**: RAG search results from user-selected Target Voice collection

```
Selected Voice collection
         │
         ▼
┌─────────────────────────────────┐
│  RAG Search (4 queries)         │
│  mcp__langconnect-rag__         │
│  search_documents               │
│  (hybrid, limit=5 per query)    │
└─────────────┬───────────────────┘
              │
              ▼
┌─────────────────────────────────┐
│    Style Extractor              │
│  • Sentence structure patterns  │
│  • Transition patterns          │
│  • Statistical reporting style  │
│  • Logic flow mapping           │
│  • Tone profiling               │
└─────────────┬───────────────────┘
              │
              ▼
   Update style-guide.md
   → Target Voice Layer section
```

**Process**:
1. Execute 4 voice-focused RAG queries against selected collection
2. Pass search results to Style Extractor agent
3. Extractor analyzes sentence patterns, transitions, stats format, tone
4. Update `data/style-guide.md` Target Voice Layer section

### Phase 1: Style Merge

**Input**: Structure Layer + Target Voice Layer from Phase 0a/0b

```
Structure Layer + Target Voice Layer
         │
         ▼
┌─────────────────────────────────┐
│    Style Merge                  │
│  • Apply Priority Rules         │
│  • Resolve conflicts            │
│  • Generate merged guidance     │
└─────────────┬───────────────────┘
              │
              ▼
   Merged style-guide.md ready
   for Results Writer
```

**Process**:
1. Read both layers from `data/style-guide.md`
2. Apply Priority Rules (defined in style guide):
   - Structure → Structure Layer
   - Voice/tone → Target Voice Layer
   - Statistics → Target Voice Layer
   - Transitions → Target Voice Layer
   - Figure refs → Structure Layer (with Voice overlay)
3. The merged guide is the complete `data/style-guide.md` — no separate file needed
4. Writer reads the complete style guide and applies priority rules during writing

### Phase 2: Content Creation (Results Writer)

**Input**: User's research materials + merged style guide + real-time RAG

```
User provides:
• CSV data files + descriptions
• Figures + descriptions + key findings
• Tables with key metrics
• Results context (.md files)
• (Optional) Target journal
• (Optional) Reporting guideline
         │
         ▼
┌─────────────────────────────────┐
│    Results Writer               │
│                                 │
│  Step 0: Input Collection       │
│    (CSV + Figures + MD context) │
│                                 │
│  Step 1: Writing Intent         │
│    Interview (Q1-Q5, required)  │
│    → User answers all questions │
│    → Intent summary confirmed   │
│                                 │
│  Step 2: Interactive Outline    │
│    2a: Overall structure        │
│        → USER APPROVAL          │
│    2b: Subsection key points    │
│        → USER APPROVAL          │
│    2c: Figure/Table placement   │
│        → USER APPROVAL          │
│    2d: Transition plan          │
│        → USER APPROVAL          │
│                                 │
│  Step 3: Prose + RAG few-shot   │
│    Per subsection:              │
│    • Extract topic keywords     │
│    • RAG search → 2-3 exemplars │
│    • Write matching voice/style │
│    • Integration pass           │
└─────────────┬───────────────────┘
              │
              ▼
   Draft Results Section
```

**Multi-Stage Process**:

**Step 0 — Input Collection**: Gather user's data files, figures, tables, and context.

**Step 1 — Writing Intent Interview** (Required, Q1-Q5):
- Q1: "What should be the central focus of this Results section?"
- Q2: "Are there any findings or comparisons you want to particularly emphasize?"
- Q3: "Do you have a preferred subsection order?"
- Q4: "Do you have a previously written Results section?" (if yes, plan the transition)
- Q5: "Are there any papers or styles you would particularly like to reference?"

**Step 2 — Interactive Outline** (Each step requires user approval):
- 2a: Overall structure → USER APPROVAL REQUIRED
- 2b: Per-subsection key points → USER APPROVAL REQUIRED
- 2c: Figure/Table placement → USER APPROVAL REQUIRED
- 2d: Transition and connection plan → USER APPROVAL REQUIRED

**Step 3 — Prose with RAG Few-Shot**:
- For each subsection: extract keywords → RAG search Target Voice collection → use as style exemplars
- Write prose matching merged style guide
- Integration pass: terminology, figure refs, statistics, flow, word count, voice consistency

### Phase 3: Quality Review (Results Reviewer)

**Input**: Draft Results section + source data

```
Draft Results Section
         │
         ▼
┌─────────────────────────────────┐
│    Results Reviewer             │
│  Pass 1: Factual Accuracy       │
│  Pass 2: Statistical Review     │
│  Pass 3: Structural Review      │
│  Pass 4: Style & Clarity        │
│  Pass 5: Completeness Check     │
│  Pass 6: Reporting Guideline    │
│  Pass 7: Reproducibility        │
└─────────────┬───────────────────┘
              │
              ├──────────────▶ Issues found? ──▶ Return to Writer
              │                                  (with revision_diff)
              ▼
   Approved Results Section
```

**7-Pass Review**:
- **Pass 1-5**: Core review (accuracy, statistics, structure, style, completeness)
- **Pass 6**: Reporting guideline compliance check (STROBE/CONSORT/PRISMA/etc.)
- **Pass 7**: Reproducibility check (sufficient detail for a reader to understand how results were obtained)

**Output includes**:
- Quality score (1-10) per pass
- Issues categorized as critical/major/minor
- `revision_diff`: Exact text replacements with location, original, suggested, and reason
- Reporting compliance checklist with met/missing items
- Prioritized improvement list

**Revision loop**: If assessment is "major_revisions" or "minor_revisions", the draft returns to the Writer with specific revision_diff instructions. Maximum 3 revision cycles before presenting to user for decision.

### Phase 4: Continuous Learning (Pattern Learner)

**Input**: Approved sections + reviewer feedback + style accuracy data

```
Approved Section + Feedback
         │
         ▼
┌─────────────────────────────────┐
│    Pattern Learner              │
│  • Extract success patterns     │
│  • Analyze corrections          │
│  • Track reporting gaps         │
│  • Track RAG effectiveness      │
│  • Track Voice Layer accuracy   │
│  • Update style guide           │
│  • Version control              │
└─────────────┬───────────────────┘
              │
              ├──────────▶ data/style-guide.md (updated)
              └──────────▶ data/pattern-history/ (versioned)
```

**Learning triggers**:
- User approves final Results section
- Significant reviewer corrections (>3)
- Periodic review (every 10 outputs)
- Style Extractor voice accuracy below threshold

---

## Fallback Chain

When RAG is unavailable or returns insufficient results:

```
Primary: RAG Search (mcp__langconnect-rag__search_documents)
    │
    ├── Success (3+ relevant chunks) → Continue pipeline
    │
    └── Failure (RAG unavailable, <3 chunks, or connection error)
            │
            ▼
Fallback 1: Paper Preprocessor (PDF direct processing)
    │
    ├── Success → Pass extracted content to Analyzer
    │
    └── Failure (no PDF available, extraction fails)
            │
            ▼
Fallback 2: User Input (paste text, provide page ranges)
    │
    └── Continue with user-provided content
```

**Fallback triggers**:
- RAG MCP server unavailable (connection error)
- RAG collection empty or not configured
- RAG search returns fewer than 3 relevant chunks
- User explicitly provides PDF files instead of using RAG

When fallback activates:
1. Inform user: "RAG search did not return sufficient results. Switching to direct PDF processing."
2. Activate Paper Preprocessor for PDF-based extraction
3. Pass preprocessed content to Results Analyzer (same downstream pipeline)
4. If PDF also unavailable, ask user for direct text input

---

## Data Directory Structure

```
data/
├── style-guide.md          # Living style guide — Dual-Layer architecture
│                           #   Structure Layer (from reference papers)
│                           #   Target Voice Layer (from target voice papers)
│                           #   Priority Rules
├── feedback-log.md         # Reviewer feedback history
├── samples/                # Example Results sections
├── approved-sections/      # User-approved final outputs
└── pattern-history/        # Style guide versions
    ├── style-guide-v1.md
    ├── style-guide-v2.md
    └── changelog.md
```

---

## Usage

### Complete Workflow (Full Pipeline)

```
/academic-results-writer

Phase -1: Select RAG collections
          → Choose Structure Layer collection (default: agentpaper)
          → Choose Target Voice collection (default: mypaper)
Phase 0:  Dual-layer learning (parallel)
          → RAG search + Results Analyzer → Structure Layer
          → RAG search + Style Extractor → Target Voice Layer
Phase 1:  Style merge (Priority Rules applied)
Phase 2:  Provide your figures, tables, CSV data, and context
          → Writing Intent Interview (Q1-Q5)
          → Interactive Outline (user approves each step)
          → Prose + real-time RAG few-shot references
Phase 3:  Review generated draft
          → Results Reviewer runs 7-pass review
          → Auto-revision if issues found
Phase 4:  Approve or request revisions
Phase 5:  (Automatic) Patterns learned for future use
```

### Quick Mode (Skip Phase 0-1)

When you already have a style guide or want to write without reference paper analysis:

```
User provides: Figure files + detailed descriptions
               (key findings, statistics, experimental context)
         │
         ▼
Phase -1: Collection Selection (preserved — for real-time RAG in Phase 2)
         │
         ▼
Phase 2: Writer (uses existing style-guide.md + real-time RAG few-shot)
         │
         ▼
Phase 3: Reviewer (7-pass review)
         │
         ▼
Phase 4: Pattern Learner
```

**When to use Quick mode**:
- Style guide is already populated from previous reference analysis
- User wants fast turnaround without full reference paper processing
- Reference papers are not available but user knows the target style

**To invoke Quick mode**: Provide your data and figures directly without reference papers. The system will detect the absence of reference input and proceed to Phase 2. Collection Selection is still performed for real-time RAG few-shot during writing.

### Style Learning Only

```
Provide reference papers for style guide update:
• Select Structure Layer collection (or use default)
• Select Target Voice collection (or use default)

→ Phase 0a: RAG search + Results Analyzer → Structure Layer
→ Phase 0b: RAG search + Style Extractor → Target Voice Layer
→ Phase 1: Style Merge
→ Style guide updated
```

### Writing Only (Using Existing Patterns)

```
Provide your research materials:
• CSV data files: [list with descriptions]
• Figures: [list with descriptions and key findings]
• Results context: [.md files with analysis summaries]
• Key findings: [summary with statistics]
• Target journal: [optional]
• Reporting guideline: [optional]

→ Phase -1: Collection selection (for real-time RAG)
→ Phase 2: Intent Interview → Interactive Outline → Prose + RAG
→ Phase 3: Results Reviewer checks quality (7 passes)
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
- `##` for main Results heading
- `###` for subsections
- Figure references: `(Figure X)`, `(Table Y)`
- Statistics inline: `(p < 0.05)`, `(n = 100)`
- Professional academic tone matching Target Voice Layer

---

## Quality Checklist

Before final approval:
- [ ] Collection selection completed (Phase -1)
- [ ] Structure Layer populated from reference papers
- [ ] Target Voice Layer populated from target voice papers
- [ ] Writing intent interview completed (Q1-Q5)
- [ ] Outline approved by user (Steps 2a-2d)
- [ ] Structure matches learned patterns
- [ ] Voice matches Target Voice Layer profile
- [ ] All figures/tables referenced
- [ ] Statistics complete (values, tests, p-values, n)
- [ ] Transitions connect subsections
- [ ] No interpretation beyond data
- [ ] Consistent terminology
- [ ] Appropriate voice and tense
- [ ] Reporting guideline items addressed (if applicable)
- [ ] Word count within journal limits (if specified)
- [ ] Revision diff items resolved

---

## Continuous Improvement

The system improves over time:

1. **More reference papers in RAG** → Better pattern diversity
2. **More approved outputs** → Refined style guide (both layers)
3. **Reviewer feedback** → Eliminated common errors
4. **Pattern versioning** → Trackable improvement
5. **Reporting compliance tracking** → Fewer guideline gaps over time
6. **RAG query refinement** → Better retrieval quality
7. **Voice accuracy tracking** → More faithful style emulation

Check improvement metrics in `data/feedback-log.md`.

---

## Files

- `SKILL.md` — This orchestrator
- `agents/style-extractor.md` — Target Voice extraction agent (RAG-based)
- `agents/results-analyzer.md` — Structure pattern extraction agent (RAG-based)
- `agents/results-writer.md` — Multi-stage content creation agent (Interview → Outline → Prose + RAG)
- `agents/results-reviewer.md` — 7-pass quality review agent
- `agents/pattern-learner.md` — Continuous learning agent (both layers)
- `agents/paper-preprocessor.md` — Fallback PDF preprocessing agent
- `references/results-patterns.md` — Reference paper patterns (A, B, C, D)
- `data/style-guide.md` — Living style guide (Dual-Layer: Structure + Target Voice + Priority Rules)
- `data/feedback-log.md` — Feedback history
