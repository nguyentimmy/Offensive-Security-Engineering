# 🔓 RDP Brute Force Lab

> 🗓️ *This lab was conducted in 2022 for testing and educational purposes in an isolated environment. It is uploaded here to present my work.*

**Purpose:** End-to-end detection of an RDP brute-force attack in Azure using native Microsoft Sentinel analytics. Combines red-team simulation (Nmap + Hydra) with blue-team detection on Windows **Event ID 4625**, turning raw failed logons into a high-severity incident with automated entity mapping.

**Stack:** Microsoft Sentinel · Windows Server · Nmap · Hydra · KQL

---

## ⚔️ Phase 1 — Attack Simulation

![Nmap scan showing RDP open on the target server](https://github.com/nguyentimmy/azure-lab/blob/main/Purple%20Teaming%20Assessment/Network%20Pentest/01%20RDP%20Brute%20Force/Pictures/1.%20rdp%20bf.png)

**Recon** — Nmap confirms RDP is open. Open RDP/SSH are prime targets for exploitation. This scan is deliberately loud (`-T5 -vvv`) and would trip most modern defenses (IDS/IPS/SIEM/EDR) — run here only for demonstration.

```
nmap -sC -sV -vvv -T5 -oN report.txt 10.0.0.4
```

**Brute force** — With RDP discovered, Hydra runs a password attack to generate the failed-logon traffic the Sentinel rule is built to catch.

```
hydra -t 1 -V -l winserver -P rockyou.txt rdp://10.0.0.4
```

---

## 🛡️ Phase 2 — Detection

A Sentinel analytics rule generates an incident on multiple failed sign-ins by **Event ID 4625** (every failed local logon attempt).

![Sentinel analytics rule detecting the brute-force attempt](https://github.com/nguyentimmy/azure-lab/blob/main/Purple%20Teaming%20Assessment/Network%20Pentest/01%20RDP%20Brute%20Force/Pictures/2.%20rdp%20bf.png)

- The rule picked up the brute-force attempt.
- Configured to auto-assign the incident, set severity to **High**, and map entities.

---

## ✅ Phase 3 — Result

Sentinel detected the activity and created a high-severity incident in real time, correlating the flood of failed RDP logons into a single actionable incident — a working analytics rule that detects RDP brute forcing.

![High-severity incident created in Sentinel](https://github.com/nguyentimmy/azure-lab/blob/main/Purple%20Teaming%20Assessment/Network%20Pentest/01%20RDP%20Brute%20Force/Pictures/3.%20rdp%20bf.png)

![Incident detail with mapped entities](https://github.com/nguyentimmy/azure-lab/blob/main/Purple%20Teaming%20Assessment/Network%20Pentest/01%20RDP%20Brute%20Force/Pictures/4.%20rdp%20bf.png)
