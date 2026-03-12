# 🚩 CTF Repository — Logan Smith

> Capture The Flag writeups, challenge documentation, and guided module walkthroughs. Every exercise is approached with a **SOC analyst mindset** — understanding attacker techniques to become a sharper defender. Writeups document methodology, tools used, detection opportunities, and MITRE ATT&CK mappings.

---

## 📋 Table of Contents

- [Repository Structure](#repository-structure)
- [TryHackMe](#tryhackme)
  - [Advent of Cyber 2025](#-advent-of-cyber-2025)
  - [Modules](#-modules)
- [Skills & Tools Index](#skills--tools-index)
- [MITRE ATT&CK Coverage](#mitre-attck-coverage)
- [Goals & Profile](#goals--profile)

<!--
Planned platforms — writeups will be added as labs are completed:
- [CyberDefenders](#cyberdefenders)
- [PicoCTF](#picoctf)
-->

---

## Repository Structure

```
CTFs/
├── README.md
├── TryHackMe/
│   ├── Advent-of-Cyber-2025/
│   │   ├── README.md
│   │   └── Day-XX-[Topic]/
│   │       └── README.md
│   └── Modules/
│       ├── Blue/
│       │   └── README.md
│       ├── SOC-Level-1/
│       │   └── README.md
│       ├── Cyber-Defense/
│       │   └── README.md
│       └── Jr-Penetration-Tester/
│           └── README.md
├── CyberDefenders/       <!-- planned -->
└── PicoCTF/              <!-- planned -->
```

---

## TryHackMe

### 🎄 Advent of Cyber 2025

**Directory:** [`TryHackMe/Advent-of-Cyber-2025/`](./TryHackMe/Advent-of-Cyber-2025/README.md)

A 24-day beginner-to-intermediate challenge series. Each day introduces a new security domain through a guided, hands-on scenario. Full progress tracker and per-day writeups live in the directory above.

| Domain | Topics Covered |
|---|---|
| 🔍 Threat Detection & SIEM | Log analysis, alert triage, SIEM querying |
| 🌐 Web Security | IDOR, XSS, authentication flaws |
| 🐚 Linux & CLI | Bash scripting, privilege escalation, file forensics |
| 🪟 Windows Security | Event logs, Active Directory basics, registry forensics |
| 🔒 Cryptography | Encoding, hashing, basic cipher challenges |
| 📡 Networking | Packet analysis, Wireshark, protocol identification |
| 🤖 AI & Automation | Prompt injection, AI-assisted attacks |
| 🧪 Malware Analysis | Reverse engineering basics, behavioral analysis |

---

### 📚 Modules

The modules below are selected because they build skills that appear most frequently in SOC Analyst and entry-level cybersecurity job descriptions. Each has its own directory with room breakdowns, detection notes, and writeups.

---

#### [Blue](./TryHackMe/Modules/Blue/README.md)

A foundational room walking through EternalBlue (MS17-010) — the vulnerability behind WannaCry — from both the offensive and defensive perspective. Covers exploitation, privilege escalation, credential dumping, and the log artifacts each technique generates.

**Skills:** Windows exploitation · LSASS credential dumping · Meterpreter detection · Event Log analysis · MITRE ATT&CK T1190, T1003, T1068

---

#### [SOC Level 1](./TryHackMe/Modules/SOC-Level-1/README.md)

TryHackMe's structured path built explicitly for Tier 1 SOC Analysts. Spans the full analyst workflow across seven modules:

- **Cyber Defence Frameworks** — Kill Chain, MITRE ATT&CK, Diamond Model, Pyramid of Pain
- **Cyber Threat Intelligence** — YARA rules, VirusTotal, MISP, OpenCTI, threat intel feeds
- **Network Traffic Analysis** — Wireshark, Zeek, Snort/Suricata, NetworkMiner, TShark, Brim
- **Endpoint Security Monitoring** — Core Windows processes, Sysmon, Windows Event Logs, Wazuh, Osquery
- **SIEM** — Splunk SPL, ELK/Kibana, alert triage, detection logic
- **Digital Forensics & Incident Response** — Volatility, Autopsy, KAPE, Redline, Velociraptor, TheHive
- **Phishing Analysis** — Email header analysis, attachment triage, SPF/DKIM/DMARC

**Skills:** SIEM querying · Wireshark & Zeek log analysis · Windows Event Log investigation · Sysmon · Threat intel (MISP/VirusTotal) · DFIR fundamentals · Phishing triage · MITRE ATT&CK fluency

---

#### [Cyber Defense](./TryHackMe/Modules/Cyber-Defense/README.md)

A hands-on defensive path with deeper tooling focus and an emphasis on Active Directory attack detection and memory forensics. Complements SOC Level 1 with additional lab time on the same tool set.

- **Threat & Vulnerability Management** — Nessus, OpenVAS, CVSS scoring, Zero Logon (CVE-2020-1472)
- **Security Operations & Monitoring** — Core Windows processes, Sysinternals, Splunk, ELK, Sysmon
- **Threat Emulation** — Active Directory attacks: Kerberoasting, AS-REP roasting, Pass-the-Hash, DCSync
- **Incident Response & Forensics** — Volatility memory forensics, Windows host investigation, disk/browser artifact analysis
- **Malware Analysis** — Static vs. dynamic analysis, string extraction, sandbox analysis (Any.run), behavioral review
- **Network Security & Traffic Analysis** — Wireshark, Snort, NetworkMiner, HTTP/DNS deep dives

**Skills:** Active Directory attack detection · Volatility memory forensics · Malware behavioral analysis · IDS rule writing · Kerberoasting/AS-REP detection (Event ID 4769/4768)

---

#### [Jr Penetration Tester](./TryHackMe/Modules/Jr-Penetration-Tester/README.md)

An offensive path completed with a defensive purpose — understanding how attacks are executed makes detection logic significantly stronger. Every technique is paired with the artifacts it leaves behind in logs.

- **Web Hacking** — SQLi, XSS, IDOR, SSRF, Command Injection, File Inclusion, Authentication Bypass, Burp Suite
- **Network Security** — Nmap (discovery, port scanning, NSE scripts), passive/active recon, protocol analysis
- **Vulnerability Research** — CVE system, CVSS, exploit identification
- **Metasploit** — Module types, payload selection, Meterpreter post-exploitation
- **Privilege Escalation** — Linux (SUID, sudo, cron, PATH) and Windows (service misconfigs, token impersonation, registry)

**Skills:** Web vulnerability identification · Nmap enumeration · Metasploit framework · Linux & Windows privilege escalation · Attacker TTP awareness · Defensive translation of offensive techniques

---

<!--
## CyberDefenders

Purpose-built blue team CTF platform. Labs simulate real SOC investigations — DFIR, threat hunting, malware analysis, and network forensics. All labs are browser-based with no local setup required. Content is MITRE ATT&CK aligned and updated weekly.

**Platform:** [cyberdefenders.org](https://cyberdefenders.org)  
**Directory:** [`CyberDefenders/`](./CyberDefenders/README.md)

Planned labs:
- CyberCop — Network Forensics
- PsExec Hunt — Threat Hunting
- PacketMaze — Network Forensics
- Boss of the SOC v1 — SIEM / Splunk
- Malware Traffic Analysis — Malware / PCAP
-->

<!--
## PicoCTF

Carnegie Mellon University's annual jeopardy-style competition. Free, beginner-to-intermediate difficulty spanning forensics, cryptography, web exploitation, and reverse engineering. Runs every spring. The picoGym practice arena remains open year-round.

**Platform:** [picoctf.org](https://picoctf.org)  
**Directory:** [`PicoCTF/`](./PicoCTF/README.md)

Planned categories: Forensics · Cryptography · Web Exploitation · General Skills
-->

---

## Skills & Tools Index

| Skill | Platforms Practiced | Application |
|---|---|---|
| SIEM querying (Splunk / ELK) | TryHackMe SOC L1, Cyber Defense | Alert triage, SPL searches, Kibana dashboards |
| Network packet analysis | TryHackMe SOC L1 / Cyber Defense, Advent of Cyber | Wireshark, Zeek conn.log, TShark, Brim |
| Windows Event Log investigation | TryHackMe Blue / SOC L1 / Cyber Defense | Event IDs 4624/4625/4688/4768/4769/4104 |
| Threat hunting (MITRE ATT&CK) | TryHackMe SOC L1 / Cyber Defense | TTP identification, IOC extraction, Navigator |
| Active Directory attack detection | TryHackMe Cyber Defense | Kerberoasting, AS-REP, Pass-the-Hash, DCSync |
| Memory forensics | TryHackMe SOC L1 / Cyber Defense | Volatility — processes, network, injections, hashes |
| Malware analysis basics | TryHackMe Cyber Defense, Advent of Cyber | Behavioral analysis, sandbox review, string extraction |
| Threat intelligence | TryHackMe SOC L1 | YARA rules, VirusTotal, MISP, OpenCTI |
| Phishing analysis | TryHackMe SOC L1 | Email headers, attachment triage, SPF/DKIM/DMARC |
| Web vulnerability awareness | TryHackMe Jr Pen Tester, Advent of Cyber | SQLi, XSS, IDOR, SSRF — alert identification |
| IDS rule writing | TryHackMe SOC L1 / Cyber Defense | Snort / Suricata signatures |
| Linux & Bash CLI | TryHackMe Cyber Defense, Advent of Cyber | Log parsing, forensic triage, privilege escalation |
| Offensive fundamentals | TryHackMe Jr Pen Tester | Nmap, Metasploit, Burp Suite — attacker TTP awareness |

---

## MITRE ATT&CK Coverage

Techniques encountered and analyzed across CTF exercises. Updated as challenges are completed.

| Tactic | Technique | ID | Encountered In |
|---|---|---|---|
| Reconnaissance | Network Service Discovery | T1046 | Advent of Cyber 2025 · Jr Pen Tester |
| Initial Access | Exploit Public-Facing Application | T1190 | Blue · Jr Pen Tester |
| Initial Access | Phishing | T1566 | SOC Level 1 |
| Execution | PowerShell | T1059.001 | Advent of Cyber 2025 · SOC Level 1 |
| Execution | Windows Command Shell | T1059.003 | Advent of Cyber 2025 · Blue |
| Execution | Exploitation for Client Execution | T1203 | Blue |
| Privilege Escalation | Exploitation for Privilege Escalation | T1068 | Blue · Jr Pen Tester |
| Credential Access | OS Credential Dumping: LSASS Memory | T1003.001 | Blue · Cyber Defense |
| Credential Access | Kerberoasting | T1558.003 | Cyber Defense |
| Credential Access | AS-REP Roasting | T1558.004 | Cyber Defense |
| Credential Access | Brute Force | T1110 | Advent of Cyber 2025 · SOC Level 1 |
| Lateral Movement | Pass the Hash | T1550.002 | Cyber Defense |
| Discovery | System Information Discovery | T1082 | Blue · SOC Level 1 |
| Command & Control | DNS | T1071.004 | SOC Level 1 · Advent of Cyber 2025 |

---

## Goals & Profile

Certifications, career goals, and learning roadmap are documented in the main GitHub profile README.

➡️ **[github.com/thelogansmith](https://github.com/thelogansmith)**

---

*All offensive techniques are performed exclusively within authorized, isolated lab environments. Writeups are added as challenges are completed.*
