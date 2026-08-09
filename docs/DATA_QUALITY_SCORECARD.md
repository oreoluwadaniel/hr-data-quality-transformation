# HR Data Quality Assessment

## Executive Summary

The project evaluates the transformation of a synthetic HR dataset from:

> **1,530 records × 25 columns**

to:

> **1,500 records × 20 columns**

The assessment focuses on five project-defined dimensions:

1. Completeness
2. Validity
3. Uniqueness
4. Consistency
5. Data-Type Conformity

These dimensions are used as an internal project assessment framework. They should **not** be interpreted as a universal industry certification or an objective measurement of factual accuracy.

---

# 1. Completeness

The source contains missing values across several fields.

Examples include:

| Field | Missing after transformation |
|---|---:|
| Exit_Date | 1,301 |
| Manager_Employee_ID | 241 |
| Performance_rating | 177 |
| Education_Level | 100 |
| Exp | 105 |
| Bank_Name | 85 |
| State_of_Origin | 70 |
| Emp Status | 62 |
| Leave_Balance | 62 |
| Location | 37 |
| Job_Title | 30 |

### Interpretation

Not every null represents an error.

For example:

- Exit_Date can legitimately be null for employees who have not exited.
- Manager_Employee_ID may be unavailable for certain organizational roles.
- Performance ratings may not exist for every employee.

The correct treatment is therefore to **document missingness rather than blindly impute values**.

---

# 2. Validity

The transformation addresses structural and formatting validity issues including:

- mixed date representations
- salary formatting
- placeholder values
- inconsistent categorical labels
- numeric fields stored as text

### Important limitation

A syntactically valid value is not necessarily factually valid.

For example:

> `124.0K` can be successfully converted to `124000`.

That proves the value was parsable.

It does not prove that the source salary was correct.

---

# 3. Uniqueness

### Before

- 1,530 records
- 30 duplicate records

### After

- 1,500 records
- 0 duplicate Employee_ID values

### Result

The final dataset contains one record per Employee_ID.

### Governance limitation

The transformation removes duplicate records, but a production HR system should establish an authoritative duplicate-resolution policy before deleting conflicting employee records.

---

# 4. Consistency

## Department

| Measure | Before | After |
|---|---:|---:|
| Department representations | 43 | 10 canonical values |

Canonical values:

- Customer Service
- Executive
- Finance
- Human Resources
- Legal
- Marketing
- Operations
- Sales
- Supply Chain
- Technology

---

## Employment Type

Final values:

| Type | Records |
|---|---:|
| Full-Time | 522 |
| Contract | 503 |
| Part-Time | 475 |

---

## Gender

Final values:

| Gender | Records |
|---|---:|
| Male | 765 |
| Female | 735 |

### Governance note

The raw dataset contains title-like values. Title-based gender inference should be treated as an assumption and should not be used as an authoritative demographic classification without organizational approval.

---

# 5. Data-Type Conformity

The cleaned dataset applies appropriate analytical types.

Examples:

| Field | Final representation |
|---|---|
| Employee_ID | Text |
| Date_Of_Birth | Date |
| Hire_Date | Date |
| Exit_Date | Date/null |
| Exp | Numeric |
| Leave_Balance | Numeric |
| Monthly Gross Salary | Numeric |
| Department | Categorical text |
| Employment Type | Categorical text |

### Why it matters

Correct data types support reliable:

- aggregation
- sorting
- filtering
- date calculations
- BI ingestion
- analytical modeling

---

# 6. Salary Business Validation

The cleaned salary range is:

> **₦94 to ₦3,490,000**

The minimum value of ₦94 is a clear **business-rule exception candidate**.

The transformation successfully converted the value into a number, but numeric conversion alone cannot establish whether the salary is commercially plausible.

### Recommended production rule

Define an HR/payroll-approved plausibility range and classify records outside that range as:

> **Business Validation Exception**

rather than silently modifying them.

This is an important distinction between:

> **Data transformation**

and:

> **Data validation.**

---

# 7. Quality Assessment

Rather than presenting a single "99% quality" figure as an objective truth, the project should report the dimensions separately:

| Dimension | Assessment |
|---|---|
| Completeness | Improved; remaining nulls documented |
| Validity | Formatting and structural issues transformed |
| Uniqueness | 30 duplicate records removed; 0 duplicate IDs remain |
| Consistency | Major categorical variants standardized |
| Data-Type Conformity | Output fields assigned analytical types |
| Business Accuracy | **Not independently established from the synthetic source** |

The final distinction is essential.

No cleaning workflow can prove factual accuracy when there is no authoritative source against which the values can be reconciled.

---

# 8. Validation Checklist

### Passed / addressed

- Employee_ID uniqueness checked
- Duplicate records reduced from 30 to 0
- Department standardized to 10 canonical values
- Employment Type standardized to 3 output categories
- Gender standardized in final output
- Salary converted to numeric representation
- Date fields transformed
- Placeholder values normalized
- Missing-value patterns documented
- Output schema reduced to 20 analytical fields

### Requires business validation

- Salary plausibility
- Accuracy of employee attributes
- Accuracy of department assignment
- Accuracy of compensation values
- Accuracy of manager relationships
- Accuracy of demographic classifications
- Authoritative employment status

---

# 9. Key Data-Quality Lesson

The project demonstrates an important principle:

> **Cleaning data is not the same as proving that the data is true.**

A field can be:

- correctly typed,
- consistently formatted,
- non-null,
- unique,

and still be factually wrong.

Therefore a mature data-quality process separates:

```text
Profiling
   ↓
Transformation
   ↓
Structural Validation
   ↓
Business Validation
   ↓
Source Reconciliation
```

The current project covers the first three layers strongly and establishes a foundation for the latter two.

---

# 10. Recommended Extension

A stronger next version would add:

- salary plausibility rules
- foreign-key validation for Manager_Employee_ID
- automated exception reporting
- before/after quality metrics
- refresh history
- field-level data-quality thresholds
- data-quality monitoring dashboard

These are extensions, not prerequisites for the current portfolio case study.
