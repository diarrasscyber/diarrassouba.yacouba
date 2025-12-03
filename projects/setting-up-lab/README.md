# 🛡️ SOC Analyst Home Lab – Full Setup Guide
This repository contains a complete step-by-step guide to building a SOC Analyst training lab, including Windows Server, Windows 10/11, Linux distributions, SIEMs, IDS/IPS, FTP server and web applications.

---

## 📑 Table of Contents
- [1. Installing the Lab Environment](#1-installing-the-lab-environment)
- [2. Installing & Configuring Windows Server 2022 (SIEM1)](#2-installing--configuring-windows-server-2022-siem1)
- [3.Confi guring Windows Server 2019](#3-configuring-windows-server-2019)
- [4. Installing Windows 10/11 VM](#4-installing-windows-1011-vm)
- [5. Installing Windows 10/11 VM (SIEM2)](#5-installing-windows-1011-vm-siem2)
- [6. Disable Internet Explorer Enhanced Security](#6-disable-internet-explorer-enhanced-security)
- [7. Adding Roles & Features on Windows Server 2019](#7-adding-roles--features-on-windows-server-2019)
- [8. Installing Parrot Security / Kali Linux](#8-installing-parrot-security--kali-linux)
- [9. Configuring OSSIM SIEM Server](#9-configuring-ossim-siem-server)
- [10. Disable Firewall & Defender on SIEM1](#10-disable-firewall--defender-on-siem1)
- [11. Share & Map SOC-Tools Directory](#11-share--map-soc-tools-directory)
- [12. Install Required Applications](#12-install-required-applications)
- [13. Create User Accounts (SIEM2)](#13-create-user-accounts-siem2)
- [14. Install SQL Server Express 2017](#14-install-sql-server-express-2017)
- [15. Test Network Connectivity](#15-test-network-connectivity)
- [16. Configure FTP Server (Server 2019)](#16-configure-ftp-server-server-2019)
- [17. Deploy LuxuryTreats Website](#17-deploy-luxurytreats-website)
- [18. Edit Hosts File (Windows & Linux)](#18-edit-hosts-file-windows--linux)
- [19. Create VM Snapshots](#19-create-vm-snapshots)

---

# 1. Installing the Lab Environment

### Install VMware or VirtualBox  
Configure the virtual network adapter:

1. Open **Edit → Virtual Network Editor**
2. Select **VMnet8**
3. Click **Change Settings**
4. Select VMnet8
5. Set IP address: `10.10.1.0/24`
6. Open **NAT Settings**
   - Gateway IP: `10.10.1.1`
7. Open **DHCP Settings**
   - Start: `10.10.1.2`
   - End: `10.10.1.254`
8. Apply and confirm.

---

# 2. Installing & Configuring Windows Server 2022 (SIEM1)

### Install Windows Server 2022 VM
Rename the computer:
- **SIEM1**

### Static IP Configuration
- IP: `10.10.1.22`
- Mask: `255.255.255.0`
- Gateway: `10.10.1.1`
- DNS: `8.8.8.8`

Disable automatic Server Manager startup:
- Server Manager → Manage → Server Manager Properties

---

# 3. Configuring Windows Server 2019

### Install Windows Server 2019  
Rename PC:
- **windows19**

### Static IP
- IP: `10.10.1.19`
- Mask: `255.255.255.0`
- Gateway: `10.10.1.1`
- DNS: `8.8.8.8`

Disable Server Manager on startup.

---

# 4. Installing Windows 10/11 VM

- Add a second hard disk  
- Rename machine (Win10 or Win11)
- Assign static IP: `10.10.1.11`
- Create new disk volume:
  Windows + X → Disk Management → Create New Simple Volume

---

# 5. Installing Windows 10/11 VM (SIEM2)

- Create new VM  
- Rename → **SIEM2**
- Assign static IP: `10.10.1.18`

---

# 6. Disable Internet Explorer Enhanced Security

On **Server 2022 & 2019**:
- Server Manager → Local Server → IE Enhanced Security Configuration  
Disable for:
- Administrators  
- Users  

---

# 7. Adding Roles & Features on Windows Server 2019

Open:
**Server Manager → Add Roles and Features**

Enable:

- Web Server IIS  
- BranchCache  
- NFS Client  
- .NET Framework 3.5 (HTTP Activation, Non-HTTP Activation)  
- .NET Framework 4.7 (TCP Activation, Named Pipes)  
- SNMP Service  
- Telnet Client  
- TFTP Client  
- RPC over HTTP Proxy  
- FTP Server  

When prompted for installation source:
- Select **DVD → sources/SXS**

Restart.

---

# 8. Installing Parrot Security / Kali Linux

- Install ParrotOS or Kali Linux  
- Run `ifconfig` to view interface name  
- Assign static IP: `10.10.1.13`
- Reboot using `reboot`

---

# 9. Configuring OSSIM SIEM Server

### VM Specs
- RAM: 8 GB  
- CPU: 4  
- Cores: 2  
- Disk: 60 GB  

Install:
- AlienVault USM OSSIM 4.13

### Assign IP
- `192.168.103.17`

### Default Login
- User: `root`
- Password: `toor`

### Network Configuration
- SENSOR → NETWORK CIDR → `192.168.103.0/24`

---

# 10. Disable Firewall & Defender Antivirus on SIEM1

### Firewall
- Control Panel → Windows Defender Firewall → Turn Off  
- Advanced Settings → Disable Domain Profile

### Disable Windows Defender Antivirus
Run:
`gpedit.msc`

Navigate:
- Computer Config → Admin Templates → Windows Components → Windows Defender Antivirus  
Enable:
- **Turn off Windows Defender Antivirus**

---

# 11. Share & Map SOC-Tools Directory (SIEM1)

On **SIEM1**:

1. Install WinRAR  
2. Shrink C: by 40GB → Create new partition  
3. Create folder: **SOC**
4. Extract SOC tools into SOC folder  
5. Enable Network Discovery  
6. Share folder → Permissions → Everyone = Read/Write  

On all machines (Win10, Win11, Server2019, SIEM2):
- Map drive:

