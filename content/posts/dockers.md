---
title: "Docker on Arch Linux and Ubuntu: How I Got It Running"
date: 2022-07-26
tags: ["docker", "ubuntu", "arch-linux", "containers"]
---

## Why I Started Using Docker

I wanted a way to run apps without messing up my main system. Docker packs an app with everything it needs into a **container** — a small, isolated environment that shares my kernel but stays separate from everything else.

If something breaks inside a container, I delete it and start over. No reinstalling the whole OS.

## Containers vs Images (Quick Version)

- **Image** — a read-only template (like a recipe)
- **Container** — a running instance of that image (like the actual dish you cooked)

You build or pull an image once. You can spin up as many containers from it as you want.

---

## Arch Linux

This is what I use on my main machine now.

### Install Docker

```bash
sudo pacman -S docker
sudo systemctl enable --now docker
```

Check the service is running:

```bash
systemctl status docker
```

You should see `active (running)`.

### Test with hello-world

```bash
sudo docker pull hello-world
sudo docker images
sudo docker run hello-world
```

If you see a friendly message from Docker, it works.

### Run Docker without sudo (optional)

I got tired of typing `sudo` every time:

```bash
sudo usermod -aG docker $USER
```

Log out and log back in. Then test:

```bash
docker ps
```

If that works without sudo, you're set.

---

## Ubuntu

This is what I used first when I was learning Docker on Ubuntu 22.04.

### Install Docker

```bash
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

Check the service is running:

```bash
systemctl status docker
```

You should see `active (running)`.

### Test with hello-world

```bash
sudo docker pull hello-world
sudo docker images
sudo docker run hello-world
```

Same test as Arch — if hello-world prints a success message, Docker is working.

### Run Docker without sudo (optional)

```bash
sudo usermod -aG docker $USER
```

Log out and log back in. Then test:

```bash
docker ps
```

---

## Quick Reference

| Step | Arch Linux | Ubuntu |
| ---- | ---------- | ------ |
| Install | `sudo pacman -S docker` | `sudo apt install docker.io` |
| Enable on boot | `sudo systemctl enable --now docker` | `sudo systemctl enable docker` |
| Test | `sudo docker run hello-world` | `sudo docker run hello-world` |
| Skip sudo | `sudo usermod -aG docker $USER` | `sudo usermod -aG docker $USER` |

The Docker commands (`pull`, `run`, `ps`, etc.) are the same on both distros once it is installed.

---

## What's Next?

Once Docker is installed, the next step is creating your own container. I wrote that up here: [Creating My First Docker Container](/posts/container_docker/).
