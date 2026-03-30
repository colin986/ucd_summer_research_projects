# Getting Started with Galaxy
## A Complete Beginner's Guide to usegalaxy.eu

---

This guide is for students who have **never used Galaxy before**. Complete this guide before attempting any workflow stage. It should take approximately 30–45 minutes.

---

## What Is Galaxy?

Galaxy is a free, web-based platform for running bioinformatics analyses without needing to write code or install software on your own computer. You work through a web browser — all computation happens on remote servers.

Think of Galaxy as a laboratory bench for data analysis: you upload your raw data, apply tools to it in sequence, and Galaxy keeps a complete record of everything you did and every result you produced. This record (called a **history**) is the computational equivalent of a lab notebook.

You will use Galaxy throughout your project to process and analyse RNA-seq data. Your supervisor has built workflows (automated sequences of tools) that you will run — but understanding how Galaxy works will help you troubleshoot problems, verify results, and make progress independently.

---

## Step 1: Create Your Account

1. Go to **usegalaxy.eu** in your web browser (Chrome or Firefox recommended)
2. Click **Login or Register** (top menu bar)
3. Click **Register here**
4. Fill in your details using your **UCD email address**
5. Check your email for a confirmation link and click it
6. Log in with your new credentials

> **Important:** usegalaxy.eu is a **free public server** run by the University of Freiburg. It is shared by researchers worldwide. Jobs may queue behind other users' work — this is normal, not an error. Do not create multiple accounts to try to skip the queue.

---

## Step 2: Understand the Interface

When you log in, you see three panels:

```
┌──────────────┬────────────────────────┬──────────────┐
│              │                        │              │
│  TOOL PANEL  │     MAIN PANEL         │   HISTORY    │
│  (left)      │     (centre)           │   (right)    │
│              │                        │              │
│  Search for  │  Shows tool forms,     │  Shows your  │
│  and select  │  results, workflows,   │  datasets    │
│  tools here  │  and visualisations    │  and their   │
│              │                        │  status      │
│              │                        │              │
└──────────────┴────────────────────────┴──────────────┘
```

- **Tool panel (left):** Search for and select analysis tools. You will mostly use workflows rather than individual tools, but it is useful to know where tools live.
- **Main panel (centre):** Where tool configuration forms, results, and information are displayed.
- **History panel (right):** Your work log. Every dataset you upload or generate appears here as a numbered item. This is the most important panel to monitor.

---

## Step 3: Understanding Datasets and Their States

Every file in Galaxy — whether you uploaded it or a tool created it — is a **dataset** in your history. Each dataset has a coloured bar indicating its state:

| Colour | State | What it means | What to do |
|---|---|---|---|
| **Grey** | Queued | Waiting to run — the server is busy with other jobs | Wait. Do not re-submit. |
| **Yellow** | Running | Currently being processed | Wait. This can take minutes to hours depending on the tool and data size. |
| **Green** | Complete | Finished successfully | Click the eye icon to preview, or the dataset name to expand details. |
| **Red** | Error | The job failed | Click the dataset to read the error message. Consult the troubleshooting guide. |
| **Light blue/grey** | Paused/Waiting | Waiting for a previous step to complete | Normal in workflows — the next step runs automatically when the previous one finishes. |

**Key actions on a dataset:**
- **Eye icon** — preview the contents in the main panel
- **Pencil icon** — edit attributes (name, datatype, metadata)
- **X icon** — delete the dataset (use with caution)
- **Info icon (i)** — see full details including tool parameters, input/output connections, and error logs
- **Download icon** — download the file to your computer
- Click the **dataset name** to expand/collapse the detail view

---

## Step 4: Upload Your First File

Let's practise uploading a file so you are familiar with the process before running a workflow.

1. Click the **Upload Data** button (up arrow icon, top of the tool panel)
2. A dialog box appears. Click **Choose local files**
3. Select any small text file from your computer (or create a test file with a few lines of text)
4. **Important:** Check the **Type** dropdown — Galaxy tries to auto-detect the file format, but it sometimes gets it wrong. For the workflow input files, you will need to set this manually (e.g., to "tabular")
5. Click **Start**
6. Close the upload dialog
7. Your file appears in the history panel. Wait for it to turn green.
8. Click the **eye icon** to preview it — verify the contents look correct

**If the file looks wrong** (e.g., all data in one column, or strange characters), the datatype may be set incorrectly. Click the **pencil icon** → **Datatypes** tab → change to the correct type → **Save**.

---

## Step 5: Understanding Collections

A **collection** is Galaxy's way of grouping related datasets together. When you download RNA-seq data, you typically get one FASTQ file per sample (single-end) or a pair of files per sample (paired-end). Rather than cluttering your history with dozens of individual files, Galaxy groups them into a collection.

- **List collection:** A simple group of datasets (used for SE data — one file per sample)
- **List of pairs collection:** A group where each entry contains a forward and reverse file (used for PE data)

When you click a collection name in your history, it expands to show the individual datasets inside. You can click into any individual file to preview it.

Most Galaxy tools are designed to work on collections — they automatically process every file in the collection. This means you run a tool once and it processes all 6, 12, or 24 samples.

---

## Step 6: Understanding Workflows

A **workflow** is a pre-built sequence of tools that runs automatically. Your supervisor has created workflows for each stage of the analysis pipeline. When you run a workflow:

1. You provide the input(s) — typically one or two files
2. Galaxy runs each tool in sequence, passing outputs from one tool as inputs to the next
3. Intermediate files may be hidden automatically to keep your history clean
4. The final output(s) appear in your history

**To import a workflow:**
1. Top menu → **Workflow**
2. Click **Import** (top right)
3. Upload the `.ga` file provided by your supervisor
4. It appears in your workflow list — you only need to import it once

**To run a workflow:**
1. Top menu → **Workflow**
2. Click the **play button (▶)** next to the workflow name
3. A form appears showing the required inputs — select the correct dataset(s) from your history
4. Click **Run Workflow**
5. Monitor your history as datasets appear and change state

---

## Step 7: Managing Your History

Your history is your computational lab notebook. Keep it organised.

**Rename your history:**
- Click the history name at the top of the history panel (it defaults to "Unnamed history")
- Type a descriptive name, e.g., "Stage 1 — Data Download" or "Project_SRA_Download"

**Create a new history:**
- Click the **+** icon at the top of the history panel
- This gives you a clean workspace. Your old history is not deleted — it is saved.

**Find old histories:**
- Top menu → **History** → **Saved Histories** (or **Histories** in some Galaxy versions)

**Good practice:**
- Use one history per stage of the analysis (or at least name clearly)
- Do not delete datasets you might need later — hide them instead (right-click → Hide)
- If your storage quota is full, delete datasets from test runs you no longer need

---

## Step 8: Checking Your Storage Quota

usegalaxy.eu gives each user a limited amount of storage (typically 250 GB, but this may change). RNA-seq data is large — especially paired-end data.

**To check your quota:**
1. Click your username in the top menu bar
2. Select **Preferences**
3. Look for **Disk usage** or storage information

**If you are running low:**
- Delete datasets from test runs or failed jobs
- Permanently delete items from the trash (History → Deleted datasets → Purge)
- Compress large output files if possible

---

## Step 9: Where to Find Help Within Galaxy

Galaxy has built-in help at multiple levels:

- **Tool help text:** When you open any tool, scroll down — there is usually a description of what the tool does, what the inputs mean, and what the outputs are
- **Galaxy Training Network (training.galaxyproject.org):** Free, high-quality tutorials on many topics including RNA-seq. These are written for beginners.
- **Galaxy Help forum (help.galaxyproject.org):** A Q&A forum. Search for your error message — someone has probably encountered it before.
- **Tool shed documentation:** Each tool links to its original documentation. Click the tool name in any completed dataset's info page to find it.

---

## Checklist: You Are Ready to Start Stage 1 When...

- [ ] You have an account on usegalaxy.eu and can log in
- [ ] You can identify the three panels (tool, main, history)
- [ ] You have successfully uploaded a test file and can preview it
- [ ] You understand what green, yellow, grey, and red mean for datasets
- [ ] You know how to import a workflow (.ga file)
- [ ] You know how to rename your history
- [ ] You know where to find your saved histories
- [ ] You have read the "How to Get Help" guide (separate document)

---

*CHEN40140 Biopharmaceutical Engineering Project — Dr Colin Clarke, School of Chemical and Bioprocess Engineering, University College Dublin*
