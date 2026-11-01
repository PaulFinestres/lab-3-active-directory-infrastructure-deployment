# Lab 5.1 – Network Architecture & Infrastructure

**Status:** Completed  
**Estimated duration:** 10–12 hours

Lab 5.1 establishes the virtual network foundation for the SOC environment. It covers the Proxmox infrastructure, network segmentation, routing through OPNsense, and the basic services required to administer and validate the environment.

## Infrastructure

The lab is hosted on Proxmox VE. The virtual machines are separated by role, with OPNsense providing routing and firewalling between the lab networks.

| Component | Role |
|---|---|
| Proxmox VE | Hypervisor |
| OPNsense (`soc-fw-01`) | Firewall and router |
| Windows Server (`soc-ad-01`) | Active Directory and DNS |
| Ubuntu Server 26.04.1 (`soc-siem-01`) | SOC / SIEM server |
| Debian with GUI (`soc-adm-01`) | Administration workstation |
| Kali Linux (`soc-attack-01`) | Attack simulation workstation |
| Debian Server (`soc-dmz-01`) | DMZ host |
| Windows 11 (`soc-cli-01`) | Client workstation |

The Proxmox inventory below shows the virtual machines deployed for this lab.

![Proxmox SOC VM inventory](evidence/01-proxmox-vm-inventory.png)

## Network Architecture

The network is divided into VLANs according to the role of each environment. OPNsense acts as the central router and firewall, controlling inter-VLAN traffic and providing outbound connectivity through the home network.

![SOC network architecture](architecture.png)

### IP addressing

| VLAN | Name | Network | Gateway |
|---:|---|---|---|
| 10 | CLIENTS | `192.168.10.0/24` | `192.168.10.254` |
| 20 | ATTACKER | `172.20.20.0/24` | `172.20.20.254` |
| 40 | DMZ | `10.0.40.0/27` | `10.0.40.30` |
| 50 | SERVERS | `10.0.50.0/28` | `10.0.50.14` |
| 80 | SOC / MONITORING | `172.20.80.0/28` | `172.20.80.14` |
| 90 | ADMIN | `192.168.90.0/28` | `192.168.90.14` |
| 99 | INFRA-MGMT | `192.168.99.96/28` | `192.168.99.110` |

## Implementation

### OPNsense

OPNsense provides the layer 3 gateways for the lab VLANs. Its WAN interface, `WAN_HOME`, uses the static address `192.168.1.200/24` on the home network, with `192.168.1.254` as the upstream gateway.

Outbound source NAT is configured manually. Traffic from the internal VLANs is translated to the OPNsense WAN address when leaving through `WAN_HOME`.

![OPNsense dashboard](evidence/02-opnsense-dashboard.png)

**Manual outbound NAT configuration:**

![OPNsense outbound NAT rules](evidence/03-opnsense-source-nat.png)

### Administration access

The administration workstation (`soc-adm-01`) is used to manage the lab systems. Remote access to the SOC server is provided over SSH on TCP port `3333`.

SSH is configured for public-key authentication, with password authentication and root login disabled. The effective daemon configuration was checked with `sshd -T`.

![Effective SSH configuration](evidence/04-ssh-hardening.png)

### DNS

The Active Directory server at `10.0.50.10` provides internal DNS for the `paul.lan` domain. The SOC server uses this address as its DNS resolver.

Forward and reverse lookups were checked to confirm name resolution for the internal systems.

![DNS forward lookup](evidence/06-dns-forward-lookup.png)

![DNS reverse lookup](evidence/05-dns-reverse-lookup.png)

## Verification

The following checks were used to verify the main services configured in this lab:

- **DNS:** forward lookup of the Active Directory server and reverse lookup of the SOC server address (`172.20.80.10`).
- **SSH:** successful remote connection to the SOC server from the administration workstation using WindTerm.
- **SSH hardening:** confirmation of the effective SSH daemon settings.
- **Outbound NAT:** review of the manual source NAT rules on OPNsense.

![Successful SSH connection to the SOC server](evidence/07-ssh-connection.png)

## Challenges & Troubleshooting

- **Outbound NAT interface assignment:** During configuration, one source NAT rule was assigned to the wrong interface. The rule was corrected so that outbound translations for the internal VLANs use `WAN_HOME`.

## Takeaways

This lab provided practical experience with virtual network design, VLAN-based segmentation, firewall routing, outbound NAT, internal DNS and secure remote administration. It also reinforced the importance of checking interface assignments and validating each service after configuration.

## Next step

**Lab 5.2 – Suricata IDS on OPNsense:** configure network intrusion detection and validate alert generation using controlled test traffic.
