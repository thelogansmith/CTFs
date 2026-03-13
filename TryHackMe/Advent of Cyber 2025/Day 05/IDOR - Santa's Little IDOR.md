# Day 05 — IDOR: Santa's Little IDOR

**Platform:** TryHackMe  
**Category:** Web Security / Access Control  
**Difficulty:** Medium  
**Room:** [IDOR — Santa's Little IDOR](https://tryhackme.com/room/idor-aoc2025-zl6MywQid9)  
**Tools Used:** Burp Suite, Browser DevTools

---

## Objective

Find and understand every IDOR flaw in TryPresentMe before another attacker does.

Learning objectives for this room:

- Understand the concept of authentication and authorization
- Learn how to spot potential opportunities for Insecure Direct Object References (IDORs)
- Exploit IDOR to perform horizontal privilege escalation
- Learn how to turn IDOR into SDOR (Secure Direct Object Reference)

---

## Environment Setup

This room requires the TryHackMe AttackBox or OpenVPN connection to access the lab machine running the TryPresentMe web application.

| Field | Value |
|---|---|
| Target URL | `http://10.67.128.187` |
| AttackBox | 'http://10.67.76.197' |
| Proxy | Burp Suite (127.0.0.1:8080) |
| Test Account | `niels` (provided in room) |

---

## Walkthrough

### Task 1 — Introduction & Scenario

The elves of Wareville are on high alert following McSkidy's disappearance. Parents contacting TBFC support report two issues: vouchers on TryPresentMe are failing to activate, and they're receiving phishing emails containing non-public personal information.

Staff discovered the culprit account — Sir Carrotbane — loaded with vouchers it shouldn't have. The account has been deleted and the vouchers reclaimed, but the root vulnerability remains. Our mission is to audit the TryPresentMe application and map every broken access control.

---

### Task 2 — IDOR Theory

**Authentication vs. Authorization**

These two concepts are often confused but are distinct:

- **Authentication** — verifying *who you are* (e.g., username + password login)
- **Authorization** — verifying *what you're allowed to do* (e.g., can this user view account #15?)

Authorization cannot happen without authentication. If the application doesn't know who is asking, it cannot enforce any permissions.

**What is IDOR?**

IDOR (Insecure Direct Object Reference) is an access control vulnerability. Web applications use references — IDs, filenames, package numbers — to identify objects in a database. When the application returns that object without verifying whether the requesting user is *authorized* to access it, IDOR exists.

Classic example: a parcel tracking endpoint at `/track?packageID=1001`. A user changes the value to `1002` and sees someone else's delivery. The application used the ID directly, without checking ownership.

**Privilege Escalation Types**

| Type | Description | Example |
|---|---|---|
| Horizontal | Access data belonging to a peer-level user | Viewing another parent's child list |
| Vertical | Gain features restricted to higher-privilege roles | Regular user accessing admin panel |

Most IDOR vulnerabilities produce **horizontal** privilege escalation.

---

### Task 3 — Exploiting the Plain IDOR (view_accounts)

After logging in as the test account `niels`, the application stores the current user's `user_id` in **localStorage**. Inspecting this via the browser's DevTools reveals the value directly.

**Steps:**

1. Open the browser DevTools → Application → Local Storage
2. Note the current `user_id` value
3. Modify the `user_id` to a different integer (e.g., increment from 10 → 11)
4. Refresh the account view page (`/view_accountinfo`)
5. Observe that the application returns another user's data without any authorization check

By iterating through user IDs (or using Burp Suite Intruder in Sniper mode against the `user_id` parameter), we can enumerate all parent accounts. The parent with the most children will produce the largest HTTP response body — identifiable in Intruder by the **Content-Length** column.

> **Finding:** The parent with 10 children is found at the user_id with the largest response. (Note: this value resets on machine restart — always verify dynamically.)

---

### Task 4 — Bonus: Base64 and MD5 Encoded IDOR (Child Endpoint)

IDOR doesn't only hide behind plain integers. The child detail endpoints use encoded IDs:

- **Base64 endpoint:** `/api/child/b64/<base64_encoded_id>` — e.g., `Mg==` decodes to `2`
- **MD5 endpoint:** `/api/child/md5/<md5_hash_of_id>` — e.g., the MD5 of `2` is `c81e728d9d4c2f636f067f89cc14862c`

**Exploitation Steps (Base64):**

1. Log in and click the eye icon next to any child profile to trigger the request
2. Intercept it in Burp Suite → send to **Intruder**
3. Set attack type to **Sniper**, highlight the Base64 value as the payload position
4. Set payload type to **Numbers** (1–100, step 1)
5. Under **Payload Processing**, add rule: `Encode → Base64-encode`
6. Launch the attack and inspect responses for the target date of birth (`2019-04-17`)

The same approach works for the MD5 endpoint — substitute the encoding rule for `Hash → MD5`.

> **Key Lesson:** Encoding and hashing are **not** security controls. Base64 is trivially reversible. MD5 of sequential integers is easily precomputed. Without server-side authorization checks, obfuscation just adds a small speed bump.

---

### Task 5 — Bonus: Predictable UUIDv1 Voucher Brute Force

The `/parents/vouchers/claim` endpoint accepts a voucher code in UUID format. The challenge: find the voucher valid on **20 November 2025**, knowing it was generated exactly on the minute between **20:00–24:00 UTC** (240 possible timestamps).

**Why is this exploitable?**

UUID version 1 embeds a **timestamp** in its structure (100-nanosecond intervals since October 15, 1582). When the generation time is known (or narrowed to a small window), all possible UUIDs can be enumerated.

**Steps:**

1. Capture a POST request to `/parents/vouchers/claim` in Burp Suite → send to **Intruder**
2. Set the voucher code field as the payload position
3. Write a Python script to generate UUIDv1 values for every minute in the target window, using the fixed UUID components provided in the challenge
4. Load the generated list as a custom payload in Intruder
5. Run the attack and look for a `200 OK` or different response length indicating a valid claim

```python
import uuid
import datetime

# Generate UUIDv1 payloads for Nov 20, 2025, 20:00–24:00 UTC
start = datetime.datetime(2025, 11, 20, 20, 0, 0, tzinfo=datetime.timezone.utc)
for i in range(240):
    target_time = start + datetime.timedelta(minutes=i)
    # UUID v1 timestamp: 100-ns intervals since 1582-10-15
    epoch = datetime.datetime(1582, 10, 15, tzinfo=datetime.timezone.utc)
    timestamp = int((target_time - epoch).total_seconds() * 1e7)
    time_low = timestamp & 0xFFFFFFFF
    time_mid = (timestamp >> 32) & 0xFFFF
    time_hi  = (timestamp >> 48) & 0x0FFF | 0x1000
    # Use fixed node/clock_seq from the challenge
    print(f"{time_low:08x}-{time_mid:04x}-{time_hi:04x}-8c4f-83c222be0c4f")
```

> **Key Lesson:** UUIDv1 is **not a secret**. If an attacker knows (or can guess) the generation time window, they can reconstruct every possible value. Use UUIDv4 (random) for tokens, and always enforce server-side expiry and ownership checks regardless of ID format.

---

## Answer Summary

| # | Question | Answer |
|---|---|---|
| 1 | What does IDOR stand for? | Insecure Direct Object Reference |
| 2 | What type of privilege escalation are most IDOR cases? | Horizontal |
| 3 | What is the user_id of the parent with 10 children? | 15 *(dynamic — verify in lab)* |
| 4 | (Bonus) What is the id_number of the child born on 2019-04-17? | *(Use Burp Intruder — hint available in room)* |
| 5 | (Bonus) What is the voucher valid on 20 November 2025? | `22643e00-c655-11f0-ac99-026ccdf7d769` *(hint)* |

---

## Lessons Learned

**Tools introduced:**

| Tool | Purpose |
|---|---|
| Browser DevTools (Application → LocalStorage) | Inspect and modify client-side stored values |
| Python (uuid / datetime) | Generate predictable UUIDv1 payloads for brute force |

**Key takeaways:**

- IDOR is fundamentally an **authorization failure** — the server trusts the client-supplied ID without verifying ownership
- Encoding (Base64) and hashing (MD5) of object IDs are **not security controls** — they must never substitute for server-side checks
- UUIDv1 tokens are **timestamp-derived and predictable** if the generation window is known; prefer UUIDv4 for security-sensitive identifiers
- Authorization must happen **on every request**, not just at login — a valid session token only proves identity, not entitlement
- The largest HTTP response body in an Intruder scan is a reliable signal when enumerating data by record size (most children → biggest payload)

---

## Defensive Recommendations

**Technical controls:**

- Enforce **server-side authorization checks** on every object access — verify that the authenticated user owns or has explicit permission to view the requested resource
- Never rely on client-side values (localStorage, cookies, URL parameters) as the sole source of identity for access decisions
- Replace sequential integer IDs in public-facing URLs with **UUIDv4** (random) to raise the enumeration cost — but pair this with authorization checks, not instead of them
- Use **UUIDv4** (not v1) for any security-sensitive tokens such as voucher codes or invite links
- Implement **rate limiting** on enumeration-prone endpoints to slow automated fuzzing
- Log and alert on **access denied** events — repeated failures across multiple IDs are a strong IDOR exploitation signal

**Process controls:**

- Include **authorization bypass test cases** in QA checklists — for every endpoint returning user-specific data, verify that a different authenticated user cannot retrieve it by ID manipulation
- Conduct **IDOR-focused code reviews** at the API layer, specifically looking for any endpoint that accepts a user-controllable identifier without an ownership check
- Add **Burp Suite / OWASP ZAP** scans to the CI/CD pipeline to catch new IDOR regressions before deployment

---

## MITRE ATT&CK Mapping

| Technique | ID | Description |
|---|---|---|
| Exploit Public-Facing Application | T1190 | Exploiting IDOR flaws in the TryPresentMe web application |
| Valid Accounts | T1078 | Leveraging authenticated sessions to access other users' data |
| Data from Information Repositories | T1213 | Enumerating parent/child account data via parameter manipulation |
| Brute Force | T1110 | Iterating UUIDs and encoded IDs to discover valid vouchers and child records |

---

*Part of the [Advent of Cyber 2025](../README.md) series · [CTFs Repository](../../../README.md)*
