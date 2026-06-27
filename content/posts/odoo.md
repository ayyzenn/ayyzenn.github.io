---
title: "Odoo on Ubuntu: How I Installed It"
date: 2022-08-11
tags: ["odoo", "installation", "ubuntu"]
---
## What I Did

I installed **Odoo** (open-source ERP) on Ubuntu for a project. These are the exact steps I ran — all `apt` commands on Ubuntu.

```bash
sudo apt update && sudo apt upgrade
```

## Create an Odoo User
Run this command to create a system user for Odoo:
```bash
sudo adduser -system -home=/opt/odoo -group odoo
```

## Install PostgreSQL
Odoo needs PostgreSQL as its database, so install it with:
```bash
sudo apt-get install postgresql -y
```

## Create a PostgreSQL User for Odoo
```bash
sudo su - postgres -c "createuser -s odoo" 2> /dev/null || true
```

## Install Python Dependencies
Odoo requires some Python packages. Install them with:
```bash
sudo apt-get install libpq-dev python-dev libxml2-dev libxslt1-dev libldap2-dev libsasl2-dev libffi-dev
```
```bash
sudo -H pip3 install -r https://raw.githubusercontent.com/odoo/odoo/master/requirements.txt
```

## Install Other Required Packages
You'll need Node.js and npm:
```bash
sudo apt-get install nodejs npm -y
```
```bash
sudo npm install -g rtlcss
```

## Install Wkhtmltopdf
Odoo uses Wkhtmltopdf to generate PDFs. Install it with:
```bash
sudo apt-get install xfonts-75dpi
```
```bash
sudo wget https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox_0.12.6-1.bionic_amd64.deb
```
```bash
sudo dpkg -i wkhtmltox_0.12.6-1.bionic_amd64.deb
```
```bash
sudo cp /usr/local/bin/wkhtmltoimage /usr/bin/wkhtmltoimage
```
```bash
sudo cp /usr/local/bin/wkhtmltopdf /usr/bin/wkhtmltopdf
```

## Set Up Logging
Create a log directory so Odoo can store its logs:
```bash
sudo mkdir /var/log/odoo
```
```bash
sudo chown odoo:odoo /var/log/odoo
```

## Install Odoo
Get Odoo from GitHub:
```bash
sudo apt-get install git
```
```bash
sudo git clone --depth 1 --branch 14.0 https://www.github.com/odoo/odoo /odoo/odoo-server
```

## Set Permissions
Give Odoo ownership of its files:
```bash
sudo chown -R odoo:odoo /odoo/*
```

## Create a Config File
Set up Odoo's configuration:
```bash
sudo touch /etc/odoo-server.conf
```
```bash
sudo su root -c "printf '[options] \n; This is the password that allows database operations:\n' >> /etc/odoo-server.conf"
```
```bash
sudo su root -c "printf 'admin_passwd = admin\n' >> /etc/odoo-server.conf"
```
```bash
sudo su root -c "printf 'xmlrpc_port = 8069\n' >> /etc/odoo-server.conf"
```
```bash
sudo su root -c "printf 'logfile = /var/log/odoo/odoo-server.log\n' >> /etc/odoo-server.conf"
```
```bash
sudo su root -c "printf 'addons_path=/odoo/odoo-server/addons\n' >> /etc/odoo-server.conf"
```
```bash
sudo chown odoo:odoo /etc/odoo-server.conf
```
```bash
sudo chmod 640 /etc/odoo-server.conf
```

## Start Odoo
Finally, let's fire it up!
```bash
sudo su - odoo -s /bin/bash
```
```bash
cd /odoo/odoo-server
```
```bash
./odoo-bin -c /etc/odoo-server.conf
```

Now your Odoo instance is up and running! Open your browser and go to:
```bash
localhost:8069
```

