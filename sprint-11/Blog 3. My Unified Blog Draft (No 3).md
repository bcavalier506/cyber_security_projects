



## 1. Introduction: Moving Beyond Theory 💡

I’m a security enthusiast just starting my journey in this field. I'm fascinated by the constant puzzle of attack and defense, and I've decided to enter cybersecurity to learn how to build resilient systems. This is my very first contribution to the community, and I truly hope that somebody out there finds this experiment fun, interesting, or at least useful for hardening their own environments.

Welcome to my blog! As a security enthusiast, I've learned that a system is only as good as its weakest log, and a SIEM is only useful if it's set up to catch the stealthy stuff. That's what this series is all about: moving beyond theory and running hands-on experiments to **validate our cybersecurity defenses**. My primary target was our domain controller, **AD01**. I used **Atomic Red Team** to simulate three distinct stages of a real-world attack—reconnaissance, defense evasion, and lateral movement—and used the **Wazuh SIEM** to see if our systems would actually fire the necessary high-severity alerts. Can we really catch an attacker hiding files with a simple command, or using legitimate administrative tools for persistence? The results of this journey were eye-opening. I’ll share my key takeaways in the final section, focusing on the critical differences between **just collecting logs** and **effectively detecting threats**. Spoiler alert: **Process Command Line Auditing** is non-negotiable for defense.

---

## 2. Setup: Bringing AD01 Under Surveillance 🕵️

The entire premise of this experiment—testing detection capabilities—required one critical, foundational step: integrating the domain controller, **AD01**, into our Security Information and Event Management (SIEM) system. Without the logs from **AD01** streaming into the Wazuh Manager, the entire exercise is blind.

My primary task for this setup phase was to get the **Wazuh Agent** successfully deployed and communicating. Instead of a manual install, I used the **Wazuh Manager's Deployment Command** on the **AD01** server to ensure a silent, automated setup.

### The Automated Agent Deployment

I accessed **AD01** and opened a **PowerShell** prompt. I then ran the highly efficient, one-line command provided by the Wazuh dashboard. This command was a packed script that handled downloading, installing, and configuring the agent all at once:

PowerShell

```
# (This is a conceptual breakdown of the deployment command run in PowerShell)
Invoke-WebRequest -Uri <DOWNLOAD_URL> -OutFile ${env:tmp}\wazuh-agent.msi; msiexec.exe /i ${env:tmp}\wazuh-agent.msi /q WAZUH_MANAGER='10.170.0.99' WAZUH_AGENT_GROUP='default,Windows_Servers' WAZUH_AGENT_NAME='ad01'
```

### The Inevitable Oversight: A Simple Typo 🤦

While the command itself was flawless, I made a simple, but crucial, error that cost me a few minutes: **I forgot to start the service immediately after the install!**

After the `msiexec` command finished, I immediately returned to the Wazuh Manager console, expecting to see **ad01** show up as **Active**. Instead, it was stuck in a state of `Never connected`. I quickly realized my oversight: the installer sets up the service, but it doesn't always kick it off.

The fix was trivial, but it highlighted the need to follow every step precisely. I went back to the PowerShell prompt on **AD01** and ran the final activation command:

PowerShell

```
NET START WazuhSvc
```

With the service running, the agent instantly connected to the manager and **ad01** appeared with an **Active** status. This single, successful deployment of the agent was the foundation upon which all three of our upcoming Atomic Tests would be measured.

---

## 3. Experiment Time! Validating Detection in Action 💥

My process for each experiment was consistent: **Prepare** the remote PowerShell session, **Execute** the atomic test via `Invoke-AtomicTest`, **Analyze** the local logs on **AD01**, and finally, **Verify** the resulting alert in the Wazuh SIEM.

### 🔬 Experiment #1: WMI Reconnaissance (T1047)

This test simulated an attacker using **Windows Management Instrumentation (WMI)**, a native administrative tool, for reconnaissance.

- **Tactic/Technique:** **T1047: Windows Management Instrumentation**
    
- **Goal:** Enumerate local user accounts to gather intelligence.
    
- **Command Executed (via ART):** `wmic useraccount get /ALL /format:csv`
    

#### Log Artifacts

The detection here confirmed the value of deep logging.

- **Local Log Evidence (AD01):** A Sysmon **Process Creation Event (ID 1)** was generated on the domain controller.
    
- **Wazuh Alert Evidence:** The SIEM successfully correlated the event to a detection rule. Crucially, the log detail captured the full command line: **`wmic useraccount get /ALL /format:csv`**.
    
- **Key Indicator:** The rule triggered because the **`wmic.exe`** binary was executed with specific system-enumeration keywords like **`useraccount`** and **`get`** in the command line arguments.
    

### 🔬 Experiment #2: Defense Evasion with Hidden Files (T1564.001)

This technique simulated the adversary concealing a created file using the native Windows `attrib.exe` utility.

- **Tactic/Technique:** **T1564.001: Hidden Files and Directories**
    
- **Goal:** Set the hidden file attribute (`+h`) on a temporary file to evade discovery.
    
- **Command Executed (via ART):** `attrib.exe +h T1564_001.txt`
    

#### The All-Too-Common Oversight 🤦

I initially ran the test and found that the SIEM was completely silent. After checking the target host, I realized the test never executed because I made a fundamental operational mistake:

- **The Mistake:** I forgot to define the required remote PowerShell session variable, **`$sess`**, by running the command: `New-PSSession -ComputerName 10.170.0.10 -Credential administrator`.
    
- **The Fix:** I ran the `New-PSSession` command to establish the remote connection context, and immediately upon re-running the `Invoke-AtomicTest` command, the logs began to flow.
    

#### Log Artifacts

With the session fixed, the detection was successful:

- **Local Log Evidence (AD01):** The Sysmon log confirmed the execution of **`ATTRIB.EXE`**.
    
- **Wazuh Alert Evidence:** The resulting Wazuh alert provided the definitive detection. The command line captured was **`attrib.exe +h C:\Users\ADMINI~1\AppData\Local\Temp\T1564.001.txt`**.
    
- **Key Indicator:** The rule triggered specifically on the presence of **`attrib.exe`** combined with the defense evasion flag **`+h`**, confirming our ability to catch the adversary's intent.
    

### 🔬 Experiment #3: Remote Services SSH (T1021.004)

This final test validated the detection of lateral movement by establishing a successful, authenticated remote connection via SSH to the domain controller.

- **Tactic/Technique:** **T1021.004: Remote Services: SSH**
    
- **Goal:** Successfully authenticate to the target using a valid account for lateral movement.
    
- **Command Executed (via ART):** Simulated a successful SSH logon event.
    

#### Log Artifacts

This detection relies on foundational authentication logging.

- **Local Log Evidence (AD01):** A **Windows Security Event ID 4624** (An account was successfully logged on) was generated.
    
- **Wazuh Alert Evidence:** The SIEM triggered an alert (Rule ID **60106**) mapped to **T1078: Valid Accounts**.
    
- **Key Indicator:** The detection is based on the **successful authentication** of a valid user account and the associated **Logon Type 10 (RemoteInteractive)**, which signifies that the session was established remotely.
    

---

## 4. Tuning and Takeaways

### 4.1 Summary of Experimental Findings

The goal of these experiments was to validate that our SIEM deployment on the **ad01** Domain Controller (DC) was not just collecting logs, but was creating **actionable alerts** mapped to the MITRE ATT&CK framework. The results confirmed our detection capabilities but also exposed an initial configuration gap.

**T1047: WMI Reconnaissance**

- **Detection Confirmed:** Yes. The SIEM successfully generated an alert mapped to **T1047**.
    
- **Key Log Evidence:** The alert contained the **full command line** (`wmic useraccount get /ALL /format:csv`), proving that the necessary **Process Command Line Auditing** was functioning correctly (after initial tuning, if needed).
    

**T1564.001: Hidden Files and Directories**

- **Detection Confirmed:** Yes. The SIEM alerted on the attempt to hide a file.
    
- **Key Log Evidence:** The detection hinged on capturing **`attrib.exe`** execution along with the defense evasion flag **`+h`** in the command line. This directly validated our ability to spot an adversary using native tools for concealment.
    

**T1021.004: Remote Services SSH**

- **Detection Confirmed:** Yes. The simulated SSH access was logged and alerted upon.
    
- **Key Log Evidence:** The alert was triggered by a **Successful Logon Event (Windows Security Event ID 4624)**, validating the essential control of logging lateral movement associated with **T1078: Valid Accounts**.
    

The primary takeaway from the detection phase is that while Wazuh has solid out-of-the-box rules, **forensic quality depends entirely on the verbosity of the endpoint's logging configuration.**

### 4.2 Advice on Avoiding Mistakes

The errors encountered during this project were not complex technical bugs but fundamental workflow oversights. Avoiding these saves valuable time and prevents a false negative result (where the attack ran but the SIEM didn't see it).

1. **Validate the Attack Execution First:** Before wasting time troubleshooting the SIEM, **always confirm that the attack ran successfully on the target host**. My mistake of forgetting the **`$sess`** variable meant the `Invoke-AtomicTest` command was effectively running into a void. If the target host's Event Viewer is quiet, the SIEM will be quiet. The command to check the session is `$sess`.
    
2. **Verify Command Line Auditing:** Do not assume your Windows endpoints are logging command lines by default. You must **explicitly enable "Include command line in process creation events"** via Group Policy. Without this single tuning step, WMI, PowerShell, and _any_ native execution technique will be a **blind spot**.
    
3. **Mind the Timezone Mismatch:** When correlating events between the local host (like **ad01**) and the centralized SIEM (like Wazuh), remember that the SIEM often **normalizes all timestamps to UTC**. If you're looking for an event that happened at 10:00 AM EST, filter your SIEM search for 2:00 PM UTC. Focus on matching the minutes and seconds before fixing the hour.
    
4. **Check Service Status:** After deploying any agent, always run a local check (e.g., `NET START WazuhSvc` or `Get-Service -Name WazuhSvc` on Windows) to confirm the service is running and **not just installed**. The installation step doesn't always automatically start the service.
    

---

## 5. Final Thoughts: Lessons from the Blue Team Trenches 🛡️

### 5.1 The Coolest Thing I Learned

The coolest thing I learned was how much detection capability relies on a single, often-overlooked Windows log source: **Sysmon Event ID 1 (Process Create)**, particularly when collecting its full command line. It felt like acquiring X-ray vision. Without Sysmon, the **T1047 WMI** test would have been almost invisible, just a process running. With it, the SIEM saw the **specific query** (`wmic useraccount get /ALL /format/csv`) that revealed the attacker's intent to gather intelligence. This realization completely shifted my focus from collecting _more_ logs to collecting _smarter_ logs.

### 5.2 One Piece of Advice

My single piece of advice for any engineer is to **pay obsessive attention to detail when writing commands.** My mistake of forgetting the **`$sess`** variable meant the attack test never even ran on the target host. That one missed detail led to fifteen frustrating minutes of troubleshooting a "silent SIEM" that wasn't even receiving the attack logs. It confirms the basic truth: whether you're defining a remote session, setting a firewall rule, or writing a search query, a single missing character or misplaced flag will break the pipeline. Slow down, double-check your syntax, and make sure your command is going where you think it is.

### 5.3 My Favorite Resource

My favorite resource, by far, was the **Atomic Red Team (ART) project from Red Canary.** As a blue teamer focused on detection, the biggest challenge is safely and reliably replicating adversary behaviors. ART provided a standardized, pre-built library of attack commands that map directly to the MITRE ATT&CK framework. The ability to simply run `Invoke-AtomicTest T1564.001` and know exactly which technique was being executed made the entire validation and log-tracing process efficient, measurable, and highly educational. It’s the ultimate bridge between threat intelligence and detection engineering.

### 5.4 Thank You (Gratitudes)!

Huge thanks go out to the platform environment for providing the controlled lab space—it was invaluable for safely executing these tests without risk. Gratitude also to the developers and maintainers of **Wazuh** and **Atomic Red Team** for creating powerful, accessible open-source tools. Finally, a big thank you to my teammates and mentors **Anna De Jesus, Hamza, and Ali** for their support and for sharing insights that helped me troubleshoot the entire detection pipeline. This project truly solidified the core concepts of detection validation.

---

## 6. Resources and References 📚

This annotated list includes key references that were essential for setting up, validating, and troubleshooting the security controls and SIEM platform used throughout this project. These resources are highly recommended for anyone looking to execute similar detection validation exercises.

- **Author/Organization:** Red Canary
    
    - **Date Published:** N/A
        
    - **Title of Resource:** **Atomic Red Team**
        
    - **Annotation:** This was the primary tool for executing and simulating adversary tactics (TTPs). It provided the exact commands for testing WMI (**T1047**) and Hidden Files (**T1564.001**) directly on the target host, which was critical for validation.
        
- **Author/Organization:** MITRE
    
    - **Date Published:** N/A
        
    - **Title of Resource:** **ATT&CK Matrix for Enterprise**
        
    - **Annotation:** This was the foundational reference for mapping our alerts. We used it to ensure the Wazuh rules correctly identified the simulated attacks and assigned the proper technique ID.
        
- **Author/Organization:** Wazuh Documentation
    
    - **Date Published:** N/A
        
    - **Title of Resource:** **Wazuh Agent Installation Guide (Windows)**
        
    - **Annotation:** Essential for the initial setup, specifically for troubleshooting the agent deployment command and ensuring the service was correctly started and communicating with the Manager.
        
- **Author/Organization:** Microsoft
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Advanced Security Audit Policy Settings**
        
    - **Annotation:** Critical for the **Tuning** phase. This resource detailed the exact Group Policy settings required to enable **Process Creation Auditing** and, most importantly, the **command line inclusion** feature.
        
- **Author/Organization:** MITRE
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Technique T1047: Windows Management Instrumentation**
        
    - **Annotation:** Provided forensic context for the WMI test, detailing what system utilities (like `wmic.exe`) an attacker uses and what command arguments we should expect to see in our logs.
        
- **Author/Organization:** SwiftOnSecurity
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Sysmon Modular Configuration**
        
    - **Annotation:** A crucial community resource that guided the initial deployment of **Sysmon** and helped ensure we were logging the highest fidelity events, particularly **Event ID 1 (Process Create)**.
        
- **Author/Organization:** Microsoft
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Security Event ID 4624 (An account was successfully logged on)**
        
    - **Annotation:** Used to validate **T1021.004 (SSH Remote Services)** detection. It confirmed the logon was successful and helped us identify the specific **Logon Type** associated with remote access.
        
- **Author/Organization:** Wazuh Documentation
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Wazuh Ruleset: Windows Security Events**
        
    - **Annotation:** A reference for understanding the default rule IDs (e.g., **60106**) that triggered during the SSH test, allowing us to confirm the SIEM's built-in capability for detecting successful Windows logons.
        
- **Author/Organization:** Red Canary Blog
    
    - **Date Published:** Varies
        
    - **Title of Resource:** **Atomic Red Team Documentation on PowerShell Execution**
        
    - **Annotation:** Provided specific instructions and caveats regarding remote execution using `Invoke-AtomicTest -Session $sess`, which was directly relevant to troubleshooting the **missing `$sess` variable mistake** I encountered.