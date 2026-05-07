# Methods and Results Blueprint HITL Design

## Summary

Update `academic-writer` so Methods and Results sections require a stronger pre-draft planning stage. Before prose generation, the writer must collaborate with the user to produce and approve a structured Blueprint that combines:

- a human-readable hierarchical section skeleton, similar to the user's example image
- a verification-oriented matrix that maps each section block to the evidence, parameters, statistics, figures, and risks that constrain the draft

The goal is to prevent the writer from moving directly from interview answers to prose before the paper's Methods/Results structure has been explicitly shaped and approved by the user.

## Scope

In scope:

- Methods and Results only.
- Update the skill documentation and agent protocols, especially `academic-writer/agents/section-writer.md`.
- Add reviewer checks that enforce prose-to-Blueprint alignment.
- Update public workflow documentation in `academic-writer/SKILL.md` and `README.md`.

Out of scope for this iteration:

- Introduction and Discussion Blueprint gates.
- New runtime tools, new dependencies, or UI implementation.
- Automatic parsing of uploaded outline images.
- Changing RAG collection behavior.

## User Experience

The user-facing flow for Methods and Results becomes:

1. Complete input collection and the tiered interview.
2. Generate a draft Blueprint instead of prose.
3. Review the Blueprint with the user.
4. Iterate until the user explicitly approves.
5. Only then generate prose from the approved Blueprint.

The writer must treat the approved Blueprint as a contract. After approval, prose generation must not introduce:

- new subsections
- new claims or findings
- new method steps
- new figure/table placements
- new statistical comparisons
- new parameters or tools

If any of those become necessary during prose generation, the writer must pause and ask for Blueprint revision approval before continuing.

## Blueprint Format

Use a hybrid format: hierarchical outline first, matrix second.

### Results Blueprint

Hierarchical skeleton:

```markdown
## Results Blueprint

**Target length**: [word budget if known]
**Narrative arc**: [central Results story]

1. [Subsection title]
   1. [planned claim/finding]
   2. [supporting evidence]
2. [Subsection title]
   1. [planned claim/finding]
   2. [supporting evidence]
```

Matrix:

```markdown
| Block | Subsection | Claim/Finding | Evidence Source | Figure/Table | Statistics | Scope Limits |
|-------|------------|---------------|-----------------|--------------|------------|--------------|
| R1 | ... | ... | ... | ... | ... | no interpretation beyond data |
```

Required Results fields:

- `Block`
- `Subsection`
- `Claim/Finding`
- `Evidence Source`
- `Figure/Table`
- `Statistics`
- `Scope Limits`

### Methods Blueprint

Hierarchical skeleton:

```markdown
## Methods Blueprint

**Target length**: [word budget if known]
**Organization principle**: [chronological / pipeline / modular]

1. [Subsection title]
   1. [procedure or method step]
   2. [data/tool/parameter note]
2. [Subsection title]
   1. [procedure or method step]
   2. [data/tool/parameter note]
```

Matrix:

```markdown
| Block | Subsection | Procedure/Step | Data/Input | Tool/Version | Parameters | Output | Reproducibility Risk |
|-------|------------|----------------|------------|--------------|------------|--------|----------------------|
| M1 | ... | ... | ... | ... | ... | ... | missing parameter/version/etc. |
```

Required Methods fields:

- `Block`
- `Subsection`
- `Procedure/Step`
- `Data/Input`
- `Tool/Version`
- `Parameters`
- `Output`
- `Reproducibility Risk`

## Workflow Changes

Replace the current Methods and Results Step 2 outline protocol with a stronger gated sequence:

1. **Step 2a: Section Skeleton**
   - Propose subsection order, titles, target length, and narrative/organization principle.
   - User approval required before matrix generation.

2. **Step 2b: Blueprint Matrix**
   - Generate section-specific matrix.
   - Results matrix focuses on claim, evidence, figure/table, statistics, and scope limits.
   - Methods matrix focuses on procedure, data, tools/versions, parameters, outputs, and reproducibility risks.

3. **Step 2c: Gap and Risk Check**
   - Results: unsupported claims, orphan figures, missing p-values/sample sizes, over-interpretation risk.
   - Methods: missing tools/versions, missing parameters, unclear input/output transitions, reproducibility gaps.

4. **Step 2d: Strong HITL Approval Gate**
   - Ask for explicit user approval of the complete Blueprint.
   - Do not proceed to Step 3 prose unless approval is explicit.
   - If the user requests changes, revise the Blueprint and repeat the approval gate.

## Reviewer Enforcement

Update `section-reviewer.md` so Methods and Results reviews include Blueprint alignment checks:

- Every Results claim in prose must map to a Blueprint row.
- Every Results figure/table placement must match the approved Blueprint.
- Every reported statistic must be present in, or directly supported by, the Blueprint.
- Every Methods procedure must map to a Blueprint row.
- Every Methods tool/version/parameter required by the Blueprint must appear in prose.
- Any prose content not represented in the Blueprint is a major issue unless the user approved a Blueprint revision.

## Documentation Updates

Update `academic-writer/SKILL.md` and `README.md` to describe:

- the new Methods/Results Blueprint stage
- the hybrid outline + matrix format
- the strong HITL approval gate
- the rule that approved Blueprints constrain prose generation and review

The docs should continue to describe Introduction and Discussion using the existing interactive outline process, without implying the new Blueprint gate applies to them.

## Test Plan

Manual verification:

- Read `section-writer.md` and confirm Methods and Results Step 2 include the four Blueprint stages.
- Confirm Introduction and Discussion outline flows remain unchanged except for any shared wording needed for clarity.
- Confirm `section-reviewer.md` has explicit Methods/Results Blueprint alignment checks.
- Confirm `SKILL.md` and `README.md` describe the new behavior consistently.

Scenario checks:

- Results with a claim not in the Blueprint should be flagged as a major review issue.
- Results with a figure in the Blueprint but missing from prose should be flagged.
- Methods with a parameter listed in the Blueprint but omitted from prose should be flagged.
- Methods with a new tool introduced during prose but absent from the Blueprint should require Blueprint revision.

## Assumptions

- The first implementation will update documentation/protocol files only; it will not add executable code or new dependencies.
- The explicit approval phrase does not need to be exact, but the user must clearly approve the complete Blueprint before prose starts.
- The Blueprint should be generated after the existing interview intent confirmation, not before it.
- The format is Markdown-only so it remains compatible with the existing skill structure.
