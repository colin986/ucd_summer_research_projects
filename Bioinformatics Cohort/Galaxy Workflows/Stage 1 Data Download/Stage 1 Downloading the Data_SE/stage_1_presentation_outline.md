# Stage 1: Downloading the Data
## Presentation Outline — Instructor Delivery Guide

---

**Audience:** MEngSc Biopharmaceutical Engineering students (bioinformatics cohort)
**Duration:** Approximately 20–30 minutes (adjust to group size and questions)
**Delivery format:** In-class session with live demonstration on usegalaxy.eu
**Pre-requisites:** Students should have their assigned publication and know their GEO/SRA accession numbers

---

## Slide 1: Title

- **Stage 1 — Downloading Your Data**
- MEngSc Summer Research Project — Bioinformatics Cohort
- [Date]

---

## Slide 2: Where We Are in the Pipeline

- Show a simple high-level pipeline graphic:
  **Download Data** → QC → Trimming → Alignment → Quantification → Differential Expression
- Highlight Stage 1 — emphasise that everything downstream depends on getting this step right
- "Today's objective: get your raw FASTQ files from NCBI SRA into Galaxy, verified and ready for quality control"

---

## Slide 3: What Are We Downloading and Where Does It Come From?

**Key points to cover:**
- NCBI SRA (Sequence Read Archive) — the public repository where raw sequencing data from published studies is deposited
- Every published RNA-seq study is required to deposit its raw data — this is what enables re-analysis
- Your project: re-analysing a published dataset with updated genome assemblies or newer bioinformatics tools — this is a legitimate and common form of research
- FASTQ format — the standard file format for raw sequencing reads (sequence + quality scores per read)

**Discussion prompt:** "Can anyone tell me what the four lines of a FASTQ record represent?"

---

## Slide 4: Single-End vs. Paired-End Reads

**Key points to cover:**
- SE: one read per fragment → one FASTQ file per sample
- PE: two reads per fragment (forward + reverse) → two FASTQ files per sample
- Implications: PE gives more information (spanning longer fragments, better for detecting structural variants, splice junctions) but at higher cost and more data to process
- **This workflow is for single-end data.** Students with PE data will be flagged — stress the importance of checking this before running
- Where to check: SRA Run Selector → "Library Layout" column

**Visual:** Diagram showing a DNA fragment with arrows indicating SE (one direction) vs PE (both directions)

---

## Slide 5: Read Length — Why It Matters Later

**Key points to cover:**
- Read length (e.g., 50bp, 75bp, 100bp, 150bp) is set at the time of sequencing and cannot be changed
- Shorter reads (50bp) are cheaper but harder to map uniquely, especially to repetitive regions
- Longer reads (150bp) map more uniquely but generate more data
- Students should note their read length now (find it on SRA, or wait for FastQC in Stage 2) — it affects trimming decisions and alignment parameters in later stages
- "You don't need to do anything with this today — just know what yours is"

---

## Slide 6: The sample_info.txt File

**Key points to cover:**
- This is the input to the workflow — a simple tab-delimited text file listing SRA accession numbers
- Show an example on screen (the one from the student guide)
- **Critical formatting points:**
  - SRA accessions must be in **column 1**
  - **Tab-delimited**, not comma-separated
  - Saving from Excel: use "Text (Tab delimited)"
  - Header row is fine — fasterq-dump will skip non-accession text

**Live demo:** Open an example `sample_info.txt` in a text editor and show the tab characters. Show what happens if you accidentally save as CSV (the workflow will fail at the Cut step).

---

## Slide 7: The Workflow — What It Does

**Key points to cover:**

Show the 3-step workflow visually (screenshot from Galaxy or a simplified diagram):

1. **Input:** Your sample_info.txt
2. **Cut tool:** Extracts column 1 (the SRA accessions) from the table — this isolates just the accession numbers for the download tool
3. **Faster Download and Extract Reads in FASTQ (fasterq-dump):** Connects to NCBI SRA, downloads the raw FASTQ data for each accession, compresses it (.fastq.gz), names the collection "Raw RNA-seq data", and tags it with `raw_data`

- "The workflow handles tagging and naming automatically — these tags are important for downstream tools like DESeq2 to correctly associate samples with conditions"
- Intermediate files are hidden to keep your history clean — you can reveal them if needed

---

## Slide 8: Live Demonstration

**Walk through the workflow on screen:**

1. **Upload** the example `sample_info.txt`
   - Show how to set the datatype to tabular
   - Verify the upload with the eye icon
2. **Import the workflow** (if students haven't already)
   - Workflow menu → Import → upload the .ga file
3. **Run the workflow**
   - Select the input file
   - Click Run Workflow
4. **Show the history updating** — explain grey/yellow/green/red states
5. "The download will take some time — I'll show you what the output looks like from a completed run"
6. **Show a completed output:**
   - Expand the "Raw RNA-seq data" collection
   - Click on one file — show the FASTQ format
   - Note the file size, number of files, tags

---

## Slide 9: Verifying Your Download

**Key checks students should perform:**

1. Correct number of files in the output collection (should match the number of accessions)
2. Non-zero file sizes (click individual files)
3. FASTQ format is correct (preview with eye icon — 4-line records starting with @)
4. Collection is tagged with `raw_data`

"If any of these checks fail, consult the troubleshooting guide before the next session"

---

## Slide 10: Common Pitfalls

| Pitfall | How to avoid |
|---|---|
| File is CSV not tab-delimited | Save from Excel as "Text (Tab delimited)" |
| Galaxy datatype is wrong (not tabular) | Manually set datatype after upload |
| PE data used with SE workflow | Check Library Layout on SRA Run Selector first |
| Job fails with "accession not found" | Verify accessions at ncbi.nlm.nih.gov/sra |
| Download very slow or times out | NCBI servers can be slow — retry at off-peak times; don't cancel and re-queue |
| Storage quota exceeded | Delete old datasets from previous test runs |

---

## Slide 11: What This Means for Your Thesis

- This stage maps to the **Methods section** of your thesis
- You need to record: tool name and version, platform used, accession numbers, what you downloaded
- Show the example methods text from the student guide
- "You should be writing your Methods as you go, not at the end — start a draft document now"

---

## Slide 12: Tasks Before Next Session

- [ ] Prepare your `sample_info.txt` with your assigned dataset's SRA accessions
- [ ] Upload it to Galaxy and run the Stage 1 workflow
- [ ] Verify the output (correct file count, non-zero sizes, FASTQ format)
- [ ] Note your read type (SE/PE), approximate read length, and organism
- [ ] Read the troubleshooting guide if you encounter any errors
- [ ] Begin drafting the Methods paragraph for this stage

---

## Slide 13: Questions

- Open floor for questions
- Remind students: troubleshooting guide is available; try to resolve issues independently first before the next session
- "Next session: Stage 2 — Quality Control with FastQC"

---

## Instructor Notes

**Before the session:**
- Have a pre-completed Galaxy history with the workflow already run successfully, so you can show the output without waiting for downloads
- Test that the workflow imports correctly on usegalaxy.eu with the current version of fasterq-dump
- Have an example `sample_info.txt` ready to distribute or display

**During the session:**
- Start the live demo download early (while talking through the slides) so the job is running/complete by the time you reach the output verification slide
- If Galaxy is slow during the session, use the pre-completed history as a backup

**After the session:**
- Confirm each student knows their assigned dataset and has correct SRA accessions
- Check in with any student whose data is PE to arrange the correct workflow

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
