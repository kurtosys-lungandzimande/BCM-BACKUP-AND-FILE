# BCM Full WordPress Backup Runbook
**Ticket:** DATA-XXXX  
**Client:** Brown Capital Management (BCM)  
**Client ID:** 56  
**Project Number:** epgwhfnlun  
**S3 Folder:** 8f409127-b258-467e-969f-c9b91eefea9f  

---

## Prerequisites

- AWS credentials for source (BCM) account
- Access to jumpbox via AWS Console Session Manager
- Ticket number confirmed

---

## Step 1 — Log into Jumpbox
Access the jump server via AWS Console using Session Manager.

---

## Step 2 — Set AWS Credentials
```bash
export AWS_ACCESS_KEY_ID="<access_key>"
export AWS_SECRET_ACCESS_KEY="<secret_key>"
export AWS_SESSION_TOKEN="<session_token>"
```

---

## Step 3 — SQL Dump (Database Backup)
```bash
cd /backups
./wordpress-maintenance.sh backup_remote
```

When prompted enter:
| Field         | Value                                              |
|---------------|----------------------------------------------------|
| Ticket Number | DATA-XXXX                                          |
| Source URL    | https://bcm-prd.ksysweb.com/                       |
| S3 URI        | s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX|

SQL dump will be compressed and uploaded to:
```
s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX/
```

---

## Step 4 — S3 Files Backup (Media / Assets)

### 4.1 — Create working directory
```bash
mkdir /tmp/DATA-XXXX
cd /tmp/DATA-XXXX
```

### 4.2 — Set SOURCE credentials (BCM account)
```bash
export AWS_ACCESS_KEY_ID="<source_access_key>"
export AWS_SECRET_ACCESS_KEY="<source_secret_key>"
export AWS_DEFAULT_REGION=eu-west-1
```

### 4.3 — Copy BCM S3 assets locally
```bash
aws s3 cp s3://<bcm-source-bucket>/8f409127-b258-467e-969f-c9b91eefea9f/ . --recursive
```

### 4.4 — Verify downloaded files
```bash
ls -lah
```

### 4.5 — Switch to DESTINATION credentials
```bash
export AWS_ACCESS_KEY_ID="<destination_access_key>"
export AWS_SECRET_ACCESS_KEY="<destination_secret_key>"
export AWS_DEFAULT_REGION=eu-west-1
```

### 4.6 — Upload to secure delivery bucket
```bash
aws s3 cp . s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX/8f409127-b258-467e-969f-c9b91eefea9f/ --recursive
```

### 4.7 — Verify upload
```bash
aws s3 ls s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX/ --recursive --human-readable
```

---

## Step 5 — Generate Secure Download Link (Presigned URL)
```bash
aws s3 presign s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX/ --expires-in 604800
```
> Link expires in **7 days**. Share with client immediately.

---

## Step 6 — Cleanup Temporary Files
```bash
rm -rf /tmp/DATA-XXXX
```

---

## Step 7 — Update Ticket

> Hi @Tracey Lundy, the full WordPress backup for Brown Capital Management (Client ID: 56) has been completed successfully. This includes:
> - ✅ SQL dump
> - ✅ WordPress files archive (themes, plugins, uploads)
> - ✅ S3 media assets (folder: 8f409127-b258-467e-969f-c9b91eefea9f)
>
> Secure download link: `[presigned URL]`
> ⚠️ Link expires in 7 days — please ask the client to confirm receipt.

---

## Notes
- Project Number: `epgwhfnlun`
- S3 Folder UUID: `8f409127-b258-467e-969f-c9b91eefea9f`
- All backups stored under: `s3://k-data-uk/Backups/WordPress/DXM_TEMP/DATA-XXXX/`
