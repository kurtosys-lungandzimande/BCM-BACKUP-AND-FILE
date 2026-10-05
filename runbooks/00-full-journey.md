# 00 - Full Journey (How I Did the BCM Backup)

By Lunga Ndzimande  
Ticket: TECH-4327  
Date: 2026-10-05

---

## The Request
A client (Brown Capital Management) requested a full WordPress backup including:
- SQL database dump
- All media files and assets

---

## Step 1 — Got the Client Details from the Ticket
When a ticket comes in, the first thing you need is the client details.
These were provided on the ticket:

```
clientId       = 56
clientName     = BCM
projectNumber  = epgwhfnlun
s3Folder       = 8f409127-b258-467e-969f-c9b91eefea9f
```

> **Why do you need this?**
> The project number is used as the folder name in S3 where the media files are stored.
> The client ID helps identify the client in the database.

---

## Step 2 — Identified the Correct Jumpbox
There are multiple jumpboxes for different regions:

| Region | Jumpbox | S3 Bucket |
|--------|---------|-----------|
| UK | `ew1r-jump-01` | `s3://k-data-uk/...` |
| US East (Virginia) | `ue1p-jump-02` | `s3://ksys-ue1p-kapp-dbbackup/...` |

BCM is hosted in **US East (Virginia)** so I used `ue1p-jump-02`.

> **How to connect:**
> 1. Go to AWS Console → EC2 → Instances
> 2. Search for `ue1p-jump-02`
> 3. Click **Connect → Session Manager → Connect**

---

## Step 3 — Found the Database Name
I didn't know the database name so I searched for it on the jumpbox:

```bash
mysql -h$WP_DB_READONLY_HOST -uwp_db_master -p -e "SHOW DATABASES;" | grep -i bcm
```

Output:
```
zjbcmemven_dev
zjbcmemven_prd
zjbcmemven_stg
```

> **Why three databases?**
> Every client has three environments — DEV, STG (staging), and PRD (production).
> For a backup we always use PRD.

---

## Step 4 — Found the WordPress URL
The backup script needs the WordPress URL to derive the database name automatically.
I found it by querying the database:

```bash
mysql -h$WP_DB_READONLY_HOST -uwp_db_master -p -e "SELECT option_value FROM zjbcmemven_dev._wpoptions WHERE option_name = 'siteurl';"
```

Output:
```
https://zjbcmemven-dev.ksysweb.com
```

From this I could work out all environment URLs:

| Environment | Database | URL |
|-------------|----------|-----|
| DEV | `zjbcmemven_dev` | `https://zjbcmemven-dev.ksysweb.com` |
| STG | `zjbcmemven_stg` | `https://zjbcmemven-stg.ksysweb.com` |
| PRD | `zjbcmemven_prd` | `https://zjbcmemven-prd.ksysweb.com` |

> **Pattern:** just replace `-dev` with `-stg` or `-prd` in the URL.

---

## Step 5 — Ran the SQL Dump (Database Backup)
The script takes a full copy of the database and uploads it to S3.

```bash
cd /backups
./DBA/wordpress-maintenance.sh backup_remote
```

When prompted I entered:

| Prompt | Value |
|--------|-------|
| Ticket number | `TECH-4327` |
| Source URL | `https://zjbcmemven-prd.ksysweb.com/` |
| Destination URL | `https://zjbcmemven-prd.ksysweb.com/` |
| S3 URI | pressed Enter (left blank) |

> **Why same URL for source and destination?**
> Because we are doing a backup not a restore — source and destination are the same.

> **Why leave S3 URI blank?**
> The script already knows where to upload — it's hardcoded in the script for the US environment.

Output:
```
2026-10-05 11:13:49 - Performing backup of [zjbcmemven_prd]
-- Dump completed on 2026-10-05 11:13:50
-- File compress successful
-- File successfully copied to [s3://ksys-ue1p-kapp-dbbackup/.../TECH-4327/...]
```

---

## Step 6 — Copied Media Files (S3 Backup)
The media files (images, uploads) are stored in a separate S3 bucket.
I found the BCM folder using the project number `epgwhfnlun`:

```bash
aws s3 ls s3://ksys-ue1p-dxm-content/ | grep -i epgwhfnlun
```

Then copied everything to the backup bucket:

```bash
aws s3 cp s3://ksys-ue1p-dxm-content/epgwhfnlun/ s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/media/ --recursive
```

> **Note:** You may see tagging warnings — ignore them, files still copy successfully.

---

## Step 7 — Verified Everything
```bash
aws s3 ls s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/ --recursive --human-readable --summarize | tail -5
```

Output:
```
Total Objects: 2426
Total Size: 291.8 MiB
```

---

## Step 8 — Updated the Ticket
Posted this comment on TECH-4327:

> Hi @Tracey Lundy, the full WordPress backup for Brown Capital Management (BCM) has been completed successfully. This includes:
> - ✅ SQL dump — `20261005_111349_zjbcmemven_prd_backup.sql.gz`
> - ✅ Media/files archive — 2,426 objects (291.8 MiB)
>
> All files are stored securely at:
> `s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/`
>
> Please let me know if a presigned download link is required for the client.

---

## Key Lessons Learned
- Always confirm which jumpbox the client is on (UK or US) before starting
- Each jumpbox has its own script configured for its own region and S3 bucket
- The project number (`epgwhfnlun`) is the folder name in S3 for media files — not the UUID
- Always test commands in DEV or STG before running against PRD
- The `mysqldump password warning` is normal — not an error
- Tagging warnings during S3 copy are normal — files still copy successfully
