---
title: "Chef Server Setup: What I Did on Ubuntu"
date: 2022-08-02
tags: ["chef", "automation", "ubuntu"]
---
## Why I Looked Into Chef

Managing servers one by one doesn't scale. I set up **Chef** to store server configs as code — a Chef server, a workstation where I write recipes, and a node that pulls its config automatically.

**Note:** I did this on **Ubuntu 18.04** using `.deb` packages and `apt`.

## Chef Server

The Chef server acts as the central hub for all workstations and nodes under Chef management. Configuration changes made on workstations are pushed to the Chef server, where they are pulled by nodes using `chef-client` to apply the configurations.

### Install the Chef Server

1. Download the latest Chef server core:

   ```bash
   wget https://packages.chef.io/files/stable/chef-server/13.1.13/ubuntu/18.04/chef-server-core_13.1.13-1_amd64.deb
   ```

2. Install the server:

   ```bash
   sudo dpkg -i chef-server-core_*.deb
   ```

3. Start the Chef server services:

   ```bash
   sudo chef-server-ctl reconfigure
   ```

### Create a Chef User and Organization

To link workstations and nodes to the Chef server, create an administrator and organization with associated RSA private keys.

1. Create a `.chef` directory to store the keys:

   ```bash
   mkdir ~/.chef
   ```

2. Create a user with `chef-server-ctl`, replacing placeholders with your details:

   ```bash
   sudo chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' --filename ~/.chef/USER_NAME.pem
   ```

3. List all users:

   ```bash
   sudo chef-server-ctl user-list
   ```

4. Create an organization and associate the user:

   ```bash
   sudo chef-server-ctl org-create ORG_NAME "ORG_FULL_NAME" --association_user USER_NAME --filename ~/.chef/ORG_NAME.pem
   ```

5. List all organizations:

   ```bash
   sudo chef-server-ctl org-list
   ```

With the Chef server installed and RSA keys generated, the workstation can now be configured.

## Chef Workstations

The Chef workstation is where recipes, cookbooks, attributes, and configurations are managed. It can be a local machine or a remote server.

### Setting Up a Workstation

1. Download the latest Chef Workstation:

   ```bash
   wget https://packages.chef.io/files/stable/chef-workstation/0.2.43/ubuntu/18.04/chef-workstation_0.2.43-1_amd64.deb
   ```

2. Install Chef Workstation:

   ```bash
   sudo dpkg -i chef-workstation_*.deb
   ```

3. Create your Chef repository:

   ```bash
   chef generate repo chef-repo
   ```

4. Ensure that your workstation’s `/etc/hosts` file correctly maps IP addresses to the Chef server and workstation hostnames.

5. Create a `.chef` subdirectory within `chef-repo` to store authentication files:

   ```bash
   mkdir ~/chef-repo/.chef
   cd chef-repo
   ```

### Add the RSA Private Keys

Authentication between the Chef server and workstations/nodes is performed using public-key encryption. Copy the RSA private keys from the Chef server to the workstation.

1. Generate an RSA key pair on the workstation (if not already available):

   ```bash
   ssh-keygen -b 4096
   ```

2. Upload the workstation’s public key to the Chef server:

   ```bash
   ssh-copy-id example_user@192.0.2.0
   ```

3. Copy the `.pem` files from the Chef server to the workstation:

   ```bash
   scp example_user@192.0.2.0:~/.chef/*.pem ~/chef-repo/.chef/
   ```

4. Confirm the files were copied:

   ```bash
   ls ~/chef-repo/.chef
   ```

## Bootstrap a Node

Bootstrapping a node installs the Chef client and validates the node, allowing it to pull and apply configurations from the Chef server.

1. Update the `/etc/hosts` file on the **node** to correctly identify the node, Chef server, and workstation.

2. From the **workstation**, navigate to the `.chef` directory:

   ```bash
   cd ~/chef-repo/.chef
   ```

3. Bootstrap the node:

   ```bash
   knife bootstrap 192.0.2.0 -x root -P password --node-name nodename
   ```

4. Verify the node has been bootstrapped:

   ```bash
   knife client list
   ```

5. Add the bootstrapped node to the workstation’s `/etc/hosts` file.

For more detailed information, refer to the [Linode guide](https://www.linode.com/docs/guides/install-a-chef-server-workstation-on-ubuntu-18-04/).

