# CHEN40140 Bioinformatics Projects — Data Reference Table
## RNA-seq Library Preparation and Sequencing Metadata

*Last updated: 1 April 2026*
*Module Coordinator: Dr Colin Clarke, School of Chemical and Bioprocess Engineering, UCD*

---

## Summary

| Proj | Paper | Accession | Library Selection | Kit | Read Length | SE/PE | Reads/Sample | Platform | Data Public? |
|---|---|---|---|---|---|---|---|---|---|
| 28, 29 | Muluhngwi et al. 2017 | GSE85710 | PolyA | TruSeq | 75 bp | SE | Not stated | NextSeq 500 | Yes |
| 30, 31 | Rives et al. 2024 | GSE217637 | Likely polyA | Novogene (kit not specified) | ~150 bp | PE | ~26–33M | NovaSeq 6000 | Yes |
| 32, 38 | Tanemura et al. 2022 | GSE197570 | PolyA | TruSeq RNA v2 | Not stated | Not stated | Not stated | HiSeq 2000 | Yes |
| 33, 34, 35 | Vito et al. 2020 | GSE140671 | rRNA depletion | Not stated | ~150 bp | SE | ~15–40M | HiSeq 4000 | Yes |
| 36 | Tsunoda et al. 2025 | PRJDB35724 (DDBJ) | PolyA (stranded) | KAPA Stranded mRNA / TruSeq Stranded mRNA LT | 76 bp / 101 bp | PE | Not stated | NextSeq 500 / NovaSeq X | Yes |
| 37 | Nguyen et al. 2020 | PRJEB33024 (ENA) | Not stated | VBCF (kit not specified) | ~100 bp | SE | ~22–26M | HiSeq 2500 | Yes |
| 39 | Hefzi et al. 2016 | GSE77800 | PolyA (stranded) | TruSeq Stranded mRNA | 2x101 bp | PE | Not stated | HiSeq 2500 | Yes |
| 40, 41 | Orellana et al. 2018 | NOT FOUND | Not stated | Not stated | 100 bp | PE | >100M | HiSeq 2000 | NO |
| 42 | Tzani et al. 2020 | PRJNA593052 | Not stated | Not stated | ~2x150 bp | PE | 50–77M | NextSeq 500 | Yes |
| 43, 44, 45, 46 | Barzadd et al. 2022 | NOT FOUND | Not stated | Not stated | Not stated | Not stated | Not stated | Not stated | NO |
| 47 | Thalen et al. 2024 | NOT FOUND | Not stated | Not stated | Not stated | Not stated | Not stated | Not stated | NO |

---

## Workflow Assignment (SE vs PE)

| Workflow | Projects |
|---|---|
| **SE workflow** (Stage 1 SE) | 28, 29, 33, 34, 35, 37 |
| **PE workflow** (Stage 1 PE) | 30, 31, 36, 39, 42 |
| **Needs confirmation on SRA** | 32, 38 (Tanemura — accession GSE197570, layout not stated in paper) |
| **Data not publicly available** | 40, 41 (Orellana), 43–46 (Barzadd), 47 (Thalen) |

---

## Issues and Notes

### Missing Public Data (7 projects affected)

**Orellana et al. 2018 (Projects 40, 41)**
- No GEO/SRA accession found despite extensive searching
- Paper: Biotechnol J 13:e1700231. DOI: 10.1002/biot.201700231. PMID: 29316330
- Data may be part of the Singh et al. 2018 CHO RNA-seq compendium (doi: 10.1002/biot.201800070)
- Action: Contact corresponding author at University of Queensland, or check DTU CHO compendium

**Barzadd et al. 2022 (Projects 43, 44, 45, 46)**
- No GEO/SRA/ArrayExpress accession found
- Paper: N Biotechnol 68:68-76. DOI: 10.1016/j.nbt.2022.01.010. PMID: 35123066
- Industry involvement (AstraZeneca) — data may be proprietary
- Action: Contact corresponding author at KTH Royal Institute of Technology

**Thalen et al. 2024 (Project 47)**
- No GEO/SRA/ArrayExpress accession found
- Paper: Metab Eng 81:157-166. DOI: 10.1016/j.ymben.2023.12.003. PMID: 38081506
- Industry involvement (SOBI AB, KTH) — data may be proprietary
- Action: Contact corresponding author

### Special Considerations

**Vito et al. 2020 (Projects 33, 34, 35) — rRNA depletion**
- This is the only dataset using rRNA depletion rather than polyA selection
- The study targeted lncRNAs, hence the choice of library prep
- Students should be aware this affects downstream analysis (more intronic/intergenic reads, different quantification considerations)
- GEO SuperSeries GSE140671 comprises GSE140668 (mRNA subseries) and GSE140669 (lncRNA/ncRNA subseries)

**Tsunoda et al. 2025 (Project 36) — Mixed platforms and read lengths**
- Two different library kits: KAPA Stranded mRNA (parental cells) and TruSeq Stranded mRNA LT (IgG clones)
- Two different read lengths: 76 bp PE (NextSeq 500) and 101 bp PE (NovaSeq X)
- Students must account for potential batch effects from different platforms and read lengths
- Data deposited in DDBJ (Japanese archive), not GEO — accession PRJDB35724

**Tanemura et al. 2022 (Projects 32, 38) — Incomplete metadata**
- GEO accession GSE197570 exists but library layout (SE/PE) not stated in paper
- Need to check SRA Run Selector directly to confirm before assigning SE or PE workflow

---

## Full Publication Details

### 1. Muluhngwi et al. 2017 (Projects 28, 29)
Muluhngwi P, Richardson K, Napier J, Rouchka EC, Mott JL, Klinge CM. Regulation of miR-29b-1/a transcription and identification of target mRNAs in CHO-K1 cells. *Mol Cell Endocrinol.* 2017 Mar 15;444:38-47. doi: 10.1016/j.mce.2017.01.044. PMID: 28137615; PMCID: PMC5316361.

### 2. Rives et al. 2024 (Projects 30, 31)
Rives D, Peak C, Blenner MA. RNASeq highlights ATF6 pathway regulators for CHO cell engineering with different impacts of ATF6beta and WFS1 knockdown on fed-batch production of IgG1. *Sci Rep.* 2024 Jun 19;14(1):14141. doi: 10.1038/s41598-024-64767-1. PMID: 38898154; PMCID: PMC11187196.

### 3. Tanemura et al. 2022 (Projects 32, 38)
Tanemura H, Masuda K, Okumura T, Takagi E, Kajihara D, Kakihara H, Nonaka K, Ushioda R. Development of a stable antibody production system utilizing an Hspa5 promoter in CHO cells. *Sci Rep.* 2022 May 24;12(1):7239. doi: 10.1038/s41598-022-11342-1. PMID: 35610229; PMCID: PMC9130236.

### 4. Vito et al. 2020 (Projects 33, 34, 35)
Vito D, Eriksen JC, Skjodt C, Weilguny D, Rasmussen SK, Smales CM. Defining lncRNAs Correlated with CHO Cell Growth and IgG Productivity by RNA-Seq. *iScience.* 2020 Jan 24;23(1):100785. doi: 10.1016/j.isci.2019.100785. PMID: 31962234; PMCID: PMC6971398.

### 5. Tsunoda et al. 2025 (Project 36)
Tsunoda Y, Arishima R, Boronina T, Cole R, Yamano-Adachi N, Betenbaugh M, Omasa T. Comparative omics profiling reveals differences in biomass, energy production, and vesicle transport between CHO and fast-growing CHL-YN cells. *Sci Rep.* 2025 Nov 13;15(1):39784. doi: 10.1038/s41598-025-23503-z. PMID: 41233401; PMCID: PMC12615816.

### 6. Nguyen et al. 2020 (Project 37)
Nguyen LN, Novak N, Baumann M, Koehn J, Borth N. Bioinformatic Identification of Chinese Hamster Ovary (CHO) Cold-Shock Genes and Biological Evidence of their Cold-Inducible Promoters. *Biotechnol J.* 2020 Mar;15(3):e1900359. doi: 10.1002/biot.201900359. PMID: 31785035.

### 7. Hefzi et al. 2016 (Project 39)
Hefzi H et al. A Consensus Genome-scale Reconstruction of Chinese Hamster Ovary Cell Metabolism. *Cell Syst.* 2016 Nov 23;3(5):434-443.e8. doi: 10.1016/j.cels.2016.10.020. PMID: 27883890; PMCID: PMC5132346.

### 8. Orellana et al. 2018 (Projects 40, 41)
Orellana CA, Marcellin E, Palfreyman RW, Munro TP, Gray PP, Nielsen LK. RNA-Seq Highlights High Clonal Variation in Monoclonal Antibody Producing CHO Cells. *Biotechnol J.* 2018 Mar;13(3):e1700231. doi: 10.1002/biot.201700231. PMID: 29316330.

### 9. Tzani et al. 2020 (Project 42)
Tzani I, Monger C, Motheramgari K, Gallagher C, Hagan R, Kelly P, Costello A, Meiller J, Floris P, Zhang L, Clynes M, Bones J, Barron N, Clarke C. Subphysiological temperature induces pervasive alternative splicing in Chinese hamster ovary cells. *Biotechnol Bioeng.* 2020 Aug;117(8):2489-2503. doi: 10.1002/bit.27365. PMID: 32346860.

### 10. Barzadd et al. 2022 (Projects 43, 44, 45, 46)
Barzadd MM et al. Autophagy and intracellular product degradation genes identified by systems biology analysis reduce aggregation of bispecific antibody in CHO cells. *N Biotechnol.* 2022 May 25;68:68-76. doi: 10.1016/j.nbt.2022.01.010. PMID: 35123066.

### 11. Thalen et al. 2024 (Project 47)
Thalen NB, et al. Tuning of CHO secretional machinery improve activity of secreted therapeutic sulfatase 150-fold. *Metab Eng.* 2024 Jan;81:157-166. doi: 10.1016/j.ymben.2023.12.003. PMID: 38081506.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
