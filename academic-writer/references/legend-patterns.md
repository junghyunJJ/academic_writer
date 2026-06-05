# Figure and Table Legend Patterns

Use this reference when `section_type == "results"` and the user provides available figures or tables. "Available" includes actual files and user-provided descriptions, panel notes, statistics, or table metadata sufficient to draft a legend.

Patterns are synthesized from:
- `/Users/jungj2/Dropbox/Metadata/aiagent/DeepMAST/docs/paper/STAgent.md`
- `/Users/jungj2/Dropbox/Metadata/aiagent/DeepMAST/docs/paper/SpatialAgent.md`
- `/Users/jungj2/Dropbox/Metadata/aiagent/DeepMAST/docs/paper/MEDEA.pdf`

## Common Figure Legend Structure

Reference papers use a compact, information-dense pattern:

1. **Identifier and title**: `Figure X:` or `Fig. X:` followed by a concise claim-oriented title.
2. **One-sentence scope**: state what the whole figure establishes, not just what it contains.
3. **Panel-by-panel descriptions**: `(a)`, `(b-c)`, `(d-f)` blocks describe each panel in order.
4. **Methods context only when needed**: include the model, cohort, benchmark, assay, or analysis source required to understand the visual.
5. **Visual encoding notes**: define colors, axes, units, arrows, log scales, boxplot elements, error bars, and legends.
6. **Statistical notes**: name tests, sample sizes, replicate count, correction, and p-value notation when the panel reports comparisons.
7. **Abbreviations and caveats**: define uncommon abbreviations and explain unmatched, excluded, or unresolved categories.

## Common Table Legend Structure

Table legends are shorter than figure legends and usually provide:

1. **Identifier and descriptive title**: `Table X:` or `Supplementary Table X:`.
2. **Scope**: what rows and columns compare or summarize.
3. **Value semantics**: what each cell reports, including units and whether values are means, medians, counts, percentages, coefficients, test statistics, or p-values.
4. **Statistical notation**: thresholds, bolding, asterisks, confidence intervals, and multiple-testing correction.
5. **Directionality**: what positive/negative values mean for pairwise comparisons.
6. **Missing or special values**: define `NA`, dashes, unresolved entries, or unmatched categories.

## Writing Rules

### Main Figure Legends
- Use the reference paper's title-forward style: a short title that states the figure's role in the paper.
- Keep panel order strict and complete. Do not skip a panel unless it is explicitly unavailable.
- Prefer present tense for visual content: "Panel A shows...", "Colors denote...", "Box plots show...".
- Keep interpretation bounded. Legends may state what is shown and how to read it; broader biological or methodological implications belong in Results or Discussion.
- Include enough method context for standalone readability, but do not duplicate full Methods.

### Supplementary and Extended Data Figure Legends
- Use the same panel-by-panel pattern as main figures.
- Be more explicit about technical details, prompts, workflows, confusion matrices, supplementary reports, or quality-control outputs.
- If a supplementary item is descriptive only, a one-sentence legend is acceptable.

### Main Table Legends
- Start with what the table compares or summarizes.
- Define row and column semantics before statistical notation.
- Include units and sample sizes when not obvious from column headers.

### Supplementary Table Legends
- Can be concise, but must define value meaning and notation.
- For pairwise statistical tables, state the test, what the sign means, what parentheses contain, and how significance is marked.
- Distinguish legend text from table contents. The legend describes what the Supplementary Table contains; large table values live in a linked CSV/TSV artifact rather than in the legend block.
- A Supplementary Table is large when it has `>20 rows` or more than `8 columns`. Large tables default to `supplementary/supplementary_table_X.csv`; use TSV only when the user provides TSV or explicitly requests TSV.
- If the generated artifact path, row/column semantics, units, or value meanings are incomplete, keep known legend text and mark unresolved fields as `[needs: ...]`.

### Supplementary Data Descriptions
- Supplementary Data is not a legend body. Represent Supplementary Data as a linked existing or generated data file plus a short Markdown description.
- Include identifier, file link, value semantics, and missing-field markers when needed.
- Do not invent Supplementary Data values, schemas, units, file names, or row/column meanings.

## Missing Information Policy

Generate a partial draft when an item is available but incomplete. Keep known content in normal prose and mark unknowns explicitly:

```markdown
Figure 2: [draft title]. (a) [known panel description]. (b) [known panel description]. [needs: sample size, statistical test, color encoding]

Missing fields:
- Figure 2A: sample size
- Figure 2B: statistical test and p-value format
- Whole figure: color legend
```

Do not invent:
- sample sizes, cohort composition, replicate counts
- test names, p-values, confidence intervals, error bar definitions
- color meanings, scale bars, units, panel labels
- abbreviations or dataset sources

## Output Blocks

When Results writing includes legends, output them after the Results prose in separate blocks:

```markdown
## Figure Legends

### Main Figures
Figure 1: ...

### Main Tables
Table 1: ...

### Supplementary Figures
Supplementary Figure 1: ...

### Supplementary Tables
Supplementary Table 1: [descriptive title]. Rows show [row semantics], columns show [column semantics], and values report [value semantics]. Full table: [supplementary/supplementary_table_X.csv](supplementary/supplementary_table_X.csv). [needs: unresolved units]

## Supplementary Materials

### Supplementary Table 1
[supplementary/supplementary_table_X.csv](supplementary/supplementary_table_X.csv). [Short description]. Values represent [value semantics]. [needs: missing column units]

### Supplementary Data 1
[path/to/supplementary_data_1.csv](path/to/supplementary_data_1.csv). [Short description]. Records represent [value semantics]. [needs: schema description]
```

Omit empty subsections. If all available items are incomplete, still provide partial drafts plus a consolidated missing-fields checklist.
