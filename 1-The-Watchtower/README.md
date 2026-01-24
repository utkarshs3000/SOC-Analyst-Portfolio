# 📡 Project 1: The Watchtower
**Focus:** SIEM Architecture & Log Ingestion
**Tools:** Ubuntu Server, Wazuh, Elastic Stack

## // MISSION BRIEF
To construct a centralized logging environment capable of detecting brute-force attacks and malware signatures in real-time. This server acts as the "Brain" of the SOC.

## // ARCHITECTURE
* **OS:** Ubuntu Server 22.04 LTS (Headless)
* **IP Addressing:** Static (Bridged Mode)
* **Remote Access:** SSH via Key-Pair

## // DEPLOYMENT LOG
### Phase 1: Virtualization
Deployed Ubuntu Server on VirtualBox with 4GB RAM / 2 vCPUs.
* [x] ISO Verification (SHA256)
* [x] Disk Encryption (LVM)
* [x] SSH Service Hardening

### Phase 2: Wazuh Installation
Executed the unattended installation script to deploy the Manager, Indexer, and Dashboard.

**Verification Evidence:**
*(You will move your screenshot here later)*
