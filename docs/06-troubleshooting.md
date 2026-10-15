# 06: Troubleshooting Log

Problems I hit while building the lab and how I solved them.

| Problem | Cause | Fix |
|---|---|---|
| VM got the default IP `10.0.2.15` instead of an address on `192.168.10.0/24` | Network adapter was attached to VirtualBox's default NAT instead of the custom NAT Network | Created a NAT Network (`ad-project`) and reattached the VM's adapter to it |
| "NAT Network" option wasn't visible in the adapter dropdown | Basic view's dropdown list was cut off on screen | Switched to Expert Mode in the VM's network settings |
| Windows installed as Home edition instead of Pro, and setup threw "Windows cannot find the Microsoft Software License Terms" | VirtualBox's unattended installation feature was auto-generating an answer file and silently picking the edition/interfering with Setup | Unchecked "Proceed with Unattended Installation" so Setup ran interactively and Windows 10 Pro could be selected manually |
| VirtualBox would not run 64-bit VMs | AMD-V?SVM was disabled in BIOS | Enabled virtualization (SVM) in BIOS |
| Domain join failed: "domain controller could not be contacted" | Target PC's DNS was pointed at `8.8.8.8` instead of the domain controller | Set the Target PC's DNS server to the domain controller's IP |
| BSOD "CRITICAL_PROCESS_DIED" right after joining the domain, with no snapshot to roll back to | Unknown root cause; Windows recovery tools (Startup Repair, System Restore, `bootrec`, manual `bcdboot` rebuild) couldn't fix it | Deleted and rebuilt the Target VM from the beginning; now take a snapshot before any major change |
| Splunk Universal Forwarder connected successfully, but `index=endpoint` showed 0 events | `inputs.conf' stanzas were mistyped as `[WinEvent://...]` instead of `[WinEventLog://...]`, so Splunk never recognized them as valid inputs | corrected the stanza names to `WinEventLog` and restarted the forwarder |
| ADDC01 showed 0 evetns under both `index=endpoint` and `index=_internal` despite a correct, working config | `splunk.log` was stale - the forwarder service hadn't been restarted since initial setup, so the log wasn't showing current activity | Restarted the SplunkForwarder service, which revealed the connection had just been timing out intermittently before stabilizing |
| Splunk search results seemed to be missing events or a host, even with data confirmed flowing | Timezone/clock mismatch between the browser's search time range (e.g. "Last 24 hours") and the Splunk server's clock | Set the search time range to "All time" to confirm all data |
| Running all VMs (Splunk, ADDC01, Target) simultaneously crashed the host PC | VM memory allocations totaled 100% of the host's 16 GB RAM, leaving nothing for the host OS | Reduced each VM's base memory (e.g. Splunk 8 GB > 4 GB) to leave head room for the host |
| Splunk Enterprise's web UI wouldn't load correctly in Firefox, though other tools worked fine | Browser-specific rendering issues | Downloaded and used Chrome to access the Splunk web UI instead |

