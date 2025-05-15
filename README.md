# honeylab-homelab
this project is purely on Microsoft Azure to log the attacks and IP addresses


![Adobe Express - file](https://github.com/user-attachments/assets/471a054d-90ba-403d-a23d-9db3dd3cdcd0)
This project demonstrates how to deploy a Windows 10 honeypot in Azure, capture malicious activity, enrich log data with geographic information, and visualize attacks using Microsoft Sentinel.

🔹 Part 1: Azure Setup
Create an Azure Subscription


Sign up for a free account: https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account


Login to Azure


Portal: https://portal.azure.com



🔹 Part 2: Deploy the Honeypot (Windows VM)
Create a Virtual Machine


Navigate to "Virtual Machines" in the Azure portal.


Deploy a Windows 10 VM (select a lightweight size if using the Cyber Range).


Save the username and password for login.


Allow Inbound Traffic


Go to your VM’s Network Security Group (NSG).


Create a new inbound rule to allow all traffic.


Disable Windows Firewall (from within the VM)


Start → wf.msc → Properties → Turn off Domain, Private, and Public profiles.



🔹 Part 3: Simulate Failed Logins and View Logs
Simulate Attacks


Attempt three failed logins as a user (e.g., employee).


Inspect Logs in Event Viewer


Open Event Viewer → Security Logs → Look for Event ID 4625 (failed login attempts).



🔹 Part 4: Log Forwarding and KQL Queries
Create a Log Analytics Workspace (LAW)


Use the Azure portal to create a new LAW.


Deploy Microsoft Sentinel


Connect it to your LAW.


Connect Windows Security Events via AMA


Within Sentinel, go to Data Connectors → Enable Windows Security Events (via AMA).


This sets up a Data Collection Rule (DCR).


Query Logs with KQL
 Use the following query to find failed logins:

 SecurityEvent
| where EventID == 4625

🔹 Part 5: Enrich Logs with Geolocation Data
Download GeoIP Watchlist CSV


geoip-summarized.csv


Create a Watchlist in Sentinel


Name/Alias: geoip


Source Type: Local File


Lines before headers: 0


Search Key: network


Join Logs with GeoIP Data
 Sample KQL:

 let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == "<attacker IP address>"
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents



🔹 Part 6: Attack Map Visualization
Create a Workbook in Sentinel


Add a new Query element.


Switch to the Advanced Editor and paste the contents of map.json.


View and Analyze the Map


The map will display attacker IPs with geographic context using the enriched data.



✅ Summary
By the end of this lab, you will have:
Deployed a honeypot VM in Azure


Captured and analyzed failed login attempts


Forwarded logs to Microsoft Sentinel


Enriched logs with GeoIP data


Visualized attack origins using a custom map


