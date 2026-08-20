# Vulnerability Scanning & Recon — General Method Guide

A reusable walkthrough for common VM-based recon/scanning tasks. Every VM/IP will be different for you — swap in your own target IP wherever you see `<target-ip>`.

---

## 1. Finding Directory Indexing on a Web Server

**Goal:** Find a directory on a web server that's misconfigured to show a raw file listing instead of a proper page.

### Steps
1. Import the VM into VirtualBox, put its network adapter on **Internal Network**, and set your attacker VM's second NIC (Adapter 2) to the same internal network.
2. Find the target's IP:
   ```
   nmap -sn <subnet>/24
   ```
   e.g. `nmap -sn 192.168.56.0/24`
3. Full port + version scan to find web ports:
   ```
   nmap -p- -sV <target-ip>
   ```
4. For each web port found, brute-force directories:
   ```
   gobuster dir -u http://<target-ip>:<port>/ -w /usr/share/wordlists/dirb/common.txt
   ```
5. Watch for `301` redirects or unusual paths in the output — these are worth checking manually.
6. Open the flagged path directly in a browser:
   ```
   http://<target-ip>:<port>/<path>/
   ```
   Directory indexing looks like a plain **"Index of /path/"** page listing files with no styling — that's your answer. A `403 Forbidden` means indexing is disabled there.

---

## 2. Grabbing a Service Banner from an Unusual Port

**Goal:** Find a non-standard open port and read whatever text/banner it returns.

### Steps
1. Import and boot the target VM (give it time to fully boot if it's slow).
2. Find its IP:
   ```
   nmap -sn <subnet>/24
   ```
3. Full port scan — use aggressive timing if the target seems to rate-limit responses:
   ```
   sudo nmap -sV -p- --min-rate=1000 <target-ip>
   ```
   If results look incomplete or mostly "filtered," try a full connect scan instead:
   ```
   sudo nmap -p- -sT -T4 <target-ip>
   ```
4. Ignore the expected/common ports (SSH 22, DNS 53, HTTP 80, HTTPS 443, etc. — check what the task tells you to ignore).
5. For whatever unusual port remains, connect directly to read the banner:
   ```
   nc <target-ip> <port>
   ```
   Nmap's own version-detection output sometimes also captures the banner text directly in its "unrecognized service" fingerprint block — check there too if `nc` doesn't return anything.

---

## 3. Running an Authenticated Vulnerability Scan (Greenbone/OpenVAS)

**Goal:** Confirm SSH credentials work, then run a scan that logs into the target for deeper (authenticated) results, and export everything including normally-hidden results.

### Steps
1. Import and boot the target VM; note its IP.
2. From the Greenbone console, test SSH manually first:
   ```
   ssh root@<target-ip>
   ```
   If you get an RSA/algorithm error (common with older SSH servers like Dropbear):
   ```
   ssh -o HostKeyAlgorithms=ssh-rsa root@<target-ip>
   ```
   If you've scanned this VM before and get a "someone could be eavesdropping" warning:
   ```
   ssh-keygen -R <target-ip>
   ```
3. In the Greenbone web UI:
   - **Configuration > Credentials** → create a new credential (Username + Password, matching what the target expects)
   - **Configuration > Targets** → create a target with the IP, attach the SSH credential, port 22
   - **Scans > Tasks** → create a task, select the target, use scan config **"Full and fast"**, then start it
4. Wait for the scan to finish (can take 20 min to over an hour).
5. Open the report and check whether authentication actually succeeded — look for the NVT result **"SSH Authorization Check"** (success) vs **"SSH Login Failed For Authenticated Checks"** (failure). If it failed:
   - Try a completely fresh credential + target + task (don't just edit the old ones — some Greenbone builds keep stale state)
   - Check the "SSH Login Failed" result's own diagnostic output — it lists the algorithms the scanner supports vs what the target supports. If they genuinely overlap, the failure is likely a deeper compatibility issue (e.g. between Greenbone's libssh engine and an old SSH daemon like Dropbear) rather than a credentials problem — document this rather than endlessly retrying.
6. Before exporting, apply a filter to unhide everything:
   ```
   apply_overrides=0 levels=chmlgf min_qod=0 result_hosts_only=0
   ```
   (`levels=chmlgf` = Critical, High, Medium, Low, Log, False Positive — all of them; `min_qod=0` removes the confidence-threshold hiding)
7. Export as plain text (or copy the Results tab content) — that's your report.

---

## 4. Identifying a Web Application Firewall (WAF)

**Goal:** Determine which WAF (if any) protects a given website.

### Steps
1. Install the fingerprinting tool if needed:
   ```
   sudo apt install wafw00f -y
   ```
2. Run it against the target:
   ```
   wafw00f https://<target-domain>
   ```
3. The output directly names the detected WAF, e.g.:
   ```
   [+] The site https://example.com is behind <WAF name> WAF.
   ```
4. If inconclusive, cross-check manually via response headers:
   ```
   curl -I https://<target-domain>
   ```
   Look for telltale headers/cookies (`cf-ray`/`Server: cloudflare`, `X-Akamai-*`, `Incap-*`/`X-Iinfo`, `X-Sucuri-ID`, etc.)

---

## 5. Running an Atomic Red Team Test

**Goal:** Execute a specific MITRE ATT&CK atomic test and capture the command + output.

### Steps
1. **Before installing the full framework, check what the test actually does** — search the official repo:
   ```
   https://github.com/redcanaryco/atomic-red-team/blob/master/atomics/<TECHNIQUE-ID>/<TECHNIQUE-ID>.md
   ```
   Many tests are just a single simple command (registry query, file operation, network call) that you can run directly without installing anything.

2. **If you do need the full framework** (for more complex/multi-step tests):
   - Make sure the VM has internet access:
     - VirtualBox → Settings → Network → Adapter 1 → Enabled, Attached to **NAT**
     - If the adapter shows "Not Present" in Windows (`Get-NetAdapter`), do a full cold boot (fully power off, not just restart)
   - Fix TLS issues on older Windows/PowerShell if downloads fail:
     ```powershell
     [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
     ```
   - Install:
     ```powershell
     IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing)
     Install-AtomicRedTeam -getAtomics
     ```
     Note: this needs the `Microsoft.PowerShell.Archive` module, which requires PowerShell 5.0+ — not available on older Windows builds (e.g. stock Win8 ships PowerShell 4.0). If it fails, fall back to running the test's underlying command manually (step 1).

3. Run the test directly, e.g.:
   ```powershell
   Invoke-AtomicTest <TECHNIQUE-ID> -TestNumbers 1
   ```
   or, if running the command manually, just execute it in Command Prompt / PowerShell as Administrator.

4. Copy **both** the exact command executed and its full output — that's your submission.

---

## General Tips

- **VirtualBox networking:** Adapter 1 = NAT (internet access), Adapter 2 = Internal Network matching your attacker VM (for VM-to-VM scanning). Don't mix these up.
- **Take a snapshot** right after a target VM boots successfully — lets you revert instantly instead of re-downloading/re-importing if something breaks.
- **`sudo` your nmap scans** for more accurate results (raw socket access).
- If a scan seems to hang or return incomplete/filtered results, the target is likely rate-limiting — slow down (`-T2`) or speed up with explicit rate control (`--min-rate`), and try both SYN (`-sS`, nmap's default) and full-connect (`-sT`) scan types.
- When something doesn't work after multiple genuine attempts, it's fine to document the failure with evidence (error messages, diagnostic output) rather than endlessly retrying — that's often exactly what's being assessed.
