# SOC-Analyst-Portfolio
# SOC ANALYST / DEFENSE PORTFOLIO
---

**OPERATOR** :: [Utkarsh Soni]
**ROLE** :: SOC Analyst / Security Engineer
**STATUS** :: [ IN-PROGRESS ]

## // EXECUTIVE SUMMARY

This repository documents the end-to-end deployment of a **Security Operations Center (SOC)** environment, designed to simulate real-world attack vectors and defensive protocols. The project strictly aligns with the **NIST Cybersecurity Framework (CSF) 2.0** and **MITRE ATT&CK** methodologies.

**CORE OBJECTIVES**
> **Infrastructure as Code** :: Deploying virtualized logging and monitoring servers.
> **Threat Detection** :: Implementing SIEM (Wazuh/ELK) for log ingestion and analysis.
> **Incident Response** :: Executing playbooks against simulated ransomware and brute-force attacks.

---

## // PROJECT 1: THE WATCHTOWER (SIEM ARCHITECTURE)
**NIST FUNCTION** :: DETECT [DE.AE-1]

### 1.1 INFRASTRUCTURE PROVISIONING
**OBJECTIVE** :: Establish a secure, isolated virtualization environment to host the Centralized Log Management system.

| COMPONENT | SPECIFICATION | RATIONALE |
| :--- | :--- | :--- |
| **Hypervisor** | VirtualBox / VMware | Isolation from host OS |
| **Host OS** | Ubuntu Server 22.04 LTS | Standard Enterprise Linux Environment |
| **Resources** | 4 vCPU / 4GB RAM | Optimized for Elasticsearch processes |
| **Network** | Bridged Adapter | Simulation of Enterprise LAN access |

<img width="1920" height="1080" alt="Screenshot 2026-01-24 214753" src="https://github.com/user-attachments/assets/5c9cff3e-2bb1-40f7-9e7f-cbb93160ff70" />


### 1.2 DEPLOYMENT VERIFICATION
**STATUS** :: [ COMPLETED ]

*Successful initialization of the Ubuntu Server kernel and SSH remote access verification.*

<img width="1920" height="1080" alt="Screenshot 2026-01-24 215038" src="https://github.com/user-attachments/assets/e5529f03-9d30-437a-aae4-08161a3a7194" />
