# Lab Setup

This document records the deployment and configuration of the SOC/SIEM lab environment.

Configuration steps will be documented as each component is deployed and validated.

## Current Status
**Phase 0 — Planning and Architecture**
## VMware Network Configuration

A dedicated VMware Host-Only network was created for the SOC lab.

### Lab Network

- Network: `VMnet2`
- Network Type: Host-only
- Subnet: `192.168.50.0/24`
- Subnet Mask: `255.255.255.0`
- Host Virtual Adapter: Enabled
- VMware DHCP: Disabled

Static IP addresses will be assigned manually to each lab system:

| System | Planned IP Address |
|---|---|
| SOC-SPLUNK01 | 192.168.50.10 |
| SOC-WIN01 | 192.168.50.20 |
| SOC-KALI01 | 192.168.50.30 |

The Host-Only network isolates controlled security testing from the public Internet and physical home network.

VMware NAT (`VMnet8`) will only be used temporarily when a virtual machine requires trusted Internet access for operating system updates or official software downloads.

Bridged networking will not be used for security simulations.

### Validation

The dedicated `VMnet2` Host-Only network was successfully created with DHCP disabled and the host virtual adapter enabled.

Evidence:

`images/02-vmware-isolated-network.png`
