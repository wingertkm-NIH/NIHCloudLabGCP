# RNA-seq on GCP (Compute Engine + Salmon): Overview

This tutorial shows a **beginner-friendly RNA-seq workflow** on Google Cloud Platform (GCP) using:
- a small Compute Engine VM,
- one small public RNA-seq sample,
- lightweight command-line tools.

You will run:
1. VM setup on Compute Engine,
2. tool installation,
3. read quality check,
4. transcript quantification with Salmon,
5. review and optional upload of results to Cloud Storage.

---

## What is RNA-seq?

RNA sequencing (RNA-seq) measures gene/transcript expression by sequencing RNA-derived fragments. In a simple workflow:
- raw reads (`.fastq.gz`) are downloaded,
- quality is checked (FastQC),
- reads are quantified against a reference transcriptome (Salmon),
- expression values are reported as counts and TPM.

---

## Dataset and reference used in this tutorial

This tutorial uses:
- **Public RNA-seq sample accession:** `SRR453566` (single-end; *Saccharomyces cerevisiae*),
- **Reference transcriptome:** Ensembl release 111 cDNA FASTA for *S. cerevisiae*,
- **Annotation:** Ensembl release 111 GTF for *S. cerevisiae*.

Why this choice:
- yeast reference is small,
- one sample keeps runtime/cost low,
- suitable for a VM with **<= 8 GB RAM**.

---

## Prerequisites

Assumptions:
- You are already authenticated to your GCP project (`gcloud auth login` done).
- You have `gcloud` installed (Cloud Shell or local terminal).
- Billing and Compute Engine API are enabled in your project.

---

## Expected runtime and cost (rough)

On `e2-standard-2` (2 vCPU, 8 GB RAM):
- setup + install: ~10-20 min,
- data download + QC + quantification: ~10-25 min.

Costs are typically low for a short run, but always check current GCP pricing for your region.
