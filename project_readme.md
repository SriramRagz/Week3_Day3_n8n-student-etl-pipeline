# Student Data ETL Pipeline (n8n + Google Drive)

**Week 3 Day 3 Assignment — Social Eagle (`learn. build. operate`)**

An automated **ETL (Extract, Transform, Load)** workflow built in **n8n** that retrieves messy student enrollment data from Google Drive, cleans and normalizes text fields, removes duplicate records, filters by enrollment status, and uploads the cleaned dataset back to Google Drive as a new CSV file.

---

## Pipeline Overview

| Stage | n8n Node | Action Performed |
| :--- | :--- | :--- |
| **Trigger** | `Manual Trigger` | Executes the end-to-end pipeline with a single click. |
| **Extract** | `Google Drive` (Search & Download) $\rightarrow$ `Extract from File` | Downloads `students.csv` from Google Drive and parses CSV rows into individual n8n JSON items. |
| **Transform** | `Code in JavaScript` | Trims leading/trailing/extra internal whitespace, converts `name` and `course` to Title Case, and lowercases `email` and `status`. |
| **Deduplicate** | `Remove Duplicates` | Drops duplicate student records by comparing normalized `email` fields. |
| **Filter** | `Filter` | Retains only records matching the rule `status = "enrolled"`. |
| **Load** | `Convert to File` $\rightarrow$ `Google Drive` (Upload) | Converts the cleaned items back into CSV format and uploads `students_clean.csv` to the target Google Drive folder. |

---

## Row Count & Data Audit

| Metric | Row Count | Notes |
| :--- | :---: | :--- |
| **Input (`students.csv`)** | **18** | Raw dataset containing whitespace issues, inconsistent casing, duplicate emails, and mixed enrollment statuses. |
| **Duplicates Removed** | **-3** | Dropped via `Remove Duplicates` node matching on normalized `email`. |
| **Filtered Out** | **-2** | Dropped rows where `status != "enrolled"`. |
| **Output (`students_clean.csv`)** | **13** | Clean, deduplicated, enrolled-only student records saved to Google Drive. |

### Key Design Decisions
* **Duplicate Definition:** Records are deduplicated based on normalized lowercase `email` (`Remove Duplicates` on `email`).
* **Missing Email Handling:** Rows with a blank `email` are intentionally **retained** in the cleaned output (`William Taylor`) so valid enrolled students are not silently lost and can be flagged for administrative follow-up.
* **Filter Rule:** Only rows where `status` strictly equals `"enrolled"` are kept.

---

## Repository Contents

* `students.csv` — Raw messy input CSV dataset (18 student records).
* `students_clean.csv` — Cleaned and filtered output CSV dataset (13 student records).
* `Student Data ETL Pipeline.json` — Exported n8n workflow JSON ready for import.
* `README.md` — Project overview, ETL architecture, and row-count summary.
* `REQUIREMENTS.md` — Tools, credentials, and setup instructions to run the workflow.

---

## How to Run

1. Open your **n8n** workspace (Self-hosted or Cloud).
2. Click **Add Workflow** $\rightarrow$ **Import from File** and select `Student Data ETL Pipeline.json`.
3. Connect your **Google Drive OAuth2** credentials to the Google Drive nodes (`Search files and folders`, `Download file`, and `Upload file`).
4. Ensure `students.csv` is uploaded to your target Google Drive folder and mapped in the `Download file` node.
5. Click **Execute workflow** and verify all nodes complete with green checkmarks and output `students_clean.csv` (13 rows).