# 💥 Penetrating Testing

> 🗓️ *Archived work from 2022. These labs were built for educational purposes — to try out common offensive-security tools, understand how they work, and validate whether my detections would pick them up. Uploaded here as proof of concept. Very nooby pentesting :P*

## 📖 About

Back in 2022 I set up a small purple-team range to get hands-on with penetration testing. The goal wasn't just to run attacks — it was to run them against my own **Microsoft Sentinel** detections and confirm the activity actually surfaced as alerts and incidents. Each lab pairs a **red-team simulation** with the **blue-team detection** built to catch it, walking the full loop: attack → detect → analyze.

This is archived work, kept here to document the exercises and show the hands-on foundation behind my detection engineering.

## 🧪 Labs

| Lab | Attack | Detection |
| --- | --- | --- |
| **🔓 RDP Brute Force** | Nmap recon + Hydra brute force against exposed RDP | Sentinel analytics rule on Windows **Event ID 4625** (failed logons) |
| **💦 Password Spray** | MSOLSpray against Microsoft Online (Azure AD) users | Sentinel **SigninLogs** queries + workbook on spray result codes |

## ⚠️ Disclaimer

All activity was performed in an isolated lab environment I owned, for learning and detection-validation purposes only. Nothing here targets systems I did not control.