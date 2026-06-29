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


## Lab Architecture

The lab was built in **Hyper-V** and consisted of an assessment workstation, a Kali Linux testing VM, and multiple Windows systems connected to the same isolated internal virtual network:

| Machine / System | Operating System            | Role                                                                                                                                                            |
| ---------------- | --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `VM-AUDIT`  | Windows 10  Pro               | Main risk assessment workstation used for Advanced IP Scanner, Total Network Inventory 6, Tenable Nessus Essentials, Python automation, and Excel report review |
| `Kali Linux VM`  | Kali Linux                  | Internal penetration testing VM used for host discovery, Nmap enumeration, SMB validation, MAC flooding, MITM testing                     |
| `FSASKISI2`      | Windows Server 2019 VM | Intentionally vulnerable test target used for SMB Guest-access validation, shared-folder testing, and traffic capture                                           |
| `DCASKISI`       | Windows Server 2019         | Active Directory Domain Controller exposing domain services such as LDAP, Kerberos, DNS, SMB, RDP, and WinRM                                                    |
| `WinAsk10`       | Windows 10 Pro              | Internal endpoint used for service enumeration, SMB configuration assessment, and vulnerability scanning                                                        |
| `dvls-server`    | Windows Server 2019           | Internal server used for authenticated vulnerability scanning and service enumeration                                                                           |
| `VM-ADMIN-RDM`   | Windows VM                  | Internal endpoint used for SMB, WinRM, and general service-enumeration assessment                                                                               |

---
## What I Did

Created a Windows 10 virtual machine in Hyper-V to use as the main assessment workstation.
