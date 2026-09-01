# Week 7 – Laws, Ethics and Risks
**COIT11241 Cyber Security Technologies**

---

## 1. Cyber Laws

Key Australian cyber laws to know:

- **Australian Privacy Principles (APPs)** — condensed in this unit into six rights, **CASFUD**:
  - **C**onsent
  - **A**nonymity
  - **S**ecurity
  - **F**orgotten (right to be forgotten)
  - **U**pdate
  - **D**isclosure (safe disclosure)
- **Cyber Security Act 2024** — e.g. mandatory reporting of ransomware payments.
- **Underinvestment in cybersecurity is unethical and potentially negligent.** Ask yourself: are you investing enough time and money in your family's CIA (Confidentiality, Integrity, Availability)?

### Cyber law obligations fall into two broad categories

**Identify & Protect (prepare)**
- Data storage systems may count as *critical infrastructure* (like energy) → risk-management obligations (**SOCI Act 2018**, updated 2025).
- **APPs** — e.g. *My Health Records* must be stored in Australia.
- IoT / smart devices need to be secure (**Cyber Security Act 2024**).

**Detect & Respond**
- Large organisations (e.g. banks, private health) must be able to detect and respond to incidents.
- **Report** obligations:
  - Ransomware payments — businesses with $3M+ turnover (**Cyber Security Act 2024**).
  - **Notifiable Data Breaches** (**Privacy Act 1988**, updated 2025) — government, health, and large businesses must report breaches likely to cause serious harm.

---

## 2. Ethical Cybersecurity

### At home
- **Security vs privacy** — informed consent matters:
  - Are you monitoring family/friends (internet activity, cameras) with their knowledge?
  - Are cloud-connected devices monitoring you or giving others remote access?
- **Responsibility for securing devices** — duty of care:
  - Infected IoT cameras can be hijacked for DDoS attacks.
  - Components like routers eventually stop receiving security updates.
  - Never penetration test without permission.

### In organisations
Common ethical challenges:

| Challenge | Example |
|---|---|
| Resource allocation | Balancing security spend vs new features; patching vs shipping |
| Vulnerability management & disclosure | Temptation to falsely report critical vulns as non-critical to protect reputation |
| Privacy | Monitoring of staff or student devices |
| Competing interests | A supervisor asking for access they shouldn't have |
| Incident response | How honestly and quickly incidents are disclosed |

---

## 3. Cybersecurity as the Largest Organisational Risk

- **Risk = Likelihood of incident × Cost to fix**
- Survey of 2,000+ risk experts (Allianz Risk Barometer 2026) ranked **cyber incidents** as the top organisational risk — ahead of supply chain disruption, natural catastrophes, fire, law changes, climate, and new tech (AI).
- FBI data: cybercrime losses in 2024 were **US$16bn**, ~60% from ransomware.
- 43% of experts say **high investment** is needed; only 1% say no investment is required.
- Useful household analogy: how much time/money do you spend reducing *fire* risk (smoke alarms) vs *cyber* risk (offline backups)?

---

## 4. NIST Cybersecurity Framework (CSF)

Helps answer: **"Where do I start?"**

| Function | Purpose | Example |
|---|---|---|
| **Identify** | Understand risks to assets | Losing photos to a hard-drive failure |
| **Protect** | Harden your setup | Install a firewall |
| **Detect** | Identify an incident | Intrusion Detection System (IDS) |
| **Respond** | React to an incident | Turn off the router, inform family |
| **Recover** | Restore capabilities | Restore from backup |

*(NIST 2021)*

---

## 5. Lifecycle of Attacks

**Threats → Vulnerabilities → Incidents → Impacts**

Worked example ("Evil Eve"):

| Stage | Example |
|---|---|
| Threat | Evil Eve |
| Vulnerability | Alice's misplaced trust |
| Incident | Alice opens a phishing email |
| Impact | Alice's files are encrypted |

---

## 6. Risk Controls

Controls map onto the attack lifecycle:

| Control Type | Reduces/Detects | Example |
|---|---|---|
| **Deterrents** | Threats | Login warnings, e.g. "Your actions are logged" |
| **Preventatives** | Vulnerabilities | Access controls (mobile passwords), bars & guard dogs |
| **Detectives** | Incidents | Security cameras, motion detectors |
| **Correctives** | Impacts | Fire suppressants |

*(Chp 4, Kohnke, Shoemaker & Sigler)*

### More risk control categories

| Category | Example |
|---|---|
| **Managerial** | Risk assessments — determine threats, vulnerabilities & controls |
| **Operational** | Awareness training (challenge visitors without badges), change management, contingency planning |
| **Compensating** | Password login as backup for smart card |
| **Recovery** | Backups tested weekly; backups physically secured & encrypted |

---

## 7. NIST "Identify" Function in Detail

Five steps:
1. **Identify your critical assets**
2. **Document information flows** — where is data manipulated, transferred, stored?
3. **Maintain an asset inventory**
4. **Establish policies**, including roles & responsibilities
5. **Identify threats, vulnerabilities & risks** to assets

Assets are considered across the **CIA** triad (Confidentiality, Integrity, Availability) and across six info-system components:

- **Data** — design a classification scheme (e.g. Bank account = CIA Confidential; Contact list = IA Always accessible)
- **Hardware**
- **Software**
- **Network**
- **Processes**
- **People**

> Refer to CIS Control 1 (Inventory of Enterprise Assets), CIS Control 2 (Inventory of Software Assets), and CIS Control 3 (Data Protection).

### CIS Controls v8 — relevant processes

| Safeguard | Process |
|---|---|
| 3.1 | Data management process |
| 4.1 | Secure configuration process |
| 6.2 | Revoke access process |
| 7.1 | Vulnerability management process |
| 8.1 | Audit log management process |
| 16.1 | Secure app development process |
| 17.2 | Incident response process |

### Roles & responsibilities (enterprise)

| Role | Responsibility |
|---|---|
| Chief Information Officer (CIO) | Develop & enact IS strategy |
| Chief Security Officer (CSO) / CISO | All physical & information resources; sets security policy & procedures |
| Security technicians | e.g. configure firewalls & IDS |
| Data owners | e.g. manager who specifies who can access their data set |
| Data custodians | e.g. ICT department who implements permissions |

*(Whitman & Mattford 2019)*

**At home**, similar roles exist informally — e.g. a "Data Custodian" (kids/grandparents) vs the "Chief Info Security Officer" (you) — covering tasks like automated updates, strong/unique passwords & MFA, device locks, backups, drive encryption, ongoing training, and secure disposal of old devices.

---

## 8. Risk — Formal Review

**Risk = P(Threat) × P(Vulnerability) × Impact = Likelihood × Value**

- **P(Threat)** — is the attacker (e.g. Eve) motivated enough to attack us?
- **P(Vulnerability)** — is our system likely to be misconfigured in a way that's exploitable?
- **Impact** — what would a successful attack cost us?

> Rule of thumb: avoid spending $1,100 to prevent a $1,000 risk — controls should cost less than the risk they mitigate.

---

## 9. Information Security Threats — Reference Table

| Threat | Description / Examples |
|---|---|
| People errors | Social engineering, phishing, server misconfigurations |
| Software attacks | Backdoor, DoS, interceptions (MITM, sniffing, DNS poisoning, session hijacking, spoofing), malware |
| Info extortion | Ransomware, blackmail, information disclosure |
| Espionage & trespass | Unauthorised access, e.g. via password attacks |
| Theft | Illegal confiscation of assets |
| Technological obsolescence | Outdated tech |
| Forces of nature | Cyclone, fire, flood, lightning |
| Technical hardware failures | e.g. leaky capacitors |
| Technical software failures | Bugs, code performance issues, loopholes |
| Changing quality of services | Blackout, brownout, spike, NBN outage |
| Sabotage & vandalism | Destruction of assets |
| Intellectual property compromises | Piracy, copyright infringement |

*(Table 1.1, Whitman & Mattford 2019)*

---

## References

- Allianz 2026, *Risk Barometer*, viewed April 2026, www.agcs.allianz.com
- Garbis, J & Chapman, JW 2021, *Zero Trust Security: An Enterprise Guide*, Apress, Berkeley, CA.
- Kohnke, A, Shoemaker, D & Sigler, E 2016, *The Complete Guide to Cybersecurity Risks and Controls*, Auerbach Publications.
- Morgan, A & Voce, A 2025, *The Cost of Espionage*, Australian Institute of Criminology.
- NIST 2021, *Getting Started with the NIST Cybersecurity Framework*, viewed 17 January 2022.
- Whitman, M & Mattford, H 2019, *Management of Information Security*, 6th edn.
