# Day 3 — Did You SIEM?

**Platform:** TryHackMe  
**Category:** Security Operations / SIEM  
**Difficulty:** Easy  
**Room:** Advent of Cyber 2025 — Day 3  
**Tools Used:** Splunk, SPL (Search Processing Language)

---

## Objective

Use Splunk to investigate the intrusion, trace the full attack chain, and identify the threat actor before Christmas is ruined.

Learning objectives for this room:

- Ingest and interpret custom log data in Splunk
- Create and apply custom field extractions
- Use Search Processing Language (SPL) to filter and refine search results
- Conduct a full investigation within Splunk to uncover key insights

---

## Environment Setup

This room requires a Splunk instance with pre-ingested log data, accessible via the TryHackMe AttackBox or VPN.

| Field | Value |
|---|---|
| SIEM Platform | Splunk |
| Index | `main` |
| Data Source 1 | `web_traffic` — HTTP requests/responses to/from the web server |
| Data Source 2 | `firewall_logs` — network traffic allowed or blocked |
| Web Server IP | `10.10.1.15` |

---

## Walkthrough

### Task 1 — Introduction

King Malhare of HopSec Island has sent his Bandit Bunnies to attack TBFC's systems and replace Christmas with "EAST-mas." A ransom message has appeared on the SOC dashboard. McSkidy is missing and it falls to us to investigate the compromise using Splunk.

---

### Task 2 — Log Analysis with Splunk

**Initial Exploration**

Opening Search & Reporting and querying the main index:

```spl
index=main
```

This reveals two pre-ingested datasets `web_traffic` and `firewall_logs`. Set the time range to **All Time** ensuring all events are captured.

**Visualising the Attack Timeline**

Charting event volume per day to identify when anomalous activity spiked:

```spl
index=main sourcetype=web_traffic | timechart span=1d count | sort by count | reverse
```

A traffic spike is visible on the day of the main attack

**Anomaly Detection**

Inspecting key fields within `web_traffic` to surface suspicious values:

- **user_agent** — Alongside legitimate browser agents (Mozilla, Chrome, Safari, Firefox) several non-standard agents are present indicating automated tooling
- **client_ip** — One IP address stands out in request volume
- **path** — Several URI paths point to known attack vectors including config file exposure and path traversal attempts

---

### Task 3 — Filtering and Isolating the Attacker

Excluding legitimate browser traffic to isolate suspicious requests:

```spl
index=main sourcetype=web_traffic
user_agent!=*Mozilla*
user_agent!=*Chrome*
user_agent!=*Safari*
user_agent!=*Firefox*
```

All remaining traffic traces back to a single attacker IP. Confirming the top offending IP using head and a reverse sort (-count):

```spl
sourcetype=web_traffic
user_agent!=*Mozilla*
user_agent!=*Chrome*
user_agent!=*Safari*
user_agent!=*Firefox*
| stats count by client_ip
| sort -count
| head 5
```

---

### Task 4 — Tracing the Attack Chain

With the attacker IP confirmed the full attack chain was reconstructed chronologically.

**Reconnaissance**

```spl
sourcetype=web_traffic client_ip="<ATTACKER_IP>"
AND path IN ("/.env", "/*phpinfo*", "/.git*")
| table _time, path, user_agent, status
```

Tools used: `curl`, `wget`. HTTP `401/403/404` responses confirm the attacker was probing for exposed configuration files and sensitive endpoints.

**Enumeration**

The attacker queried paths associated with path traversal and open redirect vulnerabilities, systematically mapping the application's attack surface.

**Exploitation**

Cross-referencing firewall logs with web traffic confirmed the payload delivery method and the precise window during which the server was compromised.

---

## Answer Summary
<details>
  <summary>Spoiler - click to reveal</summary>
  
| # | Question | Answer |
|---|---|---|
| 1 | What was the attacker's IP address? | 198.51.100.55 |
| 2 | On what day did the attack traffic spike occur? | 2025-10-12 |
| 3 | What is the count of Havij user_agent events found in the logs? | 993 |
| 4 | How many path traversal attempts to access sensitive files on the server were observed? | 658 |
| 5 | Examine the firewall logs. How many bytes were transferred to the C2 server IP from the compromised web server? | 126167 |

</details>

---

## Lessons Learned

**Tools introduced:**

| Tool | Purpose |
|---|---|
| Splunk | SIEM platform for log ingestion, search, visualisation, and incident investigation |
| SPL | Query language for searching, filtering, and aggregating data within Splunk |

**Key takeaways:**

- A SIEM aggregates logs from across an environment into a single platform, enabling fast correlation and incident investigation
- Filtering out known-good values (legitimate user agents, known IPs) is a powerful technique for surfacing anomalies quickly
- SPL commands like `timechart`, `stats count by`, `sort`, and `head` form the core toolkit for log-based threat hunting
- Attackers follow a predictable pattern — Reconnaissance → Enumeration → Exploitation — which can be reconstructed from logs after the fact
- Tightening the time range after initial data loading is a best practice in real-world SOC investigations

---

## Defensive Recommendations

**Technical controls:**

- Deploy a SIEM with alerting rules for spikes in request volume from a single IP
- Implement rate limiting and geo-blocking on internet-facing web servers
- Restrict access to sensitive paths (`.env`, `.git`, `phpinfo`) at the web server or WAF level, returning `404` rather than `403` to avoid confirming their existence
- Enable detailed access logging on all web-facing infrastructure and forward logs to a centralised SIEM in real time

**Process controls:**

- Establish baseline traffic profiles for web servers so anomalous spikes trigger automatic alerts
- Include log review as part of routine SOC triage — not just reactive incident response
- Define playbooks for common attack patterns (automated scanning, credential stuffing, path traversal) so analysts can respond consistently

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Active Scanning | T1595 | Attacker probed the web server for exposed configuration files and vulnerable endpoints |
| Search Victim-Owned Websites | T1594 | Enumeration of application paths and URI structure |
| Exploit Public-Facing Application | T1190 | Exploitation of a vulnerability in the internet-facing web application |
| Indicator Removal | T1070 | Attacker used non-standard user agents to blend in or avoid detection |

---

*Part of the [Advent of Cyber 2025](../README.md) series · [CTFs Repository](../../../README.md)*
