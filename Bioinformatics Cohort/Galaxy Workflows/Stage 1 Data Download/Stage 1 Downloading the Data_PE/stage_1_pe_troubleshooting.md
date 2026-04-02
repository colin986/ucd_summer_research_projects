# Stage 1: Downloading the Data (Paired-End)
## Troubleshooting Guide

---

Use this guide to diagnose and resolve problems with the PE download workflow on usegalaxy.eu. Work through the relevant section before contacting your supervisor.

---

## Quick Diagnosis

| Symptom | Go to section |
|---|---|
| Dataset is red in my history | [Job Failed (Red Dataset)](#job-failed-red-dataset) |
| Dataset has been yellow/grey for a very long time | [Job Stuck or Very Slow](#job-stuck-or-very-slow) |
| Workflow ran but output collection is empty or has fewer files than expected | [Empty or Missing Output](#empty-or-missing-output) |
| I see single files instead of paired collections | [Missing Paired Structure](#missing-paired-structure) |
| One read direction is missing (only forward or only reverse) | [Incomplete Pairs](#incomplete-pairs) |
| Output files exist but are empty (0 bytes) | [Empty FASTQ Files](#empty-fastq-files) |
| My sample_names.txt won't upload or looks wrong | [Upload and Formatting Issues](#upload-and-formatting-issues) |
| I can't find or import the workflow | [Workflow Import Issues](#workflow-import-issues) |
| Galaxy is unresponsive or won't load | [Galaxy Platform Issues](#galaxy-platform-issues) |

---

## Job Failed (Red Dataset)

**Step 1: Read the error message.**
Click the red dataset → click the bug icon or info icon (i) → read "Tool Standard Error".

**Common errors and fixes:**

| Error message (or key phrase) | Cause | Fix |
|---|---|---|
| `failed to resolve accession` or `accession not found` | SRA accession number is incorrect or does not exist | Verify every accession at ncbi.nlm.nih.gov/sra. Check for typos. |
| `timeout` or `network error` | NCBI SRA servers temporarily unavailable | Wait 30 minutes and re-run. NCBI has intermittent outages. |
| `disk quota exceeded` | Galaxy storage quota full | Delete old or unnecessary datasets. Check quota under User → Preferences. |
| `memory` or `killed` error | Job used too many resources | Split your `sample_names.txt` into smaller batches (e.g., 4–6 samples) and run multiple times. PE data is larger than SE. |
| `column 1 not found` or Cut tool error | Input file formatting issue | This workflow uses **comma-separated** input. Check your file uses commas, not tabs. See [Upload and Formatting Issues](#upload-and-formatting-issues). |
| `input format` error | Galaxy did not recognise the input file format | Re-upload and manually set the datatype. See [Upload and Formatting Issues](#upload-and-formatting-issues). |

**Step 2: If the error is not in the table above:**
1. Copy the full error message
2. Search on the Galaxy Help forum (help.galaxyproject.org)
3. Bring the error message to your next supervision session if unresolved

---

## Job Stuck or Very Slow

**Grey (queued) for more than 2 hours:**
- usegalaxy.eu is a public server — jobs queue behind other users
- PE downloads are larger than SE and take longer in the queue
- Do not cancel and re-submit — this puts you at the back of the queue
- If grey for more than 24 hours, try re-running at off-peak times (early morning, weekends)

**Yellow (running) for more than 3 hours:**
- PE downloads are roughly double the data volume of SE
- Large datasets (many samples, deep sequencing, long reads) can take several hours
- This is normal — let it run

---

## Empty or Missing Output

**The workflow completed but I cannot see the output collection:**

1. Click **"X hidden"** at the top of your history to reveal hidden datasets
2. The workflow hides intermediate files. The paired collection and any single-read collection should be visible.
3. If still not visible, go to **User → Datasets** and search for the output name

**The output collection has fewer entries than expected:**

1. Count the data rows in your `sample_names.txt` (excluding the header)
2. Check whether the header row caused an issue — if fasterq-dump received "SRR_accession" as a literal accession, it would fail on that entry
3. Unhide the download log ("rnaseq_download_log") and check for error messages about specific accessions

---

## Missing Paired Structure

**I see individual FASTQ files instead of paired forward/reverse collections:**

This can happen if:

1. **Your data is actually single-end, not paired-end.** Go back to the SRA Run Selector and check the "Library Layout" column. If it says SINGLE, you need the SE workflow instead.
2. **The SRA record's metadata is inconsistent.** Some SRA entries have paired data stored in a single-file format. In this case, fasterq-dump may place all reads in the `output_collection` (single) rather than `list_paired`. Check the `list_paired` collection — if it is empty but `output_collection` has files, this is likely the cause.
3. **The `--split-3` flag** places unpaired/orphan reads into a separate collection (`output_collection_other`, hidden by default). If most reads ended up there, the SRA record may have quality issues. Discuss with your supervisor.

---

## Incomplete Pairs

**One direction (forward or reverse) is missing or empty for some samples:**

1. This indicates a problem with the SRA record itself — the paired data may be corrupted or incompletely deposited
2. Check the specific accession on ncbi.nlm.nih.gov/sra — look for any warnings or notes about the record
3. Try downloading that accession manually using the SRA Run Browser to verify
4. If the problem persists, discuss with your supervisor — you may need to exclude that sample or find a replacement

---

## Empty FASTQ Files

If output files exist but are 0 bytes or very small:

1. The accession may point to data that has been withdrawn from SRA
2. Check the specific accession at ncbi.nlm.nih.gov/sra — is it still available?
3. If the data has been withdrawn, discuss with your supervisor

---

## Upload and Formatting Issues

**"My file uploaded but all data is in one column":**

This PE workflow expects a **comma-separated (CSV)** file. Check:
- Open your file in a plain text editor (not Excel)
- You should see commas between values: `SRR1234567,control,1`
- If you see tabs instead of commas, your file is tab-delimited — either re-save as CSV, or check whether you should be using the SE workflow (which uses tab-delimited input)

**"How to save as CSV":**
- In Excel: File → Save As → select "CSV (Comma delimited) (*.csv)"
- In Google Sheets: File → Download → Comma Separated Values (.csv)
- Rename to `.txt` if the workflow requires a `.txt` extension

**"Galaxy detected my file as a different format":**
- Click the pencil icon on the dataset
- Go to the **Datatypes** tab
- Change to an appropriate type (tabular or txt)
- Click Save

**"My file has Windows line endings":**
- Use Galaxy's Convert tool: search "Convert" → "Windows to UNIX line endings"

---

## Workflow Import Issues

**"I can't find the Import button":**
- Top menu → **Workflow** → **Import** button (top right)
- Upload the `.ga` file from your computer

**"Import failed":**
- Ensure the file extension is `.ga`
- Do not open or edit the file before importing
- If downloaded from email/shared drive, try re-downloading

---

## Galaxy Platform Issues

**"Galaxy is very slow or not loading":**
- Check for maintenance banners at the top of the page
- Clear browser cache or try a different browser (Chrome/Firefox recommended)

**"I lost my history":**
- Your data is saved on the server: top menu → **History** → **Saved Histories**
- Always name your histories descriptively (e.g., "Stage 1 — PE Data Download")

---

## PE-Specific Issues Summary

| Issue | SE workflow | PE workflow |
|---|---|---|
| Input file format | Tab-delimited | **Comma-separated (CSV)** |
| Expected output | One FASTQ per sample | **Two FASTQs per sample** (forward + reverse) in a paired collection |
| Output size | ~X GB | **~2X GB** (roughly double) |
| Download time | Moderate | **Longer** (more data) |
| Storage quota | Less likely to hit limits | **More likely** — monitor your quota |

---

## When to Contact Your Supervisor

Contact your supervisor if:

- [ ] You have tried the fixes above and the problem persists
- [ ] Your SRA accessions return no data or the data appears to be withdrawn
- [ ] Your data appears to be SE despite the publication stating PE
- [ ] The paired structure is missing or corrupted for multiple samples
- [ ] Galaxy has been down or unresponsive for more than 24 hours

When contacting your supervisor, include:
1. A screenshot of the error (red dataset expanded with error message visible)
2. The SRA accession number(s) that failed
3. What you already tried from this guide

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
