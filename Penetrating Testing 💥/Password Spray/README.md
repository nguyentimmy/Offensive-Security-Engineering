# 💦 Password Spray Lab

> 🗓️ *This lab was conducted in 2022 for testing and educational purposes in an isolated environment. It is uploaded here to present my work.*

## 🎯 Purpose

This lab demonstrates end-to-end detection of a **password spray attack** against Microsoft Online (Azure AD / Entra ID) using native Microsoft Sentinel analytics. It pairs a **red-team simulation** (MSOLSpray against a list of tenant users) with **blue-team detection** built on Azure AD **SigninLogs** result codes, showing how spray attempts — and the account lockouts they trigger — surface as clear, correlated signals. The goal is to walk the full purple-team loop — attack, detect, analyze — and prove the detection surfaces both the successful spray and the resulting failures.

## 🧰 Stack

| Tool | Role | Purpose in this lab |
| --- | --- | --- |
| **Microsoft Sentinel** | Cloud-native SIEM | Ingests Azure AD sign-in logs, runs the KQL detections, and visualizes results in a workbook |
| **Azure AD / Entra ID** | Identity provider | The target tenant; source of the SigninLogs telemetry |
| **MSOLSpray** | Password spray tool | Sprays a single password across all tenant users to find valid credentials |
| **Kali Linux** | Attacker host | Runs the spray against Microsoft Online |
| **KQL** | Query language | Powers the Sentinel queries that detect the spray and the lockouts |

**Key sign-in result codes:**

| Code | Meaning |
| --- | --- |
| **50053** | Account locked out (or sign-in blocked due to repeated attempts / risky IP) |
| **50126** | Invalid username or password (the classic failed-spray signal) |
| **50072** | User prompted for MFA — often indicates a valid password |

---

## ⚔️ Phase 1 — Attack Simulation

![MSOLSpray returning a SUCCESS hit against a tenant user](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/1%20password%20spray%20success.png?raw=true)

**The spray** — From Kali, MSOLSpray runs against a list of 14 tenant users, trying a single common password across all of them at once. Spraying one password across many accounts (rather than many passwords against one) is what lets the attack fly under lockout thresholds.

```
./MSOLSpray.py -u users.txt -p Duda0375
```

The tool confirms a **SUCCESS** — one user's password matched the sprayed value, giving the attacker a valid set of Microsoft Online credentials.

**Escalation** — Follow-up sprays with additional passwords (`password`, `password123`) generate a wave of failed attempts. MSOLSpray reports several accounts now **appear to be locked**, which is itself a strong, noisy indicator on the defensive side.

```
./MSOLSpray.py -u users.txt -p password123
```

---

## 🛡️ Phase 2 — Detection

On the blue-team side, a Sentinel query against **SigninLogs** filters for the result codes tied to spray activity — failed logons, blocked sign-ins, and lockouts — over the last 24 hours, projecting the location, identity, and source IP for each hit.

```kql
SigninLogs
| where ResultType == "50053" or ResultType == "50126" or ResultType == "50072"
| where TimeGenerated > ago(24hr)
| project Location, Identity, IPAddress, ResultType, TimeGenerated
| sort by TimeGenerated asc
```

The results show a burst of failures from a **single source IP (20.0.136.31)** hitting many different identities — the signature pattern of a password spray.

---

## 📊 Phase 3 — Workbook Analysis

A Sentinel workbook visualizes the same activity, breaking sign-ins into Success / Failure / Pending buckets and surfacing the top error codes and the users behind them.

![Sentinel workbook summarizing sign-in errors and top offenders](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/2.%20sentinel%20workbook.png?raw=true)

- **Top errors** are dominated by **50126** (invalid username/password) — the failed spray attempts — alongside **50053** (blocked/locked) sign-ins.
- **Sign-ins with errors** ties those codes back to specific users and the shared attacker IP (`20.0.136.31`), making the single-source, many-target pattern obvious at a glance.

---

## 🔎 Phase 4 — Lockout Query

A focused query counts the **50126** failures per user to quantify how many accounts the spray hit and how hard.

```kql
SigninLogs
| where ResultType == "50126"
| where TimeGenerated > ago(24hr)
| summarize Count=count() by ResultType == "50126", UserDisplayName
```

![Per-user count of failed sign-ins from the spray](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/3.%20sentinel%20query.png?raw=true)

The output shows a consistent failure count across many users — each account hit roughly the same number of times, exactly what you'd expect from a password spray cycling one password across the whole user list.

---

## ✅ Result

The lab demonstrates the full password-spray lifecycle: a successful spray from a single attacker host, the account lockouts it triggered, and multiple Sentinel views — raw query, workbook, and per-user analysis — that detect and characterize the attack. The single-source-IP, many-target, uniform-failure pattern is the clear fingerprint that separates a spray from normal failed logons.
=======
# 💦 Password Spray Lab

> 🗓️ *This lab was conducted in 2022 for testing and educational purposes in an isolated environment. It is uploaded here to present my work. Please note that this is a very nooby pentesting assessment intended just for practice. :)*

## 🎯 Purpose

This lab demonstrates end-to-end detection of a **password spray attack** against Microsoft Online (Azure AD / Entra ID) using native Microsoft Sentinel analytics. It pairs a **red-team simulation** (MSOLSpray against a list of tenant users) with **blue-team detection** built on Azure AD **SigninLogs** result codes, showing how spray attempts — and the account lockouts they trigger — surface as clear, correlated signals. The goal is to walk the full purple-team loop — attack, detect, analyze — and prove the detection surfaces both the successful spray and the resulting failures.

## 🧰 Stack

| Tool | Role | Purpose in this lab |
| --- | --- | --- |
| **Microsoft Sentinel** | Cloud-native SIEM | Ingests Azure AD sign-in logs, runs the KQL detections, and visualizes results in a workbook |
| **Azure AD / Entra ID** | Identity provider | The target tenant; source of the SigninLogs telemetry |
| **MSOLSpray** | Password spray tool | Sprays a single password across all tenant users to find valid credentials |
| **Kali Linux** | Attacker host | Runs the spray against Microsoft Online |
| **KQL** | Query language | Powers the Sentinel queries that detect the spray and the lockouts |

**Key sign-in result codes:**

| Code | Meaning |
| --- | --- |
| **50053** | Account locked out (or sign-in blocked due to repeated attempts / risky IP) |
| **50126** | Invalid username or password (the classic failed-spray signal) |
| **50072** | User prompted for MFA — often indicates a valid password |

---

## ⚔️ Phase 1 — Attack Simulation

![MSOLSpray returning a SUCCESS hit against a tenant user](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/1%20password%20spray%20success.png?raw=true)

**The spray** — From Kali, MSOLSpray runs against a list of 14 tenant users, trying a single common password across all of them at once. Spraying one password across many accounts (rather than many passwords against one) is what lets the attack fly under lockout thresholds.

```
./MSOLSpray.py -u users.txt -p Duda0375
```

The tool confirms a **SUCCESS** — one user's password matched the sprayed value, giving the attacker a valid set of Microsoft Online credentials.

**Escalation** — Follow-up sprays with additional passwords (`password`, `password123`) generate a wave of failed attempts. MSOLSpray reports several accounts now **appear to be locked**, which is itself a strong, noisy indicator on the defensive side.

```
./MSOLSpray.py -u users.txt -p password123
```

---

## 🛡️ Phase 2 — Detection

On the blue-team side, a Sentinel query against **SigninLogs** filters for the result codes tied to spray activity — failed logons, blocked sign-ins, and lockouts — over the last 24 hours, projecting the location, identity, and source IP for each hit.

```kql
SigninLogs
| where ResultType == "50053" or ResultType == "50126" or ResultType == "50072"
| where TimeGenerated > ago(24hr)
| project Location, Identity, IPAddress, ResultType, TimeGenerated
| sort by TimeGenerated asc
```

The results show a burst of failures from a **single source IP (20.0.136.31)** hitting many different identities — the signature pattern of a password spray.

---

## 📊 Phase 3 — Workbook Analysis

A Sentinel workbook visualizes the same activity, breaking sign-ins into Success / Failure / Pending buckets and surfacing the top error codes and the users behind them.

![Sentinel workbook summarizing sign-in errors and top offenders](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/2.%20sentinel%20workbook.png?raw=true)

- **Top errors** are dominated by **50126** (invalid username/password) — the failed spray attempts — alongside **50053** (blocked/locked) sign-ins.
- **Sign-ins with errors** ties those codes back to specific users and the shared attacker IP (`20.0.136.31`), making the single-source, many-target pattern obvious at a glance.

---

## 🔎 Phase 4 — Lockout Query

A focused query counts the **50126** failures per user to quantify how many accounts the spray hit and how hard.

```kql
SigninLogs
| where ResultType == "50126"
| where TimeGenerated > ago(24hr)
| summarize Count=count() by ResultType == "50126", UserDisplayName
```

![Per-user count of failed sign-ins from the spray](https://github.com/nguyentimmy/Offensive-Security-Engineering/blob/main/Penetrating%20Testing%20%F0%9F%92%A5/Password%20Spray/Pictures/3.%20sentinel%20query.png?raw=true)

The output shows a consistent failure count across many users — each account hit roughly the same number of times, exactly what you'd expect from a password spray cycling one password across the whole user list.

---

## ✅ Result

The lab demonstrates the full password-spray lifecycle: a successful spray from a single attacker host, the account lockouts it triggered, and multiple Sentinel views — raw query, workbook, and per-user analysis — that detect and characterize the attack. The single-source-IP, many-target, uniform-failure pattern is the clear fingerprint that separates a spray from normal failed logons.

>>>>>>> 975aaabc297cee3dfb5749ab9808b5b2b943b57f
