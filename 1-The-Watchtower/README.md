# Watchtower: Enterprise Security Operations Center (SOC)

**Status:** ✅ Operational
**Version:** Wazuh 4.14.2
**Architecture:** Distributed (Single-Node Cluster Simulation)

## 📖 Overview
**Watchtower** is a centralized Security Information and Event Management (SIEM) system built on the Wazuh platform. It acts as the "nervous system" for the trading infrastructure, providing real-time threat detection, integrity monitoring, and incident response for the algorithmic trading bot network.

Unlike standard deployments, Watchtower was engineered to run on a resource-constrained environment, requiring significant custom configuration to bypass standard dependency locks and race conditions.

## 🏗️ Architecture Stack
The cluster consists of four distinct, integrated components:
1.  **The Brain (Wazuh Manager):** Analyzes incoming telemetry and triggers alerts.
2.  **The Memory (Wazuh Indexer):** High-performance search engine (OpenSearch) for long-term data retention.
3.  **The Courier (Filebeat):** Secure pipeline transporting encrypted data from Manager to Indexer.
4.  **The Face (Wazuh Dashboard):** Visual analytics interface for threat hunting and "Green/Red" status monitoring.

---

## 🛠️ Engineering Log: Critical Challenges & Solutions
*During the deployment of the Watchtower Security Cluster, we encountered and resolved several high-priority infrastructure blockers. This log documents the troubleshooting protocols used to stabilize the system.*

### 1. The Dependency Race Condition ("The Zombie Lock")
* **Issue:** The Wazuh Manager and Filebeat services entered a crash loop upon startup.
* **Root Cause:** A system-level Race Condition occurred where the `apt` package manager was locked by a background process, and the Filebeat service attempted to connect to the Indexer before the database was fully initialized ("Green" status).
* **Solution:**
    * Manually terminated zombie lock files (`/var/lib/dpkg/lock`).
    * Forced a dependency integrity check (`dpkg --configure -a`).
    * Implemented a "Wait-for-Green" protocol: verified the Indexer API response via `curl` before releasing the Manager service.

### 2. The Permission Trap ("Shell Expansion Failure")
* **Issue:** Critical SSL certificates failed to transfer to the Dashboard directory, resulting in `Permission Denied` errors even when executed with `sudo`.
* **Technical Insight:** The shell attempted to expand the wildcard (`*`) in `cp /certs/*` *before* applying `sudo` privileges. Since the unprivileged user had no read access to the source folder, the command failed pre-execution.
* **Solution:**
    * Bypassed shell expansion by copying files **explicitly by name** (Root CA, Admin Cert, Admin Key).
    * Strictly defined ownership (`chown wazuh-dashboard`) and applied "Read-Only" permissions (`chmod 400`) to secure the credentials.

### 3. The Version Mismatch Crisis (4.9 vs 4.14)
* **Issue:** The Dashboard UI crashed with a persistent `Application Not Found` error.
* **Root Cause:** Legacy configuration documentation pointed the Dashboard to a deprecated startup path (`/app/wazuh`) and attempted to inject an incompatible plugin version (4.9) into the modern 4.14.2 engine.
* **Solution:**
    * Executed a **"Scorched Earth" Purge**: completely removed the Dashboard package and configuration files.
    * Reinstalled clean 4.14.2 binaries.
    * Rewrote the `opensearch_dashboards.yml` configuration to target the modern route: `uiSettings.overrides.defaultRoute: /app/wz-home`.

### 4. The "Ghost" Log & Hash Collision
* **Issue:** The Dashboard failed to authenticate with the Database, despite correct credentials.
* **Root Cause:** The internal password hashing script (`hash.sh`) failed silently due to Java environment permission restrictions, writing a null/corrupted hash into the internal user database.
* **Solution:**
    * Performed a "Root-Force" fix by exporting the Java home path explicitly within the `sudo` context (`sudo -E`).
    * Generated a valid BCrypt hash and forcibly injected it into the internal user table using the security admin tool.

---

## 🚀 Access & Usage

### Dashboard Access
* **URL:** `https://10.10.1.8`
* **Default Route:** `/app/`
* **Status:** Secure (SSL Enabled)
<img width="1920" height="1080" alt="Screenshot 2026-01-26 135038" src="https://github.com/user-attachments/assets/965b5dbf-bf2c-442e-9fd1-936ad998f568" />

---

## 🔮 Roadmap: "Operation First Eyes"
The immediate next phase is to deploy **Wazuh Agents** to the trading infrastructure.

* [ ] **Agent Deployment:** Install lightweight agents on the Trading Bot Host.
* [ ] **Log Integration:** Configure agents to ingest Python trading logs (execution errors, API latency).
* [ ] **Custom Alerting:** Create rules to trigger alerts on "Abnormal Trading Volume" or "API Disconnection."

---
