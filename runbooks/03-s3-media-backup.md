# 03 - S3 Media & Files Backup

## What is this?
This step copies all BCM WordPress media files (images, uploads)
from the source S3 bucket to the backup/delivery S3 bucket.

## Flow
```
ksys-ue1p-dxm-content/epgwhfnlun/ → copy → ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/media/
```

---

## Step 1 — Copy BCM media files to backup bucket
```bash
aws s3 cp s3://ksys-ue1p-dxm-content/epgwhfnlun/ s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/media/ --recursive
```

> **Note:** You may see warnings like `no identity-based policy allows the s3:PutObjectTagging action` — this is normal, files are still copied successfully.

---

## Step 2 — Verify the copy completed
```bash
aws s3 ls s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/ --recursive --human-readable --summarize | tail -5
```

### Expected output
```
Total Objects: 2426
Total Size: 291.8 MiB
```

---

## Result
| | |
|--|--|
| Source | `s3://ksys-ue1p-dxm-content/epgwhfnlun/` |
| Destination | `s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/media/` |
| Total Files | 2,426 objects |
| Total Size | 291.8 MiB |
