# Stage 3: Pre-processing (Trimming)
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will trim and quality-filter your raw FASTQ files to remove adapter sequences and low-quality bases identified in Stage 2. By the end of this stage, you will have a collection of preprocessed FASTQ files ready for alignment, and a post-trimming QC report confirming that the preprocessing was effective.

This guide currently covers the **single-end (SE)** trimming workflow in full. A paired-end (PE) trimming workflow is in preparation — PE students should refer to the section for PE students at the end of this guide.

---

## Why Pre-processing Matters in This Project

Trimming is not always necessary, but the decisions you make here directly affect downstream alignment efficiency and the reliability of your differential expression results. Two types of issues are addressed in this stage:

**1. Adapter contamination.** During Illumina sequencing, if a DNA/cDNA insert is shorter than the read length, the sequencer reads into the adapter sequence ligated to the end of the fragment. These adapter sequences are not part of the Chinese hamster transcriptome — if they are not removed, they will fail to align to the reference genome, reducing the proportion of usable reads. In cases of heavy contamination, adapter sequences can also introduce artefactual signals into downstream analysis.

**2. Low-quality bases.** Base-call quality typically declines toward the 3' end of reads as the sequencing signal degrades. Retaining very low-quality bases (Q < 20, i.e., > 1% error probability) in reads that will then be aligned to the genome can cause mis-alignments. Trimming these bases improves the specificity of alignment.

The decisions about whether and how aggressively to trim should be driven by your Stage 2 QC results. Students who apply trimming without reference to their QC report, or who apply settings inconsistent with their data, will not be able to justify their Methods in the written submission.

---

## Before You Begin

Confirm the following before running this workflow:

- [ ] Stage 2 completed successfully — you have a MultiQC report for your raw data
- [ ] You have reviewed your Stage 2 QC report and noted: whether adapter sequences are present and at what level, and whether quality drops below Q20 at the 3' end of reads
- [ ] You have noted the adapter type flagged in Stage 2 (should be TruSeq3 — this matches the adapter file used in the workflow)
- [ ] You know your data type (SE or PE), confirmed by your supervisor

> If your Stage 2 QC report showed no adapter contamination and high quality throughout, trimming may have minimal impact on your data — but it is still good practice to run this step. Document the QC metrics that informed your decision in your Methods section.

---

## Understanding Trimmomatic

This workflow uses **Trimmomatic** (v0.39) to perform two trimming operations in sequence on each read. Understanding what these operations do is essential for your Methods section and for interpreting the before-and-after QC comparison.

### Operation 1: ILLUMINACLIP — Adapter Removal

```
ILLUMINACLIP:TruSeq3-SE.fa:2:30:10:8
```

| Parameter | Value | What it means |
|---|---|---|
| Adapter file | `TruSeq3-SE.fa` | The adapter sequences to search for — this file contains Illumina TruSeq3 single-end adapters, the standard adapter set for most Illumina short-read SE libraries |
| Seed mismatches | `2` | The maximum number of mismatches allowed in the initial short seed match to the adapter sequence |
| Simple clip threshold | `10` | The minimum alignment score required for a simple (non-palindromic) adapter match to trigger clipping. Higher values are more stringent. |
| Min adapter length | `8` | A match shorter than 8 bases is not clipped, even if it meets the score threshold — prevents over-aggressive clipping of sequences that happen to match the adapter by chance |

**What it does:** Trimmomatic scans each read from the 3' end for sequences that match the TruSeq3 adapter. When a match is found that meets the threshold criteria, the adapter and everything 3' of it is removed. The trimmed read is kept; any clipped portion is discarded.

> **Is TruSeq3-SE the right adapter for my data?** For most Illumina RNA-seq datasets using polyA selection or rRNA depletion, TruSeq3-SE is the correct adapter set. Your Stage 2 FastQC adapter content module will have flagged the adapter type — if it identified "Illumina Universal Adapter" or "TruSeq3", this setting is correct. If FastQC identified a different adapter type, discuss with your supervisor before proceeding.

### Operation 2: SLIDINGWINDOW — Quality Trimming

```
SLIDINGWINDOW:4:20
```

| Parameter | Value | What it means |
|---|---|---|
| Window size | `4` | The number of consecutive bases evaluated at each step |
| Required quality | `20` | The minimum mean Phred quality score required within the window |

**What it does:** Trimmomatic scans from the 5' end of each read using a sliding window of 4 bases. When it finds a window where the mean quality score falls below Q20, it trims the read at that position — everything from that point to the 3' end is discarded. The remaining 5' portion of the read is kept.

> **Why Q20?** A Phred score of Q20 corresponds to a 1% base-call error probability. This is a widely used threshold in RNA-seq preprocessing — stringent enough to remove unreliable bases while not discarding too much of the read.

> **Note — No minimum length filter:** The current workflow does not include a MINLEN operation. In principle, very short trimmed reads (e.g., < 20 bp) could pass through and cause issues at the alignment stage. In practice, for most good-quality CHO RNA-seq data, trimming will remove only a small number of bases from the 3' end of reads and very few reads will be trimmed to problematic lengths. If your Stage 2 QC showed severe quality degradation across a large portion of reads, discuss whether a MINLEN parameter should be added with your supervisor.

---

---

## Section A — Single-End (SE) Workflow

---

### A1: Confirm Your Input

The SE preprocessing workflow takes the **"Raw RNA-seq data"** collection from Stage 1 as its input — the flat list of fastq.gz files tagged `raw_data`.

Confirm this is present in your history before running:
- Expand the collection and verify the number of files matches your number of samples
- Confirm files are typed as `fastq.gz` and tagged `raw_data`

---

### A2: Import and Run the SE Preprocessing Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload the file **`Galaxy-Workflow-3_rnaseq_preprocessing_se.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the imported SE preprocessing workflow
2. Under **"raw_rnaseq_data"**, select your **"Raw RNA-seq data"** collection from Stage 1
3. Click **Run Workflow**

---

### A3: Monitor and Verify (SE)

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- Trimmomatic runs on each sample independently — jobs will complete at different times as they move through the queue
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset) below

**When complete, verify:**
- A collection called **"Preprocessed RNA-seq data"** appears in your history, tagged `preprocessed_data`
- Expand it — you should see one FASTQ file per sample
- Files should be typed as `fastq.gz`
- Click the info icon (i) on any Trimmomatic output and check the log — it will report how many reads were retained and how many were dropped

**What to look for in the Trimmomatic log:**

The Trimmomatic log (accessible via the info icon) reports the input and output read counts per sample. Key metrics:

| Metric | What to look for |
|---|---|
| Input reads | Should match your Stage 1 read count for that sample |
| Surviving reads | Reads retained after trimming — the proportion surviving is your **survival rate** |
| Dropped reads | Reads removed entirely by trimming |

**Survival rate:** For typical good-quality CHO RNA-seq data, you would expect >90% of reads to survive trimming. A survival rate below 80% suggests either heavy adapter contamination or widespread low-quality data — note this and discuss with your supervisor. Record the survival rate for each sample, as you will need it for your written assessment.

---

### A4: SE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input dataset collection | Accepts the "Raw RNA-seq data" list collection (fastq.gz, tagged `raw_data`) from Stage 1 |
| 2 | Trimmomatic (v0.39+galaxy2) | Applies ILLUMINACLIP (TruSeq3-SE adapters; seed mismatches=2; simple clip threshold=10; min adapter length=8) followed by SLIDINGWINDOW (window=4, min quality=20) to each read. Output collection renamed "Preprocessed RNA-seq data", tagged `preprocessed_data`. Log file is hidden. |

---

---

## Section B — Paired-End (PE) Students

**A PE trimming workflow is currently in preparation and will be provided by your supervisor when available.**

In the meantime, this stage folder contains the file **`Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_pe.ga`**. This is the **post-trimming QC workflow** for PE data (FastQC + MultiQC on preprocessed reads) — it does not perform trimming itself. Do not run it until you have preprocessed PE FASTQ files to assess.

Contact your supervisor for the current status of the PE preprocessing workflow and the trimming parameters to be applied. When the PE preprocessing guide is issued, it will be added to this folder.

---

---

## Post-Trimming QC: Confirming the Preprocessing Worked

After trimming, you must run a second round of QC to confirm that:
- Adapter sequences have been removed (the Adapter Content module should now be clean)
- Read quality has improved or is maintained across the surviving reads
- The surviving read counts are consistent with the expected survival rate

**For SE students:** Run the post-trimming QC using the Stage 4 SE QC workflow on your **"Preprocessed RNA-seq data"** collection. See the Stage 4 student guide for details.

**For PE students:** The PE post-trimming QC workflow is **`Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_pe.ga`**, which is provided in this folder. When your PE preprocessed data is available, run it as follows:

1. Go to **Workflow** → **Import** and upload **`Galaxy-Workflow-4_preprocessed_rnaseq_data_qc_pe.ga`**
2. Run the workflow, selecting your preprocessed paired collection as the input under **"processed_fastq_files"**
3. The output collection will be named **"Preprocessed Data QC Analysis"**, tagged `qc,preprocessed,results`, containing an HTML MultiQC report and a tabular summary

Refer to the Stage 2 Student Guide for guidance on interpreting the FastQC and MultiQC outputs — the same interpretation principles apply. The key difference is what you are looking for: at this stage, you are verifying that trimming has resolved the issues identified in Stage 2, not performing an initial assessment.

**What to compare between Stage 2 and Stage 3 QC:**

| Module | What should have changed after trimming |
|---|---|
| Per Base Sequence Quality | 3' quality drop should be reduced or eliminated |
| Adapter Content | Should now be at or near zero across all positions |
| Sequence Length Distribution | Reads will no longer all be the same length — a distribution of lengths is now expected (shorter reads were trimmed) |
| Total reads | Will be slightly lower — reads dropped by Trimmomatic will no longer appear |
| Per Base Sequence Content | 5' hexamer priming bias will remain — this is biological, not adapter-related, and trimming does not remove it |

---

## Diagnosing a Red Dataset

A red dataset means the job failed. Follow the same diagnostic approach described in the Stage 1 Student Guide. In brief:

1. **Click the red dataset** to expand it
2. **Click the info icon (i)** and read **Tool Standard Error**
3. **Check the exit code** in Job Information: `1` = tool error; `137` = killed by server (memory/time)
4. **Identify the root failure** — the first dataset to go red is the cause; downstream failures are consequences

**Common Stage 3 errors:**

| Error or symptom | Cause | Fix |
|---|---|---|
| Trimmomatic fails with format error | Input file is not recognised as FASTQ | Confirm the Stage 1 collection is typed as `fastq.gz`; click pencil icon on a file to verify and correct if needed |
| Trimmomatic fails with adapter file error | The TruSeq3-SE adapter file is not available on this Galaxy instance | This should not occur on usegalaxy.eu — if it does, contact your supervisor; do not attempt to change the adapter file without guidance |
| Very low survival rate (< 50%) for some samples | Severe adapter contamination or widespread low quality | Review the Stage 2 QC report for that sample; discuss with supervisor before proceeding to alignment |
| All samples fail, none survive | Input collection was not tagged `raw_data` or was the wrong collection type | Confirm you selected the correct Stage 1 output collection as input |
| Post-trimming QC (PE): Flatten step fails | Input collection is not a paired collection | Confirm you selected a paired collection (preprocessed PE output), not a flat list |

For errors not listed here, copy the full error text from Tool Standard Error and search the Galaxy Help forum (help.galaxyproject.org).

---

## Recording Your Preprocessing Results for the Written Assessment

This stage is one of the most commonly underreported in student Methods sections. Trimming decisions and their outcomes must be explicitly documented — examiners expect to see the specific parameters used and the result of applying them.

---

### What to Record

For each sample, record:

| Metric | Where to find it |
|---|---|
| Input read count | Trimmomatic log (info icon on each output file) — should match Stage 1 |
| Surviving read count | Trimmomatic log |
| Survival rate (%) | Calculate: (surviving / input) × 100 |
| Adapter content before trimming | From your Stage 2 MultiQC report |
| Adapter content after trimming | From your post-trimming MultiQC report (should be ~0%) |
| Quality profile before/after | Stage 2 vs. Stage 3 QC — per base quality plot comparison |

---

### How This Feeds into Your Written Submission

#### Methods Section (rubric A2 — 10 marks)

Your Methods section must describe the trimming step with sufficient specificity to be reproducible. Include:

- The tool and version (Trimmomatic v0.39)
- The adapter file used (TruSeq3-SE for SE data)
- The exact trimming operations and parameters (ILLUMINACLIP and SLIDINGWINDOW settings)
- The platform (Galaxy, usegalaxy.eu)
- The outcome: proportion of reads retained after trimming

**Example Methods language:**

> "Raw FASTQ files were preprocessed using Trimmomatic (v0.39, Galaxy platform, usegalaxy.eu). Adapter sequences were removed using ILLUMINACLIP with the TruSeq3 single-end adapter set (seed mismatches = 2, simple clip threshold = 10, minimum adapter length = 8 bp). Low-quality bases were removed using a sliding window approach (SLIDINGWINDOW:4:20), trimming reads when the mean quality within a 4-base window fell below Q20. A mean of [X]% of reads were retained across all samples following preprocessing."

A strong Methods section (A-grade) names the tool and version, states every parameter by name and value, names the adapter file, and reports the survival rate. Stating only "reads were trimmed using Trimmomatic" will not meet the standard for even a C-grade Methods section.

---

#### Results Section (rubric A3 — 15 marks)

Your Results section should briefly report the outcome of trimming as part of the data processing narrative, before presenting your main findings. This does not require a dedicated figure — a short paragraph with quantitative results is sufficient.

**Example Results language:**

> "Following quality assessment of raw sequencing data (Stage 2), adapter sequences and low-quality 3' bases were removed using Trimmomatic. A mean of [X]% of reads were retained across the [n] samples (range: [X]–[X]%), with adapter content reduced from a mean of [X]% to less than 0.1% following trimming (Figure X / Supplementary Figure S[X]). Post-trimming quality assessment confirmed improved per-base quality scores across all samples."

Include a figure comparing the adapter content or per-base quality before and after trimming only if there is a meaningful difference to show. If your data was already clean and trimming had minimal effect, state this — it is a valid result.

> **Supplementary vs. main text:** The full per-sample survival rate table belongs in supplementary material (e.g., Supplementary Table S2). Cite it explicitly: "Survival rates for individual samples are provided in Supplementary Table S2." A summary sentence with mean and range is sufficient for the main text.

---

#### Discussion Section (rubric A4 — 15 marks)

Your Discussion should briefly contextualise your preprocessing decisions:

- Did the original authors apply trimming? If so, what tool and parameters did they use, and how do your settings compare?
- If they did not describe trimming explicitly (common in older papers), comment on whether the data quality you observed in Stage 2 was consistent with their downstream results
- If any samples had notably low survival rates, acknowledge this as a potential limitation

One to three sentences is appropriate — this is a minor point in the context of the full Discussion, which should focus on your biological findings.

---

### Summary: Stage 3 Contributions to Your Submission

| Submission section | What Stage 3 provides | Rubric component |
|---|---|---|
| Methods | Tool (Trimmomatic v0.39), adapter file, ILLUMINACLIP parameters, SLIDINGWINDOW parameters, mean survival rate | A2 (10 marks) |
| Results | Mean and range of survival rates; confirmation of adapter removal (before/after comparison) | A3 (15 marks) |
| Discussion | Comparison with original authors' preprocessing approach; note on any samples with low survival | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 4, you should be able to answer:

1. What does the ILLUMINACLIP step do? What adapter file was used, and why is that the correct choice for your data?
2. What does SLIDINGWINDOW:4:20 mean? Where does trimming occur within a read?
3. Why is Q20 used as the quality threshold? What is the error probability associated with a Phred score of 20?
4. Why are there no reads dropped for minimum length (MINLEN) in this workflow? In what circumstances might you need to add this?
5. What survival rate did you observe across your samples? Is this consistent with what you would expect given your Stage 2 QC results?
6. How has the adapter content changed between Stage 2 and Stage 3 post-trimming QC? How has the per-base quality profile changed?
7. Why is the sequence length distribution no longer a single peak after trimming?
8. How will you report the specific trimming parameters in your Methods section?

---

## Before You Move On: Checklist

- [ ] My "Preprocessed RNA-seq data" collection is present in my Galaxy history and contains the correct number of files
- [ ] I have reviewed the Trimmomatic log for each sample and recorded input reads, surviving reads, and survival rate
- [ ] I have run the post-trimming QC workflow and confirmed that adapter content has been removed and quality has improved or been maintained
- [ ] I have saved or noted the before-and-after QC comparison for use in my written submission
- [ ] If any samples had survival rates below 90%, I have discussed these with my supervisor
- [ ] I am ready to proceed to Stage 4 (Preprocessed Data QC) with my "Preprocessed RNA-seq data" collection

---

## Frequently Asked Questions

**Q: My Stage 2 QC showed no adapter contamination — do I still need to run this stage?**
A: Yes. Running a consistent pipeline across all samples is good practice regardless of apparent adapter levels, as very low levels may not be visible in the QC plots but can still be present. Additionally, running Trimmomatic and reporting a high survival rate with minimal adapter content is itself a valid and reportable result. Document what you found and what you did.

**Q: The adapter type flagged in FastQC was not TruSeq3 — should I change the adapter file?**
A: Consult your supervisor before changing any adapter settings. Most Illumina RNA-seq datasets deposited in SRA use TruSeq adapters; however, some older datasets or those from specific facilities may use different adapters (e.g., Nextera). Using the wrong adapter file will result in poor adapter removal. Do not change the workflow without guidance.

**Q: My survival rate is below 80% for some samples — is this a problem?**
A: A survival rate below 80% is unusual for typical good-quality published RNA-seq data and warrants investigation. Check your Stage 2 QC for that sample — was there a quality issue visible at the time? Also check that the correct adapter file is being used. If the data genuinely has heavy adapter contamination or low quality, discuss with your supervisor before proceeding to alignment.

**Q: Trimmomatic dropped some of my reads entirely — where did they go?**
A: Reads that are entirely adapter sequence, or that are trimmed to zero length by the quality filter, are dropped (not written to the output file). The Trimmomatic log reports the number dropped. A small number of dropped reads (< 1%) is normal. Unusually high numbers of dropped reads should be investigated.

**Q: Can I change the SLIDINGWINDOW threshold to be more or less stringent?**
A: The current workflow uses SLIDINGWINDOW:4:20, which is a commonly used setting for RNA-seq. Changing parameters without justification is not appropriate for this project. If your Stage 2 QC suggests a different threshold is warranted, discuss with your supervisor first — any deviation from the default parameters must be explicitly justified in your Methods section.

**Q: I am a PE student and the PE trimming workflow is not available yet — what should I do?**
A: Contact your supervisor. Do not attempt to run the SE trimming workflow on PE data. Your supervisor will advise on when the PE workflow will be available or whether an alternative approach should be taken.

**Q: Do I need to keep my browser open while Trimmomatic runs?**
A: No. Galaxy runs jobs on the server. Close your browser and return later — your history will be exactly as you left it.

**Q: My supervisor is not available and I am stuck — what do I do?**
A: Re-read this guide. For Galaxy platform issues, search the Galaxy Help forum (help.galaxyproject.org). For questions about interpreting trimming results, the Trimmomatic documentation (available from the Usadel Lab at RWTH Aachen) has detailed explanations of all parameters. Bring any unresolved issues to your next supervision session.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
