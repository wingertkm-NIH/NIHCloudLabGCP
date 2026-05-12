# 02: Create a VM and SSH into it

Use these steps from Cloud Shell.

## 1) Set variables

```bash
export PROJECT_ID="$(gcloud config get-value project)"
export REGION="us-central1"
export ZONE="us-central1-a"
export VM_NAME="rna-seq-vm"
```

Expected output:
- `PROJECT_ID` should not be empty.

If empty, set it explicitly:

```bash
gcloud config set project YOUR_PROJECT_ID
export PROJECT_ID="YOUR_PROJECT_ID"
```

## 2) Create a small VM (<= 8 GB RAM)

```bash
gcloud compute instances create "$VM_NAME" \
  --project="$PROJECT_ID" \
  --zone="$ZONE" \
  --machine-type="e2-standard-2" \
  --boot-disk-type="pd-balanced" \
  --boot-disk-size="50GB" \
  --image-family="ubuntu-2204-lts" \
  --image-project="ubuntu-os-cloud" \
  --tags="http-server"
```

Expected output:
- Command finishes with a table containing the instance name and external IP.

Troubleshooting:
- If you see quota errors, try a different zone in the same region.
- If resource availability fails, switch to another zone (for example `us-central1-b`).

## 3) SSH into the VM

```bash
gcloud compute ssh "$VM_NAME" --zone="$ZONE"
```

Expected output:
- Prompt changes to something like `username@rna-seq-vm:~$`.

## 4) (Optional) Verify VM memory

Run on the VM:

```bash
free -h
```

Expected output:
- Total memory around `7.8Gi` to `8.0Gi`.
