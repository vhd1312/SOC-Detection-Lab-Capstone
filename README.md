# SOC-Detection-Lab-Capstone
End-to-end SOC detection lab simulating real-world adversary behavior across 4 MITRE ATT&amp;CK techniques

![Status](https://img.shields.io/badge/Status-Completed-brightgreen)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE%20ATT%26CK-T1046%20|%20T1110.001%20|%20T1548%20|%20T1059.001-red)
![SIEM](https://img.shields.io/badge/SIEM-Wazuh-blue)
![IDS](https://img.shields.io/badge/IDS-Suricata-orange)

---

## 📌 Overview

This capstone project — completed as part of the MS in Cybersecurity program at 
UNC Charlotte — demonstrates end-to-end detection engineering in a fully operational 
SOC lab environment. The primary objective is to show how security events can be 
analyzed and correlated across multiple telemetry sources including endpoint logs, 
network traffic, and SIEM alerts to detect malicious activity across different stages 
of the cyber kill chain.

---

## 🏗️ Lab Architecture

![Lab Architecture](screenshots/lab-architecture.png)

**Components:**
| Role | Tool | IP |
|---|---|---|
| Attacker | Kali Linux | 192.168.153.131 |
| Firewall / IDS | pfSense + Suricata | WAN: 192.168.153.142 / LAN: 192.168.117.1 |
| SIEM | Wazuh Manager | 192.168.117.50 |
| Domain Controller | Windows Server + AD | 192.168.117.40 |
| Endpoint | Windows VM + Sysmon | 192.168.117.80 |

---

---

## 🎯 MITRE ATT&CK Coverage

| Stage | Technique | ID | Detection Source |
|---|---|---|---|
| Reconnaissance | Network Scanning (Nmap) | T1046 | Suricata IDS → Wazuh |
| Credential Access | Brute Force — Password Guessing | T1110.001 | Windows Event ID 4625 → Wazuh |
| Privilege Escalation | Abuse Elevation Control (runas.exe) | T1548 | Sysmon Event ID 1 → Wazuh |
| Execution | PowerShell — Obfuscated Execution | T1059.001 | Sysmon + Wazuh custom rule |

---

## 🔍 Attack Simulation & Detection

### 1. Reconnaissance — Nmap Scan (T1046)
- **Attack:** Nmap scan from Kali Linux targeting internal LAN network
- **Detection:** Suricata IDS detected port/service probes on WAN interface
- **Wazuh Rule:** Custom rule correlating Suricata alerts with source/destination IP metadata
- **Result:** ✅ Detected — attacker IP attributed, scanning behavior identified

**Suricata Alert Logs:**

![Suricata Nmap Alerts](screenshots/suricata-nmap-alert-log.png)
![Suricata Nmap Alerts](screenshots/suricata-nmap-alert-log1.png)

**Wazuh SIEM Detection:**

![Wazuh Nmap Detection](screenshots/nmap-wazuh-customdetection.png)
![Wazuh Nmap Detection](screenshots/nmap-wazuh-customdetection1.png)

**Custom Detection Rule (Rule ID: 100304):**

![Wazuh Nmap Detection Rule](screenshots/nmap-wazuh-customrule.png)


### 2. Credential Access — SMB Brute Force (T1110.001)
- **Attack:** SMB brute force from Kali Linux targeting Windows host
- **Detection:** Multiple Windows Event ID 4625 (failed logon) from single source IP
- **Wazuh Rule:** Custom correlation rule identifying brute force pattern threshold
- **Result:** ✅ Detected — brute force alert triggered with source IP attribution

![SMB Brute Force Detection](screenshots/smb-bruteforce-detection.png)

### 3. Privilege Escalation — runas.exe (T1548)
- **Attack:** Native Windows runas.exe spawning elevated command shell
- **Detection:** Sysmon Event ID 1 capturing parent-child process chain
- **Wazuh Rule:** Custom rule detecting suspicious process relationships and elevated execution
- **Result:** ✅ Detected — runas.exe spawning cmd.exe flagged as privilege escalation

### 4. Execution — Obfuscated PowerShell (T1059.001)
- **Attack:** PowerShell with -ExecutionPolicy Bypass and IEX DownloadString flags
- **Detection:** Sysmon Event ID 1 captured full command line including obfuscated flags
- **Wazuh Rule:** Custom rule detecting ExecutionPolicy Bypass + DownloadString behavior
- **Result:** ✅ Detected — remote script execution artifact identified

---

## 🛠️ Tools & Technologies

| Category | Tool |
|---|---|
| SIEM | Wazuh |
| IDS/IPS | Suricata |
| Firewall | pfSense |
| Endpoint Logging | Sysmon + Windows Event Logs |
| Hypervisor | VMware Workstation Pro |
| Attacker Machine | Kali Linux |
| Network | Active Directory + Windows Server |
| Packet Analysis | Wireshark / tcpdump |

---

## ⚙️ Detection Engineering

**7 Custom Wazuh Detection Rules written to identify:**
- Port scanning behavior from external attacker (T1046)
- Brute force patterns from single source IP (T1110.001)
- Suspicious parent-child process chains — runas.exe spawning shells (T1548)
- PowerShell ExecutionPolicy Bypass + DownloadString behavior (T1059.001)

**Log Pipeline:**
Sysmon + Windows Logs → Wazuh Agent → Wazuh Manager
Suricata Alerts → Syslog → Wazuh Manager
pfSense Firewall Logs → Syslog → Wazuh Manager

**Email Alerting:**
Configured Wazuh to send real-time email notifications on triggered 
detection rules for immediate SOC analyst notification.

---

## 📊 Key Results

| Metric | Result |
|---|---|
| Attack stages simulated | 4 |
| Attack stages detected | 4 / 4 ✅ |
| Custom Wazuh rules written | 7 |
| Telemetry sources correlated | 3 (Endpoint + Network + Firewall) |
| Email alerting | Configured ✅ |
| False positive reduction | Rule threshold tuning applied ✅ |

---

## 💡 Key Takeaway

Effective detection requires not just log collection but strong 
correlation, rule formulation, and contextual analysis across 
multiple telemetry sources. Raw logs alone are insufficient — 
the gap between collecting data and generating actionable alerts 
is where real detection engineering happens.

---


