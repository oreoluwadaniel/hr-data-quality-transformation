# HR Data Quality & Transformation: A Power Query Case Study

I built this project around a question every HR or people analytics team eventually runs into: what do you actually do when the data you're handed can't be trusted yet?

This is a self-directed case study, not paid client work. I used a synthetic HR export styled after a real operational dataset: the kind with duplicate employee records, department names spelled six different ways, and salary fields that mix currency symbols with plain numbers. I built it around a fictional company, GTech Solutions, to keep the scenario grounded. Everything else, including the workflow, the decisions, and the trade-offs, is handled the way I'd approach it on a live team.

**Tools:** Excel, Power Query
**Input:** 1,530 records × 25 columns
**Output:** 1,500 records × 20 columns, 99%+ across the quality dimensions I tracked

---

## The problem

HR data rarely shows up clean. It comes out of whatever system HR happens to be using, gets exported by whoever's on shift, and picks up years of inconsistent habits along the way: one person types "HR," another types "Human Resources," a third types "human resources dept." None of that is malicious. It's just what happens when data entry isn't anyone's full-time job.

The catch is that none of it shows up until someone tries to use the data. Headcount reports come out wrong because duplicate records inflate the count. Payroll totals don't reconcile because half the salary field is text. Tenure analysis breaks because Excel can't agree on whether "6/6/91" means June 6th or a typo. By the time it reaches a dashboard, nobody trusts the numbers, and rebuilding that trust takes far longer than cleaning the data would have.

So the goal here wasn't "make the spreadsheet look nicer." It was to build a transformation process I could explain, defend, and re-run the next time the source file changes.

## What I did

I used Power Query to profile the raw file, then work through each problem in order: duplicates first, then categorical mess (departments, gender, employment type), then dates, then salaries, then the placeholder values people use in place of a real "I don't know." Every step is documented in [`docs/TRANSFORMATION_WORKFLOW.md`](docs/TRANSFORMATION_WORKFLOW.md), including the actual M code.

```
Raw HR export → profiling → deduplication → category standardization
→ date parsing → salary normalization → missing-value handling
→ data typing → validation → clean dataset
```

## Before and after

| Metric | Before | After |
|---|---:|---:|
| Records | 1,530 | 1,500 |
| Columns | 25 | 20 |
| Duplicate employee IDs | 30 | 0 |
| Department name variants | 43 | 10 canonical values |
| Employment type variants | multiple, inconsistent | 3 (Full-Time, Contract, Part-Time) |
| Gender variants | 12, including title-based guesses | 2, standardized |
| Date formats | 7+, mixed in the same column | consistent date values |
| Salary formats | 8, mixing currency symbols, "K" suffixes, plain text | numeric, ready to aggregate |

I also scored the dataset against five quality dimensions before and after: completeness, validity, uniqueness, consistency, and accuracy. The full breakdown, including exactly which fields still have gaps and why, is in [`docs/DATA_QUALITY_SCORECARD.md`](docs/DATA_QUALITY_SCORECARD.md).

## The fixes that mattered most

**Thirty duplicate employee records.** Same Employee_ID, appearing twice. I removed them, but I want to be upfront that "keep the first row" is a reasonable rule for a portfolio project and not necessarily the right one for production HR data. A real duplicate-resolution policy needs input from whoever owns the source system.

**Forty-three ways of spelling ten department names.** "Sales," "sales dept," "SALES," "Sales Department", all the same thing, all breaking any report grouped by department. I normalized case and whitespace, then mapped everything to ten canonical department names.

**Seven date formats living in one column.** `6/6/1991`, `27-Apr-80`, `22/02/1991`, `25.06.2018`, sometimes in the same field, for the same employee type. Power Query parsed these into consistent date values, but I'll flag the obvious risk: a date that parses successfully isn't automatically the correct date. Ambiguous formats need a locale assumption, and I made one. It should be checked against the source system before anyone treats it as ground truth.

**Salary values that ranged from clean numbers to `N609,000` to `124.0K`.** I stripped symbols, expanded suffixes, and converted everything to numeric. The output ranges from ₦94 to ₦3,490,000. That ₦94 is exactly the kind of thing a cleaning process can convert but not validate. It's flagged as a business-rule exception rather than quietly accepted, because a parsed number isn't the same thing as a plausible salary.

**Placeholder text standing in for missing data.** `TBD`, `NIL`, `Unknown`, `N/A`, `Not Available`, over 400 instances across the file, all meaning "no value," none of them recognized as null by anything downstream. I converted them to proper nulls. I did *not* touch legitimate missing values like Exit_Date being blank for someone who's still employed. That blank is correct, not broken.

## What this project does and doesn't prove

This is the part I think matters most, and it's also the part a lot of "data cleaning" portfolio projects skip.

Cleaning data well means the file is consistent, typed correctly, deduplicated, and free of placeholder junk. It does not mean every value in it is factually true. A salary field can be perfectly numeric and still contain a typo from whoever entered it. A department field can be standardized and still be the wrong department if someone was miscoded at the source.

So I split the work into what I could actually establish and what I couldn't:

**I can show:** deduplication, category standardization, date parsing, salary normalization, missing-value handling, correct data typing, and a documented, repeatable process behind all of it.

**I can't show, and don't claim to:** that every individual field is factually accurate, that this qualifies as an authoritative HR master record, or that it's ready for payroll reconciliation without a business owner checking the edge cases I flagged above.

That distinction, between a dataset being *clean* and a dataset being *true*, is the one I'd want a hiring manager to notice, because it's the difference between an analyst who runs a script and one who thinks about what the output actually means.

## What the cleaned data supports

Headcount reporting by department, location, and employment type. Payroll and compensation analysis. Attrition and tenure tracking. Performance rating distributions. Workforce planning inputs like leave balances and org structure. Diversity and employment-type reporting for compliance purposes.

## Repository structure

```
HR-Data-Quality-Transformation/
├── README.md                          (you're reading it)
├── PROJECT_OVERVIEW.md                (one-page summary)
├── data/
│   └── GTech_Solutions_HR_Data.csv    (raw source file, 1,530 × 25)
├── output/
│   └── GTech_Solutions_HR_Cleaned.xlsx (cleaned output, 1,500 × 20)
└── docs/
    ├── TRANSFORMATION_WORKFLOW.md     (step-by-step process, with M code)
    └── DATA_QUALITY_SCORECARD.md      (full quality assessment)
```

## Data dictionary

| Field | Type | Notes |
|---|---|---|
| Employee_ID | Text | Unique after deduplication |
| First_Name / Last_Name | Text | |
| Gender | Text | Standardized to Male / Female |
| Date_Of_Birth | Date | Parsed from mixed source formats |
| State_of_Origin | Text | 70 missing |
| Dept | Text | 10 canonical values |
| Job_Title | Text | 30 missing |
| Hire_Date | Date | |
| Exit_Date | Date, nullable | Null for active employees (1,301 nulls) |
| Bank_Name | Text | 85 missing |
| Education_Level | Text | 100 missing |
| Manager_Employee_ID | Text | 241 missing |
| Emp Type | Text | Full-Time / Contract / Part-Time |
| Emp Status | Text | 62 missing |
| Exp | Numeric | Years of experience, 105 missing |
| Leave_Balance | Numeric | 62 missing |
| Performance_rating | Text | 177 missing |
| Location | Text | 37 missing |
| Monthly Gross Salary | Numeric | ₦94–₦3,490,000; low end flagged for review |

## How to use this

Open `output/GTech_Solutions_HR_Cleaned.xlsx` directly in Excel, Power BI, or Tableau. The columns are already typed correctly. If you want to see the transformation logic itself, `docs/TRANSFORMATION_WORKFLOW.md` walks through every step with the actual Power Query M code, and `data/GTech_Solutions_HR_Data.csv` is the untouched original if you want to trace any transformation back to source.

If the source file changes, the documented steps are meant to be re-run, not rebuilt from scratch. That's the point of writing them down instead of just fixing the spreadsheet once and moving on.

---

Built by **Daniel Olatunji**, Data Analyst working across fintech, SaaS, and e-commerce data. I write more about data quality, Power BI, and analytics workflows regularly.

Questions about the approach or want to talk data? Reach me at oluwafikayore@gmail.com.
