🛡️ Sprint 11: SIEM Deployment & Defense Validation (Purple Teaming)
Objective: To deploy a centralized Wazuh SIEM infrastructure, integrate high-fidelity host-based logging via Sysmon, and validate detection capabilities using Atomic Red Team simulation frameworks.

🛠️ Technical Implementation
SIEM Infrastructure: Deployed and configured a Wazuh manager to collect, parse, and visualize security events from across the network.

Agent Deployment & Log Forwarding: * Deployed Wazuh agents across Windows (AD01) and Linux endpoints.

Integrated Sysmon (System Monitor) to capture high-fidelity telemetry, including Process Creation (Event ID 1), Network Connections (ID 3), and File Creation (ID 11).

Configured log forwarders to ensure raw event data was correctly parsed into searchable fields within the SIEM.

Continuous Monitoring: Utilized the MITRE ATT&CK module within Wazuh to map incoming alerts to known adversary tactics and techniques.

🧪 Adversary Simulation & Detection Validation
To ensure the SIEM was "catching the stealthy stuff," I conducted three hands-on experiments using the Atomic Red Team (ART) framework:

Reconnaissance (T1047): Executed WMI commands to gather local user information. Validated that Wazuh successfully alerted on suspicious WMI process activity.

Defense Evasion (T1564.001): Simulated an attacker hiding files using the attrib.exe utility. Confirmed that Process Command Line Auditing captured the specific flags used to hide the file.

Lateral Movement (T1021.004): Tested SSH remote services on the Domain Controller. Validated detection through Windows Security Event ID 4624 (Successful Logon) and Wazuh's built-in rulesets.

📄 Project Deliverables
SIEM_Deployment_&_Validation_Blog.md: A comprehensive technical blog post titled "Moving Beyond Theory," detailing the setup, the "why" behind Sysmon, and the results of the Atomic Red Team experiments.

Sprint_11_Project_Plan.pdf: The formal proposal outlining the deployment steps and experimental methodology approved by senior analysts.

🧰 Tools & Technologies
SIEM/SOAR: Wazuh, Graylog.

Endpoint Telemetry: Sysmon (Windows/Linux), Windows Event Logs.

Simulation Tools: Atomic Red Team (PowerShell-based execution).

Frameworks: MITRE ATT&CK, NIST Continuous Monitoring.

Operating Systems: Windows Server (Active Directory), Linux (Ubuntu/Debian).
