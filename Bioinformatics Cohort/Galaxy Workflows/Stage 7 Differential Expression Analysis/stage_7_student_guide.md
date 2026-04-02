# Stage 7 — Differential Expression Analysis

## Objective

Identify genes that are significantly differentially expressed between your treatment and control conditions, annotate those genes with biological identifiers, and run a pathway enrichment analysis to interpret the biological significance of your findings.

This is the stage that produces the primary biological results of your project. Everything you generate here will form the core of your Results and Discussion sections.

---

## Context: What Are You Actually Doing and Why Does It Matter?

CHO cells are the dominant production system for recombinant biopharmaceutical proteins. Understanding how a treatment — a media supplement, a genetic modification, a process change — alters gene expression at the transcriptome level is central to rational bioprocess development. If you know *which genes* change and *which pathways* those genes belong to, you can start to explain *why* a treatment changes cell behaviour (productivity, growth, quality).

This stage uses three tools:

**DESeq2** normalises your raw count data, models the sources of variation between replicates, and tests each gene for statistically significant differential expression. It produces a fold change and an adjusted p-value for every gene in the genome.

**deg_annotate** adds human-readable gene names and biotype labels to the DESeq2 output so you can see "ACTB" instead of ENSCGRG00015004534.

**gProfiler GOSt** takes your list of significant genes and asks: are these genes over-represented in known biological pathways and processes? This converts a gene list into a biological story.

---

## Before You Begin

You need all three of the following ready before running the workflow:

| Input | What it is | Where it comes from |
|-------|-----------|---------------------|
| Count collection | A Galaxy dataset collection of HTSeq-count output files, one per sample | Output of Stage 6 |
| Group table | A two-column file linking sample names to experimental groups | You create this — see Step 1 below |
| Annotation file | The GTF or gene annotation file for *Cricetulus griseus* | The same GTF used in Stages 5 and 6 |

**Do not proceed until your Stage 6 count collection is complete and all samples are present.** Check your count collection: it should contain one file per sample, and the file names should match your sample identifiers exactly.

---

## Step 1 — Prepare Your Group Table

The Group table is the most important file you will create in this project. If it is wrong, your DESeq2 results will be wrong — either silently incorrect or the workflow will fail.

### What the Group Table Does

DESeq2 needs to know which samples belong to the "control" group and which belong to the "treatment" (test) group. The workflow uses a method called `group_tags`, which reads sample group assignments from this file and uses them to set the experimental design.

### Exact Label Requirements

The workflow is configured with two specific factor levels: `control_cohort` (for control samples) and `test_cohort` (for treatment samples). To match these levels, your Group table **must** use exactly these labels:

- `control` — for all control samples
- `test` — for all treatment samples

Any other spelling, capitalisation, or label will cause DESeq2 to fail or produce incorrect results. Use lowercase. Do not use "Control", "CONTROL", "ctrl", "treated", or any other variant.

### File Format

The Group table is a two-column tab-delimited text file with no header row:

```
SRR1234567	control
SRR1234568	control
SRR1234569	control
SRR1234570	test
SRR1234571	test
SRR1234572	test
```

Column 1: the sample identifier — this **must exactly match** the dataset name in your count collection in Galaxy.  
Column 2: the group label — `control` or `test` only.

### How to Find Your Sample Names

1. In Galaxy, open your count collection from Stage 6.
2. Click the collection name to expand it.
3. Note the exact name of each dataset inside (not the collection name — the individual file names).
4. Use these names, character for character, in column 1 of your Group table.

If your Stage 6 output was from **single-end data**, your count collection is named `HTSeq_counts`. If it was from **paired-end data**, your count collection contains per-sample `htseq_counts` files.

### Creating and Uploading the Group Table

1. Create the file in a plain text editor (TextEdit on Mac in plain text mode, Notepad on Windows, or any code editor). Do not use Word or Excel — they introduce hidden formatting.
2. Save the file as `group_table.txt`.
3. In Galaxy, go to **Upload Data** → **Choose local file** → select `group_table.txt`.
4. Set the file format to **tabular**.
5. Click **Start**, then **Close** when the upload completes.

> **Check:** After uploading, click the eye icon next to your group_table.txt in Galaxy to preview it. You should see two columns separated cleanly. If it appears as one long column, the file is not tab-delimited — recreate it.

---

## Step 2 — Run the Workflow

Only one workflow file exists for Stage 7: **Workflow 7 — Differential Gene Expression (SE)**. If you are working with paired-end data, use the same workflow — your HTSeq-count collection from Stage 6 PE is compatible.

### Loading the Workflow

1. In Galaxy, go to **Workflow** in the top navigation bar.
2. Find **7_differential_gene_expression_se** and click **Run**.

### Connecting the Inputs

The workflow has three input boxes:

| Input box label | Connect to |
|-----------------|-----------|
| `count_collection` | Your HTSeq-count collection from Stage 6 |
| `group_table` | Your `group_table.txt` from Step 1 |
| `annotation` | The GTF annotation file (genes.gtf or the decompressed GTF from Stage 5/6) |

For the annotation input: use the same GTF file you used in Stages 5 and 6. If your GTF is still compressed (`.gtf.gz`), either use the decompressed version from your Stage 6 history or run the **"Convert gz compressed file to uncompressed"** tool on it first and change the datatype to `gtf`.

### Running

Click **Run Workflow**. The workflow contains 13 steps and will take several minutes to complete. Most steps are fast; DESeq2 itself typically takes 2–5 minutes.

---

## Step 3 — Understand What the Workflow Does

The workflow runs in this order:

### Tag Elements (Step 3 in the workflow)

Before DESeq2 can read the group assignments, Galaxy must attach a tag to each count file identifying which group it belongs to. The **Tag elements** tool reads your Group table and applies `control_cohort` or `test_cohort` tags to the appropriate files in your count collection. This is what connects your sample metadata to the count data — it is not a filtering step and does not change any counts.

### DESeq2

DESeq2 receives the tagged count collection and performs:

1. **Normalisation** — accounts for differences in total read counts between samples (library size differences)
2. **Dispersion estimation** — models the natural variation between biological replicates for each gene
3. **Statistical testing** — fits a negative binomial model and tests whether the difference between `test_cohort` and `control_cohort` is greater than expected by chance

The workflow uses a **parametric fit** for dispersion estimation, which is appropriate for most RNA-seq datasets. The experimental design is: `~ condition`, where `condition` is the factor with two levels: `control_cohort` (reference) and `test_cohort` (treatment). Log2 fold changes are reported as test vs. control (positive = higher in test).

**DESeq2 produces two outputs**, both of which are hidden by default in the workflow:

| Output | Contents |
|--------|----------|
| DESeq2 results table | One row per gene: gene ID, base mean, log2FC, lfcSE, stat, p-value, adjusted p-value |
| Diagnostic plots (PDF) | Size factor distribution, dispersion estimate plot, MA plot, PCA plot |

To unhide and view the DESeq2 outputs, find them in your Galaxy history (they will appear with a faded/hidden icon), click the visibility icon (eye with a line through it) to make them visible.

> **Always examine the DESeq2 diagnostic plots before interpreting results.** The PCA plot is particularly important — your replicate samples should cluster together, and the treatment group should separate from the control group. If your replicates do not cluster, there may be a sample swap, a labelling error in your Group table, or a genuine biological problem with one of your samples.

#### DESeq2 Results Table Column Key

| Column | Name | Meaning |
|--------|------|---------|
| c1 | gene_id | Ensembl gene identifier |
| c2 | baseMean | Average normalised count across all samples |
| c3 | log2FoldChange | log2(test / control) |
| c4 | lfcSE | Standard error of the log2FC estimate |
| c5 | stat | Wald test statistic |
| c6 | pvalue | Uncorrected p-value |
| c7 | padj | Adjusted p-value (Benjamini-Hochberg FDR) |

### Filter by Adjusted P-value (c7 < 0.05)

The first filter retains only genes where the adjusted p-value (column 7) is less than 0.05. This is the standard FDR threshold — it means you accept a 5% false discovery rate across all genes tested. Genes that do not pass this threshold are not considered statistically significant.

### Volcano Plot

A volcano plot is generated from the filtered results. The workflow uses:
- **LFC threshold: 0.263** (log2 fold change = log2(1.2), approximately 1.2-fold change)
- **Significance threshold: 0.05** (adjusted p-value)

The 0.263 log2FC threshold is deliberately permissive — it is lower than the commonly cited 1.0 (2-fold change). This is intentional for CHO cell biology, where many biologically meaningful transcriptional changes are modest in magnitude. A 2-fold cut-off would exclude many genuinely regulated genes.

On the volcano plot:
- X-axis: log2 fold change (test vs. control). Right = higher in test; left = lower in test.
- Y-axis: −log10(adjusted p-value). Higher = more significant.
- Points in the upper-right quadrant: significantly upregulated in test condition
- Points in the upper-left quadrant: significantly downregulated in test condition
- Grey points: not significant or below fold change threshold

### Filter by Fold Change (|c3| ≥ 0.263)

A second filter applies the same LFC threshold used in the volcano plot, keeping only genes where the absolute log2 fold change is at least 0.263. After this step your dataset contains genes that are both statistically significant (FDR < 0.05) and of biological relevance (≥ 1.2-fold change).

### deg_annotate

The `deg_annotate` tool maps Ensembl gene IDs in your results back to the original GTF annotation file and adds two new columns:

| New column | Contents |
|------------|---------|
| gene_biotype | The Ensembl biotype classification (e.g., protein_coding, lncRNA, pseudogene, rRNA) |
| gene_name | The HGNC gene symbol or common name (e.g., ACTB, MYC, GAPDH) |

After this step you can read actual gene names instead of Ensembl identifiers.

### Filter for Protein-Coding Genes (c12 == 'protein_coding')

This filter retains only genes classified as `protein_coding` in the gene_biotype column (column 12 after annotation). Non-coding RNAs, pseudogenes, and other biotype classes are removed at this stage.

This is an analytical choice, not a technical requirement — it focuses your pathway analysis and written assessment on genes that produce proteins, which are the most interpretable in the context of CHO cell biology and biopharmaceutical manufacturing. You should acknowledge this filter in your Methods section and note what it excludes.

### Cut Column 13 (Gene Names)

This step extracts just the gene name column (column 13) from your filtered, annotated, protein-coding DEG list. This produces a plain list of gene symbols that gProfiler can read as input.

### gProfiler GOSt

**gProfiler** tests whether your differentially expressed genes are over-represented in known biological pathways and processes, relative to what you would expect by chance from all protein-coding genes in the genome.

#### Organism Setting: Why mmusculus?

The workflow is configured to use **Mus musculus (mouse)** as the reference organism, not *Cricetulus griseus* (Chinese hamster).

This is a deliberate analytical decision with a biological rationale: *Cricetulus griseus* has limited coverage in the gProfiler pathway database. The GO annotations, KEGG pathways, and REAC (Reactome) pathway assignments are sparse for Chinese hamster compared to mouse. Mouse is the most closely related well-annotated mammalian model organism to CHO, and the gene orthologues are well-characterised.

**You must acknowledge this limitation in your Discussion.** You are performing cross-species pathway inference. The gene names output by deg_annotate are derived from the CHO genome annotation, and gProfiler maps these names to their mouse orthologues for pathway lookup. Some CHO genes may not map cleanly, and some pathway assignments may differ between species.

#### Pathway Databases Used

The workflow queries these databases simultaneously:

| Database | Full name | What it covers |
|----------|-----------|---------------|
| GO:MF | Gene Ontology: Molecular Function | Biochemical activities of gene products |
| GO:CC | Gene Ontology: Cellular Component | Where in the cell a gene product is found |
| GO:BP | Gene Ontology: Biological Process | Higher-level biological processes |
| KEGG | Kyoto Encyclopedia of Genes and Genomes | Metabolic and signalling pathways |
| REAC | Reactome | Curated biochemical reaction pathways |
| WP | WikiPathways | Community-curated pathway maps |
| TF | Transcription Factor binding motifs | Regulatory enrichment (TRANSFAC/JASPAR) |
| MIRNA | MicroRNA targets | miRNA-based regulatory enrichment |

#### Multiple Testing Correction: gSCS

The workflow uses **g:SCS** (Set Counts and Sizes) correction, which is gProfiler's own threshold adjustment method tailored for the correlation structure of gene sets. It is more conservative than Bonferroni but more appropriate than uncorrected p-values when testing hundreds of overlapping gene sets.

#### Interpreting gProfiler Results

Each row in the gProfiler output represents one enriched term. Key columns:

| Column | Meaning |
|--------|---------|
| term_id | Database identifier (e.g., GO:0006955, KEGG:hsa04060) |
| term_name | Human-readable pathway/process name |
| p_value | Enrichment p-value (g:SCS corrected) |
| term_size | Total genes in this pathway in the database |
| query_size | Number of your genes that were tested |
| intersection_size | Number of your genes that overlap with this pathway |
| intersections | The specific gene names that overlap |

**Focus on terms where:**
- `p_value` < 0.05 (corrected)
- `intersection_size` ≥ 3 (avoid terms with only 1-2 genes — unreliable)
- The term makes biological sense in the context of CHO cells and your treatment

Look across databases: if KEGG, REAC, and GO:BP all return related terms pointing to the same biological process, that is strong convergent evidence. A term appearing in only one database with borderline significance should be interpreted cautiously.

### Final Output: Differential Expression Results

The workflow ends by assembling a Galaxy list collection named **Differential Expression Results** containing your gProfiler enrichment output. Your other key outputs — the volcano plot, the annotated DEG table, and the protein-coding gene list — are accessible in your Galaxy history.

---

## Step 4 — Unhide and Save Key Outputs

Several important outputs are hidden by default in the workflow. After the workflow completes:

1. In your Galaxy history, look for greyed-out (hidden) datasets.
2. Click **"Include hidden datasets"** at the top of the history panel to reveal them.
3. Unhide and rename the following:

| Output to find | Suggested rename |
|----------------|-----------------|
| DESeq2 results table | `deseq2_results_all` |
| DESeq2 diagnostic plots PDF | `deseq2_diagnostics` |
| Filtered DEGs (after both filters, before annotation) | `degs_filtered` |
| Annotated protein-coding DEG table | `degs_annotated_protein_coding` |
| Gene name list (gProfiler input) | `deg_gene_names` |
| gProfiler enrichment results | `pathway_enrichment_results` |

---

## Step 5 — Interpreting Your Results

### How Many DEGs Is Reasonable?

There is no single correct number. For a typical CHO cell perturbation experiment:

| DEG count | Interpretation |
|-----------|---------------|
| < 50 | Modest response; treatment had limited transcriptional impact, or statistical power is low (small n) |
| 50–500 | Moderate response; typical for media supplements, mild stress, moderate genetic modifications |
| 500–2,000 | Substantial response; typical for significant process changes or strong genetic perturbations |
| > 2,000 | Large-scale response; verify your Group table is not inverted before interpreting |

If you get zero DEGs, check:
- Is your Group table correctly formatted with `control` and `test` labels?
- Are your replicate samples in the correct groups?
- Does the PCA plot in the DESeq2 diagnostics show separation between groups?
- Do you have at least 3 replicates per group? (DESeq2 requires ≥ 2; ≥ 3 is strongly preferred)

### Reporting Counts at Each Filter Stage

Record the number of genes at each stage of the pipeline:

| Stage | Number of genes |
|-------|-----------------|
| All genes tested by DESeq2 | |
| After FDR < 0.05 filter | |
| After \|log2FC\| ≥ 0.263 filter | |
| After protein-coding filter | |
| Upregulated (log2FC > 0) | |
| Downregulated (log2FC < 0) | |

These numbers belong in your Results section. The filtering is part of your Methods.

---

## Error Diagnosis

### Workflow Fails at DESeq2

| Symptom | Likely cause | Fix |
|---------|-------------|-----|
| Error: "factor level not found" | Group table uses wrong labels | Recreate group table with exactly `control` and `test` in column 2 |
| Error: "zero counts for all samples" | Count collection is empty or wrong collection connected | Verify the count collection has data by opening individual files |
| Error: "sample names do not match" | Column 1 of group table does not match dataset names | Expand your count collection and copy the exact file names into column 1 |
| "All genes have zero variance" | Samples may be duplicates, or the wrong GTF was used in Stage 6 | Check your Stage 6 output; verify samples are biologically distinct |

### Workflow Completes but Zero DEGs

- Open the DESeq2 results table and confirm it has rows
- Look at the `padj` column — if all values are NA, DESeq2 could not fit the model (usually too few replicates or extreme outliers)
- Check the PCA plot — are your treatment and control samples completely intermixed? If so, there may be a labelling error

### gProfiler Returns No Enriched Terms

- This can be biologically real — a small DEG list may not hit any enriched pathway
- Check `intersection_size` in the output — if the query size is very small (< 10 genes), pathway enrichment is unlikely to be significant
- Consider whether your gene names are in the correct format for gProfiler (human/mouse HGNC symbols)
- Report the lack of enrichment honestly in your Discussion and discuss what this might mean

### gProfiler Returns Only GO:CC Terms for "Nucleus" or "Cytoplasm"

These are trivially enriched for almost any gene list (most genes are in the nucleus or cytoplasm). Do not report these as meaningful biological findings. Focus on specific molecular function, biological process, and named pathway terms.

---

## Red Dataset Diagnosis

If any step in the workflow produces a red (failed) dataset:

1. Click the red dataset to expand it.
2. Click the bug icon (View or report this error) to open the error log.
3. Check the **stderr** tab first — this usually contains the specific error message from the tool.
4. Check **Parameters** to confirm the inputs were connected correctly.
5. Check **Job Information** for exit code:

| Exit code | Meaning |
|-----------|---------|
| 0 | Success (if dataset is red despite exit 0, this is a Galaxy display issue — re-run or report) |
| 1 | Tool-level error — read stderr for the specific message |
| 137 | Out of memory — contact your supervisor; the job may need more resources |
| 143 | Timed out — re-run; transient infrastructure issue |

6. For DESeq2 failures: the most informative message is in stderr. Copy the exact error text when asking for help.

---

## Recording for Assessment

This is the highest-value stage for your written assessment. Your DESeq2 and pathway enrichment results provide the primary biological evidence for your Results and Discussion sections.

### Methods (A2 — 10 marks)

Your Methods must describe:

- That you used DESeq2 v2.11.40.8 via Galaxy (usegalaxy.eu) for differential expression analysis
- The experimental design: pairwise comparison, treatment vs. control, minimum n replicates per group
- Significance threshold: adjusted p-value < 0.05 (Benjamini-Hochberg FDR correction)
- Fold change threshold: |log2FC| ≥ 0.263 (approximately 1.2-fold)
- That results were filtered to protein-coding genes
- That pathway enrichment was performed using gProfiler GOSt with g:SCS multiple testing correction
- The databases queried: GO:BP, GO:MF, GO:CC, KEGG, REAC, WP
- That *Mus musculus* was used as the reference organism in gProfiler as a functional proxy for *Cricetulus griseus*, acknowledging the limitation

### Results (A3 — 15 marks)

Your Results must include:

- Total number of genes tested by DESeq2
- Number of genes passing each filter (FDR < 0.05; |log2FC| ≥ 0.263; protein-coding)
- Number upregulated and downregulated
- The volcano plot as a figure (with a proper figure legend including axis labels, threshold explanations, and n values)
- A table of the top 10–20 most significant DEGs (gene name, log2FC, adjusted p-value) — do not paste the entire results table
- The top enriched pathways from gProfiler with p-values and intersection sizes
- Description of the DESeq2 PCA plot (do samples cluster as expected?)

> **Recommended figure set:** Volcano plot (Figure 1), PCA plot from DESeq2 diagnostics (Figure 2), gProfiler enrichment dot plot or bar chart if available (Figure 3). You have a 2,000–2,500 word limit — your figure legends count toward this.

### Discussion (A4 — 15 marks)

Your Discussion must:

- Interpret what the DEG count and direction (more up vs. down regulation) means for CHO cell biology under your treatment
- Discuss specific genes: why are these genes changing? What is known about their function in the context of biopharmaceutical manufacturing, cell growth, or protein production?
- Connect your pathway enrichment results to the biological question: does the treatment activate or suppress pathways that you would expect based on the paper you read in Stage 1?
- Discuss the mmusculus proxy: acknowledge that CHO cells lack comprehensive pathway annotations; consider whether any CHO-specific biology might be missed
- Discuss limitations: sample size (replicates per group), fold change threshold choice, what the protein-coding filter excludes

### Conclusion

Your final written document must include a Conclusion section, separate from the Discussion. The Conclusion is short (50–100 words) and does not introduce new results or citations. It states:

- The primary biological finding in one sentence
- How this relates to the original paper's conclusion in one sentence
- The broader significance for biopharmaceutical manufacturing or CHO cell engineering
- A single forward-looking statement identifying the most important next step

Full guidance on writing the Conclusion is in the Stage 8 guide. Note that the Conclusion draws on the biological argument you have constructed from your Stage 7 results — it is not a summary of the pipeline.

---

## Self-Check Before Submitting Your History for Review

- [ ] Group table uploaded with `control` and `test` labels (exact)
- [ ] Count collection from Stage 6 connected correctly
- [ ] DESeq2 diagnostic PDF opened and PCA plot examined
- [ ] DESeq2 outputs unhidden and renamed
- [ ] Volcano plot generated and saved
- [ ] Annotated DEG table contains gene names and biotype columns
- [ ] Protein-coding DEG list downloaded for your write-up
- [ ] gProfiler results viewed and top enriched terms identified
- [ ] All outputs renamed with meaningful labels in your Galaxy history
- [ ] DEG counts at each filter stage recorded

---

## FAQ

**Q: My Group table looks correct but DESeq2 still fails saying "factor levels not found".**  
A: The most common cause is invisible whitespace or Windows line endings in your group table file. Open the file in a plain text editor and check that there are no trailing spaces after `control` or `test`. If you created the file on Windows, the line endings may be CRLF instead of LF — use a tool like Notepad++ to convert, or recreate the file in the Galaxy text editor.

**Q: My volcano plot looks blank — no red points.**  
A: Either no genes pass both thresholds, or the plot was generated from the pre-filter DESeq2 output. Check which dataset is connected to the volcano plot step. It should receive the output of the adjusted p-value filter (FDR < 0.05), not the raw DESeq2 output.

**Q: Should I report all the gProfiler results?**  
A: No. Report the top enriched terms, focusing on those with biological relevance to your experimental question. Group related terms — you do not need to list every GO:CC term independently. Focus on named pathways (KEGG, REAC, WP) and specific GO:BP terms that relate to CHO cell biology, cell growth, metabolism, or the specific perturbation you are studying.

**Q: I have paired-end data — do I need a different workflow?**  
A: No. The Stage 7 workflow accepts any HTSeq-count collection regardless of whether the reads were single- or paired-end. Connect your PE HTSeq-count collection from Stage 6 to the `count_collection` input as normal.

**Q: My DESeq2 results have many rows where padj is NA. Is this a problem?**  
A: NA values in the adjusted p-value column are expected for genes with very low counts across all samples — DESeq2 excludes these from multiple testing correction automatically. This is not an error. It means those genes had too few counts to be reliably tested and are excluded from the analysis.

**Q: How do I know if my gProfiler result is biologically meaningful or just noise?**  
A: Look for convergence: if several independent databases (e.g., KEGG, REAC, and GO:BP) all return terms pointing to the same cellular process, that is strong evidence. A single term in one database at p = 0.04 with an intersection of 2 genes is not meaningful — do not highlight it in your Discussion. Focus on terms with intersection_size ≥ 3 and ideally ≥ 5.

**Q: The paper I read in Stage 1 proposed a specific mechanism. Should I try to find evidence for it in my results?**  
A: Yes — and this is one of the key things your Discussion will be assessed on. Start from what the paper predicted, then look in your DEGs and pathway results for supporting or contradicting evidence. If you find it, explain why it is consistent. If you don't, discuss why it might be absent (statistical power, threshold choices, species differences, cell line differences).
