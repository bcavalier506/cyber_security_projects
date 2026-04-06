🕵️‍♂️ Sprint 13: Advanced Incident Investigation & Digital Forensics
Objective: To conduct a comprehensive, multi-staged forensic investigation into a complex security breach. This project involved tracing an adversary's path from early reconnaissance and credential stuffing to the final execution of crypto-ransomware.

🛠️ The Investigative Journey
Reconnaissance & Tool Identification: Identified the use of the Acunetix vulnerability scanner by the attacking IP (40.80.148.42) to probe a Joomla CMS for weaknesses.

Credential Stuffing & Compromise: Analyzed brute-force patterns to identify the moment of successful compromise.

Confirmed the breach occurred when the attacker successfully guessed the administrative password ("batman").

Established a timeline between the early request spikes (2016-08-10) and the decisive compromise event (2016-08-24).

Malware Delivery & Steganography: Traced the delivery of a malicious payload obfuscated using steganography.

Identified the malicious image file poisonivy-is-coming-for-you-batman.jpeg.

Linked the infection to a malicious VBScript and the execution of a primary payload named 3791.EXE.

Ransomware Analysis & Impact: * C2 Communication: Identified the malicious FQDN prankglassinebracket.jumpingcrab.com and its associated IP 92.222.104.182.

Blast Zone Mapping: Calculated the impact of the ransomware, which encrypted 257 unique files (including 401 specific format instances) using a cryptor process with PID 3968 (filename: mhtr.jpg).

OSINT & Malware Profiling: Leveraged Open Source Intelligence to correlate file hashes (MD5/SHA256) and WHOIS information to profile the likely APT group and their specific obfuscation techniques.

📄 Project Deliverables
Major_Incident_Triage_Report.pdf: A formal, high-severity report (Alert ID: UR-INT-20250603-0047) documenting the True Positive disposition and the full attack narrative for stakeholders.

Investigation_Forensic_Notes.pdf: 66 pages of detailed investigative evidence, including screenshots of log analysis, file system artifacts, and process tracking.

🧰 Tools & Technologies
Investigation: SIEM Log Analysis, PCAP Forensics, File Integrity Monitoring.

OSINT: VirusTotal, WHOIS, Threat Intel Feeds (for APT profiling).

Concepts: Credential Stuffing (T1110.004), Ransomware (T1486), Steganography (T1027.003), Command and Control (C2).

Analysis: MD5/SHA256 Hashing, Process ID (PID) Tracking, Entropy & Obfuscation Analysis.
