# SOC-Detection-Lab-Capstone
![Wazuh](https://img.shields.io/badge/Wazuh-SIEM-blue?style=flat-square)
![Suricata](https://img.shields.io/badge/Suricata-IDS-orange?style=flat-square)
![pfSense](https://img.shields.io/badge/pfSense-Firewall-darkblue?style=flat-square)
![Sysmon](https://img.shields.io/badge/Sysmon-Endpoint%20Logging-purple?style=flat-square)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-black?style=flat-square)
![Kali Linux](https://img.shields.io/badge/Kali-Linux-557C94?style=flat-square)
![VMware](https://img.shields.io/badge/VMware-Hypervisor-607078?style=flat-square)

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

**Firewall Logs:**

![SMB Brute Force Detection](screenshots/smb-brute-force-firewall-log.png)

**Windows Event ID 4625 — Failed Logon Attempts:**

![SMB Brute Force Detection](screenshots/smb-brute-force_event-4625.png)

**Wazuh SIEM Detection:**

![SMB Brute Force Detection](screenshots/smb-brute-force-wazuhcustomdetection.png)
![SMB Brute Force Detection](screenshots/smb-brute-force-wazuhcustomdetection1.png)

**Custom Detection Rule (Rule ID: 100300):**

![SMB Brute Force Detection](screenshots/smb-brute-force-wazuhcustomrule.png)

### 3. Privilege Escalation — runas.exe (T1548)
- **Attack:** Native Windows runas.exe spawning elevated command shell
- **Detection:** Sysmon Event ID 1 capturing parent-child process chain
- **Wazuh Rule:** Custom rule detecting suspicious process relationships and elevated execution
- **Result:** ✅ Detected — runas.exe spawning cmd.exe flagged as privilege escalation

**Custom Detection Rule (Rule ID: 100302):**

![Privilege Escalation](screenshots/pe-customrule.png)

**Windows Event ID 4624 — Logon Session (labuser):**

![Privilege Escalation](screenshots/pe-labuser-cmd.png)

**Windows Event ID 4688 — Process Creation (labuser → cmd):**

![Privilege Escalation](screenshots/pe-labuser-cmd1.png)

**Wazuh SIEM Detection (labuser context):**

![Privilege Escalation](screenshots/pe-labuser-cmd_wazuhcustomrule.png)
![Privilege Escalation](screenshots/pe-labuser-cmd_wazuhcustomrule1.png)

**Windows Event ID 4688 — whoami (Post-Escalation Recon):**

![Privilege Escalation](screenshots/pe-labuser-whoami-cmd.png)

**Windows Event ID 4624 — Elevated Logon (LAB\Administrator):**

![Privilege Escalation](screenshots/pe-runas-admin-4624.png)

**Windows Event ID 4688 — Process Creation (Administrator → cmd):**

![Privilege Escalation](screenshots/pe-runas-admin-4688.png)

**Wazuh SIEM Detection (Administrator context):**

![Privilege Escalation](screenshots/pe-runas-admin-cmd_wazuhcustom.png)
![Privilege Escalation](screenshots/pe-runas-admin-cmd_wazuhcustom1.png)

### 4. Execution — Obfuscated PowerShell (T1059.001)
- **Attack:** PowerShell with -ExecutionPolicy Bypass and IEX DownloadString flags
- **Detection:** Sysmon Event ID 1 captured full command line including obfuscated flags
- **Wazuh Rule:** Custom rule detecting ExecutionPolicy Bypass + DownloadString behavior
- **Result:** ✅ Detected — remote script execution artifact identified

**Custom Detection Rule (Rule ID: 100405):**

![PowerShell Abuse](screenshots/powershell-abuse-custom-rule.png)

**Sysmon Event ID 1 — Process Create (PowerShell with IEX DownloadString):**

![PowerShell Abuse](screenshots/powershell-abuse-event-id1.png)

**Windows Event ID 4688 — Process Creation (ExecutionPolicy Bypass Command Line):**

![PowerShell Abuse](screenshots/powershell-abuse-event-id3.png)

**Sysmon Event ID 11 — File Created (PSScriptPolicyTest artifact):**

![PowerShell Abuse](screenshots/powershell-abuse-event-id2.png)

**Wazuh SIEM Detection:**

![PowerShell Abuse](screenshots/powershell-abuse-custom-wazuh-rule.png)
![PowerShell Abuse](screenshots/powershell-abuse-custom-wazuh-rule1.png)
![PowerShell Abuse](screenshots/powershell-abuse-custom-wazuh-rule2.png)

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


