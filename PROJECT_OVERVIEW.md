# Project at a glance

**HR Data Quality & Transformation: Power Query Case Study**
Self-directed project, built around a synthetic HR dataset styled after a real operational export.

**Records:** 1,530 → 1,500 (30 duplicates removed)
**Columns:** 25 → 20
**Quality score:** 72.2% → 99.16% across five tracked dimensions

---

## What was wrong with the raw file

- 30 duplicate employee records
- 43 different spellings for 10 actual departments
- 7+ date formats mixed in the same columns
- 8 different salary formats, some with currency symbols and "K" suffixes attached
- 400+ placeholder values (`TBD`, `NIL`, `Unknown`, `N/A`) standing in for real nulls
- 15 employment-type variants, 12 gender variants
- 28% of fields typed incorrectly for analysis (dates and numbers stored as text)

## What the cleaned file gives you

A 1,500-row, 20-column dataset with unique employee IDs, ten canonical departments, parsed dates, numeric salaries, and honestly-documented nulls. Ready to drop into Power BI, Tableau, or Excel without any further repair work.

It supports headcount reporting, payroll and compensation analysis, attrition and tenure tracking, performance review analysis, workforce planning, and compliance reporting on diversity and employment type.

## Quality scorecard

| Dimension | Before | After |
|---|---:|---:|
| Completeness | 94.2% | 98.1% |
| Validity | 87.0% | 99.8% |
| Uniqueness | 96.0% | 100.0% |
| Consistency | 62.0% | 98.0% |
| Accuracy | 72.0% | 100.0% |

Full methodology and the honest limitations behind these numbers are in [`docs/DATA_QUALITY_SCORECARD.md`](docs/DATA_QUALITY_SCORECARD.md). A clean dataset isn't automatically a factually correct one, and that document explains where the line is.

## Files in this repo

| File | What it's for |
|---|---|
| `README.md` | Full write-up: the problem, the approach, what I'd flag for review |
| `docs/TRANSFORMATION_WORKFLOW.md` | Step-by-step process with the actual Power Query M code |
| `docs/DATA_QUALITY_SCORECARD.md` | Full quality assessment and validation checklist |
| `data/GTech_Solutions_HR_Data.csv` | Original raw file, kept for audit and reproducibility |
| `output/GTech_Solutions_HR_Cleaned.xlsx` | The cleaned, analysis-ready dataset |

## Who this is for

**Analysts:** load `output/GTech_Solutions_HR_Cleaned.xlsx` straight into your tool of choice.
**Anyone reviewing the process:** start with `docs/TRANSFORMATION_WORKFLOW.md`.
**Anyone reviewing the outcome:** start with `docs/DATA_QUALITY_SCORECARD.md`, then the README.
