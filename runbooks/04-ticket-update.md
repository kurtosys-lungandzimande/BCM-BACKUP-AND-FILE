# 04 - Ticket Update

## What is this?
Once the backup is complete, update the Jira ticket to inform the team and client.

---

## Ticket Details
| Field | Value |
|-------|-------|
| Ticket | `TECH-4327` |
| Client | Brown Capital Management (BCM) |

---

## Comment to post on the ticket

> Hi @Tracey Lundy, the full WordPress backup for Brown Capital Management (BCM) has been completed successfully. This includes:
>
> - ✅ SQL dump — `20261005_111349_zjbcmemven_prd_backup.sql.gz`
> - ✅ Media/files archive — 2,426 objects (291.8 MiB)
>
> All files are stored securely at:
> `s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/`
>
> Please let me know if a presigned download link is required for the client.

---

## How to generate a presigned download link (if requested)
A presigned URL allows the client to download files directly from S3 without needing AWS access.

```bash
# For the SQL dump
aws s3 presign s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/20261005_111349_zjbcmemven_prd_backup.sql.gz --expires-in 604800
```

> **Note:** `--expires-in 604800` = 7 days. Share the link with the client immediately and ask them to confirm receipt before it expires.
