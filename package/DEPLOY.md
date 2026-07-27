# User Guide: Deploying nettacker-arm64

This guide walks you through downloading the deployment package, verifying its signature, extracting it, and running it via Docker Compose on an **ARM64** environment.

---

## Prerequisites
Ensure you have the following installed on your system:
- `wget` or `curl`
- `gpg` (GNU Privacy Guard)
- `docker` and `docker-compose` (or the newer `docker compose` plugin)

---

## Step 1: Download the Package and Signature Files

Download the application package along with its signature file (`.sig`) and the public key used to verify the integrity of the release.

```bash
# Download the compressed tarball
wget https://github.com/deeprooter/nettacker-arm64/blob/main/package/nettacker_image.tar.gz

# Download the detached signature file
wget https://github.com/deeprooter/nettacker-arm64/blob/main/package/nettacker_image.tar.gz.sigstore.json

# Download the developer's public key
wget https://github.com/deeprooter/nettacker-arm64/blob/main/package/cosign.pub
```

---

## Step 2: Verify the Signatures

Before extracting, verify that the package has not been tampered with.
**Verify the archive file:**
   ```bash
   cosign verify-blob --key cosign.pub --bundle nettacker_image.tar.gz.sigstore.json nettacker_image.tar.gz
   ```
   *If successful, the output will explicitly print `Verified OK`.*

---

## Step 3: Gunzip and Untar the Package

Decompress and extract the archive to access the deployment files.

```bash
tar -xzvf nettacker-arm64.tar.gz
```

---

## Step 4:  See README.md for next steps.

