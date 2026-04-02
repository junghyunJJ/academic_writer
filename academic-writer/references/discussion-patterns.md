# Discussion Section Patterns for Bioinformatics Papers

Structural patterns extracted from exemplar computational biology papers.
Follows the Hourglass Model: Discussion is the "bottom half" (inverted funnel: specific findings --> broad significance).

## Table of Contents
1. [Pattern A: Tool/Framework Discussion](#pattern-a-toolframework-discussion)
2. [Pattern B: AI/ML Method Discussion](#pattern-b-aiml-method-discussion)
3. [Pattern C: Computational Analysis Discussion](#pattern-c-computational-analysis-discussion)
4. [Pattern D: Multi-omics Integration Discussion](#pattern-d-multi-omics-integration-discussion)
5. [Subsection Writing Templates](#subsection-writing-templates)
6. [Limitation Formulation Templates](#limitation-formulation-templates)
7. [Literature Comparison Paragraph Templates](#literature-comparison-paragraph-templates)
8. [Future Direction Templates](#future-direction-templates)
9. [Hedging Language Guide](#hedging-language-guide)

---

## Pattern A: Tool/Framework Discussion

**Source**: CellVoyager-style papers (LLM-based agents and computational frameworks)

### Structure
```
Discussion
├── Summary of Contributions (1 paragraph)
│   ├── Restate what the tool is and does
│   ├── Key design philosophy (e.g., collaborative, autonomous, iterative)
│   └── Core results recap (benchmark performance, case study findings)
├── Performance Context (1-2 paragraphs)
│   ├── Benchmark performance in context of baselines
│   ├── Case study highlights with expert validation
│   └── Human-in-the-loop capability demonstration
├── Specific Limitations (1-2 paragraphs)
│   ├── Limitation 1: Operational constraint (e.g., autonomous mode, sequential execution)
│   ├── Limitation 2: Scope constraint (e.g., single data modality, specific domain)
│   ├── Limitation 3: Technical constraint (e.g., model dependency, runtime)
│   └── Each limitation stated honestly with specifics
├── Future Directions (1 paragraph)
│   ├── Direction 1: Tied to Limitation 1 (e.g., real-time interaction)
│   ├── Direction 2: Tied to Limitation 2 (e.g., multi-omics extension)
│   ├── Direction 3: Tied to Limitation 3 (e.g., fine-tuning, parallelization)
│   └── Each direction is concrete and actionable
└── Broader Impact (1 paragraph)
    ├── Field-level significance (e.g., reanalysis of public data at scale)
    ├── Potential for accelerating discovery
    └── Closing statement on vision
```

### Key Characteristics
- **Contribution-first opening**: immediately reminds reader of what was achieved
- **Honest, specific limitations**: not vague ("some limitations exist") but concrete ("operates sequentially, taking 15-30 min per analysis")
- **Limitation-to-future-direction pairing**: each limitation motivates a specific future direction
- **No new data introduced**: Discussion interprets existing results, does not add new analyses
- **Active voice preferred**: "We demonstrated..." / "Our results suggest..."
- **Present tense for interpretations**: "These results demonstrate..." Past tense for recaps: "CellVoyager outperformed..."

### Example Summary Paragraph
> "[ToolName] is an LLM-based agent designed to autonomously explore [data type] datasets and complement prior work conducted by human researchers. It generates new analysis ideas informed by existing [literature/analyses] and any analyses that have already been attempted. These ideas are then executed by [ToolName], which leverages [technology/platform] and iterates within a [execution environment]. The agent synthesizes these [outputs] to produce [data-specific/biologically meaningful] analyses that extend beyond standard templates."

### Example Performance Context Paragraph
> "To test [ToolName] in a challenging setting, we focused on how well [ToolName] can expand upon the results presented in a published [data type] research paper, where extensive effort has been put into fully exploring the dataset. Using [BenchmarkName], we evaluated [ToolName]'s ability to propose analysis ideas that are consistent with the kinds of analyses typically undertaken in [domain] studies. Our results suggest that [ToolName] can generate analysis ideas that are appropriate given a research direction, outperforming state-of-the-art LLMs in this task."

### Example Limitations Paragraph (from CellVoyager)
> "While [ToolName] is capable of proposing and executing meaningful analysis on [data type] data, our work had several limitations. In the current version, [ToolName] operates in a fully autonomous mode and only accepts user feedback after the analysis has completed, rather than during execution. Enabling real-time user guidance represents a natural direction for future work. Additionally, [ToolName] currently runs analyses sequentially, taking around [time range] per analysis. One possible addition to speed up run time is to identify independent lines of analysis and perform them in parallel. Furthermore, while we focus on [specific domain], the approach generalizes to other [domains/modalities]; future work can include [extension 1] and [extension 2]."

### Example Broader Impact Paragraph
> "While [ToolName] can help users to analyze new data, our study also demonstrates that many new insights could be gained from reanalysis of existing public data. With [scale: tens of thousands of] published [datasets] in [data type], thorough reanalysis by human researchers is prohibitive; however, agents such as [ToolName] could potentially unlock a treasure trove of new insights by tirelessly analyzing these vast public datasets."

---

## Pattern B: AI/ML Method Discussion

**Source**: Papers introducing new machine learning methods for biological problems

### Structure
```
Discussion
├── Principal Achievement (1 paragraph)
│   ├── Core finding stated in biological terms, not just metrics
│   ├── How the method advances the state of the art
│   └── Key performance numbers in context
├── Why It Works (1 paragraph)
│   ├── Architectural/methodological insights explaining performance
│   ├── What the model learned (interpretability)
│   └── Ablation evidence supporting design choices
├── Comparison with Prior Methods (1-2 paragraphs)
│   ├── Direct comparison with specific prior work
│   ├── Where new method excels and why
│   ├── Where prior methods may still be preferable
│   └── Fair, balanced assessment
├── Limitations (1 paragraph)
│   ├── Data requirements (training data size, quality)
│   ├── Generalization boundaries (domains, species, data types)
│   ├── Computational cost
│   └── Interpretability constraints
├── Future Directions (1 paragraph)
│   ├── Model improvements (architecture, training)
│   ├── New applications and domains
│   ├── Integration with other methods
│   └── Addressing specific limitations
└── Significance Statement (2-3 sentences)
    └── What this means for the field broadly
```

### Key Characteristics
- **Biological grounding**: performance discussed in terms of biological utility, not just metrics
- **Mechanistic insight**: explains why the method works, not just that it does
- **Balanced comparison**: acknowledges scenarios where existing methods remain competitive
- **Scalability discussion**: addresses how well the method handles growing data sizes

### Example Principal Achievement Paragraph
> "In this work, we developed [MethodName], a [model type] for [task] that [key achievement]. Across [N] benchmark datasets, [MethodName] achieved a [metric] of [value], representing a [X%] improvement over [best baseline] and demonstrating robust performance across [diverse conditions/data types]. Importantly, [MethodName] maintained [key advantage: speed / interpretability / low resource requirements] while delivering these improvements, making it practical for [application context]."

### Example Comparison Paragraph
> "Compared to [Method1][ref], which [approach/limitation], [MethodName] benefits from [advantage]. [Method2][ref] achieves competitive performance on [specific subset] but requires [disadvantage: larger training sets / manual feature engineering / specific data format]. Our ablation studies (Supplementary Table [N]) confirm that [component] accounts for [X%] of the performance gain, while [other component] contributes to [specific improvement]. We note that [prior method] may remain preferable for [specific use case], where [its specific advantage] outweighs [our method's advantage]."

---

## Pattern C: Computational Analysis Discussion

**Source**: Papers presenting novel computational analyses of biological datasets

### Structure
```
Discussion
├── Key Discoveries Summary (1-2 paragraphs)
│   ├── Most important biological finding restated
│   ├── Second most important finding
│   └── How findings relate to each other
├── Literature Contextualization (2-3 paragraphs)
│   ├── Finding 1 vs. published literature
│   ├── Finding 2 vs. published literature
│   ├── Novel aspects highlighted
│   └── Consistent aspects noted for validation
├── Mechanistic Interpretation (1 paragraph)
│   ├── Proposed biological mechanism
│   ├── Supporting evidence from data
│   └── Consistency with known biology
├── Clinical/Translational Implications (1 paragraph)
│   ├── Relevance for disease understanding
│   ├── Potential therapeutic/diagnostic implications
│   └── Appropriate hedging for preliminary findings
├── Limitations (1 paragraph)
│   ├── Sample size and cohort diversity
│   ├── Technical limitations of the platform
│   ├── Validation gaps
│   └── Potential confounders
├── Future Directions (1 paragraph)
│   └── Experimental validation, larger cohorts, mechanistic follow-up
└── Concluding Statement (2-3 sentences)
    └── Broad significance for the field
```

### Key Characteristics
- **Discovery-centric**: organizes discussion around biological findings, not methods
- **Extensive literature comparison**: multiple paragraphs comparing findings to published work
- **Careful interpretation**: appropriately hedged biological interpretations
- **Clinical relevance**: connects findings to potential applications
- **Acknowledges validation needs**: clearly states what remains to be validated experimentally

### Example Key Discovery Paragraph
> "Our analysis revealed [N] [molecularly distinct / transcriptionally defined / functionally characterized] [subtypes/populations/states] within [system/tissue], including a previously uncharacterized [specific population/state] defined by [marker/feature]. This [population] exhibited [distinguishing characteristic], suggesting [biological interpretation]. Notably, [specific finding] was validated in an independent cohort of [N] [samples], supporting the robustness of this observation."

### Example Literature Contextualization Paragraph
> "Our identification of [finding] is consistent with recent reports by [Author et al.][ref] and [Author et al.][ref], who observed [related finding] in [context]. However, our study extends these observations by [specific extension: larger cohort / additional data type / new condition / finer resolution]. In contrast to [Author et al.][ref], who reported [different finding], our data suggest [alternative interpretation], potentially reflecting [explanation for discrepancy: different cohort / technical differences / biological context]."

---

## Pattern D: Multi-omics Integration Discussion

**Source**: Papers integrating multiple molecular data types

### Structure
```
Discussion
├── Integration Value Proposition (1 paragraph)
│   ├── What integration revealed that single-omics could not
│   ├── Concordance across layers as validation
│   └── Unique insights from cross-layer analysis
├── Subtype/Biological Discovery (1-2 paragraphs)
│   ├── Novel molecular subtypes and their significance
│   ├── Comparison to existing classification systems
│   ├── Clinical associations and prognostic value
│   └── Regulatory mechanisms discovered
├── Cross-Layer Regulatory Insights (1 paragraph)
│   ├── Key regulatory relationships identified
│   ├── Driver genes/regulators and their multi-omics evidence
│   └── Consistency with known regulatory mechanisms
├── Comparison with Prior Studies (1-2 paragraphs)
│   ├── How subtypes relate to previous single-omics classifications
│   ├── Novel findings not captured by prior approaches
│   └── Consistent findings providing additional evidence
├── Limitations (1 paragraph)
│   ├── Missing omics layers or incomplete profiles
│   ├── Cohort size and diversity
│   ├── Batch effects and technical confounders
│   └── Lack of functional validation
├── Future Directions (1 paragraph)
│   ├── Additional omics layers (single-cell, spatial)
│   ├── Functional validation (CRISPR, in vivo)
│   ├── Clinical application (biomarker panels, treatment stratification)
│   └── Computational extensions (temporal, spatial integration)
└── Significance (2-3 sentences)
    └── Resource value and field impact
```

### Key Characteristics
- **Integration justification retrospective**: demonstrates post hoc that integration was necessary
- **Cross-layer validation**: concordance across omics layers strengthens findings
- **Clinical translation emphasis**: strong focus on prognostic/therapeutic implications
- **Resource contribution**: positions the dataset as a community resource

### Example Integration Value Paragraph
> "By integrating [omics1], [omics2], and [omics3] data across [N] [patients/samples], we uncovered molecular subtypes that could not be identified from any single omics layer alone. While [omics1]-based clustering identified [N] groups, the integrated analysis refined these into [N] subtypes with distinct [clinical/molecular] characteristics. Concordance between [omics1] and [omics2] classifications was [metric = value], and the [N] discordant cases showed [distinctive feature], suggesting that multi-omics integration captures biologically meaningful heterogeneity missed by individual assays."

### Example Regulatory Insight Paragraph
> "Cross-omics analysis revealed [N] putative regulatory relationships between [feature type 1] and [feature type 2] that were not apparent from either data type alone. Notably, [Regulator/Gene] emerged as a key driver of [biological process], showing concordant [changes] across [omics layers]. This multi-omics evidence, combined with its established role in [known biology][ref], positions [Gene] as a promising [therapeutic target / biomarker] for [application]. Experimental validation through [approach] will be needed to confirm the functional significance of these regulatory relationships."

---

## Subsection Writing Templates

### Template 1: Summary of Contributions

```markdown
In this [work/study], we [presented/developed/introduced] [contribution],
a [brief description] for [purpose]. [ToolName/Method] [key capability]
by [approach]. [Key result 1 with numbers]. [Key result 2 with context].
These results [demonstrate/suggest/establish] [significance statement].
```

### Template 2: Performance in Context

```markdown
[Method/Tool] [outperformed/achieved competitive performance with] [baselines]
on [benchmark/task], [achieving/with] [metric] of [value]. These improvements
were [particularly pronounced / most evident] in [specific condition/subset],
suggesting that [interpretation]. [Comparison to specific prior work with nuance].
```

### Template 3: Limitation-Direction Pair

```markdown
[Specific limitation statement]. [Consequence or implication of this limitation].
[Natural transition: "Enabling X represents..." / "Future work could address this by..."].
[Concrete future direction that would resolve or mitigate the limitation].
```

### Template 4: Broader Impact Closing

```markdown
[Beyond the immediate findings], our [study/tool/method] [demonstrates/suggests]
that [broader principle]. [Scale statement: how many X could benefit].
[Vision statement: what becomes possible with this advance].
```

### Template 5: Literature Comparison

```markdown
[Our finding] is [consistent with / extends / contrasts with] previous reports
by [Author et al.][ref], who [found/demonstrated/showed] [their finding]
in [their context]. [Explanation of consistency or discrepancy].
[What our study adds beyond this prior work].
```

---

## Limitation Formulation Templates

### Operational Limitations (how the system runs)
- "In the current version, [ToolName] operates in [constraint], rather than [ideal mode]. Enabling [ideal mode] represents a natural direction for future work."
- "[ToolName] currently [limitation: runs sequentially / requires manual input / operates offline], taking approximately [time/resource]. [Concrete improvement] could address this constraint."
- "Our approach relies on [dependency: specific API / model / infrastructure], which [limitation: may change / incurs cost / limits accessibility]."

### Scope Limitations (what was tested)
- "While we focus on [specific domain/data type], the approach [generalizes/may generalize] to [broader domain]; testing on [other domains] remains future work."
- "Our evaluation was limited to [N] [datasets/studies] in [specific domain]; additional validation across [diverse conditions/species/diseases] would strengthen the generality of these findings."
- "The current study did not [examine/include/test] [specific aspect]; [brief justification for the omission and its potential impact]."

### Technical Limitations (inherent constraints)
- "A key limitation is [specific technical constraint], which [consequence]. However, [mitigating factor or ongoing development]."
- "[Method] assumes [assumption], which may not hold for [specific conditions]. [Evidence for or against this assumption in our data]."
- "Without [missing element: fine-tuning / larger training set / experimental validation], [ToolName/MethodName] may [specific failure mode]."

### Data Limitations (what the data cannot tell us)
- "Our analysis is limited by [data constraint: sample size / batch effects / missing modalities / single timepoint], which may [potential impact]."
- "Caution is warranted in interpreting [specific finding], as [confound/limitation] cannot be ruled out from [observational/computational] data alone."
- "The [cell counts / sample size / coverage] in [specific subset] were insufficient to [specific analysis], and these results should be interpreted with caution."

### Constructive Limitation Format (limitation + silver lining)
- "Although [limitation], this [design choice] allowed us to [benefit], and [extending to broader scope] represents a [natural/promising] direction for future work."
- "While [constraint] limited [aspect], [mitigating factor] suggests that [optimistic interpretation]. Future studies with [improvement] could [expected gain]."

---

## Literature Comparison Paragraph Templates

### Template: Consistent Finding (validates prior work)
> "Our observation that [finding] is consistent with [Author et al.][ref], who reported [their finding] in [their context]. [Additional supporting reference][ref] similarly found [related observation]. Our study extends these prior observations by [specific novel contribution: larger cohort / new data type / additional analysis / temporal dimension], providing further evidence that [consolidated interpretation]."

### Template: Novel Finding (extends beyond prior work)
> "To our knowledge, the [finding] reported here has not been previously described. While [Author et al.][ref] noted [related but distinct observation], our analysis reveals [specific new aspect] that [biological significance]. This finding was [validated/supported] by [our validation approach], suggesting [interpretation]. [Mechanism/explanation for why this was not observed before: resolution / sample size / analytical approach]."

### Template: Conflicting Finding (disagrees with prior work)
> "In contrast to [Author et al.][ref], who reported [their finding], our analysis suggests [our different finding]. This discrepancy may reflect [possible explanation 1: different cohort/conditions], [possible explanation 2: methodological differences], or [possible explanation 3: biological context]. Notably, [evidence or argument favoring one interpretation]. Future studies [comparing/using] [approach] will be needed to resolve this discrepancy."

### Template: Contextualizing within Broader Literature
> "Our results [add to / contribute to] a growing body of evidence suggesting [broader pattern][refs]. Collectively, these studies point to [unified interpretation] as a [key mechanism / important factor / common feature] in [field/context]. However, [nuance: outstanding questions / heterogeneity across studies / need for mechanistic validation]."

---

## Future Direction Templates

### Tied to Limitations (recommended pattern)
- **Limitation**: sequential execution --> **Future**: "Identifying independent lines of analysis and performing them in parallel could substantially reduce runtime."
- **Limitation**: single data modality --> **Future**: "Extending [ToolName] to other data modalities, such as [spatial transcriptomics / proteomics / imaging], would broaden its applicability. This requires [specific modifications: adjusting data loading / updating analysis templates / configuring modality-specific packages]."
- **Limitation**: no fine-tuning --> **Future**: "Future work can investigate how to fine-tune [model components] within [ToolName] to improve [specific capability]."
- **Limitation**: limited validation --> **Future**: "A promising future direction is the development of systematic replication benchmarks, where agents are tested on their ability to faithfully reproduce key results from published studies."

### Standalone Future Directions
- "[Advance] creates opportunities for [application], particularly in [specific context]."
- "An important next step will be to [validate/extend/apply] [findings] through [experimental approach / larger cohorts / clinical trials]."
- "Integration of [our approach/findings] with [complementary approach] could enable [combined capability]."

---

## Hedging Language Guide

### Strong Evidence (direct, data-supported)
- "These results **demonstrate** that..."
- "Our analysis **reveals** / **identifies** / **uncovers**..."
- "These findings **confirm** / **establish** / **provide evidence** that..."
- "The data **show** / **indicate** that..."

### Moderate Evidence (well-supported but not definitive)
- "These results **suggest** that..."
- "Our findings **are consistent with** [interpretation]."
- "This observation **supports the hypothesis** that..."
- "The data **point to** / **implicate** [factor] in [process]."
- "[Finding] **is in line with** previous reports of..."

### Preliminary Evidence (requires further validation)
- "These results **may indicate** / **may suggest** that..."
- "This observation **raises the possibility** that..."
- "[Finding] **is potentially consistent with** [interpretation]."
- "While **preliminary**, these results **hint at** [possibility]."
- "This **warrants further investigation** into [topic]."

### Speculative / Mechanistic (proposed explanations)
- "One possible explanation is that..."
- "This **could reflect** [mechanism], although [alternative] cannot be ruled out."
- "We **speculate** that [mechanism], based on [evidence]."
- "It is **tempting to speculate** that [interpretation], though [caveat]."
- "Further experiments will be needed to determine whether [proposed mechanism]."

### Hedging Calibration Rules
1. **Match hedging to evidence strength**: strong stats = strong language; trend-level = hedge
2. **No hedging on methods/data**: "We performed X" not "We attempted to perform X"
3. **Hedge interpretations, not observations**: "Gene X was upregulated" (observation, no hedge) vs. "Gene X may drive [process]" (interpretation, hedge)
4. **Avoid double hedging**: "may possibly suggest" -- choose one hedge
5. **Never overclaim**: "proves" is almost never appropriate; prefer "demonstrates" or "provides strong evidence"

---

## Hourglass Model: Discussion as the Bottom Half

The Discussion forms the bottom of the hourglass -- an inverted funnel expanding from specific to broad:

```
              ┌────────────┐              Summary of findings (the pivot)
              │  "We found" │              (1 paragraph)
              └──────────┘
           ┌───────────────────┐          Comparison with literature
           │  "Consistent with"│          (1-2 paragraphs)
           └─────────────────┘
        ┌──────────────────────────┐      Limitations & future directions
        │  "A key limitation..."   │      (1-2 paragraphs)
        └────────────────────────┘
     ┌─────────────────────────────────┐   Broader significance
     │  "This work demonstrates..."    │   (1 paragraph)
     └───────────────────────────────┘
```

### Mirror Relationship with Introduction
| Introduction (top) | Discussion (bottom) |
|---|---|
| Broad field context | Broad significance / impact |
| Specific problem | Specific limitations acknowledged |
| Gap statement | Gap addressed (summary of contribution) |
| Contribution claim | Evidence supporting contribution |
| Prior work cited | Comparison with that prior work |

### Structural Symmetry Checklist
- [ ] Discussion opening addresses the gap stated in Introduction
- [ ] Each prior work cited in Introduction is discussed or compared to
- [ ] Discussion closing returns to the broad context established in Introduction
- [ ] Contribution claims in Introduction are supported by evidence in Discussion
- [ ] Broad significance mirrors and extends Introduction's field context

---

## Common Pitfalls to Avoid

1. **Repeating Results**: Discussion interprets, it does not re-describe data or re-state statistics
2. **Introducing new data**: No new figures, tables, or analyses in Discussion
3. **Vague limitations**: "This study has some limitations" -- be specific and constructive
4. **Missing limitations**: Reviewers will add them; better to address proactively
5. **Overclaiming significance**: "revolutionary" / "paradigm shift" -- let the data speak
6. **Disconnected future directions**: Each future direction should tie to a limitation or finding
7. **Ignoring contradictory literature**: Acknowledge and explain conflicting prior work
8. **No broader significance**: Discussion should end broader than it began
9. **Over-interpretation**: Every interpretation must be traceable to a specific result
10. **Missing hedging**: Speculative interpretations need appropriate qualifying language
