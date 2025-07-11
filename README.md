# Active Directory Detection Lab

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

<img width="388" height="494" alt="Image" src="https://github.com/user-attachments/assets/1a81c6e6-7683-4fd6-8d91-6ae377d7a57e" />

Ref 1: Diagram illustrates the virtual lab environment used to simulate and detect Active Directory attacks. All systems are hosted in VirtualBox and configured on the same NAT Network (192.168.10.0/24) for internal communication. 

### 🛠️ Network Configuration

All virtual machines were placed on the same NAT network in VirtualBox to ensure they could communicate internally. This setup allowed for proper log forwarding, simulated attacks, and domain operations.

### 1. Virtual Machines in VirtualBox
<img width="958" height="549" alt="Image" src="https://github.com/user-attachments/assets/daee662a-8ef9-410b-9200-a3f61262a5d4" />

Ref 2: All machines (Kali Linux, Target Machine, Windows Server, and a Splunk Server) are running in VirtualBox. 

### 2. NAT Network Setup

<img width="767" height="471" alt="Image" src="https://github.com/user-attachments/assets/56677e1f-8c2c-4d2b-a5fd-707f870ffc0d" />

Ref 3: Configured all VMs on the same NAT Network (AD-Project) to simulate a real AD environment with internal lateral movement and monitoring. 

### ⚙️ Splunk Server Configuration

This section covers setting up the Splunk server, including assigning a static IP, installing the software, and configuring it to start on boot—ensuring it's ready to receive and index logs throughout the lab.

### 1. Static IP Configuration

<img width="743" height="200" alt="Image" src="https://github.com/user-attachments/assets/8eb4e8ab-4856-4e56-a56c-49d9eef8906e" />

Ref 4: Used Netplan to assign static IP address to the Splunk server for consistent log forwarding.

### 2. Splunk Installation and Startup

<img width="781" height="427" alt="Image" src="https://github.com/user-attachments/assets/eef09357-2519-41b6-842f-ddef145222ec" />

Ref 5: Navigated to Splunk install directory and started the service manually using `./splunk start`.

### 3. Enable Boot-Time Startup

![Screenshot 2025-07-07 210023](https://github.com/user-attachments/assets/1b469c33-71c2-4f51-a04b-4dd58165fca8)

Ref 6: Configured Splunk to run automatically on system reboot using the `enable boot-start` command.


### 🧩 Endpoint Configuration (Windows 10 & Domain Controller)

Both the Windows Server (ADDC01) and Target Machine (TARGET-PC) were configured similarly to enable reliable log forwarding to the Splunk server. 

### 1. Static IP Configuration 

<img width="395" height="452" alt="Image" src="https://github.com/user-attachments/assets/257b70a4-651f-449a-b4c2-f47c657cabec" />

Ref 7: Set a static IP on both machines so they could reliably talk to each other on the network. The DNS points to the domain controller to allow proper domain communication.
       Note: IP differs between machines (e.g., `192.168.10.100` on target, `192.168.10.7` on AD server).

### 2. Splunk Universal Forwarder Setup

<img width="410" height="346" alt="Image" src="https://github.com/user-attachments/assets/6ee4d57b-ed86-45ce-ab63-9a7dd29da809" />

Ref 8: Installed the Splunk Universal Forwarder and pointed it to the Splunk server so it knows where to send log data.

### 3. Sysmon Installation
<img width="856" height="324" alt="Image" src="https://github.com/user-attachments/assets/04142723-b3c2-4b46-a578-0a7403d8d46f" />

Ref 9: Installed Sysmon using a custom config to collect detailed system activity like process creation and network connections.

### 4. inputs.conf Configuration 
<img width="748" height="515" alt="Image" src="https://github.com/user-attachments/assets/46245684-a6b5-401e-83c4-5dc14d7a21ce" />

Ref 10: Edited the inputs.conf file to tell the forwarder which Windows logs to send to Splunk — including Application, Security, System, and Sysmon logs.

### 5. Host Verification in Splunk
<img width="985" height="696" alt="Image" src="https://github.com/user-attachments/assets/1a65c975-a0c4-47f3-8dbd-e1c927245fda" />

Ref 11: Verified that both machines ('ADDC01' and 'TARGET-PC') were successfully sending logs to Splunk by checking the 'index=endpoint' search.

### 6. Source Events Confirmation
<img width="985" height="693" alt="Image" src="https://github.com/user-attachments/assets/a11672f8-83e0-4e2e-8f8d-976359fb610a" />

Ref 12: Confirmed that logs from all the right places (Sysmon, Security, System, and Application) were being picked up and indexed in Splunk.

### 🏗️ Active Directory Setup & Domain Configuration
This section shows the setup of a Windows Server as a Domain Controller, the creation of a test user and organizational units (OUs), and successfully joining a Windows 10 machine to the new domain.

### 1. Promote Server to Domain Controller

<img width="1022" height="484" alt="Image" src="https://github.com/user-attachments/assets/c35a62c2-6a14-451d-a98b-2d3bb95e4fad" />

Ref 13: Configured ADDC01 as a Domain Controller and created a new forest with the root domain `adproject.local`.

### 2. Created Organizational Units and Users

<img width="750" height="499" alt="Image" src="https://github.com/user-attachments/assets/8b6d841d-f4ce-449e-a760-5b33c855fee8" />

Ref 14: Created two test OUs (HR and IT) and added a sample user, Jenny Smith, to the IT OU.

### 3. Join Machine to the Domain

<img width="523" height="463" alt="Image" src="https://github.com/user-attachments/assets/f172ee45-98b5-4b9a-909b-bc104bec7554" />

Ref 15: Successfully joined the Windows 10 machine (`target-PC`) to the domain `adproject.local`.

### 4. Log In as Domain User

<img width="1026" height="638" alt="Image" src="https://github.com/user-attachments/assets/d08209e4-3456-4869-9091-ff687d9f94e5" />

Ref 16: Verified domain connectivity by logging in as Jenny Smith on the domain-joined machine. 

### 🔐 Brute Force Attack Detection

### 1. RDP Access Enabled

<img width="764" height="458" alt="Image" src="https://github.com/user-attachments/assets/9bb83fc3-749c-403d-8156-4e56f909f05d" />

Ref 17: Turned on Remote Desktop and allowed domain users (`jsmith`, `tsmith`) to connect.

### 2. Hydra Attack Launched 

<img width="1629" height="825" alt="Image" src="https://github.com/user-attachments/assets/fdcc2d5d-8c37-4127-9a43-63b86ab6b519" />

Ref 18: Ran Hydra from Kali to brute-force the `jsmith` account. It found the correct password (`Password1!`). On the right, Splunk logs show multiple failed logins (Event ID 4625), flagging the attack activity.

### 3. Login Success from Kali

<img width="1372" height="665" alt="Image" src="https://github.com/user-attachments/assets/75a4cebf-2a85-402c-ba68-260b1502fde0" />

Ref 19: Used `xfreerdp` with the cracked password to log in. Splunk confirmed it with a successful Event ID 4624 — showing it came from the Kali box (`192.168.10.250`).

### 💣 Atomic Red Team: Simulated Attacks & Detection

To simulate common attack behaviors and validate log visibility in Splunk, I ran a few MITRE ATT&CK-based tests using Atomic Red Team.

### 1. Simulated Account Creation (T1136.001)

<img width="1751" height="717" alt="Image" src="https://github.com/user-attachments/assets/a2ecce2c-d55b-4e36-84d2-ab4dc53a7486" />


Ref 20: Ran test T1136.001, which created a new local user (`NewLocalUser`) using multiple methods. The action was picked up in Splunk under `EventCode=4726`, confirming the detection of a suspicious user creation.

### 2. Detected Script Execution Bypass (T1059.001)

<img width="1671" height="710" alt="Image" src="https://github.com/user-attachments/assets/485b01ef-6044-4423-a2c2-0a54c873b743" />

Ref 21: Executed T1059.001 to simulate a PowerShell execution bypass using `-exec bypass -noprofile`. Windows Security flagged the behavior, and Splunk successfully logged the process creation with the corresponding command-line arguments.

## 🧠 Closing Thoughts

This project gave me hands-on experience building a real-world Active Directory environment from the ground up and simulating attack scenarios in a controlled lab. I learned how to:

- Set up and manage a Windows domain with realistic users, OUs, and group policies  
- Forward logs from multiple endpoints and interpret them using Splunk  
- Detect brute-force attacks and script-based threats using real-world tools like Hydra and Atomic Red Team  
- Troubleshoot networking issues, system configs, and log forwarding across multiple machines  
- Correlate logs using Event IDs and spot suspicious activity through Splunk search queries  

Beyond the technical stuff, this project helped me really connect the dots between offense and defense. I got to see how attackers move and how to catch those moves using tools and methods that are actually used in the field.




