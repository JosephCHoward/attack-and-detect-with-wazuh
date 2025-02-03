# Attack and Detect with Wazuh

## Objective
Understanding attacks and being able to detect them are important skills for SOC analysts. In this home lab, I created a virtual enterprise enviornment, introduced vulnerable configurations, performed a multi-phase end-to-end attack, and identified specific activity with detection rules and alerts created in Wazuh. 

This project was created by Grant Collins and can be accessed for free <a href = "https://projectsecurity.teachable.com/p/build-a-cybersecurity-homelab-a-practical-guide-to-offense-defense-enterprise-101"> here</a>. 

## Skills Involved
- Computer Networking
- Firewall Configurations
-	Active Directory
-	Attack Tactics: reconnaissance, phishing, brute force, lateral movement, privilege escalation, data exfiltration, persistence mechanisms
-	Security Monitoring with Security Onion & Wazuh

## Main Steps

### 1. Build an Enterprise Environment

The first phase of the project involved building an enterprise environment using VirtualBox. 

![image](https://github.com/user-attachments/assets/ea6cbbd3-2607-456d-8f86-7cdce9c23117)

As shown in the topology above, the email and server and 3 PC's were connected to the Active Directory domain controller.

![image](https://github.com/user-attachments/assets/2f5a47d5-973b-44c3-9e8e-e0c152757ede)

### 2. Introduce Vulnerable Configurations
In order to conduct the end-to-end attack, a number of vulnerabile configurations were introduced into the environment. 
- Install and/or enable SSH (port 22) and RDP (port 3389) on relevant machines.
- Install and enable Linux firewall on relevant machines.
- Allow relevant services and ports (22 & 3389) on firewalls.
- Create an intentionally weak password on the email server to create susceptiblity to brute force attack.

### 3. Create Detection Rules in Wazuh
In order to detect activities from the end-to-end attack, three rules and alerts were created in Wazuh.
1. Rule to detect RDP logins.
    Windows does not have an event ID specifically for WinRM so a rule was created to detect authentication via Kerberos (which WinRM uses for remote connections) and successful logins.       Specifically, two filters were used:
   - data.win.eventdata.logonProcessName is Kerberos
   - data.win.system.eventID is 4624
   
3. Rule to monitor access of sensitive data (file integrity monitoring). This rule used two filters:
    -  full_log contains secrets.txt (the file containing the "sensitive" data)
    -  syscheck.event is modified
   
5. Rule to detect 3 failed SSH login attempts. This rule used two filters:
   -  decoder.name is sshd
   -  rule.groups contains authentication_failed

![image](https://github.com/user-attachments/assets/89a0f68b-6adb-4c72-a56f-57d7430707d9)

### 4. Conduct End-to-End Attack

Using Kali Linux as the attacker machine, an end-to-end multi-phase attack was conducted involving a number of tactics. Some of the main steps and tactics are presented below.

Initial reconnaissance began with using nmap to scan ports on the machine at 10.0.0.8: 
![image](https://github.com/user-attachments/assets/cf6366d9-da77-414a-bdd3-d7edb82fb91c)


