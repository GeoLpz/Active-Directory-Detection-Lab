# Active-Directory-Detection-Lab

## Objective

The Active Directory Detection Lab project aimed to simulate a real-world brute-force attack against a Windows Active Directory environment and detect it using Splunk, demonstrating end-to-end log creation, event correlation, and SIEM analysis in a controlled lab setting. 

### Skills Learned

- Practical experience with SIEM tools, using Splunk to detect and analyze security events 
- Configured and managed a Windows Active Directory environment in a virtual lab
- Analyzed Windows Security Logs (Event ID 4625) to identify failed login attempts
- Simulated real-world attack scenarios using Hydra and xfreerdp
- Strengthened troubleshooting and problem-solving skills in networked environments

### Tools Used

- Splunk for centralized log ingestion, event correlation, and alerting 
- Windows Server for Active Directory setup, domain control, and event logging
- Kali Linux for attack simulation and security testing
- Hydra for automated RDP brute-force attempts
- xfreerdp for remote desktop protocol testing and access validation
- VirtualBox for creating and managing isolated virtual environments 

## Steps

Here’s a breakdown of the key steps I took to build, configure, and test the Active Directory detection lab.


### Lab Overview / Architecture 
![Screenshot 2025-07-05 222213](https://github.com/user-attachments/assets/5997363d-604e-4ee6-babb-dd63f4e078c6) 


Ref 1: Diagram illustrates the virtual lab environment used to simulate and detect Active Directory attacks. All systems are hosted in VirtualBox and configured on the same NAT Network (192.168.10.0/24) for internal communication. 

### 🛠️ Network Configuration

![Screenshot 2025-07-05 221636](https://github.com/user-attachments/assets/663bd352-c149-45e7-a5da-3e5f5eb217b8)

Ref 2: All machines (Kali Linux, Target Machine, Windows Server, and a Splunk Server) are running in VirtualBox. 


![Screenshot 2025-07-05 221115](https://github.com/user-attachments/assets/0c2c9b2b-8c5f-40a9-bbbe-a675334d326e)


Ref 3: Configured all VMs on the same NAT Network (AD-Project) to simulate a real AD environment with internal lateral movement and monitoring. 


### ⚙️ Splunk Server Configuration

### 1. Static IP Configuration

![Screenshot 2025-07-07 205538](https://github.com/user-attachments/assets/1ef8c14e-9305-4327-a311-d786386f177c)

Ref 4: Used Netplan to assign static IP address to the Splunk server for consistent log forwarding.

### 2. Splunk Installation and Startup

![Screenshot 2025-07-07 205830](https://github.com/user-attachments/assets/979f42f6-279c-4f82-a4d0-7b561db549c0)

Ref 5: Navigated to Splunk install directory and started the service manually using `./splunk start`.

### 3. Enable Boot-Time Startup

![Screenshot 2025-07-07 210023](https://github.com/user-attachments/assets/1b469c33-71c2-4f51-a04b-4dd58165fca8)

Ref 6: Configured Splunk to run automatically on system reboot using the `enable boot-start` command.


### 🧩 Endpoint Configuration (Windows 10 & Domain Controller)

Both the Windows Server (ADDC01) and Target Machine (TARGET-PC) were configured similarly to enable reliable log forwarding to the Splunk server. 

### 1. Static IP Configuration 
![Screenshot 2025-07-07 220221](https://github.com/user-attachments/assets/d4ad0dbc-6329-4634-a593-c2cf818510fb)

Ref 7: Set a static IP on both machines so they could reliably talk to each other on the network. The DNS points to the domain controller to allow proper domain communication.
       Note: IP differs between machines (e.g., `192.168.10.100` on target, `192.168.10.7` on AD server).
### 2. Splunk Universal Forwarder Setup
![Screenshot 2025-07-07 220421](https://github.com/user-attachments/assets/279a5522-261d-482e-ad73-e1e2ea078e21)

Ref 8: Installed the Splunk Universal Forwarder and pointed it to the Splunk server so it knows where to send log data.

### 3. Sysmon Installation
![Screenshot 2025-07-07 223004](https://github.com/user-attachments/assets/9f6d5856-a7f8-4dfb-83fb-e9266a4e2fa8)

Ref 9: Installed Sysmon using a custom config to collect detailed system activity like process creation and network connections.

### 4. inputs.conf Configuration 
![Screenshot 2025-07-08 202808](https://github.com/user-attachments/assets/59da2894-97ed-43d6-a570-943d86b34356)

Ref 10: Edited the inputs.conf file to tell the forwarder which Windows logs to send to Splunk — including Application, Security, System, and Sysmon logs.

### 5. Host Verification in Splunk
![Screenshot 2025-07-08 203138](https://github.com/user-attachments/assets/0cae8d4c-2bf6-47e8-abbd-ff49413bb2d5)

Ref 11: Verified that both machines ('ADDC01' and 'TARGET-PC') were successfully sending logs to Splunk by checking the 'index=endpoint' search.

### 6. Source Events Confirmation
![Screenshot 2025-07-08 203347](https://github.com/user-attachments/assets/a8589457-3b48-4a78-9f8e-026acf3040c7)

Ref 12: Confirmed that logs from all the right places (Sysmon, Security, System, and Application) were being picked up and indexed in Splunk.

### 🏗️ Active Directory Setup & Domain Configuration
This section shows the setup of a Windows Server as a Domain Controller, the creation of a test user and organizational units (OUs), and successfully joining a Windows 10 machine to the new domain.

### 1. Promote Server to Domain Controller

![Screenshot 2025-07-08 214757](https://github.com/user-attachments/assets/71991bf0-07b0-4064-8655-ebd2a4110b7a)

Ref 13: Configured ADDC01 as a Domain Controller and created a new forest with the root domain `adproject.local`.

### 2. Created Organizational Units and Users

![Screenshot 2025-07-08 215702](https://github.com/user-attachments/assets/ad371332-d0e1-4cd3-9c50-91a63867d36f)

Ref 14: Created two test OUs (HR and IT) and added a sample user, Jenny Smith, to the IT OU.

### 3. Join Machine to the Domain

![Screenshot 2025-07-08 220251](https://github.com/user-attachments/assets/e0b65356-997f-48c4-9300-b488a8da68d3)

Ref 15: Successfully joined the Windows 10 machine (`target-PC`) to the domain `adproject.local`.

### 4. Log In as Domain User

![Screenshot 2025-07-08 220612](https://github.com/user-attachments/assets/b1df544b-0b71-455f-b677-2c17d2b73205)

Ref 16: Verified domain connectivity by logging in as Jenny Smith on the domain-joined machine. 

### 🔐 Brute Force Attack Detection

### 1. RDP Access Enabled

![Screenshot 2025-07-09 210954](https://github.com/user-attachments/assets/7463851e-9777-4be8-b757-1f02cd8a326b)

Ref 17: Turned on Remote Desktop and allowed domain users (`jsmith`, `tsmith`) to connect.

### 2. Hydra Attack Launched 

![Screenshot 2025-07-10 112429](https://github.com/user-attachments/assets/35d8d9c2-8a3c-4f4a-bb6a-d97769dff9a6)

Ref 18: Ran Hydra from Kali to brute-force the `jsmith` account. It found the correct password (`Password1!`). On the right, Splunk logs show multiple failed logins (Event ID 4625), flagging the attack activity.

### 3. Login Success from Kali

![Screenshot 2025-07-09 221844](https://github.com/user-attachments/assets/fd0c752a-d9af-44ae-9de2-bc2d3411e8f8)

Ref 19: Used `xfreerdp` with the cracked password to log in. Splunk confirmed it with a successful Event ID 4624 — showing it came from the Kali box (`192.168.10.250`).

