☁️ Sprint 14: APT Investigation & Cloud Security (AWS)
Objective: To conduct a massive, multi-year forensic investigation into a sophisticated APT campaign. This project involved correlating endpoint telemetry with AWS CloudTrail logs to uncover a full-chain compromise involving phishing, privilege escalation, cloud resource hijacking, and data exfiltration.

🛠️ The Technical Deep-Dive
Cloud Infrastructure Triage (AWS): Analyzed AWS CloudTrail logs to detect unauthorized IAM activity.

Identified a leaked AWS Access Key used to launch unauthorized Ubuntu instances for Monero (XMR) cryptocurrency mining.

Traced the creation of a publicly accessible S3 bucket used by the adversary for staging exfiltrated data.

Endpoint & Server Compromise: Investigated the breach of a core Linux domain controller ("Hoth").

Privilege Escalation: Identified a Kernel exploit attempt (Ubuntu 16.04.4) where the adversary used gcc to compile malicious binaries on the fly.

Persistence: Discovered unauthorized user creation and the modification of Exchange Transport Rules designed to BCC confidential corporate emails (containing "SOX" and "Confidential" keywords) to an external attacker-controlled address.

Adversary Infrastructure & C2: * Identified long-term Command and Control (C2) beaconing via Netcat (nc) sessions to 45.77.53.176 over port 8088.

Decoded Base64 and Hex-obfuscated command lines from Sysmon logs to reveal the attacker's intent and toolkit.

Phishing & Malware Analysis: Traced the initial entry point to a phishing document delivered to the Finance department.

Verified the execution of a malicious macro that enabled the initial foothold.

Used OSINT to geolocate attack origins and pivot off indicators to identify the ISP and contact info associated with the adversary's staging environment.

📄 Project Deliverables
APT_Cloud_Investigation_Report.pdf: A critical-severity report (Alert ID: UR-AWS-20180820-0038) covering a two-year activity window, documenting the transition from initial access to root-level cloud compromise.

Forensic_Evidence_Manifest.pdf: 114 pages of investigative notes, including Splunk queries, CloudTrail JSON analysis, and process tree visualizations.

Cloud_Log_Analysis_Hoth.csv: A raw data export showing deobfuscated command-line activity from the compromised "Hoth" server.

🧰 Tools & Technologies
Cloud Security: AWS CloudTrail, IAM Policy Analysis, S3 Bucket Auditing.

Digital Forensics: Splunk (Complex correlation), Sysmon, Linux Kernel Exploit Analysis.

Threat Hunting: Atomic Red Team (validation), OSINT (VirusTotal, AlienVault).

Techniques: Resource Hijacking (T1496), Hidden Files (T1564.001), Email Forwarding Rules (T1114.003), Cryptomining.
