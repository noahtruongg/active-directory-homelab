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

## Issues and Fixes
- **Issue: Atomic Red Team's local account creation test (T1136.001) produced no events in Splunk at first.**

After running the test, I searched Splunk expecting to see the new local account creation show up immediately, but the search came back empty — which initially looked like a logging failure. Rather than a config error like the other forwarder issues in this project, this turned out to be a genuine detection gap: my default Sysmon/Windows Event Log setup wasn't capturing this specific technique the way I assumed it would

**Fix:** After waiting and re-running the search, the event eventually appeared, showing that the issue wasn't a total blind spot, but a lag between the activity happening and it reaching Splunk. This distinguished "nothing is being logged" from "logging is just delayed," and highlighted that detection coverage has to be tested technique-by-technique rather than assumed from a general telemetry setup. 
  
## What I Learned
This section made log-based detection feel real instead of just a concept. Running the brute-force attack from Kali and then finding the exact same activity in Splunk — 20 failed logons (Event ID 4625) packed into a single second, followed by one successful logon (4624) — showed me how a login pattern showed me how a single login does not look suspicious on its own, but seeing 20 of them occur at the same time makes the attack evident. Pivoting from that successful logon to the source workstation and attacker IP also clicked for me: an event ID isn't just a status code, it's a path you can follow back to where an attack originated from. Atomic Red Team's local account creation test (T1136.001) was the more important lesson, although it initially produced no events in Splunk at all, which meant my logging setup had a real blind spot, not telemetry isn't always instant, so "no events yet" and "no events, period" aren't the same thing, and is worth distinguishing before assuming a detection gap. Altogether, this section reframed detection for me: it's not enough to generate logs, you have to actually test whether your setup catches the specific behavior you care about, and Atomic Red Team is exactly the tool for finding that out before an attacker does. 

## Cleanup
Once the attack simulation and detection testing were done, I tool VirtualBox snapshots of every VM (Target-PC, the Splunk server, and Kali) so I could restore each one to a known-good state before experimenting further. This was a direct lesson from the BSOD I ran into earlier in the project after the domain join — rebuilding a VM from scratch once was enough to make snapshotting before any risky step (running more attacks, testing new Atomic Red Team techniques, etc.) a habit.
