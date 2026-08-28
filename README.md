# ⚔️ Offensive Security Engineering

A hands-on repo where offensive techniques meet detection engineering. Attacks are run in controlled labs — then validated against **Microsoft Sentinel** and **Microsoft Defender XDR** to confirm they actually surface as alerts and incidents. The theme throughout: *don't just run the attack — prove you'd catch it.*

---

## ⚛️ Atomic Red Teaming — Detection Validation

Detection engineering is the practice of designing, implementing, and continually improving SIEM and EDR capabilities to catch real threats. This project focuses on **validating those detections** through controlled attack simulation — running **Atomic Red Team** tests mapped to **MITRE ATT&CK** against **Microsoft Sentinel** and **Microsoft Defender XDR** to prove coverage and find blind spots.

Before running any test, four questions scope the work:

- What do I need to detect?
- What critical assets need protecting?
- What techniques and tools are relevant to our environment?
- Which ATT&CK TTPs do we already have coverage for — and which are blind spots?

### 🔭 Scope

Validate and strengthen detection coverage through hands-on adversary simulation:

| Stage | What it does |
| --- | --- |
| **Attack simulation** | Execute Atomic Red Team tests and commands mapped to MITRE ATT&CK TTPs to exercise EDR / SIEM / SOAR against real attacker behavior. |
| **Detection validation** | Confirm each simulated technique generates the expected telemetry, alert, and severity. Where it doesn't, that's a gap to close. |
| **Detection tuning** | Build or refine the analytics rules and alert logic needed to cover the techniques that slipped through, then re-test to confirm the fix. |

The goal: prove that detections fire when they should, catch the ones that don't, and close the gap — technique by technique.

### ✅ Benefits

| Benefit | Why it matters |
| --- | --- |
| **Proactive security** | Find and close detection gaps before an attacker finds them. |
| **Validated coverage** | Confirm endpoint and SIEM detections actually alert on the techniques that matter, and surface what goes undetected. |
| **Better alert policies** | Prioritize the events that need immediate attention, reducing alert fatigue and sharpening response. |
| **Stronger skillsets** | Running simulations and writing the detections to catch them deepens the team's understanding of how attacks actually flow. |
| **Refined incident response** | Measurable coverage means threats get caught earlier, before damage is done. |

### 🧰 Requirements

- Microsoft Sentinel and/or Defender for Endpoint (or your EDR/SIEM of choice)
- Atomic Red Team (or your red-team simulation of choice)
- Working knowledge of PowerShell and Bash

➡️ See the [`Atomic Red Team`]([./Atomic%20Red%20Team](https://github.com/nguyentimmy/Offensive-Security-Engineering/tree/16b937595b2b247e13cac0969b3698dba4144ca5/Atomic%20Red%20Team%20%E2%9A%94%EF%B8%8F)) folder.

---

## 💥 Penetration Testing *(beginner-level)*

> 🗓️ *Archived work from 2022 — beginner-level pentesting labs. I was just starting out and admittedly pretty green (still am, honestly), but the goal was to get hands-on with common offensive tools, understand how they work, and validate whether my detections would pick them up. Uploaded as proof of work and to show the learning journey.*

Back in 2022 I set up a small purple-team range to try out penetration testing. These are entry-level exercises — nothing advanced — but each one pairs a **red-team simulation** with the **blue-team detection** built to catch it, walking the full loop: attack → detect → analyze.

➡️ See the [`Penetrating Testing`](./Penetrating%20Testing%20%F0%9F%92%A5) folder.

---

## ⚠️ Disclaimer

All activity was performed in isolated lab environments I owned, for educational and detection-validation purposes only. Nothing here targets systems I did not control.
