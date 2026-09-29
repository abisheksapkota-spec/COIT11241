# Assignment 2 - Full Demo Walkthrough (Q1 to Last)

Every step, which device it's on, and the exact command to type.

---

## SECTION 1 - Reconnaissance (Quiz 1)
**Device: Win8 VM, PowerShell (as Administrator)**

| Step | Command | What it shows |
|---|---|---|
| 1 | `Get-NetAdapter \| Select-Object Name, MacAddress` | Lists adapters. Confirm "Ethernet 2" is present (MAC 08-00-27-26-52-84). |
| 2 | `Get-NetIPConfiguration` | Shows config for every adapter. Scroll to the **Ethernet 2** section: IPv4 `192.168.56.50`, gateway `192.168.56.52`, DNS `8.8.8.36`. |
| 3 | `Resolve-DnsName example.com` | Resolves example.com to `192.168.56.76` on this network. |
| 4 | `New-PSSession -ComputerName localhost -Credential (Get-Credential)` | Opens a remote PowerShell session. A login popup appears - enter `vagrant` / `vagrant`. |
| 5 | `Get-NetTCPConnection` | Lists all active/listening TCP connections (e.g. WinRM on port 5985). |

---

## SECTION 2 - Vulnerability Detection (Quiz 2)
**Device: mixed - see each row**

| Step | Device | Command / Action | What it shows |
|---|---|---|---|
| 1 | Kali | `wafw00f <url>` (e.g. `wafw00f https://brambles.com.au`) | Site is protected by an ASP.NET Generic (Microsoft) WAF. |
| 2 | (saved report) | Open the Greenbone scan report | High severity (CVSS 7.5): SSH vulnerable to D(HE)ater DoS via DHE key exchange. |
| 3 | (saved report) | Directory scan results | Indexing enabled on `/ows-bin/` (ReactOS VM). |
| 4 | (saved report) | Port scan results | Non-standard open port returned a plain-text banner (router VM). |
| 5 | Win8, PowerShell | `reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language` | Atomic Red Team T1614.001 - System Location Discovery. Output ends in `InstallLanguage REG_SZ 0409` / `Default REG_SZ 0409`. |

*(Greenbone, directory scan, and port scan are shown from your saved quiz results - not re-run live, since they're too slow to redo in the demo window.)*

---

## SECTION 3 - Internal Attacks (Quiz 3)
**Device: Win8 VM, PowerShell (as Administrator)**

| Step | Command | What it does / shows |
|---|---|---|
| 1 | `rundll32.exe keymgr.dll,KRShowKeyMgr` | Runs the attack (Atomic Red Team T1003 Test #6 - simulated Credential Manager dump). This generates the Sysmon event you'll detect next. |
| 2 | `Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' \| Where-Object {$_.Message -like '*keymgr*'}` | Filters the Sysmon Operational log for the keymgr-related event. Returns one matching event. |
| 3 | `Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' \| Where-Object {$_.Message -like '*keymgr*'} \| Select-Object -First 1 -ExpandProperty Message` | Expands the full event message. Shows `RuleName: technique_id=T1218.011,technique_name=rundll32.exe` and the full `CommandLine`. |

Never open Event Viewer's GUI for this section - PowerShell only, per the rubric.

---

## SECTION 4 - Network Sniffing (Quiz 4)
**Device: Kali VM (attacker/sniffer) + sniff VM (target, generates the flood)**

| Step | Device | Command | What it shows |
|---|---|---|---|
| 1 | Kali | `ip a` | Confirms `eth1` is up and on the correct internal network ("lan"), IP `192.168.56.34`. |
| 2 | Kali | `sudo tcpdump -i eth1 -nn` | Captures live packets. Shows a continuous stream of `Flags [S]` (SYN) packets from `172.16.1.35:9877` to `192.168.56.34:19`, with no `[S.]` (SYN-ACK) ever returned - a textbook TCP SYN flood. |

---

## Wrap-up talking point
Summarise: confirmed live network config with PowerShell, surfaced real vulnerabilities (Greenbone/wafw00f/Atomic Red Team), traced an internal attack to MITRE ATT&CK T1218.011 via Sysmon, and captured/interpreted a live TCP SYN flood with tcpdump.
