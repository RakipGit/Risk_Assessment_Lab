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

Asset Discovery: Inventory Collection -> Vulnerability Scanning -> Automated Risk Reporting -> Internal Security pen Testing
