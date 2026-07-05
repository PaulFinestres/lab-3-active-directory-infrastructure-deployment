# Lab 3 - Active Directory Infrastructure Deployment


![Windows](https://custom-icon-badges.demolab.com/badge/Windows-0078D6?logo=windows11&logoColor=white)
![Proxmox](https://img.shields.io/badge/Proxmox-VE-E57000?logo=proxmox)
![Active Directory](https://img.shields.io/badge/Active_Directory-Domain_Services-003366)
![DNS](https://img.shields.io/badge/DNS-Configured-0A66C2)
![DHCP](https://img.shields.io/badge/DHCP-Configured-2E8B57)

<br>

Designed and deployed a Windows Server Active Directory infrastructure featuring DNS, DHCP, VLAN segmentation, centralized authentication, AGDLP-based access control, SMB file sharing, NTFS permissions, and Group Policy management.

<br>

**Technologies:** Windows Server 2022 • Active Directory (AD DS) • DNS • DHCP • Cisco IOS • Proxmox VE • GPO • SMB • NTFS

## Table of Contents

- [Objectives](#objectives)
- [Infrastructure](#infrastructure)
- [Network Topology](#network-topology)
- [Active Directory Architecture](#active-directory-architecture)
- [Configuration](#configuration)
- [Validation](#validation)
- [Challenges Encountered](#challenges-encountered)
- [Lessons Learned](#lessons-learned)
- [Future Improvements](#future-improvements)


## Objectives

The objective of this project was to design and deploy a realistic Active Directory infrastructure simulating a small enterprise environment.

**This projet demonstrates:**

- Implementing role-based access control following the AGDLP model.
- Securing shared folders using NTFS permissions.
- Configuring Group Policy Objects (GPOs) to automate administrative tasks.
- Centralizing network services through Active Directory, including DNS and DHCP.

## Infrastructure

This lab runs on a mini PC hosting Proxmox VE with two physical network interfaces. 
One interface is connected to my home network for management, while the second is connected to a Cisco router configured for Router-on-a-Stick (RoaS). 
Virtual machines communicate through a Proxmox Linux bridge, which forwards 802.1Q tagged traffic to the router. 
The router performs inter-VLAN routing between the configured VLANs.

### IP Addressing

| Component | IP Address | VLAN | Assignment |
|-----------|------------|------|------------|
| Windows Server 2022 | 10.0.50.10 | 50 | Static |
| Windows 10 Client | 10.0.10.100 | 10 | DHCP |
| Router g0/0.50 | 10.0.50.14 | 50 | Gateway |
| Router g0/0.10 | 10.0.10.254 | 10 | Gateway |

## Network Topology

The following diagram illustrates the network architecture of the lab. To better simulate an enterprise environment, client and server systems are separated into dedicated VLANs. Inter-VLAN routing is performed using a Cisco Router-on-a-Stick (RoaS) configuration.

![Network Topology](screenshots/network-topology.png)

## Active Directory Architecture

The Active Directory environment is organized into dedicated Organizational Units (OUs) for users, groups, and computers. Security groups follow the AGDLP model to provide structured and scalable permission management.

![Active Directory Architecture](screenshots/agdlp-structure.png)

## Configuration 

**Router configuration:** [`configs/r1.cfg`](configs/r1.cfg)

### Domain Services

A Windows Server 2022 virtual machine was promoted to a Domain Controller for the `paul.lan` domain. The Active Directory Domain Services (AD DS), DNS, and DHCP roles were installed to provide centralized authentication, name resolution, and IP address management.

### DHCP

A DHCP scope was configured to provide IP addresses to clients located in VLAN 10. DHCP Relay (`ip helper-address`) was configured on the Cisco Router-on-a-Stick, allowing DHCP requests to reach the server located in VLAN 50. Clients automatically receive their IP address, default gateway, DNS server, and domain suffix.

![DHCP Validation](screenshots/dhcp-validation)

### File Server

### Group Policy
