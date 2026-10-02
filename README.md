# Active Directory Home Lab: Domain Setup, SIEM Logging, and Attack Detection

A virtualized enterprise environment built in VirtualBox to practice Active Directory administration, endpoint logging with Sysmon, log analysis in Splunk, and detecting a simulated brute-force attack.

> **Note:** This lab was completed by following the [myDFIR Active Directory Project series](https://www.youtube.com/playlist?list=PLGrcVHQv6mp-_5jY1XU1SJ3tjoUMrTI7E). The write-ups here are my own documentation of the build, including my configuration values, screenshots, and what I learned along the way.

## Network Diagram

![Network diagram](images/01-planning/network-diagram.svg)

## Lab Environment

| Machine | OS | Role | IP Address |
|---|---|---|---|
| ADDC01 | Windows Server 2022 | Domain controller (`mydfir.local`), Sysmon, Splunk UF | 192.168.10.7 (static) |
| Target-PC | Windows 10 Pro | Domain-joined endpoint, Sysmon, Splunk UF, Atomic Red Team | 192.168.10.100 (static) |
| splunk | Ubuntu Server 22.04 | Splunk Enterprise (SIEM) | 192.168.10.10 (static) |
| kali | Kali Linux | Attacker machine | 192.168.10.250 (static) |

**Network:** VirtualBox NAT Network `ad-project` (192.168.10.0/24)
**Domain:** `mydfir.local`

## Tools Used

VirtualBox, Windows Server 2022, Active Directory Domain Services, Windows 10, Ubuntu Server, Splunk Enterprise, Splunk Universal Forwarder, Sysmon, Kali Linux, Crowbar, Atomic Red Team, MITRE ATT&CK, draw.io

## Skills Demonstrated

- Virtualization and virtual network configuration (NAT networks, static IPs, DNS)
- Active Directory administration: installing AD DS, promoting a domain controller, creating OUs and users, joining a machine to a domain
- Endpoint telemetry with Sysmon and Splunk Universal Forwarder (`inputs.conf`)
- Splunk indexing, receiving ports, and SPL searches
- Log analysis using Windows Event IDs (4624, 4625)
- Simulating attacks and mapping activity to MITRE ATT&CK techniques
- Identifying detection gaps
- Troubleshooting (DNS resolution, service permissions, VirtualBox issues)

## Walkthrough

1. [Planning and Network Diagram](docs/01-planning-and-diagram.md)
2. [Virtual Machine Setup](docs/02-vm-setup.md)
3. [Splunk and Sysmon Configuration](docs/03-splunk-and-sysmon.md)
4. [Active Directory Setup and Domain Join](docs/04-active-directory.md)
5. [Attack Simulation and Detection](docs/05-attack-and-detection.md)
6. [Troubleshooting Log](docs/06-troubleshooting.md)

## Key Findings

- The simulated RDP brute-force attack generated **20 failed logons (Event ID 4625)** in the same second, followed by **one successful logon (4624)** whose workstation name and source IP matched the Kali machine.
- Atomic Red Team's local account creation test (T1136.001) initially produced no events in Splunk, exposing a **gap in detection visibility** in the default logging setup.
- [Add anything else you noticed]

## Reflection

[Write 3 to 5 sentences in your own words: what was hardest, what clicked, what you would add next, e.g. a firewall/IDS, alerts and dashboards in Splunk, more attack scenarios.]

## Disclaimer

All attacks were performed against machines I own inside an isolated lab environment, for educational purposes only.
