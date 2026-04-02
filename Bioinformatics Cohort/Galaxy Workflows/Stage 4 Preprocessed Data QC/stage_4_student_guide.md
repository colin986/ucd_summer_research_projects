# Stage 4: Preprocessed Data Quality Control
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will run a second round of quality control on your preprocessed (trimmed) FASTQ files to confirm that Stage 3 preprocessing was effective and that your data is ready for alignment. By the end of this stage, you will have a post-trimming MultiQC report and a clear decision on whether your data meets the quality standard required to proceed to Stage 5.

This guide covers both **single-end (SE)** and **paired-end (PE)** datasets.

---

## The Purpose of Stage 4

Stage 4 runs the same tools as Stage 2 (FastQC + MultiQC), but the context and intent are entirely different. Stage 2 was an initial characterisation of unknown data. Stage 4 is a confirmation step — you are asking a specific set of questions about whether preprocessing worked as expected.

**The three questions Stage 4 must answer:**

1. **Were adapters successfully removed?** Adapter content in your post-trimming data should be at or near zero across all samples. If adapters are still present above 0.5%, trimming did not work as expected and you should not proceed to alignment until this is resolved.

2. **Has quality improved at the 3' end of reads?** The quality drop identified in Stage 2 should be reduced or absent. If quality still degrades sharply below Q20 before the end of reads, trimming may have been insufficient.

3. **Are all samples still consistent with each other?** Trimming should affect all samples similarly. If one sample looks dramatically different from the rest after trimming — particularly with a very low mean read length or very few reads — it may have been over-trimmed or had a pre-existing quality problem that was masked in Stage 2.

If all three questions can be answered satisfactorily, your data is ready for alignment.

---

## Before You Begin

Confirm the following before running this workflow:

- [ ] Stage 3 completed successfully — you have a **"Preprocessed RNA-seq data"** collection in your Galaxy history, tagged `preprocessed_data`
- [ ] You have reviewed the Stage 3 Trimmomatic survival rates for all samples and noted any outliers
- [ ] You have your Stage 2 MultiQC report available to compare against — you will need both reports open simultaneously

---

---

## Section A — Single-End (SE) Workflow

---

### A1: Confirm Your Input

The SE Stage 4 workflow takes the **"Preprocessed RNA-seq data"** collection from Stage 3 as its input — the flat list of trimmed fastq.gz files tagged `preprocessed_data`.

> **Important:** The SE workflow is configured to look for a collection tagged `preprocessed_data`. If you select a collection with a different tag, the workflow may not recognise it. Confirm the tag on your Stage 3 output before running.

---

### A2: Import and Run the SE QC Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_se.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported SE Stage 4 QC workflow
2. Under **"processed_fastq_files"**, select your **"Preprocessed RNA-seq data"** collection from Stage 3
3. Click **Run Workflow**

---

### A3: Monitor and Verify (SE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- FastQC jobs will run independently per sample; MultiQC waits for all to complete
- The workflow will email you when the MultiQC report is ready
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below

**When complete, verify:**
- A collection called **"Preprocessed data QC"** appears in your history, tagged `results`
- Expand it — it contains **HTML** (the MultiQC report) and **Tabular** (the statistics file)
- Open the HTML report and confirm the correct number of samples is shown

---

### A4: SE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input dataset collection | Accepts the "Preprocessed RNA-seq data" list collection (fastq.gz, tagged `preprocessed_data`) from Stage 3 |
| 2 | FastQC (v0.74+galaxy1) | Runs quality analysis on each trimmed FASTQ file independently (both outputs hidden) |
| 3 | MultiQC (v1.33+galaxy0) | Aggregates FastQC outputs into "Preprocessed RNA-seq Data Quality HTML" and "Preprocessed RNA-seq Data Quality Tabular", tagged `preprocessed,qc`; emails HTML report on completion |
| 4 | Build list | Packages HTML and Tabular into the "Preprocessed data QC" collection, tagged `results` |

---

---

## Section B — Paired-End (PE) Workflow

---

### B1: Confirm Your Input

The PE Stage 4 workflow takes your preprocessed paired collection as its input — trimmed fastq.gz files in a paired collection structure.

> **Note for PE students:** If you ran the PE post-trimming QC as part of Stage 3 (using `Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_pe.ga` from the Stage 3 folder), you have already completed Stage 4. The workflow file in this folder is identical. Confirm with your supervisor whether you need to run it again here or whether the Stage 3 output satisfies Stage 4.

---

### B2: Import and Run the PE QC Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_pe.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported PE Stage 4 QC workflow
2. Under **"processed_fastq_files"**, select your preprocessed paired collection from Stage 3
3. Click **Run Workflow**

---

### B3: Monitor and Verify (PE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- The Flatten step runs first; FastQC then processes forward and reverse reads individually
- For PE data with 6 samples, expect 12 FastQC jobs before MultiQC runs
- The workflow will email you when the MultiQC report is ready

**When complete, verify:**
- A collection called **"Preprocessed Data QC Analysis"** appears in your history, tagged `qc,preprocessed,results`
- Expand it — it should contain **HTML** and **Table** items
- Open the report and confirm both forward (`_1`) and reverse (`_2`) files are present for each sample

---

### B4: PE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input dataset collection | Accepts the preprocessed paired collection (fastq.gz) from Stage 3 |
| 2 | Flatten collection (v1.0.0) | Converts paired collection to flat list for individual FastQC processing (hidden) |
| 3 | FastQC (v0.74+galaxy1) | Runs quality analysis on each file (forward and reverse separately) (both outputs hidden) |
| 4 | MultiQC (v1.33+galaxy0) | Aggregates outputs into "Preprocessed RNA-seq Data Quality HTML" and "Preprocessed RNA-seq Data Quality Tabular", tagged `preprocessed,qc`; emails HTML on completion |
| 5 | Build list | Packages outputs into "Preprocessed Data QC Analysis" collection, tagged `qc,preprocessed,results` |

---

---

## Interpreting the Stage 4 QC Report

You should already be familiar with FastQC modules and their interpretation for RNA-seq data from Stage 2. Refer to the **Stage 2 Student Guide** for module-by-module explanations. This section focuses on what is different and what specifically to look for at Stage 4.

---

### What Should Have Changed Since Stage 2

| FastQC module | Expected change after trimming | Action if not changed |
|---|---|---|
| **Adapter Content** | Should be at or near zero (< 0.5%) across all positions | If still elevated, trimming did not remove adapters — check that the correct adapter file was used in Stage 3; discuss with supervisor before proceeding to alignment |
| **Per Base Sequence Quality** | 3' quality drop should be reduced or absent; reads should maintain Q20 or above for more of their length | If quality still drops sharply before the end of reads, consider whether stricter trimming is needed; discuss with supervisor |
| **Sequence Length Distribution** | Reads will no longer all be the same length — you will see a distribution of lengths | This is expected and not a problem — it is the direct result of variable-length trimming |
| **Total reads** | Slightly lower than Stage 2 — the reads dropped by Trimmomatic are no longer present | A small reduction (< 10%) is normal; a large reduction should match the survival rates recorded from the Trimmomatic log in Stage 3 |

---

### What Should Not Have Changed

| FastQC module | Why it does not change | What to do |
|---|---|---|
| **Per Base Sequence Content** | The 5' hexamer priming bias is biological, not adapter contamination — trimming does not remove it | Expected — no action required; note this in your written submission |
| **Sequence Duplication Levels** | Duplication reflects transcript abundance, not data quality — trimming does not alter this | Expected — no action required |
| **Per Sequence GC Content** | GC content distribution reflects transcript composition — trimming removes a small fraction of reads and does not meaningfully shift the distribution | Expected — minor shifts may be visible; a large shift warrants investigation |

---

### New Things to Record at Stage 4

**Mean read length after trimming:**  
In Stage 2, all reads were the same length (e.g., 75 bp or 100 bp). After trimming, reads vary in length. The MultiQC General Statistics table will show the **median read length** for each sample after trimming. Record this value — you will need it when selecting alignment parameters in Stage 5, as some aligners are sensitive to read length.

**Post-trimming read count:**  
The General Statistics table shows the total reads remaining after trimming. Compare this to the input reads from Stage 1 and the survival rates from Stage 3. The numbers should be consistent.

---

### Making the Go/No-Go Decision

Before proceeding to Stage 5, your Stage 4 QC report should satisfy all of the following:

| Criterion | Pass | Discuss with supervisor before proceeding |
|---|---|---|
| Adapter content | < 0.5% across all positions in all samples | Adapter content still present at > 1% in any sample |
| Per-base quality | Q20 or above for the substantial majority of each read's length | Quality drops below Q20 across more than the last 5 bases of most reads |
| Sample consistency | All samples show similar quality profiles and read length distributions | One or more samples looks markedly different from the group |
| Total reads retained | > 80% of Stage 1 reads across all samples | Any sample retains < 70% of Stage 1 reads |

If all criteria are met, proceed to Stage 5. If any criterion is not met, do not proceed until you have discussed the issue with your supervisor.

---

## Diagnosing a Red Dataset

Follow the same diagnostic procedure described in the Stage 1 Student Guide. The errors most likely to occur at Stage 4 are:

| Error or symptom | Cause | Fix |
|---|---|---|
| FastQC fails on one or more files | A trimmed FASTQ file may be empty (all reads were dropped by Trimmomatic) | Check the Trimmomatic log for that sample in Stage 3 — if survival rate was 0%, the source data had a problem. Discuss with supervisor. |
| MultiQC fails with "no input data" | One or more FastQC jobs failed | Check all FastQC jobs are green; re-run any that are red |
| SE workflow rejects input collection | Collection is not tagged `preprocessed_data` | Click the pencil icon on the collection in your history and add the tag `preprocessed_data`, then re-run |
| PE workflow Flatten step fails | Input is not a paired collection | Confirm you selected the preprocessed paired collection, not a flat list |

---

## Recording Your Stage 4 Results for the Written Assessment

Stage 4 does not generate new scientific findings — it confirms that your data processing pipeline has been executed correctly. Its contribution to your written submission is as part of the Methods narrative and as supporting evidence for the data quality claims in your Results.

---

### What to Record

| Metric | Where to find it |
|---|---|
| Post-trimming total reads per sample | MultiQC General Statistics table |
| Median read length after trimming | MultiQC General Statistics table |
| Adapter content post-trimming | Adapter Content module — should be ≈ 0% |
| Any remaining quality issues | Per Base Sequence Quality module |
| Confirmation of sample consistency | Compare samples visually in MultiQC — are all profiles similar? |

---

### How This Feeds into Your Written Submission

Stage 4 does not typically require a dedicated paragraph in the Results section. It is reported as part of the preprocessing narrative introduced in Stage 3, and as evidence supporting the quality of the data used in your main analysis.

#### Methods Section (rubric A2 — 10 marks)

Add a sentence confirming that post-trimming quality was verified:

> "Following trimming, data quality was re-assessed using FastQC (v0.74) and MultiQC (v1.33) to confirm successful adapter removal and retention of read quality above Q20. All samples were confirmed to meet quality thresholds prior to alignment."

#### Results Section (rubric A3 — 15 marks)

The pre/post-trimming comparison is most effectively reported by extending the paragraph begun in Stage 3:

> "Post-trimming quality assessment confirmed adapter content was reduced to < 0.1% in all samples, with a mean post-trimming read length of [X] bp and a mean of [X]% of Stage 1 reads retained (Supplementary Table S[X])."

If there was a visible and meaningful improvement in per-base quality that you want to show, a before/after comparison of the MultiQC per-base quality plot can be included — but use your figure allowance carefully. Three main-text figures must cover your entire submission; do not spend them on a QC comparison that can be adequately described in a sentence.

#### Discussion Section (rubric A4 — 15 marks)

Stage 4 does not normally require direct Discussion content beyond the brief contextualisation already noted under Stage 3. If your post-trimming data revealed a persistent issue in one or more samples that you then had to exclude or flag, discuss this as a limitation.

---

### Summary: Stage 4 Contributions to Your Submission

| Submission section | What Stage 4 provides | Rubric component |
|---|---|---|
| Methods | Confirmation sentence — post-trimming QC tools, quality threshold met | A2 (10 marks) |
| Results | Post-trimming read counts, median read length, confirmation of adapter removal (extends Stage 3 paragraph) | A3 (15 marks) |
| Discussion | Only if a sample-level issue was identified — flagged as a limitation | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 5, you should be able to answer:

1. What are the three specific questions Stage 4 is designed to answer? Can you answer all three for your dataset?
2. Why does the sequence length distribution look different in Stage 4 compared to Stage 2? What does a variable-length distribution tell you?
3. Why is it expected that per base sequence content still shows a 5' bias after trimming? What causes this and why doesn't trimming fix it?
4. What is the median read length in your post-trimming data? Why does this matter for Stage 5?
5. If adapter content is still present after trimming, what does this suggest about the Stage 3 settings, and what would you do?
6. Does your data satisfy all four go/no-go criteria? If not, what action are you taking?
7. How does the total read count in your post-trimming data compare to Stage 1? Does this match the survival rates you recorded from Trimmomatic?

---

## Before You Move On: Checklist

- [ ] My "Preprocessed data QC" (SE) or "Preprocessed Data QC Analysis" (PE) collection is present and opens correctly
- [ ] Adapter content is < 0.5% across all positions in all samples
- [ ] Per-base quality is maintained at Q20 or above across the majority of read length in all samples
- [ ] All samples show consistent quality profiles — no unexplained outliers
- [ ] I have recorded the post-trimming total reads and median read length for each sample
- [ ] All four go/no-go criteria are met — I am ready to proceed to Stage 5
- [ ] I have saved the Stage 4 MultiQC report for reference in my written submission

---

## Frequently Asked Questions

**Q: Stage 4 looks identical to Stage 2 — do I need to re-read all the FastQC module explanations?**
A: No. The tools and modules are the same. The Stage 2 Student Guide covers all module interpretations in detail. Refer to it if you need a reminder. What is different here is the purpose: you are not characterising data for the first time, you are verifying that preprocessing worked. Focus on the specific changes listed in this guide.

**Q: Adapter content is still showing at 0.3% after trimming — is this acceptable?**
A: A residual adapter content of less than 0.5% is generally considered acceptable and will not meaningfully impact alignment. Values between 0.5% and 1% are borderline — note it and discuss with your supervisor. Values above 1% after trimming indicate a problem that should be resolved before proceeding to alignment.

**Q: My reads are now shorter than expected — some are as short as 30 bp. Is this a problem?**
A: Short trimmed reads can occasionally cause issues at the alignment stage, particularly for aligning to the Chinese hamster genome. If you see a significant proportion of very short reads (< 30 bp) in the sequence length distribution, discuss with your supervisor. This may indicate that your Stage 2 data had quality issues more severe than typical, or that a MINLEN parameter should have been applied in Stage 3.

**Q: My total reads in Stage 4 are slightly different from what I expected based on the Trimmomatic survival rates — why?**
A: Small discrepancies may arise from rounding in the log output. Differences of more than 1–2% between the Trimmomatic-reported survival and the Stage 4 read count are unusual and should be investigated — check that you selected the correct collection as input for this workflow.

**Q: The MultiQC report title still says "Raw RNA-seq quality" even though this is preprocessed data — is this a mistake?**
A: This is a minor label in the workflow configuration that does not affect the analysis. The data you provided as input is the preprocessed collection, and the output correctly reflects your trimmed data. You do not need to take any action.

**Q: Do I need to keep my browser open while the workflow runs?**
A: No. Galaxy runs jobs on the server and will email you when the MultiQC report is ready.

**Q: My supervisor is not available and my data fails the go/no-go criteria — what should I do?**
A: Document exactly what you observed and what the failing criterion was. Do not proceed to Stage 5 until you have discussed this. In the meantime, re-examine your Stage 3 settings and Stage 2 QC to understand whether the issue originated at trimming or was present in the raw data. Record your observations carefully — this is exactly the kind of analytical problem-solving the Discussion section rewards when written up well.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
