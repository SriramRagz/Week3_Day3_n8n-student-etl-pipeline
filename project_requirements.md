# Project Requirements & Environment Setup

This document outlines the platform dependencies, credentials, and configuration required to reproduce and execute the **Student Data ETL Pipeline** in n8n.

---

## 1. Core Tools & Services

* **Workflow Engine:** n8n (`v1.x` / `v2.x`+ compatible; tested on n8n Cloud/Self-Hosted instance)
* **Cloud Storage:** Google Drive (with read/write access to the target assignment folder)
* **Data Format:** UTF-8 CSV (`students.csv` input $\rightarrow$ `students_clean.csv` output)

---

## 2. n8n Nodes Used

Ensure your n8n instance supports the following built-in core nodes (no external community nodes or npm packages required):

1. `n8n-nodes-base.manualTrigger` (v1)
2. `n8n-nodes-base.googleDrive` (v3) — Search, Download, and Upload operations
3. `n8n-nodes-base.extractFromFile` (v1.2) — Extract From CSV (Header Row enabled)
4. `n8n-nodes-base.code` (v2) — JavaScript transformation script
5. `n8n-nodes-base.removeDuplicates` (v2) — Deduplication on `email`
6. `n8n-nodes-base.filter` (v2.4) — String equality filter (`status == "enrolled"`)
7. `n8n-nodes-base.convertToFile` (v1.2) — Convert to CSV

---

## 3. Credentials & Permissions

* **Google Drive OAuth2 API (`googleDriveOAuth2Api`):**
  * Must be authenticated via **Sign in with Google** inside n8n.
  * Required scopes must allow searching folders, downloading files (`students.csv`), and uploading new files (`students_clean.csv`).

---

## 4. Input Schema Requirements (`students.csv`)

The input CSV must include a header row with the following five columns:

| Column Name | Type | Transformation Applied |
| :--- | :--- | :--- |
| `name` | String | Trimmed whitespace + Title Case normalization |
| `email` | String | Trimmed whitespace + Lowercase normalization (used for deduplication) |
| `course` | String | Trimmed whitespace + Title Case normalization |
| `status` | String | Trimmed whitespace + Lowercase normalization (filtered for `"enrolled"`) |
| `enrolled_date` | String (`YYYY-MM-DD`) | Trimmed whitespace |

---

## 5. Verification Checklist

* [ ] Workflow executes end-to-end in a single click with all nodes green.
* [ ] Input row count (`18`) and output row count (`13`) match the expected transformation breakdown (`-3` duplicates, `-2` non-enrolled).
* [ ] `students_clean.csv` opens cleanly in Google Drive / spreadsheet viewers with intact headers and zero duplicate emails.