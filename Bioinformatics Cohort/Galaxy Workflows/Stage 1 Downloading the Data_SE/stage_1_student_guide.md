# Stage 1: Downloading the Data
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will download the raw RNA-seq FASTQ files for your assigned published dataset from NCBI's Sequence Read Archive (SRA). By the end of this stage, you will have a collection of FASTQ files in your Galaxy history, correctly labelled and tagged, ready for quality assessment in Stage 2.

---

## Before You Begin

Before running this workflow, you need to know the following about your dataset. Confirm these with your supervisor if you are unsure.

| What you need to know | Where to find it | Why it matters |
|---|---|---|
| **SRA accession numbers** (SRR...) for each sample | Your `sample_info.txt` file, or search on NCBI SRA/GEO | These tell Galaxy which files to download |
| **Single-end (SE) or paired-end (PE) reads** | The GEO/SRA entry for the dataset, or the original paper's methods section | This workflow is configured for **single-end** reads. If your data is paired-end, you will need the PE version of this workflow |
| **Read length** | The SRA entry or FastQC output (Stage 2) | Affects downstream trimming and alignment decisions — note it now for later stages |
| **Organism and genome build** | The original publication | You will need this for alignment (later stages), but knowing it now helps you verify you have the correct dataset |

> **Key concept — Single-end vs. Paired-end reads:**
> - **Single-end (SE)**: Each DNA/RNA fragment is sequenced from one end only, producing one FASTQ file per sample.
> - **Paired-end (PE)**: Each fragment is sequenced from both ends, producing two FASTQ files per sample (forward and reverse reads, often labelled _1 and _2).
> - This workflow uses the `--split-3` flag in fasterq-dump, which means: if your data is SE, it produces one file per sample; if it encounters PE data, it will split into forward/reverse files. However, the workflow tagging and downstream steps are configured for SE data. **Check your data type before running.**

---

## Step-by-Step Guide

### Step 1: Prepare your sample information file

You need a **tab-delimited text file** called `sample_info.txt` containing at minimum a column of SRA accession numbers (e.g., `SRR1234567`). This file may also contain additional columns with sample metadata (e.g., condition, replicate number).

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
- The SRA accession numbers **must be in column 1**
- The file must be **tab-delimited** (not comma-delimited)
- Include a header row — the Cut tool will extract all of column 1 including the header, but fasterq-dump will skip non-accession text
- Save the file with a `.txt` or `.tabular` extension

**How to find your SRA accessions:**
1. Go to NCBI GEO (ncbi.nlm.nih.gov/geo/) and search for the dataset from your assigned publication
2. Find the GEO Series (GSE...) page
3. At the bottom, click the SRA link or "SRA Run Selector"
4. The SRA Run Selector shows all runs (SRR numbers). You can download the accession list or manually copy them
5. Check the "Library Layout" column — it will say SINGLE or PAIRED. Confirm this matches the workflow you are using

---

### Step 2: Upload your sample information file to Galaxy

1. Log in to **usegalaxy.eu**
2. Click the **Upload Data** button (up arrow icon) at the top of the tool panel
3. Click **Choose local files** and select your `sample_info.txt`
4. **Important**: Set the file type to **tabular** (not auto-detect). Use the dropdown or pencil icon to change it.
5. Click **Start** to upload
6. Wait for the dataset to turn green in your history panel — this confirms the upload is complete

**Verify your upload:**
- Click the eye icon on the dataset in your history to preview it
- Confirm the columns are separated correctly (not all in one column)
- Confirm the SRA accession numbers are in column 1

---

### Step 3: Import and run the workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file `Galaxy-Workflow-1_download_se_faqstq_data.ga` provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button** (▶) next to the imported workflow
2. You will see one input: **"Provide the sample information"**
3. Select your uploaded `sample_info.txt` file from the dropdown
4. Click **Run Workflow**

---

### Step 4: Monitor the workflow execution

After clicking Run Workflow:

1. Your Galaxy history will populate with new datasets (some will be hidden — this is expected)
2. Datasets go through three states:
   - **Grey** — queued (waiting to run)
   - **Yellow** — running
   - **Green** — complete
   - **Red** — error (see troubleshooting guide)
3. The download step can take **several minutes to over an hour** depending on the number and size of your samples. usegalaxy.eu is a shared public resource and jobs are queued.
4. You do not need to keep the browser open — Galaxy will continue running the job. You can log back in later to check progress.

---

### Step 5: Verify the output

When the workflow completes successfully, you should see:

- A **dataset collection** called **"Raw RNA-seq data"** in your history
- Click the collection name to expand it — you should see one FASTQ file per SRA accession number
- Each file should be typed as **fastq.gz**
- The collection should be tagged with **raw_data**

**Verification checks:**
1. **Correct number of files**: Count the files in the collection. This should match the number of SRA accessions in your `sample_info.txt` (minus the header row).
2. **Non-zero file sizes**: Click any individual file and check that it is not empty. A FASTQ file for a typical RNA-seq sample is usually tens to hundreds of MB.
3. **Correct format**: Click the eye icon on one file. You should see FASTQ-formatted text:
   ```
   @SRR1234567.1 1/1
   ATCGATCGATCGATCG...
   +
   IIIIIIIIIIIIIII...
   ```
   Each read has 4 lines: header (@), sequence, separator (+), quality scores.

---

## Features to Note for Later Stages

Record the following information now — you will need it in subsequent stages:

| Feature | How to find it | Where to record it |
|---|---|---|
| **Number of samples** | Count files in the output collection | Your lab notebook / project notes |
| **Read type** | SE (confirmed by using this workflow) | Project notes — affects aligner settings |
| **Approximate read length** | Preview a FASTQ file and count the characters in a sequence line, or wait for FastQC (Stage 2) | Project notes — affects trimming decisions |
| **Organism** | Your assigned publication | Project notes — determines which reference genome to use |
| **Original publication** | Your assignment brief | You will cite this in your thesis |

---

## What Happens in This Workflow (Technical Summary)

For your understanding — you do not need to configure any of this manually, but knowing what happens will help you troubleshoot and write your Methods section.

| Step | Tool | What it does | Key parameter |
|---|---|---|---|
| 1 | Input dataset | Accepts your `sample_info.txt` | Format: tabular |
| 2 | Cut (v1.0.2) | Extracts column 1 (SRA accession numbers) from the table | Column: c1, Delimiter: Tab |
| 3 | Faster Download and Extract Reads in FASTQ (fasterq-dump v3.1.1) | Connects to NCBI SRA and downloads the FASTQ data for each accession | `--split-3` (handles SE/PE automatically); skips technical reads; output renamed to "Raw RNA-seq data"; tagged `raw_data`; datatype set to `fastq.gz` |

---

## For Your Thesis Methods Section

When you write up this stage, your Methods section should include language similar to:

> "Raw RNA-seq data were downloaded from the NCBI Sequence Read Archive (SRA) using the Faster Download and Extract Reads in FASTQ tool (fasterq-dump v3.1.1) on the Galaxy platform (usegalaxy.eu). Single-end FASTQ files for [n] samples were retrieved using accession numbers obtained from GEO Series [GSE number] (Author et al., Year). Data were downloaded in FASTQ format and compressed (.fastq.gz)."

Adapt this to your specific dataset — do not copy it verbatim.

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 2, you should be able to answer these questions. If you cannot, re-read the relevant section of this guide or consult the Key Concepts reference document.

1. **What is a FASTQ file?** Can you describe what the four lines of a FASTQ record represent?
2. **What does "single-end" mean?** How does it differ from paired-end, and how does this affect the number of output files?
3. **What did the Cut tool do?** Why was it needed before fasterq-dump?
4. **What does the `--split-3` flag do?** What would happen if your data were actually paired-end?
5. **How many FASTQ files should you have?** Does this match the number of SRA accessions in your input file?
6. **Can you explain to someone else what the SRA is** and why public data deposition matters for science?
7. **What read length does your dataset use?** Where did you find this information (or where will you confirm it in Stage 2)?

If you can answer all seven confidently, you are ready for Stage 2.

---

## Before You Move On: Checklist

- [ ] My output collection contains the correct number of FASTQ files
- [ ] I have previewed at least one file and confirmed it is in FASTQ format
- [ ] I have noted my read type (SE), approximate read length, and organism
- [ ] I have named my Galaxy history descriptively
- [ ] I have started drafting the Methods paragraph for this stage
- [ ] I have read the "How to Get Help" document (in the Galaxy Workflows folder)

---

## Frequently Asked Questions

**Q: The download has been running for over an hour — is this normal?**
A: Yes. usegalaxy.eu is a shared public server. Download time depends on NCBI server speed, your data size, and how busy the Galaxy server is. Do not cancel and re-submit — this puts you back in the queue.

**Q: I got a red dataset — does this mean I did something wrong?**
A: Not necessarily. Red means the job failed, but the cause may be external (NCBI timeout, server overload). Read the error message first, then check the troubleshooting guide. Many failures resolve by simply re-running.

**Q: Do I need to keep my browser open while the workflow runs?**
A: No. Galaxy runs jobs on the server. You can close your browser and come back later. Your history will be exactly as you left it.

**Q: How do I know if my data is single-end or paired-end?**
A: Check the SRA Run Selector for your dataset — the "Library Layout" column says SINGLE or PAIRED. If your data is paired-end, you need the PE version of this workflow.

**Q: I have never used a command line or written code — will I need to?**
A: No. Galaxy is entirely web-based and point-and-click. The workflows your supervisor has built will run without any coding. You just need to provide the correct input files.

**Q: My supervisor is not available and I am stuck — what do I do?**
A: Follow the "How to Get Help" guide in the Galaxy Workflows folder. In short: re-read this guide → check the troubleshooting guide → search Galaxy Help forum → post to the class forum. Do not wait for individual supervision.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
