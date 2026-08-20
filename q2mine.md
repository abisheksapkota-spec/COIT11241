# COIT11241 — A1 W6 Vulns (12.5%) — Solution Guide

A walkthrough of all 5 questions: methodology, commands used, and final answers submitted.

---

## Question 1 — Directory Indexing (ReactOS VM)

**Task:** Import `ReactOS.ova`, scan it to find where directory indexing is possible.

### Method
1. Imported the VM, set its network adapter to Internal Network, matched Kali's Adapter 2 to the same network.
2. Found the target IP with a ping sweep:
   ```
   nmap -sn 192.168.56.0/24
   ```
3. Ran a full port + version scan:
   ```
   nmap -p- -sV 192.168.56.37
   ```
   Found two web ports running **Abyss httpd**: `80/tcp` and `9999/tcp`.
4. Brute-forced directories on port 80:
   ```
   gobuster dir -u http://192.168.56.37/ -w /usr/share/wordlists/dirb/common.txt
   ```
   Found a `301` redirect on `/ows-bin/` — a strong candidate.
5. Opened it in the browser and confirmed a raw file listing page ("Index of /ows-bin/"), containing a bonus file `secretfile.html` (a red heart image — an Easter egg, not relevant to the answer).

### ✅ Answer
```
/ows-bin/
```

---

## Question 2 — Banner Grab (quiz_router VM)

**Task:** Import `quiz_router.ova`, scan for open ports, ignore SSH/DNS/HTTP/HTTPS, and grab the banner from any other open TCP port.

### Method
1. Imported and booted the VM (allowed ~3 minutes for full boot).
2. Found the target via ping sweep — resolved as `router.lan` at `192.168.56.32`.
3. Ran a full port scan (needed aggressive timing/rate flags due to the router rate-limiting responses):
   ```
   sudo nmap -sV -p- --min-rate=1000 192.168.56.32
   ```
4. Result showed the expected ports (22, 53, 80, 443 — all to be ignored) **plus** one extra:
   ```
   1/tcp open  tcpmux?
   ```
5. Nmap's service-fingerprint block captured the actual banner text embedded in the response (movie-quote style banner), confirmed via:
   ```
   nc 192.168.56.32 1
   ```

### ✅ Answer
```
I am gunna make him an offer he cannot refuse.
```

---

## Question 3 — Authenticated Greenbone Scan (vulns_root_root VM)

**Task:** Import `vulns_root_root.ova`, confirm SSH access (`root/root`), run an **authenticated** Greenbone scan, export all results (including hidden), and paste the full report.

### Method
1. Imported and booted the VM; confirmed it was reachable at `192.168.56.77`.
2. Confirmed manual SSH access from the Greenbone console:
   ```
   ssh -o HostKeyAlgorithms=ssh-rsa root@192.168.56.77
   ```
   (The `-o HostKeyAlgorithms=ssh-rsa` override was required because the target runs **Dropbear SSH**, which only offers a legacy `ssh-rsa` host key type.)
3. In the Greenbone web UI (`https://<greenbone-ip>`):
   - Created an SSH credential (Username+Password: `root` / `root`)
   - Created a target (`192.168.56.77`, SSH credential attached, port 22)
   - Created and ran a task using the **Full and fast** scan config
4. **Issue encountered:** Despite multiple fresh credential/target/task combinations across 3 separate scan runs (~90+ minutes total), Greenbone's scan engine consistently reported:
   ```
   SSH Login Failed For Authenticated Checks
   ```
   Investigation of the report's own diagnostic output showed the scanner and target actually **do** share compatible algorithms (`ssh-rsa` host key, matching KEX/encryption/MAC algorithms) — ruling out a simple mismatch. The most likely cause is a lower-level incompatibility between Greenbone's libssh-based scan engine and this specific Dropbear SSH implementation.
5. Applied the "show all, including hidden" filter before exporting:
   ```
   apply_overrides=0 levels=chmlgf min_qod=0 result_hosts_only=0
   ```
6. Exported the full plain-text report (17 of 21 results shown after filtering) and submitted it, along with a note explaining the authentication issue and the evidence gathered.

### Key findings in the report
- **High (7.5):** Diffie-Hellman Ephemeral Key Exchange DoS Vulnerability (SSH, D(HE)ater) — CVE-2002-20001, CVE-2022-40735, CVE-2024-41996
- **Low (2.6):** TCP Timestamps Information Disclosure
- **Low (2.1):** ICMP Timestamp Reply Information Disclosure
- Multiple Log-level entries: SSH server fingerprinting (Dropbear), OS detection (Linux/Unix), missing HTTP security headers, PQC KEX algorithm absence, and the authentication failure details themselves.

### ✅ Answer
Full text export of the Greenbone report (17 results), with an explanatory note on the authentication failure, submitted in the answer box. See the actual submission for the complete text — too long to duplicate here in full, but the structure is:
```
[Authentication note explaining SSH login failure + evidence of algorithm compatibility]

I Summary
=========
[Scan metadata: task vulns-scan-v3, 17 of 21 results, host 192.168.56.77]

II Results per Host
====================
[17 individual NVT findings, each with Summary / Detection Result / 
Impact / Solution / References]
```

---

## Question 4 — WAF Identification (brambles.com.au)

**Task:** Identify the web application firewall used by `brambles.com.au`.

### Method
Ran `wafw00f` (a dedicated WAF fingerprinting tool) directly against the target:
```
wafw00f https://www.brambles.com.au
```

Output:
```
[+] The site https://www.brambles.com.au is behind ASP.NET Generic (Microsoft) WAF.
```

### ✅ Answer
```
ASP.NET Generic (Microsoft) WAF
```

---

## Question 5 — Atomic Red Team T1614.001 (Win8 VM)

**Task:** Run Atomic Test 1 of T1614.001 ("System Language Discovery") in the Win8 VM and submit the command(s) and output.

### Method
1. Initially attempted to install the full Atomic Red Team PowerShell framework, but hit multiple blockers on the old Win8 VM:
   - No internet access (VirtualBox NIC was disabled — fixed by enabling Adapter 1 as NAT and doing a full cold boot)
   - TLS handshake failure (fixed by forcing TLS 1.2: `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12`)
   - Missing `Microsoft.PowerShell.Archive` module — not available on this VM's PowerShell 4.0 (requires PowerShell 5.0+)
2. **Pivoted to a simpler approach:** looked up the actual Atomic Red Team Test #1 definition for T1614.001 directly from the official GitHub repo — it's just a single registry query command, no framework required.
3. Ran the command directly:
   ```
   reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language
   ```

### ✅ Answer
**Command:**
```
reg query HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language
```

**Output (abridged — full list of ~300 language codes all pointing to `l_intl.nls`, ending with the key result):**
```
HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Nls\Language
    1009    REG_SZ    l_intl.nls
    1809    REG_SZ    l_intl.nls
    ... [full list of language codes] ...
    InstallLanguage    REG_SZ    0409
    Default    REG_SZ    0409
```
`0409` = English (United States) — the system's configured language, which is what this MITRE ATT&CK technique (System Language Discovery) is designed to reveal.

---

## Summary Table

| # | Question | Answer |
|---|----------|--------|
| 1 | Directory indexing location | `/ows-bin/` |
| 2 | Port 1 banner | `I am gunna make him an offer he cannot refuse.` |
| 3 | Authenticated Greenbone scan | Full report + auth-failure note (see Q3 submission) |
| 4 | WAF on brambles.com.au | `ASP.NET Generic (Microsoft) WAF` |
| 5 | Atomic Test 1, T1614.001 | `reg query ...\Nls\Language` + output (`InstallLanguage`/`Default` = `0409`) |

---

## Key Lessons / Reusable Techniques

- **`gobuster`/`dirb`** for directory brute-forcing; always manually verify a hit in the browser rather than trusting a status code alone.
- **`nmap --min-rate` / `-T4`** to defeat routers that rate-limit scan responses; `-sT` (full connect) sometimes gets through where `-sS` (SYN stealth) is filtered.
- **`nc <ip> <port>`** is the cleanest way to grab a raw service banner once nmap flags an unusual port.
- **`ssh -o HostKeyAlgorithms=ssh-rsa`** is required when a target's SSH server only offers legacy host key types (common with Dropbear).
- Greenbone authenticated scans can fail even with correct credentials if the scanner's SSH library can't negotiate with a legacy SSH daemon — check the "SSH Login Failed" NVT's diagnostic algorithm dump to confirm whether it's a real mismatch or a deeper compatibility issue.
- Not every Atomic Red Team test needs the full framework installed — check the test definition on GitHub first; some are a single one-line command.
- Always double-check VirtualBox NIC settings (Enabled + correct mode) after any snapshot/settings change — a "Not Present" adapter status means Windows lost the hardware and needs a full cold boot to re-detect it.
