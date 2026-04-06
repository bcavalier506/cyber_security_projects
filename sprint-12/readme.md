🕵️‍♂️ Sprint 12: SOC Operations & Incident Triage
Objective: To master the security alert lifecycle by performing high-pressure triage, investigation, and reporting. This project involved analyzing a real-world credential-stuffing attack, utilizing Wireshark and Splunk to determine the blast zone, and drafting formal escalation reports.

🛠️ The Analytical Workflow
Investigation Theory: Applied "Diagnostic Inquiry" to move beyond surface-level alerts. Used pivoting techniques to link disparate data points (IPs, User-Agents, and Timestamps) into a cohesive attack narrative.

Alert Triage & Lifecycle: Managed the full alert lifecycle from initial review to disposition.

Distinguished between True Positives (TP), False Positives (FP), and Benign True Positives (BTP).

Performed severity assessments to prioritize the triage queue effectively.

Forensic Traffic Analysis: Conducted a deep-dive investigation into two PCAP files using Wireshark.

Tool Identification: Identified the use of the Hydra brute-force utility via User-Agent string analysis (Mozilla/5.0 (Hydra)).

Success Verification: Distinguished between failed authentication attempts and successful compromises by filtering for HTTP 302 Found responses.

SIEM Correlation (Splunk): Utilized Splunk and Search Processing Language (SPL) to corroborate network findings with host logs, mapping the timeline of the attack from 15:42:22 to 15:48:41 UTC.

🧪 Incident Case Study: Credential Stuffing
The Incident: A critical brute-force attack against a WordPress login portal originating from 192.168.100.20.

The Impact: Confirmed the exposure of plain-text credentials for multiple accounts, including a high-privilege administrative account.

Remediation Strategy: Proposed immediate enforcement of HTTPS, implementation of Multi-Factor Authentication (MFA), and the creation of custom SIEM rules to alert on rapid POST requests to /wp-login.php.

📄 Project Deliverables
IR_Triage_Report_MegaQuagga.pdf: A formal technical report documenting the "True Positive" disposition, impact assessment, and recommended hardening actions for the SOC.

Incident_Investigation_Notes.pdf: Detailed forensic notes mapping specific packet numbers (e.g., Packet #9809 for admin compromise) and TCP streams to the attack timeline.

🧰 Tools & Technologies
SIEM: Splunk (SPL, Boolean operators, Field piping).

Traffic Analysis: Wireshark (TCP Stream following, HTTP response filtering).

Frameworks: NIST Incident Response Lifecycle, SOC Tiering Models.

Security Concepts: Credential Stuffing, Brute-Force Mitigation, User-Agent Profiling, Blast Zone Mapping.
