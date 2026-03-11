# Results Section Patterns for Bioinformatics Papers

Structural patterns extracted from exemplar computational biology papers.

## Table of Contents
1. [Pattern A: Multi-Agent/Pipeline Tool Papers](#pattern-a-multi-agentpipeline-tool-papers)
2. [Pattern B: AI/ML Method Papers](#pattern-b-aiml-method-papers)
3. [Pattern C: Computational Analysis Papers](#pattern-c-computational-analysis-papers)
4. [Pattern D: Multi-omics Integration Papers](#pattern-d-multi-omics-integration-papers)
5. [Subsection Writing Templates](#subsection-writing-templates)
6. [Figure Integration Patterns](#figure-integration-patterns)
7. [Statistical Reporting Conventions](#statistical-reporting-conventions)

---

## Pattern A: Multi-Agent/Pipeline Tool Papers

**Source**: CellAgent-style papers (computational frameworks for biological analysis)

### Structure
```
2. Results
├── 2.1 [Tool Name] Framework Overview
│   └── Multi-component workflow description with Figure 1
├── 2.2 [Task 1] Evaluation (e.g., Batch Correction)
│   ├── Problem statement
│   ├── Benchmark datasets
│   ├── Comparison methods
│   ├── Metrics and results (Table/Figure)
│   └── Key findings summary
├── 2.3 [Task 2] Evaluation (e.g., Cell Type Annotation)
│   └── [Same substructure as 2.2]
├── 2.4 [Task 3] Evaluation (e.g., Trajectory Inference)
│   └── [Same substructure as 2.2]
└── 2.5 Ablation Studies / Component Analysis
```

### Key Characteristics
- **Opening section**: Always describes overall framework/pipeline architecture
- **Task sections**: Each major capability gets dedicated subsection
- **Consistent internal structure**: Each task section follows identical format
- **Progressive complexity**: Tasks ordered from fundamental to advanced
- **Visual anchoring**: Each major section references specific figure/table

### Example Opening Paragraph (Framework Overview)
> "[ToolName] employs a multi-agent collaborative framework to address [problem domain]. As illustrated in Figure 1, the system consists of [N] specialized modules: [Module1] for [function1], [Module2] for [function2], and [Module3] for [function3]. These components interact through [mechanism], enabling [key capability]."

### Example Task Evaluation Paragraph
> "We first evaluated [ToolName]'s performance on [task], a critical step in [domain] analysis. Using [N] benchmark datasets spanning [characteristic range] (Table 1), we compared [ToolName] against [N] established methods: [Method1], [Method2], and [Method3]. [ToolName] achieved the highest average [metric] of [value] ± [SD] across all datasets, representing a [X%] improvement over the next-best method, [Method2] ([value] ± [SD], paired t-test, p < [threshold]) (Figure 2A). Performance gains were particularly pronounced on [specific dataset type], where [ToolName] outperformed all baselines by at least [magnitude] (Figure 2B)."

---

## Pattern B: AI/ML Method Papers

**Source**: GeneAgent-style papers (LLM-based biological analysis tools)

### Structure
```
Results
├── Overview of [Method] Workflow
│   └── System description with architecture figure
├── Benchmark Performance
│   ├── Dataset description
│   ├── Baseline comparisons
│   ├── Quantitative metrics (accuracy, F1, etc.)
│   └── Performance table with statistical tests
├── Evaluation Across Multiple Metrics
│   ├── Metric 1 analysis
│   ├── Metric 2 analysis
│   └── Cross-metric summary
├── Self-Verification / Reliability Analysis
│   └── Model confidence, error analysis
└── Real-World Application / Case Study
    ├── Application context
    ├── Results demonstration
    └── Biological interpretation
```

### Key Characteristics
- **Workflow first**: Begin with method overview before any evaluation
- **Benchmark-centric**: Heavy emphasis on quantitative comparisons
- **Multiple evaluation angles**: Same data examined through different metrics
- **Validation layer**: Include self-assessment or reliability analysis
- **Practical demonstration**: End with real-world application

### Example Benchmark Paragraph
> "To evaluate [method]'s performance, we compared it against [N] established methods ([Method1], [Method2], [Method3]) on [dataset name] containing [N samples/genes/cells]. [Method] achieved [metric] of [value] (95% CI: [range]), significantly outperforming the next best method, [Method2] ([value], p < [threshold], paired t-test). Table 1 summarizes the complete performance comparison across all evaluation metrics."

### Example Case Study Paragraph
> "To demonstrate practical utility, we applied [method] to [real-world dataset] from [source], comprising [N] samples across [conditions]. Analysis revealed [primary finding] (Figure 4A). Specifically, [method] identified [N] [entities] that were [observation], including [notable example 1] and [notable example 2]. Gene ontology enrichment of these [entities] showed significant overrepresentation of [pathway/process] (FDR-adjusted p < [threshold]), consistent with [known biology] (Figure 4B). These results demonstrate [method]'s capability to [practical application statement] in real-world datasets."

---

## Pattern C: Computational Analysis Papers

**Source**: Spatial transcriptomics, single-cell multi-modal, and large-scale genomic analysis papers

### Structure
```
Results
├── Data Overview and Quality Assessment
│   ├── Dataset characteristics (samples, cells, features)
│   ├── Quality control metrics
│   ├── Data preprocessing summary
│   └── Cohort/sample composition (Figure 1)
├── Primary Analysis
│   ├── Main analytical finding with statistics
│   ├── Visualization of key patterns (Figure 2)
│   └── Quantitative characterization
├── Comparative / Differential Analysis
│   ├── Cross-group or cross-condition comparisons
│   ├── Differential expression/accessibility results
│   ├── Statistical significance and effect sizes
│   └── Heatmap or volcano plot (Figure 3)
├── Validation or Independent Cohort
│   ├── Validation dataset description
│   ├── Concordance with primary analysis
│   └── Cross-validation metrics
└── Biological Interpretation
    ├── Pathway / enrichment analysis
    ├── Gene regulatory network or interaction findings
    ├── Functional annotation results
    └── Integration with known biology (Figure 4-5)
```

### Key Characteristics
- **Data-first**: Opens with thorough dataset description and QC
- **Layer-by-layer**: Builds from descriptive to analytical to interpretive
- **Validation emphasis**: Independent confirmation of key findings
- **Biology-anchored**: Ends with biological meaning, not just statistics
- **Figure-heavy**: Typically 5-8 main figures, often multi-panel
- **Supplementary-rich**: Many additional analyses in supplementary materials

### Example Data Overview Paragraph
> "We generated [data type] profiles from [N] [samples/patients/cells] across [N] [conditions/tissues/timepoints] (Figure 1A; Table 1). After quality control filtering (see Methods), [N] [units] were retained for downstream analysis, with a median of [value] [features] per [unit] (interquartile range: [range]). Principal component analysis revealed clear separation between [condition1] and [condition2] samples (Figure 1B), with [PC1/batch effect/biological variable] explaining [X%] of total variance."

### Example Differential Analysis Paragraph
> "Differential expression analysis between [condition1] and [condition2] identified [N] differentially expressed genes (DEGs) at FDR < [threshold] (|log2FC| > [cutoff]) (Figure 3A). Of these, [N] were upregulated and [N] were downregulated in [condition]. The most significantly upregulated genes included [Gene1] (log2FC = [value], FDR = [value]), [Gene2] (log2FC = [value], FDR = [value]), and [Gene3] (log2FC = [value], FDR = [value]) (Figure 3B). Notably, [N] of the top [N] DEGs have been previously implicated in [relevant biology], supporting the biological relevance of these findings."

### Example Validation Paragraph
> "To validate our primary findings, we analyzed an independent cohort of [N] [samples] from [source]. Consistent with our discovery dataset, [key finding was replicated] (Spearman ρ = [value], p < [threshold]) (Figure 4A). [N] of [N] ([X%]) signature genes identified in the primary analysis were also significantly [differentially expressed/enriched] in the validation cohort (hypergeometric test, p < [threshold]), confirming the robustness of the identified [pattern/signature] (Figure 4B)."

---

## Pattern D: Multi-omics Integration Papers

**Source**: Papers integrating genomics, transcriptomics, epigenomics, proteomics, and/or metabolomics

### Structure
```
Results
├── Cohort Description and Data Generation
│   ├── Patient/sample cohort overview
│   ├── Data types generated per sample
│   ├── Quality metrics for each omics layer
│   └── Overview figure (Figure 1)
├── Single-Omics Characterization
│   ├── [Omics 1] analysis and key findings
│   ├── [Omics 2] analysis and key findings
│   └── [Omics 3] analysis and key findings
├── Multi-Omics Integration
│   ├── Integration method and rationale
│   ├── Joint clustering / subtype identification
│   ├── Cross-omics correlation analysis
│   └── Integrated landscape visualization (Figure 3-4)
├── Subtype / Cluster Characterization
│   ├── Molecular features defining each subtype
│   ├── Clinical correlates of subtypes
│   ├── Survival or outcome analysis
│   └── Kaplan-Meier or clinical outcome figures (Figure 5)
└── Regulatory Mechanism / Functional Insights
    ├── Regulatory relationships across omics layers
    ├── Key driver genes/pathways
    ├── Experimental or computational validation
    └── Model or network figure (Figure 6)
```

### Key Characteristics
- **Layered presentation**: Each omics type characterized individually before integration
- **Integration as climax**: The multi-omics integration is the central analytical contribution
- **Clinical relevance**: Subtypes or findings connected to patient outcomes
- **Complex visualizations**: Circos plots, multi-panel heatmaps, Sankey diagrams
- **Large sample sizes**: Often hundreds to thousands of samples
- **Cross-layer validation**: Findings from one layer confirmed in another

### Example Integration Paragraph
> "To identify molecular subtypes reflecting the full biological complexity, we performed integrative clustering using [method] across [N] omics layers ([list]) for [N] patients with complete multi-omics profiles. This analysis identified [N] robust subtypes (consensus clustering, k = [N], average silhouette score = [value]) (Figure 3A). The subtypes showed distinct molecular landscapes: Subtype 1 (n = [N], [X%]) was characterized by [molecular feature], Subtype 2 (n = [N], [X%]) by [molecular feature], and Subtype 3 (n = [N], [X%]) by [molecular feature] (Figure 3B-D). Cross-omics agreement was high, with [N]% of subtype assignments concordant between transcriptomic and proteomic clustering alone (Cohen's κ = [value])."

### Example Clinical Outcome Paragraph
> "We next examined the clinical relevance of the identified molecular subtypes. Kaplan-Meier analysis revealed significant differences in overall survival across the [N] subtypes (log-rank test, p = [value]) (Figure 5A). Subtype [N] showed the most favorable prognosis (median OS: [value] months, 95% CI: [range]), while Subtype [N] had significantly worse outcomes (median OS: [value] months, 95% CI: [range], hazard ratio = [value], 95% CI: [range], p = [value]) (Figure 5B). In multivariate Cox regression adjusting for [covariates], subtype classification remained an independent prognostic factor (adjusted HR = [value], 95% CI: [range], p = [value]) (Table 2)."

### Example Regulatory Insight Paragraph
> "Integration of [omics1] and [omics2] data revealed [N] putative regulatory relationships between [feature type 1] and [feature type 2] (correlation |r| > [threshold], FDR < [threshold]) (Figure 6A). Among these, [Gene/Regulator] emerged as a key driver, showing concordant [omics1 change] and [omics2 change] across subtypes (Figure 6B). Network analysis identified [Gene] as a hub regulator controlling [N] downstream targets enriched in [pathway] (enrichment p < [threshold]). [Validation sentence: experimental confirmation, literature support, or independent dataset confirmation]."

---

## Subsection Writing Templates

### Template 1: Task Evaluation Section

```markdown
### [Task Name]

[1-2 sentence problem statement explaining why this task matters]

We evaluated [method] on [dataset description with size]. [Brief description of experimental setup].

[Method] achieved [primary metric] of [value], compared to [baseline method] ([value]) and [other method] ([value]) (Figure/Table X). [Additional metric] showed similar trends, with [method] demonstrating [specific improvement] (p < [threshold]).

[1 sentence highlighting most notable finding or biological relevance]
```

### Template 2: Benchmark Comparison Section

```markdown
### Performance Benchmarking

To systematically evaluate [method], we assembled a benchmark comprising [N] datasets spanning [characteristic range]. We compared against [N] state-of-the-art methods: [list methods with citations].

Across all datasets, [method] achieved an average [metric] of [value ± SD], representing a [X%] improvement over the best baseline ([method name]: [value ± SD]). Performance gains were particularly pronounced in [specific condition], where [method] outperformed alternatives by [magnitude] (Figure X).

Statistical analysis confirmed these improvements were significant ([test name], p < [threshold] for [N]/[total] comparisons).
```

### Template 3: Case Study Section

```markdown
### Application to [Specific Problem/Dataset]

To demonstrate practical utility, we applied [method] to [real-world dataset/problem] from [source].

Analysis revealed [primary finding] (Figure X). Specifically, [detailed observation 1]. Further examination identified [detailed observation 2], consistent with [known biology/literature].

These results demonstrate [method]'s capability to [practical application statement].
```

### Template 4: Data Overview Section

```markdown
### Data Overview and Quality Assessment

We [generated/collected/compiled] [data type] from [N] [samples/subjects] across [conditions] (Figure X; Table Y). After quality control ([brief QC description]), [N] [units] were retained for analysis.

[Key descriptive statistic 1]. [Key descriptive statistic 2]. [Notable characteristic of the dataset].

[Optional: batch effect assessment or data integration note].
```

### Template 5: Validation Section

```markdown
### Independent Validation

To assess the robustness of our findings, we [applied/tested] [method/signature] on an independent [dataset/cohort] comprising [N] [samples] from [source].

[Primary finding from validation] ([concordance metric] = [value], p < [threshold]) (Figure X). [N] of [N] ([X%]) [key features] were confirmed in the validation dataset, [supporting/demonstrating] [conclusion].

[Optional: note on any discrepancies and their likely explanation].
```

---

## Figure Integration Patterns

### Pattern: Figure Introduction
- **First mention**: "(Figure 1)" or "(Figure 1A)"
- **Detailed reference**: "As shown in Figure 1A, ..."
- **Comparison**: "Figure 2 compares [X] and [Y]"

### Placement Rules
1. Introduce figure within 2 sentences of first data mention
2. Describe what figure shows before stating conclusion
3. Reference specific panels (A, B, C) when applicable

### Example
> "Dimensionality reduction revealed distinct clustering patterns (Figure 2A). UMAP visualization showed clear separation between [condition1] and [condition2] samples, with [metric] confirming cluster quality (Figure 2B, silhouette score = 0.73)."

---

## Statistical Reporting Conventions

### Format Standards
- **P-values**: p < 0.05, p < 0.01, p < 0.001 (or exact: p = 0.023)
- **Confidence intervals**: (95% CI: 0.82-0.91)
- **Effect sizes**: Cohen's d = 0.8, OR = 2.3
- **Sample sizes**: (n = 100), (N = 1,000 cells)
- **Means with error**: 0.85 ± 0.03 (mean ± SD)
- **FDR correction**: (FDR-adjusted p < 0.05), (Benjamini-Hochberg, FDR < 0.01)

### Required Elements
1. Sample size for each comparison
2. Statistical test used
3. P-value or confidence interval
4. Effect size when comparing groups
5. Multiple testing correction method when applicable

### Example Statistical Sentence
> "[Method] significantly outperformed [baseline] (accuracy: 0.89 ± 0.02 vs. 0.76 ± 0.04, n = 50 datasets, paired t-test, p < 0.001, Cohen's d = 1.2)."

---

## Transition Phrases

### Between Sections
- "Having established [previous finding], we next examined..."
- "To further validate these results, we..."
- "Building on the [task] analysis, we evaluated..."
- "Given the strong performance in [task], we investigated..."
- "We next sought to determine whether [finding] extended to..."

### Within Sections
- "Consistent with these findings, ..."
- "In contrast to [X], ..."
- "Notably, ..."
- "These results suggest that..."
- "Taken together, these observations indicate..."

---

## Common Pitfalls to Avoid

1. **Interpretation in Results**: Save mechanistic explanations for Discussion
2. **Missing statistics**: Every comparison needs quantitative support
3. **Orphan figures**: Every figure must be referenced in text
4. **Vague comparisons**: "better" → "15% higher accuracy (p < 0.01)"
5. **Inconsistent terminology**: Use same term for same concept throughout
6. **Missing multiple testing correction**: Report FDR when many comparisons are made
7. **Overclaiming**: "proves" → "suggests" or "indicates" (unless truly definitive)
8. **Missing validation**: Key findings should be validated when possible
