# Academic Writing Style Guide

Living document updated by Pattern Learner based on approved outputs and feedback.

**Version**: 4.0
**Last Updated**: 2026-04-01
**Approved Sections Processed**: 0
**Architecture**: Dual-Layer (Structure Layer + Target Voice Layer)

---

## Structure Layer (from reference papers)

*Populated by Section Analyzer from the Structure Layer RAG collection (default: agentpaper).*

---

### Introduction Patterns

#### Bioinformatics Tool/Framework Papers

```
Introduction
├── Field Context (1-2 paragraphs)
│   └── Broad importance of the field (single-cell analysis, spatial genomics, etc.)
│       Present-tense framing: "X has become essential for..."
├── Specific Problem (1 paragraph)
│   └── Particular challenge being addressed
│       "Despite progress, X remains challenging because..."
├── Existing Approaches & Limitations (1-2 paragraphs)
│   └── What has been tried, with citations
│       Structured critique: Method A does X but lacks Y; Method B addresses Y but not Z
├── Gap Statement (1-2 sentences)
│   └── "However, no existing method simultaneously achieves..."
│       "A key challenge that remains unresolved is..."
├── Contribution Statement (1 paragraph)
│   └── "Here, we introduce [ToolName], a framework that..."
│       "In this work, we present..."
│       Lists 2-4 concrete contributions
└── Paper Roadmap (optional, 1-2 sentences)
    └── "In the following sections, we demonstrate..."
```

**Key Characteristics**:
- Classic funnel: broad context → specific problem → existing approaches → gap → contribution
- Gap statement is the pivot point — must be explicit and specific
- Contribution paragraph is concrete, not vague ("we present a tool" insufficient)
- Citations heavy in Existing Approaches section (5-15 references)
- Present tense for field state; past for specific studies cited

#### AI/ML Method Papers

```
Introduction
├── Application Domain Context (1 paragraph)
│   └── Problem domain and why it matters for AI/ML
├── Prior Methods Survey (1-2 paragraphs)
│   └── Categorized review of existing approaches
│       "Supervised methods... / Unsupervised methods... / Hybrid approaches..."
├── Fundamental Limitations (1 paragraph)
│   └── Why existing methods fail on key properties
│       Computational cost, generalizability, data requirements
├── Gap Statement (2-3 sentences)
│   └── Specific technical gap framed in ML terms
│       "No existing model jointly optimizes X and Y"
├── Proposed Method Summary (1 paragraph)
│   └── High-level architectural innovation
│       "We propose [Name], a [architecture type] that..."
│       Key technical novelty stated clearly
└── Contributions List (bulleted or numbered)
    └── 3-5 concrete, verifiable contributions
        "We introduce...", "We demonstrate...", "We release..."
```

**Key Characteristics**:
- Technical depth in gap statement (not just "it's hard")
- Contributions are enumerable and testable
- Architecture briefly named and framed
- Benchmark context set early (which tasks, which datasets)

#### Computational Analysis Papers

```
Introduction
├── Biological/Scientific Motivation (1-2 paragraphs)
│   └── Why this analysis matters scientifically
├── Data Landscape (1 paragraph)
│   └── Available data types, scales, resources
├── Analytical Challenges (1 paragraph)
│   └── Statistical/computational challenges unique to this domain
├── Prior Analyses & Gaps (1 paragraph)
│   └── What has been analyzed, what questions remain open
├── This Study (1 paragraph)
│   └── "To address this, we performed a comprehensive analysis of..."
│       Scope, data, approach
└── Key Questions (optional)
    └── 2-3 explicit research questions this work answers
```

**Key Characteristics**:
- Data-driven framing ("we analyzed N samples from X datasets")
- Research questions stated as questions or hypotheses
- Less emphasis on novelty, more on scope and rigor
- Regulatory/clinical context included if relevant

#### Multi-omics Integration Papers

```
Introduction
├── Systems Biology Motivation (1 paragraph)
│   └── Why single-omics is insufficient; need for integration
├── Available Omics Layers (1 paragraph)
│   └── Genomics, transcriptomics, proteomics, metabolomics, etc.
│       What each reveals; what integration adds
├── Integration Challenges (1 paragraph)
│   └── Technical challenges: batch effects, missing data, scale differences
├── Existing Integration Methods (1-2 paragraphs)
│   └── MOFA, DIABLO, Seurat WNN, etc. — with limitations
├── Gap Statement (1-2 sentences)
│   └── "Current methods do not adequately handle..."
├── Study Overview (1 paragraph)
│   └── What cohort/data, which omics layers, which integration strategy
└── Scope of Analysis (optional)
    └── Disease context, clinical relevance, discovery aims
```

**Key Characteristics**:
- Justify integration explicitly (not just "multi-omics is trendy")
- Clinical/biological relevance clear from first paragraph
- Integration method named and rationale given

---

### Methods Patterns

#### Computational Pipeline Papers

```
Methods
├── Algorithm/System Overview (1 paragraph + optional figure ref)
│   └── High-level description; reference to pipeline figure
│       "Our pipeline consists of four main stages: ..."
├── Data & Preprocessing
│   ├── Data sources and versions (with URLs/accession numbers)
│   ├── Filtering and QC criteria (explicit thresholds)
│   └── Normalization approach and rationale
├── Core Analysis Steps (one subsection per major step)
│   ├── Step A: [Name] — algorithm description + key parameters
│   ├── Step B: [Name] — algorithm description + key parameters
│   └── Step N: [Name] — algorithm description + key parameters
├── Evaluation Strategy
│   ├── Benchmark datasets (source, size, selection rationale)
│   ├── Baseline methods (names, versions, parameter settings)
│   └── Evaluation metrics (definition, computation)
└── Implementation & Availability
    └── Software, language, version, hardware, runtime, repository URL
```

**Key Characteristics**:
- Past tense, passive voice preferred for procedures
- Every parameter either stated explicitly or cited (not "default settings")
- Subsection order matches Results presentation order
- Software versions are mandatory: "Python 3.11.4", "Seurat v5.0.2"

#### Benchmark/Evaluation Papers

```
Methods
├── Benchmark Design Rationale (1 paragraph)
│   └── Why these tasks, datasets, and metrics were chosen
├── Datasets
│   ├── Dataset A: source, size, characteristics, access
│   └── Dataset B: source, size, characteristics, access
├── Methods Under Evaluation
│   └── Each method: version, parameter settings, any modifications
├── Evaluation Protocol
│   ├── Train/test/validation splits or cross-validation scheme
│   ├── Metrics: definition and justification
│   └── Statistical testing framework
└── Implementation Details
    └── Hardware, compute time, reproducibility notes
```

**Key Characteristics**:
- Justify dataset selection (not just "we used common benchmarks")
- Parameter settings for all methods (enable reproducibility)
- Statistical test for performance comparisons stated explicitly

#### Statistical Analysis Methods

```
Methods
├── Study Design Overview
│   └── Observational, experimental, longitudinal, etc.
├── Cohort/Data Description
│   └── N, demographics, inclusion/exclusion criteria
├── Statistical Methods (one subsection per analysis type)
│   ├── Primary analysis: test, correction, threshold
│   ├── Secondary analysis: test, correction, threshold
│   └── Sensitivity analyses
├── Software & Packages
│   └── R version, packages, statistical library versions
└── Data Availability
    └── Repository, accession numbers, access instructions
```

**Key Characteristics**:
- Reporting guideline compliance (STROBE, CONSORT) stated explicitly
- Multiple testing correction described for every multiple comparison
- Effect size measures defined, not just p-values

#### Multi-omics Integration Methods

```
Methods
├── Cohort & Sample Processing
│   └── Recruitment, ethics, consent, processing protocols
├── Omics Data Generation (per layer)
│   ├── Genomics: platform, coverage, calling pipeline
│   ├── Transcriptomics: platform, depth, alignment, quantification
│   └── [Other layers as applicable]
├── Quality Control (per layer)
│   └── Filtering criteria, batch correction
├── Integration Strategy
│   └── Method chosen, rationale, hyperparameters
├── Downstream Analyses
│   └── Pathway analysis, clustering, survival analysis, etc.
└── Statistical Framework
    └── Multiple testing correction, significance thresholds
```

---

### Results Patterns

#### Bioinformatics Tool/Framework Papers

```
Results
├── Framework Overview (with architecture figure)
│   └── System components, workflow, key innovations
├── Task 1: [Primary Capability]
│   ├── Problem context (1 sentence)
│   ├── Evaluation setup (datasets, baselines)
│   ├── Quantitative results (table/figure reference)
│   └── Key finding highlight
├── Task 2: [Secondary Capability]
│   └── [Same structure as Task 1]
├── Task 3: [Additional Capability]
│   └── [Same structure as Task 1]
└── Ablation/Case Study
    └── Component analysis or real-world application
```

#### AI/ML Method Papers

```
Results
├── Overview of [Method] Workflow
│   └── System description with architecture figure
├── Benchmark Performance
│   └── Dataset, baselines, quantitative metrics, statistical tests
├── Evaluation Across Multiple Metrics
│   └── Per-metric analysis, cross-metric summary
├── Self-Verification / Reliability Analysis
│   └── Model confidence, error analysis
└── Real-World Application / Case Study
    └── Application context, biological interpretation
```

#### Computational Analysis Papers

```
Results
├── Data Overview
│   └── Dataset characteristics, quality metrics
├── Primary Analysis
│   ├── Main findings with statistics
│   ├── Figure integration
│   └── Interpretation preview
├── Comparative Analysis
│   └── Cross-group/condition comparisons
├── Validation
│   └── Independent validation or cross-validation
└── Biological Interpretation
    └── Pathway/enrichment analysis findings
```

#### Multi-omics Integration Papers

```
Results
├── Cohort & Data Generation
│   └── Sample overview, omics layers, QC
├── Single-Omics Characterization
│   └── Per-layer key findings
├── Multi-Omics Integration
│   └── Joint analysis, cross-layer correlations
├── Subtype Characterization
│   └── Clinical correlates, outcome analysis
└── Regulatory / Functional Insights
    └── Driver genes, networks, validation
```

### Figure Integration

#### Per-Section Figure Rules

**Introduction**:
- Conceptual overview / graphical abstract (Figure 1 typically)
- "Figure 1 illustrates the conceptual framework underlying..."
- Placed at end of contribution statement paragraph

**Methods**:
- Pipeline/architecture diagrams referenced in overview subsection
- "As shown in Figure [N], the pipeline consists of..."
- Algorithm pseudocode referenced by algorithm number

**Results**:
- Reference within 2 sentences of relevant finding
- Describe figure content before stating conclusion
- Use panel letters (A, B, C) for multi-panel figures

**Discussion**:
- Summary/model figures rare; use only if they add synthesis value
- "As summarized in Figure [N], our findings suggest a model in which..."

#### First Reference Patterns (Results)
- `"...(Figure 1A)."`
- `"As shown in Figure 2, ..."`
- `"Figure 3 illustrates the comparison between..."`

---

### Discussion Patterns

#### Standard Discussion

```
Discussion
├── Opening Summary (1-2 sentences)
│   └── "In this study, we demonstrated that..."
│       No new data; crisp restatement of main finding
├── Interpretation of Key Findings (1-2 paragraphs)
│   └── What the findings mean in biological/scientific context
│       "This suggests that..." / "This is consistent with..."
│       Each interpretation traceable to a specific Result
├── Comparison with Prior Work (1-2 paragraphs)
│   └── How findings extend, confirm, or contradict existing literature
│       "Consistent with [Author, Year]..." / "In contrast to [Author, Year]..."
│       New citations acceptable here
├── Limitations (1 paragraph)
│   └── Explicit, specific, and honest
│       "A limitation of this study is..." / "We note that our analysis..."
│       Methodological, data, and generalizability limitations
├── Future Directions (1 paragraph)
│   └── Specific next steps enabled by this work
│       "Future studies could extend this approach to..."
└── Broader Impact / Conclusion (1-2 sentences)
    └── Significance for the field
        "Taken together, these findings provide..."
```

**Key Characteristics**:
- Inverted funnel: specific findings → broader significance (mirror of Introduction)
- Hedging language appropriate: "suggests", "may indicate", "is consistent with"
- No new data; every claim traces to Results
- Limitations section is substantive, not perfunctory
- Conclusion returns to the broad context established in Introduction

#### Combined Results-Discussion

```
Results and Discussion
├── [Subsection A]
│   ├── Findings: data-backed statements (past tense, no interpretation)
│   └── Discussion: immediate interpretation (present tense, hedged)
├── [Subsection B]
│   └── [Same structure as A]
└── Conclusion
    └── Synthesis statement tying all subsections together
```

**Key Characteristics**:
- Common in Genome Biology, some PLOS journals
- Interpretation immediately follows each finding — no separate Discussion
- Requires careful tense discipline within each subsection
- Conclusion paragraph replaces standalone Discussion conclusion

---

### Field-Specific Conventions

#### Single-Cell Genomics
- Report number of cells (N = X cells) and number of samples
- Include clustering metrics (ARI, NMI, silhouette score)
- Reference UMAP/t-SNE for visualization
- Report gene/feature filtering criteria
- Mention doublet detection and removal rate
- State resolution parameter for clustering algorithms

#### Machine Learning/AI Methods
- Report accuracy, F1, AUC as appropriate for task type
- Include baseline comparisons with established methods
- Mention computational resources (GPU type, training time) if relevant
- Report cross-validation strategy and number of folds
- Include ablation studies for architecture components

#### Pathway Analysis
- Report FDR-adjusted p-values (never raw p-values alone)
- Include number of genes/terms tested
- Reference enrichment method (GSEA, ORA, DAVID, etc.)
- Distinguish between GO terms (BP, MF, CC) and pathway databases (KEGG, Reactome)
- Report background gene set used

#### Spatial Transcriptomics
- Report number of spots/cells and tissue sections analyzed
- Include spatial resolution (e.g., 55 μm for Visium, subcellular for MERFISH)
- Describe spatial domain identification method and number of domains
- Report ligand-receptor interaction analysis with tools used (CellChat, COMMOT, etc.)
- Reference tissue morphology from H&E images
- Include Moran's I or similar for spatial autocorrelation

#### Metagenomics / Microbiome
- Report alpha diversity metrics (Shannon, Simpson, Chao1)
- Report beta diversity with PERMANOVA/ANOSIM test results
- Include taxonomic level of analysis (phylum, genus, species)
- Report differential abundance with tools used (DESeq2, ALDEx2, ANCOM-BC)
- Include sample sizes per group with sequencing depth statistics
- Report rarefaction or normalization approach

#### Structural Biology / Protein Analysis
- Report resolution metrics for predicted structures (pLDDT, TM-score)
- Include RMSD for structural comparisons
- Reference AlphaFold/ESMFold confidence scores where applicable
- Report binding affinity predictions with confidence intervals
- Include structural alignment metrics against experimental structures
- Reference PDB IDs for comparative structures

---

## Target Voice Layer (from target voice papers)

*Populated by Style Extractor from the Target Voice RAG collection (default: mypaper). This section captures the writing voice, tone, and stylistic fingerprint to emulate.*

*Note: Style Extractor now tags extracted patterns with `section_type` so voice profiles can be maintained per section.*

### Sentence Patterns
*Not yet extracted — will be populated when Style Extractor runs against Target Voice collection.*

- **Average length**: [pending]
- **Preferred openings**: [pending]
- **Voice ratio**: Active [N]% / Passive [N]%
- **Examples**: [pending]

### Transition Patterns
*Not yet extracted.*

- **Between sections**: [pending]
- **Within sections**: [pending]
- **Characteristic expressions**: [pending]

### Statistical Reporting Style
*Not yet extracted.*

- **p-value format**: [pending]
- **Effect size**: [pending]
- **CI format**: [pending]
- **Placement**: [pending]

### Logic Flow
*Not yet extracted.*

- **Presentation order**: [pending]
- **Figure reference style**: [pending]
- **Paragraph density**: [pending]

### Tone Profile
*Not yet extracted.*

- **Hedging**: [pending]
- **Jargon density**: [pending]
- **"We" usage**: [pending]
- **Formality**: [pending]
- **Assertiveness ratio**: [pending]

---

## Priority Rules

When Structure Layer and Target Voice Layer provide conflicting guidance, use these priority rules:

```yaml
priority_rules:
  structure:
    source: "Structure Layer"
    rationale: "Section organization follows field conventions"

  voice_and_tone:
    source: "Target Voice Layer"
    rationale: "Writing voice should match target author/style"

  statistics:
    source: "Target Voice Layer"
    rationale: "Statistical reporting format follows target voice"

  transitions:
    source: "Target Voice Layer"
    rationale: "Transition style is part of writing voice"

  figure_refs:
    source: "Structure Layer"
    overlay: "Target Voice Layer"
    rationale: "Figure placement follows structure; reference style follows voice"

  terminology:
    source: "Target Voice Layer"
    rationale: "Term choices reflect author's voice"

  paragraph_structure:
    source: "Structure Layer"
    overlay: "Target Voice Layer"
    rationale: "Paragraph organization follows structure; sentence flow follows voice"
```

---

## Section-Specific Writing Rules

Reference table for tense, voice, interpretation, and citation rules per IMRAD section.

### Introduction

```yaml
introduction_rules:
  tense:
    field_state: "Present — 'Single-cell sequencing has revolutionized...'"
    specific_studies: "Past — 'Smith et al. demonstrated...'"
    contribution_statement: "Present — 'Here, we introduce...'"
  voice:
    preferred: "Active for contribution statement; mixed for background"
    passive_ok: "Describing prior work passively is acceptable"
  interpretation:
    allowed: "Yes — contextual framing only; no results interpretation"
    constraint: "Do not pre-interpret your own Results in Introduction"
  citations:
    density: "Heavy — every factual claim about prior work needs citation"
    placement: "Inline parenthetical: '(Author et al., Year)'"
  key_constraint: "Gap must be stated explicitly; contribution must match actual Results"
```

### Methods

```yaml
methods_rules:
  tense:
    procedures: "Past — 'Reads were aligned using...', 'We performed...'"
    algorithm_description: "Present — 'The algorithm assigns each cell to...'"
    design_rationale: "Past — 'We chose X because...'"
  voice:
    preferred: "Passive for procedures — 'Cells were filtered if...'"
    active_ok: "Design decisions and choices — 'We selected Leiden clustering because...'"
  interpretation:
    allowed: "No — no results, no interpretation in Methods"
    constraint: "Evaluation metrics may be defined but not discussed here"
  citations:
    density: "Moderate — cite tools and methods, not concepts"
    placement: "After tool name: 'Seurat (v5.0.2; Stuart et al., 2019)'"
  key_constraint: "Sufficient detail for reproduction; every parameter stated"
```

### Results

```yaml
results_rules:
  tense:
    analyses: "Past — 'We identified 12 clusters...' / 'Analysis revealed...'"
    figure_descriptions: "Present — 'Figure 2A shows...', 'The heatmap displays...'"
    established_facts: "Present — 'UMAP visualizes...'"
  voice:
    preferred: "Active for findings — 'We found...', 'The model achieved...'"
    passive_ok: "Data processing steps — 'Cells were excluded if...'"
  interpretation:
    allowed: "No — report data-backed claims only; save interpretation for Discussion"
    exception: "Very brief 'suggesting' clause acceptable if hedged"
  citations:
    density: "Minimal — cite methods used only if not described in Methods"
  key_constraint: "Every claim must be backed by data shown; no interpretation"
```

### Discussion

```yaml
discussion_rules:
  tense:
    interpretations: "Present — 'These findings suggest that...', 'This indicates...'"
    result_recaps: "Past — 'We showed that...', 'Our analysis revealed...'"
    literature_comparison: "Present or past depending on claim type"
  voice:
    preferred: "Active — 'We suggest...', 'Our findings indicate...'"
    passive_ok: "When attributing findings to data: 'This was observed in...'"
  interpretation:
    required: "Yes — this is the purpose of Discussion"
    constraint: "Each interpretation traceable to specific Result; hedging required"
    hedging_words: "suggests, may indicate, is consistent with, appears to, could reflect"
  citations:
    density: "Heavy — compare to 5-15 relevant papers"
    placement: "After each interpretive or comparative claim"
  key_constraint: "No new data; acknowledge limitations explicitly; avoid over-claiming"
```

---

## Statistical Reporting

### Standard Format
```
[Finding] ([metric] = [value], [test], p [operator] [value])
```

### Examples
- `(accuracy = 0.89, 95% CI: 0.85-0.93)`
- `(fold-change = 2.3, t-test, p < 0.001)`
- `(ARI = 0.76 ± 0.05, n = 50 datasets)`
- `(FDR-adjusted p = 3.2 × 10⁻⁵, Benjamini-Hochberg)`
- `(HR = 2.1, 95% CI: 1.4-3.2, log-rank p = 0.003)`

### Required Elements
1. Metric name and value
2. Error measure (SD, SEM, or CI)
3. Sample size
4. Statistical test (for comparisons)
5. P-value (for significance claims)
6. Multiple testing correction method (when applicable)
7. Effect size (for group comparisons)

*Note: When Target Voice Layer is populated, its statistical reporting style takes precedence over these defaults.*

---

## Transitions

### Introduction Transitions

**Between paragraphs (narrowing funnel)**:
- "Despite these advances, a key challenge remains..."
- "However, existing approaches suffer from..."
- "While [Method A] addresses X, it does not account for Y..."
- "To bridge this gap, we developed..."
- "Building on these insights, we hypothesized that..."

**Gap-to-contribution pivot**:
- "Here, we introduce [Name], which..."
- "To address this limitation, we present..."
- "In response to this unmet need, we developed..."

### Methods Transitions

**Between subsections**:
- "Having defined the preprocessing pipeline, we next describe..."
- "Building on the output of [Step A], [Step B] takes as input..."
- "To evaluate the performance of [method], we..."
- "The resulting [output] was then passed to..."

**Rationale connectors**:
- "We chose [method] because..."
- "This approach was preferred over [alternative] due to..."
- "To ensure reproducibility, we..."

### Results Transitions

**Between subsections**:
- "Having established [X], we next examined..."
- "Building on the [previous] analysis, we evaluated..."
- "To further validate these findings, we..."
- "Given the strong performance in [X], we investigated..."
- "We next sought to determine whether [finding] extended to..."

**Within subsections**:
- "Consistent with these findings, ..."
- "In contrast, ..."
- "Notably, ..."
- "These results indicate that..."
- "Taken together, these observations indicate..."

### Discussion Transitions

**Opening recap**:
- "In this study, we demonstrated that..."
- "The central finding of this work is that..."
- "Our results reveal that..."

**Comparison to literature**:
- "These findings are consistent with previous reports showing..."
- "In contrast to [Author, Year], who found..."
- "Our results extend the work of [Author, Year] by..."
- "This is in line with the emerging consensus that..."

**Limitation acknowledgment**:
- "A limitation of this study is..."
- "We acknowledge that our analysis is limited by..."
- "Future work should address the constraint that..."

**Future directions**:
- "These findings open the possibility of..."
- "Future studies could investigate whether..."
- "An important next step is to determine..."

**Conclusion**:
- "Taken together, these findings provide evidence that..."
- "In summary, our work establishes..."
- "Overall, this study demonstrates..."

*Note: When Target Voice Layer is populated, its transition patterns take precedence over these defaults.*

---

## Voice & Tense

### Per-Section Guidance

| Rule | Introduction | Methods | Results | Discussion |
|------|-------------|---------|---------|------------|
| Primary tense | Present (field), Past (studies) | Past | Past (analyses), Present (figures) | Present (interpretations), Past (recaps) |
| Voice preference | Active preferred, mixed OK | Passive preferred | Active for findings | Active preferred |
| Interpretation | Contextual framing only | Not allowed | Not allowed | Required |
| Citations | Heavy | Moderate (tools) | Minimal | Heavy |
| Key constraint | Clear gap + contribution | Reproducible detail | Data-backed claims | Limitations acknowledged |

### Tense Usage (General)
- **Past tense**: Completed analyses, methods descriptions
- **Present tense**: Established facts, figure descriptions, general truths, interpretations

### Voice (General)
- **Passive**: Methods and procedural descriptions
- **Active**: Results and findings (preferred for clarity)
- **"We"**: Acceptable if journal style permits

*Note: When Target Voice Layer is populated, its voice profile takes precedence over these defaults.*

---

## Reporting Guidelines Reference

Quick-reference for section-specific requirements by study type.

### STROBE (Observational Studies)

**Applies to**: Cohort, case-control, cross-sectional studies

Results checklist items:
- **Participants**: Report numbers at each stage of study. Give reasons for non-participation.
- **Descriptive data**: Characteristics of participants. Indicate missing data.
- **Outcome data**: Report numbers of outcome events or summary measures over time.
- **Main results**: Unadjusted and confounder-adjusted estimates with precision (95% CI).
- **Other analyses**: Subgroup, interaction, sensitivity analyses as planned.

### CONSORT (Randomized Controlled Trials)

**Applies to**: RCTs, cluster-randomized, crossover trials

Results checklist items:
- **Participant flow**: Numbers assigned, treated, completing protocol, analysed. Include flow diagram.
- **Baseline data**: Table of baseline demographic and clinical characteristics for each group.
- **Numbers analysed**: For each group; intention-to-treat noted. Deviations from plan noted.
- **Outcomes and estimation**: Effect size with precision (95% CI) for each outcome.
- **Ancillary analyses**: Subgroup and adjusted analyses; pre-specified vs. exploratory distinguished.
- **Harms**: All important harms or unintended effects in each group.

### PRISMA (Systematic Reviews / Meta-analyses)

**Applies to**: Systematic reviews, scoping reviews, meta-analyses

Results checklist items:
- **Study selection**: Numbers screened, assessed, included; reasons for exclusion (flow diagram).
- **Study characteristics**: Summary table of each included study.
- **Risk of bias**: Results within and across studies.
- **Synthesis results**: Summary, confidence intervals, heterogeneity (I², τ²).
- **Reporting biases**: Publication/reporting bias assessment.

### ARRIVE (Animal Studies)

**Applies to**: In vivo animal experiment reporting

Results checklist items:
- **Baseline data**: Characteristics of animals in each experimental group.
- **Numbers analysed**: Exact sample sizes; any exclusions.
- **Outcomes and estimation**: Effect size and confidence interval.
- **Adverse events**: Details in each group.

### MIAME/MINSEQE (Omics Experiments)

**Applies to**: Microarray, RNA-seq, and other -omics studies

Results checklist items:
- **Experimental design**: Sample annotation, conditions, replicates.
- **Data processing**: Normalization method and rationale.
- **Quality control**: QC metrics and filtering criteria.
- **Differential analysis**: Method, thresholds, multiple testing correction.

### When No Specific Guideline Applies

General best practices for computational biology:
- State sample sizes for every analysis
- Name all statistical tests used
- Report effect sizes, not just p-values
- Include multiple testing correction when testing many hypotheses
- Define all thresholds and cutoffs used

---

## Journal-Specific Conventions

### Nature Methods
- **Word limit**: ~2,000-3,000 words for Results (in Article format)
- **Figures**: 6-8 main figures; supplementary for supporting data
- **Style**: Concise, figure-driven narrative; emphasize novelty of method
- **Statistics**: Report exact p-values where possible; include effect sizes
- **Convention**: Brief methods references in Results are acceptable
- **Tone**: Active voice preferred; technical but accessible

### Bioinformatics
- **Word limit**: ~2,000-4,000 words (application notes are much shorter)
- **Figures**: Varies by article type; application notes typically 1-2 figures
- **Style**: Technical precision; benchmark comparisons are essential
- **Statistics**: Comprehensive statistical tables; cross-validation expected
- **Convention**: Supplementary tables for full benchmark results
- **Tone**: Objective, data-heavy

### Genome Biology
- **Word limit**: No strict limit; thoroughness valued
- **Figures**: No strict limit; complex multi-panel figures common
- **Style**: Comprehensive biological context; validation expected
- **Statistics**: Full reporting including multiple testing corrections
- **Convention**: Often includes "Results and Discussion" combined section
- **Tone**: Biology-forward; connect computational findings to biological meaning

### Nucleic Acids Research
- **Word limit**: Varies by category (Breakthrough articles, Original papers, Web server issues)
- **Figures**: Varies; Web Server papers typically 3-5 figures
- **Style**: Methods-heavy Results; database/server descriptions common
- **Statistics**: Benchmark comparisons; user study results if applicable
- **Convention**: Web server papers focus on utility and interface description
- **Tone**: Technical, tool/resource-oriented

### Cell Systems / Cell Reports Methods
- **Word limit**: ~3,000-5,000 words for Results
- **Figures**: 4-7 main figures; STAR Methods separate
- **Style**: Systems biology perspective; multi-scale analysis
- **Statistics**: Full statistical reporting; computational reproducibility
- **Convention**: Graphical abstract expected; Key Resources Table
- **Tone**: Integrative; emphasize systems-level insights

### PLOS Computational Biology
- **Word limit**: No strict limit
- **Figures**: No strict limit; multi-panel figures common
- **Style**: Detailed methodology in Results acceptable; code availability important
- **Statistics**: Complete statistical reporting; reproducibility emphasized
- **Convention**: Supporting Information for extended results
- **Tone**: Educational; make computational methods accessible

---

## Learned Patterns

*This section is updated automatically by the Pattern Learner agent. All patterns are tagged with `section_type`.*

### High-Confidence Patterns

*None yet — will be populated after approved sections are processed.*

```yaml
# Pattern entry format:
# - id: "pattern-[section]-[NNN]"
#   section_type: "introduction|methods|results|discussion"
#   layer: "Structure Layer|Target Voice Layer"
#   description: "[what the pattern is]"
#   example: "[concrete example]"
#   paper_types: ["bioinformatics_tool", "ai_ml", "computational_analysis", "multi_omics"]
#   frequency: [n]  # times observed across approved sections
#   confidence: "high"
#   added_version: "4.x"
```

### Moderate-Confidence Patterns

*None yet.*

### Deprecated Patterns

*None yet.*
