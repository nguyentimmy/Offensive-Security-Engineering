# ⚛️ Atomic Red Team — Validation Walkthroughs

Detection-validation walkthroughs following the full attacker kill chain. Each covers three Atomic Red Team tests for a single MITRE ATT&CK technique — then checks whether the SIEM/EDR actually caught it. The pattern: **simulate → check → tune the gap → re-test.**

## 🗺️ The Kill Chain

| Stage | Technique | Focus |
| --- | --- | --- |
| 🚪 **Initial Access** | [T1078.001 — Default Accounts](./T1078-001-validation-walkthrough.md) | Guest / default account abuse for entry |
| ⚙️ **Execution** | [T1053.005 — Scheduled Task](./T1053-005-validation-walkthrough.md) | `schtasks` for execution and pivot |
| 🔁 **Persistence** | [T1547.001 — Run Keys / Startup Folder](./T1547-001-validation-walkthrough.md) | Registry autoruns — the most common persistence in the wild |
| 🔑 **Credential Access** | [T1003.001 — LSASS Memory](./T1003-001-validation-walkthrough.md) | ProcDump / comsvcs / direct syscalls to dump credentials |
| 🔍 **Discovery** | [T1087 — Account Discovery](./T1087-validation-walkthrough.md) | `net user` / `Get-ADUser` — post-foothold recon |
| ↔️ **Lateral Movement** | [T1021.006 — WinRM](./T1021-006-validation-walkthrough.md) | Fileless remote code exec over 5985/5986 |
| 🛡️ **Defense Evasion** | [T1562.001 — Disable/Modify Tools](./T1562-001-validation-walkthrough.md) | Kill / weaken / override AV before the main event |
| 💥 **Impact** | [T1486 — Data Encrypted for Impact](./T1486-validation-walkthrough.md) | Ransomware bulk encryption + ransom note drop |

## 📋 Walkthrough Format

Every walkthrough follows the same structure:

- **Why it matters** — why this technique is worth validating
- **Atomic Tests** — three tests spanning different tool classes so detections get stressed from multiple angles
- **Attack + cleanup commands** — copy-paste ready
- **What to validate** — the specific telemetry your detection should surface, plus a *"did all three tests fire?"* gap check
- **Detection tip** — the combination signal that separates malicious from benign

## ⚠️ Disclaimer

All tests are for detection validation in isolated lab environments only. Do not run against production systems or systems you do not own.