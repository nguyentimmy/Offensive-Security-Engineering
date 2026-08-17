## [+] Detection on MITRE ATT&CK Techniques 

### I. Proactive Research on the Techniques
For this quick demo, I will use one of the MITRE ATT&CK techniques used by the threat actor from this Threat intel, in this case it will be [T1059.001](https://atomicredteam.io/execution/T1059.001/). Check the screenshot below. 

![Image](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/T1059-APT.png)

### II. Automating the attack simulation using Atomic Red Teaming
On the list of possible attacks, we will choose an attack the threat actor may most likely use from the threat intel report. Let's say the threat actor is using Bloodhound, we can simulate an attack directly from Atomic Red Team, which is based on the [T1059.001](https://atomicredteam.io/execution/T1059.001/).

![Image](https://github.com/nguyentimmy/Detection-Engineering/blob/main/2%20-%20Detect%20Current%20Attack%20Trends%20Using%20Threat%20Intelligence/Photos/T1059.001-BloodHound.png)

### III. Create a query or policy for Detection
You can create a custom detection rule or threat hunt to detect attacks from the threat actor using Bloodhound, [(T1059.001)](https://atomicredteam.io/execution/T1059.001/), here is the KQL to detect the threat. 

```
// Query for any bloodhound related processes and files
let BloodhoundCLI = dynamic([ 'Import-Module Sharphound.ps1' , '-collectionMethod', 'invoke-bloodhound', 'get-bloodhounddata']);
let BloodhoundExe = dynamic(['SharpHound.exe', 'BloodHound.exe', 'Neo4j-Management.exe']);
DeviceProcessEvents
| where Timestamp > ago(1d)
| where ProcessCommandLine has_any(BloodhoundCLI) or ProcessCommandLine has_any(BloodhoundExe) or FileName has_any(BloodhoundExe)
| project Timestamp, DeviceName, DeviceId, AccountName, AccountDomain, ProcessCommandLine, FileName, FolderPath, InitiatingProcessCommandLine, InitiatingProcessFileName, ReportId
```
