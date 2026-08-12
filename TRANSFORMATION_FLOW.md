# Transformation Flow

```text
Raw HR file
    |
    v
Profile before cleaning
    |
    +--> duplicate IDs
    +--> pseudo-nulls
    +--> mixed dates
    +--> mixed salary formats
    +--> inconsistent categories
    |
    v
Power Query transformations
    |
    +--> type conversion
    +--> standardization
    +--> null handling
    +--> calculated fields
    |
    v
Validation
    |
    +--> ID uniqueness
    +--> date and salary errors
    +--> category values
    |
    v
Clean analysis-ready table
```

## Key rule

Completeness and consistency are different checks. A column can have no blanks and still contain values that cannot be grouped or compared reliably.

The project therefore profiles the raw file before applying transformations and validates the cleaned output after the transformation steps.
