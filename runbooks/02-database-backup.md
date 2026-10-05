# 02 - Database Backup (SQL Dump)

## What is this?
This step takes a full copy (dump) of the BCM WordPress database directly from the RDS server
and uploads it to S3 as a compressed file.

## Flow
```
RDS Replica → mysqldump → .sql file → compress (.sql.gz) → upload to S3
```

---

## Step 1 — Log into the Jumpbox
1. Log into AWS Console
2. Go to **EC2 → Instances**
3. Search for `ue1p-jump-02`
4. Click **Connect → Session Manager → Connect**

---

## Step 2 — Navigate to the backups folder
```bash
cd /backups
```

---

## Step 3 — Run the backup script
```bash
./DBA/wordpress-maintenance.sh backup_remote
```

---

## Step 4 — Enter the prompted values

| Prompt | Value to enter |
|--------|---------------|
| Jira ticket number | `TECH-4327` |
| Source URL | `https://zjbcmemven-prd.ksysweb.com/` |
| Destination URL | `https://zjbcmemven-prd.ksysweb.com/` |
| S3 URI | press **Enter** (leave blank) |

> **Note:** Source and Destination URL are the same because we are doing a backup, not a restore.

---

## Step 5 — Expected output
```
2026-10-05 11:13:49 - Performing backup of [zjbcmemven_prd]
mysqldump: [Warning] Using a password on the command line interface can be insecure.
-- Dump completed on 2026-10-05 11:13:50

2026-10-05 11:13:50 - Compress and upload to s3
-- File compress successful
-- File successfully copied to [s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/...]
```

> **Note:** The `mysqldump password warning` is normal — ignore it.

---

## Result
| | |
|--|--|
| File | `20261005_111349_zjbcmemven_prd_backup.sql.gz` |
| S3 Location | `s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/` |
