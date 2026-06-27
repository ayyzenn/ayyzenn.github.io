---
title: "Dual Boot Ubuntu and Windows: What I Did"
date: 2021-08-13
tags: ["dual boot", "ubuntu", "linux"]
---
## Why I Dual-Booted

I needed Windows for some things and Linux for everything else. Instead of picking one, I installed **Ubuntu alongside Windows** on the same machine. This is the process I followed.

---

## Steps to Dual Boot Ubuntu with Windows

### **Step 1: Prepare a Bootable USB Drive**
1. Download the latest Ubuntu ISO from the [official Ubuntu website](https://ubuntu.com/download).
2. Download and install [Rufus](https://rufus.ie) or [balenaEtcher](https://www.balena.io/etcher/) to create a bootable USB.
3. Insert a USB drive (at least 8GB) into your computer.
4. Open Rufus or balenaEtcher:
   - Select the Ubuntu ISO file.
   - Choose the USB drive.
   - Click "Start" to create the bootable drive.

---

### **Step 2: Configure BIOS/UEFI Settings**
1. Restart your computer and enter BIOS/UEFI by pressing a specific key (usually **F2, F12, Del, or Esc**) during startup.
2. Look for **Boot Order/Boot Priority** and set the USB drive as the first boot device.
3. Disable **Secure Boot** (if enabled) under the Security tab.
4. Enable **UEFI mode** (recommended) instead of Legacy mode.
5. Save changes and exit BIOS.

---

### **Step 3: Boot from the USB Drive**
1. Insert the bootable USB drive into your computer.
2. Restart the system and press the **Boot Menu key** (F12, F9, Esc, or as per your motherboard).
3. Select the bootable USB drive from the list.
4. Press **Enter** to boot into Ubuntu Live.

---

### **Step 4: Try or Install Ubuntu**
1. Once the Ubuntu installer loads, you will see two options:
   - **Try Ubuntu** – Runs Ubuntu without installing it.
   - **Install Ubuntu** – Proceeds with the installation.
2. Click on **Install Ubuntu** to continue.

---

### **Step 5: Select Keyboard Layout**
1. Choose your preferred **keyboard layout** and language.
2. Click **Continue**.

---

### **Step 6: Connect to Wi-Fi (Optional)**
1. Select your Wi-Fi network.
2. Enter the password and click **Continue**.

---

### **Step 7: Choose Installation Type**
1. Select **Normal Installation** (recommended).
2. Check the box **Install third-party software for graphics and Wi-Fi hardware, Flash, MP3, and other media**.
3. Click **Continue**.

---

### **Step 8: Choose Installation Method**
1. Select **Something else** (for manual partitioning).
2. Click **Continue**.

---

### **Step 9: Create Partitions for Ubuntu**
Ubuntu requires three main partitions:

1. **Boot Loader Partition**
   - Select **Free Space** and click **Add**.
   - Assign **512MB to 1GB** for the boot partition.
   - Choose **Ext4** as the filesystem type.
   - Set **Mount Point** as `/boot`.
   - Click **OK**.

2. **Swap Partition**
   - Select **Free Space** and click **Add**.
   - Allocate **swap size** (equal to or twice your RAM size).
   - Choose **Use as** → **Swap Area**.
   - Click **OK**.

3. **Root Partition**
   - Select **Free Space** and click **Add**.
   - Allocate remaining space for root (`/`).
   - Choose **Ext4** as the filesystem type.
   - Set **Mount Point** as `/`.
   - Click **OK**.

---

### **Step 10: Begin Installation**
1. Verify all partitions.
2. Click **Install Now**.
3. A confirmation dialog will appear; click **Continue**.

---

### **Step 11: Select Timezone**
1. Choose your region/time zone.
2. Click **Continue**.

---

### **Step 12: Create User Account**
1. Enter your **name**, **username**, and **password**.
2. Click **Continue**.

---

### **Step 13: Installation Process**
1. The installation will begin.
2. Wait for the process to complete (it may take 10-30 minutes).

---

### **Step 14: Restart the System**
1. Once the installation is completed, click **Restart Now**.
2. Remove the bootable USB when prompted.
3. Press **Enter**.

---

### **Step 15: Choose Ubuntu at Boot**
1. When your system reboots, you will see a **GRUB menu**.
2. Select **Ubuntu** to boot into Ubuntu.
3. If you want to boot into Windows, choose **Windows Boot Manager**.

---

## Conclusion
Congratulations! You have successfully dual-booted Ubuntu alongside Windows. You can switch between Windows and Ubuntu anytime by selecting the option in the boot menu.
