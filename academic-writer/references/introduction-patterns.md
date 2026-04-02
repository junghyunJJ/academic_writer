# Introduction Section Patterns for Bioinformatics Papers

Structural patterns extracted from exemplar computational biology papers.
Follows the Hourglass Model: Introduction is the "top half" (funnel: broad context --> narrow research question).

## Table of Contents
1. [Pattern A: Computational Tool/Framework Introduction](#pattern-a-computational-toolframework-introduction)
2. [Pattern B: AI/ML Method Introduction](#pattern-b-aiml-method-introduction)
3. [Pattern C: Computational Analysis Introduction](#pattern-c-computational-analysis-introduction)
4. [Pattern D: Multi-omics Integration Introduction](#pattern-d-multi-omics-integration-introduction)
5. [Funnel Structure Templates](#funnel-structure-templates)
6. [Gap Statement Formulations](#gap-statement-formulations)
7. [Contribution Statement Formulations](#contribution-statement-formulations)
8. [Citation Integration Patterns](#citation-integration-patterns)

---

## Pattern A: Computational Tool/Framework Introduction

**Source**: CellVoyager-style papers (LLM-based agents and computational frameworks for biological analysis)

### Structure
```
Introduction
├── Field Context (1-2 paragraphs)
│   └── Broad importance: modern biology relies on complex, high-dimensional datasets
├── Specific Problem (1 paragraph)
│   └── Particular challenge: analyses are time-consuming, require domain expertise
├── Existing Approaches & Limitations (1-2 paragraphs)
│   ├── Prior tools and capabilities (LLMs, AI agents, workflow assistants)
│   └── Why they fall short (isolated prompts, no iterative exploration, no prior-work awareness)
├── Gap Statement (1-2 sentences)
│   └── "However, [no existing method / these approaches have not been shown to] reliably..."
├── Contribution Statement (1 paragraph)
│   └── "In this Article, we introduce [ToolName], a/an [description] that [key capability]."
└── Evaluation Preview (2-3 sentences)
    └── Brief mention of benchmark and case studies demonstrating utility
```

### Key Characteristics
- **Classic funnel**: broad field importance --> specific challenge --> existing work --> gap --> contribution
- **Gap statement is the pivot point** between background and contribution
- **Contribution paragraph is concrete**: names the tool, states what it does, and how it differs
- **Citations are heavy** in the Existing Approaches section (often 10-20 references)
- **Tense**: present tense for field state ("Modern biology relies on..."), past for specific studies ("X demonstrated...")
- **Evaluation preview** at the end sets reader expectations for Results

### Example Opening Paragraph (Field Context)
> "Modern biological research often involves collecting and analyzing complex, high-dimensional datasets. These datasets range from multiomics assays to spatial and temporal profiling, each harboring rich biological insights[1-3]. Yet, the high dimensionality that enriches these datasets also introduces substantial analytical challenges, requiring complex, domain-specific computational methods to extract real biological signal[4-6]. Many of these methods have steep learning curves, especially for researchers without computational backgrounds."

### Example Gap Statement Paragraph
> "Recent advances in large language models (LLMs) have demonstrated strong capabilities in both biological reasoning and complex code generation. These developments have spurred the emergence of LLM-based artificial intelligence (AI) agents for scientific data analysis[10-12]. However, in a typical biological research setting, investigators have a set of analyses they plan to try or have already executed. Therefore, a truly collaborative AI agent should not only respond to user prompts in isolation but should also complement the investigator's prior efforts. Although recent LLM-based agents have shown promise, to our knowledge, they have not been shown to reliably [specific gap]."

### Example Contribution Statement Paragraph
> "In this Article, we introduce [ToolName], an LLM-driven AI agent that autonomously [generates/analyzes/explores] [data type] datasets. By taking in [input description] and a record of [prior work], [ToolName] [generates/executes/evaluates] new [analytical pipelines/hypotheses/models] that build directly on prior work. Unlike earlier systems, [ToolName] can complement and extend an existing set of analyses, facilitating a more collaborative [human-AI/researcher-agent] workflow. To demonstrate the utility of [ToolName], we applied it to [benchmark/dataset] comprising [N] [studies/samples], where it [key result summary]."

---

## Pattern B: AI/ML Method Introduction

**Source**: GeneAgent-style papers (LLM-based biological analysis and prediction tools)

### Structure
```
Introduction
├── Biological Challenge (1 paragraph)
│   └── Specific biological problem requiring computational solutions
├── Computational Landscape (1-2 paragraphs)
│   ├── Existing ML/DL approaches and their capabilities
│   └── Remaining challenges (generalization, interpretability, scale)
├── LLM/Foundation Model Opportunity (1 paragraph)
│   └── How recent AI advances create new possibilities
├── Gap Statement (1-2 sentences)
│   └── "Despite these advances, [specific unmet need]..."
├── Method Introduction (1 paragraph)
│   └── "Here, we present [MethodName], which [leverages/combines/integrates]..."
└── Key Results Preview (2-3 sentences)
    └── Quantitative highlights from evaluation
```

### Key Characteristics
- **Problem-driven opening**: starts with biological question, not technology
- **Technology progression**: traditional methods --> ML/DL --> LLMs/foundation models --> gap
- **Positions within AI landscape**: explicitly compares to other AI approaches
- **Quantitative preview**: includes specific performance numbers in introduction
- **Moderate citation density**: focused on most relevant prior methods

### Example Opening Paragraph (Biological Challenge)
> "[Biological process] is a fundamental challenge in [field], with implications for [application 1], [application 2], and [application 3]. Traditional approaches to [task] rely on [conventional method], which requires [limitation: manual curation / extensive domain expertise / prohibitive computational cost]. As the scale of [data type] datasets continues to grow -- with recent atlases comprising [millions/thousands] of [cells/samples/features][1-3] -- there is an urgent need for automated, scalable methods that can [desired capability]."

### Example Method Introduction Paragraph
> "Here, we present [MethodName], a [model type] that [core capability] by [technical approach]. [MethodName] integrates [component 1] with [component 2], enabling [key advantage over existing methods]. We evaluate [MethodName] on [N] benchmark datasets spanning [characteristic range] and demonstrate that it [outperforms/achieves] [metric] of [value], representing a [X%] improvement over [best existing method]. Furthermore, we show that [MethodName] can [practical application], identifying [specific biological finding] in [real-world dataset]."

---

## Pattern C: Computational Analysis Introduction

**Source**: Spatial transcriptomics, single-cell multi-modal, and large-scale genomic analysis papers

### Structure
```
Introduction
├── Biological System Context (1-2 paragraphs)
│   └── Importance of the tissue/disease/biological system under study
├── Technical Advances (1 paragraph)
│   └── New technologies enabling previously impossible measurements
├── Prior Studies & Remaining Questions (1-2 paragraphs)
│   ├── What has been characterized so far
│   └── What remains unknown or controversial
├── Gap Statement (1-2 sentences)
│   └── "A comprehensive [atlas/characterization/analysis] of [X] remains lacking..."
├── Study Aims (1 paragraph)
│   └── "To address this, we [generated/performed/conducted]..."
└── Discovery Preview (2-3 sentences)
    └── Major biological findings highlighted
```

### Key Characteristics
- **Biology-first framing**: the biological question drives the introduction, not the method
- **Technology as enabler**: new technologies mentioned to justify study feasibility
- **Literature-heavy**: extensive citation of prior work in the field (often 20-40 references)
- **Multiple unknowns listed**: often 2-3 specific open questions enumerated
- **Discovery-oriented preview**: highlights novel biological findings, not just methods

### Example Opening Paragraph (Biological System Context)
> "[Tissue/organ/system] plays a critical role in [physiological function], and its dysregulation is implicated in [disease 1], [disease 2], and [disease 3][1-4]. [Tissue/system] comprises a heterogeneous mixture of cell types -- including [cell type 1], [cell type 2], and [cell type 3] -- whose coordinated interactions govern [biological process][5,6]. Understanding the molecular programs that define these cell populations and how they are altered in [condition] is essential for developing [targeted therapies / diagnostic biomarkers / mechanistic insights]."

### Example Study Aims Paragraph
> "To address these questions, we performed [data type] profiling of [N] [samples/cells] from [N] [donors/patients] spanning [conditions/timepoints] (Figure 1). Our analysis reveals [finding 1], identifies [finding 2], and uncovers [finding 3]. These results provide a comprehensive [atlas/resource/framework] for understanding [biological system] and highlight [key biological insight] with implications for [clinical/therapeutic application]."

---

## Pattern D: Multi-omics Integration Introduction

**Source**: Papers integrating genomics, transcriptomics, epigenomics, proteomics, and/or metabolomics

### Structure
```
Introduction
├── Disease/System Complexity (1-2 paragraphs)
│   └── Why single-omics views are insufficient for this problem
├── Single-Omics Limitations (1 paragraph)
│   └── Specific examples of what each layer misses alone
├── Integration Promise (1 paragraph)
│   ├── Multi-omics integration as a solution
│   └── Prior integration studies and their scope
├── Gap Statement (1-2 sentences)
│   └── "However, a comprehensive multi-omics characterization of [X] has not been undertaken..."
├── Study Design & Contribution (1 paragraph)
│   └── "Here, we present an integrated [omics1]-[omics2]-[omics3] analysis of [N] [samples]..."
└── Key Findings Preview (2-3 sentences)
    └── Subtypes, regulatory mechanisms, or clinical associations discovered
```

### Key Characteristics
- **Emphasizes complementarity**: each omics layer contributes unique information
- **Justifies integration**: explicit argument for why multi-omics is necessary
- **Large cohort sizes**: often highlights the scale of the study as a strength
- **Clinical anchoring**: connects molecular findings to patient outcomes
- **Multiple data types listed**: explicitly names all omics layers included

### Example Opening Paragraph (Disease/System Complexity)
> "[Disease/condition] is a [molecularly heterogeneous / clinically diverse / complex] disorder affecting [prevalence statistic][1,2]. Despite decades of research, [clinical outcome] remains [poor/variable/unpredictable], largely due to an incomplete understanding of the molecular mechanisms driving [disease heterogeneity / treatment resistance / progression][3-5]. Individual genomic, transcriptomic, and epigenomic studies have each revealed important aspects of [disease] biology[6-10], yet no single molecular layer fully captures the regulatory complexity underlying [specific clinical challenge]."

### Example Study Design Paragraph
> "Here, we present an integrated analysis of [omics1], [omics2], and [omics3] data from [N] [patients/samples] with [condition], representing the largest multi-omics cohort of [disease/tissue] to date. By jointly analyzing [N] molecular features across [N] omics layers, we identify [N] molecularly distinct subtypes with divergent clinical outcomes. We further reveal [regulatory mechanism] linking [molecular feature] to [clinical phenotype], and validate these findings in an independent cohort of [N] [samples]. Our results provide a comprehensive multi-omics atlas of [condition] and nominate [therapeutic targets / biomarkers / mechanistic insights] for [clinical application]."

---

## Funnel Structure Templates

### Template 1: Broad-to-Narrow Funnel (Standard)

```markdown
[Paragraph 1: Field significance - broad context, 3-5 sentences]
[Domain] has [emerged as / become essential for / transformed] [broader field].
[Specific advance] enables [capability], opening new avenues for [application].

[Paragraph 2: Technical landscape - narrow to specific challenge, 3-5 sentences]
[Existing approaches] have addressed [aspect] through [methods].
However, [specific limitation] remains a significant barrier to [goal].

[Paragraph 3: Gap statement - the pivot, 1-3 sentences]
Despite [progress/advances], [specific gap] has not been [addressed/resolved/demonstrated].

[Paragraph 4: Contribution - what this paper offers, 3-5 sentences]
In this [Article/study], we [introduce/present/develop] [contribution].
[Brief description of approach and key advantage].
[Evaluation summary or key result].
```

### Template 2: Problem-Solution Funnel (Method Papers)

```markdown
[Paragraph 1: Problem definition - real-world impact, 2-3 sentences]
[Problem] affects [stakeholders] and currently requires [costly/manual/expert process].

[Paragraph 2: Why existing solutions fail - technical depth, 3-5 sentences]
Current approaches including [Method1], [Method2], and [Method3] address [partial aspect]
but [fail to / cannot / are limited in] [critical capability].

[Paragraph 3: Opportunity + Gap - pivotal transition, 2-3 sentences]
Recent advances in [technology] offer a promising [direction/opportunity].
However, [specific gap in applying this technology to this problem].

[Paragraph 4: Our contribution - concrete and specific, 3-5 sentences]
[ToolName/MethodName] addresses this gap by [approach].
We demonstrate [key result] on [benchmark/application].
```

### Template 3: Discovery Funnel (Analysis Papers)

```markdown
[Paragraph 1: Biological importance - establish relevance, 3-5 sentences]
[Biological system] is central to [process] and implicated in [diseases].

[Paragraph 2: What is known - prior work, 3-5 sentences]
Previous studies have characterized [aspects] using [approaches][refs].
[Key prior finding 1]. [Key prior finding 2].

[Paragraph 3: What remains unknown - enumerate gaps, 2-4 sentences]
However, [gap 1] remains poorly understood.
Furthermore, [gap 2] has not been systematically [examined/characterized].

[Paragraph 4: This study - aims and scope, 3-5 sentences]
To address these gaps, we [performed/generated/conducted] [approach].
Our analysis [reveals/identifies/uncovers] [key findings].
```

---

## Gap Statement Formulations

### Knowledge Gap (what is unknown)
- "Despite extensive research on [X], the [specific aspect] remains poorly understood."
- "While [general finding] is well established, [specific knowledge gap] has not been characterized."
- "A comprehensive understanding of [X] is still lacking, particularly regarding [specific aspect]."
- "The [molecular/cellular/regulatory] mechanisms underlying [phenomenon] remain elusive."
- "How [factor X] contributes to [outcome Y] in the context of [condition Z] is unknown."

### Methodology Gap (how existing tools fall short)
- "However, existing methods for [task] are limited by [specific limitation], precluding [desired capability]."
- "Current approaches require [costly requirement], making them impractical for [scale/application]."
- "No existing [tool/framework/pipeline] can reliably [specific unmet capability]."
- "While [Method X] addresses [partial problem], it [cannot/fails to] account for [critical factor]."
- "These approaches have not been shown to [specific capability] in [realistic setting]."

### Application Gap (where tools have not been applied)
- "Although [tool/approach] has been successfully applied to [domain A], its utility for [domain B] remains unexplored."
- "To our knowledge, [approach] has not been applied to [specific problem/dataset type]."
- "Whether [finding/approach] generalizes to [new context] has not been investigated."
- "The potential of [technology] for [application] has yet to be realized."
- "Despite the availability of [resource/data], a systematic [analysis/characterization] has not been undertaken."

---

## Contribution Statement Formulations

### Tool/Framework Papers
- "In this Article, we introduce [ToolName], a/an [brief description] that [key capability]."
- "Here, we present [ToolName], which [addresses gap] by [technical approach]."
- "We develop [ToolName], a [type] framework that [unique capability], enabling [downstream benefit]."
- "To address this challenge, we propose [ToolName], a [novel/automated/scalable] [system type] for [task]."

### Method Papers
- "Here, we present [MethodName], a [model/algorithm/approach] that [key innovation]."
- "We introduce [MethodName], which [leverages/combines] [component A] and [component B] to [achieve goal]."
- "In this work, we develop [MethodName], a [learning/statistical/computational] framework for [task]."

### Analysis/Discovery Papers
- "To address these questions, we [generated/performed/analyzed] [data description]."
- "Here, we present a comprehensive [atlas/characterization/survey] of [N] [samples/subjects]."
- "We report [data type] profiling of [system] across [conditions], revealing [key discovery]."
- "In this study, we systematically [characterized/profiled/mapped] [system] using [approach]."

### Multi-omics Papers
- "Here, we present an integrated [omics list] analysis of [N] [samples], revealing [key finding]."
- "We performed multi-omics profiling of [cohort], combining [omics1], [omics2], and [omics3] to [goal]."
- "To dissect the [regulatory/molecular] complexity of [system], we integrated [N] omics layers across [N] [samples]."

---

## Citation Integration Patterns

### High-Density Citation (Existing Approaches sections)
- **Listing precedents**: "[Finding] has been demonstrated in [domain1][1], [domain2][2,3], and [domain3][4-6]."
- **Grouping by approach**: "Several groups have addressed [problem] through [approach A][1-4], while others have pursued [approach B][5-8]."
- **Progressive citations**: "[Early work][1] established [foundation]. Subsequent studies extended this to [advance][2,3], and more recently, [latest advance][4,5]."

### Selective Citation (Contribution/Evaluation sections)
- **Key comparisons only**: "Unlike [Method1][ref] and [Method2][ref], [OurMethod] [distinguishing feature]."
- **Benchmark reference**: "We evaluate on [Benchmark][ref], a widely used [standard/dataset] for [task]."

### Narrative Citation (Field Context)
- **Establishing importance**: "[Field] has [transformed/revolutionized] [area] (reviewed in [ref])."
- **Acknowledging complexity**: "The challenges of [problem] are well documented[1-5]."

### Citation Placement Rules
1. Place citations at the end of the claim they support, before the period
2. Use superscript numbers or brackets depending on journal style
3. Group consecutive references with hyphens: [1-5] not [1,2,3,4,5]
4. First mention of a method/tool should include its citation
5. Review articles cited for broad claims; primary papers for specific claims

---

## Hourglass Model: Introduction as the Top Half

The Introduction forms the top of the hourglass -- a funnel narrowing from broad to specific:

```
     ┌─────────────────────────────────┐   Broad: Field significance
     │  "Modern biology relies on..."  │   (1-2 paragraphs)
     └───────────────────────────────┘
        ┌──────────────────────────┐      Narrowing: Specific challenge
        │  "scRNA-seq analysis..." │      (1 paragraph)
        └────────────────────────┘
           ┌───────────────────┐          Prior work & limitations
           │  "LLMs, agents..."│          (1-2 paragraphs)
           └─────────────────┘
              ┌────────────┐              Gap statement (the pivot)
              │  "However" │              (1-2 sentences)
              └──────────┘
           ┌───────────────────┐          Contribution statement
           │  "We introduce..."│          (1 paragraph)
           └─────────────────┘
```

### Mirror Relationship with Discussion
- Introduction GAP statement <--> Discussion SUMMARY of how gap was addressed
- Introduction BROAD context <--> Discussion BROADER implications
- Introduction CITED prior work <--> Discussion COMPARISON with that prior work
- Introduction CONTRIBUTION claim <--> Discussion EVIDENCE supporting that claim

---

## Common Pitfalls to Avoid

1. **Starting too broad**: "Since the dawn of time..." -- Begin with field-relevant context, not universal truths
2. **Missing gap statement**: Every Introduction must have an explicit gap before the contribution
3. **Vague contribution**: "We present a novel method" -- Specify what is novel and why it matters
4. **Over-promising**: Claims in the Introduction must be supported in Results
5. **Burying the contribution**: The contribution statement should be clearly identifiable, not hidden
6. **Citation imbalance**: Heavy citations in background but none for specific claims
7. **Disconnected preview**: Evaluation preview should match what actually appears in Results
8. **Too long or too short**: Typical range is 3-6 paragraphs (600-1200 words) for most journals
