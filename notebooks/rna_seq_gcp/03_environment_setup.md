# 03: Install analysis tools on the VM

Run all commands in your SSH session on the VM.

## 1) Update packages and install dependencies

```bash
sudo apt-get update
sudo apt-get install -y \
  wget curl unzip gzip bzip2 tar \
  fastqc default-jre python3 ca-certificates
```

Expected output:
- Installation completes without `E:` errors.

## 2) Install Salmon (binary)

```bash
cd ~
wget -O salmon.tar.gz \
  https://github.com/COMBINE-lab/salmon/releases/download/v1.10.3/salmon-1.10.3_linux_x86_64.tar.gz
tar -xzf salmon.tar.gz
echo 'export PATH="$HOME/salmon-latest_linux_x86_64/bin:$PATH"' >> ~/.bashrc
export PATH="$HOME/salmon-latest_linux_x86_64/bin:$PATH"
```

## 3) Verify tool versions

```bash
fastqc --version
salmon --version
```

Expected output:
- FastQC version string (for example `FastQC v0.11.9` or newer package build),
- Salmon version string (expected `salmon 1.10.3`).

Troubleshooting:
- If `salmon: command not found`, run:

```bash
export PATH="$HOME/salmon-latest_linux_x86_64/bin:$PATH"
```
