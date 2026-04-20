Purpose:

The purpose of this AD project is to create a simulated business environment for testing purposes

Project Overview:

This project has the initial setup of users in a business environment as well as setting or restricting access based on the needs of each department and team. It is meant to streamline activation and implementation of necessary and needed features for each user to match their intended workflows

Design:

![AD Lab Diagram](Diagrams/ad-lab-diagram.png)

Features:

Create organizational units with security groups for new business users
Restrict access to CMD to prevent users improperly accessing system files or running malicious code
Disable control panel for standard users to avoid system security and network changes
Install basic web browsing software automatically for new users to have internet access at the organization
Map network drives to provide access to shared files and folders for specific workstation and security groups
Create role based access for shared files and folders
Set a default password policy to prevent repeated login attempts and implement password expiration/renewal

Environment/Technology:

Windows Server 2022 (Domain Controller)
Windows 10 Iso Client VM
Hyper-V virtualization
Domain: lab.local

Process:

This is the initial setup of the AD VM using generation 2 for secure boot and UEFI firmware to prevent malicious code from running with only digitally signed software and faster startup.

![AD Install](./Images/AD-install-1.png)

Since this project was only limited to 3 OUs and user groups, 2gb was used as a minimum to handle basic lookups and searches


![AD Install](./Images/AD-install-2.png)

127 GB is the max size hyper-v allows but disk size was intended to allow installing Windows, the DC, and adding any additional third party software and files for sharing with workstations

![AD Install](./Images/AD-install-3.png)

Standard server 2022 was selected for basic DC features and to avoid deploying unusable ones such as cluster control etc.

![AD Install](./Images/AD-install-4.png)

Here the AD DC role is being installed on the newly deployed AD VM WIN-3RJET1E4128

![AD Install](./Images/AD-install-5.png)

Here a static IP was set on the AD server so the workstation can connect to it at the same location and the DNS record is correct

![AD Install](./Images/AD-install-6.png)

After AD was installed, the server was promoted to a DC

![DC Install](./Images/DC-config-1.png)

Next a client VM with windows 10 was deployed to test GPOs and set with preferred DNS as server IP to join it to the domain

![Client Install](./Images/client-install-1.png)

Here the echo command was run to confirm the client VM was connected to the domain by returning the domain value stored on Windows

![Client Install](./Images/client-install-2.png)

Back on the DC, OUs were created for 3 departments including Sales, IT, and HR with one security group and one user assigned to each

![DC Install](./Images/DC-config-2.png)

![DC Install](./Images/DC-config-3.png)

![DC Install](./Images/DC-config-4.png)

Here is both default domain policy and domain controller enabled

![DC Install](./Images/DC-config-5.png)

For IT standard users security group, a GPO was added to block control panel access by enabling the policy with the GPO first

![CP GPO](./Images/DC-config-5.png)<img width="1530" height="1502" alt="image" src="https://github.com/user-attachments/assets/63825cac-1508-498e-9214-8f2a3ee2b69d" />
