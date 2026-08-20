# ⚔️ Automating MITRE ATT&CK with Atomic Red Team

This project validates our detection coverage by simulating known attacks and confirming our SIEM/EDR catches them. Any red-team simulation framework works, but we use **Atomic Red Team** for consistency — it maps every test cleanly to MITRE ATT&CK.

The workflow is a validation loop: **simulate a technique → check whether it was detected → build or tune the detection if it wasn't → re-test to confirm.** Each Atomic test is a controlled, reversible way to answer one question — *would we catch this in production?*

---

## 🧰 Requirements

- [Atomic Red Team](https://github.com/redcanaryco/invoke-atomicredteam/wiki/Installing-Invoke-AtomicRedTeam#install-execution-framework-and-atomics-folder) *(see their wiki for install instructions)*
- PowerShell & Bash *(Atomic Red Team requires working knowledge of both)*
- Microsoft Sentinel and/or Defender for Endpoint

---

## 🗺️ Approach

We work through the ATT&CK matrix tactic by tactic. Starting with the first — [Initial Access](https://attack.mitre.org/tactics/TA0001/) — which covers the techniques adversaries use to gain their first foothold, such as spearphishing or exploiting public-facing services.

> 🎯 **Project story: "Defend and Detect: Initial Access"**

---

## 🧪 Validation Walkthrough — T1078.001: Default Accounts

**Tactic:** Initial Access, Persistence, Privilege Escalation, Defense Evasion
**Technique:** [T1078.001 — Valid Accounts: Default Accounts](https://attack.mitre.org/techniques/T1078/001)

### 📖 Why it matters

Adversaries abuse built-in default accounts — the Windows `Guest` and `Administrator` accounts, AWS `root`, ESXi `root`, the default Kubernetes service account — to gain access without dropping any malware. Appliances and network devices shipped with default credentials are easy targets when those credentials are never changed. Because this activity uses legitimate accounts and native OS commands, it blends into normal administration, which is exactly what makes validating detection for it worthwhile.

### ⚔️ The Atomic Tests

We validate three tests under this technique:

| # | Test | Platform | What it does |
| --- | --- | --- | --- |
| 1 | Enable Guest account with RDP + admin privileges | Windows | Activates Guest, adds it to Administrators and Remote Desktop Users, enables RDP |
| 2 | Activate Guest Account | Windows | Activates the default (disabled) Guest account |
| 3 | Enable Guest Account on macOS | macOS | Enables the Guest account via `sysadminctl` |

---
### 🔬 Test 1 — Enable Guest with RDP & admin privileges

The highest-signal of the three: it activates the Guest account, escalates it into the local Administrators and Remote Desktop Users groups, and turns on RDP — a full "backdoor account" setup in a handful of commands.

**Attack commands** (elevated `command_prompt`):

```cmd
net user guest /active:yes
net user guest Password123!
net localgroup Administrators guest /add
net localgroup "Remote Desktop Users" guest /add
reg add "hklm\system\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
reg add "hklm\system\CurrentControlSet\Control\Terminal Server" /v "AllowTSConnections" /t REG_DWORD /d 0x1 /f
```

**Cleanup:**

```cmd
net user guest /active:no
net localgroup Administrators guest /delete
net localgroup "Remote Desktop Users" guest /delete
```
---
### 🔬 Test 2 — Activate Guest account

The minimal version — a single command that flips the Guest account active. Good for validating whether even the low-noise version of this activity is detected.

```cmd
net user guest /active:yes
```
---
**Cleanup:** `net user guest /active:no`

### 🔬 Test 3 — Enable Guest account (macOS)

The macOS equivalent, for validating cross-platform coverage.

```sh
sudo sysadminctl -guestAccount on
```

**Cleanup:** `sudo sysadminctl -guestAccount off`

---

### ✅ What to validate after running

Each test should generate telemetry your detections can key on. After execution, confirm:

- **Did the SIEM/EDR alert?** Guest account activation and privileged group changes should surface in `DeviceEvents` / Security audit logs (`net user`, `net localgroup`, and the `fDenyTSConnections` registry write).
- **Was the severity right?** Adding Guest to Administrators is higher-impact than simply activating it — the alert should reflect that.
- **If nothing fired** — that's the gap. Build or tune a detection for the technique (account activation + privileged group add + RDP enablement), then re-run the test to confirm the fix.

> 💡 A clean detection here keys on the *combination* — a normally-disabled default account being activated **and** added to a privileged group **and** granted remote access in a short window. Any one alone may be benign; together they're a backdoor.

---

*Atomic test content last updated by Red Canary: 2024-07-16*
