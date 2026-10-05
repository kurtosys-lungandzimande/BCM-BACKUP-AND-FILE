# BCM Backup & Files — TECH-4327

Personal documentation and runbooks for the Brown Capital Management (BCM) WordPress backup request.

## Ticket
**TECH-4327** — [Brown Capital Management] Request full WordPress backup (files & SQL dump)

## Runbooks
| # | Document | Description |
|---|----------|-------------|
| 01 | [Prerequisites](runbooks/01-prerequisites.md) | Client details, infrastructure, and how to find database info |
| 02 | [Database Backup](runbooks/02-database-backup.md) | How to run the SQL dump and upload to S3 |
| 03 | [S3 Media Backup](runbooks/03-s3-media-backup.md) | How to copy media files to the backup bucket |
| 04 | [Ticket Update](runbooks/04-ticket-update.md) | What to post on the ticket when done |

## Scripts
| File | Description |
|------|-------------|
| [wordpress-maintenance.sh](wordpress-maintenance.sh) | Main backup/restore script (reference copy from jumpbox) |

## Backup Summary
| | |
|--|--|
| Date | 2026-10-05 |
| SQL Dump | `20261005_111349_zjbcmemven_prd_backup.sql.gz` |
| Media Files | 2,426 objects (291.8 MiB) |
| S3 Location | `s3://ksys-ue1p-kapp-dbbackup/Backups/WordPress/ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com/DXM_TEMP/TECH-4327/` |
