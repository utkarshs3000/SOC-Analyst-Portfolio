# The Detection Lab //
To establish real-time monitoring on a Windows 10 endpoint and centralize the logs into a Security Information and Event Management (SIEM) platform.

___
## 01 // Process
1. Sysmon Deployment: Installed Microsoft Sysmon with a tuned configuration (SwiftOnSecurity) to capture high-fidelity process logs (Event ID 1) and network connections.
2. Wazuh Agent Installation: Deployed the Wazuh agent via PowerShell to act as the "bridge" between the endpoint and the SIEM manager.
3. Log Integration: Configured the agent to collect Sysmon telemetry, providing deeper visibility than standard Windows Event Logs.

## 02 // Technical Hurdles & Troubleshooting

While deploying the agent via PowerShell, I encountered an error where the `Wazuh` service would not start.

 The Issue: A mismatch in the filename variable in the `msiexec` command caused the installer to fail silently.
 The Fix: I manually verified the download path in `$env:tmp`, corrected the file extension, and re-executed the installation with Administrator privileges.
 Result: The service successfully registered as `Wazuh` and began communicating with the manager at `10.10.1.8`.

## 03 // Evidence of Success

<img width="1920" height="1080" alt="Screenshot 2026-01-31 220359" src="https://github.com/user-attachments/assets/7f01a7d0-0fd9-4fc6-b399-493ea4a07a8e" />


 
<img width="1920" height="1080" alt="Screenshot 2026-01-31 223645" src="https://github.com/user-attachments/assets/0ebd1fdc-b94b-4356-aa46-1ccca74a8fcc" />

___
