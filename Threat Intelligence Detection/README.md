# Intergrating Threat Intelligence

:star: **Read the steps by the coresponding numbers on the screenshot for a better understanding.**
- Have your Threat Intelligence feed, I will be using Microsoft Defender Threat Intellgence (Premium)
- Have your EDR/SIEM Solution, I will be using Microsoft Sentinel and M365 Defender (E5 Security)
- There's some free open source Threat Intel you may use like MISP or OpenCTI, please follow their page for installtion steps.

![image](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/MDTI.png)

1. Analyze the relevance of the threat intelligence to your organization's security posture.
  > Is this APT targeting a software company, government agency, or health facilities?

2. Identify the IOCs from the threat intelligence report and you may use them to create custom detection rules. In some cases, your EDR provider should create rules to detect the IOCs. 
> But by creating detection rules, you can increase visibility and help threat hunters identify potential threats.  Unless, there's an automation on threat intel feed connected to your SIEM or EDR via API.
- :link: [How to Detect IOC](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Detection%20on%20IOC.md):exclamation:

3. Determine which MITRE ATT&CK techniques are being used by the APT groups and threat actors mentioned in the report. Identify the specific vulnerabilities, devices, or company assets being exploited by these techniques.
> Learning the TTP the attackers will use can help you proactively be ahead of the game by creating a detection based on their known attack patterns
- :link: [How to Detect MITRE TTP](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Detection%20on%20MITRE%20ATT%26CK%20Techniques.md):exclamation:


## [+] Does it Impact your organization?
If the threat intel report is relevant to your organization, here are some steps to be more proactive to detect and mitigate any threats coming your way. If it does, follow the next two methods to harden your defense. Follow the two procedures.
- :link: [How to Detect IOC](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Detection%20on%20IOC.md) :exclamation:
- :link: [How to Detect MITRE TTP](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Detection%20on%20MITRE%20ATT%26CK%20Techniques.md):exclamation:
