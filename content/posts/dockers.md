+++
date = '2022-07-26T15:49:43+05:00'
title = 'Docker on Ubuntu'
tags = ['dockers']
+++

## Introduction

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications.

By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

## Container

Unlike a virtual machine (VM) that provides hardware virtualization, a container provides lightweight, operating-system-level virtualization by abstracting the user space. Containers share the host system’s kernel with other containers. A container, which runs on the host operating system, is a standard software unit that packages code and all its dependencies, ensuring applications can run quickly and reliably across different environments. Containers are non-persistent and are instantiated from images.

## Docker Images

A Docker image is a collection of software that is run as a container. It contains a set of instructions for creating a container that can run on the Docker platform. Images are immutable, and any modifications require building a new image.

## Installing Docker on Ubuntu 20.04

To install Docker on Ubuntu 20.04, follow these steps:

1. Update the package index:

   ```bash
   sudo apt update
   ```

2. Install Docker:

   ```bash
   sudo apt install docker.io
   ```

3. Start the Docker service:

   ```bash
   sudo systemctl start docker
   ```

4. Enable Docker to start at boot:

   ```bash
   sudo systemctl enable docker
   ```

5. Download the Docker test image:

   ```bash
   sudo docker pull hello-world
   ```

6. Verify downloaded images:

   ```bash
   sudo docker images
   ```

7. Run the `hello-world` container:

   ```bash
   sudo docker run hello-world
   ```

**Note:** You need to run Docker with `sudo` privileges.

For creating a container, refer to my detailed blog post: [Creating a Container in Docker](https://ayyzenn.wordpress.com/2022/07/26/creating-a-container-in-docker/).

