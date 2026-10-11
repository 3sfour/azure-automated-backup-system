# Azure Automated Backup System

> Copies a small business's files to Azure every night, keeps every version, moves old data to cheaper storage automatically, and emails the owner each morning to confirm the backup worked.

## The problem

Many small businesses rely on someone occasionally copying files to an external drive. When that person is busy or forgets, nothing is backed up, and when something goes wrong the data is gone.

## What this project does

- Stores files in **Azure Blob Storage** in separate containers: `documents`, `db-exports`, `app-files`.
- Enables **blob versioning and soft delete** so deleted or overwritten files can be recovered.
- Uses **lifecycle management** to move data to Cool after 30 days and Archive after 90 days, and to delete old versions.
- Uploads automatically every night with **AzCopy** (`scripts/`).
- A **Logic App** emails a morning confirmation ("312 files protected") or a warning if nothing was backed up.

## Architecture

```
Business computer --(AzCopy sync, nightly)--> Storage account
                                               |-- documents/
                                               |-- db-exports/
                                               |-- app-files/
                                               versioning + soft delete + lifecycle rules
Logic App (daily) --> counts files modified in the last 24h --> success or warning email
```

## Repository layout

```
.
├── infra/
│   └── lifecycle-policy.json   Lifecycle management rule
├── scripts/
│   ├── backup.sh               Nightly backup for Linux/macOS
│   ├── backup.ps1              Nightly backup for Windows
│   └── backup.env.sample       Settings template (copy to backup.env)
├── logic-app/
│   └── README.md               How to build the morning email workflow
└── docs/
    ├── decisions.md
    └── restore-runbook.md
```

## Setup

1. Create a resource group (for example `rg-backup-demo`) and a small safety budget.
2. Create a storage account (choose redundancy deliberately: LRS, ZRS or GRS) and three private containers.
3. Turn on soft delete (blobs and containers) and versioning under Data protection.
4. Apply `infra/lifecycle-policy.json` under Lifecycle management > Code view.
5. Install AzCopy, create a short-lived SAS token per container, copy `scripts/backup.env.sample` to `scripts/backup.env`, and fill it in.
6. Schedule the script (Task Scheduler on Windows, cron on Linux/macOS).
7. Build the Logic App (`logic-app/README.md`).
8. Do a restore test (`docs/restore-runbook.md`).

## Key decisions

Write a few lines about your choices: redundancy level, why 30/90 day tiers, how long to keep versions, how you handle secrets.

## Security

SAS tokens are passwords. Never commit `backup.env`, tokens or account keys. Keep expiries short, grant minimum permissions, and consider `azcopy login` with Microsoft Entra ID for production.

## Cost

Small test data costs pennies. Versions add storage, which is why old versions are deleted by lifecycle rule. Archive has early-deletion fees and restore delays. Delete the resource group when you finish.

## License

MIT
# azure-automated-backup-system
