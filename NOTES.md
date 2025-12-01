### Windows AD Lab

This document walks through the steps I took to build a basic Active Directory lab environment using Windows Server 2022 as the domain controller and Windows 10 as the client, all inside VirtualBox. I also have a version of this lab run on UTM, you can check it on my website at https://oladami.com/posts/windows-client-join/


1. Virtual Machine Setup

Host Environment:
- MacBook Pro (Intel)
- VirtualBox latest stable version

VMs:
- Windows Server 2022 (DC01)
- Windows 10 Enterprise Eval (CLIENT01)

I downloaded Windows Server 2022 ISO directly from Microsoft Evaluation Center, I also did the same for Windows 10. I chose VirtualBox over VMware because I preferred the way it worked on my Mac.

Setting up the DC and Client in VirtualBox:
i.  Create the DC (Windows Server 2022)
      Open VirtualBox > Click New
      Name: DC01
      Type: Microsoft Windows
      Version: Windows 2022 (64-bit)
      Memory size: At least 4096 MB (recommended)
ii.  Hard Disk:
      Choose “Create a virtual hard disk now”
      Type: VDI
      Storage: Dynamically allocated
      Size: 80 GB
iii.  Attach ISO:
      Go to Settings > Storage
      Under "Controller: IDE", click the empty disk, then choose the Windows Server 2022 ISO using the disk icon.

iv.  Network:
      Go to Settings > Network
      Adapter 1: Enable -> Attached to: Internal Network -> Name: intnet

v.   Start the VM, and go through the installation:
      Choose Windows Server 2022 Standard Evaluation (Desktop Experience)
      Set a strong administrator password

vi.  Create the Client VM (Windows 10)
      Open VirtualBox > Click New.
      Name: CLIENT01
      Type: Microsoft Windows
      Version: Windows 10 (64-bit)
      Memory size: 4096 MB
      Hard Disk: Create a 60 GB dynamically allocated VDI

vii.  Attach ISO:
      Go to Settings > Storage
      Mount the Windows 10 ISO under "Controller: IDE"

viii.  Network:
      Go to Settings > Network
      Adapter 1: Enable -> Attached to: Internal Network -> Name: intnet
      Start the VM and go through the Windows 10 installation:
      Choose language and region
      Skip Microsoft account (choose Offline Account)
      Create a local user
      Complete the setup wizard

VM Network Settings:

All VMs were set to Internal Network using the following steps:
1. Powered off the VM.
2. Opened VM settings > Network
3. Attached to: Internal Network
4. Name: intnet

This allows the VMs to talk to each other while isolating them from the host internet. If internet is needed temporarily (e.g., for package installation), then we can add NAT on adapter 2.


2. Network Configuration

Each VM was manually assigned a static IP in the same subnet (192.168.10.0/24):

- DC01 (Windows Server 2022)  
  - IP: 192.168.10.10  
  - Subnet: 255.255.255.0  
  - Gateway: 192.168.10.1 
  - DNS: 192.168.10.10

- CLIENT01 (Windows 10)
  - IP: 192.168.10.20  
  - Same gateway and DNS as above

I initially had an issue where the client’s IP settings would reset on every reboot. I fixed it by manually reentering the static IP config, then disabling and reenabling the network adapter, which caused it to stick permanently.



3. Windows Server 2022 Setup

After installing Server 2022:

- Renamed the machine to DC01
- Set the static IP settings as above
- Installed the Active Directory Domain Services role
- Promoted the server to a domain controller:
  - Domain name: corp.local
  - NetBIOS: CORP
  - Functional levels: left at 2016 (since 2019/2022 weren’t available in the dropdown)

After reboot, the DC’s DNS changed to 127.0.0.1. This is expected for a domain controller; it points to itself for name resolution.



4. Domain User & OU Setup

Used Active Directory Users and Computers:

- Created an Organizational Unit (OU): `Workforce`
- Created a user: `j****.o**`
- Created a security group: `LinuxAdmins`
- Added `j****.o**` to the `LinuxAdmins` group
- Moved the user into the `Workforce` OU



5. Shared Folder Setup

On DC01:

- Created a folder on the desktop: SharedFolder
- Right-click > Properties > Sharing tab
- Enabled sharing, added Everyone with read/write access
- Verified the share path: \\DC01\Users\Administrator\Desktop\SharedFolder



6. Group Policy (GPO) Setup for Drive Mapping

Opened Group Policy Management:

- Created a new GPO: MapDrive
- Edited it:  
  User Configuration > Preferences > Windows Settings > Drive Maps
- Created a new mapped drive:
  - Location: \\DC01\Users\Administrator\Desktop\SharedFolder
  - Drive letter: S:
  - Reconnect: Checked
  - Action: Replace

- Linked the GPO to the Workforce OU
- Security filtering: added the LinuxUsers group

Then:
1. Move the user to the correct OU (Workforce)
2. Link the GPO to that OU
3. Make sure the user is part of the group referenced in the GPO



7. Windows 10 Client Join to Domain

On the client:

- Renamed to CLIENT01
- Set static IP: 192.168.10.20
- DNS: pointed to DC: 192.168.10.10
- Right-click My Computer > Properties > Change Settings > Domain: corp.local
- Logged in using domain account `corp\j****.o**`

I verified the domain join by restarting and logging in with the domain user.


8. Drive Mapping Verification

After login, no mapped drive showed up at first.

What fixed it:
- I forced a GPO update with `gpupdate /force`
- Rebooted
- Drive S: appeared and pointed to the shared folder

This verified that the Group Policy was applying correctly.



9. Shared Folder Access

Navigated to S:\ — full access to shared files worked. Then I confirmed I could manually browse to:

\\DC01\Users\Administrator\Desktop\SharedFolder

After logging in with the domain account on CLIENT01, I checked if the mapped drive was visible.

At first, the S: drive didn’t show up.

What I Did:

Ran: gpupdate /force

Rebooted the system

Verified that CLIENT01 was placed in the correct OU (Workforce)

Confirmed the domain user was added to the LinuxAdmins group

Linked the GPO (MapSharedDrive) to the Workforce OU in Group Policy Management

Enabled Network Discovery under Network and Sharing Center on CLIENT01

Checked folder permissions to ensure Authenticated Users had access

Reapplied static IP on CLIENT01, then disabled and re-enabled the network adapter when the IP disappeared

After the reboot and these corrections, the S: drive appeared and pointed correctly to the shared folder on DC01.

