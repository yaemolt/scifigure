# Workflow

## Phase Order

Use this fixed order:

1. create run directory
2. capture user input
3. run optional input analysis
4. run planning phase
5. require plan gate pass
6. require user approval of refined plan
7. run execution phase
8. run final review
9. route failures by category until all scores are above 80

## Input Routing

### Text-only request

Go directly to planning.

### Reference-style images

Run `style-analyzer` before or alongside planning.

### PNG or SVG assets

Run `asset-analyzer` before execution planning finalization.

## Planning Gate

Execution may not start until:

- `critic` has scored all plan dimensions above 80
- the user has approved the refined plan

## Clarification Rules

- Use one question per round
- Cap total rounds at 8
- Log each question-answer pair in `04-qa-log.md`

## Final Review Loop

Route issues by class:

- layout -> `architect`
- style -> `drawer`
- copy -> `writer`
- structure or editability -> `xml-drawer`

