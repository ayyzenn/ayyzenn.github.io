---
title: "VoIP with Asterisk: My Home Phone Setup on Ubuntu"
date: 2022-08-02
tags: ["voip", "asterisk", "ubuntu"]
---
## What I Built

I set up a home VoIP system using **Asterisk** as the server and **Twinkle** as the softphone client. Everything here runs on **Ubuntu** — same network, two extensions, calls between machines.

## How VoIP Works

VoIP sends your voice as data over the internet instead of through a phone line. Cheaper than landlines, especially for long-distance — that's why I tried it.

## Asterisk as the Server

**Asterisk** is the PBX — it handles call routing between extensions. I compiled it from source on Ubuntu.

## Installing Asterisk

Here's what I ran:
```bash
sudo apt update && sudo apt upgrade -y
```

## Step 2: Install Essential Dependencies

Asterisk requires a bunch of dependencies to function properly. Install them all in one go:
```bash
sudo apt install -y gnupg2 software-properties-common git curl wget \
    libnewt-dev libssl-dev libncurses5-dev autoconf subversion \
    libsqlite3-dev build-essential libjansson-dev libxml2-dev pkg-config \
    libtool uuid-dev
```

## Step 3: Install DAHDI and LibPRI (For Extra Features)

If you want better telephony support, install DAHDI and LibPRI:
```bash
cd /usr/src/
sudo git clone -b next git://git.asterisk.org/dahdi/linux dahdi-linux && \
cd dahdi-linux && sudo make && sudo make install

cd /usr/src/
sudo git clone -b next git://git.asterisk.org/dahdi/tools dahdi-tools && \
cd dahdi-tools && sudo autoreconf -i && sudo ./configure && \
sudo make install && sudo make install-config && sudo dahdi_genconf modules

cd /usr/src/
sudo git clone https://gerrit.asterisk.org/libpri libpri && \
cd libpri && sudo make && sudo make install
```

## Step 4: Install Asterisk

Now for the main event—installing Asterisk itself:
```bash
cd /usr/src/
sudo git clone -b 18 https://gerrit.asterisk.org/asterisk asterisk-18 && \
cd asterisk-18/ && sudo contrib/scripts/get_mp3_source.sh && \
sudo contrib/scripts/install_prereq install && sudo ./configure && \
sudo make menuselect && sudo make -j2 && \
sudo make install && sudo make samples && sudo make config && sudo ldconfig
```

## Step 5: Create Asterisk User

It's best to run Asterisk under a dedicated user:
```bash
sudo adduser --system --group --home /var/lib/asterisk --no-create-home --gecos "Asterisk PBX" asterisk
sudo usermod -a -G dialout,audio asterisk
sudo chown -R asterisk: /var/{lib,log,run,spool}/asterisk /usr/lib/asterisk /etc/asterisk
sudo chmod -R 750 /var/{lib,log,run,spool}/asterisk /usr/lib/asterisk /etc/asterisk
```

## Step 6: Configure Asterisk

### `extensions.conf`

Edit the `extensions.conf` file:
```bash
sudo nano /etc/asterisk/extensions.conf
```

Add the following:
```ini
[from-internal]
exten = 100,1,Answer()
same = n,Wait(1)
same = n,Playback(hello-world)
same = n,Hangup()
```

### `sip.conf`

Edit the `sip.conf` file:
```bash
sudo nano /etc/asterisk/sip.conf
```

Add these configurations:
```ini
[general]
context=default

[6001]
type=friend
context=from-internal
host=dynamic
secret=temp123
disallow=all
allow=ulaw

[6002]
type=friend
context=from-internal
host=dynamic
secret=temp123
disallow=all
allow=ulaw
```

## Step 7: Configure Firewall for Asterisk

Don't forget to allow VoIP traffic through the firewall:
```bash
sudo ufw allow 5060/udp
sudo ufw allow 10000:20000/udp
```

## Step 8: Start Asterisk

Everything is set! Start Asterisk and check its status:
```bash
sudo systemctl start asterisk
sudo asterisk -vvvr
```

# Installing Twinkle (Softphone)

To test our Asterisk setup, install the Twinkle softphone:
```bash
sudo apt install -y twinkle
```

### Configuring Twinkle
1. Open Twinkle and select "Wizard".
2. Enter a profile name.
3. Fill out the configuration form (use `temp123` as the password, as set in `sip.conf`).
4. Save and confirm the settings.
5. Use the main menu to place calls.

For testing, install Twinkle on a second system and configure it with `6002` instead of `6001`. Ensure both clients are on the same network.

---

That's it! You now have a working VoIP setup using Asterisk and Twinkle.

