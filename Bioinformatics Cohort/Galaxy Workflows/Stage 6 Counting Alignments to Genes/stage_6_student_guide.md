# Stage 6: Counting Alignments to Genes
## Student Guide — usegalaxy.eu

---

## Objective

In this stage you will quantify gene expression by counting how many aligned reads map to each annotated gene in the Chinese hamster genome. By the end of this stage, you will have a count table — a matrix of integer values where each row is a gene and each value represents how many reads from a given sample were assigned to that gene. This count table is the direct input to differential expression analysis in Stage 7.

This guide covers both **single-end (SE)** and **paired-end (PE)** datasets. The SE and PE workflows are functionally identical — the only structural difference is how the per-sample count files are assembled at the end.

---

## What Counting Is and Why It Matters

Alignment (Stage 5) told you *where* each read maps in the genome. Counting tells you *which gene* that position belongs to, and how many reads map to each gene across each sample. The count assigned to a gene is a proxy for its expression level — a gene with more reads mapping to it was more highly transcribed in that sample.

These raw counts are what DESeq2 (Stage 7) uses to test for differential expression between your treatment and control conditions. DESeq2 works with raw integer counts, not normalised values, not RPKM, not FPKM, not TPM. The integrity of your count table — whether counts are correctly assigned and whether the counting parameters are appropriate for your library — directly determines the validity of your differential expression results.

In the context of CHO cell biopharmaceutical research, accurately quantifying gene expression changes between, for example, a fed-batch versus a batch culture, or a cell line producing high versus low product titres, requires that every counting decision in this stage is appropriate for the data. The most consequential of these decisions is **library strandedness**.

---

## Critical: Determine and Set Your Library Strandedness Before Running

**You must complete this section before running the workflow. It is not optional.**

The workflow is hardcoded with `stranded = yes`. This assumes your RNA-seq library is **forward-stranded**. If your library is reverse-stranded or unstranded — which is the case for the majority of CHO RNA-seq datasets deposited in SRA — using `stranded=yes` will cause HTSeq-count to discard most of your reads, producing a count table that is wrong and cannot be used for differential expression analysis. You will not be able to detect this error from the workflow outputs alone without explicitly checking the diagnostic rows.

Every student must determine their correct strandedness setting and, if it differs from `yes`, follow the instructions below to run HTSeq-count with the correct parameter before proceeding.

---

### Step 1: Understand the Three Strandedness Options

| HTSeq-count setting | Library type | What it means |
|---|---|---|
| `yes` | Forward-stranded | Reads align to the **same strand** as the gene they came from |
| `reverse` | Reverse-stranded | Reads align to the **opposite strand** as the gene — the most common setting for modern Illumina libraries |
| `no` | Unstranded | Reads can originate from either strand — equal probability |

**If you use the wrong setting:**

| Your library is... | You run with... | Result |
|---|---|---|
| Reverse-stranded | `yes` | 80–95% of reads assigned to `__no_feature` — count table nearly empty |
| Unstranded | `yes` | ~50% of reads assigned to `__no_feature` — counts halved |
| Forward-stranded | `yes` | Correct — proceed normally |
| Reverse-stranded | `reverse` | Correct — proceed normally |
| Unstranded | `no` | Correct — proceed normally |

---

### Step 2: Determine Your Library's Strandedness

#### 2a — Look up the library preparation kit in the original paper

Find the Methods section of your assigned paper and look for the RNA-seq library preparation kit. Match it to the table below:

| Kit name (or phrase in paper) | Strandedness | HTSeq-count setting |
|---|---|---|
| Illumina TruSeq Stranded mRNA | Reverse-stranded | `reverse` |
| Illumina TruSeq Stranded Total RNA | Reverse-stranded | `reverse` |
| Illumina TruSeq RNA (no "Stranded") | Unstranded | `no` |
| NEB Ultra II Directional RNA Library Prep | Reverse-stranded | `reverse` |
| NEB Ultra Directional RNA Library Prep | Reverse-stranded | `reverse` |
| dUTP method / dUTP-based strand-specific | Reverse-stranded | `reverse` |
| Clontech SMARTer Stranded Total RNA | Reverse-stranded | `reverse` |
| Ligation-based strand-specific | Reverse-stranded | `reverse` |
| "strand-specific" without further detail | Likely reverse-stranded | Confirm below |
| "non-strand-specific" / "non-directional" | Unstranded | `no` |
| Kit not named / not described | Unknown | Must check SRA — see below |

> **The large majority of CHO RNA-seq datasets published from 2013 onwards use TruSeq Stranded mRNA or a dUTP-based protocol, making `reverse` the most common correct setting.** If your paper describes a TruSeq Stranded kit, your setting is `reverse`, not `yes`. The workflow default of `yes` is a starting point, not a guarantee.

#### 2b — Check the SRA metadata if the paper does not name the kit

1. Go to the NCBI SRA Run Selector for your dataset (ncbi.nlm.nih.gov/Traces/study/)
2. Enter your GEO accession (GSE...) or SRA study accession (SRP...) in the search box
3. In the results table, look for the following columns — you may need to click **Select Columns** to add them:
   - **LibraryLayout**: SINGLE or PAIRED (not strandedness, but confirms read type)
   - **LIBRARY_CONSTRUCTION_PROTOCOL**: often contains kit names or dUTP mentions
   - **Instrument**: confirms the sequencing platform (should be Illumina for all these datasets)
4. Click on an individual SRR accession and go to its **Metadata** tab — this sometimes contains more detailed library preparation information including kit names

If the SRA metadata does not specify the kit, search for the paper's GEO accession (GSE...) on NCBI GEO. On the GEO Series page, scroll to "Library strategy" and "Library source". Some submitters include the library prep protocol in the series description or in sample-level metadata.

#### 2c — Record your conclusion

Write down:
- What the paper states about library preparation
- What strandedness setting you determined (yes / reverse / no)
- Where you found this information (paper Methods, SRA metadata, etc.)

You will need this for your Methods section. If you cannot determine the strandedness from the paper or SRA metadata after careful searching, contact your supervisor **before running the workflow**.

---

### Step 3: If Your Setting is `yes` — Run the Workflow Normally

If you have confirmed your library is forward-stranded and `stranded=yes` is correct, proceed directly to Section A or Section B below.

---

### Step 4: If Your Setting is `reverse` or `no` — Run HTSeq-count Manually

The workflow file is hardcoded to `stranded=yes` and cannot be changed at the run dialog. If your library requires `reverse` or `no`, you must run HTSeq-count as a standalone tool rather than through the workflow. Follow these steps exactly.

#### Step 4a: Decompress the GTF file

HTSeq-count requires an uncompressed GTF. Your `genes.gtf.gz` from Stage 5 is compressed. Decompress it first:

1. In the Galaxy tool search bar (left panel), type **"Convert gz"**
2. Click **"Convert gz compressed file to uncompressed"**
3. Under **"File to convert"**, select your `genes.gtf.gz` file (tagged `gene_model`, from Stage 5)
4. Click **Execute**
5. When the job turns green, click the **pencil icon** on the output dataset
6. Go to the **Datatypes** tab
7. Change the datatype to **`gtf`** and click **Save**

The decompressed GTF file is now ready for HTSeq-count.

#### Step 4b: Run HTSeq-count with the correct stranded setting

1. In the Galaxy tool search bar, type **"htseq"**
2. Click **"htseq-count"** (version 2.0.9 or nearest available)
3. Configure the tool as follows — match every parameter exactly:

| Parameter | Value | Notes |
|---|---|---|
| **Aligned SAM/BAM File** | Your BAM collection from Stage 5 | SE: "Mapped RNA-seq data"; PE: "Mapped Data" |
| **Single-end or paired-end reads** | Single-end (SE) or Paired-end (PE) | Match your data type |
| **GFF/GTF File** | The decompressed GTF file from Step 4a | Do not use `genes.gtf.gz` directly here |
| **Stranded** | **`reverse`** or **`no`** | Use the setting you determined in Step 2 — this is the critical change |
| **Minimum alignment quality** | `10` | Must match the workflow default |
| **Mode** | `Union` | Must match the workflow default |
| **Feature type** | `exon` | Must match the workflow default |
| **ID Attribute** | `gene_id` | Must match the workflow default |
| **Additional options** | Leave at defaults | Do not change anything else |

4. Click **Execute**
5. HTSeq-count will run on each BAM file in your collection, producing a per-sample count file

> **Check your collection type:** When you select your BAM collection under "Aligned SAM/BAM File", Galaxy may ask whether to run on the whole collection or a single file. Select the **collection** option so that HTSeq-count runs on each sample independently.

#### Step 4c: Verify the output

When HTSeq-count completes:

1. Click the eye icon on one of the output count files
2. Scroll to the bottom and locate the `__no_feature` row
3. Calculate: `__no_feature / (sum of all gene counts + __no_feature) × 100`

| Result | Interpretation |
|---|---|
| `__no_feature` < 20% | Strandedness setting is correct — proceed |
| `__no_feature` 20–40% | Marginal — recheck your determination; discuss with supervisor |
| `__no_feature` > 40% | Setting is still wrong — do not proceed; contact supervisor |

If the counts look correct, these output files replace the workflow's output and are used as input to Stage 7.

---

### Step 5: Before You Move On

Regardless of which strandedness setting you used, you must be able to confirm:
- What setting you used and why
- That `__no_feature` is below 20% in all samples
- That your total assigned reads are consistent with your Stage 5 mapping rates

Record the strandedness setting and the evidence you used to determine it — you will need both for your Methods section.

---

## Understanding the Inputs

This workflow takes **two inputs**:

### Input 1: GTF Annotation File

The same gene annotation used in Stage 5 — the `genes.gtf.gz` file created by the Stage 5 workflow from the Ensembl download, tagged `gene_model` in your history.

HTSeq-count uses this file to determine which genomic positions correspond to which genes. The workflow automatically decompresses it before use — you do not need to decompress it manually.

> **Important:** The GTF used here must be the **same version** as the one used in Stage 5 alignment. Using a different GTF version for counting than for alignment will produce inconsistencies, because the exon coordinates in the GTF must match what STAR used to guide splice junction detection. In practice, you should always select the `genes.gtf.gz` file from your Stage 5 history.

### Input 2: BAM Collection

The collection of sorted BAM files from Stage 5.

- **SE students:** the "Mapped RNA-seq data" collection (tagged `mapped_data`)
- **PE students:** the "Mapped Data" collection (tagged `mapped`)

HTSeq-count runs on each BAM file independently, producing a per-sample count file.

---

## Before You Begin

- [ ] Stage 5 completed successfully — your BAM collection and `genes.gtf.gz` are present in your Galaxy history
- [ ] You have read the Methods section of your assigned paper and identified the library preparation kit
- [ ] You have checked the SRA metadata if the kit was not named in the paper
- [ ] You have determined your strandedness setting (`yes`, `reverse`, or `no`) and recorded the evidence
- [ ] **If your setting is `reverse` or `no`:** you have followed Step 4 above and run HTSeq-count manually with the correct parameter — do not use the workflow file directly
- [ ] **If your setting is `yes`:** you have confirmed this with the kit lookup table and are ready to run the workflow
- [ ] You have noted the Ensembl GTF version (same as used in Stage 5)

---

---

## Section A — Single-End (SE) Workflow

---

### A1: Import and Run the SE Counting Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-6_gene_level_counting_se.ga`** provided by your supervisor

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the SE counting workflow
2. Match inputs:
   - **"gtf"** → select your `genes.gtf.gz` file from Stage 5 (tagged `gene_model`)
   - **"bam"** → select your "Mapped RNA-seq data" BAM collection from Stage 5
3. Click **Run Workflow**

---

### A2: Monitor and Verify (SE)

**Monitor progress:**
- HTSeq-count runs on each BAM independently — jobs will appear and complete at different times
- After all HTSeq-count jobs finish, the Collapse Collection step runs to assemble the final output
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset)

**When complete, verify:**
- A file called **`HTSeq_counts`** appears in your history, tagged `counts`
- A collection of **`allcounts`** files also appears (one per sample) — keep these for strandedness verification
- Click the eye icon on `HTSeq_counts` — you should see a tabular file with Ensembl gene IDs in the first column and integer counts in subsequent columns

---

### A3: SE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input: GTF | Accepts `genes.gtf.gz` (gtf format) from Stage 5 |
| 2 | Input: BAM collection | Accepts the BAM list collection from Stage 5 |
| 3 | Convert gz to uncompressed (v1.0.0) | Decompresses the GTF from gz to plain GTF format (hidden) |
| 4 | HTSeq-count (v2.0.9+galaxy0) | Counts reads per gene for each BAM. Parameters: mode=union, featuretype=exon, idattr=gene_id, minaqual=10, stranded=yes. Outputs per-sample `counts` and `allcounts` tabular files |
| 5 | Collapse Collection (v5.1.0) | Merges per-sample count files into a single `HTSeq_counts` file (tagged `counts`) |

---

---

## Section B — Paired-End (PE) Workflow

---

### B1: Import and Run the PE Counting Workflow

#### Import the workflow (first time only):
1. Go to **Workflow** in the top menu bar
2. Click **Import** (top right)
3. Upload **`Galaxy-Workflow-6_gene_level_counting_pe.ga`** provided by your supervisor

#### Run the workflow:
1. Go to **Workflow** and click the **play button (▶)** next to the PE counting workflow
2. Match inputs:
   - **"gtf"** → select your `genes.gtf.gz` file from Stage 5 (tagged `gene_model`)
   - **"bam"** → select your "Mapped Data" BAM collection from Stage 5
3. Click **Run Workflow**

---

### B2: Monitor and Verify (PE)

**Monitor progress:**
- HTSeq-count runs on each BAM independently
- There is no Collapse Collection step in the PE workflow — the output is a collection of per-sample files
- If a dataset turns red, see [Diagnosing a Red Dataset](#diagnosing-a-red-dataset)

**When complete, verify:**
- A collection of **`htseq_counts`** files appears in your history (one per sample), tagged `counts`
- The `allcounts` output is hidden by default — unhide it if you need to check strandedness (see below)
- Click the eye icon on any `htseq_counts` file — it should show Ensembl gene IDs and integer counts

> **To unhide `allcounts` (PE):** Click **"X hidden"** at the top of your history panel to reveal hidden datasets. Find the `allcounts` datasets and use them for the strandedness verification described below.

---

### B3: PE Workflow — Technical Summary

| Step | Tool | What it does |
|---|---|---|
| 1 | Input: GTF | Accepts `genes.gtf.gz` (gtf format) from Stage 5 |
| 2 | Input: BAM collection | Accepts the BAM list collection from Stage 5 |
| 3 | Convert gz to uncompressed (v1.0.0) | Decompresses GTF (hidden) |
| 4 | HTSeq-count (v2.0.9+galaxy0) | Counts reads per gene for each BAM. Same parameters as SE: mode=union, featuretype=exon, idattr=gene_id, minaqual=10, stranded=yes. Per-sample `htseq_counts` files (tagged `counts`); `allcounts` hidden |

> **Note:** The PE workflow does not include a Collapse Collection step. Each sample produces its own count file. Stage 7 (DESeq2) will combine these into a count matrix.

---

---

## Understanding the HTSeq-count Parameters

### `mode = union`

The counting mode determines how reads that overlap multiple genomic features are handled.

In **union mode**, a read is assigned to a gene if it overlaps at least one exon of that gene. If a read overlaps exons from two different genes, it is counted as `__ambiguous` (not assigned to either). Union mode is the recommended setting for standard RNA-seq analysis and is the most widely used.

### `featuretype = exon`

HTSeq-count only considers features labelled `exon` in the GTF when assigning reads. Reads that fall entirely within an intron are not counted. This is correct for RNA-seq, where reads come from processed mRNA (introns removed).

### `idattr = gene_id`

Each counted read is attributed to a gene using the `gene_id` attribute from the GTF. For the Ensembl Chinese hamster annotation, these are Ensembl stable gene IDs in the format `ENSCGRG00000XXXXX`. These are the row identifiers in your count table.

### `minaqual = 10`

Reads with a MAPQ alignment quality score below 10 are not counted. Recall from Stage 5 that STAR assigns MAPQ=60 to uniquely mapping reads and lower scores to multimappers. A threshold of 10 excludes most multimapped reads from counting, which is appropriate for standard differential expression analysis where multimapper handling can introduce ambiguity.

### `stranded = yes`

As discussed above, this assumes forward-stranded libraries. **Verify this is correct for your data before running.**

---

## Understanding the Count Output

### The Count Table

The main output (`HTSeq_counts` for SE, `htseq_counts` per sample for PE) is a tab-delimited text file with two columns:

```
ENSCGRG00000000001    0
ENSCGRG00000000002    145
ENSCGRG00000000003    2341
...
```

Each row is a gene. The integer is the number of reads unambiguously assigned to that gene in that sample. Most genes will have zero counts — this is normal. The transcriptome is not uniformly expressed; only a fraction of annotated genes are expressed in any given sample.

### The Diagnostic Rows (`allcounts`)

The bottom of each count file contains special rows beginning with `__` that report reads not assigned to any gene. These are critical for quality checking:

| Row | What it counts | What a high value suggests |
|---|---|---|
| `__no_feature` | Reads that did not overlap any annotated exon | High value (> 20–30% of total reads): possibly wrong strandedness, highly intronic reads, or significant unannotated transcription |
| `__ambiguous` | Reads overlapping exons of two or more different genes | Typically 1–5%; higher values may indicate closely packed gene loci |
| `__too_low_aQual` | Reads with MAPQ < 10 (excluded by minaqual threshold) | Should be low if STAR alignment quality was high; high value may indicate multimapper inflation |
| `__not_aligned` | Reads not aligned in the BAM | Should be very low or zero for SE (unmapped reads were excluded by STAR filter); may be slightly higher for PE |
| `__alignment_not_unique` | Multimapped reads (NH tag > 1) excluded from counting | Typically reflects the multimapper rate from Stage 5 |

### Using Diagnostic Rows to Verify Strandedness

After the workflow completes, open the `allcounts` output for one or two representative samples and check the `__no_feature` count.

Calculate the proportion of reads in `__no_feature` relative to the total reads counted:

```
% no_feature = __no_feature / (sum of all gene counts + __no_feature) × 100
```

| `__no_feature` proportion | Interpretation |
|---|---|
| < 20% | Strandedness setting is likely correct |
| 20–40% | Borderline — investigate; may still be acceptable depending on the library |
| > 40% | Strandedness setting is likely **wrong** — the library may be reverse-stranded or unstranded. Do not proceed to Stage 7. Discuss with your supervisor. |

> **Quick check for reverse-stranded libraries:** If you suspect your library is reverse-stranded (e.g., TruSeq Stranded mRNA), the `__no_feature` count with `stranded=yes` will be very high (often 40–60% of reads). If you recount with `stranded=reverse`, most of these reads will be assigned to genes. Your supervisor can advise on how to re-run with the correct setting.

---

## Diagnosing a Red Dataset

**Common Stage 6 errors:**

| Error or symptom | Cause | Fix |
|---|---|---|
| GTF format error | The GTF file was not correctly decompressed or is not a valid GTF | Confirm you selected the `genes.gtf.gz` from Stage 5 and that it is non-empty; check it is typed as `gtf.gz` in your history |
| HTSeq-count fails with "BAM not sorted" | The BAM is not coordinate-sorted | STAR outputs coordinate-sorted BAMs by default — if this error occurs, check that the BAM is from Stage 5 and has not been processed by another tool that changed the sort order |
| HTSeq-count fails with "empty input" | BAM file is empty (0 reads) | Check Stage 5 mapping rate for that sample; if STAR produced an empty BAM, the alignment failed for that sample |
| All count files have very low totals | Strandedness mismatch — most reads going to `__no_feature` | See strandedness verification above; discuss with supervisor |
| Collapse Collection fails (SE) | One or more HTSeq-count jobs failed silently | Check that all HTSeq-count outputs are green before Collapse runs; re-run any failed jobs |
| Job grey for > 2 hours | Queue is busy | HTSeq-count is computationally moderate — it should complete within 1–2 hours per sample for typical datasets. If grey > 4 hours, check Galaxy status. |

---

## Recording Your Counting Results for the Written Assessment

Stage 6 produces the count table that is the direct input to your differential expression analysis. It must be described precisely in your Methods, and the counting statistics provide important quality metrics for your Results.

---

### What to Record

For each sample (from the `allcounts` diagnostic rows):

| Metric | Where to find it |
|---|---|
| Total reads counted (sum of gene counts) | Sum of all non-`__` rows in allcounts |
| `__no_feature` count and percentage | `allcounts` file, bottom rows |
| `__ambiguous` count | `allcounts` file |
| `__too_low_aQual` count | `allcounts` file |
| `__not_aligned` count | `allcounts` file |
| Number of genes with at least 1 count | Count the non-zero rows in the counts file |
| Confirmed strandedness setting | From paper + supervisor confirmation |

---

### How This Feeds into Your Written Submission

#### Methods Section (rubric A2 — 10 marks)

Your Methods section must describe the counting step precisely. Include:

- The tool and version (HTSeq-count v2.0.9)
- The annotation source (same Ensembl GTF as Stage 5 — state the version)
- The counting mode (union)
- The minimum alignment quality threshold (MAPQ ≥ 10)
- The strandedness setting and justification (why stranded=yes or why a different setting was used)
- The feature type counted (exon)
- The attribute used for gene identification (gene_id, Ensembl stable IDs)

**Example Methods language:**

> "Gene-level read counts were generated using HTSeq-count (v2.0.9, Galaxy platform, usegalaxy.eu) in union mode, counting reads overlapping exon features and assigning them using the `gene_id` attribute from the Ensembl *Cricetulus griseus* GTF annotation (release [XXX]). Reads with alignment quality below MAPQ 10 were excluded. Library strandedness was determined to be [forward-stranded / reverse-stranded / unstranded] based on the library preparation protocol described in [Author et al., Year] ([kit name]), and the stranded parameter was set accordingly. A mean of [X]% of aligned reads were assigned to annotated genes across all samples."

A strong Methods section (A-grade) states all parameters by name and value, explicitly justifies the strandedness setting with reference to the library preparation method, and reports the mean counting efficiency. Stating only "reads were counted using HTSeq-count" will not meet the minimum standard.

---

#### Results Section (rubric A3 — 15 marks)

The counting statistics should be incorporated into the data processing narrative already begun in Stages 3–5. A sentence or two is sufficient for the main text; the full per-sample diagnostic statistics belong in supplementary material.

**Example Results language:**

> "Gene-level read counts were generated for [n] samples using HTSeq-count. A mean of [X]% of aligned reads were assigned to annotated genes (range: [X]–[X]%), with [X]% classified as `__no_feature` and [X]% as `__ambiguous` (Supplementary Table S[X]). Counts were generated for [X] genes with at least one read in at least one sample."

The count table itself — a matrix of potentially tens of thousands of genes — belongs entirely in supplementary material (or in a repository link). Do not include it in the main text. The Results section discusses what you found with the count data (differential expression), not the count data itself.

---

#### Discussion Section (rubric A4 — 15 marks)

Stage 6 discussion content is typically brief unless counting revealed a specific issue worth contextualising:

- If `__no_feature` rates were higher than typical (> 20%), discuss why — is this consistent with the original paper's reported counting statistics? Is it attributable to the strandedness setting, the completeness of the GTF, or intronic transcription?
- If the original paper used a different counting tool (e.g., featureCounts, Salmon, kallisto), note this and consider whether it affects comparability of your results with theirs

---

### Summary: Stage 6 Contributions to Your Submission

| Submission section | What Stage 6 provides | Rubric component |
|---|---|---|
| Methods | HTSeq-count v2.0.9, GTF version, mode=union, MAPQ ≥ 10, strandedness setting + justification, mean counting efficiency | A2 (10 marks) |
| Results | Mean % reads assigned to genes; `__no_feature` and `__ambiguous` rates; number of expressed genes (extends data processing narrative) | A3 (15 marks) |
| Discussion | Contextualisation of counting efficiency; comparison with original paper's approach if different | A4 (15 marks) |
| Conclusion | Not directly contributed to by this stage — the Conclusion synthesises the biological findings of the full analysis (see Stage 8 guide) | — |

---

## Self-Check: Do You Understand This Stage?

Before moving to Stage 7, you should be able to answer:

1. What does HTSeq-count union mode do? How does it handle a read that overlaps two exons of the same gene? How does it handle a read that overlaps exons of two different genes?
2. Why does HTSeq-count use raw integer counts rather than normalised values? Why does DESeq2 require raw counts as input?
3. What does `stranded=yes` mean? What would happen to your count table if your library is actually reverse-stranded and you used `stranded=yes`?
4. How did you determine the strandedness of your library? What evidence did you use?
5. What are the `__no_feature` rows, and what proportion did you observe? Is this consistent with the correct strandedness setting?
6. Why must the GTF used for counting be the same version as used for alignment in Stage 5?
7. What is the difference between the SE output (`HTSeq_counts`, collapsed) and the PE output (per-sample `htseq_counts` collection)?
8. A gene has zero counts in all samples — does this mean it is not expressed, or could there be other reasons? What reasons?

---

## Before You Move On: Checklist

- [ ] HTSeq-count completed successfully for all samples
- [ ] I have confirmed with my supervisor that `stranded=yes` was appropriate for my library
- [ ] I have checked the `allcounts` diagnostic rows and `__no_feature` is < 20% in all samples
- [ ] My count output (`HTSeq_counts` or `htseq_counts` collection) contains the expected number of files/entries
- [ ] I have previewed the count table and confirmed Ensembl gene IDs are present in the correct format
- [ ] I have recorded counting statistics (% assigned, % no_feature, % ambiguous) for each sample
- [ ] I am ready to proceed to Stage 7 with my count data

---

## Frequently Asked Questions

**Q: My `__no_feature` count is very high (> 40%) — what does this mean?**
A: This almost certainly indicates that the `stranded=yes` setting does not match your library's strandedness. The most common cause is that the library is reverse-stranded (e.g., TruSeq Stranded mRNA kit), but the counter is set to forward-stranded. Do not proceed to Stage 7. Discuss with your supervisor — the workflow will need to be re-run with the correct strandedness setting.

**Q: What is the difference between `counts` and `allcounts`?**
A: `counts` contains only the per-gene integer counts (the main output used downstream). `allcounts` contains all of this plus the `__` diagnostic rows at the bottom. Use `allcounts` to verify strandedness and counting quality; use `counts` as the input to Stage 7.

**Q: My library preparation kit is not mentioned in the paper — how do I determine strandedness?**
A: Follow Step 2b in the strandedness section above — check the SRA Run Selector's LIBRARY_CONSTRUCTION_PROTOCOL column, the individual SRR metadata tab, and the GEO Series page. If the SRA metadata contains no kit information, check whether the paper's supplementary materials or data availability statement mentions the protocol. As a last resort, your supervisor can run `infer_experiment.py` (RSeQC) on one of your Stage 5 BAM files, which empirically infers strandedness from the alignment patterns. Do not guess and do not run with an unverified setting — you will have to repeat this stage if the setting is wrong.

**Q: Can I use a different GTF from Stage 5? For example, a newer version?**
A: No. The GTF must be the same version used in STAR alignment. This is because the gene annotations (exon coordinates) used to guide STAR's splice junction database must exactly match those used by HTSeq-count to assign reads. Using different versions will cause systematic errors in gene assignment.

**Q: Most of my genes have zero counts — is my counting working?**
A: Yes, this is expected. The Chinese hamster genome annotation contains tens of thousands of genes, but any given cell type typically expresses only a fraction of them at detectable levels. It is normal for the majority of annotated genes to have zero or very low counts in RNA-seq data. Focus on whether the expressed genes (non-zero counts) are present and whether the total counting efficiency is within the expected range.

**Q: The SE workflow produces a single `HTSeq_counts` file but I need per-sample files for DESeq2 — is this a problem?**
A: The `HTSeq_counts` file from the SE workflow contains counts from all samples merged into one file. Stage 7 (DESeq2) is configured to work with this format. Do not attempt to split or reformat the file manually.

**Q: For PE data, htseq-count uses the same parameters as SE — is there anything different about how it processes paired-end reads?**
A: HTSeq-count handles PE reads by treating each read pair as a single unit. Counting mode and strandedness parameters interact with paired reads in the same logical way as SE. The key difference is that PE reads provide two alignment positions (one for each mate), and HTSeq uses both when assigning a count. In practice, the parameters set in this workflow are appropriate for PE RNA-seq.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
