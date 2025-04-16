---
layout: post
title: "How I Transferred a 2TB Backblaze B2 Snapshot to AWS Glacier for $2"
date: 2025-04-16
tags: [aws, glacier, s3, backblaze, rclone, devops]
---

If you're storing large backups in **Backblaze B2** but want to archive them long-term into **AWS S3 Glacier Deep Archive**, this guide will walk you through exactly how I did that — **for just around $2**.

---

## 🗺️ Why Move from Backblaze to Glacier?

- ✅ Glacier Deep Archive is the **cheapest long-term cloud storage** on AWS
- ✅ Redundant and geographically distinct storage option
- ✅ Archiving large B2 snapshots without downloading to your local machine

---

## ⚠️ Region Matters: Use `us-west-1`

Before spinning up any AWS resources, **make sure your AWS resources are in the same region as your Backblaze B2 account**.  
In my case, both were located in **`us-west-001`**, so I used the **`us-west-1`** AWS region to keep latency (and costs) down.

---

## 🧰 What You’ll Need

- An EC2 instance (no block storage needed)
- An S3 bucket with **Glacier Deep Archive** storage class
- [Rclone](https://rclone.org/) installed
- Access credentials for both B2 and AWS
- You have already taken a snapshot of your B2 bucket via the Backblaze B2 web portal
---

## 🏗️ Setup Summary

1. **Create a t3.medium EC2 instance** in `us-west-1` with Amazon Linux 2
2. SSH into it and install `rclone`
3. Configure `rclone` with both B2 and S3 remotes:
    ```bash
    rclone config
    ```
    in this example, I named B2 as b2remote and S3 as awsremote.
    **Make sure you choose Glacier Deep Archine when configuring the S3 remote**   
4. Launch the transfer in a `tmux` session:
    ```bash
    tmux new -s b2transfer          
    rclone copy b2remote:your-bucket-name/your-snapshot.zip awsremote:your-glacier-bucket --progress --transfers=8  --checkers=16  --fast-list  --ignore-times  --size-only
    ```
5. This transfer might take a while, so to make sure the transfer is running in the background, do the following: `Ctrl + B`, then `D`.
6. You can now log out from the EC2 instance. You can revisit the progress later on via `tmux attach -t b2transfer`
7. To check whether the transfer is already done, just visit the S3 bucket and check if the zip file is already there
---

## ⏱️ Transfer Time & Performance

Transferring **1.9 TB** of data took:

> ⏱️ `14h 46m 48.6s` at an average of **~47 MB/s**

Despite using the same region, there were notable network patterns:

- First hour: ~10 GB/hr transfer rate  
- Remaining hours: dropped significantly to ~1 GB/hr

This may suggest throttling or region-crossing backend traffic between B2 and S3.

---

## 💸 Total Cost Breakdown

| Resource     | Cost   | Notes                                      |
|--------------|--------|--------------------------------------------|
| EC2 compute  | ~$2.00 | `t3.medium` (14h runtime, no block storage)|
| S3 storage   | Varies | Glacier Deep Archive is ~$1.00/TB/month    |

> 💡 No EBS volume was attached — I streamed directly with `rclone`


---

## 🧼 Cleanup Steps

Once done, don’t forget to:

- Stop or terminate your EC2 instance
- Delete any temporary files or logs
- Optionally, delete the original B2 snapshot if archived

---

## 🧪 Optional: Verify Integrity

`rclone` is reliable, but it’s good practice to verify your upload later:

```bash
rclone md5sum b2remote:your-snapshot.zip > original.md5
rclone md5sum awsremote:your-snapshot.zip > glacier.md5
diff original.md5 glacier.md5
