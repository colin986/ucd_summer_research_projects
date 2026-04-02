# Stage 1: Downloading the Data
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will download the raw RNA-seq FASTQ files for your assigned published dataset from NCBI's Sequence Read Archive (SRA). By the end of this stage, you will have a collection of FASTQ files in your Galaxy history, correctly labelled and tagged, and ready for quality assessment in Stage 2.

This guide covers both **single-end (SE)** and **paired-end (PE)** datasets. Follow the common steps first, then continue with the section that matches your data type.

---

## Step 1: Understanding the Paper the Data Came From and the Data Type You're Working With

**Before you run any workflow, you must read your assigned paper in full.**

Your project re-analyses a published RNA-seq dataset from CHO (Chinese hamster ovary) cells — the dominant cell line used in biopharmaceutical manufacturing for the production of recombinant proteins, monoclonal antibodies, and other biologics. Every dataset in this cohort uses Chinese hamster as the organism; this is fixed and does not vary between projects. All comparisons are between a treatment condition and a control — your task is to understand what that treatment was, why it was applied, and what the authors found.

Reading the paper is not background reading — it is the foundation of your entire analysis and written submission. You cannot write a meaningful Introduction, contextualise your Methods, or critically discuss your results without a thorough understanding of the original study.

**When reading your paper, focus on the following questions in the context of biopharmaceutical manufacturing:**

- **What was the biological or bioprocess question?** For example: How does a specific nutrient limitation, cell culture condition, genetic modification, or process intervention affect CHO cell behaviour at the transcriptomic level? What manufacturing-relevant problem were the authors trying to address?
- **Why does this matter for biopharmaceutical production?** CHO cells are used to manufacture life-saving medicines. Understanding how gene expression changes under different bioprocess conditions — media composition, temperature shifts, osmolality, dissolved oxygen, fed-batch vs. perfusion culture — is directly relevant to improving product yield, quality, and consistency at industrial scale.
- **What was the treatment, and what was the control?** Your analysis will reproduce the same treatment-versus-control comparison. Be precise: what exactly was done to the treatment group, and what conditions did the control represent?
- **What CHO cell line was used?** There are several common variants (CHO-K1, CHO-DG44, CHO-S, CHO-GS, etc.) with different characteristics. Note which was used and whether the paper discusses any cell-line-specific considerations.
- **What were the key transcriptomic findings?** What genes or pathways did the authors identify as differentially expressed? What biological processes were enriched? How did they interpret these findings in the context of the bioprocess?
- **What sequencing platform and library preparation method was used?** This is reported in the Methods section of the paper and is relevant to your own Methods write-up.

You will need this understanding before you proceed. Students who have not read their paper in detail consistently produce weaker written submissions — particularly in the Introduction and Discussion, where examiners look for evidence that you understand the biological and manufacturing context of the data you are analysing.

---

**Your supervisor will confirm your data type (single-end or paired-end).** Do not proceed to the workflow until you have this information.

| Data type provided by supervisor | Guide section to follow |
|---|---|
| **Single-end (SE)** | [Section A — Single-End Workflow](#section-a--single-end-se-workflow) |
| **Paired-end (PE)** | [Section B — Paired-End Workflow](#section-b--paired-end-pe-workflow) |

---

## Key Concepts

> **Single-end (SE):** Each DNA/RNA fragment is sequenced from one end only, producing **one FASTQ file per sample**.
>
> **Paired-end (PE):** Each fragment is sequenced from **both ends**, producing **two FASTQ files per sample** — a forward read (Read 1, `_1`) and a reverse read (Read 2, `_2`). PE data provides better mapping confidence, improved detection of splice junctions, and more information per sequenced fragment.

---

## Step 2: Collect Your SRA Accession Numbers

Regardless of your data type, you need the SRA accession numbers (SRR...) for each of your samples. Do this now before preparing your input file.

1. In the SRA Run Selector, select all runs belonging to your study
2. Copy or download the list of SRR accession numbers
3. Note the following — you will need this information throughout the project:

| What to record | Where to find it |
|---|---|
| SRA accession numbers (SRR...) | SRA Run Selector |
| Library Layout (SINGLE or PAIRED) | SRA Run Selector — "Library Layout" column |
| Read length | SRA entry, or confirm in Stage 2 with FastQC |
| Organism and genome build | The original publication |
| Number of samples | Count the SRR entries for your study |

---

---

## Section A — Single-End (SE) Workflow

Follow this section if the SRA Run Selector shows **Library Layout: SINGLE**.

---

### A1: Prepare Your Sample Information File

You need a **tab-delimited text file** called `sample_info.txt` with SRA accession numbers in column 1.

**Example `sample_info.txt`:**

```
SRR_accession	condition	replicate
SRR1234567	control	1
SRR1234568	control	2
SRR1234569	control	3
SRR1234570	treated	1
SRR1234571	treated	2
SRR1234572	treated	3
```

**Requirements:**
- SRA accession numbers **must be in column 1**
- The file must be **tab-delimited** (not comma-delimited)
- Include a header row — the workflow will skip non-accession text
- Save with a `.txt` or `.tabular` extension

---

### A2: Upload Your File to Galaxy

1. Log in to **usegalaxy.eu**
2. Click the **Upload Data** button (up arrow icon) at the top of the tool panel
3. Click **Choose local files** and select your `sample_info.txt`
4. **Important:** Set the file type to **tabular** (not auto-detect) using the dropdown or pencil icon
5. Click **Start** and wait for the dataset to turn green

**Verify your upload:**
- Click the eye icon to preview the dataset
- Confirm columns are separated correctly (not all in one column)
- Confirm SRA accession numbers are in column 1

---

### A3: Import and Run the SE Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file **`Galaxy-Workflow-1_download_se_faqstq_data.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported SE workflow
2. Under **"Provide the sample information"**, select your uploaded `sample_info.txt`
3. Click **Run Workflow**

---

### A4: Monitor and Verify (SE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- Downloads can take several minutes to over an hour — usegalaxy.eu is a shared public resource
- You do not need to keep your browser open; Galaxy continues running in the background
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below before consulting the troubleshooting guide

**When complete, verify:**
- A **dataset collection** called **"Raw RNA-seq data"** appears in your history
- Expand it — you should see **one FASTQ file per SRA accession**
- Files should be typed as **fastq.gz** and tagged **raw_data**
- Check that files are non-zero in size
- Preview one file (eye icon) — you should see FASTQ format:

```
@SRR1234567.1 1/1
ATCGATCGATCGATCG...
+
IIIIIIIIIIIIIII...
```

Each read has 4 lines: identifier (`@`), sequence, separator (`+`), quality scores.

---

### A5: Thesis Methods Template (SE)

> "Raw RNA-seq data were downloaded from the NCBI Sequence Read Archive (SRA) using the Faster Download and Extract Reads in FASTQ tool (fasterq-dump v3.1.1) on the Galaxy platform (usegalaxy.eu). Single-end FASTQ files for [n] samples were retrieved using accession numbers obtained from GEO Series [GSE number] (Author et al., Year). Data were downloaded in compressed FASTQ format (.fastq.gz)."

Adapt this to your dataset — do not copy it verbatim.

---

### A6: SE Workflow — Technical Summary

| Step | Tool | What it does | Key parameter |
|---|---|---|---|
| 1 | Input dataset | Accepts `sample_info.txt` | Format: tabular |
| 2 | Cut (v1.0.2) | Extracts column 1 (SRA accession numbers) | Delimiter: Tab |
| 3 | fasterq-dump (v3.1.1) | Downloads FASTQ data from NCBI SRA for each accession | `--split-3`; output tagged `raw_data`; datatype `fastq.gz` |

> **`--split-3` note:** This flag handles SE and PE data automatically. For SE data it produces one file per sample. The workflow's tagging and downstream steps are configured for SE output.

---

### A7: SE Self-Check

Before moving to Stage 2, you should be able to answer:

1. What is a FASTQ file? What do the four lines of a FASTQ record represent?
2. What does "single-end" mean? How does it differ from paired-end?
3. What did the Cut tool do? Why was it needed before fasterq-dump?
4. What does the `--split-3` flag do? What would happen if your data were actually paired-end?
5. How many FASTQ files should you have? Does this match your number of SRA accessions?
6. What is the SRA, and why does public data deposition matter?
7. What read length does your dataset use?

---

### A8: SE Completion Checklist

- [ ] My output collection contains the correct number of FASTQ files (one per sample)
- [ ] I have previewed at least one file and confirmed it is in FASTQ format
- [ ] I have noted my read type (SE), approximate read length, and organism
- [ ] I have named my Galaxy history descriptively
- [ ] I have started drafting my Methods paragraph for this stage

---

---

## Section B — Paired-End (PE) Workflow

Follow this section if the SRA Run Selector shows **Library Layout: PAIRED**.

---

### B1: Prepare Your Sample Names File

You need a **comma-separated text file** called `sample_names.txt` with SRA accession numbers in column 1.

> **Note:** The PE workflow uses a **comma-separated (CSV)** file. This differs from the SE workflow, which uses a tab-delimited file. Using the wrong delimiter is a common source of errors.

**Example `sample_names.txt`:**

```
SRR_accession,condition,replicate
SRR1234567,control,1
SRR1234568,control,2
SRR1234569,control,3
SRR1234570,treated,1
SRR1234571,treated,2
SRR1234572,treated,3
```

**Requirements:**
- SRA accession numbers **must be in column 1**
- The file must be **comma-separated** (CSV format)
- A header row is acceptable — fasterq-dump will skip non-accession text
- Save with a `.txt` or `.csv` extension

---

### B2: Upload Your File to Galaxy

1. Log in to **usegalaxy.eu**
2. Click the **Upload Data** button (up arrow icon) at the top of the tool panel
3. Click **Choose local files** and select your `sample_names.txt`
4. Click **Start** and wait for the dataset to turn green

**Verify your upload:**
- Click the eye icon to preview the dataset
- Confirm columns are separated by commas and SRA accession numbers are in column 1
- If everything appears in one column, check that the file was saved as CSV

---

### B3: Import and Run the PE Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file **`Galaxy-Workflow-1_download_pe_faqstq_data.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported PE workflow
2. Under **"Provide the sample information"**, select your uploaded `sample_names.txt`
3. Click **Run Workflow**

---

### B4: Monitor and Verify (PE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- PE downloads are typically larger and slower than SE — there are two files per sample
- You do not need to keep your browser open; Galaxy continues running in the background
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below before consulting the troubleshooting guide

**When complete, verify:**
- A **paired dataset collection** appears in your history, tagged **raw_data**
- Expand it — you should see **one entry per SRA accession**, each containing a forward and reverse file
- Files should be typed as **fastq.gz**
- Neither forward nor reverse file should be empty
- Preview a file (eye icon) to confirm FASTQ format:

```
@SRR1234567.1 1/1
ATCGATCGATCGATCG...
+
IIIIIIIIIIIIIII...
```

**Understanding the PE output collections:**
- **`list_paired`** — the properly paired forward/reverse collections. This is what you will use in downstream stages.
- **`output_collection`** — may contain unpaired or orphan reads. For clean PE data from SRA, you can generally ignore this.

---

### B5: Thesis Methods Template (PE)

> "Raw paired-end RNA-seq data were downloaded from the NCBI Sequence Read Archive (SRA) using the Faster Download and Extract Reads in FASTQ tool (fasterq-dump v3.1.1) on the Galaxy platform (usegalaxy.eu). Paired-end FASTQ files for [n] samples ([read length]-bp reads) were retrieved using accession numbers obtained from GEO Series [GSE number] (Author et al., Year). Forward and reverse reads were split using the --split-3 option and stored in compressed FASTQ format (.fastq.gz)."

Adapt this to your dataset — do not copy it verbatim.

---

### B6: PE Workflow — Technical Summary

| Step | Tool | What it does | Key parameter |
|---|---|---|---|
| 1 | Input dataset | Accepts `sample_names.txt` | Format: CSV |
| 2 | Cut (v1.0.2) | Extracts column 1 (SRA accession numbers) | Delimiter: Comma |
| 3 | fasterq-dump (v3.1.1) | Downloads FASTQ data and splits PE reads into forward/reverse pairs | `--split-3`; paired reads in `list_paired`; tagged `raw_data`; datatype `fastq.gz` |

> **`--split-3` flag:** Separates paired reads into forward (`_1`) and reverse (`_2`) files, and places any orphan reads into a separate file. For clean PE data from SRA, the paired output is what you will use downstream.

---

### B7: PE Self-Check

Before moving to Stage 2, you should be able to answer:

1. What is a FASTQ file? What do the four lines of a FASTQ record represent?
2. What does "paired-end" mean? Why are there two files per sample?
3. How does PE differ from SE in practice? What are the advantages of paired-end sequencing?
4. What did the Cut tool do? What delimiter did it use, and why does this matter?
5. What does the `--split-3` flag do? Why is splitting important for PE data?
6. How many paired entries should you have? Does this match your number of SRA accessions?
7. What is insert size? Where can you find this information for your dataset?
8. What read length does your dataset use?

---

### B8: PE Completion Checklist

- [ ] My paired output collection contains the correct number of entries (one per sample)
- [ ] Each entry contains both a forward and a reverse FASTQ file
- [ ] I have previewed at least one file and confirmed it is in FASTQ format
- [ ] I have noted my read type (PE), approximate read length, organism, and any strandedness information from the paper
- [ ] I have named my Galaxy history descriptively
- [ ] I have started drafting my Methods paragraph for this stage

---

---

---

## Diagnosing a Red Dataset

A red dataset in Galaxy means the job failed. Before assuming you have made an error, you need to read the error message — many failures are caused by external factors (NCBI timeouts, server load) and have nothing to do with your input file.

This section explains how to find and interpret the error information. For specific error messages and their fixes, see the Stage 1 Troubleshooting Guide.

---

### Step 1: Expand the Red Dataset

Click the red dataset in your history panel. It will expand to show a brief description and a row of icons. If a workflow step failed, multiple datasets may turn red — start with the first one that went red (the others often fail as a consequence).

---

### Step 2: Open the Error Details

There are two routes to the full error information:

**Route A — Bug icon (report error):**
Click the **bug icon** (looks like a small insect) in the expanded dataset row. This opens a panel showing the tool's standard output and standard error logs. You do not need to submit an error report — just read the information displayed.

**Route B — Info icon (i):**
Click the **information icon (i)** in the expanded dataset row. This opens the full job details page, which contains more information than the bug icon view.

---

### Step 3: Read the Right Log

The job details page shows several panels. Not all of them are equally useful:

| Panel | What it contains | When to read it |
|---|---|---|
| **Tool Standard Error** (`stderr`) | Error messages, warnings, and diagnostic output from the tool itself | **Always read this first** — this is where fasterq-dump reports what went wrong |
| **Tool Standard Output** (`stdout`) | Normal progress messages, read counts, and completion summaries | Read this if stderr is empty — it may contain the last status message before failure |
| **Tool Parameters** | The exact settings used when the job ran | Check this if you suspect the wrong input file was used, or to confirm parameters |
| **Job Information** | Server-level metadata: queue time, run time, compute node, exit code | Read this if the error is infrastructure-related (e.g., the job was killed by the server) |

> **The exit code** (in Job Information) can be a quick indicator of the failure type:
> - Exit code `0` — the tool completed normally (unexpected if the dataset is red — check Galaxy's own error, not the tool)
> - Exit code `1` — the tool itself reported an error (read stderr)
> - Exit code `137` — the job was killed, usually due to exceeding memory or time limits
> - Exit code `143` — the job was terminated by the server (common during maintenance or resource contention)

---

### Step 4: Identify the Error

Read the Tool Standard Error log carefully. The most useful information is usually near the **bottom** of the log, as tools typically print progress messages first and the actual error last.

**What you are looking for:**
- A line beginning with `err:`, `ERROR`, `failed`, or `Exception`
- A specific accession number that caused the failure (e.g., `SRR1234567: failed to resolve`)
- A resource message such as `Disk quota exceeded` or `Killed`

**Copy the error text** — you will need it if you search the Galaxy Help forum or contact your supervisor.

---

### Step 5: Determine Whether the Error Affects One Step or the Whole Workflow

Workflows run as a chain of dependent steps. If an early step fails, later steps that depend on it will also turn red — but their red state is a consequence, not a separate problem.

**How to trace the root failure:**
1. In your history, look at the order datasets were created (newest at the top)
2. The **first** dataset to turn red is usually the root cause
3. Hover over or expand each red dataset and note which tool generated it — the tool name is shown in the dataset header
4. The Cut tool failing indicates a problem with your input file format
5. The fasterq-dump tool failing indicates a problem with the SRA accession or a server/network issue

---

### Step 6: Decide on Your Next Action

| What you found | What to do |
|---|---|
| `accession not found` or `failed to resolve` | Check every SRA accession in your input file against ncbi.nlm.nih.gov/sra for typos or withdrawn records |
| `timeout` or `network error` | Wait 30 minutes and re-run the failed step — do not re-run the full workflow if earlier steps succeeded |
| `Disk quota exceeded` | Go to **User → Preferences → Storage** and delete old datasets to free space, then re-run |
| Exit code `137` (memory) or `143` (killed) | Try splitting your input file into smaller batches (4–6 samples at a time) and run multiple workflow instances |
| `column 1 not found` or Cut tool error | Your input file has the wrong delimiter — see A1 (SE) or B1 (PE) above for the correct format |
| Error not in this list | Copy the full error text and search the Galaxy Help forum (help.galaxyproject.org), then bring it to your supervision session |

---

### Re-running After a Failure

> **Do not re-run the entire workflow** if only one step failed and earlier steps completed successfully — this wastes queue time and storage. Instead:
>
> 1. Fix the underlying problem (correct your input file, wait for NCBI to recover, etc.)
> 2. In your history, click on the failed red dataset
> 3. Click the **re-run icon** (circular arrow) to re-run just that step with the corrected input
>
> If the whole workflow needs to be re-run (e.g., your input file was wrong from the start), delete the failed history entries first to avoid confusion, then start the workflow again.

---

## Recording Your Dataset Statistics for the Written Assessment

This section applies to all bioinformatics cohort students regardless of data type. Your written submission is a short research communication assessed on scientific content (50%), structure (15%), written communication (20%), and literature engagement (15%). The information you collect at Stage 1 directly feeds into your Methods and Results sections, which together account for 25 of the 50 marks available for scientific content.

> **Important:** At this stage, "read counts" means the **total number of sequencing reads per sample** — this is your sequencing depth. This is not the same as gene-level read counts, which are produced in Stage 5 (quantification) and form the basis of your differential expression analysis. Do not confuse the two.

---

### What to Record: Per-Sample Read Counts (Sequencing Depth)

After your workflow completes, find and record the number of reads downloaded for each sample.

**How to find read counts in Galaxy:**

1. In your history, click on the fasterq-dump output dataset for a given sample
2. Click the **information icon (i)** to open the job details
3. Click **Tool Standard Output** — this log shows the number of reads written for that accession
4. Record the read count for each sample

Alternatively, Stage 2 (FastQC) will report the total number of reads in each file — you can collect all counts there if preferred.

**What to look for:**
- Total reads per sample (often expressed as millions, e.g. 25.3M reads)
- Whether read counts are broadly consistent across samples — large discrepancies between samples may indicate a download problem or a genuine difference in sequencing depth

**Typical RNA-seq sequencing depths:**
- ≥10 million reads per sample is generally considered the minimum for detecting differentially expressed genes in well-expressed transcripts
- 20–30 million reads is more common in published CHO cell RNA-seq studies
- If any of your samples have substantially fewer reads than others, note this — it will be relevant in your Discussion

---

### How This Feeds into Your Written Submission

#### Methods Section (rubric A2 — 10 marks)

Your Methods section must describe your dataset sufficiently that a reader could reproduce your analysis. At minimum, include:

- The GEO Series accession number (GSE...)
- The number of samples and their grouping (e.g., 3 control replicates, 3 treatment replicates)
- Read type (single-end or paired-end) and approximate read length
- The range or median number of reads per sample (sequencing depth)
- The tool and platform used to download the data (fasterq-dump v3.1.1, usegalaxy.eu)

**Example Methods language:**

> "Raw RNA-seq data for [n] samples ([conditions]) were obtained from NCBI GEO (accession GSExxxxxx; Author et al., Year) and downloaded from the SRA using fasterq-dump (v3.1.1) on the Galaxy platform (usegalaxy.eu). Samples were single-end [or paired-end], with a read length of [X] bp and a median sequencing depth of [X] million reads per sample."

A strong Methods section (A-grade) names the specific dataset, gives accession numbers, states both read type and depth, and makes the analysis reproducible. A weak Methods section (C-grade or below) omits these specifics or describes tools only in general terms.

---

#### Results Section (rubric A3 — 15 marks)

Your Results section should open with a concise characterisation of your dataset before presenting your differential expression findings. This establishes the evidence base for everything that follows.

**Recommended approach — a dataset summary table:**

Include a small table (counts toward your 3 main-text figure/table allowance) summarising your samples. This is one of the highest-value uses of your limited figure allowance at this stage of the submission.

| Sample | SRA Accession | Condition | Reads (millions) |
|---|---|---|---|
| Sample 1 | SRRxxxxxxx | Control | 24.1 |
| Sample 2 | SRRxxxxxxx | Control | 22.8 |
| ... | ... | ... | ... |

> **Supplementary vs. main text:** A concise summary table (6–12 samples) belongs in the main text. If you have a large number of samples and the table would disrupt flow, include a summary in the main text (e.g., "median depth 23.4M reads, range 18.1–29.7M") and move the full per-sample table to Supplementary Table S1 — citing it explicitly in the text.

A strong Results section (A-grade) opens with a precise dataset characterisation, quantifies sequencing depth, and flags any sample-level variation before presenting the main findings. A weak Results section omits the dataset characterisation entirely or describes it only vaguely.

---

#### Discussion Section (rubric A4 — 15 marks)

Your Discussion should briefly address the adequacy of your sequencing depth in the context of your study's aims. This is a standard element of published RNA-seq papers and demonstrates scientific maturity.

Points to consider:
- Is your sequencing depth consistent with the depth used in the original publication (if stated)?
- Is it consistent with published recommendations for CHO cell RNA-seq studies? Cite a relevant paper — e.g., studies establishing read depth thresholds for DE analysis.
- If one or more samples have notably lower depth, acknowledge this as a limitation and consider whether it affects your confidence in results from those samples.

You do not need to dedicate a full paragraph to this — two or three sentences in the context of discussing your dataset and its limitations is appropriate.

---

### Summary: Stage 1 Contributions to Your Submission

| Submission section | What Stage 1 provides | Rubric component |
|---|---|---|
| Methods | Dataset accession, sample count, read type, read length, sequencing depth, download tool | A2 (10 marks) |
| Results | Dataset characterisation table: per-sample accessions, conditions, and read counts | A3 (15 marks) |
| Discussion | Comment on sequencing depth adequacy relative to study aims and published standards | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Frequently Asked Questions

**Q: The download has been running for over an hour — is this normal?**
A: Yes. usegalaxy.eu is a shared public server, and download speed depends on NCBI server load, your dataset size, and Galaxy queue status. PE downloads take longer because there are two files per sample. Do not cancel and re-submit — this places you back in the queue.

**Q: I got a red dataset. Does this mean I did something wrong?**
A: Not necessarily. Red means the job failed, but the cause is often external (NCBI timeout, server overload). Click on the red dataset and read the error message first, then consult the troubleshooting guide. Many failures resolve by simply re-running the failed step.

**Q: Do I need to keep my browser open while the workflow runs?**
A: No. Galaxy runs jobs on the server. You can close your browser and log back in later — your history will be exactly as you left it.

**Q: How do I know if my data is single-end or paired-end?**
A: Check the SRA Run Selector for your dataset. The "Library Layout" column will say SINGLE or PAIRED. If in doubt, confirm with your supervisor before running the workflow.

**Q: I used the wrong workflow for my data type — what should I do?**
A: Stop the workflow if it is still running, then start again with the correct workflow and input file. If files have already downloaded, check with your supervisor whether they can be used — in most cases it is faster to re-run correctly.

**Q: My tab-delimited file doesn't work with the PE workflow (or my CSV doesn't work with the SE workflow).**
A: The SE workflow requires a **tab-delimited** file; the PE workflow requires a **comma-separated (CSV)** file. Re-save your file with the correct delimiter and re-upload it to Galaxy.

**Q: I see individual files instead of a paired collection in the PE output — what happened?**
A: Your data may actually be single-end, or the SRA record stores the PE data in a non-standard way. Check the "Library Layout" column in the SRA Run Selector and see the troubleshooting guide section on "Missing Paired Structure."

**Q: What is the difference between `list_paired` and `output_collection` in the PE output?**
A: `list_paired` contains the properly paired forward/reverse collections and is what you will use downstream. `output_collection` may contain unpaired or orphan reads. For clean PE data, focus on `list_paired`.

**Q: I have never used a command line or written code — will I need to?**
A: No. Galaxy is entirely web-based and point-and-click. The workflows run without any coding.

**Q: My supervisor is not available and I am stuck — what do I do?**
A: Follow the "How to Get Help" guide in the Galaxy Workflows folder: re-read this guide → check the troubleshooting guide → search the Galaxy Help forum → post to the class forum.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
