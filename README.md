# Cybersecurity Detection Lab: SIEM Log Ingestion & Attack Simulation

## Objective

Designed and built a controlled homelab environment to safely simulate and detect cyberattacks. Leveraged SIEM log ingestion and analysis to generate realistic test telemetry, effectively mimicking real-world attack scenarios and mapping them to defensive visibility.

### Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used

- **Splunk:** Security Information and Event Management (SIEM) system for log ingestion and analysis.
- **pfSense Firewall:** Edge firewall and traffic management system configured to forward network syslog events to Splunk.
- **Zeek & Suricata:** Network security tools for traffic analysis and intrusion detection, forwarding logs and alerts to Splunk.
- **Active Directory (Windows Server):** Domain controller managing user accounts, authentication, and configuring audit policies, while sending security logs to Splunk.
- **Kali Linux & Atomic Red Team:** Telemetry generation frameworks used to create realistic network traffic and attack scenarios.

### Network Diagram 

<img width="790" height="511" alt="image" src="https://github.com/user-attachments/assets/2b3c5e92-a0fd-4603-a8c8-868e827d9704" />

1. Firewall (pfSense)
2. Windows 10 Host (for endpoint testing)
3. Windows Server (Active Directory Domain Controller)
4. Zeek and Suricata (network monitoring)
5. Splunk (SIEM and log analysis)
6. Attacker Host (Kali Linux)

---

## Deployment Steps

### Step 1: Onboarding pfSense Firewall
<img width="720" height="285" alt="image" src="https://github.com/user-attachments/assets/8986b1ca-8d3d-498c-a01f-e93683a876d5" />

I installed the pfSense firewall and configured two network adapters: one for WAN (External) and one for LAN (Internal). 

I also installed Squid Proxy on the pfSense firewall to generate web proxy logs for Splunk ingestion.

<img width="960" height="523" alt="image" src="https://github.com/user-attachments/assets/2eb69dde-f027-4466-a269-7818b90a492a" />

### Step 2: Setting Up the Splunk Host and Zeek-Suricata Host

After installing Splunk, I created a dedicated index called `myohset-detect` for log ingestion and monitoring.

<img width="537" height="141" alt="image" src="https://github.com/user-attachments/assets/bb6036f2-78c3-4614-b0be-7d6d1386823a" />

Since I installed Splunk on an Ubuntu Server, I assigned a static IP address mapping to the pfSense LAN:

* **Host IP:** `192.168.1.20`
* **Default Gateway:** `192.168.1.1` (pfSense firewall LAN)

<img width="576" height="263" alt="image" src="https://github.com/user-attachments/assets/0af13bc1-a993-4883-8fb7-3cd1925e3584" />

I applied the same networking logic for the Zeek-Suricata Host: 

* **Host IP:** `192.168.1.30`
* **Default Gateway:** `192.168.1.1` (pfSense firewall LAN)

<img width="881" height="277" alt="image" src="https://github.com/user-attachments/assets/59516992-bbe1-484a-852e-70cf2fcc126a" />

### Step 3: Setting Up the Windows Server & Endpoint

I installed Windows Server, promoted it to an Active Directory Domain Controller, and assigned the following static IP configuration:

* **Host IP:** `192.168.1.10`
* **Default Gateway:** `192.168.1.1` (pfSense firewall LAN)

<img width="405" height="493" alt="image" src="https://github.com/user-attachments/assets/b171f6e4-4739-4d90-a2d8-db685168b24e" />

Then, I created a domain user account for our Windows endpoint.

<img width="761" height="531" alt="image" src="https://github.com/user-attachments/assets/3a31add4-1239-4dcb-9900-4aa26262b684" />

Next, I installed Windows 10 on a virtual machine and assigned its static IP configuration:

* **Host IP:** `192.168.1.100`
* **Default Gateway:** `192.168.1.1` (pfSense firewall LAN)

<img width="396" height="452" alt="image" src="https://github.com/user-attachments/assets/c0862e9e-1a36-40da-ac61-0433a9515aab" />

I joined the Windows 10 machine to the AD domain and logged in with the newly created user account.

<img width="1025" height="766" alt="image" src="https://github.com/user-attachments/assets/876c355d-c334-41ea-aba7-38619f535153" />

**Endpoint Telemetry Configuration:** I configured the Logging Policies via Group Policy Object (GPO) based on Microsoft’s baseline recommendations and installed Sysmon. 
* Created a GPO named: `"Audit Policy – Endpoint"`

<img width="1016" height="435" alt="image" src="https://github.com/user-attachments/assets/3a4e1512-e5a2-4da0-90cb-1a309c9cd1d5" />
<img width="756" height="875" alt="image" src="https://github.com/user-attachments/assets/3618dfcc-3b69-4dfa-a459-ec02b660202b" />

After completing the setup, I installed the Splunk Universal Forwarder on both the Windows 10 machine and the Windows Server. Here is the configuration file:

<img width="751" height="511" alt="image" src="https://github.com/user-attachments/assets/93467671-8f0f-4102-b81c-d3ed90bf6cff" />

### Step 4: Installing the Splunk Universal Forwarder on Zeek-Suricata and pfSense

I installed the Splunk Forwarder on Zeek, Suricata, and pfSense, and configured them to route logs directly to my Splunk server index.

Here are the configuration files: 

* **Zeek-Suricata**
<img width="614" height="295" alt="image" src="https://github.com/user-attachments/assets/906f3c84-df38-4087-89be-ef3afe30a993" />

* **pfSense**
<img width="740" height="192" alt="PFsense" src="https://github.com/user-attachments/assets/33f31fe6-b8e6-4340-9cc1-f966a353c865" />

To ensure that the logs successfully reached the SIEM, I navigated to Splunk and verified the incoming data structures via the `sourcetype` fields.

<img width="1006" height="451" alt="image" src="https://github.com/user-attachments/assets/9e56b983-6e97-42ab-9e77-da43179312c7" />

---

## The Last Step: Generating Telemetry & Attack Simulation

I onboarded the Kali Linux attacker machine and joined it to the internal network to simulate adversary behavior.

### Scenario 1: Malware Payload Execution
I created a masqueraded malware file called `Invoixes.doc.exe`. I transferred it to the Windows host and executed it to generate Sysmon Process Creation (Event ID 1) telemetry.

<img width="1021" height="728" alt="Screenshot 2025-11-28 152815" src="https://github.com/user-attachments/assets/e55da36c-ad49-474b-8fa9-3e204ec95766" />

### Scenario 2: Network Reconnaissance
I ran an Nmap scan from Kali across the internal network to generate network detection telemetry (Suricata alerts and firewall blocks).
<img width="578" height="211" alt="image" src="https://github.com/user-attachments/assets/e485bb35-5a21-4824-8193-f7ad40532db9" />

**Result:**
<img width="1017" height="390" alt="Nmap tele" src="https://github.com/user-attachments/assets/0e32c966-7c56-41e5-9f18-756b5bc762b5" />

### Scenario 3: Persistence (Local Account Creation)
I test-ran Atomic Red Team technique **T1136.001**, which automatically creates a new local account to simulate an attacker establishing persistence.
<img width="1025" height="767" alt="Atomic red team" src="https://github.com/user-attachments/assets/a0c3d326-12e4-48e6-94be-93a854e6be70" />

**Result:**
<img width="996" height="646" alt="Atomic Redteam" src="https://github.com/user-attachments/assets/817d23a1-cd1a-47b0-adfb-50f89c5d2120" />

---

## **Splunk Alert & Detection Queries**

Below are the production-grade Splunk Processing Language (SPL) queries designed to trigger alerts for the malicious activities simulated in this lab.

### 1. Alert: Local User Account Creation (Persistence)
This alert triggers immediately when a new local or domain user account is created, mapping directly to **MITRE ATT&CK T1136 (Create Account)**.

```splunk
index=myohset-detect sourcetype="XmlWinEventLog:Security" EventCode=4720
| rename TargetUserName as Created_Account, SubjectUserName as Creating_Actor
| table _time, Computer, Creating_Actor, Created_Account, TargetDomainName
```

Log Source: Windows Security Event Logs (EventCode=4720)

Key Fields Tracked:

Creating_Actor: The user account/system that executed the creation command (e.g., Administrator or a compromised service account).

Created_Account: The name of the newly established account.

### 2. Alert: Suspicious/Malware Process Execution
This alert triggers when an executable runs using a double extension (e.g., .doc.exe), masquerading as a document, or when it executes out of common user profile directories where malware staging frequently occurs.

```
index=myohset-detect sourcetype="XmlWinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 
| search Image="*.doc.exe" OR CommandLine="*.doc.exe*" OR Image="*Invoixes.doc.exe" OR Image="*\\AppData\\Local\\Temp\\*"
| table _time, Computer, User, Image, CommandLine, ParentImage, ProcessId
```
Log Source: Microsoft Sysmon Operational Logs (EventCode=1 - Process Creation)

Key Fields Tracked:

Image: The full file path of the executed process.

CommandLine: The exact arguments and string used to launch the process (essential for catching obfuscated or renamed binaries).

ParentImage: The parent process that spawned the executable (e.g., seeing cmd.exe or powershell.exe spawn a weird binary is a high-fidelity indicator of compromise).

## **Summary**
This project successfully demonstrates the implementation of an enterprise-grade defense architecture. By bridging network-level visibility (pfSense, Zeek, Suricata) with granular endpoint telemetry (Sysmon, AD GPOs), I established a robust detection pipeline. Simulating real-world attacks via Atomic Red Team highlighted the critical importance of alert validation, log parsing, and the creation of actionable SPL hunting queries within a modern Security Operations Center (SOC).
