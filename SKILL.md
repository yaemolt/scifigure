---
name: scifigure
description: "AI-assisted scientific figure planning and draw.io XML generation with multi-agent workflow support. Use when Codex needs to turn a scientific figure request into editable diagrams.net/draw.io XML, especially for: (1) text-only figure descriptions, (2) reference-style image analysis, (3) integrating reusable PNG/SVG assets, (4) staged planning with critique and clarification gates, or (5) producing reviewable layout, style, copy, and XML artifacts."
---

# SciFigure

Create scientific figure designs through a staged, multi-agent workflow that prioritizes `draw.io` editability over one-shot visual mimicry.

## Quick Start

When this skill is invoked:

1. Detect the input mode:
   - text-only request
   - reference-style image input
   - reusable `png/svg` asset input
   - existing run with `11-final.drawio.xml` that needs modification
2. Create a run directory using the pattern from [references/run-artifacts.md](references/run-artifacts.md).
3. Write the raw request into `00-user-input.md`.
4. If `11-final.drawio.xml` already exists in the run directory, run `modifier` before planning.
5. Run the planning phase before any execution phase work.
6. Enter execution only after the planning gate passes and the user approves the refined plan.

## Workflow

Read [references/workflow.md](references/workflow.md) first.

The workflow is:

1. Optional preprocessing
   - use [references/style-analysis-rubric.md](references/style-analysis-rubric.md) if style reference images are provided
   - use [references/asset-analysis-rubric.md](references/asset-analysis-rubric.md) if reusable `png/svg` assets are provided
   - use `modifier` if the run already contains `11-final.drawio.xml`
2. Planning phase
   - `modifier` creates `07-xml-modification-analysis.md` when modifying an existing XML
   - `planner` creates `02-initial-plan.md`
   - `critic` scores the plan using [references/planning-rubric.md](references/planning-rubric.md)
   - if any score is `<= 80`, `interviewer` runs one-question-at-a-time clarification
   - `planner` creates `05-refined-plan.md`
   - user reviews and responds with `通过` or `修改意见`
3. Execution phase
   - `architect` creates `08-layout-spec.md`
   - `drawer` creates `09-visual-spec.md`
   - `writer` creates `10-copy-spec.md`
   - `xml-drawer` creates `11-final.drawio.xml`
   - `reviewer` scores the result using [references/review-rubric.md](references/review-rubric.md)
4. Revision loop
   - layout issues route to `architect`
   - style/color issues route to `drawer`
   - text issues route to `writer`
   - structure/editability issues route to `xml-drawer`

## Agent Files

Use these character files as the authoritative agent contracts:

- [agents/planner.md](agents/planner.md)
- [agents/critic.md](agents/critic.md)
- [agents/interviewer.md](agents/interviewer.md)
- [agents/style-analyzer.md](agents/style-analyzer.md)
- [agents/asset-analyzer.md](agents/asset-analyzer.md)
- [agents/modifier.md](agents/modifier.md)
- [agents/architect.md](agents/architect.md)
- [agents/drawer.md](agents/drawer.md)
- [agents/writer.md](agents/writer.md)
- [agents/xml-drawer.md](agents/xml-drawer.md)
- [agents/reviewer.md](agents/reviewer.md)

## Core Rules

- Work in the user's input language unless the user explicitly asks to change it.
- Favor explicit intermediate documents over hidden reasoning.
- Favor editable `draw.io` structures over visually dense but hard-to-edit output.
- If `11-final.drawio.xml` already exists, diagnose it before re-planning.
- Do not skip the planning gate.
- Do not ask more than one clarification question per round.
- Stop clarification after 8 rounds maximum.
- Require all plan scores to be above 80 before execution starts.
- Require all final review scores to be above 80 before the run passes.
- Keep role boundaries clean. Agents should hand off instead of freelancing outside scope.

## Draw.io Priority

Read [references/drawio-xml-guidelines.md](references/drawio-xml-guidelines.md) before generating XML.

Use these priorities:

1. editability
2. scientific clarity
3. layout coherence
4. visual consistency
5. visual fidelity that does not reduce editability

## Output Contract

Each run should create a stable artifact set in a single run directory. Use [references/run-artifacts.md](references/run-artifacts.md) for file names and stage expectations.

## Validation

Before considering the skill package complete:

1. generate `agents/openai.yaml`
2. run `scripts/quick_validate.py` from the `skill-creator` skill against this skill directory
