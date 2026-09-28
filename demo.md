

Commands used reference · MD
# Commands Used - Quick Reference
 
## Quiz 1 - Reconnaissance
 
| Q | Command | Purpose |
|---|---------|---------|
| Q1 | `Get-NetAdapter \| Select-Object Name, MacAddress` | List network adapters and their MAC addresses |
| Q1 | `Get-NetIPConfiguration` | Shows IP config for every adapter; scroll to the Ethernet 2 section for IP, gateway, DNS |
| Q4 | `Resolve-DnsName example.com` | Resolve a domain name to an IP on this network |
| Q5 | `New-PSSession -ComputerName localhost -Credential (Get-Credential)` | Open a remote PowerShell session |
| Q5 | `Get-NetTCPConnection` | List all active/listening TCP connections |
 
## Quiz 2 - Vulnerability Detection
 
| Q | Command | Purpose |
|---|---------|---------|
| - | `wafw00f <url>` | Fingerprint whether a site is behind a WAF |
| - | `reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language` | Atomic Red Team T1614.001, query registry for system locale/language |
 
*(Greenbone scan, directory scan, and port scan shown from saved quiz results, not re-run live.)*
 
## Quiz 3 - Internal Attacks
 
| Q | Command | Purpose |
|---|---------|---------|
| Q1 | `rundll32.exe keymgr.dll,KRShowKeyMgr` | Atomic Red Team T1003 Test #6, simulate Credential Manager dump (generates the Sysmon event) |
| Q1 | `Get-WinEvent -LogName 'Microsoft-Windows-Sysmon/Operational' \| Where-Object {$_.Message -like '*keymgr*'}` | Filter Sysmon log for the keymgr-related event |
| Q1 | `... \| Select-Object -First 1 -ExpandProperty Message` | Expand the full event message to reveal the technique_id=T1218.011 line |
 
## Quiz 4 - Network Sniffing
 
| Q | Command | Purpose |
|---|---------|---------|
| - | `ip a` | Show Kali's network interfaces and IP addresses |
| - | `sudo tcpdump -i eth1 -nn` | Capture live packets on the lab-facing interface (no DNS/port name resolution) |
 
