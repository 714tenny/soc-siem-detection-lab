# SOC/SIEM Detection & Incident Response Home Lab

## Project Overview

This project is a hands-on Security Operations Center (SOC) and SIEM home lab built to demonstrate practical security monitoring, detection engineering, log analysis, alerting, incident investigation, and incident response skills.

The lab uses Splunk Enterprise as the SIEM platform and collects Windows endpoint telemetry from Sysmon and Windows Event Logs through the Splunk Universal Forwarder.

The project includes custom field extraction, detection tuning, scheduled alerts, a SOC monitoring dashboard, controlled security simulations, MITRE ATT&CK mapping, and a complete incident investigation.

All simulated activity is benign and performed only against systems owned and controlled within an isolated lab environment.

---

## Skills Demonstrated

- Splunk Enterprise administration
- Splunk Search Processing Language (SPL)
- Detection engineering
- Security-event baselining and tuning
- Windows Security Event Log analysis
- Sysmon telemetry analysis
- Splunk Universal Forwarder configuration
- Search-time field extraction
- Alert creation and validation
- Process-tree analysis
- Process GUID correlation
- Registry persistence analysis
- Network connection analysis
- Windows authentication investigation
- Incident timeline reconstruction
- MITRE ATT&CK mapping
- SOC dashboard development
- Incident response documentation
- VMware virtual networking
- Linux server administration

---

## Lab Architecture

The completed environment consists of two virtual machines running in VMware Workstation.

### SOC-SPLUNK01

Ubuntu Server running:

- Splunk Enterprise
- Splunk Web
- Splunk receiving port `9997`
- Custom Sysmon and Windows Security field extraction
- Dedicated `windows` and `sysmon` indexes

### SOC-WIN01

Windows 11 Pro endpoint running:

- Sysmon
- Splunk Universal Forwarder
- Windows Security auditing
- Controlled security simulations

### Network

The systems communicate through an isolated VMware network:

```text
VMnet2
192.168.50.0/24

SOC-SPLUNK01
192.168.50.10

SOC-WIN01
192.168.50.20
```

SOC-SPLUNK01 also retains a NAT interface for required Internet connectivity while security telemetry is transported through the isolated lab network.

---

## Security Monitoring Pipeline

```text
SOC-WIN01
    |
    | Windows Event Logs
    | Sysmon
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
    +--> Field Extraction
    +--> SPL Detection Searches
    +--> Scheduled Alerts
    +--> SOC Dashboard
    +--> Incident Investigation
```

---

## Telemetry Sources

### Windows Security Logs

Collected Windows event logs include:

- Security
- System
- Application

Windows Security fields used during investigations include:

- `EventCode`
- `TargetUserName`
- `IpAddress`
- `WorkstationName`
- `LogonType`
- `Status`
- `SubStatus`

### Sysmon

Sysmon provides endpoint telemetry including:

- Event ID 1 — Process Creation
- Event ID 3 — Network Connection
- Event ID 11 — File Creation
- Event ID 12 — Registry Object Create/Delete
- Event ID 13 — Registry Value Set
- Event ID 22 — DNS Query

The Sysmon configuration was tuned to reduce unnecessary registry noise while preserving visibility into security-relevant activity.

---

## Custom Splunk Field Extraction

A custom Splunk technical add-on was created to normalize XML event data at search time.

The add-on provides automatic field extraction for:

```text
XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
XmlWinEventLog:Security
```

This allows searches to directly use fields such as:

```text
EventCode
Image
CommandLine
ParentImage
TargetFilename
TargetObject
DestinationIp
DestinationPort
TargetUserName
LogonType
```

without requiring manual `rex` commands during every investigation.

---

# Detection Engineering

Five detections were developed, baselined, tuned, tested, and converted into scheduled Splunk alerts.

## Detection 01 — Suspicious PowerShell Execution

Detects PowerShell executions containing suspicious command-line behavior including:

- Encoded commands
- Execution policy bypass
- Hidden execution
- `DownloadString`
- Base64 decoding

MITRE ATT&CK:

`T1059.001 — PowerShell`

[View Detection](detections/01-suspicious-powershell.md)

---

## Detection 02 — Registry Run-Key Persistence

Detects modifications to Windows Run and RunOnce registry locations that may be used for persistence.

The detection was tuned to exclude known legitimate baseline activity.

MITRE ATT&CK:

`T1547.001 — Registry Run Keys / Startup Folder`

[View Detection](detections/02-registry-runkey-persistence.md)

---

## Detection 03 — Suspicious Rundll32 Execution

Detects suspicious use of `rundll32.exe`, including execution involving:

- Temporary directories
- AppData locations
- Remote resources
- JavaScript patterns
- UNC paths

MITRE ATT&CK:

`T1218.011 — System Binary Proxy Execution: Rundll32`

[View Detection](detections/03-suspicious-rundll32.md)

---

## Detection 04 — PowerShell Network Connection

Detects outbound network connections initiated by PowerShell while excluding local loopback communication.

This detection demonstrates correlation between process execution and network telemetry.

[View Detection](detections/04-powershell-network-connection.md)

---

## Detection 05 — Repeated Failed Windows Logons

Detects five or more failed Windows authentication attempts against the same account during a five-minute alert window.

Data source:

`Windows Security Event ID 4625`

MITRE ATT&CK:

`T1110.001 — Brute Force: Password Guessing`

[View Detection](detections/05-repeated-failed-logons.md)

---

# SOC Security Monitoring Dashboard

A Splunk Dashboard Studio dashboard was created to provide centralized visibility into endpoint and security telemetry.

The dashboard includes:

- Total Sysmon events
- Failed Windows logons
- Top Sysmon Event IDs
- Suspicious PowerShell activity
- Registry persistence activity
- Suspicious Rundll32 activity
- PowerShell network connections
- Top destination IP addresses
- Windows Security events over time
- Detection summary

![SOC Security Monitoring Dashboard](images/54-soc-security-monitoring-dashboard.png)

---

# Controlled Incident Investigation

A controlled incident simulation was performed to demonstrate the complete SOC investigation workflow.

The simulation generated:

1. Encoded PowerShell execution
2. Temporary file creation
3. Registry Run-key persistence
4. Rundll32 child-process execution
5. TCP network communication

The activity was investigated using Sysmon telemetry in Splunk.

---

## Process Correlation

The primary PowerShell process was identified using:

```text
Process ID: 1800
Process GUID: {cfe05231-ce36-6ac2-6901-000000000700}
```

The Process GUID was then used to correlate multiple Sysmon event types back to the same execution chain.

---

## Incident Timeline

| Time (UTC) | Event ID | Activity |
|---|---:|---|
| `22:07:51.438` | 1 | Encoded PowerShell execution |
| `22:07:51.800` | 11 | Test file created |
| `22:07:51.806` | 13 | Registry Run-key persistence |
| `22:07:52.044` | 1 | Rundll32 child process launched |
| `22:07:54.229` | 3 | TCP network connection |

![Incident Timeline](images/60-incident-timeline.png)

The investigation demonstrated the ability to move from a suspicious process to related file, registry, child-process, and network activity.

[View Full Incident Investigation](incidents/01-controlled-powershell-incident.md)

---

# Investigation Workflow

The project follows a repeatable SOC workflow:

```text
Telemetry
   ↓
Collection
   ↓
SIEM
   ↓
Baseline
   ↓
Detection
   ↓
Alert
   ↓
Triage
   ↓
Investigation
   ↓
Correlation
   ↓
Timeline
   ↓
Response
   ↓
Remediation
   ↓
Validation
   ↓
Documentation
```

---

# Repository Structure

```text
soc-siem-detection-lab/
│
├── detections/
│   ├── 01-suspicious-powershell.md
│   ├── 02-registry-runkey-persistence.md
│   ├── 03-suspicious-rundll32.md
│   ├── 04-powershell-network-connection.md
│   └── 05-repeated-failed-logons.md
│
├── docs/
│   └── Technical and configuration documentation
│
├── images/
│   └── Validation and investigation evidence
│
├── incidents/
│   └── 01-controlled-powershell-incident.md
│
├── scripts/
│   └── Lab scripts and configuration resources
│
└── README.md
```

---

# Key Outcomes

This project demonstrates the ability to:

- Build a functioning SOC/SIEM environment
- Deploy and configure Splunk Enterprise
- Configure Windows telemetry collection
- Implement and tune Sysmon
- Forward endpoint logs into a SIEM
- Normalize XML telemetry using custom field extraction
- Develop SPL detections
- Establish endpoint baselines
- Reduce detection false positives
- Create scheduled security alerts
- Validate detections using controlled simulations
- Analyze Windows authentication activity
- Investigate suspicious PowerShell behavior
- Analyze registry persistence
- Investigate process relationships
- Correlate endpoint and network telemetry
- Reconstruct security incident timelines
- Map detections to MITRE ATT&CK
- Build SOC dashboards
- Produce professional incident documentation

---

## Project Status

**Complete**

The lab now includes:

- SOC infrastructure
- Windows endpoint telemetry
- Sysmon
- Windows Security logs
- Splunk Universal Forwarder
- Custom field extraction
- Five security detections
- Scheduled alerts
- Detection validation
- SOC dashboard
- Controlled incident simulation
- Incident investigation
- Incident-response documentation
- Cleanup validation

---

## Disclaimer

This project is intended exclusively for cybersecurity education and defensive security training.

All simulations were performed against systems owned and controlled within an isolated lab environment.

No real malware, unauthorized access, or third-party systems were used.
