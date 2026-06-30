![Status](https://img.shields.io/badge/status-complete-brightgreen)

## Risk Assessment Lab

A cybersecurity risk assessment lab that simulates an internal assessment of an organisation’s infrastructure using Advanced IP Scanner, Total Network Inventory 6, Tenable Nessus Essentials, and Python automation that converts Tenable HTML reports into presentation ready Excel risk reports. The assessment also includes internal penetration testing focused on SMB access validation, MAC flooding, and a man in the middle (MITM) attack to evaluate the organisation’s internal network security posture. 

---

## Porject Summary 

This project simulates how an organisation can assess its internal infrastructure to understand what assets exist, what security weaknesses affect them, what services are exposed, and which risks should be prioritised.

I first discovered active systems in the internal network using Advanced IP Scanner. I then used Total Network Inventory to collect asset informations. After the inventory stage, I performed vulnerability scanning with Tenable Nessus in the subnet. The scan identified vulnerabilities, missing patches, exposed services, CVSS scores and operating system information.

To convert the scan results into useful risk assessment deliverables, I developed a Python script that parses Nessus and Advanced IP Scanner HTML exports and automatically generates presentation ready xlsx files:
1) Asset Inventory.xlsx
2) Threat Catalogue.xlsx
3) Information Security Risk Assessment.xlsx
4) Risk Treatment Plan.xlsx
   
---

## Assessment Scope and Workflow

The assessment was performed in a controlled Hyper-V lab environment containing Windows endpoints, Windows Server systems, a domain controller and internal file sharing services. All testing was performed only against systems owned and controlled within the lab environment.

Asset Discovery: Inventory Collection -> Vulnerability Scanning -> Automated Risk Reporting -> Internal Security Pen.Testing -> Recommended Treatment Actions

---

## Lab Architecture

| Machine / System         | Operating System                 | Role                                                                                                                                                                                                            |
| ------------------------ | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `VM-AUDIT`          | Windows 10 Pro                      | Main assessment workstation used for Advanced IP Scanner, Total Network Inventory 6, Tenable Nessus Essentials, Python automation, and review of the generated Excel risk assessment reports                    |
| `Kali5`          | Kali Linux                       | Internal penetration testing machine used for SMB access validation, MAC flooding, MITM testing, and Nmap enumerations.                                                               |
| `FsAskisi`              | Windows Server 2019      | Primary target for internal penetration testing, including SMB access validation, MAC flooding, MITM testing, and Wireshark packet capture and traffic analysis.                                                                                |
| `Additional Windows VMs` | Windows 10 and Windows Server 2019 | Additional in-scope systems used to populate the subnet and provide multiple assets, operating systems, installed software, services, and vulnerability findings for Advanced IP Scanner, TNI, and Nessus scans |


---
## What I Did

### 1. VM-AUDIT Setup

Created a Windows 10 virtual machine in Hyper-V to use as the main assessment workstation. Installed and used: 
a) Advanced IP Scanner
b) Total Network Inventory
c) Tenable Nessus Essentials
d) Python and the Python libraries for report generation
e) LibreOffice for reviewing the generated xlsx files

---

### 2. Network Discovery with Advanced IP Scanner

Used Advanced IP Scanner to identify active systems within the internal subnet. The scan was used to identify: IP addresses, Hostnames, MAC addresses, Device manufacturers and Available systems in the network.

The results were exported as a HTML and later used by the Python reporting script to support Asset Inventory excel creation.

---

### 3. Asset Inventory Collection with Total Network Inventory

The collection process used administrator credentials and gathered detailed information such as: Operating Systems, Computer Names, Hardware and Software Informations.

This scan provided a broader asset management view than just a network discovery alone.

---

### 4. Vulnerability Scanning with Tenable Nessus 

Performed a vulnerability basic scan on the subnet: `192.168.10.0/24`. After the scan, I exported Nessus the below HTML reports:

a) Vulnerabilities report
b) Operating Systems report
c) Software Vulnerabilities report

---

### 5. Python Reporting Automation

Developed a Python script that processes Nessus and Advanced IP Scanner HTML exports and automatically creates GRC style Excels that are ready to present to the organization the risk assessment is done for.

The script accepts: A Nessus Vulnerabilities HTML report, a Nessus Operating Systems HTML report and a Advanced IP Scanner HTML report <br>
and it generates 4 Excel files: 
| Deliverable                          | Purpose                                                        |
| ------------------------------------ | -------------------------------------------------------------- |
| Asset Inventory                      | Lists discovered assets and classifies asset types             |
| Threat Catalogue                     | Lists actionable vulnerability findings by severity            |
| Information Security Risk Assessment | Converts findings into risk scores and ratings                 |
| Risk Treatment Plan                  | Provides recommended treatment actions and reassessment fields |

The script uses a generic CVSS:
| CVSS / Severity     | Likelihood | Impact | Risk Score | Risk Rating   | Suggested Action        |
| ------------------- | ---------- | ------ | ---------- | ------------- | ----------------------- |
| Critical / 9.0–10.0 | 5          | 5      | 25         | Critical      | Immediate remediation   |
| High / 7.0–8.9      | 4          | 5      | 20         | High          | Prioritised remediation |
| Medium / 4.0–6.9    | 3          | 3      | 9          | Medium        | Planned remediation     |
| Low / 0.1–3.9       | 2          | 2      | 4          | Low           | Accept or remediate     |
| Informational / 0.0 | 1          | 1      | 1          | Informational | Monitor                 |

---

### 6. Kali Linux to perform Internal Host Discovery and Service Detection

Used Kali Linux and Nmap to perform internal host discovery and service enumeration within the lab subnet. First, I performed a host discovery scan across 192.168.10.0/24 to identify live systems. The results were exported to host-discovery.txt for review and documentation.

I then performed targeted service enumeration against selected 5 hosts. The scan identified open ports, running services, service versions, and SMB security settings.

Examples of identified services: SMB — TCP/445, RDP — TCP/3389, WinRM — TCP/5985, LDAP — TCP/389, LDAPS — TCP/636, Kerberos — TCP/88 and TCP/464 and HTTP services.

The service results were exported to services-scan-target.txt and used to identify systems and services for further internal security validation, including SMB access checks.

---

### 7. SMB Enumeration and Guest Access 

Performed SMB enumeration against systems exposing TCP/445. The checks included: 

a) SMB signing status and SMBv1 status

b) Hostname and domain information

c) Guest account access behaviour

d) Accessible SMB shares

e) Read permissions on the exposed shares

This demonstrated that overly Guest access and share permissions can expose internal data to unauthorised users on the same network. Some systems returned NT_STATUS_ACCESS_DENIED, showing that SMB was available but anonymous share enumeration was restricted.

--- 


### 8. MAC Attack Demonstration

Performed a controlled MAC flooding attack from Kali Linux against the `FsAskisi` VM environment.

Steps followed to demonstrate the attack: 

a) Enabled MAC address spoofing on the Kali Linux VM.

b) Started Wireshark on the target machine, `FsAskisi`, before launching the attack.

c) Ran the following command on the Kali Linux VM cmd:<br> 
```bash 
sudo macof -i eth0`
```

The command generated Ethernet frames with changing source MAC addresses.

d) Observed and captured the resulting ARP broadcasts and abnormal incoming in Wireshark on `FsAskisi`.

The MAC flooding test was performed to demonstrate how an internal attacker could generate large volumes of Ethernet traffic with changing source MAC addresses and create abnormal  activity in the network. This highlights the importance of implementing switch level controls such as limiting the number of MAC addresses allowed per port, allowing only approved MAC addresses where appropriate, monitoring abnormal MAC address activity through IDS or network monitoring, and using VLAN segmentation to reduce the impact of unauthorised internal traffic.

----

### 9. DoS Attack Demonstration

A controlled ICMP Flood Denial of Service attack was performed from the Kali Linux VM against FsAskisi to assess how excessive ICMP traffic could affect a host and the network availability.

Steps followed to demonstrate the attack: 

a) From the Kali Linux VM, ran the following command against the isolated target `FsAskisi`:
```bash
sudo hping3 -1 --flood 192.168.10.99
```
b) Started Wireshark on `FsAskisi` and applied an ICMP filter to capture incoming Echo Request traffic.
and observed a sustained stream of ICMP Echo Request packets arriving.

The ICMP flood test was performed to demonstrate how an attacker could generate a large volume of ICMP traffic toward a target system and potentially affect its availability by consuming network or host resources. This highlights the importance of limiting unnecessary ICMP traffic through firewall rules, applying ICMP rate limiting to prevent excessive traffic, and monitoring the network for unusually high ICMP traffic levels.

---

### 10. Man in the Middle (MITM) Attack Demonstration

The final penetration testing activity was a controlled man in the middle attack against `FsAskisi` using Ettercap from the Kali Linux VM.

Steps followed to demonstrate the attack: 

a) Opened Ettercap on the Kali Linux VM and selected the eth0 network interface.

b) Performed host discovery to identify systems available on the internal network.

c) Selected ` 192.168.10.99 = FsAskisi ` as the target system.

d) Configured the target and initiated an ARP based man in the middle attack from the Kali Linux VM.

e) On the target VM, opened Command Prompt and ran:
```cmd
arp -a
``` 
f) Verified that the default gateway IP address was associated with the MAC address of the Kali Linux VM.

The ARP table on the target VM showed that the gateway IP address had been associated with the Kali Linux VM’s MAC address. This confirmed that ARP spoofing had altered the target’s local ARP mapping and that traffic intended for the gateway could be redirected through the Kali Linux VM that has the attacker role.

The MITM attack demonstrated how ARP spoofing can redirect internal traffic through an attacker-controlled system. This highlights the importance of using HTTPS instead of HTTP, keeping TLS certificates valid and up to date, and avoiding self signed certificates for services where trusted certificates can be used. These controls help protect traffic from interception even if an attacker gains a man in the middle position on the network.

---

