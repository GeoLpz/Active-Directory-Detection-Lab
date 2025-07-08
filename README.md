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

Ref 2: All machines (Kali Linux, Target Machine, Windows Server, and a Splunk Server) are running in VirutalBox. 


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


