# 05: Inspect outputs and optionally upload to Cloud Storage

## 1) Review generated files

```bash
cd ~/rna_seq_tutorial
find results -maxdepth 2 -type f | sort
```

You should see at least:
- `results/SRR453566_fastqc.html`
- `results/SRR453566_fastqc.zip`
- `results/SRR453567_fastqc.html`
- `results/SRR453567_fastqc.zip`
- `results/salmon_SRR453566/quant.sf`
- `results/salmon_SRR453567/quant.sf`
- `results/tx2gene.tsv`

## 2) Preview quantification tables and tx2gene mapping

```bash
column -t results/salmon_SRR453566/quant.sf | head -n 10
column -t results/salmon_SRR453567/quant.sf | head -n 10
column -t results/tx2gene.tsv | head -n 10
```

`quant.sf` columns:
- `Name`: transcript ID,
- `Length`: transcript length,
- `EffectiveLength`: bias-corrected effective length,
- `TPM`: normalized abundance,
- `NumReads`: estimated number of reads.

`tx2gene.tsv` columns:
- column 1: transcript ID (`Name` in `quant.sf`),
- column 2: gene ID used for transcript-to-gene aggregation.

## 3) Open FastQC reports

If you are in Cloud Shell, you can copy the file locally or use built-in file browser download.
If you are on local terminal + SSH, use `scp` from a separate local terminal.

Example (`scp` from local machine):

```bash
gcloud compute scp \
  --zone="us-central1-a" \
  rna-seq-vm:~/rna_seq_tutorial/results/SRR453566_fastqc.html \
  ./SRR453566_fastqc.html

gcloud compute scp \
  --zone="us-central1-a" \
  rna-seq-vm:~/rna_seq_tutorial/results/SRR453567_fastqc.html \
  ./SRR453567_fastqc.html
```

Then open each `.html` report in a web browser.

## 4) Next step: analyze at gene level in Python

Use the companion notebook:
- `notebooks/rna_seq_analysis.ipynb`

This notebook loads:
- `results/salmon_SRR453566/quant.sf`
- `results/salmon_SRR453567/quant.sf`
- `results/tx2gene.tsv`

and demonstrates beginner-friendly gene-level exploration and visualization.

## 5) (Optional) Upload results to a GCS bucket

Set your bucket name and upload:

```bash
export BUCKET_NAME="gs://YOUR_BUCKET_NAME"
gsutil -m cp -r ~/rna_seq_tutorial/results "$BUCKET_NAME"/
```

Expected output:
- `Copying ...` messages and final completion summary.

## 6) Cleanup to avoid extra cost

From Cloud Shell/local terminal (not inside VM), delete the VM when done:

```bash
gcloud compute instances delete rna-seq-vm --zone=us-central1-a
```

Confirm with `y` when prompted.
