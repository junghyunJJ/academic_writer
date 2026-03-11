# Academic Results Style Guide

Living document updated by Pattern Learner based on approved outputs and feedback.

**Version**: 3.0
**Last Updated**: 2026-03-11
**Approved Sections Processed**: 0
**Architecture**: Dual-Layer (Structure Layer + Target Voice Layer)

---

## Structure Layer (from reference papers)

*Populated by Results Analyzer from the Structure Layer RAG collection (default: agentpaper).*

### Structure Patterns

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

#### First Reference Patterns
- `"...(Figure 1A)."`
- `"As shown in Figure 2, ..."`
- `"Figure 3 illustrates the comparison between..."`

#### Rules
1. Reference within 2 sentences of relevant finding
2. Describe figure content before stating conclusion
3. Use panel letters (A, B, C) for multi-panel figures

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

### Between Sections
- "Having established [X], we next examined..."
- "Building on the [previous] analysis, we evaluated..."
- "To further validate these findings, we..."
- "Given the strong performance in [X], we investigated..."
- "We next sought to determine whether [finding] extended to..."

### Within Sections
- "Consistent with these findings, ..."
- "In contrast, ..."
- "Notably, ..."
- "These results indicate that..."
- "Taken together, these observations indicate..."

*Note: When Target Voice Layer is populated, its transition patterns take precedence over these defaults.*

---

## Voice & Tense

### Tense Usage
- **Past tense**: Completed analyses, methods descriptions
- **Present tense**: Established facts, figure descriptions, general truths

### Voice
- **Passive**: Methods and procedural descriptions
- **Active**: Results and findings (preferred for clarity)
- **"We"**: Acceptable if journal style permits

*Note: When Target Voice Layer is populated, its voice profile takes precedence over these defaults.*

---

## Reporting Guidelines Reference

Quick-reference for Results-section-specific requirements by study type.

### STROBE (Observational Studies)

**Applies to**: Cohort, case-control, cross-sectional studies

Results checklist items:
- **Participants**: Report numbers at each stage of study (e.g., potentially eligible, examined, confirmed eligible, included in analyses). Give reasons for non-participation at each stage.
- **Descriptive data**: Characteristics of participants (demographics, clinical, social). Indicate number of participants with missing data for each variable.
- **Outcome data**: Report numbers of outcome events or summary measures over time.
- **Main results**: Give unadjusted estimates and, if applicable, confounder-adjusted estimates with precision (e.g., 95% CI). If relevant, consider translating relative risk into absolute risk.
- **Other analyses**: Report other analyses (subgroup, interaction, sensitivity) as planned.

### CONSORT (Randomized Controlled Trials)

**Applies to**: RCTs, cluster-randomized, crossover trials

Results checklist items:
- **Participant flow**: For each group, numbers randomly assigned, receiving intended treatment, completing protocol, analysed. Include flow diagram.
- **Baseline data**: Table of baseline demographic and clinical characteristics for each group.
- **Numbers analysed**: For each group, number included in each analysis and whether intention-to-treat. Note deviations from planned analysis.
- **Outcomes and estimation**: For each primary and secondary outcome, results for each group and estimated effect size with precision (95% CI).
- **Ancillary analyses**: Results of subgroup analyses and adjusted analyses, distinguishing pre-specified from exploratory.
- **Harms**: All important harms or unintended effects in each group.

### PRISMA (Systematic Reviews / Meta-analyses)

**Applies to**: Systematic reviews, scoping reviews, meta-analyses

Results checklist items:
- **Study selection**: Numbers of studies screened, assessed for eligibility, and included; reasons for exclusion at each stage (flow diagram).
- **Study characteristics**: Summary table of each included study.
- **Risk of bias**: Results of risk of bias assessment within and across studies.
- **Synthesis results**: For each synthesis, summary of results, confidence intervals, heterogeneity measures (I², τ²).
- **Reporting biases**: Results of any assessment of publication/reporting bias.

### ARRIVE (Animal Studies)

**Applies to**: In vivo animal experiment reporting

Results checklist items:
- **Baseline data**: Characteristics of animals in each experimental group.
- **Numbers analysed**: Exact sample sizes used for statistical analysis, including any exclusions.
- **Outcomes and estimation**: Results for each outcome with effect size and confidence interval.
- **Adverse events**: Details of important adverse events in each group.

### MIAME/MINSEQE (Omics Experiments)

**Applies to**: Microarray, RNA-seq, and other -omics studies

Results checklist items:
- **Experimental design**: Sample annotation, conditions, replicates.
- **Data processing**: Normalization method and rationale.
- **Quality control**: QC metrics and filtering criteria with impact on sample/feature counts.
- **Differential analysis**: Method, thresholds, and multiple testing correction.

### When No Specific Guideline Applies

General best practices for computational biology Results:
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

*This section is updated automatically by the Pattern Learner agent.*

### High-Confidence Patterns
*None yet - will be populated after approved sections are processed.*

### Moderate-Confidence Patterns
*None yet.*

### Deprecated Patterns
*None yet.*
