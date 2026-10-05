# 01: Planning and Network Diagram

## Goal
Map out the lab before building it so I understand how data flows between machines.

## Hardware Requirements
- 16 GB RAM and 250 GB free disk recommended
- Host used: AMD Ryzen 7 2700X, 16 GB RAM, 1 TB HDD, Windows 10 Pro

## Planned Environment
- 2 servers: Windows Server 2022 (Active Directory), Ubuntu Server (Splunk)
- 2 clients: Windows 10 (target), Kali Linux (attacker)
- Domain: `mydfir.local`
- Network: `192.168.10.0/24`

## Diagram
Built in draw.io. Dotted green lines show log forwarding from the domain controller and target machine to Splunk. The attacker (red) is not forwarding logs.

![Network diagram](../images/01-planning/network-diagram.svg)

## What I Learned
[Your notes, e.g. why diagramming first helps, how the data flows.]
