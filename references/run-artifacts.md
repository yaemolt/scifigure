# Run Artifacts

Create a run directory using a stable pattern such as:

`runs/<timestamp>-<topic>/`

## Recommended Files

- `work/00-user-input.md`
- `work/01-intake-summary.md`
- `work/02-initial-plan.md`
- `work/03-critic-score-plan.md`
- `work/04-qa-log.md`
- `work/05-refined-plan.md`
- `work/06-style-analysis.md`
- `work/07-asset-analysis.md`
- `work/07-xml-modification-analysis.md`
- `work/08-layout-spec.md`
- `work/09-visual-spec.md`
- `work/10-copy-spec.md`
- `output/11-final.drawio.xml`
- `output/12-review-report.md`

## Rules

- Missing optional stages may omit their files
- Use `work/07-xml-modification-analysis.md` only when revising an existing `output/11-final.drawio.xml`
- Use `work/` for intermediate documents and `output/` for deliverables
- File names should remain stable across runs
- All documents should follow the user's input language
