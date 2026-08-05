**Main Topic:** Peneration_Testing_Foundations
**Sub Topic:** Penetration_Testing_Frameworks
**Date:** 15-06-2026
**Resources:**
**Related Notes:**
Tags: #status/done  

---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Peneration_Testing_Foundations](/Offensive_Security/Peneration_Testing_Foundations/0_Peneration_Testing_Foundations_MOC.md) > Penetration_Testing_Frameworks

---
# Penetration_Testing_Frameworks

---
## Cyber Kill Chain (Lockheed Martin) — 7 Steps

The Cyber Kill Chain models the stages of a targeted intrusion from the attacker's perspective. Understanding these steps helps both attackers plan campaigns and defenders identify where to interrupt them.

|Step|Phase|Description|
|---|---|---|
|1|**Reconnaissance**|Gathering information about the target (OSINT, scanning, social engineering research)|
|2|**Weaponization**|Creating the attack payload — pairing an exploit with a backdoor/malware|
|3|**Delivery**|Transmitting the weapon to the target (phishing email, malicious USB, watering hole)|
|4|**Exploitation**|Triggering the exploit to execute code on the target system|
|5|**Installation**|Installing a persistent backdoor or RAT to maintain access|
|6|**Command & Control (C2)**|Establishing a channel back to the attacker for remote control|
|7|**Actions on Objectives**|Achieving the goal: data exfiltration, ransomware, lateral movement, destruction|

> **Defensive insight:** Each stage is an opportunity to detect and break the chain. Defenders don't need to block every step — stopping any one step defeats the attack.

---

## Why Use a Framework?

Pentesting without a framework produces inconsistent results: missed attack surfaces, unreproducible findings, and reports that can't communicate risk clearly. Frameworks enforce discipline on what is otherwise a creative, chaotic process.

Two types exist:

- **Process frameworks** — define _how_ to conduct a pentest (phases, documentation, scope)
- **Knowledge/TTP frameworks** — define _what_ attacks exist and how adversaries operate

In practice, you layer them: a process framework drives the engagement; a knowledge framework ensures coverage.

---

## Framework Comparison

|Framework|Type|Best For|
|---|---|---|
|PTES|Process|General infrastructure/network pentests|
|OWASP Testing Guide|Hybrid|Web application pentests, API security|
|MITRE ATT&CK|Knowledge/TTP|Red teaming, threat modeling, detection engineering|
|OSSTMM|Process|Formal audits requiring measurable risk scores|
|NIST SP 800-115|Process|US government / compliance engagements|
|Cyber Kill Chain|Knowledge|Explaining attack stages, defensive analysis|
|TIBER-EU|Process|Financial sector red team exercises|

---

## Framework Breakdown

### PTES — Penetration Testing Execution Standard

The de facto standard for general pentests. Covers 7 phases: Pre-engagement → Intelligence Gathering → Threat Modeling → Vulnerability Analysis → Exploitation → Post-Exploitation → Reporting.

**Pros:** Comprehensive phases, widely understood, good structure for reports  
**Cons:** Not web-specific, no built-in scoring system, community-maintained (slow updates)  
**Choose when:** General infrastructure or network pentest with a deliverable report

---

### OWASP Testing Guide (OTG)

Deep, web-specific methodology with hundreds of test cases. Pairs directly with the OWASP Top 10. Each vulnerability category has specific techniques and tools.

**Pros:** Extremely detailed, web-native, free and actively updated  
**Cons:** Web only, verbose (400+ pages), no formal scoring  
**Choose when:** Web application pentest, API security assessment, bug bounty

---

### MITRE ATT&CK

Adversary TTP database organized by Tactics → Techniques → Sub-techniques. Not a process framework — a knowledge base. Every technique maps to real APT group behavior.

**Pros:** Industry standard language, maps to real threat actors, bridges offense and defense  
**Cons:** Not a methodology, no process guidance, overwhelming to use alone  
**Choose when:** Red teaming, threat modeling, blue team detection rule writing

---

### OSSTMM — Open Source Security Testing Methodology Manual

Highly formal framework with a Risk Assessment Values (RAV) scoring system. Covers physical, human, wireless, and digital security — not IT-only.

**Pros:** Formal scoring, covers non-IT domains, fully auditable  
**Cons:** Heavy bureaucracy, not practitioner-friendly, complex to apply  
**Choose when:** Formal audit engagement where the client requires a measurable risk score

---

### NIST SP 800-115

US government technical guide defining four phases: Planning → Discovery → Attack → Reporting. Compliance-oriented rather than technique-deep.

**Pros:** Trusted by government bodies, clear phases, strong documentation standards  
**Cons:** Published in 2008 (outdated), lacks technique depth, US-gov-centric  
**Choose when:** US federal or government compliance engagement

---

### Cyber Kill Chain

Lockheed Martin's 7-stage intrusion model (see top of note). Primarily used for defensive analysis and communicating attack progression to non-technical stakeholders.

**Pros:** Simple mental model, great for defenders, easy to communicate upward  
**Cons:** Linear model doesn't reflect modern APT behavior, not actionable alone  
**Choose when:** Explaining attack stages to management, defender mindset training, incident analysis

---

### TIBER-EU

Threat Intelligence-Based Ethical Red Teaming framework from the European Central Bank. Designed for nation-state-level threat simulation in the financial sector.

**Pros:** High-fidelity realism, accepted by financial regulators, thorough scoping  
**Cons:** Finance sector only, very expensive to execute, overkill for most engagements  
**Choose when:** Bank or financial institution red team with regulatory requirement

---

## How to Choose

|Scenario|Framework(s)|
|---|---|
|THM / CTF / general pentest|PTES (implicit structure)|
|Web app or API pentest|OWASP OTG|
|Red team / adversary simulation|MITRE ATT&CK + PTES|
|Formal audit with risk score|OSSTMM|
|US government compliance|NIST SP 800-115|
|Financial sector red team|TIBER-EU|
|Explaining attack flow to non-technical audience|Cyber Kill Chain|

**Practical combination:** PTES as the process scaffold → OWASP OTG for web scope → MITRE ATT&CK for TTP mapping and reporting. This covers 90% of real-world engagements.

---
## TryHackMe's Master Comparison Table

| Framework | Scope | Key Strength | Limitation | Best For |
| --- | --- | --- | --- | --- |
| OSSTMM | Multi-channel (5 channels) | Quantifiable metrics (RAVs) | Steep learning curve | Repeatable, auditable assessments |
| WSTG | Web applications | 90+ structured test cases | Can encourage checklist mentality | Web app penetration testing |
| SP 800-115 | Systems, networks, applications | Federal credibility; flexible technique categories | Guidance only, no enforcement | Government and enterprise assessments |
| PTES | End-to-end engagements | Practical, phase-driven workflow | Not recently updated | Standard network/application pentests |
| ISSAF | Networks, systems, applications | Clear nine-step adversarial progression | No longer maintained | Educational reference for attack methodology |
| ATT&CK | Adversary behavior catalog | Universal language for threat behavior | Not a testing methodology | Enriching findings from any framework |
| MSTG | Mobile applications (Android/iOS) | Platform-specific mobile test cases | Mobile-only scope | Mobile app security testing |
| Guidelines | Payment card environments | Regulatory mandate | Narrow applicability | Engagements involving cardholder data |
| CSA CCM | Cloud environments | Governance controls mapped to major standards | Not a penetration testing methodology | Cloud security posture assessments |
| CBEST | UK financial sector | Threat-intel-led, regulatory backing | UK financial sector only | UK financial institution engagements |
| WASC Threat Classification | Web applications | Historical taxonomy | Superseded by | Legacy reference only |

---
[Offensive_Security](/Offensive_Security/0_Offensive_Security_MOC.md) > [Peneration_Testing_Foundations](/Offensive_Security/Peneration_Testing_Foundations/0_Peneration_Testing_Foundations_MOC.md) > Penetration_Testing_Frameworks

---