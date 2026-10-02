# 05: Attack Simulation and Detection

> All activity was performed against machines I own in an isolated lab.

## Goal
Run a brute-force attack from Kali, find the evidence in Splunk, then use Atomic Red Team to generate more telemetry and test detection coverage.

## Part A: RDP Brute Force

### Preparation
- Set Kali's static IP to `192.168.10.250` and verified connectivity
- Built a small wordlist from `rockyou.txt`:
  ```bash
  head -n 20 rockyou.txt > password.txt
  ```
  (then added the known test password for `tsmith` to the list)
- Enabled Remote Desktop on the target and allowed `jsmith` and `tsmith`

### Attack
```bash
crowbar -b rdp -u tsmith -C password.txt -s <target-ip>/32
```
Crowbar reported a successful RDP login for `tsmith`.

### Detection in Splunk
```
index=endpoint tsmith
```
| Event ID | Meaning | Count |
|---|---|---|
| 4625 | An account failed to log on | 20 |
| 4624 | An account was successfully logged on | 1 |

- The 20 failed attempts occurred within the same second, a clear brute-force indicator
- The successful logon (4624) listed workstation name `kali` and the attacker's IP

![Failed logons 4625](../images/05-attack-detection/splunk-4625.png)
![Successful logon 4624](../images/05-attack-detection/splunk-4624.png)

## Part B: Atomic Red Team

- Set PowerShell execution policy, added a Defender exclusion so Atomic Red Team files were not removed, and installed the framework on the target
- Tests are organized by MITRE ATT&CK technique ID

| Technique | Test | Result |
|---|---|---|
| T1136.001 (Create Account: Local) | `Invoke-AtomicTest T1136.001` | No events at first, exposing a visibility gap |
| T1059.001 (PowerShell) | `Invoke-AtomicTest T1059.001` | Events appeared in Splunk, including the bypass/no-profile command |

The new-local-user activity did eventually appear after a delay, so telemetry can lag behind execution.

![Atomic Red Team test](../images/05-attack-detection/atomic-test.png)
![Splunk PowerShell event](../images/05-attack-detection/splunk-powershell.png)

## What I Learned
- What brute-force activity looks like in Windows event logs
- How to pivot from an event ID to the source workstation and IP
- Atomic Red Team helps validate whether your logging can actually see an attack
- Next steps: build Splunk alerts and dashboards for these events
- [Your own notes]

## Cleanup
Took VirtualBox snapshots of every VM so I can restore to a known-good state.
