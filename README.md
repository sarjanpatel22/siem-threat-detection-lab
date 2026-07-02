# 🛡️ SIEM + Threat Detection Lab

![Wazuh](https://img.shields.io/badge/Wazuh-4.7.5-3B82F6?style=flat-square)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-2026.2-557C94?style=flat-square)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-5_Techniques-red?style=flat-square)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen?style=flat-square)

An end-to-end **blue-team** lab: a **Wazuh 4.7.5** SIEM detecting **7 simulated attacks** — from port
scanning and SSH brute force to a real CVE exploit (Slowloris DoS) — with every event logged,
alerted, and mapped to **MITRE ATT&CK** in real time. Built from scratch on two virtual machines.

📄 **Full report with screenshots:** [SIEM_Threat_Detection_Lab_Report.pdf](./SIEM_Threat_Detection_Lab_Report.pdf)

---

## 🧱 Lab Architecture

| VM | OS | Role |
|---|---|---|
| SIEM Server | Ubuntu 26.04 LTS | Wazuh 4.7.5 + OpenSearch |
| Attacker | Kali Linux 2026.2 | Attack simulation |

Virtualized on VMware Workstation 17 Player in an isolated network.

---

## ⚔️ Attacks Simulated & Detected

| # | Attack | Tooling | Outcome |
|---|---|---|---|
| 1 | Port scan | Nmap (`-sS -A`) | Open ports 22 / 80 / 443 discovered |
| 2 | SSH brute force | Hydra + rockyou | 128 failed logins → brute-force alert |
| 3 | Vulnerability scan | Nmap `--script vuln` | Found CVE-2007-6750 (Slowloris) |
| 4 | Slowloris DoS | slowhttptest | 911 connections held — Apache taken down |
| 5 | SSH login loop | bash (50×) | ~200 alerts / 30 s spike in Wazuh |
| 6 | Web-app attacks | curl | Directory traversal / SQLi / web-shell attempts |
| 7 | Successful SSH login | ssh | Valid-account access + post-login enumeration |

---

## 📊 Detection Results

| Metric | Value |
|---|---|
| Total security alerts | **3,726+** |
| Authentication failures | 131 |
| CVEs discovered | 1 (CVE-2007-6750) |
| Slowloris peak connections | 911 |
| Alert spike (SSH loop) | ~200 alerts / 30 s |
| MITRE ATT&CK techniques | 5 |

## 🎯 MITRE ATT&CK Coverage

| Technique | Name | Triggered by |
|---|---|---|
| T1110 | Brute Force | Hydra SSH attack |
| T1110.001 | Password Guessing | Hydra + SSH loop |
| T1021.004 | Remote Services: SSH | SSH login attempts |
| T1078 | Valid Accounts | Successful SSH login |
| T1021 | Remote Services | Post-login activity |

---

## 🔧 Key Findings & Recommended Fixes

- **CVE-2007-6750 (Slowloris)** — Apache brought down by 911 held connections → enable `mod_reqtimeout`.
- **SSH brute force / exposure** — deploy `fail2ban`, disable root login (`PermitRootLogin no`), and
  move SSH off the default port 22.

---

## 💡 Lessons Learned

- Standing up a real SIEM is non-trivial: certificate handling, OS compatibility (Ubuntu 26.04 vs
  Wazuh 4.7.5), and credential management all required genuine troubleshooting.
- Attacks leave clear signatures — even without Level-12 critical alerts, brute force, DoS, and
  credential access are identifiable through alert patterns and evolution charts.

---

*Built for educational purposes in an isolated lab environment. Part of a cybersecurity portfolio
by Sarjan Patel.*
