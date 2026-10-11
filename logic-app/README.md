# Morning confirmation Logic App

Create a **Logic App (Consumption)** named `la-backup-report`.

1. Trigger: **Recurrence**, every 1 day (for example 7:00 AM, set your time zone).
2. Action: **Azure Blob Storage > List blobs (V2)** for the `documents` folder. Repeat for the other containers if you want combined counts.
3. Action: **Filter array**. From: the `value` output of List blobs. Condition (advanced mode):

```
@greater(ticks(item()?['LastModified']), ticks(addDays(utcNow(), -1)))
```

4. Action: **Condition**: `length(body('Filter_array'))` is greater than `0`.
5. If yes: **Send an email (V2)**

```
Subject: Backup completed successfully
Body: Last night's backup completed successfully.
@{length(body('Filter_array'))} files protected.
```

6. If no: **Send an email (V2)**

```
Subject: WARNING - no files backed up in the last 24 hours
Body: Please check the backup computer and its schedule.
```

The warning email is the most important part. A backup that silently stops is worse than no backup.

Note: AzCopy sync keeps the original file modified time, so unchanged files are not uploaded and will not be counted.

Do not commit connection secrets. Export the Logic App definition (Code view) with secrets removed if you want to share it.
