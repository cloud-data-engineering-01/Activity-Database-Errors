# Slide: “Activity: Database Errors”


## How should we do the activity (15 minutes)

### 1) Group roles (30 seconds)

Assign quick roles:

* **Reader**: reads the assigned error types
* **Writer**: writes the example rows + solution
* **Checker**: asks “How do we prevent it next time?”
* **Speaker**: presents in 60–90 seconds

### 2) Use one simple database story (1 minute)

Tell groups to use the same mini-scenario so it’s easy:
**Scenario:** an online store with **Customers** and **Orders** tables.

### 3) For each assigned error, fill this template (8–10 minutes)

For every error type, groups produce 4 bullets:

1. **What it means (in your words)**
2. **What it looks like in a table** (show 1 bad row)
3. **Fix now** (clean/repair approach)
4. **Prevent later** (rule/constraint/design/validation)

### 4) Share (3–4 minutes)

Each group presents their assigned errors quickly:

* Show the bad row
* Explain impact in one sentence
* Give “fix now” + “prevent later”

---

# A complete sample “solution” 

Below is one ready-to-use set of examples that covers *all* error types.

## Example tables

### Customers

| customer_id | full_name    | email                 | phone             | country | birth_date   |
| ----------: | ------------ | --------------------- | ----------------- | ------- | ------------ |
|         101 | `Ali Hassan` | `ali@example.com`     | `+1 555-1000`     | `US`    | `1998-04-12` |
|         102 | `Ali Hassan` | `ALI@EXAMPLE.COM`     | `5551000`         | `USA`   | `1998/04/12` |
|         103 | `Sara\nKhan` | `sara@@mail.com`      | `+44 7700 900123` | `GB`    | `2030-01-01` |
|         104 | *(NULL)*     | `no-name@example.com` | `+49 151 234567`  | `DE`    | `1990-09-01` |

### Orders

| order_id | customer_id | order_date   |          amount | status_code |
| -------: | ----------: | ------------ | --------------: | ----------- |
|     9001 |         101 | `2025-01-10` |           49.99 | `SHIPPED`   |
|     9002 |         999 | `2025-01-11` |           15.00 | `PAID`      |
|     9003 |         101 | `2024-12-01` |          -20.00 | `PAID`      |
|     9004 |         103 | `2025-01-12` | *(text: “ten”)* | `SHIPPPED`  |

---

## Error types: what they look like + fixes

### 1) Data-entry errors (messy typing)

* **Looks like:** `full_name = " Ali Hassan "` (extra spaces)
* **Fix now:** trim spaces (cleanup)
* **Prevent later:** trim/standardize input in the app; store standardized version

### 2) Wrong data type

* **Looks like:** `amount = "ten"` (text stored where a number should be)
* **Fix now:** convert if possible; otherwise mark invalid and correct from source
* **Prevent later:** enforce numeric column type; reject non-numeric input

### 3) Wrong code (invalid category/status values)

* **Looks like:** `status_code = "SHIPPPED"` (typo), or unknown value
* **Fix now:** map typos to valid codes
* **Prevent later:** use a allowed-list rule (CHECK constraint) or a lookup table for statuses

### 4) Illogical data (doesn’t make real-world sense)

* **Looks like:** negative order `amount = -20.00` OR `birth_date` in the future
* **Fix now:** correct using business rules; flag suspicious rows
* **Prevent later:** add constraints like amount must be >= 0, birth_date must be in the past

### 5) Missing data

* **Looks like:** `full_name = NULL`
* **Fix now:** fill from trusted source, or mark record incomplete
* **Prevent later:** require mandatory fields (NOT NULL), or block checkout if missing

### 6) Duplicate data

* **Looks like:** two customers are basically the same person:

  * `ali@example.com` vs `ALI@EXAMPLE.COM` (same email, different case)
* **Fix now:** merge duplicates; choose a “golden record”
* **Prevent later:** store emails in a consistent format (lowercase) and enforce UNIQUE

### 7) Unacceptable characters in text

* **Looks like:** `full_name = "Sara\nKhan"` (newline inside a name field)
* **Fix now:** remove/replace control characters
* **Prevent later:** validate allowed characters; sanitize inputs

### 8) Censored or truncated data

* **Looks like:** a long note stored as `“Deliver to back do…”` because the column is too short
* **Fix now:** expand column size or move notes to the correct data type
* **Prevent later:** pick correct field sizes/types; test with realistic data lengths

### 9) Inconsistent data representation (same meaning, different formats)

* **Looks like:** country stored as `US`, `USA`, and dates `1998-04-12` vs `1998/04/12`
* **Fix now:** convert everything to one standard
* **Prevent later:** use one standard format (e.g., ISO dates), and use lookup tables for countries

### 10) Data is not atomic (one field contains multiple values)

* **Looks like:** a single column stores `“+1 555-1000, +1 555-2000”` or address “street, city, zip” all jammed together
* **Fix now:** split the data into separate columns/rows
* **Prevent later:** redesign schema (normalize): separate phone table, separate address fields

### 11) Broken references (relationships don’t match)

* **Looks like:** `Orders.customer_id = 999` but no customer 999 exists
* **Fix now:** find the correct customer_id or remove/repair the order
* **Prevent later:** enforce foreign keys so orders can’t point to non-existent customers

---

# Quick “presentation-ready” output 

When groups present, we can use this exact pattern:

1. **Our error:** Duplicate data
2. **Bad example row(s):** `ali@example.com` and `ALI@EXAMPLE.COM`
3. **Why it matters:** Reports count two customers, support emails the wrong record
4. **Fix now:** merge and standardize email case
5. **Prevent later:** UNIQUE constraint + lowercase normalization

---

