# Lab Architecture

## Overview

The SOC/SIEM Detection & Incident Response Home Lab is a completed, isolated security monitoring environment built in VMware Workstation. Splunk Enterprise runs on Ubuntu Server and receives Windows Security and Sysmon telemetry from a monitored Windows 11 endpoint through the Splunk Universal Forwarder.

The completed environment contains two virtual machines:

- **SOC-SPLUNK01** — Ubuntu Server running Splunk Enterprise
- **SOC-WIN01** — Windows 11 Pro endpoint running Sysmon and the Splunk Universal Forwarder

Controlled security simulations are generated locally on SOC-WIN01. No Kali Linux virtual machine is required for the completed project.

The environment demonstrates the monitoring lifecycle:

**Telemetry → Collection → SIEM → Baseline → Detection → Alert → Triage → Investigation → Correlation → Response → Remediation → Validation → Documentation**

## Architecture Diagram

~~~mermaid
flowchart TB

    HOST["Physical Host<br/>Windows 11 Pro + VMware Workstation"]
    INTERNET["Internet<br/>VMware NAT for trusted updates/downloads"]

    subgraph LAB["VMware Isolated Lab Network — VMnet2 — 192.168.50.0/24"]
        direction LR

        WIN["SOC-WIN01<br/>Windows 11 Pro<br/>192.168.50.20<br/><br/>Windows Event Logs<br/>Sysmon<br/>Splunk Universal Forwarder"]

        SPLUNK["SOC-SPLUNK01<br/>Ubuntu Server 24.04 LTS<br/>192.168.50.10<br/><br/>Splunk Enterprise"]

        WIN -->|"Windows + Sysmon telemetry<br/>TCP 9997"| SPLUNK
    end

    HOST -->|"SSH TCP 22<br/>Splunk Web TCP 8000"| SPLUNK
    INTERNET -.->|"NAT adapter"| SPLUNK
    INTERNET -.->|"NAT adapter"| WIN
~~~

## SOC-SPLUNK01

**Operating System:** Ubuntu Server 24.04.5 LTS  
**Role:** SIEM server  
**Lab IP:** 192.168.50.10/24  
**Lab Interface:** ens37  
**NAT Interface:** ens33  
**Resources:** 4 vCPU, 8 GB RAM, 100 GB disk

### Primary Functions

- Splunk Enterprise 10.6.0.5
- Splunk Web on TCP 8000
- Universal Forwarder receiving port on TCP 9997
- Dedicated windows and sysmon indexes
- SPL searching and detection engineering
- Scheduled alerting
- Dashboard Studio monitoring
- Incident investigation and correlation
- Custom search-time field extraction

## SOC-WIN01

**Operating System:** Windows 11 Pro  
**Role:** Monitored endpoint  
**Lab IP:** 192.168.50.20/24  
**Lab Interface:** Ethernet1  
**NAT Interface:** Ethernet0  
**Resources:** 4 vCPU, 8 GB RAM, 80 GB disk

### Primary Functions

- Windows Security, System, and Application event logs
- Sysmon endpoint telemetry
- Splunk Universal Forwarder
- Controlled security simulations
- Authentication testing
- Process and parent-child monitoring
- Registry monitoring
- File-creation monitoring
- Network-connection monitoring

## VMware Network Architecture

### VMnet2

- Network: 192.168.50.0/24
- Type: VMware host-only / isolated lab network
- DHCP: Disabled
- Windows host adapter: 192.168.50.1
- SOC-SPLUNK01: 192.168.50.10
- SOC-WIN01: 192.168.50.20

VMnet2 carries lab management and security telemetry traffic without providing a default Internet route to the lab interface.

### VMware NAT

Both virtual machines retain a NAT adapter for trusted operating-system updates and official software downloads. Security telemetry between the endpoint and Splunk uses VMnet2.

Bridged networking is not used for controlled security simulations.

## Splunk Server Firewall

UFW restricts access to SOC-SPLUNK01 on the isolated interface.

| Port | Service | Allowed Source |
|---|---|---|
| 22/TCP | SSH | 192.168.50.1 |
| 8000/TCP | Splunk Web | 192.168.50.1 |
| 9997/TCP | Splunk receiver | 192.168.50.20 |

Splunk management port 8089 is not exposed to other lab systems.

## Telemetry Flow

~~~text
SOC-WIN01
    |
    +-- Windows Security / System / Application Logs
    |
    +-- Sysmon
    |
    v
Splunk Universal Forwarder
    |
    | TCP 9997 over VMnet2
    v
SOC-SPLUNK01
    |
    v
Splunk Enterprise
    |
    +-- windows index
    +-- sysmon index
    +-- Search-time field extraction
    +-- SPL detections
    +-- Scheduled alerts
    +-- SOC dashboard
    +-- Incident investigation
~~~

## Search-Time Field Extraction

A custom Splunk technical add-on named **TA-soc-lab-sysmon** provides search-time extraction for:

- XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
- XmlWinEventLog:Security

This makes fields such as EventCode, User, Image, CommandLine, ParentImage, TargetFilename, TargetObject, DestinationIp, TargetUserName, LogonType, Status, and SubStatus directly searchable.

## Security Boundaries

- All testing is performed only on systems owned and controlled in the lab.
- No real malware is executed.
- No unauthorized systems are scanned or attacked.
- No intentionally vulnerable service is exposed to the public Internet.
- Controlled simulations use benign commands and test artifacts.
- Sensitive credentials and private information are not committed to the public repository.

## Project Scope

The project focuses on defensive security operations:

- SIEM administration
- Windows endpoint telemetry
- Sysmon
- Splunk Universal Forwarder
- SPL
- Detection engineering
- Detection tuning
- False-positive analysis
- Windows authentication analysis
- Alert triage
- Process-tree investigation
- Registry persistence analysis
- Network analysis
- MITRE ATT&CK mapping
- Dashboard development
- Incident investigation
- Timeline reconstruction
- Response and cleanup validation
- Technical documentation

## Current Status

**Complete**

The architecture was deployed and validated end to end. SOC-WIN01 forwards Windows and Sysmon telemetry to SOC-SPLUNK01, five scheduled detections have been validated, a SOC dashboard has been built, and a controlled incident investigation has been completed.

Evidence:

- [SOC dashboard](../images/54-soc-security-monitoring-dashboard.png)
- [Incident timeline](../images/60-incident-timeline.png)
