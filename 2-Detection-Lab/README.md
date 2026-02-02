# The Detection Lab //
To establish real-time monitoring on a Windows 10 endpoint and centralize the logs into a Security Information and Event Management (SIEM) platform.


## 01 // Process
1. Sysmon Deployment: Installed Microsoft Sysmon with a tuned configuration (SwiftOnSecurity) to capture high-fidelity process logs (Event ID 1) and network connections.
2. Wazuh Agent Installation: Deployed the Wazuh agent via PowerShell to act as the "bridge" between the endpoint and the SIEM manager.
3. Log Integration: Configured the agent to collect Sysmon telemetry, providing deeper visibility than standard Windows Event Logs.

## 02 // Technical Hurdles & Troubleshooting

While deploying the agent via PowerShell, I encountered an error where the `Wazuh` service would not start.

 The Issue: A mismatch in the filename variable in the `msiexec` command caused the installer to fail silently.
 The Fix: I manually verified the download path in `$env:tmp`, corrected the file extension, and re-executed the installation with Administrator privileges.
 Result: The service successfully registered as `Wazuh` and began communicating with the manager at `10.10.1.8`.

 ## Storage Optimization (LVM Management)
The Problem: The Wazuh Manager crashed as the root partition reached 100% utilization (9.8GB limit).

The Solution: I utilized LVM commands (lvextend and resize2fs) to dynamically expand the logical volume and resize the ext4 filesystem without data loss.

The Challenge: Attempting to expand the LVM failed because the system had 0 bytes of free space, preventing the creation of temporary archive files.

The Solution: I performed emergency maintenance by clearing the APT cache and vacuuming the system journals. This provided the "breathing room" necessary for the LVM commands to execute.

The Result: Expanded storage to [Insert New Size], ensuring the SIEM has enough overhead for index retention.

Problem: LVM expansion failed because the underlying disk partition (sda3) was still locked at the original size.

Solution: Used cfdisk to visually resize the physical partition on the disk map, followed by a pvresize to update the LVM physical volume.

Result: Successfully reclaimed the full 50GB allocated to the VM, providing a massive buffer for SIEM logs.

Objective: To generate realistic attack data (TTPs) using Kali Linux and native Windows tools to verify that our monitoring sensors (Sysmon/Wazuh) are functioning correctly.


## Re-establishing Data Flow (Final Resolution)
The Symptom: Filebeat service "talked" to the server but the Dashboard remained empty.

The Technical Fix: Cleared the corrupted Filebeat registry (/var/lib/filebeat/registry), which allowed the shipper to re-index the local alerts.json.

## Infrastructure Recovery & Engineering

The Problem

* **Disk Exhaustion**: The `/var/ossec` partition reached 100% capacity, corrupting the Filebeat registry.
* **Version Mismatch**: Filebeat 7.10 (Elastic) failed to communicate with the Wazuh Indexer (OpenSearch 2.x) due to legacy metadata injection (`_type` parameter), resulting in constant `400 Bad Request` errors.

### The Custom Solution

To bypass the "Filebeat Bottleneck," I engineered a **Direct-to-API Python Pipeline**. This script handles complex JSON schema and Windows character encoding (UTF-16LE) that standard shell scripts failed to parse.



## 03 // Evidence of Success

<img width="1920" height="1080" alt="Screenshot 2026-01-31 220359" src="https://github.com/user-attachments/assets/7f01a7d0-0fd9-4fc6-b399-493ea4a07a8e" />


---

 
<img width="1920" height="1080" alt="Screenshot 2026-01-31 223645" src="https://github.com/user-attachments/assets/0ebd1fdc-b94b-4356-aa46-1ccca74a8fcc" />

---

## 04 // Attack Simulation

### Malicious PowerShell Execution

Using a Kali Linux attacker machine, I executed an obfuscated PowerShell payload on a Windows 10 target to trigger **MITRE ATT&CK Technique T1059.001**.

* **Payload**: `powershell.exe -EncodedCommand <Base64_Blob>`
* **Detection**: Captured via **Sysmon Event ID 1** (Process Creation) and **Event ID 11** (File Creation).

---

##  Detection Summary Table

| Attack Technique | MITRE ID | Wazuh Rule ID | Severity | Evidence Captured |
| --- | --- | --- | --- | --- |
| **PowerShell Obfuscation** | T1027 | 5712 | 12 (High) | `-EncodedCommand` detected in `full_log`. |
| **Suspicious Process Spawn** | T1059.001 | 92057 | 12 (High) | `powershell.exe` spawning child processes. |
| **Malicious File Drop** | T1105 | 92213 | 15 (Critical) | `.ps1` script created in `\Temp\`. |

---

##  Evidence //

### 1. Indexer Health Status

* **Location**: Terminal (`curl -k -u admin:admin https://10.10.1.8:9200/_cat/indices?v`)
* **Screenshot Area**: Capture the `green open` status of the `wazuh-alerts-4.x-2026.02.02` index.
* **Significance**: Proves the manual ingestion pipeline successfully created the database shards.

  <img width="1920" height="1080" alt="Screenshot 2026-02-02 130045" src="https://github.com/user-attachments/assets/9ac511a5-8fc6-4721-a4f5-0459b415ca55" />


### 2. High-Severity Security Events

* **Location**: Wazuh Dashboard > Discover
* **Screenshot Area**: Apply filter `rule.level >= 12`.
* **Significance**: Displays the histogram showing the hits recovered from the backlog.

  <img width="1920" height="1080" alt="Screenshot 2026-02-02 125954" src="https://github.com/user-attachments/assets/cfdc6887-9d2f-48cb-8c9d-e1695e911647" />


---

### 3. Forensic Detail: Encoded Command

* **Location**: Wazuh Dashboard > Discover > Expanded Row (`>`)
* **Screenshot Area**: Focus on the `data.win.eventdata.commandLine` field showing the Base64 string.
* **Significance**: Proves "Deep Packet/Log Inspection" and successful capture of attacker intent.

  <img width="1920" height="1080" alt="Screenshot 2026-02-02 124951" src="https://github.com/user-attachments/assets/c3f783ad-6fa3-4c1d-8cce-20985989308d" />


### 4. MITRE ATT&CK Framework Mapping

* **Location**: Wazuh Dashboard > Threat Hunting > Security Events
* **Screenshot Area**: The MITRE heat map highlighting Technique **T1059.001**.

  <img width="1920" height="1080" alt="Screenshot 2026-02-02 125041" src="https://github.com/user-attachments/assets/cb98cb66-96e1-4970-a776-e26949520b87" />


---

---

##  Key Takeaways & Challenges

* **Character Encoding**: Encountered Unicode glitches (Chinese characters) during ingestion due to UTF-16LE/UTF-8 mismatches—resolved by pivoting to a binary-safe Python ingestion method.
* **Temporal Drift**: Identified a 6-hour timezone offset between the server and the browser, resolved by adjusting the Dashboard temporal window to "Last 24 Hours."
* **Automation**: Implemented a **Cron Job** to run the ingestion script every 60 seconds, ensuring a real-time data feed without relying on the failed Filebeat service.

---
