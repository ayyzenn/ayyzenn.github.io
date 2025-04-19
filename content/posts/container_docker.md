+++
date = '2022-07-27T15:49:43+05:00'
title = 'Creating a container in docker'
tags = ['dockers', 'containers']
+++

### Creating a Docker Container with Ubuntu

To create a Docker container, first download the Ubuntu image from [Docker Hub](https://hub.docker.com/search?q=). Use the following command:

```bash
sudo docker pull ubuntu
```

Once the image is downloaded, create a new container using the command below:

```bash
sudo docker run --name my-container -it ubuntu
```

#### Key Parameters:

- **`-i`**: Enables interactive mode.
- **`-t`**: Allocates a terminal for the container.
- **`--name`**: Assigns a custom name to the container.

Upon execution, the container will be created, and you will be logged into its environment.

### Working Inside the Container

Since you are operating as the root user within the container, you can execute commands without requiring `sudo` privileges. To update and upgrade the system, run:

```bash
apt update
apt upgrade
```

Your container is now fully set up and ready for use.

