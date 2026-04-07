Sprint 02: Network Reconnaissance & Mapping
Objective: To perform active reconnaissance on a target network to identify live hosts, available services, and potential attack surfaces.

🔍 Technical Tasks & Skills
Host Discovery: Used nmap -sn to identify active endpoints within a specified subnet without alerting security systems with full port scans.

Service & Version Detection: Executed -sV commands to determine specific versions of running services (e.g., Apache, OpenSSH), which is critical for vulnerability mapping.

OS Fingerprinting: Utilized the -O flag to identify the underlying Operating Systems of target machines.

Command Optimization: Compared the speed and "noise" levels of different scan types, including TCP Connect (-sT) vs. Stealth/SYN Scans (-sS).

Port Analysis: Identified common open ports (80, 443, 22) and analyzed the security implications of non-standard ports found open.

🛠️ Tools Used
Nmap: The primary tool for all network mapping and enumeration.

CLI / Linux Terminal: For executing complex flag combinations and piping output to files.

📄 Deliverables
Network_Scan_Report.pdf: A detailed walkthrough of the scanning process and findings.

Nmap_Output_Analysis.csv: A structured list of identified IPs, ports, and services for easy documentation.
