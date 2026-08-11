# COIT11241 – Week 5 Vulnerabilities
## Step-by-Step Lab Guide

### Your lab environment

| VM | Role | IP | Host→Guest port | Login |
|---|---|---|---|---|
| Greenbone | Scanning target/tool | 192.168.56.2 | 2443→443 | admin/admin |
| Kali | Attack box (run tools from here) | 192.168.56.34 | — | kali/kali |
| Ms2 | Victim | 192.168.56.35 | 35080→80 | msfadmin/msfadmin |
| Win8 | PoSh | 192.168.56.50 | — | vagrant/vagrant |

Unless stated otherwise, **run every command from the Kali VM** (that's your attacker/scanner box). Start all VMs in VirtualBox before you begin.

---

## Part 1 — WAF fingerprinting with wafw00f

1. **Open the Kali VM** and log in (`kali` / `kali`).
2. **Open a terminal.**
3. Run wafw00f against CQU's site:
   ```
   wafw00f www.cqu.edu.au
   ```
4. Run it again against Nissan's site for Q1:
   ```
   wafw00f https://www.nissan.com.au
   ```
5. **Read the output.** Look for a line like `[+] The site ... is behind <VENDOR> WAF` — that vendor name is your answer. If it prints "No WAF detected," that's a legitimate result too.
6. If you get a connection error, check Kali has internet access (not just the host-only 192.168.56.0/24 lab network) — `ping 8.8.8.8` to confirm.

---

## Part 2 — Scan Greenbone using Greenbone

1. **Start the Greenbone VM** (Scanning, 192.168.56.2) and let it fully boot.
2. **Check port forwarding**: in VirtualBox → Greenbone VM → Settings → Network → Adapter (NAT) → Port Forwarding, confirm a rule exists mapping host `2443` → guest `443`. Add it if missing.
3. **On your host machine's browser**, go to:
   ```
   https://127.0.0.1:2443
   ```
   Accept the self-signed certificate warning (Advanced → Proceed).
4. **Log in** with `admin` / `admin`.
5. **Create the SSH credential:**
   - Go to **Configuration → Credentials**.
   - Click the **New Credential** icon (star/plus icon, top left).
   - Name: e.g. `local-ssh`
   - Type: **Username + Password**
   - Username: `admin`, Password: `admin`
   - Click **Save**.
6. **Create the target:**
   - Go to **Configuration → Targets**.
   - Click **New Target**.
   - Name: e.g. `Greenbone-local`
   - Hosts: `127.0.0.1`
   - Under **Credentials for authenticated checks**, select the SSH credential you just made (port 22).
   - Click **Save**.
7. **Create the task:**
   - Go to **Scans → Tasks**.
   - Click **New Task**.
   - Name: e.g. `Greenbone self-scan`
   - Scan Targets: select the target from step 6.
   - Scan Config: leave as **Full and fast**.
   - Find the **Minimum QoD (Quality of Detection)** field and set it to **1%** (this makes Greenbone report even low-confidence findings — normally filtered out).
   - Click **Save**.
8. **Start the scan**: click the ▶ (play) icon next to your task.
9. **Wait ~30 minutes.** While it runs, open `Wk5_Greenbone_Greenbone_report.pdf` from the unit website to preview what a finished report looks like.
10. **Review the finished report:**
    - Click the task → click the report (usually named by date/time).
    - **Open ports**: check the **Hosts** or **Ports** tab — it lists every open port found (e.g. 22/tcp, 443/tcp, 1883/tcp, etc.).
    - **Were authenticated checks performed?** Look in the report's **Results** for entries from "Local Security Checks" (LSC) NVTs, or check the host's **Details/Information** panel — if the SSH credential worked, you'll see local-check results (e.g. installed package/version checks) rather than only network-based findings. If you instead see errors like "Authentication failure," authenticated checks did *not* succeed.
    - **Vulnerabilities**: scroll the Results list, sorted by severity — note the ones with the highest CVSS.
    - **Q2 answer**: find the result on **port 1883/tcp** (MQTT), click it to expand, and read the **Solution** field for the mitigation text.

---

## Part 3 — CVSS 4.0 scoring

1. Identify the CVE/scenario your worksheet references (check the unit's tutorial sheet or ask your tutor if it's not explicit in your copy).
2. Go to **https://www.first.org/cvss/calculator/4.0**.
3. Work through the metric groups, reading the vulnerability description carefully for each:
   - **Base metrics — Exploitability**: Attack Vector (AV), Attack Complexity (AC), Attack Requirements (AT), Privileges Required (PR), User Interaction (UI).
   - **Vulnerable System Impact**: Confidentiality (VC), Integrity (VI), Availability (VA).
   - **Subsequent System Impact**: Confidentiality (SC), Integrity (SI), Availability (SA).
4. The calculator updates the **Base Score** and severity rating (None/Low/Medium/High/Critical) live as you select options.
5. Record the score and the full **vector string** (e.g. `CVSS:4.0/AV:N/AC:L/...`) for your submission.

---

## Part 4 — Build the CPE for iOS 16.3

1. Go to **https://nvd.nist.gov/products/cpe/search** and search `iphone_os` to confirm Apple's iOS product name in the CPE dictionary.
2. Use the CPE 2.3 template:
   ```
   cpe:2.3:part:vendor:product:version:update:edition:language:sw_edition:target_sw:target_hw:other
   ```
3. Fill it in for iOS 16.3:
   ```
   cpe:2.3:o:apple:iphone_os:16.3:*:*:*:*:*:*:*
   ```
   (`o` = operating system part; everything after the version is wildcarded `*`.)

---

## Part 5 — Nikto scan of the Greenbone web portal

1. On the **Kali** terminal, run:
   ```
   nikto -host 192.168.56.2 -port 443
   ```
2. This can take several minutes — let it finish (or optionally save output as you go):
   ```
   nikto -host 192.168.56.2 -port 443 -output nikto_greenbone.txt
   ```
3. Once done, search the output for the archive file finding:
   ```
   grep -i archive nikto_greenbone.txt
   ```
4. **Q1 answer**: that line will reference an OSVDB ID and/or a CWE number next to the `/archive.tgz` finding — read off the CWE number directly from your output.

---

## Part 6 — Nmap scans of Greenbone

1. On the **Kali** terminal, run the aggressive scan:
   ```
   nmap -A 192.168.56.2
   ```
2. **Ports scanned**: by default, `nmap` without `-p` scans the **top 1000 TCP ports**. Confirm from your own output — add the number of ports shown in the results table to the number noted in the "Not shown: N closed/filtered ports" line; the total should be 1000 (unless the scan range was customised).
3. **TLS versions (Q1)**: since `-A` runs default NSE scripts, look for an `ssl-cert` / `ssl-enum-ciphers` block under port 443 in the output — it lists each TLS version detected (e.g. TLSv1.2, TLSv1.3). If it's not shown by default, run explicitly:
   ```
   nmap --script ssl-enum-ciphers -p 443 192.168.56.2
   ```
4. Now run the discovery script scan:
   ```
   nmap --script discovery 192.168.56.2
   ```
5. **NIC type**: look for a line containing `MAC Address:` — it includes the vendor name resolved from the MAC's OUI (commonly a VirtualBox-associated vendor like "PCS Systemtechnik GmbH" for VirtualBox virtual NICs, but read the exact string from your own output).

---

## Part 7 — ATT&CK → CAPEC → CWE → CVE → CPE chain

1. Go to **MITRE ATT&CK** and look up **T1499 – Endpoint Denial of Service**; note its related CAPEC entries.
2. Go to **capec.mitre.org**, view the **CAPEC-658 "ATT&CK Related Patterns" slice** mentioned in your worksheet, and find the DoS-related pattern tied to T1499: **CAPEC-227 – Sustained Client Engagement**.
3. Open the CAPEC-227 page and scroll to **Related Weaknesses** — it links to **CWE-400 – Uncontrolled Resource Consumption**.
4. Open the CWE-400 page on **cwe.mitre.org** and check its **Observed Examples** section, or go to **NVD** and search for a CVE affecting your device that fits this weakness. For an iOS 16.3 iPhone, a strong candidate is:
   - **CVE-2023-23514** — a denial-of-service issue where processing a maliciously crafted certificate could crash the device (fixed in iOS 16.3.1, meaning **iOS 16.3 itself is vulnerable**).
5. Confirm on Apple's security-content page (`support.apple.com`) that the fix for your candidate CVE lands in the version *after* 16.3, confirming 16.3 is affected.
6. Complete the chain's final CPE using the same format as Part 4:
   ```
   cpe:2.3:o:apple:iphone_os:16.3:*:*:*:*:*:*:*
   ```

---

### Tips
- Screenshot each Greenbone/Nikto/Nmap result screen as you go — most tutors want evidence, not just the final answer.
- If any scan hangs or errors, double check the VM is powered on and reachable: `ping 192.168.56.2` from Kali before troubleshooting further.
- Paste your actual scan output back into chat any time you want help reading a specific result.
