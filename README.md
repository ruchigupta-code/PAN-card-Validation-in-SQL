# PAN Number Validation Project

## Overview

This project focuses on cleaning and validating Permanent Account Numbers (PAN) using SQL in PostgreSQL. The goal is to ensure that PAN numbers conform to the official format and are categorized into **Valid PAN** or **Invalid PAN**. The process demonstrates SQL skills in data cleaning, regex-based validation, user-defined functions, and summary reporting.

## Dataset

The dataset contains a single column of PAN numbers (`Pan_Numbers`). The raw data may include:

* Missing values
* Duplicate PANs
* Leading/trailing spaces
* Lowercase letters
* Invalid formats

These issues are addressed step by step before validation.

## Validation Rules

A **valid PAN** must satisfy the following:

1. Exactly 10 characters long.
2. Format: `AAAAA1234A`

   * First 5 characters: uppercase alphabets.
   * Next 4 characters: numeric digits.
   * Last character: uppercase alphabet.
3. Additional constraints:

   * No adjacent identical characters (in alphabets or digits).
   * First 5 characters cannot be sequential (e.g., ABCDE).
   * Next 4 digits cannot be sequential (e.g., 1234).

Example of a valid PAN: `AHGVE1276F`

## Steps Implemented

### 1. Data Cleaning

* Remove missing PANs.
* Remove duplicates.
* Trim leading/trailing spaces.
* Convert to uppercase.

### 2. Functions for Validation

* **`fn_check_adjacent_repetition`**: Flags PANs with adjacent identical characters.
* **`fn_check_sequence`**: Flags PANs with sequential characters (like ABCDE or 1234).

### 3. Validation Logic

* Regex validation: `^[A-Z]{5}[0-9]{4}[A-Z]$`
* Apply sequence and repetition checks.
* Categorize into **Valid PAN** or **Invalid PAN**.

### 4. Summary Report

Generates counts of:

* Total records processed
* Valid PANs
* Invalid PANs
* Missing/incomplete PANs

## Example Queries

* **Check for duplicates:**

```sql
SELECT pan_number, COUNT(1)
FROM stg_pan_numbers_dataset
GROUP BY pan_number
HAVING COUNT(1) > 1;
```

* **Regex validation for format:**

```sql
SELECT pan_number
FROM pan_numbers_dataset_cleaned
WHERE pan_number ~ '^[A-Z]{5}[0-9]{4}[A-Z]$';
```

* **Final categorization:**

```sql
SELECT pan_number, status
FROM vw_valid_invalid_pans;
```

* **Summary report:**

```sql
WITH summary AS (
  SELECT
    (SELECT COUNT(*) FROM stg_pan_numbers_dataset) AS total_processed_records,
    COUNT(*) FILTER (WHERE vw.status = 'Valid PAN') AS total_valid_pans,
    COUNT(*) FILTER (WHERE vw.status = 'Invalid PAN') AS total_invalid_pans
  FROM vw_valid_invalid_pans vw
)
SELECT *,
       total_processed_records - (total_valid_pans + total_invalid_pans) AS missing_incomplete_pans
FROM summary;
```

## Insights

* PAN numbers often fail validation due to incorrect format, adjacent repetitions, or sequential patterns.
* Regex alone is insufficient; additional business rules ensure stronger validation.
* The summary provides a quick health check of the dataset.

## Purpose

This project demonstrates:

* SQL-based **data cleaning**
* Use of **regex and functions** for validation
* **CTEs and Views** for structured queries
* A **summary report** for stakeholders

This project is suitable for inclusion in a GitHub portfolio to showcase SQL data quality and validation skills.
