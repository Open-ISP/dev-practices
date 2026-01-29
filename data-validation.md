# Data Validation, Missing Data Handling, and Flexible Inputs

This document outlines our standard development practices for data validation, handling missing data, and supporting flexible input datasets. These practices will initially be adopted for the **templater** and **translator** modules in the ISPyPSA package.

## Problem Statement

Without a standardised approach to missing data:

- Functions behave inconsistently when input DataFrames are `None`, empty, or have no rows
- Code becomes cluttered with defensive checks like `if df is None` or `if df.empty`
- It is unclear how functions should handle partially missing data (missing columns or `NaN` values)
- Users cannot easily provide simplified datasets containing only the tables or columns relevant to their use case

## Standard Approach: Schema-Based Data Handling

### External Inputs vs Internal Representation

This approach distinguishes between two contexts:

| Context | Description |
|---------|-------------|
| **External inputs** | What users provide to the module. Optional tables and columns may be omitted entirely. |
| **Internal representation** | What functions within the module operate on, after schema enforcement. |

```
┌─────────────────────┐      ┌─────────────────────┐      ┌─────────────────────┐
│   External Input    │      │  Schema Enforcement │      │ Internal Functions  │
│                     │ ---> │    (at boundary)    │ ---> │                     │
│ Optional tables and │      │ Adds missing tables │      │ All tables present  │
│ columns may be      │      │ and columns with    │      │ All columns present │
│ missing             │      │ empty rows or NaNs  │      │ (may have no rows)  │
└─────────────────────┘      └─────────────────────┘      └─────────────────────┘
```

### Core Principle: Internal Representation

**Within module boundaries, missing data is always represented as a DataFrame with no rows but a complete set of expected columns conforming to the defined schema.**

This "all columns, no rows" approach applies only *after* schema enforcement and ensures that:

- DataFrame operations handle missing data gracefully without special-case logic
- Internal functions always receive inputs with a predictable structure
- Code remains clean and readable without excessive null checks

Internal functions are also expected to follow this convention when returning empty data—outputs should always include all expected columns, even when there are no rows.

Users are not required to provide empty DataFrames for optional tables—they simply omit them. Schema enforcement at the module boundary transforms user inputs into the standardised internal representation.

### Schema Definition

Each workflow step defines a schema specifying:

| Element | Description                                                                                            |
|---------|--------------------------------------------------------------------------------------------------------|
| **Tables** | All input tables for the workflow step                                                                 |
| **Columns** | All expected columns within each table                                                                 |
| **Data types** | Allowed data types for each column                                                                     |
| **Allowed values** | Valid value sets to protect against typos or invalid inputs with potential for table cross referencing |
| **Required vs Optional** | Whether each table and column is compulsory or optional                                                |

### NaN values in compulsory columns

**Compulsory columns should not permit NaN values.** If a column can contain `NaN` values, the data it represents is effectively optional and the column should be defined as such. This distinction ensures the schema accurately reflects data requirements—compulsory columns guarantee complete data, while optional columns explicitly signal that missing values are acceptable and handled by the model.

### Schema format

The format for defining schemas is yet to be confirmed. We expect to use YAML files, which provide a human-readable format that can be version controlled and easily reviewed.

### Schema evolution

Schema versioning and migration strategies will be addressed as the implementation matures. During the pilot phase, we will track any schema changes and their impact to inform a more comprehensive approach to backwards compatibility and version management.

### Schema Enforcement

Schema enforcement occurs at module entry points, transforming external inputs into the standardised internal representation. This is the only point where missing optional elements are handled—internal functions never need to check for missing tables or columns.

#### Compulsory Elements

- Missing compulsory tables or columns raise a descriptive error
- Data type violations raise a descriptive error
- Allowed value violations raise a descriptive error

#### Optional Elements

- Missing optional tables are added as empty DataFrames with all schema-defined columns
- Missing optional columns are added and populated with `NaN` values

After enforcement, all internal functions can rely on receiving complete DataFrames with the expected structure.

#### Error message conventions

Error messages should identify the table name, column name (if applicable), and the nature of the violation. This consistency aids debugging and helps users quickly identify and resolve issues with their input data.

Examples:

- Missing table: `"Missing required table 'generators'"`
- Missing column: `"Table 'generators': missing required column 'capacity_mw'"`
- Type violation: `"Table 'generators', column 'capacity_mw': expected numeric type, got string"`
- Value violation: `"Table 'generators', column 'fuel_type': invalid value 'coal_fired' (allowed: coal, gas, solar)"`
- NaN in required column: `"Table 'generators', column 'capacity_mw': NaN values not permitted in required column (rows: 3, 7, 12)"`

## Testing Requirements

Tests cover both schema enforcement and internal function behaviour.

### Schema Enforcement Tests

Schema enforcement must be tested to ensure:

- Compulsory tables and columns that are missing raise appropriate errors
- Optional tables and columns that are missing are correctly added
- Data type and allowed value violations raise appropriate errors

### Public Function Tests

All public functions must be tested to ensure correct behaviour with missing data. Tests should verify:

#### Empty DataFrame Handling

- Functions accept DataFrames with no rows without raising errors
- Outputs are consistent with the modelling methodology when given empty inputs

#### NaN Value Handling

- Functions handle optional columns containing `NaN` values gracefully
- Outputs are consistent with the modelling methodology

#### Combinatorial Testing

Public functions with multiple input tables must be tested with various combinations of missing data:

- Table A empty, Table B populated
- Table A populated, Table B empty
- Both tables empty

Some input combinations may be more practically tested by directly calling private sub-functions rather than through the public interface. This is acceptable provided the overall behaviour of the public function is adequately covered.

#### Private Function Test Suites

Going forward, unit test suites for private functions should include tests to verify that:

- The function handles empty input DataFrames gracefully
- The function returns empty data according to the "all columns, no rows" principle

Achieving this level of coverage retrospectively for existing code may not be feasible, so this requirement applies to new and significantly modified functions.

## Documentation

Schema-based table documentation should specify:

| Element | Description |
|---------|-------------|
| **Table purpose** | What the table represents and how it is used in the model |
| **Column definitions** | Name, data type, allowed values, and whether required or optional |
| **Empty table behaviour** | How the model behaves when the table contains no rows |
| **NaN column behaviour** | How the model behaves when optional columns contain `NaN` values |

This documentation ensures users understand the implications of omitting optional data and can make informed decisions about which tables and columns to include in their datasets.

## Benefits

Adopting this approach provides:

| Benefit | Description |
|---------|-------------|
| **Data validation** | Invalid inputs are caught early with clear error messages |
| **Workflow flexibility** | Users can provide simplified datasets with only relevant tables/columns |
| **Robustness** | Functions handle edge cases gracefully (e.g., removing all generators for greenfield optimisation) |
| **Documentation foundation** | Schemas serve as the basis for comprehensive table documentation |
| **Code clarity** | Reduced defensive checks improve readability and maintainability |

## Implementation Plan

Adopt a staged implementation approach:

1. **Pilot**: Implement schema validation on a single ISPyPSA table
2. **Review**: Gather feedback and refine the approach
3. **Extend**: Roll out to remaining tables in templater and translator modules
4. **Standardise**: Apply learnings to establish patterns for other modules