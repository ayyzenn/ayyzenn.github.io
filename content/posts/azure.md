+++
date = '2021-08-16T15:49:43+05:00'
title = 'Creating a Virtual Machine on Microsoft Azure'
tags = ['cloud', 'azure', 'ssh']
+++

## Introduction

Microsoft Azure is a cloud computing service created by Microsoft for building, testing, deploying, and managing applications and services through Microsoft-managed data centers. It provides Software as a Service (SaaS), Platform as a Service (PaaS), and Infrastructure as a Service (IaaS). Additionally, it supports various programming languages, tools, and frameworks, including both Microsoft-specific and third-party software and systems.

In this guide, we will walk through the process of creating a Virtual Machine (VM) on Microsoft Azure.

## Steps to Create a Virtual Machine

### 1. Create a Microsoft Azure Account
Before creating a VM, you need to have a Microsoft Azure account. Once the account is set up, log in and navigate to the Azure dashboard.

### 2. Create a New Resource
1. On the Azure dashboard, click on **Create a Resource**.
2. Select **Ubuntu 20.04 LTS** and click **Create**.
3. Name your Virtual Machine.
4. Under the **Administration Account** section, select **SSH Public Key** as the authentication type.
5. Enter a username and specify a key pair name.
6. In the **Inbound Port Rules** section, select **SSH (22)** and **HTTP (80)**.

### 3. Configure and Deploy the VM
1. Click **Review and Create**.
2. Review the VM specifications and click **Create**.
3. Download the **Key Pair** as it will be required later.
4. Wait for the VM to be deployed.
5. Once the deployment is complete, click **Go to Resource**.
6. Click **Connect** to establish a connection to your virtual machine.

### 4. Connect to the Virtual Machine
1. Open a terminal and navigate to the directory where the key pair was downloaded:
   ```sh
   cd Downloads/
   ```
2. Change the key pair permissions:
   ```sh
   chmod 400 pair_key_name.pem
   ```
3. Connect to the VM using SSH:
   ```sh
   ssh -i pair_key_name.pem username@your_ip
   ```
4. When prompted, type **Yes** and press **Enter**.

### 5. Logging Out
To log out from the virtual machine, type:
   ```sh
   logout
   ```

## Conclusion
Following these steps, you have successfully created and connected to a virtual machine on Microsoft Azure. This setup allows for secure remote access, enabling you to deploy applications and manage workloads efficiently.
