# 🎄 Advent of Cyber 2025

**Platform:** TryHackMe  
**Format:** 25-day challenge (December 2025)  
**Certificate:** Earned upon completion of all 25 days  
**Difficulty:** Easy → Medium  

> A 25-day challenge spanning Linux, Windows, OSINT, malware analysis, log forensics, and more. This writeup series documents each day's methodology, tools used, and key takeaways — structured as a mini security research portfolio in its own right.

---

## 🗓️ Challenge Index

| Day | Title | Category |
|---|---|---|
| Day 01 | [Shells Bells](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2001/Linux%20CLI%20-%20Shells%20Bells.md) | Linux CLI/Log Analysis |
| Day 02 | [Merry Clickmas](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2002/Phishing%20-%20Merry%20Clickmas.md) | Phishing |
| Day 03 | [Did you SIEM?](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2003/Splunk%20Basics%20-%20Did%20you%20SIEM.md) | Splunk/Log Analysis |
| Day 04 | [old sAInt nick](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2004/AI%20in%20Security%20-%20old%20sAInt%20nick.md) | AI in Security |
| Day 05 | [Santa's Little IDOR](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2005/IDOR%20-%20Santa's%20Little%20IDOR.md) | IDOR/Web |
| Day 06 | [Egg-xecutable](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2006/Malware%20Analysis%20-%20Eggxecutable.md) | Malware Analysis |
| Day 07 | [Scan-ta Clause](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2007/Network%20Discovery%20-%20Scan-ta%20Clause.md) | Network Scanning |
| Day 08 | [Sched-yule conflict](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2008/Prompt%20Injection%20-%20Sched-yule%20conflict.md) | Prompt Injection |
| Day 09 | [A Cracking Christmas](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2009/Passwords%20-%20A%20Cracking%20Christmas.md) | Password Cracking |
| Day 10 | [Tinsel Triage](https://github.com/thelogansmith/CTFs/blob/main/TryHackMe/Advent%20of%20Cyber%202025/Day%2010/SOC%20Alert%20Triaging%20-%20Tinsel%20Triage.md) | SOC Alter Triaging |
| Day 11 | [Merry XSSMas]() | Cross-Site Scripting |
| Day 12 | [Phishmas Greetings]() | Phishing / Email Security |
| Day 13 | [YARA Mean One!]() | Malware Detection / YARA |
| Day 14 | [DoorDasher's Demise]() | Container Security |
| Day 15 | [Drone Alone]() | Web Forensics |
| Day 16 | [Registry Furensics]() | Windows Registry Forensics |
| Day 17 | [Hoperation Save McSkidy]() | CyberChef / Encoding |
| Day 18 | [The Egg Shell File]() | Code Obfuscation |
| Day 19 | [Claus for Concern]() | ICS / SCADA / Modbus |
| Day 20 | [Toy to the World]() | Race Conditions / Web |
| Day 21 | [Malhare.exe]() | Malware Analysis / Reverse Engineering |
| Day 22 | [Command & Carol]() | C2 / Threat Detection |
| Day 23 | [S3cret Santa]() | Cloud / AWS Security |
| Day 24 | [Hoperation Eggsploit]() | Web Exploitation / cURL |

---

## 📋 Writeup Template

Each day's writeup lives in its own subfolder: `Day-XX/README.md`

Use the structure below for every day:

```
## Day XX — [Room Title]
**Category:** [e.g., Log Analysis / OSINT / Web Exploitation]
**Tools Used:** [e.g., grep, Splunk, Wireshark]

### Objective
What is this challenge asking you to do?

### Reconnaissance / Setup
How did you approach the problem initially? What did you observe first?

### Findings
What did you discover? Enumerate the key observations step by step.

### Exploitation / Analysis
Walk through what you did to solve the challenge. Include:
- Commands run (with explanation)
- Screenshots where relevant
- Any dead ends and how you corrected course

### Answer Summary
| Question | Answer |
|---|---|
| Q1: ... | ... |
| Q2: ... | ... |

### Lessons Learned
- What did this teach you?
- How does it map to real-world security work?
- Any tools or techniques worth revisiting?
```

---

## 🧠 Skills Covered

Advent of Cyber 2025 spans a wide range of domains. As writeups are completed, this section will be updated to reflect the actual topics covered each year. Typical AoC coverage includes:

| Domain | Examples |
|---|---|
| **Linux Fundamentals** | File permissions, bash, SUID, cronjobs |
| **Windows Forensics** | Event logs, registry, prefetch analysis |
| **Log Analysis** | Splunk, grep, log parsing |
| **OSINT** | Maltego, OSINT Framework, passive recon |
| **Malware Analysis** | Static analysis, strings, YARA rules |
| **Web Exploitation** | SQLi, XSS, directory traversal |
| **Network Analysis** | Wireshark, pcap analysis, traffic anomalies |
| **Threat Intelligence** | IOCs, MITRE ATT&CK mapping |
| **Cryptography** | Encoding, ciphers, hash cracking |
| **DFIR** | Volatility, Autopsy, incident timelines |

---

## 🏆 Certificate

Upon completing all 25 days, TryHackMe awards a certificate of completion.

`[Certificate image or link goes here once earned]`

---

## 📂 Folder Structure

```
Advent-of-Cyber-2025/
├── README.md          ← This file
├── (room-name)/
│   └── writeup.md
├── (room-name)/
│   └── writeup.md
├── ...
└── (room-name)/
    └── writeup.md
```

---

## 🔗 Resources

- [TryHackMe — Advent of Cyber 2025](https://tryhackme.com/module/aoc-2025-kra8blxfpajw0vn6)


---

*Part of the [CTFs](../../README.md) repository · TryHackMe track*
