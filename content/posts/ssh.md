+++
date = '2021-08-14T15:49:43+05:00'
title = 'SSH: Connecting to a Remote Server in Linux'
tags = ['ssh', 'ubuntu']
+++

## Introduction

Accessing machines remotely became a necessity a long time ago, and it is difficult to imagine managing computers without remote access. There are various ways to establish a connection with a remote machine, depending on the operating system in use. The two most commonly used protocols are:

- **Secure Shell (SSH)** for Linux-based machines
- **Remote Desktop Protocol (RDP)** for Windows-based machines

Both protocols use client and server applications to establish remote connections. These tools allow you to remotely manage other computers, transfer files, and perform any task that would be possible if you were physically in front of the machine.

## What is SSH?

SSH (Secure Shell) is a protocol that enables secure remote access to computers. Establishing an SSH connection requires two components: a client and a corresponding server-side component.

- The **SSH client** is an application installed on the computer used to initiate the connection.
- The **SSH server** runs a daemon that listens for incoming client connections on a specific TCP/IP port.

When a client attempts to connect, the SSH daemon on the server responds, exchanges credentials, and, if verified, establishes a secure encrypted session.

## How to Enable an SSH Connection?

To create an SSH connection, both the client and server components must be installed on the respective machines. A widely used open-source SSH tool for Linux distributions is **OpenSSH**. It requires terminal access on both the local and remote machines.

**Note:** Ubuntu does not have the SSH server installed by default.

## How to Install an OpenSSH Client?

Before installing an SSH client, check if it is already available on your Linux system:

1. Open a terminal by searching for "Terminal" or pressing `CTRL + ALT + T`.
2. Type the following command and press `Enter`:

   ```bash
   ssh
   ```

3. If the client is installed, you will receive a response indicating SSH usage options.

If the SSH client is not installed, install it using the following command based on your distribution:

- **For Debian/Ubuntu-based systems:**
  
  ```bash
  sudo apt-get install openssh-client
  ```

- **For Arch/Manjaro-based systems:**
  
  ```bash
  sudo pacman -S openssh
  ```

Once installed, you can SSH into any machine running an SSH server, provided you have the necessary access credentials.

## How to Install an OpenSSH Server?

To accept SSH connections, the remote machine must have the SSH server installed.

To check if OpenSSH server is available on your system:

1. Open a terminal on the remote machine.
2. Type the following command and press `Enter`:

   ```bash
   ssh localhost
   ```

3. If the SSH server is not installed, an error message will appear.

To install the OpenSSH server, use the appropriate command for your distribution:

- **For Debian/Ubuntu-based systems:**
  
  ```bash
  sudo apt-get install openssh-server
  ```

- **For Arch/Manjaro-based systems:**
  
  ```bash
  sudo pacman -S openssh
  ```

After installation, verify that the SSH service is running:

```bash
sudo systemctl status sshd
```

If the SSH service is active, the system is ready to accept remote connections.

## How to Connect via SSH?

With both the OpenSSH client and server installed, you can establish a secure remote connection:

1. Open a terminal on your local machine.
2. Run the following command, replacing `<your_username>` and `<host_ip_address>` accordingly:

   ```bash
   ssh your_username@host_ip_address
   ```

3. If your local username matches the server’s username, you can simply use:

   ```bash
   ssh host_ip_address
   ```

4. Press `Enter` and enter the password when prompted.

**Note:** Both client and server must be on the same network.

On first-time connections, SSH will prompt for confirmation to continue connecting. Type `yes` and press `Enter`. This process adds the server's ECDSA (Elliptic Curve Digital Signature Algorithm) key fingerprint to your local machine for future authentication.

You are now securely connected to the remote server via SSH.

---

### Additional Notes

Since I am primarily using **Manjaro**, my focus is on **Arch-based** systems. However, the guide includes commands for both **Debian/Ubuntu-based** and **Arch/Manjaro-based** distributions to ensure flexibility. If a command works universally across both systems, it has been mentioned once.
