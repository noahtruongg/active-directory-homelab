# 02: Virtual Machine Setup

## Goal
Install four VMs in VirtualBox: Windows 10, Kali Linux, Windows Server 2022, and Ubuntu Server (Splunk).

## Steps

### VirtualBox
- Downloaded VirtualBox and verified the installer's SHA-256 checksum with PowerShell:
  ```powershell
  Get-FileHash .\VirtualBox-installer.exe
  ```
- Installed the Microsoft Visual C++ dependency when prompted.

### Windows 10
- Created an ISO with Microsoft's Media Creation Tool
- VM: 4 GB RAM, 1 CPU, 50 GB disk, Windows 10 Pro, custom install

### Kali Linux
- Imported the pre-built VirtualBox image (extracted with 7-Zip)

### Windows Server 2022
- Downloaded the ISO from the Microsoft Evaluation Center
- VM name `ADDC01`: 4 GB RAM, 50 GB disk
- Selected **Standard Evaluation (Desktop Experience)** for the GUI

### Ubuntu Server (Splunk)
- Ubuntu Server 22.04: 8 GB RAM, 2 CPUs, 100 GB disk (larger since it ingests and searches data)
- Ran `sudo apt-get update && sudo apt-get upgrade -y`

## Screenshots
![VirtualBox with all four VMs](../images/02-vm-setup/virtualbox-vm-list.png)
![Windows Server desktop](../images/02-vm-setup/windows-server-install.png)

## Issues and Fixes
[Anything that went wrong here, e.g. ISO mounting, disk space, BIOS virtualization settings, and how you fixed it. Also add it to the troubleshooting log.]

## What I Learned
[Your notes.]
