---
title: "Creating a VM on Microsoft Azure"
date: 2021-08-16
tags: ["cloud", "azure", "ssh"]
---

## What I Did

I needed a cloud server to test things without using my local machine. I spun up an **Ubuntu 20.04 VM** on Azure and connected via SSH. Here's the exact process I followed.

## Create the VM

1. Log in to [Azure](https://portal.azure.com)
2. Click **Create a Resource**
3. Pick **Ubuntu 20.04 LTS** → **Create**
4. Name the VM
5. Under **Administration Account**, choose **SSH Public Key**
6. Set a username and key pair name
7. Under **Inbound Port Rules**, open **SSH (22)** and **HTTP (80)**
8. Click **Review and Create** → **Create**
9. Download the **key pair** file when prompted
10. Wait for deployment, then click **Go to Resource**

## Connect via SSH

```sh
cd ~/Downloads
chmod 400 pair_key_name.pem
ssh -i pair_key_name.pem username@your_ip
```

Type `yes` when SSH asks about the fingerprint.

## Log Out

```sh
logout
```

## Done

That's it — a running Ubuntu box in the cloud, reachable from my terminal. I use this kind of setup when I need a clean Linux environment for testing.
