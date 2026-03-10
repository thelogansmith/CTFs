# Day 02 — Phishing: Merry Clickmas

**Platform:** TryHackMe — Advent of Cyber 2025  
**Category:** Phishing / SEToolkit  
**Difficulty:** Easy  
**Room:** [phishing-aoc2025](https://tryhackme.com/room/phishing-aoc2025-h2tkye9fzU)  
**Tools Used:** `setoolkit` (Social-Engineer Toolkit), `server.py`, Firefox, SMTP

---

## Objective

The goal is to determine whether security awareness training has been effective, and to harvest credentials via a fake login portal delivered by a convincing spear-phishing email.

Learning objectives for this room:

- Understand what social engineering is
- Learn the types of phishing
- Explore how red teams create fake login pages
- Use the Social-Engineer Toolkit to send a phishing email

---

## Environment Setup

This room requires both the **Target VM** and the **AttackBox** to be running simultaneously.

| Field | Value |
|---|---|
| **AttackBox** | TryHackMe in-browser Ubuntu VM |
| **Target mail server** | `10.64.159.113` (port `25` SMTP, port `80` webmail) |
| **Phishing server** | `10.64.89.86:8000` |
| **Working directory** | `~/Rooms/AoC2025/Day02/` |

---

## Walkthrough

### Task 1 — Host the Fake Login Page

Before sending any email, the red team needs a convincing trap. The objective is credential theft, so the trap is a fake TBFC portal login page that captures whatever username and password the victim submits.

```bash
cd ~/Rooms/AoC2025/Day02
./server.py
```

**Output:**
```
Starting server on http://0.0.0.0:8000
```

The `0.0.0.0` binding means the server listens on all network interfaces. It's reachable from both `localhost` and the AttackBox's externally routable IP, 10.64.89.86. Port `8000` is the chosen delivery port.

To verify the page looks convincing before deploying:

```bash
# In Firefox on the AttackBox:
http://127.0.0.1:8000
# or
http://ATTACKER_IP:8000
```

The page renders as a realistic TBFC portal login form. This is what the victim will see when they click the phishing link — it's designed to be indistinguishable from the legitimate portal at a glance.

---

### Task 2 — Craft and Send the Phishing Email with SET

Now that the spoofed login page is live, I will craft and send a phishing email with SET.

**Launch SET:**

```bash
setoolkit
```

SET presents a numbered main menu. Navigate as follows:

**Step 1 — Select attack category:**
```
set> 1
# Social-Engineering Attacks
```

**Step 2 — Select attack type:**
```
set> 5
# Mass Mailer Attack
```

**Step 3 — Select targeting mode:**
```
set:mailer> 1
# E-Mail Attack Single Email Address
```

**Step 4 — Configure email parameters:**

SET will prompt for each field. Fill in as follows:

| Prompt | Value | Reasoning |
|---|---|---|
| Send email to | `factory@wareville.thm` | Target user — toy factory staff |
| Use your own server or open relay? | `2` | Deliver via the target's own SMTP server |
| From address | `updates@flyingdeer.thm` | Impersonate Flying Deer, a trusted supplier |
| From name | `Flying Deer` | Display name the target will see in their inbox |
| Username for open relay | *(blank)* | No authentication required on this relay |
| Password for open relay | *(blank)* | No authentication required |
| SMTP server | `10.66.174.120` | The target's mail server IP |
| Port | `25` | Standard unauthenticated SMTP port |
| Flag the message high priority | `no` | High-priority flags can raise suspicion |
| Attach a file? | `n` | Credential harvesting via link — no attachment needed |
| Inline file? | `n` | Not needed |

**Step 5 — Write the email subject and body:**

```
Subject: Shipping Schedule Changes

Body:
To Wareville Factory,

We have to change the shipping schedule. See here: http://10.64.89.86:8000
We hope this doesn't disrupt ongoing business too much, thank you for understanding.

Regards,
Flying Deer
```

Type `END` on a new line when finished to send.

---

### Task 3 — Capture the Credentials

After SET sends the email, return to the terminal running `server.py` and wait. The simulated victim checks their email, sees a message from a trusted shipping partner about an urgent scheduling change, and clicks the link.

When they submit credentials on the fake login page, the server captures them and prints them to the terminal:

```
[CREDENTIAL CAPTURED]
Username: factory
Password: unranked-wisdom-anthem
```

**Q: What is the password used to access the TBFC portal?**  
**A: `unranked-wisdom-anthem`**

---

### Task 4 — Password Reuse: Pivot to the Email Portal

Navigate to the target's webmail portal in Firefox:

```
http://10.64.159.113
```

Log in with the harvested credentials:

- **Username:** `factory`
- **Password:** `unranked-wisdom-anthem`

Access is granted — the same password works on the email system. Browse the `factory` user's inbox for emails containing operational data about toy delivery schedules.

One email in the inbox reveals the total number of toys expected for delivery.

**Q: What is the total number of toys expected for delivery?**  
**A: `1984000`**

> **Real-world relevance:** Password reuse is one of the most reliably exploited weaknesses in post-phishing lateral movement. Once an attacker has a single credential, it's routine to test it against every other accessible service — webmail, VPN, HR portals, cloud platforms. This technique is called **credential stuffing** when performed at scale against leaked databases. The defense is simple in principle but hard in practice: a password manager enforcing unique credentials per service.

---

## Why This Attack Worked

Dissecting the phishing campaign reveals how each element addressed a potential detection point:

| Attack Element | Why It Worked |
|---|---|
| Sender: `updates@flyingdeer.thm` | Flying Deer is a real supplier — the domain and relationship are believable |
| Subject: "Shipping Schedule Changes" | Operationally plausible; staff regularly coordinates with Flying Deer |
| No attachment | No suspicious file to trigger AV or email filters |
| Link to port 8000 | Could pass a careless URL check if the IP looks internal |
| No urgency language | Avoided obvious red flags in the S.T.O.P. framework |

**Red flags a careful employee should have caught:**

- The link uses a bare IP address (`http://10.64.89.86:8000`) rather than a company domain
- Port `8000` is non-standard — legitimate portals use `443` (HTTPS)
- No prior communication from Flying Deer about schedule changes
- No digital signature or expected email thread context
- The sender domain (`flyingdeer.thm`) could be checked against known Flying Deer correspondence

---

## Answer Summary

| # | Question | Answer |
|---|---|---|
| 1 | What is the password used to access the TBFC portal? | `unranked-wisdom-anthem` |
| 2 | What is the total number of toys expected for delivery? | `1984000` |

---

## Lessons Learned

**Tools introduced:**

| Tool | Purpose |
|---|---|
| `setoolkit` | Social-Engineer Toolkit — phishing email delivery and credential harvesting |
| `server.py` | Custom Python HTTP server hosting the fake login page |

**Key takeaways:**

- Social engineering attacks the human layer, not the technical one. Even well-patched, hardened infrastructure is vulnerable if employees aren't trained.
- Spear phishing succeeds through **context** — an email that fits what the target expects is far more effective than a generic blast.
- Open SMTP relays with no authentication are a persistent internal network risk. Organizations should audit port 25 accessibility and enforce SMTP authentication.
- Password reuse turns a single phishing success into a cascade of compromises. Every credential harvested should be tested across every accessible service.
- MFA is the single most effective technical control against credential harvesting. Even a perfectly executed phishing attack is neutralized if the stolen password alone isn't enough to log in.

---

## Defensive Recommendations

**Technical controls:**

- **Multi-Factor Authentication (MFA)** on all user-facing portals — renders harvested passwords useless without the second factor
- **SPF, DKIM, and DMARC** records on all company domains — makes spoofed sender addresses either fail delivery or be flagged
- **Email gateway filtering** — scan links and attachments before delivery; sandbox suspicious URLs
- **Password manager enforcement** — prevents password reuse across systems

**Process controls:**

- **Regular phishing simulations** — employees who fail receive immediate training, not punishment
- **Clear reporting channel** — one-click "report phishing" button in the email client so suspicious messages get actioned rather than ignored
- **Vendor communication protocols** — establish expected channels for supply chain partners; anything outside those channels warrants verification

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Phishing: Spearphishing Link | T1566.002 | Targeted email with link to credential harvester |
| Acquire Infrastructure: Web Services | T1583.006 | Hosting fake login page on attacker-controlled server |
| Credentials from Web Browsers / Input Capture | T1056.003 | Fake form capturing submitted credentials |
| Valid Accounts | T1078 | Using harvested credentials to access real systems |
| Credential Stuffing | T1110.004 | Testing `factory` password on the webmail portal |

---

*Part of the [Advent of Cyber 2025](../README.md) series · [CTFs Repository](../../../README.md)*
