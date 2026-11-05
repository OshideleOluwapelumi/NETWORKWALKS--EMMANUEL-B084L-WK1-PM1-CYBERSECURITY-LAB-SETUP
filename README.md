<div align="center">

# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

</div>

<p align="center">
  <img src="https://img.shields.io/badge/Skill-Cybersecurity-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/VirtualBox-v7.2-0070C0?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Kali%20Linux-v2026.3-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/Skill-Linux-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Network-10.0.0.0%2F24-238F89?style=flat-square&labelColor=000000" />
  <img src="https://img.shields.io/badge/Skill-Virtualization-404040?style=flat-square&labelColor=C00000" />
  <img src="https://img.shields.io/badge/Ethical%20Hacking-E87500?style=flat-square&labelColor=000000&logo=kalilinux&logoColor=white" />
  <img src="https://img.shields.io/badge/NetworkWalks-404040?style=flat-square&labelColor=C00000" />
</p>

---

## 📌 Project Overview

This project documents how I built a **virtual cybersecurity and penetration-testing lab** using VirtualBox and Kali Linux, as part of the Cybersecurity at Networkwalks program.

The lab gives me a controlled environment where I can safely and repeatedly practise network scanning, reconnaissance, vulnerability assessment, and other security-testing skills.

It runs on a private virtual network, so extra machines can be added later and used as targets for authorized testing.

---

## 🎯 Objectives

- Install and configure VirtualBox.
- Import Kali Linux as a virtual machine.
- Create a private **NAT Network** for the lab.
- Assign a static IP address to the Kali VM.
- Verify network connectivity and DNS resolution.
- Recover access to the VM after a forgotten login.
- Take a clean VM snapshot for recovery.
- Document the setup, including the problems I hit and how I solved them.

---

## 🛡️ Purpose of the Lab

The lab can be used for:

- Network reconnaissance
- Port scanning
- Vulnerability assessment
- Packet analysis
- Web security testing
- Security-tool experimentation

⚠️ **Important:** This lab must only be used on systems that I own or have explicit permission to test. It is for education only.

---

## 🏗️ Lab Architecture

![Lab architecture](1-screenshot-title-image.png)

Additional target machines can be added to the same virtual network later.

---

## ⚙️ Lab Configuration

| 🧩 Component       | ⚙️ Configuration                    |
| ------------------ | ----------------------------------- |
| 🖥️ Host OS         | Windows 11     |
| 💻 Host Machine    | Dell laptop                         |
| 🧰 Hypervisor      | VirtualBox 7.2                      |
| 🐉 Security OS     | Kali Linux 2026.3 (kernel 7.1.5)    |
| 🧠 Kali RAM        | 3072 MB       |
| 🌐 Virtual Network | NAT Network (`NatNetwork`)          |
| 📡 Network Address | 10.0.0.0/24                         |
| 🐧 Kali IP Address | 10.0.0.2/24                         |
| 🚪 Default Gateway | 10.0.0.1                            |
| 🌍 DNS Server      | 10.0.0.1 (course default: 8.8.8.8)  |
| 🔮 Future VM Range | 10.0.0.3 – 10.0.0.99                |

---

# 🪜 Lab Setup Procedure

## Step 1. Install VirtualBox

VirtualBox was installed as the hypervisor on the Windows host.

## Step 2. Import Kali Linux

The prebuilt Kali Linux VirtualBox image was downloaded from the official Kali website and imported into VirtualBox.

![Kali Linux desktop running in VirtualBox, with the shared folder visible](3-screenshot-kali-linux.png)

A shared folder (`sf_Downloads`) is also mounted on the Kali desktop for moving files between Windows and the VM.

## Step 3. Create the NAT Network

In VirtualBox: **File → Tools → Network Manager → NAT Networks → Create**.

VirtualBox creates the network with a default range of `10.0.2.0/24`, so the prefix has to be changed to match the lab design:

```text
Network Name: NatNetwork
IPv4 Prefix:  10.0.0.0/24
DHCP:         Disabled (the Kali IP is set manually)
IPv6:         Disabled
```

Click **Apply** to save the change.

![NatNetwork set to 10.0.0.0/24 with DHCP disabled](2-screenshot-network-settings-1.png)

> **Note:** I first opened the **Host-only Networks** tab by mistake. Host-only networks have no internet access, so the NAT Network has to be configured under the **NAT Networks** tab.

A **NAT Network** was chosen because VMs on the same NAT Network can talk to each other and still reach the internet. This suits a multi-machine attacker/target lab.

## Step 4. Attach the Kali VM to the NAT Network

With the VM powered off: **Settings → Network → Adapter 1**:

```text
Attached to: NAT Network
Name:        NatNetwork
```

## Step 5. Configure a Static IP in Kali

In Kali: **network icon (top-right) → Edit Connections → Wired connection 1 → IPv4 Settings**.

```text
Method:     Manual
Address:    10.0.0.2
Netmask:    24
Gateway:    10.0.0.1
DNS:        10.0.0.1   (8.8.8.8 is the course default)
```

Save, then toggle networking off and on to apply.

![Kali IPv4 settings: Manual, 10.0.0.2 / 24 / 10.0.0.1, DNS 10.0.0.1](4-screenshot-kali-network-settings.png)

## Step 6. Create a Clean VM Snapshot

Once the network and login were working, I took a snapshot as a clean baseline:

```text
Snapshot name: Clean Kali - Network Setup

```

If a later exercise breaks the VM, I can restore it to this point.

---

# 🔎 Lab Verification

| ✅ Test                  | 🧾 Command / Action              | 🎯 Expected Result                | Status |
| ------------------------ | -------------------------------- | --------------------------------- | :----: |
| 🌐 Browse the internet   | Open Firefox and load a website  | Page loads                        |   ✅   |
| 🔢 Check IP address      | `ip a`                           | `10.0.0.2/24` displayed           |   ✅   |
| 📡 Test gateway          | `ping -c 3 10.0.0.1`             | Successful replies                |   ✅   |
| 🌍 Test internet         | `ping -c 3 8.8.8.8`              | Successful replies                |   ✅   |
| 🔎 Test DNS              | `nslookup networkwalks.com`      | Domain resolves                   |   ✅   |
| 🧰 Verify Nmap           | `nmap --version`                 | Version displayed                 |   ✅   |
| 🔄 Verify snapshot       | Restore snapshot, then `ip a`    | Baseline configuration restored   |   ✅   |


---

# 🐞 Problems Encountered & Solutions

## Problem 1. Forgot the Kali login (password and/or username)

**Symptom:** The login screen said "incorrect password". I had changed the default password, and I wasn't sure the username was still correct.

**Solution:** Since the VM is mine, I reset the password from the GRUB boot menu:

1. Power off the VM and start it again.
2. Press a key during the GRUB countdown to stop the timer and keep the menu on screen.
3. Select the normal Kali entry and press **e** to edit it.
4. Find the line starting with `linux` and add `init=/bin/bash` to the end, after `quiet splash`.
5. Press **Ctrl+X** to boot into a root shell.
6. Run:

```bash
mount -o remount,rw /
ls /home
passwd kali
sync
```

7. Power off the VM from VirtualBox and start it again.

`ls /home` showed that the username was still `kali`, so only the password needed resetting.

**Lesson:** Physical or console access to a VM means full control of it. This is why real systems use disk encryption and a GRUB password.

---

## Problem 2. GRUB menu disappeared before I could use it

**Symptom:** The GRUB menu flashed and Kali booted automatically after a short "Booting in 3 seconds" countdown. My Down-arrow key also did nothing.

**Solution:**

- Click inside the VM window first so it captures the keyboard.
- Press **Esc** during the countdown. This froze the timer and kept the menu open.
- If physical arrow keys don't register, use VirtualBox's on-screen keyboard: **Input → Keyboard → Soft Keyboard**.

---

## Problem 3. Firefox opened but websites would not load

**Symptom:** Firefox launched normally, but no page would load.

**Root cause:** The static IP settings (`10.0.0.2 / 24 / 10.0.0.1`) were correct, but the VirtualBox side did not match them:

- The NAT Network was using VirtualBox's default range `10.0.2.0/24`, not `10.0.0.0/24`.
- The VM adapter needed to be attached to that NAT Network.

With the wrong range, the gateway `10.0.0.1` didn't exist, so there was no route to the internet.

**Solution:**

1. In **Network Manager → NAT Networks**, change the IPv4 prefix from `10.0.2.0/24` to `10.0.0.0/24`.
2. Untick **Enable DHCP** and click **Apply**.
3. With the VM off, open **Settings → Network → Adapter 1** and set **Attached to: NAT Network** with `NatNetwork` selected.
4. Start the VM. Firefox then loaded pages normally.

**Lesson:** A static IP only works if the virtual network's address range and gateway match it. When the internet fails, check both ends of the connection.

---

## Problem 4. (Optional) Static IP still has no internet on some systems

On some Kali/NetworkManager setups, internet can still fail after a manual IP. One workaround from the course material:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
```

Then restart networking or reboot. Connection names differ between systems, so check yours first with `nmcli connection show`.

*(I did not need this fix, but I am noting it for others.)*

---

# 💡 What I Learned

### 1. NAT vs NAT Network
A standard NAT adapter isolates each VM. A NAT Network lets VMs on the same network talk to each other while sharing outbound internet access, which is what a multi-VM lab needs.

### 2. Static IPs depend on the network behind them
The address, netmask, and gateway inside Kali must match the range configured in VirtualBox. Most of my internet trouble came from this mismatch.

### 3. Troubleshooting from both sides
When something fails, I now check the guest settings (Kali) and the hypervisor settings (VirtualBox) separately.

### 4. Recovering a locked-out Linux system
I learned how GRUB works, how to edit boot parameters, and how a root shell with `init=/bin/bash` can reset a password. This also shows why physical access is a major security risk.

### 5. Snapshots
A clean snapshot taken before experiments gives me a safe recovery point.

### 6. Documentation
Writing down problems and fixes is as important as the setup itself.

---

# 🔐 Security & Ethical Use

This lab is for education only. I will only test systems I own or have written permission to test.

---

# 🔗 Tools & Resources

- **VirtualBox:** [https://virtualbox.org/wiki/Downloads](https://virtualbox.org/wiki/Downloads)
- **Kali Linux:** [https://kali.org/get-kali](https://kali.org/get-kali)
- **Course:** Network Walks, Cybersecurity at Networkwalks

---

# 👤 Author

**Oshidele Oluwapelumi Adetayo**

LinkedIn / GitHub: [add your link]

---

## 📌 Project Information

**Program:** Cybersecurity at Networkwalks | **Week:** 01 | **Project:** Cybersecurity & Pentesting Lab Setup | **Repository:** GitHub
