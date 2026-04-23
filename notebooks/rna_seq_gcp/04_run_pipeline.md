# 04: Download data and run a simple RNA-seq pipeline (two samples)

Run these commands on the VM.

## 1) Create a working directory

```bash
mkdir -p ~/rna_seq_tutorial/{data,ref,results,logs}
cd ~/rna_seq_tutorial
```

## 2) Download two small public RNA-seq samples

Datasets used in this tutorial:
- **SRA accession:** `SRR453566` (*S. cerevisiae*, single-end)
- **SRA accession:** `SRR453567` (*S. cerevisiae*, single-end)
- Source: ENA FASTQ mirror

```bash
cd ~/rna_seq_tutorial/data
wget -O SRR453566.fastq.gz \
  https://ftp.sra.ebi.ac.uk/vol1/fastq/SRR453/006/SRR453566/SRR453566.fastq.gz
wget -O SRR453567.fastq.gz \
  https://ftp.sra.ebi.ac.uk/vol1/fastq/SRR453/007/SRR453567/SRR453567.fastq.gz
```

Expected output:
- `SRR453566.fastq.gz` and `SRR453567.fastq.gz` appear in `~/rna_seq_tutorial/data`.

Quick check:

```bash
ls -lh ~/rna_seq_tutorial/data/SRR453566.fastq.gz ~/rna_seq_tutorial/data/SRR453567.fastq.gz
```

## 3) Download reference transcriptome + annotation (Ensembl)

```bash
cd ~/rna_seq_tutorial/ref
wget -O sacCer3.cdna.all.fa.gz \
  https://ftp.ensembl.org/pub/release-111/fasta/saccharomyces_cerevisiae/cdna/Saccharomyces_cerevisiae.R64-1-1.cdna.all.fa.gz
wget -O sacCer3.111.gtf.gz \
  https://ftp.ensembl.org/pub/release-111/gtf/saccharomyces_cerevisiae/Saccharomyces_cerevisiae.R64-1-1.111.gtf.gz
```

Optional sanity check:

```bash
zgrep -m 2 '^>' sacCer3.cdna.all.fa.gz
zgrep -m 2 -v '^#' sacCer3.111.gtf.gz
```

## 4) Run FastQC on raw reads

```bash
cd ~/rna_seq_tutorial
fastqc -o results data/SRR453566.fastq.gz data/SRR453567.fastq.gz
```

Expected output:
- Files like `SRR453566_fastqc.html`, `SRR453567_fastqc.html` and matching `.zip` files in `results/`.

## 5) Build Salmon index

```bash
cd ~/rna_seq_tutorial
salmon index \
  -t ref/sacCer3.cdna.all.fa.gz \
  -i ref/salmon_index \
  --threads 2 \
  2>&1 | tee logs/salmon_index.log
```

Expected output:
- `ref/salmon_index/` directory created,
- final log lines indicate indexing completed.

## 6) Quantify expression with Salmon for both samples

```bash
cd ~/rna_seq_tutorial
for SAMPLE in SRR453566 SRR453567; do
  salmon quant \
    -i ref/salmon_index \
    -l A \
    -r data/${SAMPLE}.fastq.gz \
    -p 2 \
    --validateMappings \
    -o results/salmon_${SAMPLE} \
    2>&1 | tee logs/salmon_quant_${SAMPLE}.log
done
```

Expected output:
- `results/salmon_SRR453566/quant.sf` is created.
- `results/salmon_SRR453567/quant.sf` is created.

## 7) Create transcript-to-gene mapping table (`tx2gene.tsv`)

Run this command from `~/rna_seq_tutorial`:

```bash
zcat ref/sacCer3.111.gtf.gz \
  | awk 'BEGIN{FS="\t"; OFS="\t"}
         $3=="transcript" {
           tid=""; gid="";
           if (match($9, /transcript_id "[^"]+"/)) {
             tid=substr($9, RSTART+15, RLENGTH-16)
           }
           if (match($9, /gene_id "[^"]+"/)) {
             gid=substr($9, RSTART+9, RLENGTH-10)
           }
           if (tid!="" && gid!="") print tid, gid
         }' \
  | sort -u > results/tx2gene.tsv
```

Expected output:
- `results/tx2gene.tsv` is created as a two-column tab-delimited file:
  - column 1: transcript ID
  - column 2: gene ID

Quick check:

```bash
head -n 5 results/tx2gene.tsv
wc -l results/tx2gene.tsv
```

Troubleshooting:
- If download fails, retry `wget` (temporary network issues are common).
- If VM runs out of disk, delete large temporary files and ensure boot disk is at least 30-50 GB.
- If Salmon is slow, that is normal on small VMs; wait for completion.
