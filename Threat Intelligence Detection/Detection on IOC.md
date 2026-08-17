## [+] Detecting IOC 

In this case, I will be use Defender for Endpoint as my EDR solution to create a detection alert and threat hunting query (Which can be beneficial for the threat hunters or SOC).

### I. Using the IOC from your threat intel feed 
Use the list of IOC provided by your threat intel or open source feed. In my case I will use Microsoft Threat Intel for my IOC. 
![Image](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/IOC-Coinminer.png)

### II. Creating the Query based on your needs
Alternatively, you can use an [IOC parser on SOC Prime](https://tdm.socprime.com/uncoder-cti/) platform to convert into the list IOC to query that fits your SIEM/EDR Solution. It will help speed up the proccess. However, you can also create the query of your choice on your own.

![Image](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/IOC-Steps.png)

Since I'm using Defender for Endpoint, here is an example of an KQL script that extract IOC's from a link that contains a TXT file. This query will search for any devices connected to any IOC. A detection rule can be created.

```
// This will search for the Indicators of Compromise (IOCs) in the form of SHA256 hashes within the link containing the TXT file.
let EmotetDomain = externaldata(Domain: string)[@"https://raw.githubusercontent.com/Cisco-Talos/IOCs/main/2022/11/Emotet_contacted_domains.txt"] with (format="txt", ignoreFirstRecord=True);
DeviceNetworkEvents
| where RemoteUrl in~ (EmotetDomain)
| project Timestamp, RemoteUrl, RemoteIP, DeviceName, DeviceId, InitiatingProcessCommandLine, InitiatingProcessFileName, InitiatingProcessAccountDomain, InitiatingProcessAccountName, ReportId
```

### III. Create a query or policy for Detection
You can create a custom detection rule or threat hunt to detect any IOC's in your environment. In this case, I will use the KQL that was provided on SOC prime to detect any files that contains that IOC. You create and modify the KQL on your own preference.

```
union * 
| where (InitiatingProcessMD5 =~ "2748c76e21f7daa0d41419725af8a134" or InitiatingProcessMD5 =~ "851d4ab539030d2ccaea220f8ca35e10" or InitiatingProcessMD5 =~ "bd0312d048419353d57068f5514240dc" or InitiatingProcessMD5 =~ "d63be89106d40f7b22e5c66de6ea5d65")
```

With Defender for Endpoint, I proactively hunt for threats in the M365 Portal, can be done within Sentinel as long as you have Data Connectors ingesting. 
![Photo](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/ThreatHuntDetection.png)
