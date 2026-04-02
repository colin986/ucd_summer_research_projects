# Stage 1: Downloading the Data (Paired-End)
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will download the raw paired-end RNA-seq FASTQ files for your assigned published dataset from NCBI's Sequence Read Archive (SRA). By the end of this stage, you will have a collection of paired FASTQ files (forward and reverse reads) in your Galaxy history, correctly labelled and tagged, ready for quality assessment in Stage 2.

> **Important:** This guide is for **paired-end (PE) data only**. If your dataset contains single-end reads, use the SE version of this workflow and its accompanying guide.

---

## Before You Begin

| What you need to know | Where to find it | Why it matters |
|---|---|---|
| **SRA accession numbers** (SRR...) for each sample | Your `sample_names.txt` file, or search on NCBI SRA/GEO | These tell Galaxy which files to download |
| **Confirm your data is paired-end** | SRA Run Selector → "Library Layout" column should say PAIRED | This workflow is configured for **paired-end** reads. Using it on SE data will produce unexpected output structure |
| **Read length** | SRA entry or FastQC output (Stage 2) | Affects downstream trimming and alignment — note it now |
| **Organism and genome build** | The original publication | Needed for alignment in later stages |

> **Key concept — Paired-end reads:**
> - Each DNA/RNA fragment is sequenced from **both ends**, producing two reads per fragment: a **forward read** (Read 1, often labelled _1) and a **reverse read** (Read 2, labelled _2).
> - This gives more information than single-end sequencing: better mapping confidence, ability to span longer fragments, and improved detection of splice junctions and structural variants.
> - Paired-end data produces **two FASTQ files per sample** (or a paired collection in Galaxy). The forward and reverse files must stay correctly paired throughout your analysis — the workflow handles this automatically, but you should understand the principle.
> - **Insert size** is the length of the original fragment between the two sequenced ends. This varies by library preparation and is relevant for some alignment tools. You can find it in the SRA metadata or estimate it after alignment.

---

## Step-by-Step Guide

### Step 1: Prepare your sample names file

You need a **comma-separated text file** called `sample_names.txt` containing the SRA accession numbers in column 1.

> **Note: This PE workflow uses a comma-separated (CSV) file, not a tab-separated file.** This differs from the SE workflow. Ensure your file uses commas as delimiters.

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
- Save the file with a `.txt` or `.csv` extension

**How to find your SRA accessions:**
1. Go to NCBI GEO (ncbi.nlm.nih.gov/geo/) and search for the dataset from your assigned publication
2. Find the GEO Series (GSE...) page
3. At the bottom, click the SRA link or "SRA Run Selector"
4. The SRA Run Selector shows all runs (SRR numbers) — confirm the "Library Layout" column says **PAIRED**
5. You can download the accession list or manually copy the SRR numbers

---

### Step 2: Upload your sample names file to Galaxy

1. Log in to **usegalaxy.eu**
2. Click the **Upload Data** button (up arrow icon) at the top of the tool panel
3. Click **Choose local files** and select your `sample_names.txt`
4. Click **Start** to upload
5. Wait for the dataset to turn green in your history panel

**Verify your upload:**
- Click the eye icon on the dataset to preview it
- Confirm the columns are separated by commas and the SRA accession numbers are in column 1
- If the file looks like everything is in one column, check whether it was saved with the correct delimiter

---

### Step 3: Import and run the workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file `Galaxy-Workflow-1_download_pe_faqstq_data.ga` provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button** (▶) next to the imported workflow
2. You will see one input: **"Provide the sample information"**
3. Select your uploaded `sample_names.txt` file from the dropdown
4. Click **Run Workflow**

---

### Step 4: Monitor the workflow execution

After clicking Run Workflow:

1. Your Galaxy history will populate with new datasets (some will be hidden)
2. Dataset states:
   - **Grey** — queued
   - **Yellow** — running
   - **Green** — complete
   - **Red** — error (see troubleshooting guide)
3. Paired-end downloads are typically **larger and slower** than single-end because there are two files per sample. Allow extra time.
4. You do not need to keep the browser open — Galaxy continues running in the background.

---

### Step 5: Verify the output

When the workflow completes, you should see:

- A **paired dataset collection** in your history — this contains the forward and reverse FASTQ files for each sample
- Both the paired collection (`list_paired`) and any single-read output (`output_collection`) are tagged with **raw_data**
- Files should be typed as **fastq.gz**

**Verification checks:**

1. **Correct number of sample pairs**: Expand the paired collection. You should see one entry per SRA accession, and each entry should contain a forward and reverse file.
2. **Both reads present**: Click into any sample pair — you should see two files (forward and reverse). Neither should be empty.
3. **Non-zero file sizes**: PE files are typically larger than SE files for the same sequencing depth. Check that files are not 0 bytes.
4. **Correct format**: Preview a file with the eye icon. You should see standard FASTQ format:
   ```
   @SRR1234567.1 1/1
   ATCGATCGATCGATCG...
   +
   IIIIIIIIIIIIIII...
   ```
5. **Forward and reverse are distinct**: The forward file read headers should end with `/1` and the reverse with `/2` (depending on the SRA record's defline format). Alternatively, forward reads will be in the `_1` file and reverse in `_2`.

---

## Features to Note for Later Stages

Record the following — you will need it in subsequent stages:

| Feature | How to find it | Why it matters |
|---|---|---|
| **Number of samples** | Count entries in the paired collection | Affects compute time and storage |
| **Read type** | PE (confirmed by using this workflow) | Affects aligner settings — PE alignment requires both files per sample |
| **Approximate read length** | Preview a FASTQ file and count characters in a sequence line, or wait for FastQC (Stage 2) | Affects trimming and alignment parameters |
| **Insert size** (approximate) | SRA metadata, or estimate post-alignment | Some aligners use insert size information |
| **Organism** | Your assigned publication | Determines which reference genome to use |
| **Strandedness** | Original publication methods section, or infer with tools in later stages | Affects quantification (e.g., featureCounts, HTSeq) — note if the publication states it |

---

## What Happens in This Workflow (Technical Summary)

| Step | Tool | What it does | Key parameter |
|---|---|---|---|
| 1 | Input dataset | Accepts your `sample_names.txt` | Format: CSV |
| 2 | Cut (v1.0.2) | Extracts column 1 (SRA accession numbers) from the CSV | Column: c1, Delimiter: Comma |
| 3 | Faster Download and Extract Reads in FASTQ (fasterq-dump v3.1.1) | Downloads FASTQ data from NCBI SRA for each accession; `--split-3` splits PE data into forward/reverse pairs | Output collections tagged `raw_data`; datatype set to `fastq.gz`; paired reads stored in `list_paired` collection |

**The `--split-3` flag:** This tells fasterq-dump to separate paired reads into forward (_1) and reverse (_2) files, and place any unpaired/orphan reads into a separate file. For clean PE data, the paired output is what you will use downstream.

---

## For Your Thesis Methods Section

When you write up this stage, your Methods section should include language similar to:

> "Raw paired-end RNA-seq data were downloaded from the NCBI Sequence Read Archive (SRA) using the Faster Download and Extract Reads in FASTQ tool (fasterq-dump v3.1.1) on the Galaxy platform (usegalaxy.eu). Paired-end FASTQ files for [n] samples ([read length]-bp reads) were retrieved using accession numbers obtained from GEO Series [GSE number] (Author et al., Year). Forward and reverse reads were split using the --split-3 option and stored in compressed FASTQ format (.fastq.gz)."

Adapt this to your specific dataset — do not copy it verbatim.

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 2, you should be able to answer these questions. If you cannot, re-read the relevant section of this guide or consult the Key Concepts reference document.

1. **What is a FASTQ file?** Can you describe what the four lines of a FASTQ record represent?
2. **What does "paired-end" mean?** Why are there two files per sample, and what do the forward and reverse reads represent?
3. **How does PE differ from SE in practice?** What are the advantages of paired-end sequencing?
4. **What did the Cut tool do?** Why was it needed, and what delimiter did it use (and why does this matter)?
5. **What does the `--split-3` flag do?** Why is splitting important for paired-end data?
6. **How many paired entries should you have?** Does this match the number of SRA accessions in your input file?
7. **What is insert size?** Where can you find it for your dataset?
8. **What read length does your dataset use?** Where will you confirm this?

If you can answer all eight confidently, you are ready for Stage 2.

---

## Before You Move On: Checklist

- [ ] My output paired collection contains the correct number of entries
- [ ] Each entry contains both a forward and reverse FASTQ file
- [ ] I have previewed at least one file and confirmed it is in FASTQ format
- [ ] I have noted my read type (PE), approximate read length, organism, and any strandedness info
- [ ] I have named my Galaxy history descriptively
- [ ] I have started drafting the Methods paragraph for this stage
- [ ] I have read the "How to Get Help" document (in the Galaxy Workflows folder)

---

## Frequently Asked Questions

**Q: The download has been running for over an hour — is this normal?**
A: Yes, especially for PE data which is roughly double the volume of SE. usegalaxy.eu is a shared server and downloads depend on NCBI speed. Do not cancel and re-submit.

**Q: I got a red dataset — does this mean I did something wrong?**
A: Not necessarily. Read the error message first, then check the troubleshooting guide. Many failures are caused by NCBI timeouts and resolve by re-running.

**Q: Do I need to keep my browser open while the workflow runs?**
A: No. Galaxy runs jobs on the server. Close your browser and come back later.

**Q: My file is tab-delimited — will that work?**
A: No. This PE workflow uses a **comma-separated (CSV)** input file. This is a common source of errors. Re-save your file as CSV. If your data is SE, use the SE workflow which takes tab-delimited input.

**Q: I see individual files instead of paired collections — what happened?**
A: Your data may actually be single-end, or the SRA record stores PE data non-standardly. Check the "Library Layout" column on the SRA Run Selector. See the troubleshooting guide section on "Missing Paired Structure."

**Q: What is the difference between `list_paired` and `output_collection`?**
A: `list_paired` contains the properly paired forward/reverse collections. `output_collection` may contain unpaired or single-end reads. For PE data, the `list_paired` output is what you will use downstream.

**Q: I have never used a command line or written code — will I need to?**
A: No. Galaxy is entirely web-based. The workflows run without any coding.

**Q: My supervisor is not available and I am stuck — what do I do?**
A: Follow the "How to Get Help" guide in the Galaxy Workflows folder. Re-read this guide → check troubleshooting → search Galaxy Help forum → post to the class forum.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
