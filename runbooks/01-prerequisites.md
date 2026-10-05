# 01 - Prerequisites

## What is this?
Before doing any backup or restore work for a client, you need to gather some information first.
This document explains what you need and where to find it.

---

## Client Details
| Field | Value |
|-------|-------|
| Client Name | Brown Capital Management (BCM) |
| Client ID | 56 |
| Project Number | `epgwhfnlun` |
| S3 Folder UUID | `8f409127-b258-467e-969f-c9b91eefea9f` |

---

## Infrastructure Details
| Resource | Value |
|----------|-------|
| Jumpbox | `ue1p-jump-02.prd.kurtosys-internal.net` |
| Jumpbox IP | `10.128.22.249` |
| Jumpbox Instance ID | `i-0a8939c96fb1f1936` |
| Main RDS | `ue1p-dxm.ccj9eknkk7w9.us-east-1.rds.amazonaws.com` |
| Replica RDS (read-only) | `ue1p-dxm-repl.ccj9eknkk7w9.us-east-1.rds.amazonaws.com` |
| Region | `us-east-1` (Virginia) |

---

## BCM Database Details
| Environment | Database Name | URL |
|-------------|--------------|-----|
| DEV | `zjbcmemven_dev` | `https://zjbcmemven-dev.ksysweb.com` |
| STG | `zjbcmemven_stg` | `https://zjbcmemven-stg.ksysweb.com` |
| PRD | `zjbcmemven_prd` | `https://zjbcmemven-prd.ksysweb.com` |

---

## S3 Buckets
| Purpose | Bucket |
|---------|--------|
| BCM Media/Files (source) | `ksys-ue1p-dxm-content` |
| Backup destination | `ksys-ue1p-kapp-dbbackup` |

---

## How to find the database name for any client
If you don't know the database name, run this on the jumpbox:
```bash
mysql -h ue1p-dxm-repl.ccj9eknkk7w9.us-east-1.rds.amazonaws.com -uwp_db_master -p -e "SHOW DATABASES;" | grep -i <clientname>
```

## How to find the WordPress URL for any client
Once you have the database name, run:
```bash
mysql -h ue1p-dxm-repl.ccj9eknkk7w9.us-east-1.rds.amazonaws.com -uwp_db_master -p -e "SELECT option_value FROM <database_name>._wpoptions WHERE option_name = 'siteurl';"
```

---

## How to access the Jumpbox
1. Log into AWS Console
2. Go to **EC2 → Instances**
3. Search for `ue1p-jump-02`
4. Click **Connect → Session Manager**
5. Click **Connect**
