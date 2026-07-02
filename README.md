![Status](https://img.shields.io/badge/status-complete-brightgreen)

## Risk Assessment Lab

A cybersecurity risk assessment lab that simulates an internal assessment of an organisation’s infrastructure using Advanced IP Scanner, Total Network Inventory 6, Tenable Nessus Essentials, and Python automation that converts Tenable HTML reports into presentation ready Excel risk reports. The assessment also includes penetration testing focused on SMB access validation, MAC & DoS ICMP flooding and a MITM attack to evaluate the organisation’s internal network security posture support its wider security and compliance efforts. 

---

## Porject Summary 

This project simulates how an organisation can assess its infrastructure to understand what assets exist, what security weaknesses affect them, what services are exposed, and which risks should be prioritised.

The main objective was to provide a structured, report based view of the organisation’s security posture and support its wider security and compliance efforts. The assessment outputs can help an organisation identify gaps, prioritise remediation actions, and maintain evidence that may support alignment with cybersecurity frameworks and regulatory requirements such as NIS2.

I first discovered active systems in the internal network using Advanced IP Scanner. I then used Total Network Inventory to collect asset informations. After the inventory stage, I performed vulnerability scanning with Tenable Nessus in the subnet. The scan identified vulnerabilities, missing patches, exposed services, CVSS scores and operating system information.

To convert the scan results into useful risk assessment deliverables, I developed a Python script that parses Nessus and Advanced IP Scanner HTML exports and automatically generates presentation ready xlsx files:
1) Asset Inventory.xlsx
2) Threat Catalogue.xlsx
3) Information Security Risk Assessment.xlsx
4) Risk Treatment Plan.xlsx

To validate selected findings beyond automated scanning, I also used Kali Linux to perform internal penetration testing against an isolated target system. This included Nmap host and service enumeration, SMB share and Guest access validation, MAC flooding, DoS ICMP flood testing, and an ARP based man in the middle attack using Ettercap. These activities were used to demonstrate how selected weaknesses could be identified and exploited, while Wireshark packets provided evidence of the resulting traffic and attack behaviour.

---

## Assessment Scope and Workflow

The assessment was performed in a controlled Hyper-V lab environment containing Windows endpoints, Windows Server systems, a domain controller and internal file sharing services. All testing was performed only against systems owned and controlled within the lab environment.

Asset Discovery: Inventory Collection -> Vulnerability Scanning -> Automated Risk Reporting -> Internal Security Pen.Testing -> Recommended Treatment Actions

---

## Lab Architecture

| Machine / System         | Operating System                 | Role                                                                                                                                                                                                            |
| ------------------------ | -------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `VM-AUDIT`          | Windows 10 Pro                      | Main assessment workstation used for Advanced IP Scanner, Total Network Inventory 6, Tenable Nessus Essentials, Python automation, and review of the generated Excel risk assessment reports                    |
| `Kali5`          | Kali Linux                       | Internal penetration testing machine used for SMB access validation, MAC flooding, MITM testing, and Nmap .                                                               |
| `FsAskisi`              | Windows Server 2019      | Primary target for internal penetration testing, including SMB access validation, MAC flooding, MITM testing, and Wireshark packet capture and traffic analysis.                                                                                |
| `Additional Windows VMs` | Windows 10 and Windows Server 2019 | Additional systems used to populate the subnet and provide multiple assets, operating systems, installed software, services, and vulnerability findings for Advanced IP Scanner, TNI, and Nessus scans |


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

Performed a vulnerability basic scan on the subnet: `192.168.10.0/24`. After the scan, I exported from Nessus the below HTML reports:

a) Vulnerabilities report
b) Operating Systems report
c) Software Vulnerabilities report

---

### 5. Python Reporting Automation

Developed a Python script that processes Nessus and Advanced IP Scanner HTML exports and automatically creates GRC style Excels that are ready to present to the organization that the risk assessment is done for.

The script accepts: a Nessus Vulnerabilities HTML report, a Nessus Operating Systems HTML report, an Advanced IP Scanner HTML report and it generates 4 Excel files: 
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

Examples of identified services: 

SMB - TCP/445 

RDP - TCP/3389

WinRM - TCP/5985 

LDAP - TCP/389 

LDAPS - TCP/636 

Kerberos - TCP/88 and TCP/464 and HTTP services.

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
sudo macof -i eth0
```

The command generated Ethernet frames with changing source MAC addresses.

d) Observed and captured the resulting ARP broadcasts and abnormal incoming in Wireshark on `FsAskisi`.

The MAC flooding test was performed to demonstrate how an internal attacker could generate large volumes of Ethernet traffic with changing source MAC addresses and create abnormal  activity in the network. This highlights the importance of implementing switch level controls such as limiting the number of MAC addresses allowed per port, allowing only approved MAC addresses where appropriate, monitoring abnormal MAC address activity through IDS or network monitoring, and using VLAN segmentation to reduce the impact of unauthorised internal traffic.

----

### 9. DoS Attack Demonstration

A controlled ICMP Flood Denial of Service attack was performed from the Kali Linux VM `kali5` against `FsAskisi` to assess how excessive ICMP traffic could affect a host and the network availability.

Steps followed to demonstrate the attack: 

a) From the Kali Linux VM, ran the following command against the isolated target `FsAskisi`:
```bash
sudo hping3 -1 --flood 192.168.10.99
```
b) Started Wireshark on `FsAskisi` and applied an ICMP filter to capture incoming Echo Request traffic
and observed a sustained stream of ICMP Echo Request packets arriving.

The ICMP flood test was performed to demonstrate how an attacker could generate a large volume of ICMP traffic toward a target system and potentially affect its availability by consuming network or host resources. This highlights the importance of limiting unnecessary ICMP traffic through firewall rules, applying ICMP rate limiting to prevent excessive traffic, and monitoring the network for unusually high ICMP traffic levels.

---

### 10. Man in the Middle (MITM) Attack Demonstration

The final penetration testing activity was a controlled man in the middle attack against `FsAskisi` using Ettercap from the `kali5` VM.

Steps followed to demonstrate the attack: 

a) Opened Ettercap on the Kali Linux VM and selected the eth0 network interface.

b) Performed host discovery to identify systems available on the network.

c) Selected ` 192.168.10.99 = FsAskisi ` as the target system.

d) Configured the target and initiated an ARP based man in the middle attack.

e) On the target VM, opened Command Prompt and ran:
```cmd
arp -a
``` 
f) Verified that the default gateway IP address was associated with the MAC address of the `kali5` VM.

The ARP table on the target VM showed that the gateway IP address had been associated with the Kali Linux VM’s MAC address. This confirmed that ARP spoofing had altered the target’s local ARP mapping and that traffic intended for the gateway could be redirected through the Kali Linux VM that has the attacker role.

The MITM attack demonstrated how ARP spoofing can redirect internal traffic through an attacker-controlled system. This highlights the importance of using HTTPS instead of HTTP, keeping TLS certificates valid and up to date, and avoiding self signed certificates for services where trusted certificates can be used. These controls help protect traffic from interception even if an attacker gains a man in the middle position on the network.

---

## Project Purpose

The purpose of the automated Excel reports and internal penetration testing activities was to give the organisation a structured overview of its security posture within the agreed assessment scope. The assessment identifies the assets present in the environment, the vulnerabilities and security weaknesses affecting them, the services exposed to internal users, and selected ways in which those weaknesses could be exploited by an attacker.

The Excel deliverables translate technical findings into prioritised risks and the penetration testing activities provide practical evidence of how selected weaknesses, such as weak SMB access controls, ARP spoofing exposure, or excessive network traffic, could affect confidentiality, integrity, or availability. Together, these outputs help the organisation understand what is wrong, how selected weaknesses could be abused from within the network, and which security controls or remediation actions should be considered to strengthen the environment.

---

## Screenshots

![Active Directory Lab Architecture](images/logo3.png)

<details>
<summary>🔎 View Full Lab Walkthrough (Screenshots)</summary>

### 1.  Preparing the Windows assessment VM and installing the assessment tools.

![Tools Installation](images/IPSCAN.png)
![Tools Installation](images/TNI.png)
![Tools Installation](images/Tenable.png)

### 2. Identifying the internal subnet and preparing the scan scope.

![VM Creation](images/IP-AUDIT.png)

### 3. Discovering active assets with Advanced IP Scanner.

![Advanced IP Scanner Scan](images/IP-SCAN.png)
![Advanced IP Scanner Export](images/REPORT-ADV-IP.png)
![Advanced IP Scanner Export](images/REPORT-ADV-IP2.png)

### 4. Collecting asset inventory data with Total Network Inventory 6.

![TNI Scan Setup](images/TNI1.png)
![TNI Scan Setup](images/TNI2.png)
![TNI Scan Setup](images/TNI3.png)
![TNI Scan Setup](images/TN4.png)
![TNI Results](images/REPORT-TNI.png)


### 5. Performing authenticated vulnerability scanning with Tenable Nessus.

I FORGOT TO TAKE SCREENSHOTS :)...you can navigate to my other project named: ` VulnerabilityManagement_TenableNessusPro_Lab ` and see a step by step guide on how to create scans in tenable.

For this scan the steps were: New Scan > Basic scan > added the subnet 192.168.19.0/24 as the target and then executed the scan.

After the scan was completed, I exported these 3 html reports: 

Vulnerabilities.html
![Nessus HTML Exports](images/vulne-html.png)
![Nessus HTML Exports](images/vulne-html2.png)

Operating-system.html
![Nessus HTML Exports](images/os-html.png)
![Nessus HTML Exports](images/os-html2.png)

Software-vulnerabilities.html
![Nessus HTML Exports](images/soft-html.png)

### 6. Running the Python automation script to generate Excel risk assessment reports.

First I added the .py code I wrote into the same file with the reports
![Python Script Execution](images/FILE-CODE.png)

Then, into cmd I navigated into the file path and I downloaded the required python packets to execute and generate the xlsx files.
![Python Script Execution](images/CMD-CD.png)
![Python Script Execution](images/CMD1-PACKET.png)
![Python Script Execution](images/CMD2-PACKET.png)

And finally run the command: `py -3.11 excel_parser.py --vulnerabilities "RAKIP_vulnerbilities.html" --os "RAKIP_operating_systems.html" --advip "RAKIP_adv-ip_scan.html" --out-prefix "RAKIP" ` that calls the html reports and generates the new excel files with the information the called files got.
![Python Script Execution](images/CMD-RUNCODE.png)

### 7. Reviewing the generated Excel deliverables.

![Created Excels](images/AFTER-RUN.png)


Asset Inventory Excel

![Asset Inventory Report](images/assetexcel1.png)
![Asset Inventory Report](images/assetexcel2.png)



Threat Catalogue Excel

![Threat Catalogue Report](images/threatexcel1.png)
![Threat Catalogue Report](images/threatexcel2.png)
![Threat Catalogue Report](images/threatexcel3.png)
![Threat Catalogue Report](images/threatexcel4.png)

Risk Treatment Excel 

![Risk Treatment Plan](images/risktreat1.png)
![Risk Treatment Plan](images/risktreat2.png)
![Risk Treatment Plan](images/risktreat3.png)
![Risk Treatment Plan](images/risktreat4.png)


Information Security Risk Assessment Excel
![Risk Assessment Report](images/riskexcel1.png)
![Risk Assessment Report](images/riskexcel2.png)
![Risk Assessment Report](images/riskexcel3.png)
![Risk Assessment Report](images/riskexcel4.png)
![Risk Assessment Report](images/riskexcel5.png)
![Risk Assessment Report](images/riskexcel6.png)

### 8. Performing internal host discovery and service enumeration with Kali Linux and creating a .txt file to save those informations.

![KALI-VM-IP](images/kali1.png)
![kali-linux](images/kali2.png)
![kali-linux](images/kali3.png)
![kali-linux](images/kali4.png)
![kali-linux](images/kali5.png)

### 9. Creating a second .txt to save service informations only about the 5 VMs of my network so I can do the pen testing on them.

![KALI-VM-IP](images/kali6.png)
![KALI-VM-IP](images/servicestxt.png)
![KALI-VM-IP](images/scantxt.png)

### 10. Chekcing where the SMB service that uses port 445 is open.

![SMB](images/smb1.png)
![SMB](images/smb2.png)
![SMB](images/smb3.png)

### 11. SMB Enumeration and Anonymous Access Validation.

First, SMB exposure and anonymous access behaviour were checked across multiple internal hosts.
![SMB](images/smbinfo.png)


![SMB](images/smb-5checks.png)

After reviewing the results, I focused the SMB testing on `FsAskisi` = `192.168.10.99`.
![SMB](images/smb-enu-anony.png)

### 12. Validating SMB access and Guest share permissions.

![SMB](images/smb-accessed.png)
![SMB](images/smb-accessed2.png)

The SMB testing confirmed that the Guest account could access shared files on `FsAskisi` = `192.168.10.99` . Using Guest credentials, I was able to list the share contents, retrieve the `shared.txt` file, and read it successfully from the Kali Linux VM. This confirmed unauthorised read access to data exposed through the SMB share.

### 13. Demonstrating MAC flooding and capturing traffic in Wireshark.

Searching for the MAC Address of the `FsAskisi` = `192.168.10.99` machine.
![MAC Flooding](images/macadd-vm.png)

Before starting the MAC attack I had to enable MAC Spoofing on my KALI-VM Hyper-V virtual machine.
![MAC Flooding](images/enabling-MAC-spoofing.png)

Launching the MAC attack.

![MAC Flooding](images/mac1.png)
![MAC Flooding](images/mac2.png)

Wireshark was opened on the target VM, `FsAskisi` = `192.168.10.99`, during the MAC flooding test. The capture showed a rapid stream of incoming Layer2 traffic with changing source MAC addresses, confirming that the generated traffic reached the target system.

![MAC Flooding](images/mac-wireshark.png)
  
### 14. Demonstrating an DoS ICMP flood attack.

The ICMP flood test was performed from the Kali Linux VM using Hping3 against `FsAskisi` = `192.168.10.99`. Wireshark was running on the target VM during the test and captured a sustained stream of ICMP Echo Request packets, confirming that the high volume ICMP traffic reached the target system.

![ICMP Flood Command](images/normal-DoS.png)

You can use --flood to make the attack more powerfull and quick: 

![ICMP Flood Command](images/DoS-flooging-start.png)

Wireshark results:

![ICMP Flood Wireshark Capture](images/wireshark-DoS-results.png)


Stopping the attack:

![ICMP Flood Command](images/DoS-flooding-end.png)

### 15. MITM attack with Ettercap.

![Ettercap MITM Setup](images/ett1.png)

![Ettercap MITM Setup](images/ett2-searchhost.png)

![Ettercap MITM Setup](images/ett3-searchhost.png)

![Ettercap MITM Setup](images/ett4-hostmenu.png)

![Ettercap MITM Setup](images/ett5a-addtarget55.png)

![Ettercap MITM Setup](images/ett5b-chooseattack.png)


![Ettercap MITM Setup](images/ett6-attack.png)

The default gateway of the attacked VM `FsAskisi` = `192.168.10.99` is now attached to the MAC of our Kali Linux VM.

![Ettercap MITM Setup](images/kali-info.png)
![Ettercap MITM Setup](images/arp-a-mitm-results.png)



</details>

---

## Commands Used

- Internal Host Discovery

Performed a ping scan across the subnet to identify live hosts without conducting port scans. The results were saved to `host-discovery.txt`.

```bash
sudo nmap -sn 192.168.10.0/24 -oN host-discovery.txt
```

- Servce enumeration on the 5 hosts.

This command performed service detection and operating system detection against the 5 selected hosts. The results were saved to `service_scan_targets.txt`.

```bash
sudo nmap -sV -sC -O 192.168.10.99 192.168.10.100 192.168.10.101 192.168.10.105 192.168.10.147 -oN service_scan_targets.txt
```

- Identyfying systems exposing SMB on TCP/445.

After completing the targeted service enumeration, I reviewed the saved service-scan-target.txt results to identify systems exposing SMB on TCP/445. I used grep to filter the scan output for port 445, which helped me quickly identify the hosts that were suitable for further SMB security validation.

```bash
grep -B 3 -A 10 "445/tcp" services-scan-target.txt
```

- SMB Service Check

Identified basic SMB configuration details such as the hostname, domain, SMB signing status, and SMBv1 support on the `FsAskisi` host.

```bash
nxc smb 192.168.10.99
```

- Anonymous SMB Share Enumeration
  
Attempted to list SMB shares on `FsAskisi` without supplying credentials, to assess whether anonymous share enumeration was permitted.

```bash
smbclient -L //192.168.10.99 -N
```

- Guest SMB Access Validation
  
Tested whether the Guest account could access SMB shares and identified permissions available to that account.

```bash 
nxc smb 192.168.10.99 -u '.\guest' -p '' --shares
```

- Accessing the Exposed SMB Shared files

Connected to the accessible `file1hares` SMB share using the Guest account to validate whether files could be listed and retrieved.

```bash
smbclient -U 'guest' //192.168.10.99/fileshares
```

- Retrieved File Review

Displayed the contents of the retrieved test file to confirm successful read access from the SMB share.

```bash
cat shared.txt
```

- kali5 VM Network informations

Verified the Kali Linux VM network interface, local IP address, default gateway and routing table. `kali5` was assigned `192.168.10.163/24` in the isolated `192.168.10.0/24` lab subnet.

```bash
ip a
ip route
```

- Target Reachability

Confirmed that `FsAsksis` = `192.168.10.99` was reachable and identified exposed services before the starting the pen testing security tests.

```bash
sudo nmap -Pn 192.168.10.99
```

- MAC Flooding Attack

Generated high volume Ethernet traffic with changing source MAC addresses from the `kali5` .

```bash
sudo macof -i eth0
```

- DoS ICMP Attack

Generated a sustained high rate ICMP Echo Request stream from the `kali5` towards `FsAskisi`

```bash
sudo hping3 -1 --flood 192.168.10.99
```

- ARP Table Check

Executed on `FsAskisi` after the MITM ARP attack to review the local ARP table and verify whether the gateway IP to MAC address mapping had changed.

```bash
arp -a
```
---

## How to Run the Python Automation code

- Prerequisites

Install Python 3.11 or later, then install the required packages:

```bash
py -3.11 -m pip install pandas beautifulsoup4 lxml openpyxl

```
- Run the Script

Place the Nessus HTML report exports in the same folder as the script. Replace the example filenames below with the names of your own exported HTML files, then run:

```bash
py -3.11 excel_parser_v12.py --vulnerabilities "RAKIP_vulnerabilities.html" --os "RAKIP_operating_systems.html" --advip "RAKIP_adv-ip_scan.html" --out-prefix "RAKIP"
```


## Tools & Technologies

- Advanced IP Scanner 
- Total Network Inventory
- Tenable Nessus
- Kali Linux
- Nmap
- Wireshark
- Pyhton

---

## Security Concepts Demonstrated

- Asset Discovery and Inventory Management
- Vulnerability Scanning
- Internal Network and Service Enumeration
- Vulnerability Prioritisation using CVSS 
- Python Automation to Convert Nessus HTML Exports into presentation ready Excel Risk Assessment Reports
- Risk Assessment and Risk Treatment Planning
- Penetration Testing: SMB Access Validation, MAC Flooding, DoS ICMP Flooding, and MITM
- Wireshark packet capture and Network Traffic Analysis

---

## Insights & Lessons Learned

- Using Advanced IP Scanner showed me how asset discovery provides the first visibility into what systems are active within an internal network.
- Using Total Network Inventory helped me understand the value of inventory collection for identifying operating systems, installed software, users, services, and update information across multiple systems.
- Performing the Nessus scans showed how vulnerability scanners can identify missing patches, exposed services, software vulnerabilities, and configuration weaknesses.
- Reviewing CVSS scores and Nessus findings helped me understand how technical vulnerabilities can be prioritised based on severity and potential impact.
- Developing Python automation to process Nessus HTML reports showed how repetitive security reporting tasks can be converted into structured Excel deliverables for easier review and presentation.
- Performing Nmap host discovery and service enumeration demonstrated how an attacker or security assessor can identify exposed services such as SMB, RDP, WinRM, LDAP etc.
- Testing SMB access showed that an open service is not automatically insecure, but weak Guest permissions and shared folder access can expose internal data.
- Performing MAC flooding demonstrated how abnormal traffic can be generated inside a network and why switch level controls and network monitoring are important.
- Performing an DoS ICMP flood test showed how a large volume of traffic can be directed toward a host and potentially affect availability by consuming network or system resources.
- The MITM test demonstrated how ARP spoofing can alter local ARP mappings and redirect internal traffic through an attackers controlled system.
- Using Wireshark during the attack simulations helped me understand how packet captures can provide evidence of abnormal traffic and activity.

  
---

## Copyright Notice

All content and visuals in this repository are original and may not be reused without permission.


## Rakip 

ICT Engineering | Cybersecurity & Network Security

---
