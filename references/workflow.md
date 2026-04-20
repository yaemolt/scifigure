# Workflow

## Phase Order

Use this fixed order:

1. create run directory
2. capture user input
3. detect whether `output/11-final.drawio.xml` already exists
4. run optional input analysis
5. run XML modification analysis if modifying an existing XML
6. run planning phase
7. require plan gate pass
8. require user approval of refined plan
9. run execution phase
10. run final review
11. route failures by category until all scores are above 80

## Input Routing

### Text-only request

Go directly to planning.

### Reference-style images

Run `style-analyzer` before or alongside planning.

### PNG or SVG assets

Run `asset-analyzer` before execution planning finalization.

### Existing `output/11-final.drawio.xml`

Run `modifier` before planning.

`modifier` should inspect:

- `output/11-final.drawio.xml`
- `work/00-user-input.md`
- `work/08-layout-spec.md` if present
- `work/09-visual-spec.md` if present
- `work/10-copy-spec.md` if present
- [references/drawio-xml-guidelines.md](drawio-xml-guidelines.md)

`modifier` produces:

- `work/07-xml-modification-analysis.md`

Then `planner` uses that analysis as an additional planning input.

## Planning Gate

Execution may not start until:

- `critic` has scored all plan dimensions above 80
- the user has approved the refined plan
- if modifying an existing XML, `work/07-xml-modification-analysis.md` has been written first

## Clarification Rules

- Use one question per round
- Cap total rounds at 8
- Log each question-answer pair in `work/04-qa-log.md`

## Final Review Loop

Route issues by class:

- layout -> `architect`
- style -> `drawer`
- copy -> `writer`
- structure or editability -> `xml-drawer`
