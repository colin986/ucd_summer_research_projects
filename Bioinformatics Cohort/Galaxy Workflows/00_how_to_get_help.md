# How to Get Help
## A Self-Help Guide for Making Progress Independently

---

You are one of 25 students on this project. Your supervisor manages the entire cohort and **will not be available for individual meetings**. Supervision takes place in **group sessions**. Between sessions, you are expected to make progress independently using the resources provided.

This is by design: learning to solve problems independently — reading documentation, diagnosing errors, searching for answers — is one of the most important skills you will develop during this project, and one of the most valued by employers.

This guide tells you exactly what to do when you get stuck.

---

## The Help Hierarchy

Work through these steps **in order**. Do not skip to step 5.

---

### Step 1: Re-read the Student Guide for Your Current Stage

The student guide for each stage contains step-by-step instructions, expected outputs, and common pitfalls. The answer to most questions is already in the guide.

Before asking for help, confirm:
- [ ] I have read the full student guide for this stage (not just skimmed it)
- [ ] I have checked the "Verify the output" section to confirm what I should see
- [ ] I have checked the "Features to Note" or "Self-Check" section
- [ ] I have compared my output to what the guide describes

---

### Step 2: Check the Troubleshooting Guide for Your Current Stage

Every stage has a troubleshooting guide that covers common errors and their fixes. Most problems you will encounter have been anticipated.

- Use the **Quick Diagnosis table** at the top to find the section relevant to your symptom
- Follow the fix described
- If the fix involves re-running a workflow, do so before concluding the problem is unsolvable

---

### Step 3: Check the Reference Documents

If you encounter a term, concept, or file format you do not understand:

1. Check the **Key Concepts: RNA-seq for Non-Biologists** document (in the Galaxy Workflows folder)
2. Check the **Glossary** at the bottom of that document
3. Check the **Getting Started with Galaxy** guide if the issue is about Galaxy's interface or features

---

### Step 4: Search External Resources

If the guides and troubleshooting documents do not resolve your problem:

**For Galaxy-specific issues:**
- **Galaxy Training Network** — training.galaxyproject.org — free, beginner-friendly tutorials. The RNA-seq tutorials are particularly relevant.
- **Galaxy Help Forum** — help.galaxyproject.org — search for your error message. Someone has almost certainly encountered it before.
- **Galaxy tool help text** — every tool in Galaxy has a help section at the bottom of its configuration form. Read it.

**For biology/bioinformatics concepts:**
- **NCBI tutorials** — ncbi.nlm.nih.gov/guide/training-tutorials/ — tutorials on SRA, GEO, and public databases
- **Galaxy Training Network RNA-seq tutorials** — comprehensive walkthroughs of the entire pipeline

**When searching for an error:**
- Copy the **exact error message** text from Galaxy (from the dataset info or bug report)
- Paste it into Google or the Galaxy Help Forum search
- Look for results from help.galaxyproject.org, biostars.org, or the Galaxy mailing list
- Pay attention to the **date** of the result — bioinformatics tools change frequently, so prefer recent answers

---

### Step 5: Post to the Class Forum

If steps 1–4 have not resolved your issue, post to the **class Brightspace discussion forum** (or whatever class communication channel your supervisor has set up).

**Why post publicly rather than emailing your supervisor?**
- Other students may have solved the same problem already
- Other students encountering the same problem will find your post and the answer
- Your supervisor can answer once and all 25 students benefit
- This mirrors professional practice — software engineers, data scientists, and researchers post questions on forums, not private emails

**How to write a good forum post:**

```
Subject: [Stage X] Brief description of the problem

What I was trying to do:
[e.g., "Run the Stage 1 SE download workflow"]

What happened:
[e.g., "The fasterq-dump step turned red after 20 minutes"]

Error message:
[Paste the exact error text from Galaxy]

What I have already tried:
[e.g., "Checked troubleshooting guide — verified accession numbers
are correct on SRA. Re-ran the workflow — same error."]

Screenshot:
[Attach a screenshot of the red dataset with error details visible]
```

**A post that says "it doesn't work" with no detail cannot be answered.** The more specific you are, the faster you will get help.

---

### Step 6: Bring It to the Group Supervision Session

If the issue remains unresolved, bring it to the next scheduled group supervision session. Prepare:

1. A clear description of the problem
2. The error message (screenshot or text)
3. What you already tried (steps 1–5)

Your supervisor will address common issues that multiple students have encountered. If your issue is unique, they will help you in the session.

---

## When to Email Your Supervisor Directly

Email your supervisor **only** for:

- [ ] You cannot access Galaxy at all (account issues, platform outage lasting >24 hours)
- [ ] You believe your assigned dataset has been withdrawn from SRA and you need a replacement
- [ ] You have discovered your data is a different type than expected (e.g., PE when you were told SE) and need a different workflow
- [ ] A personal circumstance affecting your ability to work (extensions, extenuating circumstances)
- [ ] You have a deadline-critical issue that cannot wait for the next group session

For all technical and analytical problems, use steps 1–5 first.

---

## Common Questions That Are Already Answered in the Guides

Before posting or asking, check whether your question is on this list:

| Question | Where the answer is |
|---|---|
| "What is a FASTQ file?" | Key Concepts guide → Part 2 |
| "How do I upload a file to Galaxy?" | Getting Started guide → Step 4 |
| "My dataset is red — what do I do?" | Stage troubleshooting guide → Job Failed section |
| "How long should the download take?" | Stage student guide → Step 4 (Monitor) |
| "What is the difference between SE and PE?" | Key Concepts guide → Part 2; Stage 1 student guide |
| "My file looks wrong after upload" | Getting Started guide → Step 4; Troubleshooting → Upload section |
| "How do I find my old history?" | Getting Started guide → Step 7 |
| "What should my output look like?" | Stage student guide → Step 5 (Verify the output) |
| "How do I write the Methods for this stage?" | Stage student guide → For Your Thesis Methods Section |

---

## A Note on Learning

You will encounter errors. Tools will fail. Downloads will be slow. Results will look different from what you expected. This is normal — it happens to experienced bioinformaticians too.

The difference between a student who makes good progress and one who gets stuck is not intelligence or prior knowledge — it is the willingness to read the error message, search for the answer, try a fix, and repeat. Every problem you solve independently makes the next one easier.

Your supervisor is here to guide you, not to do the work for you. The resources in this folder are designed to give you everything you need to progress between group sessions. Use them.

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
