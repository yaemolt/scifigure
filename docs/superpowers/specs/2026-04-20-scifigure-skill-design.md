# SciFigure Skill Design

## 1. Overview

`scifigure` is a multi-agent skill for AI-assisted scientific figure production. Its primary output is a `draw.io`-compatible XML file that remains easy to edit after import. The workflow also produces a complete set of intermediate documents so the design process is reviewable, repeatable, and auditable.

This skill supports three input modes:

1. The user describes the desired figure in natural language.
2. The user uploads one or more reference images for style analysis.
3. The user uploads reusable `png` / `svg` assets that must be incorporated into the layout.

The skill must prioritize:

1. Editability in `draw.io`
2. Scientific clarity
3. Layout and style consistency
4. Visual fidelity where it does not reduce editability

## 2. Product Goal

Turn vague or partial scientific figure requests into a structured, multi-stage design workflow that:

- clarifies user intent,
- extracts style and asset constraints when needed,
- creates a reviewed execution plan,
- generates layout, color, and text specifications,
- emits editable `draw.io` XML,
- reviews the final XML with a scoring gate,
- stores all process artifacts in a single run directory.

## 3. Non-Negotiable Constraints

- Final deliverable must be importable into `draw.io` / `diagrams.net`.
- The generated XML must favor editability over visual overfitting.
- Every agent must have its own standalone character file in Markdown.
- Documents must follow the user's input language.
- Planning must pass a scoring gate before execution begins.
- Final XML must pass a scoring gate before the run is considered complete.
- Requirement clarification must use one-question-at-a-time interaction.
- Clarification is capped at 8 rounds.
- User plan review accepts only two classes of response:
  - `通过`
  - `修改意见`
- Reviewer failures must route back to the responsible execution agent:
  - layout issues -> `architect`
  - color/style issues -> `drawer`
  - text/copy issues -> `writer`
  - XML structure/editability issues -> `xml-drawer`

## 4. Workflow Summary

The skill has two main phases:

1. Planning phase
2. Execution phase

Optional preprocessing steps may run before planning or before execution depending on the input mode.

### 4.1 Planning phase

Core chain:

`planner -> critic -> interviewer(if needed) -> planner(refined) -> user review`

Outputs:

- `initial-plan.md`
- `critic-score-plan.md`
- `qa-log.md` if clarification is needed
- `refined-plan.md`

Gate:

- All planning dimensions must score above 80 before execution can begin.

### 4.2 Execution phase

Core chain:

`architect -> drawer -> writer -> xml-drawer -> reviewer`

Outputs:

- `layout-spec.md`
- `visual-spec.md`
- `copy-spec.md`
- `final.drawio.xml`
- `review-report.md`

Gate:

- All final review dimensions must score above 80 before the run is considered complete.

## 5. Input Modes

### 5.1 Text-only request

Use the planning phase directly.

The planner must extract:

- scientific purpose,
- target audience,
- intended usage context,
- figure type,
- component list,
- flow or causal structure,
- expected annotations,
- visual constraints,
- size or density hints,
- success criteria.

### 5.2 Reference style image

Add a style analysis step before or alongside planning.

Agent:

- `style-analyzer`

Output:

- `style-analysis.md`

The style analysis must capture:

- palette and contrast strategy,
- layout rhythm and grouping,
- spacing tendencies,
- text hierarchy,
- arrow and connector style,
- border and container style,
- visual density,
- scientific illustration tone.

This document becomes a constraint source for `architect`, `drawer`, and `writer`.

### 5.3 Reusable PNG / SVG assets

Add an asset analysis step before execution.

Agent:

- `asset-analyzer`

Output:

- `asset-analysis.md`

The asset analysis must capture:

- asset semantic role,
- scientific meaning,
- file type,
- original size,
- aspect ratio,
- transparency/background condition,
- suggested placement priority,
- scaling constraints,
- whether the asset should remain independent in the final diagram.

This document becomes a constraint source for `architect` and `xml-drawer`.

## 6. Planning Phase Design

### 6.1 `planner`

Responsibilities:

- interpret the user request,
- translate ambiguous requests into a structured plan,
- decide what is still unknown,
- create the initial plan,
- create the refined plan after clarification.

Inputs:

- user prompt,
- optional `style-analysis.md`,
- optional `asset-analysis.md`

Outputs:

- `initial-plan.md`
- `refined-plan.md`

The plan must define:

- scientific message,
- intended figure structure,
- major elements,
- expected text content,
- style constraints,
- asset constraints,
- layout assumptions,
- XML execution implications,
- known uncertainties.

### 6.2 `critic`

Responsibilities:

- score the plan,
- identify weak or ambiguous parts,
- decide whether clarification is required.

Output:

- `critic-score-plan.md`

Scoring dimensions:

- scientific clarity,
- task completeness,
- element definition clarity,
- layout sufficiency,
- style specificity,
- copy/text specificity,
- asset constraint clarity,
- draw.io executability,
- internal consistency,
- risk controllability.

Gate rule:

- If any dimension is `<= 80`, clarification is required.
- Only when every dimension is `> 80` may the workflow continue.

### 6.3 `interviewer`

Responsibilities:

- ask one question at a time,
- prioritize the highest-impact ambiguity,
- stop once all critical gaps are resolved or the round cap is hit.

Rules:

- maximum 8 rounds,
- each round asks exactly one focused question,
- all Q&A must be logged in `qa-log.md`,
- after clarification, control returns to `planner`.

### 6.4 User review contract

After `refined-plan.md` is generated, the user reviews it.

Allowed user responses:

- `通过`
- `修改意见`

The planner must revise the plan based on user modifications until the user passes the plan.

## 7. Execution Phase Design

### 7.1 `architect`

Responsibilities:

- define the canvas model,
- decide the global composition,
- define regions, alignment, spacing, and element placement,
- integrate uploaded `png/svg` assets with semantic and size constraints.

Output:

- `layout-spec.md`

The layout spec must define:

- canvas width and height,
- center or anchor logic,
- margins and safe areas,
- zone partitioning,
- placement of images, labels, arrows, legends, and grouped modules,
- alignment rules,
- scale relationships,
- asset placement decisions.

### 7.2 `drawer`

Responsibilities:

- define the visual language,
- choose colors and contrast,
- define shape, border, arrow, highlight, and background rules.

Output:

- `visual-spec.md`

The visual spec must define:

- palette,
- emphasis colors,
- semantic color usage,
- line styles,
- arrow styles,
- container styles,
- shadow/background usage if any,
- consistency rules.

### 7.3 `writer`

Responsibilities:

- define all text used in the figure,
- maintain scientific accuracy,
- keep labels concise and consistent,
- follow the user's input language unless explicitly changed.

Output:

- `copy-spec.md`

The copy spec must define:

- title,
- labels,
- annotations,
- group headings,
- explanatory text,
- terminology consistency,
- text length constraints.

### 7.4 `xml-drawer`

Responsibilities:

- translate layout, visual, and copy specs into `draw.io` XML,
- preserve editability,
- keep elements independent whenever possible.

Output:

- `final.drawio.xml`

XML generation rules:

- prefer editable text nodes,
- prefer independent shapes over flattened composites,
- prefer adjustable connectors and arrows,
- preserve grouping only when it improves editability,
- avoid overly complex structures that make downstream editing difficult,
- integrate external assets in a way that remains manageable in `draw.io`.

### 7.5 `reviewer`

Responsibilities:

- inspect the final XML,
- score the output,
- identify failure type and route it back to the correct agent.

Output:

- `review-report.md`

Review dimensions:

- scientific accuracy,
- layout clarity,
- visual consistency,
- text readability,
- alignment and spacing quality,
- connector and flow quality,
- draw.io compatibility,
- editability,
- asset usage quality,
- overall completeness.

Gate rule:

- If any dimension is `<= 80`, the review fails.
- The review report must classify each problem so the workflow can route it back correctly.

## 8. Failure Routing

When final review fails, route issues as follows:

- layout / spacing / placement / composition -> `architect`
- palette / contrast / visual style / color hierarchy -> `drawer`
- labels / annotations / terminology / text overflow -> `writer`
- XML structure / editability / draw.io compatibility / connector mechanics -> `xml-drawer`

After revision, `reviewer` runs again until all scores are above 80 or the workflow is externally stopped.

## 9. Run Directory Contract

Each run must create a dedicated folder, for example:

`runs/<timestamp>-<topic>/`

Recommended artifact set:

- `00-user-input.md`
- `01-intake-summary.md`
- `02-initial-plan.md`
- `03-critic-score-plan.md`
- `04-qa-log.md`
- `05-refined-plan.md`
- `06-style-analysis.md`
- `07-asset-analysis.md`
- `08-layout-spec.md`
- `09-visual-spec.md`
- `10-copy-spec.md`
- `11-final.drawio.xml`
- `12-review-report.md`

Rules:

- Missing optional stages may omit their files.
- File naming should stay stable across runs.
- All documents must use the user's input language.

## 10. Skill Package Structure

Recommended skill layout:

```text
scifigure/
├── SKILL.md
├── agents/
│   ├── planner.md
│   ├── critic.md
│   ├── interviewer.md
│   ├── style-analyzer.md
│   ├── asset-analyzer.md
│   ├── architect.md
│   ├── drawer.md
│   ├── writer.md
│   ├── xml-drawer.md
│   └── reviewer.md
├── references/
│   ├── workflow.md
│   ├── planning-rubric.md
│   ├── review-rubric.md
│   ├── style-analysis-rubric.md
│   ├── asset-analysis-rubric.md
│   ├── drawio-xml-guidelines.md
│   └── run-artifacts.md
└── assets/
    └── templates/
```

## 11. Character File Requirements

Each agent character file must define:

- mission,
- input documents,
- output documents,
- decision priorities,
- scoring or acceptance criteria if applicable,
- handoff conditions,
- failure reporting expectations,
- language policy,
- constraints unique to that role.

The agent files should be operational, not merely descriptive.

## 12. Design Principles

- Prefer structured intermediate artifacts over hidden reasoning.
- Prefer editability over static visual perfection.
- Prefer explicit review gates over silent continuation.
- Prefer one focused clarification question over multi-question dumps.
- Prefer role separation over one giant prompt.
- Prefer stable file naming and run records over ad hoc output.

## 13. Next Implementation Step

Turn this design into the actual skill package:

1. Create `SKILL.md`
2. Create all agent character files
3. Create rubric references
4. Create any run-folder templates needed by the workflow
5. Validate the final skill structure

