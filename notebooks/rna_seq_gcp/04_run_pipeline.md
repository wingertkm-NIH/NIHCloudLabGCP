# 04: Download data and run a simple RNA-seq pipeline

Run these commands on the VM.

## 1) Create a working directory

```bash
mkdir -p ~/rna_seq_tutorial/{data,ref,results,logs}
cd ~/rna_seq_tutorial
```

## 2) Download one small public RNA-seq sample

Dataset used in this tutorial:
- **SRA accession:** `SRR453566` (*S. cerevisiae*, single-end)
- Source: ENA FASTQ mirror

```bash
cd ~/rna_seq_tutorial/data
wget -O SRR453566.fastq.gz \
  https://ftp.sra.ebi.ac.uk/vol1/fastq/SRR453/006/SRR453566/SRR453566.fastq.gz
```

Expected output:
- `SRR453566.fastq.gz` appears in `~/rna_seq_tutorial/data`.

Quick check:

```bash
ls -lh ~/rna_seq_tutorial/data/SRR453566.fastq.gz
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
fastqc -o results data/SRR453566.fastq.gz
```

Expected output:
- Files like `SRR453566_fastqc.html` and `SRR453566_fastqc.zip` in `results/`.

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

## 6) Quantify expression with Salmon

```bash
cd ~/rna_seq_tutorial
salmon quant \
  -i ref/salmon_index \
  -l A \
  -r data/SRR453566.fastq.gz \
  -p 2 \
  --validateMappings \
  -o results/salmon_SRR453566 \
  2>&1 | tee logs/salmon_quant.log
```

Expected output:
- `results/salmon_SRR453566/quant.sf` is created.

Troubleshooting:
- If download fails, retry `wget` (temporary network issues are common).
- If VM runs out of disk, delete large temporary files and ensure boot disk is at least 30-50 GB.
- If Salmon is slow, that is normal on small VMs; wait for completion.
