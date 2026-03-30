# Stage 1: Downloading the Data
## Troubleshooting Guide

---

Use this guide to diagnose and resolve problems you encounter while running the Stage 1 workflow on usegalaxy.eu. Work through the relevant section before contacting your supervisor — most issues can be resolved independently.

---

## Quick Diagnosis

| Symptom | Go to section |
|---|---|
| Dataset is red in my history | [Job Failed (Red Dataset)](#job-failed-red-dataset) |
| Dataset has been yellow/grey for a very long time | [Job Stuck or Very Slow](#job-stuck-or-very-slow) |
| Workflow ran but I have no output files or the collection is empty | [Empty or Missing Output](#empty-or-missing-output) |
| Output files exist but are empty (0 bytes) | [Empty FASTQ Files](#empty-fastq-files) |
| I see paired-end files when I expected single-end | [Unexpected Paired-End Output](#unexpected-paired-end-output) |
| My sample_info.txt won't upload or looks wrong | [Upload and Formatting Issues](#upload-and-formatting-issues) |
| I can't find or import the workflow | [Workflow Import Issues](#workflow-import-issues) |
| Galaxy is unresponsive or won't load | [Galaxy Platform Issues](#galaxy-platform-issues) |

---

## Job Failed (Red Dataset)

A red dataset means the job failed. This is the most common issue.

**Step 1: Read the error message.**
- Click the red dataset in your history
- Click the **bug icon** (report error) or the **info icon** (i) to see the error details
- Read the "Tool Standard Error" section — this usually contains the useful information

**Common errors and fixes:**

| Error message (or key phrase) | Cause | Fix |
|---|---|---|
| `failed to resolve accession` or `accession not found` | One or more SRA accession numbers are incorrect or do not exist | Check your `sample_info.txt` — verify every accession on ncbi.nlm.nih.gov/sra. Look for typos (e.g., `SRR` vs `SSR`, wrong digits). |
| `timeout` or `network error` | NCBI SRA servers are temporarily unavailable or slow | Wait 30 minutes and re-run the workflow. NCBI has intermittent outages; this is not your fault. |
| `disk quota exceeded` | Your Galaxy storage quota is full | Delete old or unnecessary datasets from your history (click the X icon). Check your quota under User → Preferences. |
| `memory` or `killed` error | The job used too many resources | This can happen if you are downloading very many samples at once. Try splitting your `sample_info.txt` into smaller batches (e.g., 6 samples at a time) and running the workflow multiple times. |
| `input is not tabular` or `format error` | The sample_info.txt file was not recognised as tabular | Re-upload the file and manually set the datatype to **tabular** (see Upload section below). |
| `column 1 not found` or `Cut` tool error | The input file has formatting issues (e.g., comma-separated instead of tab-separated) | Open your file in a plain text editor and confirm columns are separated by tabs, not commas or spaces. Re-upload. |

**Step 2: If the error is not in the table above:**
1. Copy the full error message text
2. Search for the error on the Galaxy Help forum (help.galaxyproject.org)
3. If still stuck, bring the error message to your next supervision session

---

## Job Stuck or Very Slow

**Grey (queued) for more than 2 hours:**
- usegalaxy.eu is a public server — jobs are queued behind other users' work
- Check the server status page (usegalaxy.eu/status or the Galaxy notice banner) for maintenance announcements
- You do not need to cancel and re-submit — this will put you at the back of the queue
- If grey for more than 24 hours, consider re-running the workflow at a different time (early morning or weekends tend to have shorter queues)

**Yellow (running) for more than 2 hours:**
- SRA downloads depend on NCBI's server speed. Downloading large datasets (many samples, deep sequencing) can take several hours
- Check if any individual sample is particularly large by looking at the SRA entry
- This is normal for large datasets — let it run

---

## Empty or Missing Output

**The workflow completed but I cannot see the output collection:**

1. Check whether hidden datasets are visible: click the **"X hidden"** link at the top of your history to reveal hidden datasets
2. The workflow is configured to hide intermediate files (the Cut output and the fasterq-dump log). The main output collection ("Raw RNA-seq data") should be visible.
3. If you still cannot see it, go to **User → Datasets** and search for "Raw RNA-seq data"

**The output collection exists but contains fewer files than expected:**

1. Check how many SRA accessions are in your `sample_info.txt` (count the data rows, not the header)
2. If the header was not properly formatted, the Cut tool may have extracted the header text (e.g., "SRR_accession") as if it were a real accession number — fasterq-dump would then fail on that entry but may succeed on others
3. Check the fasterq-dump log (unhide hidden datasets, look for "rnaseq_download_log") for error messages about specific accessions

---

## Empty FASTQ Files

If output files exist but are 0 bytes or very small:

1. The accession number may point to a dataset that has been withdrawn from SRA, or the SRA record may be corrupted
2. Check the specific accession on ncbi.nlm.nih.gov/sra — is the data still available?
3. If the data has been withdrawn, discuss with your supervisor — you may need a replacement dataset

---

## Unexpected Paired-End Output

If you see two files per sample (forward and reverse) instead of one:

- Your data is **paired-end**, not single-end
- This workflow is configured for single-end reads
- The `--split-3` flag will still download PE data, but the downstream tagging and structure assume SE
- **Action**: Inform your supervisor. You will need to use the paired-end version of this workflow when it is available, or the tagging and subsequent stages will need to be adjusted

**How to check before running:**
- Go to the SRA Run Selector for your dataset
- Look at the "Library Layout" column — it will say **SINGLE** or **PAIRED**

---

## Upload and Formatting Issues

**"My file uploaded but all data is in one column":**
- The file is likely comma-separated or space-separated instead of tab-separated
- Open the file in a plain text editor (Notepad on Windows, TextEdit in plain text mode on Mac)
- If you see commas between values, replace them with tabs
- In Excel: save the file as "Text (Tab delimited) (*.txt)" — not as CSV
- Re-upload to Galaxy with the datatype set to **tabular**

**"Galaxy detected my file as a different format":**
- Click the pencil icon on the dataset in your history
- Go to the **Datatypes** tab
- Change the datatype to **tabular**
- Click Save

**"My file has Windows line endings / extra characters":**
- If you created the file on Windows and uploaded to Galaxy, line ending characters may cause issues
- In Galaxy, after uploading, you can use the **Convert** tool to fix line endings: search for "Convert" in the tool panel and select "Windows to UNIX line endings"

---

## Workflow Import Issues

**"I can't find the Import button":**
1. Go to the top menu → **Workflow**
2. The **Import** button is in the top-right area of the workflow list page
3. Click it, then use **Upload** to select the `.ga` file from your computer

**"Import failed / invalid workflow format":**
- Ensure the file extension is `.ga` (a Galaxy workflow JSON file)
- Do not open or edit the file before importing — some text editors modify the JSON formatting
- If the file was downloaded from an email or shared drive, try re-downloading it

---

## Galaxy Platform Issues

**"Galaxy is very slow or not loading":**
- usegalaxy.eu has maintenance windows — check for banners at the top of the page
- Try clearing your browser cache or using a different browser
- Galaxy works best in Chrome or Firefox; Safari and Edge may have occasional rendering issues

**"I lost my history":**
- Your data is saved on the server. Go to the top menu → **History** → **Saved Histories** to find it
- If you are logged out and log back in, your most recent history should reload automatically
- Always name your histories (click the history name at the top to rename it, e.g., "Stage 1 — Data Download")

---

## When to Contact Your Supervisor

Contact your supervisor if:

- [ ] You have tried the fixes above and the problem persists
- [ ] Your SRA accessions return no data and you suspect the dataset has been withdrawn
- [ ] You discover your data is paired-end and need the PE workflow
- [ ] Galaxy has been down or unresponsive for more than 24 hours
- [ ] You are unsure whether your `sample_info.txt` is correct

When contacting your supervisor, include:
1. A screenshot of the error (the red dataset expanded with the error message visible)
2. The SRA accession number(s) that failed
3. What you already tried from this guide

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
