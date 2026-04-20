# Active Directory Home Lab: Enterprise Domain Deployment & Security Implementation

## 📑 Table of Contents

- [Purpose](#purpose)
- [Project Overview](#project-overview)
- [Architecture](#architecture)
- [Features](#features)
- [Technologies Used](#technologies-used)
- [Process](#process)
- [What I Learned](#what-i-learned)
- [Future Enhancements](#future-enhancements)
  
## 🎯 Purpose

The purpose of this AD project is to create a simulated business environment for testing purposes

## 🧭 Project Overview

This project has the initial setup of users in a business environment as well as setting or restricting access based on the needs of each department and team. It is meant to streamline activation and implementation of necessary and needed features for each user to match their intended workflows

## 🏗️ Architecture

![AD Lab Diagram](Diagrams/ad-lab-diagram.png)

## 🔒 Features & Security Controls

- Create organizational units with security groups for new business users  
- Restrict access to CMD to prevent users from improperly accessing system files or running malicious code  
- Disable Control Panel for standard users to avoid system security and network changes  
- Install basic web browsing software automatically for new users  
- Map network drives to provide access to shared files and folders for specific workstations and security groups  
- Create role-based access for shared files and folders  
- Set a default password policy to prevent repeated login attempts  
- Implement password expiration and renewal

## 🛠️ Technologies Used

- Windows Server 2022 (Domain Controller)
- Windows 10 Iso Client VM
- Hyper-V virtualization
- Domain: lab.local

## ⚙️ Process

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

Here a static IP was set on the AD server so the workstation can connect to it at the same location and the DNS record is stable

![AD Install](./Images/AD-install-6.png)

After AD was installed, the server was promoted to a DC

![DC Install](./Images/DC-config-1.png)

Next a client VM with windows 10 was deployed to test GPOs and set with preferred DNS as server IP to join it to the domain

![Client Install](./Images/client-install-1.png)

Here the echo command was run to confirm the client VM was connected to the domain by returning the domain value stored on Windows

![Client Install](./Images/client-install-2.png)

Back on the DC, the default domain policy and domain controller are enabled

![DC Install](./Images/DC-config-5.png)

Next 3 OUs were created for Sales, IT, and HR departments, with one security group, and one user assigned to each

![DC Install](./Images/DC-config-2.png)

Sales received Sales_Users, IT received IT_Standard_Users, and HR HR_Users as security groups

![DC Install](./Images/DC-config-3.png)

One user was added to each security group

![DC Install](./Images/DC-config-4.png)

Here is both default domain policy and domain controller enabled

![DC Install](./Images/DC-config-5.png)

For IT standard users security group, a GPO was added to block control panel access by enabling the policy with the GPO first

![CP GPO](./Images/ControlPanel-GPO-1.png)

Initially the policy did not apply on the client VM because it only had IT_Standard_Users that allowed apply only access. The GPO had to be edited to include authenticated users with read permission to allow anyone who logs into the domain on the workstation to interpret the policy first.

![CP GPO](./Images/ControlPanel-GPO-2.png)

Unfortunately, the GPO did not apply successfully until gpupdate /force was run and gpresult /r after to confirm the result

![CP GPO](./Images/ControlPanel-GPO-3.png)

![CP GPO](./Images/ControlPanel-GPO-4.png)

The same process was followed to add a GPO to disable CMD for HR_Users security group by adding authenticated users and then running gpupdate /force and gpresult /r on the client VM

![CMD GPO](./Images/CMD-GPO-1.png)

Again, authenticated users and HR_Users needed read permission to read and apply the new GPO

![CMD GPO](./Images/CMD-GPO-2.png)

Now cmd is no longer accessible on the client VM

![CMD GPO](./Images/CMD-GPO-3.png)

For Sales_Users security group, a GPO was added to schedule an automatic install of Chrome on the client VM. This used a shared folder on the DC with domain computers security group set on it to allow access to the installer during the boot process on the client. Originally, domain users was added to the security for the shared Software folder but that was quickly removed as that only allowed a manual install once the Windows was logged in and not automatic.

![Chrome GPO](./Images/Chrome-install-1.png)

Upon checking programs after reboot, Chrome was successfully installed automatically

![Chrome GPO](./Images/Chrome-install-2.png)

Similarly, a mapped drive was added to setup RBAC between the different OUs and security groups using GPOs

![RBAC GPO](./Images/RBAC-install-1.png)

Each OU received its own folder with a  specific security group assigned that had modify and read access. This allowed only the specific OU security group to access the folder while denying others.

![RBAC GPO](./Images/RBAC-install-2.png)

As part of troubleshooting inheritance from the parent folder had to be converted to allow permissions to be edited on the OU specific folder to remove other security groups that did not need access

![RBAC GPO](./Images/RBAC-install-3.png)

Now the network drive (Z:) with the specific OU folder was accessible by users in the specific Sales security group and not others

![RBAC GPO](./Images/RBAC-install-4.png)

Here the sales user could not access the IT departments folder so it is working as designed

![RBAC GPO](./Images/RBAC-install-5.png)

Finally, password policy was set on the default domain for all security groups. This included a 30 day password expiration timer but also a lockout threshold of 5 invalid login attempts etc.

![PW GPO](./Images/Password-config-1.png)

One was a password policy for setting a time to renew the password by the user and another to lock the user out if they made too many failed login attempts

![PW GPO](./Images/Password-config-2.png)

After gpupdate /force and rebooting the DC and workstation, the new policy went into effect for all OU security groups

![PW GPO](./Images/Password-config-3.png)

Now the user is locked out after 5 failed attempts

![PW GPO](./Images/Password-config-4.png)

## 🧠 What I Learned

- The scope of GPO is determined by security filtering — both Authenticated Users and specific security groups are required for proper application  
- Inheritance must be removed or modified to prevent unintended access from additional security groups on shared folders  
- The **Default Domain Policy** controls organization-wide password settings unless overridden by **Fine-Grained Password Policies (FGPP)**  
- The **Domain Computers** group must be added to shared folders to allow software installations during the boot process (before user logon)  
- Role-Based Access Control (RBAC) does not require explicitly denying other groups — simply granting access to the correct security group is sufficient  
- Mapped drives follow users across machines, but **NTFS permissions** ultimately control access to files and folders  
- Security configurations should be applied at the **organizational unit, group, and user level**, since different groups may require different levels of access

## 🚀 Future Enhancements

- FGPP could have been implemented for specific security groups that overrides default domain password policy. It, however, would require specific requirements definition for the group that needed it such as temporary users.

- A PowerShell script could have been added to automatically test implementations such as network drive access, software installs, domain join etc. This could be executed when  the user logins in to verify the GPO applied correctly such as running a gpupdate /force in cmd.
