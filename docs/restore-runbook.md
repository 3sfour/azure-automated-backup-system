# Restore runbook

Test each of these before you trust the system. A backup you have never restored is only a hope.

## Restore a previous version of a file
1. Portal > Storage account > Containers > open the container > select the blob.
2. Open the **Versions** tab, choose an older version, then **Make current version**.

## Undelete a deleted file
1. In the container, turn on **Show deleted blobs**.
2. Select the file, choose **Undelete**.

## Restore a file from Archive
Archived blobs cannot be read until rehydrated.
1. Select the blob > **Change tier** > Hot or Cool.
2. Choose a rehydrate priority. This can take hours (Standard) or less with High priority at higher cost.
3. When the tier shows Hot or Cool, download the file.

## Roll back a whole container (if point-in-time restore is enabled)
Storage account > Data protection > Point-in-time restore, then choose a restore point.

## Record your tests
| Date | What I restored | How long it took | Result |
|------|-----------------|------------------|--------|
|      |                 |                  |        |
