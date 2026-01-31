// Project 2: The Detection Lab

In this project, I set up a professional monitoring system to catch "hackers" in real-time. I used **Sysmon** to record every move made on a Windows 10 computer and sent those records to **Wazuh**, a central security dashboard (SIEM). This setup is the foundation of a modern Security Operations Center (SOC).

---

//: Installing the "Security Cameras" (Telemetry)

**What is this?** Standard Windows logs are like a blurry security camera. **Sysmon** is like upgrading to 4K resolution. It records exactly what programs are running and what commands are being typed.

**Steps Taken:**

1. **Installed Sysmon:** I added Sysmon to my Windows 10 VM using a specialized configuration (SwiftOnSecurity) that filters out junk data and only keeps the important security events.
2. **Connected to the Dashboard:** I installed a Wazuh agent on the Windows 10 VM. This agent "picks up" the Sysmon logs and delivers them to my Wazuh dashboard so I can see everything in one place.

#### ### Proof of Work

**🚨 [INSERT SCREENSHOT 1]** *Description: Screenshot of Windows Event Viewer showing Sysmon logs flowing (Event ID 1).*

**🚨 [INSERT SCREENSHOT 2]** *Description: Screenshot of the Wazuh Dashboard showing the Windows 10 machine as "Active."*
