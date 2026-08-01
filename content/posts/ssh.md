---
title: "SSH: How I Connect to Remote Servers"
author: "Saad"
date: 2021-08-14
tags: ["ssh", "ubuntu", "arch-linux", "linux"]
categories: ["DevOps"]
---

## Why I Use SSH

SSH is how I control another machine from my terminal. I use it to manage servers, copy files, and run commands on remote systems without sitting in front of them.

For Windows remote desktop you use RDP. For Linux, SSH is the standard.

## How SSH Works (Short Version)

- **Client** — on your laptop (where you type)
- **Server** — on the remote machine (what you connect to)

You run `ssh user@ip`, enter your password (or use a key), and you get a shell on the remote box.

## Install the SSH Client

Check if you already have it:

```bash
ssh
```

If you see usage info, you're set. If not:

**Ubuntu:**

```bash
sudo apt update
sudo apt install openssh-client
```

**Arch Linux:**

```bash
sudo pacman -S openssh
```

## Install the SSH Server

The machine you want to connect **to** needs the server package.

On that remote machine, test:

```bash
ssh localhost
```

If it fails, install the server:

**Ubuntu:**

```bash
sudo apt update
sudo apt install openssh-server
```

**Arch Linux:**

```bash
sudo pacman -S openssh
sudo systemctl enable --now sshd
```

Check it's running:

```bash
sudo systemctl status sshd
```

Ubuntu does **not** ship with the SSH server installed — you have to add it yourself.

## Connect

From your local machine:

```bash
ssh your_username@host_ip_address
```

First time you connect, it asks you to trust the server's fingerprint. Type `yes`.

## Real Example From My Setup

I sometimes SSH from my Arch machine to an Ubuntu VM:

```bash
ssh ubuntu@192.168.1.80
```

And the other way:

```bash
ssh ayysaad@192.168.1.20
```

Swap the username and IP for your own setup.

## Quick Reference

| Task | Ubuntu | Arch Linux |
| ---- | ------ | ---------- |
| Install client | `sudo apt install openssh-client` | `sudo pacman -S openssh` |
| Install server | `sudo apt install openssh-server` | `sudo pacman -S openssh` |
| Enable server | starts automatically | `sudo systemctl enable --now sshd` |
| Connect | `ssh user@ip` | `ssh user@ip` |
