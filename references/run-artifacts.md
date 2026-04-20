# Run Artifacts

Create a run directory using a stable pattern such as:

`runs/<timestamp>-<topic>/`

## Recommended Files

- `00-user-input.md`
- `01-intake-summary.md`
- `02-initial-plan.md`
- `03-critic-score-plan.md`
- `04-qa-log.md`
- `05-refined-plan.md`
- `06-style-analysis.md`
- `07-asset-analysis.md`
- `07-xml-modification-analysis.md`
- `08-layout-spec.md`
- `09-visual-spec.md`
- `10-copy-spec.md`
- `11-final.drawio.xml`
- `12-review-report.md`

## Rules

- Missing optional stages may omit their files
- Use `07-xml-modification-analysis.md` only when revising an existing `11-final.drawio.xml`
- File names should remain stable across runs
- All documents should follow the user's input language
