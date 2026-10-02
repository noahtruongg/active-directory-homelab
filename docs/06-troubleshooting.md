# 06: Troubleshooting Log

Problems I hit while building the lab and how I solved them.

| Problem | Cause | Fix |
|---|---|---|
| Domain join failed: "domain controller could not be contacted" | Target PC's DNS pointed to `8.8.8.8` | Set DNS to the domain controller IP |
| [Windows Server ISO would not mount / unattended install artifacts] | [cause] | [fix] |
| [VirtualBox would not run 64-bit VMs] | [AMD-V/SVM disabled in BIOS] | [Enabled virtualization in BIOS] |
| [Low disk space on host] | [cause] | [fix] |
| [Splunk forwarder not sending logs] | [e.g. service not restarted, wrong log on account] | [fix] |

Add a row for anything else you ran into. Even small errors show your troubleshooting process.
