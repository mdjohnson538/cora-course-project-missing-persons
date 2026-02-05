# CoRA / UNO / DPAA  
## JSON Data Modeling & Schema Proposal  
### Missing, Unclaimed, and Unidentified Persons Datasets

---

## 1. Purpose and Motivation

This document proposes a JSON data modeling strategy for three datasets used in the **CoRA (Comingled Remains Analysis)** partnership between the University of Nebraska at Omaha (UNO) and the Defense POW/MIA Accounting Agency (DPAA). The work is part of a semester-long Data Visualization course project focused on responsible, transparent data design.

- Establish a **robust and visualization-friendly schema** that can be reliably consumed by a wide range of analytical workflows within the CoRA application. The proposed schema is designed to “play nicely” with downstream dashboards, summaries, and comparative visualizations—supporting tasks such as aggregation, faceting, filtering, and contextual comparison across case types. Importantly, the schema is intended to support **both individual record-level review** (e.g., navigable tables and case detail views) **and higher-level summarization and aggregation**, enabling users to move fluidly between detailed inspection and broader pattern analysis.
- Represent each dataset **exactly as provided** in CSV form  
- Enable deterministic CSV → JSON conversion  
- Preserve provenance and auditability  
- Support future **census-derived contextual enrichment** without altering or obscuring the original data

---

## 2. Core Design Principles

### 2.1 Exact Fidelity to Source CSVs

For this stage of the project:
- **Every JSON property corresponds 1:1 with a CSV column**
- **Column names are preserved verbatim**, including:
  - spaces
  - capitalization
  - punctuation (e.g., `Race / Ethnicity`, `ME/C Case`)
- **No derived, normalized, or renamed fields appear in raw records**

This ensures:
- perfect traceability back to the source files
- no ambiguity about what is observed vs. computed
- schema validation that directly reflects the CSV exports

---

### 2.2 Separate Schemas for Separate Record Types

Although the datasets share conceptual similarities, they represent **distinct record types** with different required fields.

| Dataset        | Distinct Fields |
|---------------|-----------------|
| Missing       | `DLC`, `Missing Age` |
| Unclaimed     | `DBF` |
| Unidentified  | `ME/C Case`, `Age From`, `Age To`, `Case` |

Using **three schemas** avoids:
- large conditional logic blocks
- optional-field sprawl
- misinterpretation of which fields apply to which records

This mirrors real-world data engineering practice in public-sector and forensic systems.

---

### 2.3 Raw Data vs. Derived Context (Why a Wrapper Exists)

The project anticipates augmenting records with **census-derived information** using geographic fields such as City, County, and State.

Rather than embedding derived values into the raw records, this proposal uses a **wrapper pattern**:

```json
{
  "recordType": "...",
  "data": { /* raw CSV-shaped object */ },
  "enrichment": { /* derived census/contextual data */ }
}
```

This separation ensures:
- raw data remains immutable and auditable
- derived context is clearly labeled and versionable
- dashboards can display both without conflating evidence with context

---

## 3. Dataset-Specific Raw Schemas  
(All fields listed below appear **exactly** in the provided CSVs)

---

## A. Missing Persons  
**Source file:** `MISSING PERSONS DATA.csv`

**CSV Columns Represented:**
- `Case Number`
- `DLC`
- `Legal Last Name`
- `Legal First Name`
- `Missing Age`
- `City`
- `County`
- `State`
- `Biological Sex`
- `Race / Ethnicity`
- `Date Modified`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Missing Persons — Raw CSV Schema",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "Case Number",
    "DLC",
    "Legal Last Name",
    "Legal First Name",
    "Missing Age",
    "City",
    "County",
    "State",
    "Biological Sex",
    "Race / Ethnicity",
    "Date Modified"
  ],
  "properties": {
    "Case Number": { "type": ["string", "null"] },
    "DLC": { "type": ["string", "null"] },
    "Legal Last Name": { "type": ["string", "null"] },
    "Legal First Name": { "type": ["string", "null"] },
    "Missing Age": { "type": ["string", "null"] },
    "City": { "type": ["string", "null"] },
    "County": { "type": ["string", "null"] },
    "State": { "type": ["string", "null"] },
    "Biological Sex": { "type": ["string", "null"] },
    "Race / Ethnicity": { "type": ["string", "null"] },
    "Date Modified": { "type": ["string", "null"] }
  }
}
```

---

## B. Unclaimed Persons  
**Source file:** `UNCLAIMED PERSONS DATA.csv`

**CSV Columns Represented:**
- `Case Number`
- `DBF`
- `Legal Last Name`
- `Legal First Name`
- `Biological Sex`
- `Race / Ethnicity`
- `City`
- `County`
- `State`
- `Date Modified`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Unclaimed Persons — Raw CSV Schema",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "Case Number",
    "DBF",
    "Legal Last Name",
    "Legal First Name",
    "Biological Sex",
    "Race / Ethnicity",
    "City",
    "County",
    "State",
    "Date Modified"
  ],
  "properties": {
    "Case Number": { "type": ["string", "null"] },
    "DBF": { "type": ["string", "null"] },
    "Legal Last Name": { "type": ["string", "null"] },
    "Legal First Name": { "type": ["string", "null"] },
    "Biological Sex": { "type": ["string", "null"] },
    "Race / Ethnicity": { "type": ["string", "null"] },
    "City": { "type": ["string", "null"] },
    "County": { "type": ["string", "null"] },
    "State": { "type": ["string", "null"] },
    "Date Modified": { "type": ["string", "null"] }
  }
}
```

---

## C. Unidentified Persons  
**Source file:** `UNIDENTIFIED PERSONS DATA.csv`

**CSV Columns Represented:**
- `Case`
- `ME/C Case`
- `DBF`
- `Age From`
- `Age To`
- `City`
- `County`
- `State`
- `Biological Sex`
- `Race / Ethnicity`
- `Date Modified`

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Unidentified Persons — Raw CSV Schema",
  "type": "object",
  "additionalProperties": false,
  "required": [
    "Case",
    "ME/C Case",
    "DBF",
    "Age From",
    "Age To",
    "City",
    "County",
    "State",
    "Biological Sex",
    "Race / Ethnicity",
    "Date Modified"
  ],
  "properties": {
    "Case": { "type": ["string", "null"] },
    "ME/C Case": { "type": ["string", "null"] },
    "DBF": { "type": ["string", "null"] },
    "Age From": { "type": ["number", "null"] },
    "Age To": { "type": ["number", "null"] },
    "City": { "type": ["string", "null"] },
    "County": { "type": ["string", "null"] },
    "State": { "type": ["string", "null"] },
    "Biological Sex": { "type": ["string", "null"] },
    "Race / Ethnicity": { "type": ["string", "null"] },
    "Date Modified": { "type": ["string", "null"] }
  }
}
```

---

## 4. Optional Wrapper Schema for Census Enrichment

This wrapper allows census-derived or other contextual information to be displayed alongside core dataset fields **without altering the raw record structure**.

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "CoRA Person-Case Record Wrapper",
  "type": "object",
  "additionalProperties": false,
  "required": ["recordType", "data"],
  "properties": {
    "recordType": {
      "type": "string",
      "enum": ["missing", "unclaimed", "unidentified"]
    },
    "data": {
      "description": "Raw record matching exactly one of the CSV-shaped schemas above."
    },
    "enrichment": {
      "type": ["object", "null"],
      "description": "Optional census-derived or contextual data based on geographic fields. This data is explicitly derived and not part of the source CSVs.",
      "additionalProperties": true
    }
  }
}
```

---

## 5. Summary (Key Points to Emphasize)

- Every raw schema field appears **exactly** in the provided CSVs
- No renaming, normalization, or derivation occurs in raw records
- Census and contextual data are explicitly separated
- The model supports dashboard visualization, auditability, and future extension
- The design reflects real-world data governance best practices for sensitive forensic and humanitarian datasets
