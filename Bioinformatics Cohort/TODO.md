# CHEN40140 Bioinformatics Cohort — TODO List
## Dr Colin Clarke

*Last updated: 2 April 2026*

---

### Data Availability — URGENT (7 projects affected)

- [ ] **Projects 40, 41 (Orellana et al. 2018)** — No public SRA/GEO accession found. Contact corresponding author at University of Queensland to request data access. Alternative: check if data is in the Singh et al. 2018 CHO RNA-seq compendium (doi: 10.1002/biot.201800070).

- [ ] **Projects 43, 44, 45, 46 (Barzadd et al. 2022)** — No public accession found. Industry involvement (AstraZeneca/KTH). Contact corresponding author to request data or confirm if proprietary. If data unavailable, identify alternative CHO bispecific antibody RNA-seq datasets.

- [ ] **Project 47 (Thalen et al. 2024)** — No public accession found. Industry involvement (SOBI AB/KTH). Contact corresponding author. If data unavailable, identify alternative sulfatase-producing CHO RNA-seq dataset.

- [ ] **Projects 32, 38 (Tanemura et al. 2022)** — GEO accession GSE197570 exists but SE/PE layout not confirmed. Check SRA Run Selector to determine library layout and assign correct Galaxy workflow (SE or PE).

---

### Input File Format — Standardisation

- [ ] **Unify input file format across all workflows to either CSV or TSV.** Currently Stage 1 SE uses tab-delimited (`sample_info.txt`) and Stage 1 PE uses comma-separated (`sample_names.txt`). Pick one format and apply it consistently across all stages and workflows — update workflow `.ga` files (Cut tool delimiter), student guides, and sample file templates accordingly.

---

### Workflow Assignment

- [ ] Confirm SE/PE for Tanemura (GSE197570) and update project_data_reference.md
- [ ] Prepare sample_info.txt / sample_names.txt files for each project with correct SRA accessions — to distribute to students at project allocation
- [ ] For projects using DDBJ (Tsunoda, PRJDB35724) and ENA (Nguyen, PRJEB33024) accessions, verify these are downloadable via fasterq-dump on usegalaxy.eu (DDBJ/ENA accessions sometimes require different handling)

---

### Special Handling

- [ ] **Vito et al. (Projects 33, 34, 35)** — rRNA depletion, not polyA. Prepare a note for students on how this affects their analysis (more non-coding reads, different QC expectations in FastQC, potential impact on quantification)
- [ ] **Tsunoda et al. (Project 36)** — Two different kits and read lengths across sample groups. Prepare batch effect guidance for the student

---

### Galaxy Workflows — Remaining Stages to Build

- [ ] Stage 2: Quality Control (FastQC / MultiQC)
- [ ] Stage 3: Trimming (Trimmomatic / fastp)
- [ ] Stage 4: Alignment (HISAT2 / STAR) — will need reference genome for Chinese hamster (CriGri)
- [ ] Stage 5: Quantification (featureCounts / HTSeq)
- [ ] Stage 6: Differential Expression (DESeq2)
- [ ] For each stage: create student guide, troubleshooting guide, and presentation outline (as per Stage 1 template)

---

### PDF Conversion (Incomplete from Previous Session)

- [ ] Resolve remaining LaTeX dependency issues (tikzfill.image.sty was blocking)
- [ ] Generate test PDF and get feedback
- [ ] Convert all assessment documents and Galaxy guides to PDF for student distribution

---

### Assessment Documents

- [ ] Review footer text across all documents — some files have been updated with "Dr Colin Clarke, School of Chemical and Bioprocess Engineering" and some may still have older footer text. Ensure consistency.
- [ ] Confirm assessment weighting matches project list PDF: Written 90%, Oral 10% — current rubrics weight these independently (each out of 100). May need to add a note clarifying how the two marks combine.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
