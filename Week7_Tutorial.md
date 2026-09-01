# Week 7 Tutorial – Law and Risk
**COIT11241 Cyber Security Technologies**

**By the end of this tutorial you will:**
- Explore cyber laws, and
- Assess the risk of a small information system for one important data set.

---

## Activity 1: Australian Privacy Principles (APPs) — 15 minutes

Break into small groups.

1. Pick your favourite organisation (e.g. your favourite fast-food chain).
2. Review its privacy policy. Can you detect any violations or dilutions of the APPs?
3. Be ready to report back to the class in 10 minutes.

**Recall question:** In this unit, the APPs were condensed into six cybersecurity rights — **CASFUD**. What are those six rights?

- **C** — ?
- **A** — ?
- **S** — ?
- **F** — ?
- **U** — ?
- **D** — ?

*(See the Week 7 lecture notes if you need a refresher.)*

---

## Activity 2: Cybersecurity Law Case Studies — 30 minutes

Break into groups of 4–5. Your tutor will assign one scenario per group. Take 15 minutes to prepare an explanation of the case study and how an ICT professional should manage it — some groups will present to the class.

### Scenario 1 — Law for Privacy
Look up the *Federal Register of Legislation* for the **Commonwealth Privacy Act 1988**, Part III (Information Privacy), Division 2 (APPs), Section 16 — *Personal, family or household affairs*.

- Question: Are you allowed to keep a record of your partner's passport number?
- Hint: search for "household affairs."

### Scenario 2 — Law for State Interception
Refer to the **Telecommunications Act 1997 (Cth)**, Volume 2, Part 15 — *Industry Assistance*.

**Situation:** You work for Vodafone. A low-level ASIO officer arrives at your office and orders you to remove the encryption Vodafone applied to a customer's voice messages, citing an imminent threat.

Discuss:
- What should you do?
- Is this a lawful request?
- Who can you talk to?
- What would you do if your boss says you'll be fired if you don't decrypt the messages?

Search terms that may help: *"Listed acts"*, *"Unauthorised disclosure of information"*, *"Authorised disclosures – general"*, *"Compliance with notices"*.

### Scenario 3 — Cybersecurity Act 2024
**Situation:** A cloud collaboration provider used by governments and critical service providers detects unauthorised access to a third-party integration marketplace account, exposing customer organisation names, support contact emails, and metadata about installed plugins. Fake emails impersonating the provider's security team instruct customers to "verify their credentials" via a malicious link. It isn't yet clear if this is a ransomware attack.

Discuss:
- Does this incident trigger obligations under Australia's **Cyber Security Act 2024**, even before all the facts are known?
- Should the provider tell customers to "ignore all emails until further notice"?

---

## Activity 3: Classify the Ethical Scenarios

For each real-world scenario below, identify which ethical-challenge category it best fits: **Resource allocation**, **Incident response**, **Vulnerability management & disclosure**, **Privacy/Monitoring**, or **Competing interests**.

| Scenario | Category |
|---|---|
| A tech company stores users' "private-mode" search data despite privacy expectations | ? |
| After a breach, experts recommend a vendor delay adding new cloud features until security improves | ? |
| A country reports a shortage of cybersecurity analysts | ? |
| Local councils have significant cybersecurity gaps, including out-of-date incident response plans | ? |
| Cybersecurity specialists are found moonlighting as hackers | ? |
| An analyst publicly discloses an unpatched vulnerability in a PAM product after weeks of no response from the vendor | ? |

---

## Activity 4: Prioritising Risk Scenarios

Discussion question: What factors of a risk should we weigh up when deciding whether to focus more attention on **stopping identity theft via credit-card skimming** versus **stopping thieves stealing our car**?

Recall the risk formula:

**Risk = Likelihood × Impact = P(Threat) × P(Vulnerability) × Cost to fix**

- Consider **likelihood** and **impact** of each incident to help rank priorities — you can compare risks qualitatively.
- Reminder: **threats** are threat actors (TAs) — e.g. storms, or "Evil Eve." **Vulnerabilities** are the weaknesses a TA exploits.

---

## Activity 5: Cybersecurity Risk Controls — Which Controls Reduce What?

For each control type, tick which of the four stages of the attack lifecycle it reduces or detects (Incidents / Threats / Vulnerabilities / Impacts):

| Control Type | Incidents | Threats | Vulnerabilities | Impacts |
|---|---|---|---|---|
| Deterrent | ☐ | ☐ | ☐ | ☐ |
| Preventative | ☐ | ☐ | ☐ | ☐ |
| Detective | ☐ | ☐ | ☐ | ☐ |
| Corrective | ☐ | ☐ | ☐ | ☐ |

### Classify these specific controls
For each control below, mark whether it is Deterrent, Preventative, Detective, Corrective, or Recovery:

| Control | Deterrent | Preventative | Detective | Corrective | Recovery |
|---|---|---|---|---|---|
| Autolocking mobile | ☐ | ☐ | ☐ | ☐ | ☐ |
| Remote mobile lock & wipe | ☐ | ☐ | ☐ | ☐ | ☐ |
| Change management process (e.g. supervisor must approve firewall changes) | ☐ | ☐ | ☐ | ☐ | ☐ |
| Motion-sensitive floodlights | ☐ | ☐ | ☐ | ☐ | ☐ |
| One-time passwords (OTP) | ☐ | ☐ | ☐ | ☐ | ☐ |
| Telecommunications (Interception & Access) Act 1979 | ☐ | ☐ | ☐ | ☐ | ☐ |
| Reduce digital footprint (e.g. review social media settings) | ☐ | ☐ | ☐ | ☐ | ☐ |
| Trend analysis (e.g. of failed login logs) | ☐ | ☐ | ☐ | ☐ | ☐ |
| Nightly "therapeutic" reboots of servers | ☐ | ☐ | ☐ | ☐ | ☐ |
| Encrypt drive | ☐ | ☐ | ☐ | ☐ | ☐ |
| Remove unsupported apps | ☐ | ☐ | ☐ | ☐ | ☐ |

---

## Activity 6: Risk Assessment of Your Mobile's Contacts Data

### Step 1 — Create an asset inventory for your phone
Identify all important data stored on, or transmitted to/from, your phone. To save time, treat your **Contacts data** as if it were your only important data, and work through the standard asset categories:

- Hardware
- Software
- Networking
- People
- Processes

*(An asset inventory would normally record details like MAC addresses, model, serial number, and version — you don't need to collect this data for this tutorial exercise.)*

### Step 2 — Allocate roles & responsibilities
Fill in a table like this for your phone's Contacts data:

| Role | Responsibilities | Allocated To |
|---|---|---|
| | | |
| | | |
| | | |

### Step 3 — Provide rationales for your risks
Copy the assets and threats you identified above into a risk table. For each risk, estimate **Likelihood** and **Impact** (e.g. Mod/High) and identify which asset the risk applies to, the threat to that asset, and what makes the asset vulnerable.

| Risk # | Asset | Threat to Asset | Asset Vulnerable To | Likelihood | Impact |
|---|---|---|---|---|---|
| 1 | | | | Mod | High |
| 2 | | | | | |
| 3 | | | | | |
| 4 | | | | | |
| 5 | | | | | |

By the end of this activity, you should have a clearer, evidence-based picture of the real risks to your phone's Contacts data.

---

## Reference

- Calder, A 2018, *NIST Cybersecurity Framework*, IT Governance, Ely.
