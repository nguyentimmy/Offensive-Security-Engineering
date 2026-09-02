# 🔓 RDP Brute Force Lab

> 🗓️ *This lab was conducted in 2022 for testing and educational purposes in an isolated environment. It is uploaded here to present my work. Please note that this is a very nooby pentesting assessment intended just for practice. :)*

## 🎯 Purpose

This lab demonstrates end-to-end detection of an RDP brute-force attack in an Azure environment using native Microsoft Sentinel analytics. It pairs a **red-team simulation** (Nmap reconnaissance + Hydra password attack) with **blue-team detection** built on Windows **Event ID 4625**, showing how a flood of raw failed logons can be turned into a single, high-severity incident with automated entity mapping and assignment. The goal is to walk the full purple-team loop — attack, detect, respond — and prove the analytics rule fires correctly in real time.

## 🧰 Stack

| Tool | Role | Purpose in this lab |
| --- | --- | --- |
| **Microsoft Sentinel** | Cloud-native SIEM | Ingests Windows security logs, runs the analytics rule, and generates the incident |
| **Windows Server** | Target host | The victim machine with RDP exposed; source of Event ID 4625 logs |
| **Nmap** | Network scanner | Reconnaissance — confirms RDP is open before the attack |
| **Hydra** | Brute-force tool | Generates the failed RDP logon traffic the detection is built to catch |
| **KQL** | Query language | Powers the Sentinel analytics rule that correlates the failed logons |

---

## ⚔️ Phase 1 — Attack Simulation
**Reconnaissance** — An Nmap scan confirms RDP is open on the Windows server. Exposed remote-access protocols like RDP and SSH are among the first things an attacker probes for, since they offer a direct path to interactive access. This particular scan is deliberately aggressive (`-T5 -vvv`) and would light up most modern defenses — IDS, IPS, SIEM, EDR, XDR — so it isn't how you'd operate quietly in the real world. It's run loud here on purpose, to clearly surface the open ports for demonstration.

```
nmap -sC -sV -vvv -T5 -oN report.txt 10.0.0.4
```

**Brute force** — With RDP confirmed open, Hydra runs a dictionary attack against the `winserver` account using the `rockyou.txt` wordlist. Each failed attempt writes an Event ID 4625 to the Windows security log — exactly the traffic the Sentinel rule is watching for.

```
hydra -t 1 -V -l winserver -P rockyou.txt rdp://10.0.0.4
```
![Nmap scan showing RDP open on the target server](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/RDP%20Brute%20Force/Pictures/1.%20rdp%20bf.png)


---

## 🛡️ Phase 2 — Detection

On the blue-team side, I built a Sentinel analytics rule that raises an incident when it sees multiple failed sign-ins tied to **Event ID 4625** — the event Windows records for every failed local logon attempt. A short burst of these against a single account is a strong brute-force signal.

![Sentinel analytics rule detecting the brute-force attempt](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/RDP%20Brute%20Force/Pictures/2.%20rdp%20bf.png
)

- The rule successfully picked up the brute-force attempt as it happened.
- It's configured to **auto-assign** the incident to me, set the severity to **High**, and **map entities** (account, host, IP) so the incident is immediately actionable.

---

## ✅ Phase 3 — Result

Sentinel detected the activity and created a high-severity incident in real time, correlating the flood of failed RDP logons into a single, actionable incident rather than a stream of noisy individual events. The end result is a working analytics rule that reliably detects RDP brute forcing and hands the analyst a fully mapped incident to respond to.

![High-severity incident created in Sentinel](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/RDP%20Brute%20Force/Pictures/3.%20rdp%20bf.png
)

![Incident detail with mapped entities](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/RDP%20Brute%20Force/Pictures/4.%20rdp%20bf.png)
