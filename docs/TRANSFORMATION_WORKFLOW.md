# HR Data Quality & Transformation Workflow

## 1. Workflow Overview

```text
Raw HR CSV
1,530 records × 25 columns
        │
        ▼
Data Profiling
        │
        ▼
Deduplication
30 duplicate records removed
        │
        ▼
Category Standardization
Department / Gender / Employment Type
        │
        ▼
Date Transformation
Birth / Hire / Exit dates
        │
        ▼
Salary Transformation
Currency / symbols / K-suffix / text
        │
        ▼
Missing-Value Normalization
Placeholder strings → nulls
        │
        ▼
Data-Type Enforcement
        │
        ▼
Validation
        │
        ▼
Clean HR Dataset
1,500 records × 20 columns
```

---

# 2. Step 1 — Profile the Source

**Input**

`GTech_Solutions_HR_Data.csv`

- 1,530 records
- 25 columns

Initial profiling identifies:

- duplicate Employee_ID values
- inconsistent department labels
- mixed gender representations
- employment-type variants
- mixed date formats
- inconsistent salary representations
- placeholder values
- missing values

The profiling stage establishes the transformation requirements before cleaning begins.

---

# 3. Step 2 — Deduplicate Employee Records

### Rule

`Employee_ID` is treated as the primary employee identifier.

The source contains:

> **30 duplicate records**

The output contains:

> **1,500 unique Employee_ID values**

### Governance consideration

A simple deduplication operation is suitable for this portfolio workflow.

For production HR master data, duplicate records should be reviewed using:

- record completeness
- source-system authority
- record recency
- hire/exit information
- conflicting attributes

The project should therefore not claim that "keeping the first row" is universally the correct HR remediation rule.

---

# 4. Step 3 — Standardize Department

### Source

43 distinct department representations.

### Output

10 canonical departments:

```text
Customer Service
Executive
Finance
Human Resources
Legal
Marketing
Operations
Sales
Supply Chain
Technology
```

### Transformation principle

Normalize text before applying the mapping rules.

Conceptually:

```text
Raw Department
      ↓
Trim / Normalize Case
      ↓
Pattern-Based Mapping
      ↓
Canonical Department
```

### Business value

A standardized department dimension prevents the same business unit from being split across multiple categories in:

- headcount reports
- payroll analysis
- workforce planning
- salary comparisons

---

# 5. Step 4 — Standardize Gender

The source contains inconsistent representations.

The final workbook contains:

| Gender | Records |
|---|---:|
| Male | 765 |
| Female | 735 |

### Governance note

Some source values appear to use titles such as `Mr`, `Ms`, and `Mrs`.

Those should be treated cautiously.

A title is not an authoritative demographic field. For production use, the transformation should either:

- preserve the source value,
- map only explicit gender values,
- or use an approved organizational data standard.

---

# 6. Step 5 — Standardize Employment Type

The final output contains:

| Employment Type | Records |
|---|---:|
| Full-Time | 522 |
| Contract | 503 |
| Part-Time | 475 |

These are the actual categories in the cleaned workbook.

The transformation consolidates inconsistent source representations into these canonical values.

---

# 7. Step 6 — Transform Date Fields

Primary date fields:

- Date_of_Birth
- Date_of_Hire
- Date_of_Exit

The source includes several representations such as:

```text
6/6/1991
27-Apr-80
22/02/1991
25.06.2018
```

Power Query is used to parse the source values into consistent date representations.

### Validation principle

A parsed date is not automatically a factually correct date.

Ambiguous values require:

- locale assumptions
- source-system context
- or business-owner validation

---

# 8. Step 7 — Transform Salary

The source salary field contains representations such as:

```text
N609,000
#219,000
124.0K
334000
NGN 631000
```

The transformation:

1. converts the source to text where necessary
2. removes formatting characters
3. handles suffix-based representations
4. converts the result to numeric form
5. applies an appropriate numeric data type

### Critical validation distinction

```text
String can be parsed
        ≠
Salary is business-valid
```

The cleaned output contains an observed minimum salary of:

> **₦94**

This should be flagged for source/business validation rather than automatically accepted as a realistic payroll value.

---

# 9. Step 8 — Normalize Missing Values

Placeholder values are converted into proper nulls.

Examples:

```text
TBD
NIL
Unknown
N/A
Not Available
```

The transformation preserves legitimate missingness.

For example:

> Exit_Date = null

does not necessarily mean a bad record; an active employee normally has no exit date.

---

# 10. Step 9 — Apply Data Types

The output uses appropriate representations for:

- identifiers → text
- names → text
- categories → text
- dates → date
- numeric measures → numeric

This ensures that downstream analytical tools can perform:

- aggregations
- filtering
- date arithmetic
- grouping
- sorting

without repeatedly repairing the source.

---

# 11. Step 10 — Validate the Output

The final dataset is checked for:

### Structural checks

- 1,500 rows
- 20 columns
- unique Employee_ID

### Categorical checks

- 10 canonical departments
- 3 employment types
- standardized gender values

### Numeric checks

- salary is numeric
- experience is numeric
- leave balance is numeric

### Missingness checks

- null counts are documented
- placeholder strings are removed where targeted

### Date checks

- date fields are represented consistently
- invalid parsing errors are not carried into the final dataset

---

# 12. Output

Final dataset:

> **1,500 records × 20 columns**

The cleaned workbook is intended as an analytical-ready output of the transformation process.

It is not an authoritative HR master database.

---

# 13. Refreshability

When the raw CSV changes, the Power Query workflow can be refreshed and the transformation steps reapplied.

The workflow should then be revalidated for:

- new department values
- new employment-type values
- new placeholders
- new date formats
- salary anomalies
- duplicate Employee_ID values
- changes in null patterns

This is the key difference between a repeatable transformation pipeline and manually cleaning a spreadsheet once.
