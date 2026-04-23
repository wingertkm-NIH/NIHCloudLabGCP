# 05: Inspect outputs and optionally upload to Cloud Storage

## 1) Review generated files

```bash
cd ~/rna_seq_tutorial
find results -maxdepth 2 -type f | sort
```

You should see at least:
- `results/SRR453566_fastqc.html`
- `results/SRR453566_fastqc.zip`
- `results/salmon_SRR453566/quant.sf`
- `results/salmon_SRR453566/cmd_info.json`
- `results/salmon_SRR453566/lib_format_counts.json`

## 2) Preview quantification table

```bash
column -t results/salmon_SRR453566/quant.sf | head -n 10
```

`quant.sf` columns:
- `Name`: transcript ID,
- `Length`: transcript length,
- `EffectiveLength`: bias-corrected effective length,
- `TPM`: normalized abundance,
- `NumReads`: estimated number of reads.

## 3) Open FastQC report

If you are in Cloud Shell, you can copy the file locally or use built-in file browser download.
If you are on local terminal + SSH, use `scp` from a separate local terminal.

Example (`scp` from local machine):

```bash
gcloud compute scp \
  --zone="us-central1-a" \
  rna-seq-vm:~/rna_seq_tutorial/results/SRR453566_fastqc.html \
  ./SRR453566_fastqc.html
```

Then open `SRR453566_fastqc.html` in a web browser.

## 4) (Optional) Upload results to a GCS bucket

Set your bucket name and upload:

```bash
export BUCKET_NAME="gs://YOUR_BUCKET_NAME"
gsutil -m cp -r ~/rna_seq_tutorial/results "$BUCKET_NAME"/
```

Expected output:
- `Copying ...` messages and final completion summary.

## 5) Cleanup to avoid extra cost

From Cloud Shell/local terminal (not inside VM), delete the VM when done:

```bash
gcloud compute instances delete rna-seq-vm --zone=us-central1-a
```

Confirm with `y` when prompted.
