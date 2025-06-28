## Windows Active Directory Lab (On VirtualBox)

This is a self-paced hands-on lab simulating a basic Active Directory (AD) domain setup, built from scratch on VirtualBox. The lab includes the installation and configuration of a Windows Server 2022 domain controller and a Windows 10 client machine, domain join, DNS, shared folder access, user/group creation, and GPO enforcement.

Lab Summary

- Domain Controller (Windows Server 2022)
  - Configured as `DC01.corp.local`
  - Hosts Active Directory, DNS, and DHCP
  - Shared folder mapped via GPO
- Client Machine (Windows 10 Enterprise Evaluation)
  - Hostname: `CLIENT01`
  - Joined to the `corp.local` domain

Features Implemented

- Static IP configuration on DC and client
- Domain setup and user account creation
- Shared folder (`\\DC01\Users\Administrator\Desktop\SharedFolder`)
- Group Policy Object (GPO) to auto-map network drive
- User account moved from `Users` to `Workforce` OU
- GPO link targeted via Organizational Unit

Tools Used

- VirtualBox
- Windows Server 2022 ISO
- Windows 10 ISO
- Group Policy Management Console (GPMC)
- Active Directory Users & Computers
- Command line + GUI

Setup Instructions

Detailed walkthrough and troubleshooting are available in the `NOTES.md`.

Author

Dami Ola 
