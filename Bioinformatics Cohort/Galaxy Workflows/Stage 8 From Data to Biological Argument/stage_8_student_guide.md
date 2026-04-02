# Stage 8 — From Data to Biological Argument

## A Guide to Scientific Reasoning, Literature Integration, and Assessment Preparation

---

## What This Stage Is

There is no Galaxy workflow to run here. Stage 8 is the intellectual core of your project: the step where you transform a list of differentially expressed genes and enriched pathways into a coherent scientific argument.

This is where most of the marks you have not yet secured will be earned or lost. The assessment criteria for CHEN40140 weight scientific content at 50% of your short research communication grade. The biological reasoning you construct in this stage is that 50%.

This guide is intentionally long. You will not meet your supervisor frequently from this point on. Read it in full.

---

## Overview: The Task

You have, from Stage 7:
- A list of differentially expressed genes (DEGs) with log2 fold changes and adjusted p-values
- A volcano plot showing the distribution of those changes
- Annotated gene names and biotypes
- A pathway enrichment result from gProfiler

From these, you must now:

1. Compare what you found to what the original paper reported
2. Choose a specific biological focus — one process, one pathway, one mechanistic question
3. Construct a reasoned argument linking the gene expression changes you observed to the phenotype the experiment was designed to study
4. Support that argument with evidence from the broader literature, including non-CHO species where it is scientifically defensible
5. Be honest and explicit about the limitations of what you can and cannot conclude
6. Present this argument clearly in both your written submission and oral examination

None of these steps are mechanical. All of them require judgement.

---

## Part 1 — Comparing Your Results to the Previous Study

### Why You Must Do This

You are re-analysing data from a published study. This puts you in a specific scientific position: your job is not simply to repeat what the paper said, and it is not to pretend the paper does not exist. Your job is to establish a dialogue between your analysis and the original findings.

This comparison is scientifically valuable because:
- You may have used different tools, versions, or parameter choices
- You may have applied different thresholds
- The original authors may have focused on a subset of results that interested them, leaving other findings unaddressed
- You may have applied a more rigorous strandedness check, a better reference genome version, or identified a batch effect the original paper did not discuss

### What to Compare

**Total DEG counts.** How many genes did the original paper call differentially expressed? How many did you find? If the numbers differ substantially, why?

Differences are expected and are not a failure on your part. Common reasons include:
- Different alignment tools (HISAT2 vs STAR vs TopHat2) — STAR typically produces higher mapping rates and more reliably counted reads
- Different counting tools (featureCounts vs HTSeq-count) — counting mode and stranding settings have large effects
- Different reference genome versions — gene model changes between Ensembl releases affect how many reads can be assigned
- Different statistical thresholds — some papers use p < 0.05 (unadjusted), which is far more permissive than FDR < 0.05
- Different fold change thresholds — many papers use 2-fold (log2FC ≥ 1.0), your analysis uses 0.263

**Overlapping genes.** Which genes appear in both your DEG list and the paper's DEG list? For the top 20–30 most significant genes in the original paper, check whether they appear in your results and whether the direction of change (up/down) is consistent.

**Overlapping pathways.** Which biological processes or pathways did the paper highlight? Do your gProfiler results identify the same processes?

**Novel findings.** What appears in your results that the original paper did not discuss? This is not necessarily because you found something new — it may be that the paper focused on a specific subset of genes relevant to their hypothesis and did not report everything. But any DEGs or enriched pathways that were not mentioned in the paper are legitimate additions to your Discussion.

**Discrepancies.** If a gene is central to the original paper's conclusion but does not appear in your DEG list, address this directly. Do not ignore it. Possible explanations include different strandedness settings, different read lengths affecting counting at specific loci, gene model changes between annotation versions, or different statistical correction approaches. Discuss which is most likely.

### How to Present the Comparison

In your written Discussion, the comparison to the original paper should occupy approximately one short paragraph. It is not the centrepiece of your argument — it is context-setting. Typical framing:

> "Consistent with [Author et al., Year], we identified significant upregulation of [process/gene], supporting the original finding that [brief summary of original conclusion]. Our analysis additionally identified [novel finding not in original paper], which was not reported in the original study, potentially due to [methodological difference]. Notably, [gene X] identified by [Author et al.] as a key target was not present in our DEG list, which may reflect [plausible explanation]."

The comparison paragraph establishes that you understand the original work, that your pipeline is producing biologically coherent results, and that your analysis adds something.

---

## Part 2 — Choosing Your Biological Focus

### You Cannot Discuss Everything

A typical DEG analysis in CHO cells returns between 100 and 2,000 differentially expressed genes. Your written assessment is 2,000–2,500 words total, of which your Discussion section accounts for roughly 700–900 words of scientific interpretation. You cannot meaningfully discuss more than 1–3 biological themes in that space.

Attempting to discuss many genes superficially will score lower than discussing one biological thread deeply. The mark scheme rewards scientific reasoning and argument quality — not comprehensiveness of gene listing.

**Your goal is to choose one main biological focus** and to develop it in enough depth that a reader understands the mechanistic story you are telling.

### How to Choose Your Focus

A good focus has all of the following properties:

**1. Supported by your data**
The genes or pathway you focus on must be statistically significant in your analysis. A single gene with FDR = 0.03 that happens to be interesting to you is a weak choice if only one gene from that pathway appears in your list. A coherent cluster of five genes all pointing to the same biological process is a strong choice.

**2. Connected to the phenotype under study**
Your experiment has a biological question — a treatment was applied to CHO cells and something was being measured or expected to change. This is defined by the paper from Stage 1. Your chosen focus must be mechanistically connected to that phenotype.

If the paper is about improving antibody productivity, a focus on secretory pathway genes is directly relevant. A focus on mitochondrial ribosome biogenesis may be harder to connect, even if those genes are significantly changed, unless you can build a credible argument for how energy production relates to the secretory burden of an antibody-producing cell.

Ask yourself: *If I had to explain to someone why this gene change matters for what the experiment was trying to achieve, could I do it in three sentences?* If yes, it is a good focus. If you need ten sentences of caveats, it is probably not.

**3. Supported by literature**
You need publications to support your argument. Before committing to a focus, do a preliminary literature search (see Part 3). The CHO research field is small — for the majority of genes in your DEG list, you will find little or no CHO-specific experimental literature, and this is normal. The absence of a CHO paper is not evidence that a gene is unimportant or that your argument is weak. What matters is whether the protein's function is understood in any well-studied mammalian context (human, mouse) and whether that function is plausibly relevant to your phenotype. A gene characterised only in human or murine cells can anchor a very strong argument if the cross-species reasoning is sound (see Part 5). The only situation where a gene becomes genuinely difficult to argue for is when it has no functional characterisation in any relevant biological system.

**4. Experimentally tractable (in principle)**
Part of your Discussion will involve considering what you would do next. A good focus is one where you can articulate a realistic experimental follow-up. This does not mean you have to design a full experiment — but you should be able to say, for example, "siRNA knockdown of X in a recombinant CHO cell line followed by fed-batch productivity measurement would directly test whether this gene change is causally linked to the observed productivity phenotype."

### Deciding Between Candidates

If you have two or three candidate focuses and are unsure which to choose, apply this prioritisation framework:

| Criterion | Weight | How to evaluate |
|-----------|--------|-----------------|
| Statistical strength | High | Lowest FDR + largest |log2FC| wins |
| Phenotype relevance | High | Directly connected to the paper's measured outcome vs. tangentially related |
| Literature support | High | At least 2–3 publications in relevant mammalian context |
| Pathway coherence | Medium | Multiple genes from same pathway present in your DEG list |
| Novelty relative to original paper | Medium | Not already the central focus of the original paper |
| Experimental tractability | Medium | Clear validation experiment exists |

Score each candidate informally against these criteria. Do not choose a focus based on the magnitude of the fold change alone — a gene with log2FC = 5.0 that has no functional characterisation in any mammalian system is genuinely harder to argue for than a gene with log2FC = 0.8 whose protein function is well-understood in human or mouse, even if no one has studied it directly in CHO. The absence of CHO-specific publications is the norm, not a disqualifier.

### Examples of Focused Arguments in CHO RNA-seq Studies

The following are the kinds of focused arguments that work well within the word limit. They are illustrative, not prescriptive:

- "Our analysis identifies upregulation of three UPR-associated chaperones (HSPA5, DNAJB9, CALR), suggesting increased ER folding demand as a consequence of the treatment. We argue this reflects [specific mechanism] and propose this represents a potential target for cell line engineering."

- "Multiple genes in the NF-κB signalling cascade were significantly downregulated following treatment, including [gene A], [gene B], and [gene C]. We propose that the treatment reduces pro-inflammatory signalling, which may contribute to the observed improvement in culture viability by [mechanism]."

- "The most significantly upregulated gene in our dataset, [gene X], encodes a protein involved in [function]. While not highlighted in the original paper, we present evidence that this gene's induction is consistent with [paper 1, paper 2] and may explain [phenotypic observation]."

---

## Part 3 — Understanding Your Genes of Interest

Before you can build an argument, you need to understand what the proteins encoded by your genes of interest actually do. Do not assume you already know this from the gene name — gene names can be misleading, and the function in CHO cells may differ from the function in the context you are most familiar with.

### Step 1: UniProt

Go to [uniprot.org](https://www.uniprot.org) and search for the human orthologue of your gene of interest (use the gene symbol). The reviewed (Swiss-Prot) entry is the most reliable.

From the UniProt entry, extract:
- **Function** section — the molecular function of the protein
- **Subcellular location** — where the protein operates in the cell
- **Tissue specificity** (if relevant)
- **Post-translational modification** — is it regulated by phosphorylation, ubiquitination, etc.?
- **Involvement in disease** — if the gene is implicated in disease, this sometimes gives strong phenotypic evidence
- **Publications** — the curated literature listed in UniProt is a good starting point

### Step 2: GeneCards

Go to [genecards.org](https://www.genecards.org) and search for the same gene. GeneCards aggregates information from many databases and includes:
- Expression data across tissues
- Pathways the gene appears in across multiple databases
- Disease associations
- Orthologue information

This is a useful cross-check and often provides leads to publications you would not find through UniProt alone.

### Step 3: PubMed Literature Search

Go to [pubmed.ncbi.nlm.nih.gov](https://pubmed.ncbi.nlm.nih.gov) and perform targeted searches. Work from broad to specific — start with the protein's function in any well-characterised mammalian system, then narrow to contexts that are relevant to your phenotype:

```
[gene name] AND ("CHO" OR "Chinese hamster ovary")               [may return little or nothing — this is normal]
[gene name] AND ("biopharmaceutical" OR "recombinant protein" OR "monoclonal antibody")
[gene name] AND ("cell stress" OR "ER stress" OR "unfolded protein response")  [adapt to your context]
[gene name] AND ("apoptosis" OR "proliferation" OR "productivity")  [adapt to your context]
[gene name] AND (mouse OR murine OR "HEK293" OR "HeLa")          [for general mammalian cell function]
```

For each relevant paper you find, ask:
- What was the experimental context? (cell type, treatment, species)
- What did they find when this gene was overexpressed, knocked down, or inhibited?
- What phenotype did they measure?
- Does this support or challenge the direction of change you observed (upregulated vs. downregulated)?
- Is the cellular context close enough to CHO that the finding is transferable?

**Most genes in your DEG list will have no CHO-specific experimental literature.** This does not mean you cannot build an argument around them. The CHO research field is small relative to human or mouse biology, and the majority of mammalian gene function has been characterised in human cell lines or mouse models. Human and mouse evidence, properly contextualised, is your primary resource — not a fallback (see Part 5). CHO-specific publications, when they exist, are an additional supporting layer.

### Step 4: STRING Database

Go to [string-db.org](https://string-db.org) and enter your gene of interest using *Homo sapiens* as the organism (or *Mus musculus* if your gene has a known mouse name). Look at:

- **Physical and functional interactions** — which other proteins does your gene product interact with or co-function with?
- **Co-expression** — in what contexts are these proteins co-regulated?
- **Pathway membership** — does STRING confirm the pathway enrichment from gProfiler?

If several of your DEGs form a connected network in STRING, this is strong supporting evidence that they are co-regulated and acting together in the same biological process. A screenshot of a STRING interaction network is a legitimate figure for your written assessment if it adds interpretive value.

---

## Part 4 — Building the Reasoned Argument

### The Structure of a Mechanistic Argument

A strong biological argument in a research communication takes this form:

1. **Observation** — state what you found (gene X is significantly upregulated, fold change, adjusted p-value)
2. **Protein function** — state what protein X does and where it operates in the cell
3. **Mechanistic link** — explain how a change in the abundance of mRNA for protein X would plausibly affect the biological process relevant to your phenotype
4. **Literature support** — cite evidence (from CHO, mouse, human, or another relevant system) showing that this gene or protein has been associated with the phenotype or process you are discussing
5. **Direction check** — confirm that the direction of change (up or down) is consistent with the effect you are proposing

Every step must be present. An argument that stops at step 1 is description, not science. An argument that goes from step 1 directly to step 4 without step 3 is correlation, not mechanism.

### The Direction of Change Is Evidence

Whether a gene is upregulated or downregulated is itself biological information. It constrains your argument.

If you are arguing that treatment X improves CHO cell productivity by reducing ER stress, and you observe that the ER stress marker HSPA5 is *upregulated*, you have a contradiction. HSPA5 upregulation indicates *increased* ER stress, not reduced stress. Either your interpretation is wrong, or the result is more complex than you initially thought (perhaps transient stress resolution leads to long-term productivity gain — a nuanced argument that requires literature support).

Before committing to an argument, ask: *Does the direction of change in each gene I am discussing make sense for the conclusion I am drawing?* If not, either refine your argument or explain the apparent contradiction.

### Connecting mRNA to Phenotype: The Caveats You Must Acknowledge

RNA-seq measures mRNA abundance, not protein abundance, and not protein activity. Your argument must acknowledge this chain of inference:

```
mRNA change → (assumed) protein abundance change → (assumed) activity change → pathway effect → phenotype
```

Each arrow is an assumption. In a short research communication you do not need to dwell on this, but you must acknowledge it once, typically with a sentence such as: *"While we cannot confirm changes at the protein level from transcriptomic data alone, the sustained upregulation of [gene X] across all replicates is consistent with [published finding at protein level in reference]."*

If a paper you cite measured the same gene at the protein level (by western blot, proteomics, or another method) and found consistent results, cite it — this is strong corroborating evidence.

### What Makes an Argument Strong vs. Weak

The strength of an argument is determined by the quality of its reasoning, not by the species in which the supporting evidence was generated. A well-constructed argument using human or mouse data is stronger than a superficial argument citing a CHO paper that is only tangentially relevant.

| Strong argument | Weak argument |
|-----------------|---------------|
| Multiple genes from the same pathway all change in a consistent direction | A single gene changes and you argue the whole pathway is affected |
| Direction of change is consistent with the proposed mechanism | Direction of change requires a convoluted explanation to fit the hypothesis |
| Cross-species inference is made explicit: protein function is established in a mammalian system and the conservation of the relevant pathway is stated | Cross-species evidence is cited without acknowledging it is from a different species, or the cellular context is very different from CHO |
| Pathway enrichment from gProfiler independently supports the gene-level argument | Pathway enrichment is absent or marginal, and the argument relies solely on gene lists |
| Limitations are clearly acknowledged and their impact on the conclusion is assessed | Limitations are absent, or you claim your argument is weak because "there are no CHO studies on this gene" without attempting mammalian evidence |
| The fold change is at least modest (|log2FC| > 0.5) and FDR is well below threshold | Gene is barely above the fold change or significance threshold |
| The protein's function is characterised in any well-studied mammalian system and linked mechanistically to the phenotype | The gene has no functional characterisation in any biological system beyond a name in a database |

---

## Part 5 — Cross-Species Evidence

### Cross-Species Evidence Is Your Primary Resource

CHO cells are derived from *Cricetulus griseus*, the Chinese hamster. The CHO research field, while growing, remains small relative to the volume of published work in human and mouse biology. For the overwhelming majority of genes you will encounter in your DEG list, human or mouse data will be the primary — and sometimes only — published experimental evidence available. This is not a weakness in your argument. It is the reality of working in a specialised field, and it is exactly the situation that published CHO papers themselves face: they routinely cite human and mouse mechanistic evidence to support their interpretations.

Your task is not to find CHO-specific evidence before you can make an argument. Your task is to use evidence from the most relevant available biological systems and to reason carefully about its applicability to CHO.

**Mouse (*Mus musculus*) data is the most directly transferable.** CHO is a rodent cell line. Gene function, protein interactions, and signalling pathway architecture are highly conserved between CHO and mouse. Evidence from murine cell lines, primary mouse cells, or mouse models is directly applicable provided the cellular context is not too different.

**Human (*Homo sapiens*) data is broadly applicable for conserved processes.** Core cellular processes — protein folding, the ubiquitin-proteasome system, central carbon metabolism, the cell cycle, apoptosis, secretory pathway function, and most signalling cascades — are highly conserved between human and hamster. For genes operating in these conserved systems, human cell line data is legitimate evidence. The argument becomes weaker only when the process you are discussing is known to differ substantially between species (e.g., certain aspects of glycosylation, specific immune signalling pathways absent from CHO).

**Non-mammalian data (yeast, *C. elegans*, *Drosophila*) is generally too distant** for direct mechanistic inference in CHO biology, unless the function is so ancient and conserved (core ribosome components, histones, ubiquitin, the proteasome) that the conservation is thoroughly established across all eukaryotes.

### How to Argue Cross-Species Relevance

When you cite evidence from mouse or human in support of an argument about CHO, you should make the cross-species inference explicit. Do not simply write "X protein promotes Y in [mouse cell type], therefore this is relevant." Write:

> "[Gene X] encodes a protein with [function]. Knockdown of [Gene X] in murine [cell type] reduced [phenotype] by [magnitude] [citation]. CHO cells share high sequence conservation in this protein (>85% amino acid identity between *Cricetulus griseus* and *Mus musculus*; [reference to orthologue databases if available]), and the pathway in which it operates is conserved in mammalian cells. This supports the inference that upregulation of [Gene X] in our dataset may reflect [proposed mechanism] in CHO."

You do not need to look up percentage identity for every gene — this level of detail is appropriate only for your primary gene of interest. For secondary supporting evidence, citing the cross-species relevance in one sentence is sufficient.

### When Cross-Species Arguments Break Down

Be cautious about cross-species inference in the following situations:

- **CHO-specific biology.** CHO cells have undergone substantial chromosomal evolution in culture and have CHO-specific metabolic and regulatory characteristics. Glycosylation patterns, certain metabolic fluxes, and some stress response thresholds differ between CHO and other mammalian cells. If you are arguing about a gene whose function is likely to be cell-line specific or whose expression pattern is known to differ between CHO and mouse, acknowledge this.

- **Context specificity.** A gene that promotes proliferation in a human cancer cell line is not automatically relevant to a CHO cell line growing in suspension culture in a bioreactor. The cellular and environmental context matters. If the only published evidence for your gene is from a context that is biologically quite different from yours, acknowledge the limitation.

- **Conflicting evidence.** If papers from different species or cell types disagree about what a gene does, do not cherry-pick the one that supports your argument. Present both findings and discuss the discrepancy.

---

## Part 6 — Limitations and Prioritisation

### The Obligation to Prioritise

Even after you have chosen a focused argument, you will still have more data than you can responsibly discuss in 2,000–2,500 words. You must prioritise. This is not a failure — it is scientific practice.

Every published RNA-seq paper makes prioritisation decisions. Authors choose which genes to highlight, which pathways to emphasise, and which results to present as primary findings versus supplementary information. The difference between a student project and a published paper is that in the student project, you are expected to be explicit about how and why you prioritised.

In your Discussion, acknowledge what you are not discussing:

> "While our analysis identified X genes differentially expressed in [other pathway], we have focused on [chosen focus] because [reason]. A full characterisation of all DEG classes is beyond the scope of this work."

This is not weakness — it demonstrates scientific judgement.

### Technical Limitations of Your Analysis You Must Acknowledge

The following limitations apply to every RNA-seq analysis in this module and should be addressed in your Discussion:

**mRNA does not equal protein.** Gene expression changes may not translate linearly to protein abundance changes due to post-transcriptional regulation, translational efficiency differences, and protein stability. Your conclusions are about transcriptional responses.

**Correlation versus causation.** The gene expression changes you observe are associated with the treatment, but RNA-seq alone cannot establish whether they are causes of, consequences of, or coincidental to the treatment effect. Experimental validation (see below) is needed to establish causality.

**Statistical thresholds are arbitrary.** The FDR < 0.05 and |log2FC| ≥ 0.263 thresholds are standard conventions, not natural boundaries. Some genes just below threshold may be biologically relevant; some above threshold may be spurious. Your conclusions should not depend on a single gene barely passing the threshold.

**The mmusculus proxy in pathway analysis.** As discussed in Stage 7, gProfiler was run against the mouse pathway database. Chinese hamster-specific pathway architecture may differ, and some CHO gene names may not map cleanly to mouse orthologues. Pathway enrichment results should be treated as indicative rather than definitive.

**Replication.** Report the number of biological replicates in each group. If your experiment has only two replicates per group, statistical power is limited, and false discovery rates may be underestimated. Acknowledge this.

**Single time point.** RNA-seq is a snapshot of gene expression at a single point in time (or averaged across a culture phase). It does not capture the dynamics of the response. Genes that are transiently regulated may appear absent from your DEG list; genes that appear regulated may reflect a later stage of a complex response.

### Proposing Experimental Follow-Up

A Discussion that ends with "future work should validate these findings" without specifying what that work would be is less convincing than one that proposes a specific experiment.

For your primary gene of interest, identify the most logical experimental validation. Common approaches in CHO bioprocess research:

| What you want to test | Approach |
|----------------------|----------|
| Does reducing/eliminating this gene's expression impair productivity? | siRNA knockdown or CRISPR-Cas9 knockout in a recombinant CHO cell line, followed by fed-batch productivity assay |
| Does overexpressing this gene increase productivity? | Stable overexpression construct in CHO, fed-batch comparison |
| Is the mRNA change reflected at the protein level? | Western blotting for the protein of interest in your treatment vs. control samples |
| Is the gene causally linked to the phenotype or just associated? | Inducible expression system to modulate gene expression independently of the original treatment |
| Is the pathway effect real? | Metabolic flux analysis (if metabolic pathway), secretory pathway assay (if secretory pathway), etc. |

Your proposed validation does not need to be novel — if a published paper has already validated the role of your gene in a relevant phenotype, cite that paper. But stating what validation *could or should* be done demonstrates understanding of the scientific process.

**Be realistic about cost and complexity.** A sentence like "future work could use whole-proteome mass spectrometry to validate these findings at the protein level" is less useful than "western blotting for [specific protein] in the original or a comparable CHO cell line would be the most direct first validation step, given that a well-validated antibody is commercially available." The more specific and grounded in feasibility your proposal, the better.

---

## Part 7 — Writing the Discussion

### The Purpose of the Discussion

The Discussion is not a summary of your Results. It is where you tell the scientific story your data supports — where you explain what the results mean, why they are interesting, how they fit with existing knowledge, and what the limitations and implications are.

A Discussion that re-states results without interpretation is a common student error and will score poorly on the scientific content criterion.

### Structure of a Strong Discussion for This Type of Work

The following structure works well within your word limit. It is a guide, not a rigid template — good scientific writing requires flexibility.

**Opening paragraph (60–100 words)**
State the main finding of your study in one sentence. Then immediately contextualise it: what does this finding mean in relation to the original paper's question and the broader context of CHO cell biology? Do not open with "In this study, we used DESeq2 to analyse…" — the reader already knows this from your Methods. Open with the science.

Example: *"Re-analysis of [Author et al. Year] using [your pipeline] confirms their finding of widespread transcriptional remodelling following [treatment] in CHO cells, and identifies [X novel finding] as a previously unreported feature of this response."*

**Comparison to original study (100–150 words)**
Summarise agreements, discrepancies, and novel findings relative to the published paper. This should be concise — one to two paragraphs maximum. Connect discrepancies to methodological differences where you can.

**Primary biological argument (300–450 words)**
This is the heart of your Discussion. Present your chosen biological focus in depth:
- State the observation (specific genes, with numbers)
- Introduce the protein function and its mechanistic relevance
- Build the argument linking gene change → molecular function → pathway effect → phenotype
- Cite literature supporting each step
- Check and confirm that directionality is consistent
- Acknowledge any complexity or nuance (genes changing in unexpected directions, competing evidence)

This section should cite at least 3–5 primary research papers. Do not cite review papers exclusively — the assessment criteria reward engagement with primary literature.

**Additional observations (100–200 words, optional)**
If you have a second biological thread worth noting briefly, you may include it here. Keep it shorter than your primary argument. Do not develop three separate threads equally — depth in one is better than breadth across three.

**Limitations (100–150 words)**
Address the technical and biological limitations of your analysis directly. Do not bury limitations in caveats throughout other paragraphs — a dedicated limitations section demonstrates scientific maturity. Cover at least: the mRNA-to-protein inference problem, the mmusculus proxy, and any study-specific limitations (sample size, time point, library prep concerns noted in Stage 1).

**Concluding statement and future directions (60–100 words)**
Close with a forward-looking sentence or two. What is the most important validated finding? What is the most important unanswered question? What is the single most informative next experiment? This does not need to be long — it needs to be specific.

### Writing the Conclusion

The Conclusion is a short, standalone section that follows the Discussion. It is not a summary of the Discussion, and it is not a place to introduce new results, new citations, or new arguments. It is the final statement of what your work has established and why it matters.

**What the Conclusion must do:**

1. **State the primary finding** in one sentence — the single most important thing your analysis showed. This should be the biological conclusion, not the technical achievement. "We successfully ran a DESeq2 pipeline" is not a conclusion. "Transcriptional upregulation of [gene/pathway] following [treatment] in CHO cells is consistent with [proposed mechanism] and identifies a potential target for future bioprocess engineering" is a conclusion.

2. **Connect to the original study** in one sentence — does your analysis support, extend, or partially contradict the original paper's conclusions? This is a synthesis point, not a repetition of your comparison paragraph from the Discussion.

3. **State the broader significance** in one sentence — what does this finding mean for biopharmaceutical manufacturing, CHO cell engineering, or the field? This should be specific to your result, not a generic statement about the importance of RNA-seq.

4. **Close with a forward-looking statement** — one sentence identifying the most important unanswered question or the most critical next step. This is a condensed version of your future directions, not a new proposal.

**What the Conclusion must not do:**

- Do not restate the Discussion's opening paragraph with different words
- Do not introduce new data, new citations, or new claims
- Do not write "In conclusion, we have shown that…" followed by a bullet list of results — this is a list, not a conclusion
- Do not use hedging language that undermines your own findings ("we may have possibly identified what could be…") — the Conclusion is where you commit to what your analysis has demonstrated, appropriately qualified

**Length:** 50–100 words is sufficient. A Conclusion that runs to 200 words has usually absorbed material that belongs in the Discussion. If you find yourself adding caveats and qualifications, those belong in the Limitations section of your Discussion, not here.

**Example structure** (not a template to copy — use as a structural reference):

> "Re-analysis of [Author et al., Year] identified [N] differentially expressed genes in CHO cells following [treatment], of which upregulation of [gene/pathway] was the most consistent finding. This is in agreement with the original authors' conclusion that [brief original claim] and further suggests that [your novel interpretive contribution]. These findings have implications for [specific aspect of bioprocess engineering — e.g., media design, cell line engineering]. Functional validation of [primary gene/pathway] in a recombinant CHO production context represents the most direct next step to establish the causal relationship between this transcriptional response and the observed [phenotype]."

### Citation and Literature Style

- Cite using numbered references in the format required by the module (check submission guidelines)
- Every factual claim that is not from your own analysis must be cited
- Do not cite Wikipedia, textbooks, or course notes as primary evidence
- Prioritise papers published in the last 15 years unless citing foundational work
- When citing a paper for a specific claim, ensure that claim is actually in the paper — do not cite papers you have not read

### Word Allocation

Your total word limit is 2,000–2,500 words. A rough allocation:

| Section | Suggested word range |
|---------|---------------------|
| Abstract | 150–200 |
| Introduction | 300–400 |
| Methods | 300–400 |
| Results | 400–500 |
| Discussion | 600–800 |
| Conclusion | 50–100 |
| References | Not counted |

The Discussion remains your largest section, but note that the Conclusion is a distinct section — not a paragraph appended to the Discussion. Together they account for the majority of your scientific interpretation and carry the most assessment weight.

### Figures and Tables in the Results Section

#### The Figure Constraint

Your submission has a strict word limit and limited space for figures. You should plan for **a maximum of 4–5 figures** in the main document. Every figure you include costs space, and a figure that does not earn its place — either because it is redundant, uninformative, or not discussed in the text — actively harms your submission by displacing content that would score better.

**Quality over quantity.** A single well-constructed, informative figure that you discuss in depth is worth more than three screenshots from Galaxy that you mention in passing. Figures are assessed on whether they contain the right information, are presented clearly, and are placed appropriately — not on how many you include.

#### Multipanel Figures

You are permitted to combine related panels into a single figure. This is standard practice in published papers and is an efficient way to make the most of your figure budget. A well-designed multipanel figure communicates more per unit of document space and demonstrates that you understand the relationship between the panels.

Examples of sensible multipanel combinations:

- **Figure 1: Data quality overview** — Panel A: MultiQC per-sample read quality summary (Stage 2). Panel B: STAR mapping rate bar chart (Stage 5). These together establish that your data was of sufficient quality to proceed.
- **Figure 2: Differential expression overview** — Panel A: PCA plot (samples separate as expected). Panel B: Volcano plot (DEG distribution). These two panels together tell the story of your experimental design working and your primary result.
- **Figure 3: Biological focus** — Panel A: A bar chart or heatmap of log2FC values for your genes of interest. Panel B: gProfiler enrichment dot plot for your primary pathway. These connect the gene-level result to the pathway-level interpretation.

Label panels as A, B, C etc. and refer to them in the figure legend and in-text as "Figure 2A", "Figure 2B" etc.

#### Galaxy Figures vs. Replotted Figures

Galaxy produces functional but not publication-quality figures. You are not required to replot your results, and Galaxy output figures (volcano plots, MultiQC charts, PCA plots) are perfectly acceptable for submission.

However, if you want to produce a cleaner or more informative version of a figure — for example, a heatmap of your top 20 DEGs with gene names, a customised volcano plot with specific genes labelled, or a grouped bar chart of expression levels for your genes of interest — tools like **R with ggplot2**, **Python with matplotlib/seaborn**, or even **Prism** can be used. This is optional, not required.

If you do produce a custom figure, a few rules apply:
- The data must come from your own analysis (your DESeq2 results, your count data)
- The figure must be reproducible — if asked, you should be able to describe how you made it
- Custom figures must follow the same legend requirements as Galaxy figures
- Do not fabricate or manually adjust data points — present what your analysis produced

A brief note such as "Figure 3A was produced using ggplot2 (v3.4) in R" in the figure legend is sufficient attribution.

#### Recommended Figure Set

Plan your figures before you start writing. The following set covers the key stages of your analysis within the figure budget:

| Priority | Figure (or panel) | Section it belongs in | What it must show |
|----------|------------------|-----------------------|-------------------|
| Essential | Volcano plot | Results | DEG distribution with thresholds marked, n values stated |
| Essential | PCA plot | Results | Sample separation between treatment and control groups |
| Essential | Top DEG table | Results | Gene name, log2FC, FDR for top 15–20 genes |
| Recommended | MultiQC or mapping rate | Results | Evidence of data quality |
| Recommended | Pathway enrichment | Results or Discussion | Top enriched terms with p-values and intersection sizes |
| Optional | Heatmap of genes of interest | Results or Discussion | Expression pattern across all samples for your chosen focus |
| Optional | STRING network | Discussion | Interaction context for your primary biological argument |

The PCA and volcano are the minimum pair that must be present in every submission. Everything else is chosen based on what best supports your specific argument.

#### What Makes a Good Figure Legend

Every figure — Galaxy screenshot or custom plot — requires a complete legend. A legend has three parts:

1. **Title** — a short descriptive statement of what the figure shows (not what you conclude from it)
2. **Description** — what the axes, colours, shapes, or panels represent; any thresholds or cutoffs that appear on the figure; the n for each group
3. **Key result** — one sentence stating what the figure demonstrates in the context of your argument

Example:

> **Figure 2. Differential gene expression analysis of CHO cells following [treatment] compared to control.**
> (A) Principal component analysis (PCA) of DESeq2-normalised count data. Each point represents one biological replicate (n=3 per group). Treatment samples (red) cluster separately from control samples (blue) along PC1 (X% variance explained), indicating consistent transcriptional separation between conditions. (B) Volcano plot showing log2 fold change (x-axis) against −log10 adjusted p-value (y-axis) for all genes tested. Horizontal dashed line: FDR = 0.05 threshold. Vertical dashed lines: |log2FC| = 0.263 threshold. Genes meeting both thresholds are coloured (red: upregulated; blue: downregulated). N = X upregulated, Y downregulated genes.

Do not write a legend that says only "Volcano plot of DEGs." That is a label, not a legend.

#### Tables

A table of your top 15–20 most significant DEGs (sorted by adjusted p-value) is appropriate in the Results section. Include: gene name, log2FC (rounded to 2 decimal places), and FDR. Do not include Ensembl IDs in your main results table — use gene names.

Do not paste your entire DEG list as a table. If you wish to include the full list, it belongs in supplementary material, not the main text.

---

## Part 8 — Recorded Presentation

### Format

The oral component of this assessment is a **recorded, narrated PowerPoint presentation**. There is no live examination and no question-and-answer session. You record yourself presenting your slides and submit the recording alongside your written communication.

This changes the task in a specific and important way: you cannot be prompted or asked to clarify in real time. A point you leave ambiguous stays ambiguous. A gap in your argument cannot be rescued by a well-answered question. Everything you want to communicate must be in the recording itself, and the recording must stand alone as a complete, coherent scientific presentation.

The assessor is looking for the same qualities as in the written submission — scientific understanding, quality of argument, clarity of communication — but in a spoken form. The presentation is not a recitation of your written communication. It is a complementary piece: the written communication gives the detail; the presentation shows you can synthesise and present the key story.

### What the Recording Is Assessed On

- That you understand the biological question the experiment was designed to address and can explain its relevance to biopharmaceutical manufacturing
- That you can describe your pipeline at a conceptual level — what each stage did and why the key parameter choices were made
- That your primary results are presented accurately, with the correct numbers, thresholds, and figures
- That your biological argument is articulated clearly — not merely that you found differentially expressed genes, but what those changes mean for the biology of the system under study
- That you acknowledge the limitations of transcriptomic data and of your specific analysis
- That you propose a specific and reasoned next experiment

### Slide-by-Slide Structure

Aim for **10–12 slides** and a recording length of **8–12 minutes**. Every slide should have a clear purpose. If you cannot state in one sentence why a slide is in the deck, cut it.

---

**Slide 1 — Title**

Your name, project number, the full citation of the paper you re-analysed, and the date. One sentence of narration introducing yourself and the paper is sufficient.

---

**Slides 2–3 — The biological question (approximately 2 minutes)**

Establish the context and the experimental question. Cover:

- The biological system: CHO cells in a biopharmaceutical manufacturing context. Keep this to two sentences — your assessor does not need an introduction to CHO cells, but the specific relevance of this experiment to bioprocess engineering should be stated
- The experimental design: what treatment was applied, what was the control condition, and what phenotypic outcome was the study trying to understand or improve
- The original paper's main conclusion: what did the authors find? Two to three sentences

Do not reproduce the paper's abstract on a slide. Synthesise it. The goal is to establish that you have read and understood the paper, not that you can quote from it.

---

**Slide 4 — Pipeline overview (approximately 60–90 seconds)**

A simple flowchart of Stages 1–7. Label each stage with the tool name and one-phrase description of what it produces (e.g., "Stage 5: STAR — position-sorted BAM files aligned to *C. griseus* genome"). You do not need to justify every parameter choice — that belongs in the written Methods. What this slide communicates is that you ran a coherent, multi-stage analysis and understand what each step contributes.

The most common mistake on this slide is spending too long on it. The pipeline is infrastructure. It should be covered efficiently so you have time for the science.

---

**Slide 5 — Data quality (approximately 60 seconds)**

One or two figures showing that your data were suitable for analysis. A MultiQC read quality summary or STAR mapping rate chart is appropriate. State the key numbers: average reads per sample, mapping rate range. If any sample was notably lower quality and you retained it, briefly state why.

This slide exists to establish credibility for the downstream analysis. It is not the story — do not linger on it.

---

**Slides 6–7 — Primary results (approximately 2 minutes)**

These two slides present your main Stage 7 outputs and should be narrated as a pair.

**Slide 6 — PCA plot.** Interpret it, do not describe it. The assessor can see the axes. Tell them what the plot means: do the treatment and control groups separate along the major axis of variation? Do replicates cluster? What does this tell you about the reliability of the subsequent differential expression calls? A PCA plot that shows tight replicate clustering and clear group separation is evidence that the experiment worked; say so.

**Slide 7 — Volcano plot.** State the thresholds (FDR < 0.05, |log2FC| ≥ 0.263) and the outcome: total DEGs, number upregulated, number downregulated. If specific genes or regions of the volcano are relevant to the argument you will make on the next slides, point to them here. This primes the assessor for the biological argument to follow.

In both slides: interpret, do not describe. The assessor reads the figure; your narration should tell them what it means.

---

**Slides 8–9 — Your biological argument (approximately 3–4 minutes)**

This is the most important part of your presentation and should receive the most preparation and the most narration time.

**Slide 8 — The finding**

Present the specific genes or pathway you have chosen as your focus. For each key gene: name, direction of change, log2FC, FDR. If you are arguing at pathway level, show the relevant gProfiler result or a summary figure (heatmap, bar chart of log2FC values for the gene set). The figure here should be informative and clean — if the Galaxy gProfiler output is cluttered, consider producing a cleaner version using R or Excel.

State clearly what the pattern is. Is it a coherent cluster of genes all pointing in the same direction within a pathway, or is it a single gene with exceptional significance? The former is a stronger argument and you should say so.

**Slide 9 — The argument**

This slide carries the scientific weight of your entire presentation. It must contain:

- What the proteins encoded by your genes of interest do — stated briefly but precisely (one to two sentences per gene, or a description of the pathway function if arguing at that level)
- The proposed mechanism: how does this pattern of gene expression change plausibly contribute to the phenotype the experiment was studying? Walk through the chain of reasoning — gene change → protein function → pathway effect → phenotype
- The supporting evidence: name the key papers and what they found. "A study in murine fibroblasts showed that knockdown of [gene X] reduced [phenotype] by 40% [citation]" is the level of specificity required. Do not say "previous studies have shown" without naming the studies.
- A statement about the direction of change: is the up/downregulation consistent with your proposed mechanism? If there is any complexity — genes changing in an unexpected direction — address it directly rather than ignoring it

A presentation that lists genes without explaining protein function, or that names pathways without explaining what those pathways do in the cell, will not score well on scientific content regardless of slide design.

---

**Slide 10 — Comparison to the original paper (approximately 60–90 seconds)**

What did the original paper find, and how do your results compare? Cover:
- Points of agreement: findings that are consistent between your analysis and the original paper
- Any discrepancy between your DEG list and the paper's reported results, and the most likely methodological explanation
- Anything your analysis identified that the paper did not report

This slide demonstrates that your re-analysis is not merely a reproduction of the published work and that you can critically compare methodologies and findings.

---

**Slide 11 — Limitations and future directions (approximately 60–90 seconds)**

Two or three bullet points on the limitations most relevant to your specific argument — not a generic RNA-seq limitations list. Then one clearly stated proposed next experiment. Be specific: name the assay, the cell line, the measured outcome, and what result would support or refute your interpretation. Vague statements about "further validation" do not demonstrate understanding of what validation actually involves.

---

**Slide 12 — Summary (optional but recommended)**

Three bullet points maximum. What are the three things you want the assessor to remember? Lead with the biological conclusion, not the technical accomplishment. "We identified 342 DEGs" is not a conclusion. "Transcriptional upregulation of ER chaperones following [treatment] suggests increased secretory demand that may underlie the observed productivity improvement" is a conclusion.

---

### Narration Guidance

**Speak to your slides, not from them.** If the text on the slide and your narration are identical, the slide has too much text. Bullet points on slides should be short prompts; the narration is where you expand and explain.

**Interpret, do not describe.** The assessor can read your figures. Your narration should tell them what the figures mean in the context of your argument.

**Use precise language for data, hedged language for interpretation.** "We identified 87 significantly upregulated genes (FDR < 0.05)" is precise. "This suggests that [treatment] activates the UPR" is appropriately hedged. The distinction between what you measured and what you conclude should be audible.

**State numbers once, clearly.** Repeating the same number three times in one sentence does not add emphasis — it wastes time and sounds uncertain.

**Pace yourself.** 8–12 minutes for 10–12 slides is approximately 60–90 seconds per slide. Slides 8–9 will take longer; slides 1 and 5 will be shorter. Rehearse at least once to check your timing before recording.

**Record in a quiet space with good audio.** Check your microphone level before recording the final version. Poor audio quality will disadvantage your presentation regardless of its scientific content. A brief test recording of 30 seconds before the full recording will confirm your audio is clear.

---

## Self-Check Before Submitting Your Written Assessment

**Comparison to original paper:**
- [ ] I have identified what the original paper found and summarised it clearly
- [ ] I have compared my DEG counts and key findings to the paper's reported results
- [ ] I have explained any discrepancies with reference to methodological differences
- [ ] I have identified at least one finding in my analysis that adds to or extends the original paper

**Biological focus:**
- [ ] I have chosen a single main biological focus with clear justification
- [ ] My focus is supported by statistically significant results from my analysis
- [ ] My focus is directly connected to the phenotype the experiment was designed to study
- [ ] I have done a literature search and found at least 3 primary papers supporting my argument

**Mechanistic argument:**
- [ ] For my primary gene(s) of interest, I have stated protein function clearly
- [ ] I have described the mechanistic link between mRNA change → protein function → phenotype
- [ ] I have confirmed that the direction of change (up/down) is consistent with my argument
- [ ] I have acknowledged the mRNA-to-protein inference limitation at least once

**Cross-species evidence:**
- [ ] I have used human or mouse evidence as my primary literature support where CHO-specific publications do not exist (this is normal and expected in a small field)
- [ ] I have explicitly stated that evidence is from a different species and explained why the function is likely conserved in CHO
- [ ] I have not claimed an argument is weak simply because no CHO paper exists on this gene
- [ ] I have not over-extrapolated from distantly related species (yeast, insect, worm) without establishing the conservation

**Limitations:**
- [ ] I have acknowledged the mRNA ≠ protein limitation
- [ ] I have noted the mmusculus proxy in pathway analysis
- [ ] I have stated the number of biological replicates and acknowledged any power limitations
- [ ] I have acknowledged the single-time-point nature of RNA-seq
- [ ] I have proposed at least one specific experimental validation

**Conclusion:**
- [ ] My Conclusion is a separate section, not a paragraph appended to the Discussion
- [ ] The Conclusion states the primary biological finding in one sentence
- [ ] The Conclusion connects to the original paper in one sentence
- [ ] The Conclusion states the broader significance for biopharmaceutical manufacturing
- [ ] The Conclusion closes with a forward-looking statement (not a new argument or new citation)
- [ ] My Conclusion is 50–100 words

**Presentation:**
- [ ] My Discussion opens with the science, not a restatement of methods
- [ ] Each figure has a complete legend (title, description, key result)
- [ ] Every factual claim is cited
- [ ] Citations are to primary literature, not only reviews
- [ ] My word count is within 2,000–2,500 words

---

## Quick Reference: Key Databases and Resources

| Resource | URL | What to use it for |
|----------|-----|-------------------|
| UniProt | uniprot.org | Protein function, subcellular location, post-translational modification, curated literature |
| GeneCards | genecards.org | Quick gene overview, expression data, disease associations, cross-database links |
| PubMed | pubmed.ncbi.nlm.nih.gov | Primary literature search |
| STRING | string-db.org | Protein-protein interactions, co-expression, network visualisation |
| KEGG | genome.jp/kegg | Pathway maps, gene-to-pathway mapping |
| Reactome | reactome.org | Curated biochemical reaction pathways, good for mechanistic detail |
| Ensembl | ensembl.org | Gene models, orthologue identification, sequence conservation |
| gProfiler | biit.cs.ut.ee/gprofiler | Pathway enrichment (same tool used in Stage 7 — you can run custom queries here) |

---

## A Note on Academic Integrity

Your analysis is your own. The re-analysis pipeline you have run produces data no other student in the cohort has in exactly the same form — different sample collections, different count outputs, different DEG lists. The biological argument you construct from that data must be your own reasoning.

Using published papers to support your argument is not only permitted but required. Presenting the conclusions of a published paper as your own interpretation without citation is plagiarism. There is a clear difference between:

- *"Gene X is upregulated in our dataset (log2FC = 1.2, FDR = 0.003). This is consistent with [Author et al., Year], who showed that X is induced by [treatment type] in [cell type]."* — correct use of literature

- *"Gene X is upregulated in response to [treatment] and promotes [phenotype]."* (where the evidence for this claim comes entirely from [Author et al.] and you have not stated this) — incorrect, either because it implies this is your finding when it is not, or because the claim is uncited

All papers you read and use must appear in your reference list, formatted correctly.

---

*CHEN40140 MEngSc Biopharmaceutical Engineering Project — School of Chemical and Bioprocess Engineering, University College Dublin*

*This guide is intended to support independent scientific reasoning. The quality of your argument, the precision of your writing, and the depth of your engagement with the primary literature are the principal criteria by which this stage of your project will be assessed.*
