# Stage 5: Alignment to the Reference Genome
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will align your preprocessed RNA-seq reads to the *Cricetulus griseus* (Chinese hamster) reference genome using STAR. By the end of this stage, you will have a collection of sorted BAM files — one per sample — and a MultiQC alignment summary report showing the mapping rates for each sample. This output is the direct input to quantification in Stage 6.

This guide covers both **single-end (SE)** and **paired-end (PE)** datasets. The SE and PE workflows differ in their STAR configuration and are not interchangeable.

---

## Why Alignment Matters

Alignment is the step that transforms your FASTQ reads — short sequences of unknown genomic origin — into positional information relative to a known reference genome. Every read is asked: where in the Chinese hamster genome does this sequence best fit?

The answer determines everything that follows. Reads that align to exonic regions of annotated genes are counted in Stage 6 to produce the gene expression matrix that drives your differential expression analysis. Reads that fail to align — because they are too short, too divergent from the reference, or genuinely from another source — do not contribute to expression estimates.

In the context of CHO cell biopharmaceutical research, alignment quality has direct implications for the reliability of your results. CHO cells used in manufacturing are often genomically unstable — they carry copy number variations, rearrangements, and gene amplifications not fully captured in any reference genome. The reference used here (*Cricetulus griseus*, CriGri-9.1 or current Ensembl release) is an approximation of the genome of your cells, not an exact match. Understanding this limitation, and reporting the alignment rate you achieved, is part of scientific rigour.

---

## Understanding the Workflow Inputs

This workflow has **three inputs**, which is more than any previous stage. Two of them — the genome sequence and the gene annotation — are new, and require specific files that your supervisor will provide.

### Input 1 and 2: Reference Genome FTP Links (text files)

Unlike earlier stages where you provided your own data, the reference genome is downloaded automatically by the workflow from Ensembl's FTP server. You do not upload the genome itself — you upload two small **plain text files**, each containing a single FTP URL, and the workflow handles the download.

**What you need:**

| File | Content | File extension |
|---|---|---|
| Genome sequence link | A single line containing the Ensembl FTP URL to the Chinese hamster genome FASTA file | `.txt` |
| GTF annotation link | A single line containing the Ensembl FTP URL to the Chinese hamster GTF annotation file | `.txt` |

**Your supervisor will provide you with these two text files** (or the URLs to put in them). Do not attempt to find or construct these URLs yourself — using the wrong genome assembly or annotation version will produce incorrect results that are difficult to diagnose.

> **What is the GTF file?** The GTF (Gene Transfer Format) file contains the gene annotation: the coordinates of every known gene, transcript, and exon in the Chinese hamster genome. STAR uses this file to build a splice junction database, which allows it to align reads that span exon–exon boundaries — a defining feature of RNA-seq data that distinguishes it from DNA sequencing.

> **Which genome assembly?** All projects in this cohort use the *Cricetulus griseus* genome from Ensembl. Your supervisor will specify the exact assembly and release version. This must be consistent across all students for results to be comparable. Record the assembly name and Ensembl release number — you will need it for your Methods section.

### Input 3: Preprocessed RNA-seq Data

Your **"Preprocessed RNA-seq data"** collection from Stage 3, tagged `preprocessed_data`.

- **SE students:** flat list collection of trimmed fastq.gz files
- **PE students:** paired collection of trimmed fastq.gz files

---

## Before You Begin

- [ ] Stage 4 completed — all samples passed the go/no-go QC criteria
- [ ] You have received two text files from your supervisor containing the Ensembl FTP URLs for the CGR genome FASTA and GTF
- [ ] You have uploaded both text files to Galaxy and verified each contains a single FTP URL on one line
- [ ] You have noted the Ensembl assembly name and release version from your supervisor
- [ ] You know your data type (SE or PE) and the read length of your post-trimming data (recorded in Stage 4)

> **Before uploading the FTP link text files:** Open each file in a plain text editor and confirm it contains a single URL on one line with no extra spaces, blank lines, or formatting. Galaxy's lftp tool will fail if the URL has any whitespace issues.

---

## Understanding STAR

STAR (Spliced Transcripts Alignment to a Reference) is a splice-aware RNA-seq aligner. "Splice-aware" means it can align reads that span an intron — a read that begins in one exon and ends in another, bridging a splicing event. This is essential for eukaryotic RNA-seq because the mature mRNA your reads come from has had its introns removed; naively aligning those reads to the genomic sequence would fail unless the aligner knows to look for split alignments.

**Why STAR for CHO RNA-seq?**
STAR is widely used for mammalian transcriptomics because it is fast, accurate, and handles the complexity of the mammalian genome well. For CHO cell RNA-seq in particular, STAR performs well on the *Cricetulus griseus* reference despite the challenges of CHO genome instability.

**What STAR produces:**
- A **BAM file** per sample — the aligned reads in binary format, sorted by position. This is the primary output and the input to Stage 6 quantification.
- A **log file** per sample — detailed alignment statistics used to assess mapping quality.
- A **splice junctions file** per sample — coordinates of all splice junctions detected in the data (hidden in this workflow, used internally).

---

---

## Section A — Single-End (SE) Workflow

---

### A1: Confirm Your Inputs

Before running, verify in your Galaxy history:

1. **Genome FTP link text file** — single line containing the Ensembl FTP URL for the CGR FASTA. Upload as file type `txt`.
2. **GTF FTP link text file** — single line containing the Ensembl FTP URL for the CGR GTF. Upload as file type `txt`.
3. **"Preprocessed RNA-seq data"** collection — flat list, tagged `preprocessed_data`, from Stage 3.

---

### A2: Import and Run the SE Alignment Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-5_star_read_mapping_se.ga`** provided by your supervisor
4. The workflow will appear in your workflow list

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the SE alignment workflow
2. Match inputs to the three fields:
   - **"Enter Genome Sequence FTP link"** → your genome FASTA URL text file
   - **"Enter CGR GTF FTP txt"** → your GTF URL text file
   - **"Preprocessed RNA-seq Data"** → your "Preprocessed RNA-seq data" collection
3. Click **Run Workflow**

---

### A3: Monitor and Verify (SE)

**This workflow will take significantly longer than previous stages.** Expect:

- **Genome download:** 20–60 minutes depending on NCBI/Ensembl server speed and the file size (the CGR genome FASTA is several hundred MB)
- **GTF download:** 5–15 minutes
- **STAR index building + alignment:** 1–3 hours per sample. STAR builds the genome index from the FASTA and GTF before aligning — this index building step runs once per invocation and takes considerable time for the ~2.5 Gb Chinese hamster genome. Alignment then runs for each sample.
- **Total expected time:** Several hours to over a day for a full cohort dataset

You do not need to keep your browser open. Return periodically to check progress. Do not cancel and re-submit jobs that have been running for several hours — they are likely still working.

**Monitor progress:**
- Datasets cycle through: **Grey** (queued) → **Yellow** (running) → **Green** (complete) → **Red** (error)
- The genome and GTF downloads run in parallel first; STAR waits for both before starting
- STAR jobs per sample will complete at different times
- MultiQC runs after all STAR jobs are complete

**When complete, verify:**
- A collection of **"Mapped RNA-seq data"** BAM files appears in your history, tagged `mapped_data` — one BAM per sample
- A build list collection tagged `mapping,results` appears containing **HTML** (MultiQC mapping report) and **Table** (mapping statistics)
- Open the MultiQC HTML report and confirm all samples are listed

---

### A4: SE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input: Genome FTP link | Accepts the text file containing the Ensembl FTP URL for the CGR FASTA |
| 2 | Input: GTF FTP link | Accepts the text file containing the Ensembl FTP URL for the CGR GTF |
| 3 | Input: Preprocessed RNA-seq Data | Accepts the preprocessed fastq.gz collection (tagged `preprocessed_data`) |
| 4 | lftp (v4.9.2) | Downloads the genome FASTA from Ensembl FTP (hidden) |
| 5 | lftp (v4.9.2) | Downloads the GTF from Ensembl FTP (hidden) |
| 6 | Collapse Collection (v5.1.0) | Assembles downloaded genome chunks into `genome.fasta` (fasta.gz, tagged `reference_genome`) |
| 7 | Collapse Collection (v5.1.0) | Assembles downloaded GTF chunks into `genes.gtf.gz` (gtf.gz, tagged `gene_model`) |
| 8 | RNA STAR (v2.7.11b+galaxy0) | Builds genome index from FASTA + GTF; aligns SE reads. Key parameters below. Output: "Mapped RNA-seq data" (BAM, tagged `mapped_data`); log hidden; splice junctions hidden |
| 9 | MultiQC (v1.33+galaxy0) | Aggregates STAR log files into "Mapping QC HTML" and "Mapping QC Tabular", tagged `mapping_qc` |
| 10 | Build list | Packages MultiQC HTML and Table into final results collection, tagged `mapping,results` |

**Key STAR parameters (SE):**

| Parameter | Value | What it means |
|---|---|---|
| `genomeSAindexNbases` | `14` | Suffix array index parameter — value of 14 is appropriate for the Chinese hamster genome (~2.5 Gb). Changing this would require genome rebuilding. |
| `sjdbOverhang` | `75` | Splice junction overhang: the length of the genomic sequence around a splice junction used for index building. Optimally set to *read length − 1*. A value of 75 is optimised for 76 bp reads. If your post-trimming median read length differs from 76 bp, note this — it may slightly reduce sensitivity for splice junction detection but will not prevent alignment. |
| `sjdbGTFfeatureExon` | `exon` | STAR uses exon features from the GTF to define splice junctions |
| `basic_filters` | `exclude_unmapped`, `RemoveInconsistentStrands` | Unmapped reads are excluded from the output BAM; reads with inconsistent strand assignments at splice junctions are filtered |
| `outSAMmapqUnique` | `60` | MAPQ score assigned to uniquely mapping reads (standard value for compatibility with downstream tools) |
| `outSAMprimaryFlag` | `OneBestScore` | Only the single best alignment is reported as the primary alignment per read |
| `twopassMode` | `None` | Single-pass alignment. Two-pass mode improves novel splice junction detection but is substantially more compute-intensive — appropriate for standard expression analysis |

---

---

## Section B — Paired-End (PE) Workflow

---

### B1: Confirm Your Inputs

Before running, verify in your Galaxy history:

1. **Genome FTP link text file** — single line containing the Ensembl FTP URL for the CGR FASTA. Upload as file type `txt`.
2. **GTF FTP link text file** — single line containing the Ensembl FTP URL for the CGR GTF. Upload as file type `txt`.
3. Your **preprocessed paired collection** from Stage 3.

---

### B2: Import and Run the PE Alignment Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-5_star_read_mapping_pe.ga`** provided by your supervisor

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the PE alignment workflow
2. Match inputs to the three fields:
   - **"Enter Genome Sequence FTP link"** → your genome FASTA URL text file
   - **"Enter CGR GTF FTP txt"** → your GTF URL text file
   - The unlabelled paired collection input → your preprocessed paired collection from Stage 3
3. Click **Run Workflow**

---

### B3: Monitor and Verify (PE)

Timing expectations are the same as SE — several hours to a day. PE alignment jobs may take longer than SE per sample because STAR processes two files per sample.

**When complete, verify:**
- A collection of **"Mapped Data"** BAM files appears in your history, tagged `mapped`
- A collection called **"STAR Mapping Analysis"** appears containing **HTML** and **Table**
- Open the HTML and confirm both samples and mapping statistics are present

---

### B4: PE Workflow — Technical Summary

The PE workflow is structurally identical to SE. Key differences:

| Parameter | SE value | PE value | Note |
|---|---|---|---|
| `sPaired` | `single` | `paired_collection` | Tells STAR to expect paired-end reads |
| `sjdbOverhang` | `75` | `100` | PE workflow optimised for 101 bp reads (read length − 1) |
| `basic_filters` | `exclude_unmapped`, `RemoveInconsistentStrands` | *(none configured)* | PE output BAM will contain unmapped reads; SE output does not |
| Output BAM name | "Mapped RNA-seq data" | "Mapped Data" | Different names — confirm which you have before running Stage 6 |

---

---

## Interpreting Your Alignment Statistics

Open your MultiQC alignment report. This section explains what the STAR statistics mean for CHO RNA-seq data.

The most important section in the MultiQC STAR report is the **alignment scores summary**. For each sample, STAR reports where reads went after alignment:

---

### % Uniquely Mapped Reads

**This is the single most important alignment metric.** It is the percentage of reads that aligned to exactly one location in the Chinese hamster genome.

| Value | Interpretation |
|---|---|
| > 75% | Good — typical for high-quality CHO RNA-seq aligned to the correct genome |
| 65–75% | Acceptable — may reflect genome incompleteness or sample-specific variation |
| 50–65% | Below expectation — investigate; may indicate wrong genome build, poor quality data, or biological contamination |
| < 50% | Poor — do not proceed to Stage 6 without discussing with your supervisor |

> **Why is the uniquely mapped rate not 100%?** Several factors reduce it: (1) reads from repetitive regions that map equally well to multiple locations (multimappers); (2) reads from intergenic regions not well-represented in the genome; (3) reads from transposable elements; (4) very short trimmed reads that are too short to map reliably; (5) gaps and errors in the reference genome assembly itself — the CriGri reference is not complete, and CHO cell genomes are highly rearranged relative to the reference.

---

### % Mapped to Multiple Loci

Reads that align equally well to more than one location. These are multimappers, typically from repetitive sequences or gene families with high sequence similarity (e.g., olfactory receptor genes, zinc finger proteins).

- **Typical range for CHO RNA-seq:** 5–15%
- Multimapped reads are not included in the uniquely mapped count and are handled differently by downstream quantification tools. Note this percentage — it is relevant to your Methods.

---

### % Unmapped: Too Short

Reads that were discarded because they were too short to satisfy STAR's minimum alignment length requirements after trimming.

- **Expected:** < 5% for well-trimmed data
- A high value (> 10%) suggests that Stage 3 trimming was aggressive and removed too much sequence — or that the source data had severe quality issues
- If elevated, check your Stage 3 survival rates and Stage 4 read length distribution

---

### % Unmapped: Too Many Mismatches

Reads that aligned but with too many mismatches to the reference to be accepted.

- **Expected:** < 2% for good data aligned to the correct genome
- A high value may indicate: the wrong reference genome was used; very low quality reads that survived trimming; or genuine sequence divergence between the CHO cell line and the CriGri reference

---

### % Unmapped: Other

Reads that failed to align for other reasons. Typically < 1%. Values above this are unusual and should be investigated.

---

### Comparing Mapping Rates Across Samples

Mapping rates should be broadly consistent across all samples in your dataset. Large differences between samples in uniquely mapped rate (> 10 percentage points) may indicate:

- A download or trimming problem with a specific sample (check Stage 1 and Stage 3 logs)
- A genuine sample quality difference that was not apparent in earlier QC stages
- A sample swap or mislabelling in the original SRA record

Flag any outlier samples before proceeding to Stage 6 and discuss with your supervisor.

---

## Understanding Your BAM Output

Each sample produces a **BAM file** (Binary Alignment Map) — the standard format for storing aligned reads. You do not need to open or inspect BAM files directly; they are used as input to Stage 6. However, understanding what they contain is important for your Methods and Discussion.

| BAM file property | Value | What it means |
|---|---|---|
| Format | Binary (compressed SAM) | Not human-readable; requires tools like samtools to inspect |
| Sort order | Position-sorted | Reads are ordered by their position in the genome — required for quantification |
| MAPQ score for unique reads | 60 | A MAPQ of 60 indicates unique alignment; Stage 6 quantification tools use MAPQ thresholds |
| Unmapped reads (SE) | Excluded | The SE workflow filters unmapped reads from the BAM |
| Unmapped reads (PE) | Included | The PE workflow retains unmapped reads in the BAM |
| Tags included | NH, HI, AS, nM, ch | Standard SAM tags — NH (number of loci), HI (alignment index), AS (alignment score), nM (mismatches), ch (chimeric alignment flag) |

---

## Diagnosing a Red Dataset

Follow the same diagnostic approach described in the Stage 1 Student Guide — click the red dataset, read Tool Standard Error, check the exit code.

**Common Stage 5 errors:**

| Error or symptom | Cause | Fix |
|---|---|---|
| lftp fails with connection error | Ensembl FTP server is temporarily unavailable, or the URL is malformed | Check your FTP link text file — confirm it contains a valid URL with no extra spaces or newlines. Wait and re-run if the server is temporarily unavailable. |
| lftp fails with "file not found" | The URL points to a file that does not exist at that path | Check with your supervisor that the URL is correct and the Ensembl release specified still hosts the file |
| STAR fails with "genome index" error | The genome FASTA could not be indexed — may be a format issue with the collapsed genome file | Check the `genome.fasta` dataset in your history — confirm it is typed as `fasta.gz` and is non-empty |
| STAR fails with "GTF parse" error | The GTF file has a format problem | Confirm `genes.gtf.gz` is typed as `gtf.gz` and is non-empty; re-run the lftp and Collapse steps if corrupted |
| STAR fails with exit code 137 | Job was killed by the server (out of memory) | STAR is memory-intensive. On usegalaxy.eu the allocation is generally sufficient for the CHO genome, but if this occurs during index building, try re-running at off-peak times (early morning, weekends). Do not attempt to reduce genome index parameters without supervisor guidance. |
| Very low unique mapping rate (< 50%) for all samples | Wrong genome or annotation file | Confirm with your supervisor that the FTP links point to *Cricetulus griseus* (CriGri) and not a different organism |
| Very low unique mapping rate for one sample only | Sample-specific issue | Check Stage 1 download, Stage 3 survival rate, and Stage 4 QC for that specific sample |
| Jobs grey for > 4 hours | usegalaxy.eu queue is very busy | This workflow is computationally demanding. Long queue times are normal, especially during peak usage. Do not cancel. |

---

## Recording Your Alignment Results for the Written Assessment

Alignment is where your pipeline transitions from data preparation to scientific analysis. The alignment statistics you record here directly support the claims you make about data quality and analytical validity in your written submission.

---

### What to Record

From your MultiQC mapping report, record for each sample:

| Metric | Where to find it |
|---|---|
| % Uniquely mapped reads | MultiQC STAR summary table |
| % Multimapped reads | MultiQC STAR summary table |
| % Unmapped: too short | MultiQC STAR summary table |
| Total reads input to STAR | MultiQC STAR summary table |
| Reference genome assembly and Ensembl release | From your supervisor / FTP URL |

---

### How This Feeds into Your Written Submission

#### Methods Section (rubric A2 — 10 marks)

Your Methods section must describe the alignment step with enough specificity to be reproducible. Include:

- The alignment tool and version (STAR v2.7.11b)
- The reference genome: organism (*Cricetulus griseus*), assembly name, Ensembl release version
- The annotation used: Ensembl GTF, same release as genome
- Read mode (SE or PE)
- The `sjdbOverhang` parameter and the read length it was calibrated to
- The platform (Galaxy, usegalaxy.eu)
- The mean uniquely mapped rate across samples

**Example Methods language:**

> "Preprocessed reads were aligned to the *Cricetulus griseus* reference genome (assembly [CriGri-X.X], Ensembl release [XXX]) using STAR (v2.7.11b, Galaxy platform, usegalaxy.eu) with the corresponding Ensembl GTF annotation. Alignment was performed in [single-end / paired-end] mode with a splice junction overhang of [75/100] bp. A mean of [X]% of reads mapped uniquely across all samples (range: [X]–[X]%)."

A strong Methods section (A-grade) names the tool with version, specifies the exact genome assembly and Ensembl release, states the read mode and overhang parameter, and reports the mean uniquely mapped rate. A weak Methods section states only "reads were aligned using STAR to the Chinese hamster genome."

---

#### Results Section (rubric A3 — 15 marks)

Alignment statistics should be reported as part of the data processing narrative, quantitatively and clearly.

**Recommended approach:** Include the MultiQC STAR alignment summary as a figure (or Supplementary Table), and summarise in one paragraph:

> "Alignment of preprocessed reads to the *Cricetulus griseus* reference genome using STAR yielded a mean unique mapping rate of [X]% across all [n] samples (range: [X]–[X]%), with [X]% of reads mapping to multiple loci and [X]% failing to map due to read length constraints (Figure X / Supplementary Table S[X]). Mapping rates were consistent across treatment and control samples, with no individual sample deviating by more than [X] percentage points from the group mean."

If any sample showed a notably different mapping rate, acknowledge it explicitly.

> **Supplementary vs. main text:** A full per-sample alignment statistics table belongs in supplementary material. A single-panel figure showing the stacked alignment rate bars from MultiQC can be a useful and compact main-text figure — but weigh this against your other figure needs (you have only 3 total). A quantitative sentence citing a supplementary table is often more efficient.

---

#### Discussion Section (rubric A4 — 15 marks)

Your Discussion should contextualise the alignment rate relative to the original publication and known properties of the CHO genome:

- Did the original authors report a mapping rate? If so, is yours consistent?
- If your uniquely mapped rate is lower than expected (< 75%), discuss possible reasons in the context of CHO genome biology — the reference genome is incomplete, CHO cells are aneuploid, and rearrangements are common
- If any samples had notably lower alignment rates, discuss whether this affects confidence in their expression estimates

Two to four sentences is appropriate — this contextualises your results without overemphasising a technical step relative to the biological findings of your analysis.

---

### Summary: Stage 5 Contributions to Your Submission

| Submission section | What Stage 5 provides | Rubric component |
|---|---|---|
| Methods | STAR v2.7.11b, genome assembly + Ensembl release, GTF annotation, read mode, sjdbOverhang, mean uniquely mapped rate | A2 (10 marks) |
| Results | Per-sample alignment statistics table; mean unique mapping rate; consistency across samples | A3 (15 marks) |
| Discussion | Contextualisation of mapping rate relative to original paper and CHO genome biology | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 6, you should be able to answer:

1. What does it mean for a read to be "uniquely mapped"? How does this differ from a multimapper?
2. Why is STAR described as "splice-aware"? Why is this property essential for RNA-seq alignment and not necessary for DNA-seq alignment?
3. What is the GTF file used for in STAR? What would happen if you aligned without a gene annotation?
4. What does `sjdbOverhang` control, and why is it set to 75 for SE and 100 for PE in these workflows?
5. What is a BAM file? What does "position-sorted" mean, and why does it matter for Stage 6?
6. What unique mapping rate did you achieve across your samples? Is this within the expected range for CHO RNA-seq?
7. Why is the mapping rate for CHO RNA-seq typically lower than for human or mouse RNA-seq aligned to their respective reference genomes?
8. How will you report the reference genome assembly and STAR parameters in your Methods section?

---

## Before You Move On: Checklist

- [ ] All STAR alignment jobs completed successfully (green)
- [ ] My BAM collection ("Mapped RNA-seq data" or "Mapped Data") is present with the correct number of files
- [ ] I have opened the MultiQC mapping report and recorded per-sample alignment statistics
- [ ] Mean unique mapping rate is ≥ 65% across all samples
- [ ] No individual sample deviates by more than 15 percentage points from the group mean
- [ ] I have noted the reference genome assembly name and Ensembl release version for my Methods section
- [ ] Any samples with outlier mapping rates have been discussed with my supervisor
- [ ] I am ready to proceed to Stage 6 with my BAM collection

---

## Frequently Asked Questions

**Q: The workflow is taking many hours — is something wrong?**
A: STAR is computationally intensive, particularly the genome index building step, which must complete before any alignment can begin. For the Chinese hamster genome (~2.5 Gb) with the Ensembl annotation, index building alone can take 1–2 hours. Total workflow time of 4–12 hours for a typical dataset (6–12 samples) is normal on a shared public server. Do not cancel jobs that have been running for less than 12 hours unless you see an explicit error.

**Q: My unique mapping rate is 60% — should I be worried?**
A: A rate of 60% is below the typical expectation (>75%) but not catastrophic. Before concluding there is a problem, check: (1) is this consistent across all samples, or is one sample pulling the average down? (2) does the original paper report a mapping rate, and how does yours compare? (3) are the % too-short unmapped reads elevated, suggesting aggressive trimming? Discuss with your supervisor. The CHO genome is genuinely incomplete and highly rearranged, and mapping rates of 60–70% are not unusual for some cell lines.

**Q: What is the difference between the BAM output and the STAR log output?**
A: The BAM file contains the actual aligned reads — it is what Stage 6 uses to count reads per gene. The log file contains alignment statistics (the numbers you see in MultiQC) but does not contain the reads themselves. Both are produced by every STAR run; in this workflow, the log is used by MultiQC and then hidden, while the BAM is kept as the primary output.

**Q: I have a PE dataset but the sjdbOverhang in the PE workflow is 100 — my reads are 75 bp after trimming. Does this matter?**
A: Ideally, sjdbOverhang should be read_length − 1. For PE reads trimmed to 75 bp, the optimal value would be 74. Using 100 (the workflow default) with 75 bp reads is suboptimal but will not prevent alignment — it will slightly reduce sensitivity for detecting novel splice junctions at the very ends of reads. For standard differential expression analysis this effect is minor. Note it in your Methods and discuss with your supervisor if you want to adjust.

**Q: The lftp download failed — do I need to re-run the whole workflow?**
A: No. Identify which download step failed (genome or GTF) by checking which dataset turned red first. Fix the underlying issue (check the URL in your text file), then re-run just the failed lftp step using the re-run icon (circular arrow). Galaxy will use cached outputs from successful steps.

**Q: My supervisor is not available and I don't have the FTP link text files — what should I do?**
A: Do not proceed without the correct reference files. Using the wrong genome will produce results that look correct but are meaningless — this is one of the most common and damaging errors in bioinformatics analysis. Wait for your supervisor. Use the time to review the STAR documentation or read further into the Methods sections of published CHO RNA-seq papers.

**Q: Can I use a different reference genome, such as the human genome, as a test?**
A: No. Your analysis must use the *Cricetulus griseus* genome. All downstream analysis — quantification, differential expression, pathway analysis — depends on mapping to the correct organism. Aligning to the wrong genome is not a valid test and will not produce usable results.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
