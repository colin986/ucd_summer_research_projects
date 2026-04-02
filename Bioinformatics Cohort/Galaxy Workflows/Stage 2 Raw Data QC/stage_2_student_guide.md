# Stage 2: Raw Data Quality Control
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will assess the quality of the raw RNA-seq FASTQ files downloaded in Stage 1. By the end of this stage, you will have a MultiQC report summarising the quality of all your samples, and you will be able to make an informed decision about what trimming and preprocessing is required before alignment.

This guide covers both **single-end (SE)** and **paired-end (PE)** datasets. Your supervisor will have confirmed your data type in Stage 1 — follow the section that matches it.

---

## Why Quality Control Matters in This Project

Before performing any analysis, you must evaluate the quality of the raw sequencing data. This is not a formality — the decisions you make based on your QC report directly affect every downstream stage: trimming parameters, alignment efficiency, and ultimately the reliability of your differential expression results.

In the context of CHO cell RNA-seq for biopharmaceutical research, poor-quality data can produce false signals of differential gene expression. If a subset of reads has low base-call quality at the 3' end, or if adapter sequences are present, these artefacts will inflate apparent differences between treatment and control conditions that have nothing to do with biology. Conversely, understanding that certain QC "warnings" are expected for RNA-seq data (and not indicative of a problem) is equally important — acting unnecessarily on a normal artefact can introduce its own errors.

The two tools in this workflow — FastQC and MultiQC — do not modify your data. They only assess it. All decisions about what to do with the results (trim, filter, exclude samples) are made by you, guided by the outputs and these notes.

---

## Before You Begin

Confirm the following before running this workflow:

- [ ] Stage 1 completed successfully — you have a collection of FASTQ files in your Galaxy history tagged `raw_data`
- [ ] You know your data type (SE or PE), confirmed by your supervisor
- [ ] You have read your assigned paper and noted the sequencing platform, library preparation method, and any QC filtering the original authors applied

The sequencing platform and library preparation method reported in your paper are directly relevant to interpreting your QC output. For example, Illumina short-read RNA-seq is expected to show a quality drop at the 3' end of reads; polyA-selected libraries show different sequence content patterns than rRNA-depleted libraries. Know your data before assessing it.

---

## Understanding What FastQC and MultiQC Do

### FastQC

FastQC analyses each FASTQ file individually and produces a report covering approximately ten quality modules. Each module is assessed against built-in thresholds and assigned a status: **pass** (green tick), **warn** (orange exclamation), or **fail** (red cross).

> **Important:** FastQC's thresholds were designed for whole-genome sequencing (WGS) of genomic DNA. RNA-seq data routinely triggers warnings and failures in several modules due to the biological properties of RNA — not because the data is bad. You must interpret each module in the context of RNA-seq, not WGS. This is explained in detail in the [Interpreting Your QC Results](#interpreting-your-qc-results) section below.

### MultiQC

MultiQC aggregates the FastQC outputs from all your samples into a single interactive HTML report. Rather than reviewing ten individual FastQC reports one by one, MultiQC lets you compare all samples side by side, making it easy to spot outliers — samples that look different from the rest of the cohort, which may indicate a download problem, a sample swap, or a genuine quality issue.

---

---

## Section A — Single-End (SE) Workflow

Follow this section if your supervisor confirmed your data is **single-end**.

---

### A1: Confirm Your Input

The SE QC workflow takes the **"Raw RNA-seq data"** collection from Stage 1 as its input — this is the flat list collection of fastq.gz files, tagged `raw_data`.

Before running, verify this collection is present in your history:
- Click the collection name to expand it
- Confirm the number of files matches your number of samples
- Confirm files are typed as `fastq.gz`

---

### A2: Import and Run the SE QC Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file **`Galaxy-Workflow-2_raw_rnaseq_data_qc_se.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported SE QC workflow
2. Under **"raw_fastq_files"**, select your **"Raw RNA-seq data"** collection from Stage 1
3. Click **Run Workflow**

---

### A3: Monitor and Verify (SE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- FastQC runs on each file independently — jobs may complete at different times. This is normal.
- MultiQC will not start until all FastQC jobs are complete
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below

**When complete, verify:**
- A collection called **"Raw Data QC"** appears in your history, tagged `results`
- Expand it — it should contain two items: **HTML** (the interactive MultiQC report) and **Tabular** (the summary statistics table)
- Click the eye icon on the HTML item to open the MultiQC report in your browser
- Confirm the report header shows the correct number of samples and is titled "Raw RNA-seq quality"

---

### A4: SE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input dataset collection | Accepts the "Raw RNA-seq data" list collection (fastq.gz) from Stage 1 |
| 2 | FastQC (v0.74+galaxy1) | Runs quality analysis on each FASTQ file independently; produces an HTML report and a text summary per file (both hidden) |
| 3 | MultiQC (v1.33+galaxy0) | Aggregates all FastQC text outputs into a single interactive HTML report ("Raw RNA-seq Data Quality HTML") and a tabular summary ("Raw RNA-seq Data Quality Tabular"), tagged `raw,qc` |
| 4 | Build list | Packages the MultiQC HTML and tabular outputs into the final "Raw Data QC" collection, tagged `results` |

---

---

## Section B — Paired-End (PE) Workflow

Follow this section if your supervisor confirmed your data is **paired-end**.

---

### B1: Confirm Your Input

The PE QC workflow takes the **`list_paired`** collection from Stage 1 as its input — this is the paired collection containing forward and reverse FASTQ files for each sample, tagged `raw_data`.

Before running, verify this collection is present in your history:
- Click the collection name to expand it
- Each sample entry should contain a forward and a reverse file
- Confirm files are typed as `fastq.gz`

---

### B2: Import and Run the PE QC Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file **`Galaxy-Workflow-2_raw_rnaseq_data_qc_pe.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported PE QC workflow
2. Under **"processed_fastq_files"**, select your **`list_paired`** collection from Stage 1
3. Click **Run Workflow**

---

### B3: Monitor and Verify (PE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- The Flatten step runs first, converting the paired collection into a flat list so FastQC can process forward and reverse reads individually
- FastQC then runs on each flattened file — for PE data you will see **twice as many FastQC jobs** as you have samples (one for forward, one for reverse)
- MultiQC will not start until all FastQC jobs are complete
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below

**When complete, verify:**
- A collection called **"Raw RNA-seq Data QC Analysis"** appears in your history, tagged `qc,preprocessed`
- Expand it — it should contain two items: **HTML** and **Table** (the MultiQC outputs)
- Open the HTML report and confirm it shows entries for both forward (`_1`) and reverse (`_2`) files for each sample
- A PE dataset with 6 samples should show 12 entries in the MultiQC report

---

### B4: PE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input dataset collection | Accepts the `list_paired` paired collection (fastq.gz) from Stage 1 |
| 2 | Flatten collection (v1.0.0) | Converts the paired collection into a flat list, joining identifiers with `_`, so FastQC can process forward and reverse files individually (hidden) |
| 3 | FastQC (v0.74+galaxy1) | Runs quality analysis on each individual FASTQ file (forward and reverse separately); produces an HTML report and a text summary per file (both hidden) |
| 4 | MultiQC (v1.33+galaxy0) | Aggregates all FastQC outputs into "Raw RNA-seq Data Quality HTML" and "Raw RNA-seq Data Quality Tabular", tagged `qc,raw_data` |
| 5 | Build list | Packages HTML and tabular outputs into the "Raw RNA-seq Data QC Analysis" collection, tagged `qc,preprocessed` |

> **Note for PE data:** MultiQC will report forward and reverse reads separately. In general, reverse reads (Read 2) show slightly lower quality scores than forward reads (Read 1) — this is normal for Illumina paired-end sequencing and does not indicate a problem with the data.

---

---

## Interpreting Your QC Results

Open your MultiQC HTML report. This section explains what each module means for RNA-seq data from CHO cells, and what action (if any) each result requires.

---

### Module 1: Basic Statistics

Reported per sample. Check:

| Metric | What to look for |
|---|---|
| **Total sequences** | Should match the read counts you recorded in Stage 1. Large discrepancies suggest a mismatch between files. |
| **Sequence length** | Should be consistent across all samples and match the read length reported in the original paper. |
| **%GC** | Chinese hamster (*Cricetulus griseus*) genome GC content is approximately 41%. Expect per-sample GC to be in the 40–50% range depending on transcript composition. Values outside this range, or large variation between samples, warrant investigation. |

---

### Module 2: Per Base Sequence Quality

**What it shows:** The distribution of base call quality scores (Phred scores) at each position along the read.

**What to expect for RNA-seq:**
- Quality scores should remain high (above Q28) across most of the read length
- A gradual decline in quality toward the **3' end of reads** is normal for Illumina sequencing — the signal degrades over longer synthesis cycles
- A decline in the first 10–15 bases at the **5' end** is also common due to random hexamer priming bias (see Module 4 below)

**Action required:**
- If quality drops below Q20 across a large portion of reads (not just the 3' tail), this warrants trimming — note the position where quality degrades, as this will inform your trimming parameters in Stage 3
- If one sample shows dramatically worse quality than the others, flag it — it may need to be excluded or treated cautiously in downstream analysis

---

### Module 3: Per Sequence Quality Scores

**What it shows:** The distribution of mean quality scores across all reads in the file — i.e., how many reads have a mean quality of Q10, Q20, Q30, etc.

**What to expect:** The distribution should peak at Q30 or higher. A peak below Q28 suggests widespread poor quality. A broad or double-peaked distribution may indicate a mixed-quality dataset.

---

### Module 4: Per Base Sequence Content

**What it shows:** The proportion of A, T, G, C at each base position. For random genomic data, these should be roughly equal and flat across the read.

**What to expect for RNA-seq:** This module **almost always fails** for RNA-seq data, and this is expected and normal. There are two common causes:

1. **Random hexamer priming bias** — library preparation uses random hexamer primers to initiate cDNA synthesis. These primers have non-random sequence preferences, which creates a biased base composition in approximately the first 10–15 bases of each read. This is a well-documented artefact of RNA-seq library preparation and does not indicate a quality problem.
2. **polyA tail artefacts** — if your library is polyA-selected, you may see an imbalance in A content at the 3' end.

> Do not be alarmed by a FAIL in this module. It is one of the most frequently misinterpreted FastQC outputs. Check your paper's library preparation method — if it used random hexamer priming (which most short-read RNA-seq protocols do), the 5' bias is expected.

---

### Module 5: Per Sequence GC Content

**What it shows:** The distribution of GC content across all reads, compared to a theoretical normal distribution.

**What to expect for RNA-seq:**
- A single, roughly bell-shaped peak centred near the expected GC content (~41% for Chinese hamster) is normal
- A broader or shifted peak is common in RNA-seq because transcript GC content is not uniform — highly expressed genes can shift the distribution
- A **bimodal peak** (two humps) or a sharp secondary peak may indicate contamination (e.g., from rRNA, adapter dimers, or a different organism) and should be investigated
- Consistent GC distributions across all samples is more important than the absolute position — if one sample looks different from the rest, investigate that sample

---

### Module 6: Per Base N Content

**What it shows:** The proportion of bases called as N (no call) at each position.

**What to expect:** This should be near zero throughout. Any spike in N content indicates a sequencing problem at that position. This rarely affects well-prepared RNA-seq data from a reputable facility.

---

### Module 7: Sequence Length Distribution

**What it shows:** The distribution of read lengths.

**What to expect:** For standard Illumina short-read sequencing you should see a tight, single peak at the expected read length (e.g., 75 bp, 100 bp, or 150 bp). A uniform read length across all samples is expected.

---

### Module 8: Sequence Duplication Levels

**What it shows:** The proportion of reads that appear more than once — i.e., how many reads are duplicates.

**What to expect for RNA-seq:** This module **routinely warns or fails** for RNA-seq, and this is expected and normal. In RNA-seq, highly expressed genes generate enormous numbers of reads — when you sequence a transcript at very high depth, many reads will map to the same position and appear identical. This is biological duplication, not PCR duplication, and it is an inherent property of RNA-seq data.

> **Do not deduplicate RNA-seq data.** Unlike ChIP-seq or DNA-seq, removing duplicates from RNA-seq data removes genuine signal from highly expressed genes and introduces severe bias into differential expression analysis. A high duplication level in FastQC does not require any action for RNA-seq.

---

### Module 9: Overrepresented Sequences

**What it shows:** Sequences that appear more frequently than expected by chance.

**What to look for:**
- **Adapter sequences** — FastQC will often identify these as Illumina adapters. If present in significant amounts (>1% of reads), they will be removed in Stage 3 (trimming).
- **rRNA sequences** — if your library preparation did not fully deplete ribosomal RNA, rRNA sequences may appear as overrepresented. Check your paper's library preparation method (polyA selection should exclude rRNA; rRNA depletion protocols are less stringent).
- **Unknown sequences** — if FastQC cannot identify an overrepresented sequence, note it and discuss with your supervisor.

---

### Module 10: Adapter Content

**What it shows:** Whether Illumina adapter sequences are present in your reads, and at what positions.

**What to expect:** Adapter content should be very low (<1%) for most of the read. Adapters typically appear at the 3' end of reads, most commonly when the insert size is shorter than the read length — the sequencer reads through the insert into the adapter.

**Action required:** If adapter content is present at >1% for a significant portion of reads, this will be addressed in Stage 3 (trimming). Note which adapter type is flagged — this will inform your Trimmomatic or fastp settings.

---

### Overall Assessment: What Do I Do With These Results?

After reviewing the MultiQC report, you should be able to answer the following:

1. **Are all samples broadly consistent?** Outlier samples (those that look very different from the group) may indicate a download error, a sample mix-up, or a genuine quality issue. Flag these before proceeding.
2. **Is adapter content present?** If yes, trimming is required in Stage 3.
3. **Is there a quality drop at the 3' end that warrants trimming?** If quality drops below Q20 at the 3' end, quality trimming is required in Stage 3.
4. **Are GC content distributions consistent?** Large inter-sample variation warrants investigation.
5. **Are there any unexpected overrepresented sequences?** These should be identified and understood before proceeding.

In most cases with publicly deposited RNA-seq data, the data will be of acceptable quality. The original authors will also have published their QC approach — compare your findings to theirs. If they report similar issues and their analysis proceeded, yours can too.

---

## Diagnosing a Red Dataset

A red dataset means the job failed. Follow the same diagnostic procedure described in the Stage 1 Student Guide. In brief:

1. **Click the red dataset** to expand it in your history
2. **Click the info icon (i)** to open the job details
3. **Read Tool Standard Error** first — this contains the most useful diagnostic information
4. **Check the exit code** in Job Information: code `1` = tool error (read stderr); code `137` = killed by server (memory or time limit)
5. **Identify the root failure** — if multiple datasets are red, the first one to fail is the root cause. Check which tool generated it (shown in the dataset header)

**Common Stage 2 errors:**

| Error or symptom | Cause | Fix |
|---|---|---|
| FastQC fails on one file | The FASTQ file may be corrupted or empty (0 bytes) | Check that the Stage 1 output file is non-empty; if empty, re-run Stage 1 for that accession |
| MultiQC fails with "no input data" | FastQC jobs did not all complete successfully | Check that all FastQC jobs are green before MultiQC runs; re-run any failed FastQC jobs |
| PE workflow: Flatten step fails | The input collection is not a paired collection | Confirm you selected the `list_paired` output from Stage 1, not the flat output |
| Jobs stuck grey for >2 hours | usegalaxy.eu queue is busy | Wait; do not cancel and re-submit |
| MultiQC report shows fewer samples than expected | Some FastQC jobs failed silently | Unhide hidden datasets in your history and check for red FastQC outputs |

For errors not listed here, copy the full error text from Tool Standard Error and search the Galaxy Help forum (help.galaxyproject.org), or bring it to your supervision session.

---

## Recording Your QC Results for the Written Assessment

This section applies to all bioinformatics cohort students. Your Stage 2 QC results feed directly into your Methods and Results sections, which together account for 25 of the 50 marks available for scientific content.

---

### What to Record

From your MultiQC report, record the following for every sample:

| Metric | Where to find it in MultiQC | Why it matters |
|---|---|---|
| Total reads per sample | General Statistics table | Confirms Stage 1 read counts; samples with very different depths are flagged here |
| Mean quality score (Q score) | General Statistics table | Summary of overall read quality |
| %GC | General Statistics table | Consistency check across samples |
| Adapter content (yes/no, %) | Adapter Content module | Determines whether trimming is required in Stage 3 |
| Any failed modules | Top of the MultiQC report | Documents data quality and justifies any trimming decisions |
| Any outlier samples | Visible as outliers across multiple modules | May need to be flagged or excluded |

Save the MultiQC HTML report — you will reference it when writing up and it may be included as a supplementary figure.

---

### How This Feeds into Your Written Submission

#### Methods Section (rubric A2 — 10 marks)

Your Methods section must describe the QC step. Include:

- The tools used and their versions (FastQC v0.74, MultiQC v1.33, on usegalaxy.eu)
- What QC metrics were assessed
- Any filtering or exclusion criteria applied based on QC results
- A statement that QC was performed prior to trimming and alignment

**Example Methods language:**

> "Raw FASTQ files were assessed for quality using FastQC (v0.74, Galaxy platform, usegalaxy.eu). Quality metrics across all samples were aggregated and visualised using MultiQC (v1.33). Metrics assessed included per-base sequence quality, sequence duplication levels, adapter content, and GC content distribution. [If applicable: Samples with mean quality scores below Q28 / with >X% adapter content were flagged for trimming in the subsequent preprocessing stage.]"

A strong Methods section (A-grade) names both tools with versions, lists the specific metrics assessed, and links QC findings to downstream decisions (trimming parameters). A weak Methods section (C-grade or below) simply states "quality control was performed" without specifics.

---

#### Results Section (rubric A3 — 15 marks)

Your Results section should include a concise summary of your QC findings before describing your main analysis.

**Recommended approach:**

Include the MultiQC summary as a figure (counts toward your 3 main-text figure/table allowance). A screenshot of the General Statistics table from the MultiQC HTML report, showing per-sample read counts, quality scores, and GC content for all samples, is the most efficient use of one figure.

Accompany the figure with a short paragraph:

> "Raw RNA-seq data quality was assessed using FastQC and summarised with MultiQC (Figure 1). All [n] samples had mean Phred quality scores above Q[X] and GC content in the range [X–X]%, consistent with the expected Chinese hamster transcriptome composition. [Per base sequence content warnings at the 5' end were observed across all samples, consistent with random hexamer priming bias inherent to the library preparation protocol and not indicative of data quality issues.] [Adapter sequences were detected at low levels (<X%) in all samples and were subsequently removed during preprocessing (Stage 3).]"

> **Supplementary vs. main text:** The MultiQC HTML interactive report, or the full tabular statistics for all samples, should go to Supplementary (cited explicitly: "Full per-sample QC statistics are provided in Supplementary Table S1"). The main text needs only a summary.

A strong Results section (A-grade) quantifies all QC metrics, explains the biological reason for any expected warnings (e.g., hexamer priming bias), and explicitly connects QC findings to the decisions made in subsequent stages. A weak Results section (C-grade or below) states only that "QC was good" without reporting the actual values.

---

#### Discussion Section (rubric A4 — 15 marks)

Your Discussion should briefly address data quality in the context of the original publication:

- Did the original authors report similar QC metrics? If their paper reports a mean quality score or adapter content level, compare it to yours.
- Did the original authors apply any quality filtering or exclusions? If so, are your filtering decisions consistent with theirs?
- If any of your samples appeared as outliers in QC, discuss this limitation honestly.

Two to three sentences is sufficient — this is contextualisation, not the main focus of the Discussion.

---

### Summary: Stage 2 Contributions to Your Submission

| Submission section | What Stage 2 provides | Rubric component |
|---|---|---|
| Methods | Tools (FastQC v0.74, MultiQC v1.33), metrics assessed, filtering criteria | A2 (10 marks) |
| Results | QC summary figure (MultiQC General Statistics); paragraph quantifying quality, GC content, adapter status | A3 (15 marks) |
| Discussion | Comparison of QC findings to original publication; justification of any filtering decisions | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 3, you should be able to answer:

1. What does Phred quality score (Q score) mean? What is the relationship between Q score and base-call error probability?
2. Why does per base sequence content routinely fail for RNA-seq data? What causes the 5' bias?
3. Why should you not deduplicate RNA-seq data, even when FastQC reports high duplication levels?
4. What would a bimodal GC content distribution suggest about your data?
5. How do the QC results inform your choices in Stage 3 (trimming)? What information from FastQC tells you whether trimming is necessary, and what parameters to use?
6. What is the difference between a FastQC warning and an actual problem with your data?
7. For PE data: why does MultiQC show twice as many entries as you have samples? Are you expected to see differences between forward and reverse reads?
8. How will you describe this stage in your Methods section?

---

## Before You Move On: Checklist

- [ ] My MultiQC report is present in my Galaxy history and opens correctly in the browser
- [ ] I have reviewed all ten FastQC modules in the MultiQC report and recorded the key metrics for each sample
- [ ] I understand which warnings are expected for RNA-seq and which require action
- [ ] I have noted whether adapter trimming will be required in Stage 3
- [ ] I have noted whether any samples are outliers and discussed this with my supervisor if needed
- [ ] I have saved or exported the MultiQC HTML report for use in my written submission
- [ ] I have compared my QC findings to what the original paper reports about data quality

---

## Frequently Asked Questions

**Q: FastQC shows a red FAIL on several modules — does this mean my data is bad?**
A: Not necessarily. Several FastQC modules routinely fail for RNA-seq data due to the properties of RNA libraries, not data quality problems. Per base sequence content and sequence duplication almost always fail for RNA-seq — this is expected. Read each module result in the context of RNA-seq (see [Interpreting Your QC Results](#interpreting-your-qc-results) above) before drawing conclusions.

**Q: One of my samples looks very different from the others in the MultiQC report — what should I do?**
A: An outlier sample that differs from the group in multiple modules (quality, GC content, duplication) warrants investigation. First check that the Stage 1 download for that sample completed correctly (non-zero file size, correct accession). If the download looks fine, discuss the sample with your supervisor — in rare cases, a sample may need to be excluded from analysis.

**Q: My per base sequence content module fails — do I need to trim the first 10 bases?**
A: Trimming the 5' bases affected by hexamer priming bias is sometimes recommended but is not universally required. Most modern aligners and DE tools are robust to this bias. Your supervisor will advise on whether to apply 5' trimming for your specific dataset. In your written submission, you should acknowledge the bias and explain what action (if any) was taken.

**Q: The MultiQC report shows adapter sequences — how much is too much?**
A: If adapter content exceeds approximately 1% of reads at any position, adapter trimming is recommended in Stage 3. Even low levels of adapter contamination can reduce alignment efficiency. Note the adapter type flagged (usually Illumina Universal Adapter or TruSeq) — you will use this information when configuring your trimmer in Stage 3.

**Q: For PE data, my reverse reads (Read 2) have lower quality than forward reads (Read 1) — is this a problem?**
A: This is normal for paired-end Illumina sequencing. The signal from Read 2 is inherently noisier because it is sequenced after Read 1 on the same cluster. A modest quality difference between forward and reverse reads does not indicate a problem.

**Q: Do I need to keep my browser open while the workflow runs?**
A: No. Galaxy runs jobs on the server. Close your browser and return later — your history will be exactly as you left it. The workflow is also configured to send you an email when the MultiQC report is ready.

**Q: My supervisor is not available and I am stuck — what do I do?**
A: Re-read this guide and the troubleshooting steps above. For platform issues, search the Galaxy Help forum (help.galaxyproject.org). For questions about interpreting specific QC results, search for the module name alongside "RNA-seq" — the FastQC documentation and community forum have extensive guidance on RNA-seq-specific patterns.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
