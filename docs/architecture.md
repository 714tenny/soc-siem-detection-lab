# Lab Architecture

## Overview

The SOC/SIEM Detection & Incident Response Home Lab is designed as an isolated virtual security monitoring environment. The lab uses Splunk Enterprise as the central SIEM platform and collects endpoint telemetry from a Windows workstation using Windows Event Logs, Sysmon, and the Splunk Universal Forwarder.

A Kali Linux virtual machine will be used only to generate controlled and benign security events against systems owned within the lab.

The environment is designed to demonstrate the complete security monitoring lifecycle:

**Telemetry → Collection → SIEM → Detection → Alert → Triage → Investigation → Correlation → Response → Remediation → Validation → Documentation**
flowchart LR

    HOST["Physical Host<br/>Windows 11 + VMware Workstation"]

    INTERNET["Internet<br/>Temporary NAT Access"]

    subgraph LAB["VMware Host-Only Lab Network — 192.168.50.0/24"]

        SPLUNK["SOC-SPLUNK01<br/>Ubuntu Server 24.04 LTS<br/>192.168.50.10<br/><br/>Splunk Enterprise"]

        WIN["SOC-WIN01<br/>Windows 11<br/>192.168.50.20<br/><br/>Windows Event Logs<br/>Sysmon<br/>Splunk Universal Forwarder"]

        KALI["SOC-KALI01<br/>Kali Linux<br/>192.168.50.30<br/><br/>Controlled Security Testing"]

    end

    WIN -->|"Windows + Sysmon Telemetry<br/>TCP 9997"| SPLUNK
    KALI -->|"Controlled Test Traffic"| WIN
    HOST -->|"Splunk Web<br/>TCP 8000"| SPLUNK

    INTERNET -. "Temporary updates/downloads only" .-> SPLUNK
    INTERNET -. "Temporary updates/downloads only" .-> WIN
    INTERNET -. "Temporary updates/downloads only" .-> KALI
---

## Architecture Components

### SOC-SPLUNK01

**Operating System:** Ubuntu Server 24.04 LTS  
**Role:** SIEM Server  
**Planned IP Address:** `192.168.50.10`

**Resources**

- 4 vCPU
- 8 GB RAM
- 100 GB storage

**Primary Functions**

- Splunk Enterprise
- Centralized log ingestion
- Security event indexing
- SPL searching
- Detection rules
- Alerts
- Dashboards
- Event correlation
- Incident investigation

---

### SOC-WIN01

**Operating System:** Windows 11  
**Role:** Monitored Endpoint  
**Planned IP Address:** `192.168.50.20`

**Resources**

- 4 vCPU
- 8 GB RAM
- 80 GB storage

**Primary Functions**

- Windows Event Logs
- Sysmon telemetry
- PowerShell logging
- Splunk Universal Forwarder
- Endpoint activity generation
- Authentication testing
- Process monitoring
- Network connection monitoring

This system will represent a typical employee workstation monitored by a SOC.

---

### SOC-KALI01

**Operating System:** Kali Linux  
**Role:** Controlled Security Event Generator  
**Planned IP Address:** `192.168.50.30`

**Resources**

- 2 vCPU
- 4 GB RAM
- 40 GB storage

**Primary Functions**

- Controlled network reconnaissance
- Nmap scanning inside the isolated lab
- Network traffic generation
- Controlled authentication testing
- Wireshark/network analysis when appropriate

This system will not be used to attack public or unauthorized systems.

---

## Network Architecture

The lab will primarily use a VMware Host-Only network.

**Network:** `192.168.50.0/24`

| System | IP Address | Purpose |
|---|---|---|
| SOC-SPLUNK01 | 192.168.50.10 | Splunk SIEM |
| SOC-WIN01 | 192.168.50.20 | Monitored Windows endpoint |
| SOC-KALI01 | 192.168.50.30 | Controlled test system |

The Host-Only network will keep security simulations isolated from the public Internet and the physical home network.

Temporary VMware NAT connectivity may be enabled when Internet access is required for legitimate activities such as:

- operating system updates
- downloading trusted software
- installing Splunk
- installing Sysmon
- installing the Splunk Universal Forwarder

NAT connectivity will be disconnected when isolation is required for controlled security simulations.

Bridged networking will not be used for security testing.

---

## Telemetry Flow

The primary security telemetry path will be:

```text
SOC-WIN01
    |
    +-- Windows Event Logs
    |
    +-- Sysmon
    |
    v
Splunk Universal Forwarder
    |
    | TCP 9997
    v
SOC-SPLUNK01
    |
    v
Splunk Enterprise
    |
    +-- Indexing
    +-- SPL Searches
    +-- Detections
    +-- Alerts
    +-- Dashboards
    +-- Investigation
```

Splunk Web will be accessed through TCP port `8000` from the authorized lab environment.

---

## Security Boundaries

All testing will remain within systems owned and controlled in the home lab.

The environment will follow these restrictions:

- No real malware will be executed.
- No unauthorized systems will be scanned or attacked.
- No intentionally vulnerable services will be exposed to the public Internet.
- Security simulations will use controlled and benign techniques.
- Attack-style traffic will remain within the isolated VMware network whenever possible.
- Sensitive information will not be committed to the public GitHub repository.

---

## Project Scope

This project focuses on defensive security operations, including:

- SIEM administration
- Endpoint telemetry
- Windows Event Logs
- Sysmon
- Splunk Universal Forwarder
- SPL
- Log analysis
- Detection engineering
- Security monitoring
- Alert triage
- Incident investigation
- Event correlation
- Network analysis
- MITRE ATT&CK mapping
- Incident response
- Detection tuning
- False-positive analysis
- Security automation
- Documentation

The project is not intended to function as a penetration-testing environment, malware-analysis environment, production SOC, or enterprise Splunk deployment.

---

## Current Status

**Phase 0 — Planning and Architecture**

Architecture and system requirements defined. Virtual machines have not yet been deployed.
