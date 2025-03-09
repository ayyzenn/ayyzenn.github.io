+++
date = '2022-08-02T15:49:43+05:00'
title = 'VoIP with Asterisk: Ditch the Landline & Build Your Own Phone System!'
tags = ['voip', 'asterisk', 'ubuntu']
+++

# Introduction

Voice over Internet Protocol (VoIP) is a technology that enables voice and video communication over the internet instead of traditional landlines. With an internet connection, users can make calls without relying on local phone services or physical copper wiring. A VoIP service provider manages the call transmission over the internet.

# How VoIP Works

VoIP converts voice into a digital format, compresses it, and transmits it over the internet. A VoIP service provider facilitates the call setup. Many users prefer VoIP over traditional landlines due to its lower startup costs and reduced expenses for long-distance calls. VoIP operates using Internet Protocol, a fundamental component of modern internet infrastructure, offering significant advancements over traditional telecommunications.

# Asterisk as a Gateway

Asterisk is a software-based telephone private branch exchange (PBX) that enables voice communication across various telephony technologies. It connects different telephony services, including the public switched telephone network (PSTN) and VoIP services. Asterisk’s modular and multi-protocol architecture makes it an effective solution for building telephony gateways.

# Installing Asterisk

Let's get Asterisk up and running! Follow these steps carefully, and you'll have your own VoIP setup in no time.

## Step 1: Update Your System

Before diving in, let's update your system to ensure everything is up-to-date:
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

That's it! You now have a fully functional VoIP setup using Asterisk and Twinkle. Time to start making some calls! 📞🚀

