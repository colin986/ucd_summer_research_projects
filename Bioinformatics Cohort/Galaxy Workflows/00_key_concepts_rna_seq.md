# Key Concepts: RNA-seq for Non-Biologists
## A Reference Guide for Students New to Transcriptomics

---

This guide explains the biological and computational concepts you will encounter during this project. It is written for students who may have **no prior background in molecular biology or bioinformatics**. Keep this document open as a reference while you work through the pipeline stages.

---

## The Big Picture: What Are We Doing and Why?

Your project involves **re-analysing a published RNA-seq dataset** using updated genome assemblies or newer bioinformatics tools. The original researchers performed experiments, sequenced the RNA, and published their analysis. You are asking: **does the same data tell us something different — or more precise — when analysed with current tools?**

This is a legitimate and common form of scientific research. Bioinformatics tools and reference genomes improve over time, and re-analysis of public data frequently reveals new insights.

---

## Part 1: The Biology

### What is a gene?

A gene is a stretch of DNA that contains the instructions for making a functional product — usually a protein. Humans have roughly 20,000 protein-coding genes. Other organisms have different numbers.

### What is transcription?

Transcription is the process of copying a gene's DNA sequence into **messenger RNA (mRNA)**. Think of DNA as the master copy stored in a vault (the nucleus) and mRNA as the working copy sent out to the factory floor (the ribosome) to build proteins.

### What is a transcriptome?

The **transcriptome** is the complete set of all mRNA molecules present in a cell or tissue at a given moment. It changes depending on the cell type, the organism's condition, the time point, and external factors (e.g., drug treatment, disease).

### What is differential expression?

**Differential expression** means a gene is transcribed at different levels under different conditions. For example, a gene might produce 1,000 mRNA copies in a normal cell but 5,000 copies in a cancer cell. The goal of most RNA-seq experiments is to identify which genes are differentially expressed between conditions.

### Why does this matter in biopharmaceutical engineering?

Biopharmaceutical manufacturing relies on living cells (e.g., CHO cells) to produce therapeutic proteins. Understanding which genes are active under different culture conditions — and how expression changes in response to process parameters like temperature, media composition, or feed strategy — is critical for optimising production processes. RNA-seq is one of the primary tools for this.

---

## Part 2: The Technology

### What is RNA-seq?

RNA-seq (RNA sequencing) is a technology that measures the transcriptome by:
1. Extracting all the mRNA from a sample
2. Converting the mRNA into complementary DNA (cDNA) — because DNA is more stable and easier to sequence
3. Breaking the cDNA into short fragments
4. **Sequencing** those fragments — reading the order of the nucleotide bases (A, T, G, C) in each fragment

The output is millions of short sequences called **reads**.

### What is a read?

A **read** is a short string of nucleotides (e.g., `ATCGATCGATCG...`) that represents one sequenced fragment. A typical RNA-seq experiment produces 10–50 million reads per sample, each 50–150 nucleotides long.

### What is read length?

The **read length** is how many nucleotides are sequenced in each read. Common lengths are 50 bp, 75 bp, 100 bp, or 150 bp (bp = base pairs). Longer reads map more uniquely but cost more to produce.

### What are single-end (SE) and paired-end (PE) reads?

- **Single-end:** Each fragment is sequenced from one end only → one read per fragment
- **Paired-end:** Each fragment is sequenced from both ends → two reads per fragment (forward and reverse)

PE data gives more information and maps more confidently, but generates twice the data volume.

### What is a FASTQ file?

A **FASTQ file** is the standard file format for sequencing data. Each read is stored as four lines:

```
@SRR1234567.1 1/1          ← Read identifier (unique name)
ATCGATCGATCGATCGATCG        ← The nucleotide sequence
+                            ← Separator line
IIIIIIIIIIIIIIIIIII          ← Quality scores (one per nucleotide)
```

The **quality score** line tells you how confident the sequencer was about each base call. Higher quality = more reliable. You will assess quality in Stage 2 (FastQC).

### What is the SRA?

The **Sequence Read Archive (SRA)** is a public database run by NCBI (National Center for Biotechnology Information) where researchers deposit their raw sequencing data when they publish a paper. Every published RNA-seq study is required to deposit data here. This is what makes re-analysis possible.

Each dataset has an **accession number** (e.g., `SRR1234567` for a single run, or `GSE12345` for a GEO Series). You use these accession numbers to download the data.

---

## Part 3: The Analysis Pipeline

Your project follows a standard RNA-seq analysis pipeline. Each stage corresponds to one or more Galaxy workflows.

| Stage | What it does | Analogy |
|---|---|---|
| **1. Download data** | Retrieve raw FASTQ files from SRA | Receiving the raw materials |
| **2. Quality control (QC)** | Assess the quality of the raw reads using FastQC | Inspecting the raw materials for defects |
| **3. Trimming** | Remove low-quality bases and adapter sequences from reads | Cleaning and preparing the materials |
| **4. Alignment** | Map each read to its position in the reference genome | Sorting parts into the right locations in a blueprint |
| **5. Quantification** | Count how many reads map to each gene | Counting how many of each part you have |
| **6. Differential expression** | Statistically test which genes differ between conditions | Identifying which parts differ between two batches |

You do not need to understand every detail of every stage before you start. Each stage guide explains the relevant concepts. But knowing where you are in this pipeline — and what the output of each stage feeds into — will help you understand why each step matters.

### What is a reference genome?

A **reference genome** is the "official" assembled sequence of an organism's DNA. When you align reads, you are mapping each short read back to the reference genome to determine which gene it came from.

Reference genomes are updated over time as sequencing and assembly improve. One of the reasons for re-analysis is that a **newer genome assembly** may change how reads are mapped, potentially affecting which genes appear differentially expressed.

### What is a genome assembly version?

Genome assemblies are released as versions (e.g., GRCh38 for human, GRCm39 for mouse, CriGri-PICRH-1.0 for CHO). Newer versions typically have:
- Fewer gaps and errors
- Better annotation of genes
- More accurate gene coordinates

Using a newer assembly is one of the key reasons your re-analysis may produce different results from the original publication.

### What is a gene annotation?

A **gene annotation** (often in GTF or GFF format) is a file that tells you where each gene starts and ends on the reference genome, and what it is called. The quantification step uses this to assign reads to genes.

---

## Glossary of Terms You Will Encounter

| Term | Definition |
|---|---|
| **Accession number** | A unique identifier for a dataset in a public database (e.g., SRR1234567 in SRA, GSE12345 in GEO) |
| **Adapter** | Short synthetic DNA sequences attached during library preparation; must be trimmed before analysis |
| **Alignment / Mapping** | The process of determining where in the genome each read originated |
| **Base pair (bp)** | A unit of length for DNA/RNA sequences (one nucleotide) |
| **cDNA** | Complementary DNA — a DNA copy of mRNA, used in sequencing because it is more stable |
| **CHO cells** | Chinese Hamster Ovary cells — the most common mammalian cell line for biopharmaceutical production |
| **Collection** | A Galaxy feature that groups related datasets together (e.g., all FASTQ files for one experiment) |
| **Counts / Read counts** | The number of reads that map to a particular gene — the primary measurement in RNA-seq |
| **Dataset** | Any file in your Galaxy history (uploaded or tool-generated) |
| **DEG** | Differentially Expressed Gene — a gene with statistically significant expression differences between conditions |
| **DESeq2** | A widely-used statistical tool for identifying differentially expressed genes |
| **FASTQ** | The standard file format for raw sequencing reads (sequence + quality scores) |
| **FastQC** | A quality control tool that summarises the quality of a FASTQ file |
| **GEO** | Gene Expression Omnibus — an NCBI database where processed expression data and metadata are deposited |
| **Genome assembly** | The compiled reference sequence of an organism's genome |
| **GFF / GTF** | File formats for gene annotation (where genes are located on the genome) |
| **History** | Galaxy's record of your work — every dataset and analysis step is stored here |
| **Library** | In sequencing, a library is the collection of cDNA fragments prepared for sequencing |
| **Mapping rate** | The percentage of reads that successfully align to the reference genome |
| **mRNA** | Messenger RNA — the molecule transcribed from a gene that carries the protein-building instructions |
| **Normalisation** | Mathematical adjustment of read counts to account for differences in sequencing depth between samples |
| **Paired-end (PE)** | Sequencing from both ends of a fragment, producing two reads per fragment |
| **Phred score** | The standard quality score for each base call (higher = more confident; Q30 = 99.9% accuracy) |
| **Pipeline** | A sequence of analysis steps applied to data in order |
| **Read** | A short nucleotide sequence produced by the sequencer |
| **Reference genome** | The standard assembled genome sequence for an organism |
| **Replicate** | An independent repeat of an experimental condition (biological replicates are essential for statistical analysis) |
| **RNA-seq** | A sequencing-based method for measuring the transcriptome |
| **Single-end (SE)** | Sequencing from one end of a fragment only |
| **SRA** | Sequence Read Archive — the NCBI database for raw sequencing data |
| **Strandedness** | Whether the sequencing protocol preserved information about which DNA strand the RNA was transcribed from |
| **Tag** | A label applied to a Galaxy dataset for downstream tool compatibility |
| **Transcriptome** | The complete set of mRNA molecules in a cell or tissue at a given time |
| **Trimming** | Removing low-quality bases or adapter sequences from reads |
| **Workflow** | A pre-built sequence of Galaxy tools that runs automatically |

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
