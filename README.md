### Windows Active Directory Lab (On VirtualBox)

This is a lab simulating a basic Active Directory (AD) domain setup, built from scratch on VirtualBox. The lab includes the installation and configuration of a Windows Server 2022 domain controller and a Windows 10 client machine, domain join, DNS, shared folder access, user/group creation, and GPO enforcement.

_Lab Summary_

- Domain Controller (Windows Server 2022)
  - Configured as `DC01.corp.local`
  - Hosts Active Directory, DNS, and DHCP
  - Shared folder mapped via GPO
- Client Machine (Windows 10 Enterprise Evaluation)
  - Hostname: `CLIENT01`
  - Joined to the `corp.local` domain

_Features Implemented_

- Static IP configuration on DC and client
- Domain setup and user account creation
- Shared folder (`\\DC01\Users\Administrator\Desktop\SharedFolder`)
- Group Policy Object (GPO) to automap network drive
- GPO link targeted via Organizational Unit

_Tools Used_

- VirtualBox
- Windows Server 2022 ISO
- Windows 10 ISO
- Group Policy Management Console (GPMC)
- Active Directory Users & Computers
- Command line + GUI

_Setup Instructions_

Detailed walkthrough and troubleshooting are available in the `NOTES.md`.

_Author_

**Dami Ola**
