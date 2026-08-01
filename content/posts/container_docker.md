---
title: "Creating My First Docker Container"
author: "Saad"
date: 2022-07-27
tags: ["docker", "containers", "ubuntu", "arch-linux"]
categories: ["DevOps"]
---

## What I Did Here

After installing Docker, I wanted to run an actual Ubuntu container — not just `hello-world`. The steps inside this post are the same on **Arch Linux** and **Ubuntu** hosts. Only the Docker *installation* differs — see [Docker on Arch Linux and Ubuntu](/posts/dockers/) for that part.

---

## Arch Linux (host)

Make sure Docker is installed and running on your Arch machine first:

```bash
sudo pacman -S docker
sudo systemctl enable --now docker
systemctl status docker
```

### Pull the Ubuntu image

```bash
sudo docker pull ubuntu
```

### Create and enter a container

```bash
sudo docker run --name my-container -it ubuntu
```

What those flags mean:

- **`-i`** — keep input open (interactive)
- **`-t`** — give me a terminal inside the container
- **`--name my-container`** — name it so I can find it later

Your shell prompt changes — you are **inside** the container now.

### Update packages inside the container

You are root inside the container, so no `sudo`:

```bash
apt update
apt upgrade -y
```

### Useful commands (from the Arch host)

Run these from your normal Arch terminal, not inside the container:

```bash
# List running containers
sudo docker ps

# List all containers (including stopped)
sudo docker ps -a

# Stop the container
sudo docker stop my-container

# Start it again
sudo docker start my-container

# Re-enter a running container
sudo docker exec -it my-container bash
```

---

## Ubuntu (host)

Make sure Docker is installed and running on your Ubuntu machine first:

```bash
sudo apt update
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
systemctl status docker
```

### Pull the Ubuntu image

```bash
sudo docker pull ubuntu
```

### Create and enter a container

```bash
sudo docker run --name my-container -it ubuntu
```

Same flags as above — after this, you are inside the container.

### Update packages inside the container

```bash
apt update
apt upgrade -y
```

### Useful commands (from the Ubuntu host)

```bash
# List running containers
sudo docker ps

# List all containers (including stopped)
sudo docker ps -a

# Stop the container
sudo docker stop my-container

# Start it again
sudo docker start my-container

# Re-enter a running container
sudo docker exec -it my-container bash
```

---

## What I Learned

Docker containers are temporary by default — remove one and the changes inside are gone unless you commit it to a new image or use volumes. For learning though, `docker run -it ubuntu` on either Arch or Ubuntu is the simplest way to get started.

The container itself runs Ubuntu Linux regardless of whether your **host** is Arch or Ubuntu — that is the whole point of containers.
