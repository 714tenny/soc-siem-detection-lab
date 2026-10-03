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
## Splunk Server Virtual Machine

The Splunk Enterprise server virtual machine was created in VMware Workstation Pro.

### Virtual Machine Configuration

- VM Name: `SOC-SPLUNK01`
- Operating System: Ubuntu Server 24.04 LTS
- Memory: 8 GB
- vCPU: 4
- Virtual Disk: 100 GB
- Initial Network Adapter: NAT
- VM Storage Location: `C:\VMs\SOC-SPLUNK01`

NAT connectivity is being used temporarily during operating system installation and trusted software updates.

The dedicated VMware Host-Only network (`VMnet2`) will be added after the operating system has been installed and validated.

### Validation

The VM hardware configuration was reviewed before operating system installation.

Evidence:

`images/04-splunk-vm-hardware.png`
## Splunk Server Network Configuration

The Splunk server uses two network interfaces to separate trusted Internet access from isolated SOC lab traffic.

### NAT Interface

- Interface: `ens33`
- Address: `192.168.225.128/24`
- Purpose: Temporary Internet access for trusted updates and official software downloads
- Default route: VMware NAT through `192.168.225.2`

### SOC Lab Interface

- Interface: `ens37`
- Address: `192.168.50.10/24`
- Purpose: Permanent communication with systems inside the isolated SOC lab
- Network: `VMnet2`
- Default Gateway: None

The isolated interface does not provide an Internet route. Traffic for the lab subnet remains on `192.168.50.0/24`.

### Validation

Both interfaces were successfully activated and the routing table confirmed that the NAT interface remains the default Internet route.

Evidence:

`images/06-splunk-server-network-validation.png`
## Host-to-Splunk Connectivity Validation

Connectivity between the Windows host and the Splunk server was validated across the isolated VMware Host-Only network.

### Validation Results

- Windows host VMnet2 address: `192.168.50.1`
- Splunk server lab address: `192.168.50.10`
- ICMP connectivity: Successful
- Packet loss: 0%
- SSH TCP port 22: Reachable
- VMware interface: `VMware Network Adapter VMnet2`

The results confirm that the physical host can securely administer `SOC-SPLUNK01` through the isolated SOC lab network.

Evidence:

`images/07-host-to-splunk-lab-connectivity.png`
## Splunk Enterprise Deployment

Splunk Enterprise was installed on `SOC-SPLUNK01` using the official Linux AMD64 Debian package.

### Installation

- Splunk Enterprise Version: `10.6.0.5`
- Installation Directory: `/opt/splunk`
- Operating System: Ubuntu Server 24.04.5 LTS
- Splunk OS Service Account: `splunk`
- Splunk Web Port: `8000`
- Lab Interface: `192.168.50.10`

The downloaded Splunk package was validated against Splunk's published SHA-512 checksum before installation.

Splunk was started using the dedicated `splunk` operating-system account rather than root.

### Splunk Web Validation

Splunk Web was successfully accessed from the Windows host across the isolated VMware Host-Only network at:

`http://192.168.50.10:8000`

Successful access confirmed that:

- Splunk Enterprise started successfully
- Splunk Web is listening on TCP port `8000`
- The Windows host can reach Splunk through `VMnet2`
- Administrator authentication is functioning

Evidence:

- `images/08-splunk-package-checksum-verification.png`
- `images/09-splunk-install-validation.png`
- `images/10-splunk-first-start.png`
- `images/11-splunk-web-home.png`
## Splunk Automatic Startup

Splunk Enterprise was configured as a systemd-managed service using the dedicated `splunk` operating-system account.

### Service Configuration

- Service: `Splunkd.service`
- Service User: `splunk`
- Service Group: `splunk`
- Boot Startup: Enabled
- Service State: Active

The Ubuntu server was rebooted to verify that Splunk Enterprise starts automatically without manual intervention.

### Post-Reboot Validation

The following checks succeeded:

- `systemctl is-enabled Splunkd` returned `enabled`
- `systemctl is-active Splunkd` returned `active`
- Splunk CLI confirmed `splunkd is running`

Evidence:

`images/12-splunk-boot-start-validation.png`
