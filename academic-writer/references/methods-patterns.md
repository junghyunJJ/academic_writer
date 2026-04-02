# Methods Section Patterns for Bioinformatics Papers

Structural patterns extracted from exemplar computational biology papers.
Methods sections require maximum reproducibility: every parameter, version, and decision must be documented.

## Table of Contents
1. [Pattern A: Computational Pipeline Methods](#pattern-a-computational-pipeline-methods)
2. [Pattern B: Benchmark/Evaluation Methods](#pattern-b-benchmarkevaluation-methods)
3. [Pattern C: Statistical Analysis Methods](#pattern-c-statistical-analysis-methods)
4. [Pattern D: Multi-omics Integration Methods](#pattern-d-multi-omics-integration-methods)
5. [Subsection Writing Templates](#subsection-writing-templates)
6. [Algorithm/Pseudocode Presentation Templates](#algorithmpseudocode-presentation-templates)
7. [Parameter Reporting Conventions](#parameter-reporting-conventions)
8. [Tool Citation Patterns](#tool-citation-patterns)
9. [Reproducibility Checklist](#reproducibility-checklist)
10. [Software and Data Availability Patterns](#software-and-data-availability-patterns)

---

## Pattern A: Computational Pipeline Methods

**Source**: CellVoyager-style papers (agent-based computational pipelines with iterative execution)

### Structure
```
Methods
├── System/Algorithm Overview
│   ├── High-level description of the pipeline
│   ├── Reference to Algorithm 1 / pseudocode
│   └── Design rationale for key architectural decisions
├── Preprocessing
│   ├── Input data format and requirements
│   ├── Data summarization / context extraction
│   └── Environment initialization (packages, kernel, dependencies)
├── Core Pipeline Steps (ordered)
│   ├── Step 1: Planning / Blueprint Generation
│   │   └── Prompt design, hypothesis generation, analysis plan
│   ├── Step 2: Self-Critique / Reflection
│   │   └── Plan validation, weakness identification, revision
│   ├── Step 3: Code Generation & Execution
│   │   └── Code synthesis, iterative error fixing (max F attempts)
│   ├── Step 4: Output Interpretation
│   │   └── Vision model for figures, text interpretation, result summarization
│   └── Step 5: Replanning / Iteration
│       └── Plan update based on results, next hypothesis selection
├── Evaluation Strategy
│   ├── Benchmark dataset description (e.g., CellBench: 76 papers, 659 analyses)
│   ├── Evaluation metrics and judging criteria
│   ├── Baseline methods for comparison
│   └── Statistical tests for significance
└── Implementation Details
    ├── Software versions (Python, packages)
    ├── Model checkpoints and API parameters
    ├── Hardware / runtime estimates
    └── Code and data availability
```

### Key Characteristics
- **Algorithm pseudocode**: formal algorithm block (Algorithm 1) with line-numbered steps
- **Prompt documentation**: references to supplementary figures showing exact prompts
- **Iterative design**: explicitly describes retry/fix loops and termination conditions
- **Modular description**: each pipeline component described as self-contained unit
- **Passive voice preferred**: "Data were preprocessed..." / "The agent was configured..."
- **Past tense throughout**: "We used..." / "The model was trained..."

### Example System Overview Paragraph
> "An overview of the implementation of [ToolName] is highlighted in Algorithm 1 and Figure 1. In the following sections, we go into more detail on each step of [ToolName]'s procedure and showcase the prompts used for those steps. Implementation of [ToolName] used Python (version [X.Y.Z]) and the following packages: [package1] (version [X.Y.Z]), [package2] (version [X.Y.Z]), and [package3] (version [X.Y.Z]). For the LLMs, we used checkpoint models [model1] and [model2]."

### Example Preprocessing Paragraph
> "The agent loads in the metadata of the [data type] data object and summarizes it by listing column names and the first [N] unique values from each column. Then, the agent loads in a [manuscript/report] about the [biological context] and, optionally, a set of analyses already performed on the dataset. This information is summarized using the prompt shown in Supplementary Figure [N] and is passed to the prompts as [variable_name]."

### Example Iterative Execution Paragraph
> "Before execution, [ToolName] first self-critiques the [blueprint/plan], identifying potential weaknesses and retrieving documentation from the [libraries/functions] referenced in the code to verify that they are being used appropriately. The [plan/blueprint] and code are then revised as needed by the agent, after which the code is appended to the [execution environment] for execution. If the code execution fails, the agent iteratively rewrites the code for up to F attempts; if errors persist, [ToolName] is told to revise its analytical trajectory. In this work, we set F = [N], as empirically, the [success metric] saturates after that mark (Supplementary Figure [N])."

---

## Pattern B: Benchmark/Evaluation Methods

**Source**: Papers introducing benchmarks or systematic method comparisons

### Structure
```
Methods
├── Benchmark Construction
│   ├── Data source and curation process
│   ├── Inclusion/exclusion criteria
│   ├── Ground truth definition
│   ├── Quality assurance (expert review, inter-rater agreement)
│   └── Benchmark statistics (size, diversity, characteristics)
├── Evaluation Protocol
│   ├── Evaluation metric(s) with formal definitions
│   ├── Scoring criteria (automated or human judge)
│   ├── Cross-validation or held-out strategy
│   └── Multiple evaluation runs (replicates)
├── Baseline Methods
│   ├── Method selection rationale
│   ├── Configuration for each baseline (hyperparameters, prompts)
│   ├── Fair comparison controls (same input, same compute)
│   └── Version information for all methods
├── Statistical Analysis
│   ├── Significance testing (paired t-tests, Wilcoxon, etc.)
│   ├── Multiple comparison correction (Bonferroni, Holm)
│   ├── Effect size reporting
│   └── Confidence intervals
└── Human Evaluation (if applicable)
    ├── Evaluator recruitment and qualifications
    ├── Evaluation rubric
    ├── Inter-rater agreement metrics
    └── Blinding procedures
```

### Key Characteristics
- **Benchmark justification**: why this benchmark is needed and how it fills a gap
- **Ground truth transparency**: exactly how ground truth labels were obtained
- **Fair comparison emphasis**: explicit controls for fair method comparison
- **Reproducibility focus**: every hyperparameter and configuration documented
- **Human evaluation rigor**: rubrics, inter-rater agreement, evaluator qualifications

### Example Benchmark Construction Paragraph
> "We searched for published papers that focused primarily on analyzing [data type] datasets. For [N] such papers, we used [extraction method] to extract the [relevant content] and each [analysis/evaluation item] performed in the paper using the prompt in Supplementary Figure [N]. A subset of the curated examples were reviewed by experts to check that the extracted information accurately reflects the content of the paper."

### Example Human Evaluation Paragraph
> "Each [case study / output] was evaluated by two independent [PhD-level / domain-expert] researchers, one of whom was a [coauthor / author] of the respective [case study paper / reference work], along with an [evaluation rubric] (Supplementary Figure [N]). For each [evaluation item], reviewers were asked to assess [criterion 1] (on a scale of [range]), whether [criterion 2] (yes, mostly, or no), whether [criterion 3] (yes, mostly, or no), and whether [criterion 4] (yes or no). Concordance rates between each rater and the [automated judge] were [X%] and [Y%], respectively, indicating that the [judge] is a reliable heuristic."

---

## Pattern C: Statistical Analysis Methods

**Source**: Computational analysis papers with complex statistical frameworks

### Structure
```
Methods
├── Study Design
│   ├── Research question formalization
│   ├── Cohort selection and sample sizes
│   ├── Inclusion/exclusion criteria
│   └── Confounders and covariates
├── Data Preprocessing
│   ├── Quality control filtering criteria (with thresholds)
│   ├── Normalization method and rationale
│   ├── Batch effect correction (method, variables)
│   └── Feature selection / dimensionality reduction
├── Primary Statistical Analysis
│   ├── Test selection rationale (parametric vs. non-parametric)
│   ├── Multiple testing correction (method, threshold)
│   ├── Effect size definitions and calculations
│   └── Model specification (covariates, random effects)
├── Secondary / Downstream Analysis
│   ├── Clustering (algorithm, parameters, k selection)
│   ├── Enrichment analysis (database, method, threshold)
│   ├── Network / pathway analysis
│   └── Survival analysis (if applicable)
└── Validation Strategy
    ├── Cross-validation scheme
    ├── Independent cohort description
    ├── Permutation / bootstrap testing
    └── Sensitivity analysis
```

### Key Characteristics
- **Threshold justification**: every cutoff (p-value, fold change, quality) is explicit
- **Method rationale**: explains WHY each test/method was chosen, not just what
- **Assumption checking**: notes when parametric assumptions were verified
- **Effect size emphasis**: reports effect sizes alongside p-values
- **Reproducibility**: all parameters needed to repeat the analysis are specified

### Example Preprocessing Paragraph
> "After quality control filtering (cells with fewer than [N] genes or more than [X%] mitochondrial reads were removed), [N] cells were retained for downstream analysis. Gene expression counts were normalized using [method] implemented in [package][ref] with default parameters. Highly variable genes (n = [N]) were identified using [method], and principal component analysis was performed on the scaled expression matrix. The top [N] principal components were selected based on [elbow plot / variance explained > X%] and used for downstream clustering and visualization."

### Example Statistical Testing Paragraph
> "Differential expression analysis was performed using [method/package][ref] with a [model type] model, including [covariates] as covariates. Genes with an FDR-adjusted p-value < [threshold] and absolute log2 fold change > [cutoff] were considered differentially expressed. For comparisons involving more than two groups, [ANOVA / Kruskal-Wallis] tests were used, followed by [post-hoc test] for pairwise comparisons. Effect sizes were calculated as [Cohen's d / Cliff's delta / log2 fold change]. All statistical analyses were performed using [software] (version [X.Y.Z])."

---

## Pattern D: Multi-omics Integration Methods

**Source**: Papers integrating multiple molecular data types

### Structure
```
Methods
├── Cohort and Sample Description
│   ├── Patient cohort demographics and clinical characteristics
│   ├── Sample collection and processing
│   ├── Ethical approval and consent
│   └── Sample overview table
├── Individual Omics Generation & Processing
│   ├── Omics Layer 1 (e.g., WGS/WES)
│   │   ├── Library preparation protocol
│   │   ├── Sequencing platform and parameters
│   │   ├── Alignment and variant calling pipeline
│   │   └── Quality metrics
│   ├── Omics Layer 2 (e.g., RNA-seq)
│   │   └── [Same substructure]
│   └── Omics Layer 3 (e.g., Methylation)
│       └── [Same substructure]
├── Integration Methodology
│   ├── Integration algorithm and rationale
│   ├── Feature harmonization across layers
│   ├── Missing data handling
│   └── Parameter selection (e.g., consensus clustering k)
├── Subtype Identification
│   ├── Clustering methodology
│   ├── Cluster stability assessment
│   ├── Subtype characterization approach
│   └── Clinical association testing
└── Regulatory Analysis
    ├── Cross-omics correlation analysis
    ├── Network construction methods
    ├── Driver identification approach
    └── Validation strategy
```

### Key Characteristics
- **Per-layer detail**: each omics type gets its own subsection with full protocol
- **Integration justification**: explains why specific integration method was chosen
- **Missing data handling**: explicitly addresses incomplete multi-omics profiles
- **Quality per layer**: separate QC metrics for each data type
- **Clinical metadata**: detailed patient/sample characteristics table

### Example Per-Layer Processing Paragraph
> "[Omics type] libraries were prepared using [kit/protocol] according to the manufacturer's instructions. Sequencing was performed on [platform] (read length: [N] bp, [paired-end/single-end]). Raw reads were aligned to [reference genome] (version [X]) using [aligner] (version [X.Y.Z]) with default parameters. [Quantification/variant calling] was performed using [tool][ref]. Samples with fewer than [N] [reads/features] or [quality metric] below [threshold] were excluded, resulting in [N] samples for downstream analysis."

### Example Integration Paragraph
> "Multi-omics integration was performed using [method][ref], which [brief description of how the method works]. Features from each omics layer were [preprocessed step: normalized, filtered to top N variable features, etc.] before integration. Consensus clustering with [N] iterations was performed for k = [range], and the optimal number of clusters was determined by [silhouette score / gap statistic / cophenetic correlation]. Missing data (samples with incomplete omics profiles) were handled by [imputation method / exclusion / method-specific approach], affecting [N] ([X%]) samples."

---

## Subsection Writing Templates

### Template 1: Pipeline Step Description

```markdown
### [Step Name]

[ToolName] [performs/executes/implements] [step description] by [mechanism].
[Input description]. [Processing description with parameters].
[Output description and how it feeds into next step].

[Optional: reference to supplementary prompt/figure/algorithm line numbers]
```

### Template 2: Dataset Description

```markdown
### [Dataset Name]

[Dataset] [was obtained from / comprises / consists of] [N] [samples/subjects/cells]
[collected from / generated by / curated from] [source].
[Key characteristics: conditions, timepoints, data types, platforms].
[Quality control: filtering criteria and resulting sample size].
[Data availability: accession numbers or URLs].
```

### Template 3: Computational Environment

```markdown
### Implementation Details

All analyses were performed using [Language] (version [X.Y.Z]).
[Package 1] (version [X.Y.Z]) was used for [purpose].
[Package 2] (version [X.Y.Z]) was used for [purpose].
[Model/API]: [model name], checkpoint [date/version], with [parameters: temperature, tokens, etc.].
Computations were performed on [hardware description].
Code is available at [URL/repository].
```

### Template 4: Evaluation Setup

```markdown
### Evaluation

To evaluate [method/tool], we [designed/assembled/curated] a benchmark comprising
[N] [items] spanning [characteristic range].

**Metrics**: [Metric 1] measures [what]; [Metric 2] captures [what].
**Baselines**: We compared against [N] methods: [Method1][ref], [Method2][ref], [Method3][ref].
**Protocol**: [Cross-validation scheme / held-out strategy / replicate runs].
**Statistical testing**: Significance was assessed using [test] with [correction] at alpha = [threshold].
```

### Template 5: Validation Approach

```markdown
### Validation

[Findings/Signatures/Models] were validated using [approach] on [independent data].
[Source and characteristics of validation data].
[Concordance/replication metric] was used to assess [reproducibility/generalizability].
[Result of validation: "X of Y findings were confirmed..."].
```

---

## Algorithm/Pseudocode Presentation Templates

### Template: Formal Algorithm Block

```
Algorithm 1: [Algorithm Name]
Require: [input 1] [description], [input 2] [description], [parameter] [description]
  1: [variable] <- [FUNCTION]([args])           > [comment: what this does]
  2: [variable] <- INITIALIZE([args])
  3: for [iterator] <- 1 to [N] do
  4:     [variable] <- STEP1([args])             > [comment]
  5:     [variable] <- STEP2([args])             > [comment]
  6:     [result] <- EXECUTE([args])
  7:     if [result] = error then                > [comment: error handling]
  8:         for [retry] = 1 to [F] do
  9:             [variable] <- FIX([args])
  10:            [result] <- EXECUTE([args])
  11:            if [result] != error then
  12:                break
  13:            end if
  14:        end for
  15:    end if
  16:    [output] <- APPEND([output], [result])
  17:    [variable] <- UPDATE([args])             > [comment: replanning]
  18: end for
  19: [report] <- REPORT([output])
  20: return [report], [output]
```

### Pseudocode Guidelines
- Number all lines for easy reference in text
- Use SMALL CAPS for function names (PLAN, EXECUTE, FIX)
- Include right-aligned comments (prefixed with >) for non-obvious steps
- Specify Require block with input types and descriptions
- Use consistent indentation for nested structures

---

## Parameter Reporting Conventions

### Required Parameters by Category

**Machine Learning / LLM**:
- Model name and version/checkpoint date
- API parameters: temperature, max tokens, top-p, reasoning effort
- Number of inference runs / replicates
- Prompt references (supplementary figures)

**Clustering**:
- Algorithm name and implementation (package)
- Number of clusters (k) and selection method
- Resolution parameter (for graph-based methods)
- Number of iterations / random restarts

**Differential Expression**:
- Statistical model (e.g., negative binomial, Wilcoxon)
- Covariates included
- FDR correction method and threshold
- Fold change cutoff

**Dimensionality Reduction**:
- Method (PCA, UMAP, t-SNE)
- Number of components / dimensions
- Key parameters (n_neighbors, min_dist for UMAP; perplexity for t-SNE)

**Quality Control**:
- Minimum genes/features per cell/sample
- Maximum mitochondrial/ribosomal percentage
- Doublet detection method and threshold
- Minimum cells per gene

### Reporting Format
> "[Analysis] was performed using [package] (version [X.Y.Z])[ref] with [key parameter 1] = [value], [key parameter 2] = [value], and default settings for all other parameters."

---

## Tool Citation Patterns

### First Mention (full citation)
- "[Tool name] (version [X.Y.Z])[ref]"
- "[Tool name][ref] (version [X.Y.Z]; [URL])"
- "the [tool name] package[ref] (version [X.Y.Z]) in [language]"

### Subsequent Mentions
- "[Tool name]" (no version or reference needed after first mention)

### Package Ecosystems
- "[Package1] (version [X.Y.Z]), [Package2] (version [X.Y.Z]), and [Package3] (version [X.Y.Z])"
- "the [ecosystem] ecosystem: [Package1][ref], [Package2][ref], and [Package3][ref]"

### Custom Code
- "Custom [language] scripts were developed for [purpose] and are available at [URL]."
- "Analysis code is available in the accompanying GitHub repository ([URL])."

### Example (from CellVoyager)
> "Implementation of CellVoyager used Python (version 3.9.22) and the following Python packages: reportlab (version 4.4.0), nbformat (version 5.10.4), and openai (version 1.77.0). CellVoyager's own analyses used the following packages: scanpy (version 1.10.3), scvi-tools (version 1.1.6), celltypist (version 1.6.3), anndata (version 0.10.8), matplotlib (version 3.9.4), numpy (version 1.26.4), seaborn (version 0.13.2), pandas (version 2.2.3), and scipy (version 1.13.1)."

---

## Reproducibility Checklist

### Essential Elements (every Methods section should include)

- [ ] **Software versions**: All tools/packages with exact version numbers
- [ ] **Data availability**: Accession numbers, URLs, or repository links
- [ ] **Code availability**: GitHub/Zenodo repository with DOI
- [ ] **Parameters**: All non-default parameters explicitly stated
- [ ] **Reference genome/database**: Version and source
- [ ] **Statistical tests**: Named with justification for choice
- [ ] **Thresholds**: All cutoffs with rationale
- [ ] **Random seeds**: Fixed seeds for reproducibility (if applicable)
- [ ] **Hardware**: Computational resources used (if runtime-relevant)
- [ ] **Sample sizes**: At each stage of filtering/analysis

### Recommended Elements (strengthen reproducibility)

- [ ] **Prompt documentation**: Exact prompts in supplementary (for LLM-based methods)
- [ ] **Docker/container**: Environment specification
- [ ] **Runtime**: Approximate wall-clock time for key steps
- [ ] **Memory requirements**: Peak memory usage
- [ ] **Supplementary protocols**: Step-by-step instructions beyond what fits in Methods

---

## Software and Data Availability Patterns

### Code Availability

```markdown
Code for [running the agent / performing all analyses / reproducing all figures]
is available in the [ToolName] GitHub repository
([URL]) and at [archival repository] via [DOI][ref].
```

### Data Availability

```markdown
[Published/Generated] data used in [the case studies / this analysis] can be found
in [database] at [accession/URL]. [Additional datasets] can also be found in the
respective publications. The [benchmark/evaluation] dataset is available from
[repository] ([URL]).
```

### Reporting Summary
> "Further information on research design is available in the Nature Portfolio Reporting Summary linked to this article."

---

## Common Pitfalls to Avoid

1. **Missing versions**: Every software tool needs an exact version number
2. **Vague parameters**: "Default parameters were used" -- specify which defaults and for what
3. **Unreproducible prompts**: LLM prompts must be documented (supplementary is fine)
4. **Missing QC thresholds**: Every filtering step needs explicit cutoff values
5. **Inconsistent methods-results**: Every analysis in Results must have a corresponding Methods entry
6. **No statistical test justification**: State why a particular test was chosen
7. **Missing sample size tracking**: Report n at each analysis stage, not just initial
8. **No error handling description**: For iterative pipelines, describe failure modes and fallbacks
