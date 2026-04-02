# Stage 1: Downloading the Data (Paired-End)
## Presentation Outline — Instructor Delivery Guide

---

**Audience:** MEngSc Biopharmaceutical Engineering students (bioinformatics cohort — PE data)
**Duration:** Approximately 20–30 minutes
**Delivery format:** In-class session with live demonstration on usegalaxy.eu
**Pre-requisites:** Students should have their assigned publication and know their GEO/SRA accession numbers. Students should have confirmed their data is paired-end via the SRA Run Selector.

---

## Slide 1: Title

- **Stage 1 — Downloading Your Data (Paired-End)**
- MEngSc Summer Research Project — Bioinformatics Cohort
- [Date]

---

## Slide 2: Where We Are in the Pipeline

- Pipeline graphic:
  **Download Data** → QC → Trimming → Alignment → Quantification → Differential Expression
- Highlight Stage 1
- "Today: get your paired-end FASTQ files from NCBI SRA into Galaxy, verified and ready for QC"

---

## Slide 3: Quick Recap — SE vs PE

**Key points to cover:**
- SE: one read per fragment → one file per sample
- PE: two reads per fragment → **two files per sample** (forward _1 and reverse _2)
- PE advantages: better mapping confidence, spans longer fragments, better splice junction detection
- PE considerations: double the data volume, both files must stay correctly paired throughout analysis
- "If you ran the SE workflow already and your data is actually PE, you need to re-run with this workflow"

**Visual:** Diagram of a fragment with forward and reverse reads, showing how they map to opposite ends

---

## Slide 4: Key Differences from the SE Workflow

| Feature | SE Workflow | PE Workflow |
|---|---|---|
| Input file format | Tab-delimited (`sample_info.txt`) | **Comma-separated** (`sample_names.txt`) |
| Output structure | One FASTQ per sample | **Paired collection** (forward + reverse per sample) |
| Tagged outputs | `output_collection` tagged `raw_data` | Both `list_paired` and `output_collection` tagged `raw_data` |
| Data volume | ~X GB | **~2X GB** |
| Download time | Moderate | **Longer** |

- "The most common mistake is using the wrong file format — **CSV for PE, tab-delimited for SE**. Get this wrong and the Cut tool will fail."

---

## Slide 5: The sample_names.txt File

**Key points:**
- Comma-separated, SRA accessions in column 1
- Show an example on screen
- **Stress the difference from SE**: commas, not tabs
- Show what a correctly formatted file looks like in a plain text editor

**Live demo option:** Open a sample file in TextEdit/Notepad, show the commas. Then show what a tab-delimited file looks like for comparison.

---

## Slide 6: The Workflow — What It Does

Show the 3-step workflow:

1. **Input:** Your `sample_names.txt` (CSV)
2. **Cut tool:** Extracts column 1 using comma delimiter — isolates the SRA accessions
3. **Faster Download and Extract Reads in FASTQ (fasterq-dump v3.1.1):** Downloads from NCBI SRA, splits PE reads with `--split-3` into forward/reverse pairs, stores in paired collection, tags with `raw_data`, types as `fastq.gz`

- "The `--split-3` flag is the key: it tells fasterq-dump to separate forward and reverse reads into distinct files. If your data is genuinely PE, you get paired collections. If some reads are orphaned (unpaired), they go into a separate collection."

---

## Slide 7: Understanding Paired Collections in Galaxy

**Key points:**
- Galaxy stores PE data in **paired dataset collections** — a structured list where each entry contains a forward and reverse file
- This paired structure is maintained through all downstream tools (FastQC, trimming, alignment)
- If the pairing is broken, downstream analysis will fail
- "When you expand a paired collection, you see sample names. Click into a sample and you see the forward and reverse files."

**Visual:** Screenshot of an expanded paired collection in Galaxy showing the forward/reverse structure

---

## Slide 8: Live Demonstration

**Walk through on screen:**

1. **Upload** the example `sample_names.txt` — show it is comma-separated
2. **Import the PE workflow** (if students haven't already)
3. **Run the workflow** — select input, click Run
4. **Show the history updating** — grey/yellow/green/red states
5. "PE downloads take longer — I'll show completed output"
6. **Show completed output:**
   - Expand the paired collection
   - Click into one sample — show forward and reverse files
   - Preview one file — show FASTQ format
   - Point out the read direction indicator in the headers (`/1` vs `/2`)
   - Note file sizes (PE files per sample are individually similar to SE, but there are two of them)

---

## Slide 9: Verifying Your Download

**Key checks:**

1. **Correct number of paired entries** in the collection (should match number of accessions)
2. **Each entry has both forward and reverse files** — neither should be missing or empty
3. **Non-zero file sizes** for both files in each pair
4. **FASTQ format** is correct (preview with eye icon)
5. **Collection is tagged** with `raw_data`
6. **No unexpected single-end output** — if the `output_collection` (non-paired) has files, some reads may be unpaired; check the `list_paired` collection first

---

## Slide 10: Common Pitfalls (PE-Specific)

| Pitfall | How to avoid |
|---|---|
| File is tab-delimited instead of CSV | Save from Excel as "CSV (Comma delimited)" — this is the most common PE workflow error |
| Data is actually SE, not PE | Check "Library Layout" on SRA Run Selector before running |
| Download very slow or times out | PE data is ~2x larger — allow more time; retry at off-peak times |
| Storage quota exceeded | More likely with PE — delete old datasets proactively |
| Paired structure missing in output | Check `list_paired` vs `output_collection` — if all data is in `output_collection`, the SRA record may store PE data in a non-standard way; discuss with supervisor |
| One direction missing for some samples | SRA record may be corrupted — check the specific accession on NCBI |

---

## Slide 11: What to Note for Later Stages

| Feature | How to find | Why it matters |
|---|---|---|
| Number of samples | Count entries in paired collection | Compute time and storage |
| Read length | Preview FASTQ or wait for FastQC | Trimming and alignment settings |
| Insert size | SRA metadata or estimate post-alignment | Some aligners use this information |
| Strandedness | Publication methods section | Affects quantification tool settings |

- "PE data gives you extra information to record — particularly insert size and strandedness. Note what you can find now; you'll confirm the rest in later stages."

---

## Slide 12: Thesis Methods Paragraph

Show the template from the student guide:

> "Raw paired-end RNA-seq data were downloaded from the NCBI Sequence Read Archive (SRA) using the Faster Download and Extract Reads in FASTQ tool (fasterq-dump v3.1.1) on the Galaxy platform (usegalaxy.eu). Paired-end FASTQ files for [n] samples ([read length]-bp reads) were retrieved using accession numbers obtained from GEO Series [GSE number] (Author et al., Year). Forward and reverse reads were split using the --split-3 option and stored in compressed FASTQ format (.fastq.gz)."

- "Start writing your Methods now — adapt this template, don't copy it."

---

## Slide 13: Tasks Before Next Session

- [ ] Prepare your `sample_names.txt` (comma-separated) with your assigned dataset's SRA accessions
- [ ] Confirm your data is PE on the SRA Run Selector
- [ ] Upload to Galaxy and run the Stage 1 PE workflow
- [ ] Verify: correct number of paired entries, both forward and reverse present, non-zero sizes, FASTQ format
- [ ] Note read type (PE), approximate read length, organism, and any strandedness information
- [ ] Read the troubleshooting guide if you encounter errors
- [ ] Begin drafting the Methods paragraph for this stage

---

## Slide 14: Questions

- Open floor
- Remind students: troubleshooting guide is available; try resolving issues independently first
- "Next session: Stage 2 — Quality Control with FastQC"

---

## Instructor Notes

**Before the session:**
- Have a pre-completed Galaxy history with PE workflow already run, showing the paired collection structure
- Test that the PE workflow imports and runs correctly on usegalaxy.eu
- Have an example `sample_names.txt` (CSV format) ready
- If delivering this session alongside the SE session, have both examples ready to show the formatting difference

**During the session:**
- Start the live demo download early so the job is running/complete by the verification slide
- Use the pre-completed history as backup if Galaxy is slow
- Emphasise the CSV vs tab-delimited difference — this is the most common error for PE students

**After the session:**
- Confirm each student knows their data is PE and has the correct accessions
- Flag any student whose data turns out to be SE for the other workflow

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
