+++
date = '2022-08-07T15:49:43+05:00'
title = 'Your Gateway to FTP: A Step-by-Step Guide to ProFTPD'
tags = ['proftpd', 'ftp', 'linux']
+++

# Setting Up ProFTPD

## Introduction
FTP (File Transfer Protocol) is a way to move files between computers on a network. You can use it to swap files between accounts, transfer stuff between your computer and a remote server, or even grab files from online repositories.

ProFTPD (Professional File Transfer Protocol Daemon) is a powerful and flexible FTP server. But keep in mind, FTP isn't secure by default. It's a good idea to configure ProFTPD to use SFTP (Secure File Transfer Protocol) instead, which runs over SSH.

## Installing ProFTPD
Getting ProFTPD set up is pretty simple. Just run:

```bash
sudo apt-get install proftpd
```

## Configuring ProFTPD
Once it's installed, you'll need to tweak the config file. Open it up with:

```bash
sudo nano /etc/proftpd/proftpd.conf
```

Update the `ServerName` field to match your hostname:

```bash
ServerName "test.com"
```

Also, uncomment this line to make sure users are restricted to their home directories:

```bash
# Use this to jail all users in their homes
DefaultRoot /home/ayyzenn/
```

Once you're done, save the file and restart ProFTPD so the changes take effect:

```bash
sudo service proftpd restart
```

## Accessing the FTP Server
Now that everything is set up, you can connect to your FTP server from the command line:

```bash
ftp test.com
```

To exit, just type:

```bash
exit
```

## Adding a User
If you want another user to access the FTP server, you'll need to add them first. Run:

```bash
sudo adduser <username>
```

Follow the prompts—it’s fine to leave the details blank if you want.

Then, make sure they have the right permissions by adding them to the sudo group:

```bash
sudo usermod -aG sudo username
```

## Installing FileZilla
If you prefer a GUI, you can use FileZilla. Install it with:

```bash
sudo apt install filezilla
```

## Connecting to Your FTP Server with FileZilla
1. Open FileZilla.
2. Enter your:
   - **Hostname or IP address**
   - **Username**
   - **Password**
   - **Port number** (default: 21)
3. Click **Quickconnect**.
4. Accept any prompts and you're good to go!

## Wrapping Up
That's it! You now have a working ProFTPD server. Whether you're using the command line or FileZilla, you can easily manage file transfers between your systems. Enjoy!

