# Detection-Lab

## Objective

The purpose of creating the Detection Lab project was to establish a controlled environment for simulating and detecting cyberattacks. Its core objective was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, enabling the generation of test telemetry that mimics real-world attack scenarios.

### Skills Learned

- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used

- Splunk - Security Information and Event Management (SIEM) system for log ingestion and analysis.
- pfSense Firewall – Firewall and traffic management system that forwards logs to Splunk for analysis.
- Zeek & Suricata - Network security tools for traffic analysis and intrusion detection, forwarding logs and alerts to Splunk for analysis.
- Active Directory - Domain controller managing user accounts, authentication, and configuring audit policies, while sending security logs to Splunk.
- Kali Linux & Atomic Red Team - Telemetry generation tools to create realistic network traffic and attack scenarios.

### Nework Diragram 

<img width="790" height="511" alt="image" src="https://github.com/user-attachments/assets/2b3c5e92-a0fd-4603-a8c8-868e827d9704" />


1. Firewall (pfSense)
2. Windows 10 Host (for endpoint testing)
3. Windows Server (Active Directory Domain Controller)
4. Zeek and Suricata (network monitoring)
5. Splunk (SIEM and log analysis)
6. Attacker Host (Kali Linux)

## Step 1 : Onboarding pfSense Firewall
<img width="720" height="285" alt="image" src="https://github.com/user-attachments/assets/8986b1ca-8d3d-498c-a01f-e93683a876d5" />

I have installed the pfSense firewall and configured two network adapters: one for WAN and one for LAN.

I also installed the Squid Proxy on the pfSense firewall for log transfer

<img width="960" height="523" alt="image" src="https://github.com/user-attachments/assets/2eb69dde-f027-4466-a269-7818b90a492a" />
 
 ### Note
 
 - WAN will be External and LAN will be Internal.

## Step 2 : Setting Up the Splunk Host and Zeek-Suricurta Host

After installing Splunk, I created an indexer called myohset-detect for log ingestion and monitoring.

<img width="537" height="141" alt="image" src="https://github.com/user-attachments/assets/bb6036f2-78c3-4614-b0be-7d6d1386823a" />


Since I have installed Splunk on the Ubuntu Server, I assign an IP address to our Splunk machine.

• 	Host IP: 192.168.1.20

• 	Default Gateway: 192.168.1.1 (pfSense firewall LAN)

<img width="576" height="263" alt="image" src="https://github.com/user-attachments/assets/0af13bc1-a993-4883-8fb7-3cd1925e3584" />

The Same Goal for the Zeek-Suricata Host : 

• 	Host IP: 192.168.1.30

• 	Default Gateway: 192.168.1.1 (pfSense firewall LAN)

<img width="881" height="277" alt="image" src="https://github.com/user-attachments/assets/59516992-bbe1-484a-852e-70cf2fcc126a" />


## Step 3 : Seting Up the Windows Server

I have installed the Windowserver and pormoted to Active Directory Domain Controller and assigned a static IP configuration as follows:

• 	Host IP: 192.168.1.10

• 	Default Gateway: 192.168.1.1 (pfSense firewall LAN)

<img width="405" height="493" alt="image" src="https://github.com/user-attachments/assets/b171f6e4-4739-4d90-a2d8-db685168b24e" />


Then I created a user account for our Windows host.

<img width="761" height="531" alt="image" src="https://github.com/user-attachments/assets/3a31add4-1239-4dcb-9900-4aa26262b684" />


I have installed Windows 10 on a virtual machine and assigned a static IP configuration as follows:

• 	Host IP: 192.168.1.100

• 	Default Gateway: 192.168.1.1 (PfSense firewall LAN)

<img width="396" height="452" alt="image" src="https://github.com/user-attachments/assets/c0862e9e-1a36-40da-ac61-0433a9515aab" />


Then I joined the AD DC domain and logged in with the user account I created.

<img width="1025" height="766" alt="image" src="https://github.com/user-attachments/assets/876c355d-c334-41ea-aba7-38619f535153" />

**After That I have Configure the Logging Policies on Group Policy Object and Base on Microsoft’s baseline recommendations.** and installed SYSMON.

-Create a GPO and name it "Audit Policy – Endpoint"

<img width="1016" height="435" alt="image" src="https://github.com/user-attachments/assets/3a4e1512-e5a2-4da0-90cb-1a309c9cd1d5" />


<img width="756" height="875" alt="image" src="https://github.com/user-attachments/assets/3618dfcc-3b69-4dfa-a459-ec02b660202b" />


After completing all the setup, I installed Splunk Forwarder on the Windows 10 machine and the Windows Server.

Here is the configuration file:

<img width="751" height="511" alt="image" src="https://github.com/user-attachments/assets/93467671-8f0f-4102-b81c-d3ed90bf6cff" />

### Step 4 : - Installing the Splunk Universal Forwarder on Zeek-Suricata, and pfSense.

I installed the Splunk Forwarder on Zeek, Suricata, and pfSense, and configured them to forward logs to my Splunk server.

Here are the configuration files: 

- **Zeek-Suricata**
<img width="614" height="295" alt="image" src="https://github.com/user-attachments/assets/906f3c84-df38-4087-89be-ef3afe30a993" />

- **pfSense**
<img width="740" height="192" alt="PFsense" src="https://github.com/user-attachments/assets/33f31fe6-b8e6-4340-9cc1-f966a353c865" />


To ensure that the logs have been ingested into Splunk, go to Splunk and verify the logs sourcetype.

<img width="1006" height="451" alt="image" src="https://github.com/user-attachments/assets/9e56b983-6e97-42ab-9e77-da43179312c7" />

### The Last Step : Generating Telemetry

I onboard the kali Linux and join the my internal network. And creaeted Malware file call **Invoixes.doc.exe** 

- I have download **Invoixes.doc.exe** on my windows host and run it. Now we got the logs on my splunk .
##### Result
<img width="1021" height="728" alt="Screenshot 2025-11-28 152815" src="https://github.com/user-attachments/assets/e55da36c-ad49-474b-8fa9-3e204ec95766" />

- I ran an Nmap scan on my internal network to generate telemetry for detection.
<img width="578" height="211" alt="image" src="https://github.com/user-attachments/assets/e485bb35-5a21-4824-8193-f7ad40532db9" />

##### Reuslt

<img width="1017" height="390" alt="Nmap tele" src="https://github.com/user-attachments/assets/0e32c966-7c56-41e5-9f18-756b5bc762b5" />

- I test‑ran Atomic Red Team technique T1136.001, which creates a new local account.
<img width="1025" height="767" alt="Atomic red team" src="https://github.com/user-attachments/assets/a0c3d326-12e4-48e6-94be-93a854e6be70" />

##### Result

<img width="996" height="646" alt="Atomic Redteam" src="https://github.com/user-attachments/assets/817d23a1-cd1a-47b0-adfb-50f89c5d2120" />


## Summary

By creating this project, we can demonstrate how logs are transferred, how the tools operate, and how they interact with each other. It also highlights the importance of having visibility and generating alerts in the SIEM.











 
