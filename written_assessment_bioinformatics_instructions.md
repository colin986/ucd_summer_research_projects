# CHEN40140 Biopharmaceutical Engineering Project
## MEngSc in Biopharmaceutical Engineering — Summer Research Project
## Written Assessment: Instructions for Students
### Format: Short Research Communication (Letter to the Editor Style)
### Bioinformatics Projects Only
### Grade Scale: Alternative Linear Conversion — 40% Pass (85% = A-)

---

## 1. Overview and Purpose

> **Grading:** This module uses the **Alternative Linear Conversion Grade Scale — 40% Pass (85% = A-)**. Your total percentage mark is converted to a UCD letter grade using this scale. Key boundaries: A- begins at 85%, B- at 70%, C- at 55%, D- (minimum pass) at 40%. Marks below 40% are fail grades. The full grade conversion table is published on the UCD Understanding Grades webpage.

> **Scope:** These instructions apply exclusively to **bioinformatics summer research projects**. All projects in this cohort are computational in nature — no wet laboratory or experimental work is performed. All data, analyses, and results described in your submission must arise from bioinformatics methods (e.g., sequence analysis, structural bioinformatics, comparative genomics, transcriptomics, pathway analysis, molecular modelling). Do not describe or claim experimental results you did not generate.

Your written submission takes the form of a **short research communication**, modelled on a Letter to the Editor as published in the journal *Biotechnology and Bioengineering*. This format is widely used in professional science and engineering to report focused, self-contained research findings concisely and clearly.

**Why a shorter format for bioinformatics projects?** Students on the experimental cohort submit a full-length thesis (up to 20,000 words). The bioinformatics cohort uses a shorter format for good reasons:

- Bioinformatics analyses use established software tools and public databases, meaning the Methods section can be described concisely — you do not need to document laboratory protocols, reagent preparation, instrument calibration, or experimental optimisation steps.
- Bioinformatics projects do not involve iterative wet-lab optimisation (e.g., adjusting growth conditions, troubleshooting assays), so there is less procedural narrative to report.
- Bioinformatics projects typically do not generate negative experimental results requiring extended discussion — computational analyses either produce interpretable outputs or they do not, and the analytical choices behind this can be documented concisely.
- The shorter format requires you to be **more selective and more precise** in what you write — every sentence must earn its place. This is a harder skill than writing at length, and it is the skill most valued in professional scientific communication.

**The quality standard is not lower because the format is shorter.** Your goal is to produce a submission that is as close as possible to a manuscript suitable for submission to a peer-reviewed journal. The examiners will assess the rigour of your science, the precision of your writing, and the quality of your critical thinking with the same expectations applied to the experimental cohort. The difference is format, not standard.

**Why this project matters for your career.** Pharmaceutical and biopharmaceutical manufacturing are key sectors in the Irish and global economy, generating over 50 per cent of Irish GDP and providing excellent job opportunities for graduates. This module (CHEN40140) consolidates your taught coursework through intensive research experience, providing a route to technological publications and rounding off your year of study. The programme and its academic faculty are closely linked with the National Institute for Bioprocessing Research and Training (NIBRT), a global centre of excellence for training and research in bioprocessing. The skills you develop here — designing an analysis, interpreting results, engaging critically with published literature, and communicating findings clearly — are directly transferable to roles in biopharmaceutical R&D, process development, regulatory affairs, data science, and academic research. Employers in this sector consistently value candidates who can demonstrate independent research competence at Master's level.

Because this is a **bioinformatics project**, your submission will be assessed on the rigour and clarity of your computational analysis. Your Methods section must describe databases, software tools, and analytical pipelines rather than laboratory protocols. Your Results section will present computational outputs — sequence alignments, phylogenetic trees, predicted structures, expression profiles, statistical comparisons — rather than experimental measurements. The guidance in each section below reflects this.

---

## 2. Word Count and Scope

| Element | Requirement |
|---|---|
| **Total word count** | 2,000 – 2,500 words (excludes references, figure/table legends, and supplementary material) |
| **Main text figures/tables** | Maximum 3 (combined) |
| **Supplementary material** | Permitted; no fixed limit but must be justified — see Section 3.10 |
| **Maximum references** | 30 (main text and supplementary combined) |
| **Font** | 12pt Times New Roman |
| **Line spacing** | Single- or 1.5-spacing |
| **Margins** | 2cm left, 2cm right |
| **Page numbering** | Sequential, in footer |
| **File format** | Single PDF submitted via Brightspace (main text and supplementary in one file) |

> **Note:** Staying within the word limit is part of the assessment. Submissions that substantially exceed 2,500 words (by more than 10%) will be penalised. Concision is a scientific skill. Supplementary material does **not** count toward the word limit, but it is not a loophole — do not move essential content into supplementary to avoid the word count.

---

## 3. Structure of the Submission

Your document must follow this structure in order. Each section is described below.

---

### 3.1 Title

Write a single, informative title that tells the reader:
- **What** was studied
- **How** it was studied (bioinformatics approach used)
- **What was found** (if space allows)

**Good example:**
*Comparative genomic analysis of lipase-encoding genes across industrially relevant Bacillus species using whole-genome sequence data*

**Poor example:**
*My project on bacteria and enzymes*

---

### 3.2 Author Line and Affiliation

List your name as sole author, followed by your supervisor's name (include their name exactly as they provide it to you). Affiliation: School of Chemical and Bioprocess Engineering, UCD College of Engineering and Architecture, University College Dublin, Belfield, Dublin 4, Ireland.

---

### 3.3 Abstract (200 words maximum)

The abstract must be **unstructured** (a single paragraph, no sub-headings) and must cover:
1. The biological/engineering **problem or knowledge gap** you addressed
2. The **approach** you used (databases, tools, methods)
3. The **key finding(s)**
4. The **significance** of the finding

The abstract is often the only part of your paper a busy reader will read. Make every sentence count. Do not use abbreviations in the abstract unless they are universally known (e.g., DNA, RNA).

---

### 3.4 Keywords

Provide 4–6 keywords beneath the abstract, separated by semicolons. Choose terms that accurately describe your topic and that someone searching a database would use.

---

### 3.5 Introduction (approximately 400–500 words)

The introduction answers three questions:

1. **Why does this matter?** Briefly establish the biological or engineering context. What is the problem, and why is it important in biopharmaceutical engineering?
2. **What is already known?** Summarise the relevant literature concisely. Cite primary sources — original research articles — not just textbooks or Wikipedia.
3. **What gap does your work address?** State clearly what question your project sets out to answer. The last 1–2 sentences of the introduction should state the **specific aim** of this study.

Keep the introduction focused. You are not writing a review article. A common mistake is an introduction that is too broad or that spends too long on background that is not directly relevant to your specific question.

---

### 3.6 Methods (approximately 300–400 words)

Describe what you did in sufficient detail that a competent reader could reproduce your analysis. Include:

- **Data sources**: Name the databases used (e.g., NCBI, UniProt, PDB, Ensembl), the specific datasets retrieved, accession numbers where relevant, and the date of access.
- **Software and tools**: Name all bioinformatics tools used (e.g., BLAST, MUSCLE, IQ-TREE, PyMOL, R packages). State the version where possible.
- **Analytical pipeline**: Describe the steps of your analysis in logical order. Use past tense passive voice (e.g., "Sequences were aligned using…").
- **Parameters and thresholds**: State any key parameters you set (e.g., E-value cut-offs, bootstrap replicates, significance thresholds).

Do not describe the general background of a tool (e.g., what BLAST is); describe only what you did with it.

---

### 3.7 Results (approximately 500–600 words)

Present your findings clearly and in a logical order that builds toward your conclusion. Key guidance:

- **Lead with the finding, not the method.** Each paragraph should open with what you found, not what you did.
- **Reference every figure and table in the text.** Do not include a figure that is not mentioned in the Results section.
- **Be precise.** Report numbers, percentages, and statistics where they add meaning. Avoid vague language ("the results showed some differences").
- **Do not interpret** in the Results section. Save your interpretation for the Discussion.

**Figures and tables:**
- Figures and tables must each have a self-contained legend/title — a reader should be able to understand a figure without reading the main text.
- Use high-resolution images (minimum 300 dpi for figures).
- Colour figures are acceptable; however, ensure they are interpretable in greyscale for accessibility.

**Main text vs. supplementary — deciding what goes where:**
A critical skill in scientific writing is deciding which results belong in the main text and which belong in supplementary material. Ask yourself: *Is this finding central to answering my research question?* If yes, it belongs in the main text. If it is supporting evidence, a full data list, or a secondary analysis that the reader might want to verify but does not need to follow your argument, it belongs in supplementary.

Common examples in bioinformatics:
- A phylogenetic tree showing your key evolutionary finding → **main text**
- The full alignment of 200 sequences used to build that tree → **supplementary**
- A table of the 5–10 top-scoring hits from a BLAST search, discussed in your argument → **main text**
- The complete BLAST output listing all 500 hits → **supplementary**
- A heatmap of your core differential expression results → **main text**
- The full annotated gene list with fold-changes for every gene → **supplementary**

Every supplementary figure or table **must be explicitly cited in the main text** (e.g., "The full list of 312 candidate genes is provided in Supplementary Table S1"). Never include supplementary material that is not mentioned in the main text — if it is not worth citing, it is not worth including. See Section 3.10 for full supplementary guidance.

---

### 3.8 Discussion (approximately 400–500 words)

This is the most intellectually demanding section. It must:

1. **Interpret your key findings** in the context of your research question. What do your results mean?
2. **Relate your findings to the existing literature.** Do your results support, contradict, or extend what others have found? Cite relevant papers.
3. **Acknowledge limitations honestly.** Every computational study has limitations (e.g., quality of databases, assumptions of tools used, scope of analysis). Acknowledging these shows scientific maturity, not weakness.
4. **State your conclusions clearly.** End with 2–3 sentences that directly answer the research question stated in your Introduction. What have you contributed?

Avoid simply restating the Results. The Discussion is where you demonstrate your ability to think critically about your own data.

---

### 3.9 References

- Use **Harvard** citation style throughout, as required by the UCD School of Chemical and Bioprocess Engineering Research Project Guidelines. See the UCD Library Harvard Style Guide for full guidance (available via the UCD Library website).
- Cite sources in the text using the author-date format: (Altschul et al., 1990).
- Include only sources you have read and cited directly.
- All references must be traceable (include DOI or URL for online sources where available).
- Wikipedia, course notes, and non-peer-reviewed websites are **not** acceptable as references in the main text (though they may help you find primary sources).
- If you have been permitted to use a Generative AI tool and cite its output, follow the UCD Library guidance on citing AI-generated content (available via the UCD Library Harvard Style Guide).

**Example Harvard format (reference list):**
> Altschul, S.F., Gish, W., Miller, W., Myers, E.W. and Lipman, D.J. (1990) 'Basic local alignment search tool', *Journal of Molecular Biology*, 215(3), pp. 403–410. doi:10.1016/S0022-2836(05)80360-2.

---

### 3.10 Supplementary Material

Supplementary material allows you to provide complete data and supporting analyses without disrupting the flow of the main text. Used well, it strengthens a submission by showing rigour. Used poorly, it creates an unnavigable document. The following rules apply.

**What belongs in supplementary:**

| Content type | Examples |
|---|---|
| Complete gene/protein lists | Full BLAST hit tables; all DEGs from an RNA-seq analysis; complete list of orthologs identified |
| Extended data tables | Full sequence metadata; all accession numbers retrieved; complete GO term enrichment tables |
| Supporting analyses | Additional phylogenetic trees using alternative methods; sensitivity analyses; full pairwise identity matrices |
| Raw pipeline outputs | Complete multiple sequence alignment files (if relevant for reproducibility) |
| Additional figures | Plots that support but do not drive the main argument |

**What must stay in the main text:**
Any finding that is essential to understanding your conclusions. If removing a figure or table would leave your argument unsubstantiated, it belongs in the main text, not in supplementary.

**Mandatory rules for supplementary material:**

1. **Every supplementary item must be cited in the main text.** Use the format "Supplementary Figure S1" or "Supplementary Table S1" in the body of your paper. If you do not cite it in the main text, remove it.
2. **Number supplementary figures and tables separately** from main-text figures and tables: Figure S1, Figure S2, Table S1, Table S2, etc.
3. **Each supplementary item must have a descriptive title or legend** — the same standard that applies to main-text figures and tables. A reader should understand what the supplementary item shows without having to search the main text.
4. **Supplementary tables containing long gene lists** must have clear column headers. Include at minimum: gene/protein identifier, gene name or description, and the key metric (e.g., BLAST E-value, fold-change, identity %). Do not paste raw output without formatting.
5. **Supplementary material does not count toward the 3 figure/table limit** or the 2,000–2,500 word count. However, it does count toward the References limit (30 total).
6. **Place supplementary material at the end of your PDF**, after the references, clearly labelled with the heading "Supplementary Material".

**How to decide: a practical test**

Before placing any item in supplementary, ask:
- *Does my Discussion refer to this data directly?* → Main text
- *Would a reviewer want to check this to verify my methods?* → Supplementary
- *Is this a full list that I summarise in the main text?* → Supplementary (with main-text summary)
- *Is this a secondary finding that does not affect my conclusions?* → Supplementary (or omit)
- *Did I include this just to show I did a lot of work?* → Omit it

More is not better. A focused, well-argued submission with three excellent main-text figures and two concise supplementary tables is stronger than a submission with twelve supplementary items and a thin main text.

---

## 4. Academic Integrity

Your submission must be your own original work. You must comply with UCD's **Academic Integrity Policy** (effective 1 September 2024), which replaced the previous Student Plagiarism Policy and now covers a wider range of academic misconduct. The accompanying **Student Academic Misconduct Procedure** (also effective 1 September 2024) sets out how cases are investigated and resolved. Both documents are available via the UCD Governance Document Library.

Key points:

- **Plagiarism** — submitting another person's work, or unattributed material, as your own — is a serious academic offence dealt with under the Academic Integrity Policy and Student Academic Misconduct Procedure.
- **Generative AI tools**: There is no university-wide blanket rule permitting or prohibiting Generative AI (GenAI) tools such as ChatGPT, Copilot, or Gemini. **Permission is at the discretion of the module coordinator.** Check the module descriptor or Brightspace for a specific statement on GenAI use for this assessment. If no statement is provided, ask your supervisor before using any GenAI tool. Where use is permitted, you **must explicitly acknowledge and cite** the tool used in accordance with the UCD Library Harvard Style Guide on citing AI. Submitting AI-generated content as your own work without permission and acknowledgement constitutes academic misconduct. Note also that entering personal or confidential data into AI tools not approved by UCD may constitute a GDPR breach.
- **Self-plagiarism**: You may not resubmit work, or substantial portions of work, that you have submitted for another module.
- **Turnitin**: Your submission will be processed through Turnitin, UCD's university-wide originality and similarity detection platform integrated into Brightspace. A similarity report will be reviewed by your supervisor. Turnitin also generates an AI writing detection report; this will be used as one input, alongside other evidence, in any assessment of suspected misconduct — it is not used as sole evidence.
- **Research integrity**: All data and results reported must be genuine. Fabrication or falsification of computational results, including misrepresentation of bioinformatics outputs, is research misconduct.

If you are unsure whether a particular use of a source or tool is acceptable, contact your supervisor **before** submitting.

---

## 5. Submission

- Submit via **Brightspace** by the published deadline. Submission is not confirmed until you receive a Brightspace confirmation email — retain this.
- **Late submission penalties** are applied in accordance with UCD's Late Submission of Coursework Policy (approved January 2020, currently in force):
  - Up to and including **5 working days** late: grade reduced by **one grade point** (e.g., B → B-)
  - More than 5 but up to and including **10 working days** late: grade reduced by **two grade points** (e.g., B → C+)
  - More than **10 working days** late: work **not accepted** and a grade of FM (fail) recorded, unless Extenuating Circumstances have been formally approved in advance
  - Extensions of up to 10 working days may be granted; requests beyond 10 working days must go through the Extenuating Circumstances process
  - *Note: A new Additional Considerations for Assessment policy has been approved by Academic Council (October 2024) and will replace the current late submission and extenuating circumstances policies from September 2026.*
- Retain a copy of all your data, scripts, and analysis files — you may be asked to provide these.
- File naming convention: `StudentNumber_SummerProject_Written.pdf`

---

## 6. Practical Advice

The following guidance is offered to help you produce a strong submission, regardless of your prior writing experience.

**Start early.** Good scientific writing takes multiple drafts. Allow time to step away from a draft and return to it with fresh eyes.

**Write for your reader.** Your reader is a technically literate engineer or scientist, but not necessarily a specialist in your precise topic. Avoid unexplained jargon. Define acronyms on first use.

**Be specific and precise.** Vague language ("the analysis showed interesting results") is one of the most common weaknesses in student scientific writing. Always ask: "What exactly did I find?"

**Figures are powerful.** A well-designed figure can convey information more efficiently than several paragraphs of text. Invest time in clear, well-labelled figures.

**Use the literature actively.** The strongest submissions do not just cite a paper — they explain what it found and how that relates to your work.

**Seek feedback.** Share a draft with your supervisor before the deadline. Their feedback is part of the learning process.

**Read the journal.** Before writing, read one or two actual Letters to the Editor published in *Biotechnology and Bioengineering*. Understanding the genre you are writing in is the single most effective preparation.

---

*CHEN40140 Biopharmaceutical Engineering Project — Module Coordinator: Dr Colin Clarke, School of Chemical and Bioprocess Engineering, UCD College of Engineering and Architecture, University College Dublin. All assessments are conducted in accordance with UCD Academic Regulations.*
