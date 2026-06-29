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

The script accepts: A Nessus Vulnerabilities HTML report, a Nessus Operating Systems HTML report and a Advanced IP Scanner HTML report.

and it generates four Excel files: 

| Deliverable                          | Purpose                                                        |
| ------------------------------------ | -------------------------------------------------------------- |
| Asset Inventory                      | Lists discovered assets and classifies asset types             |
| Threat Catalogue                     | Lists actionable vulnerability findings by severity            |
| Information Security Risk Assessment | Converts findings into risk scores and ratings                 |
| Risk Treatment Plan                  | Provides recommended treatment actions and reassessment fields |


---

### 6. 

