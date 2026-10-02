# SOC/SIEM Detection & Incident Response Home Lab

## Project Overview

This project is a hands-on Security Operations Center (SOC) and SIEM home lab designed to demonstrate practical security monitoring, detection engineering, log analysis, incident investigation, and incident response skills.

The environment will use Splunk Enterprise as the primary SIEM and collect Windows endpoint telemetry using Windows Event Logs, Sysmon, and the Splunk Universal Forwarder.

All security activity generated in this project will be controlled, benign, and limited to systems owned within an isolated lab environment.

## Objectives

- Deploy and configure Splunk Enterprise
- Collect Windows Event Logs and Sysmon telemetry
- Develop practical SPL investigation skills
- Establish normal endpoint behavior baselines
- Create and tune security detections
- Investigate authentication, account, PowerShell, and network activity
- Correlate multiple security events during incident investigations
- Map detections to MITRE ATT&CK
- Build a security monitoring dashboard
- Develop a reusable incident response workflow
- Add useful PowerShell or Python security automation
- Produce professional incident documentation

## Planned Architecture

The lab will contain three virtual machines:

- **SOC-SPLUNK01** — Ubuntu Server running Splunk Enterprise
- **SOC-WIN01** — Windows endpoint monitored using Sysmon and Splunk Universal Forwarder
- **SOC-KALI01** — Kali Linux system used for controlled security-event generation

The systems will communicate through an isolated VMware lab network.

## Security Monitoring Lifecycle

**Telemetry → Collection → SIEM → Detection → Alert → Triage → Investigation → Correlation → Response → Remediation → Validation → Documentation**

## Current Status

**Phase 0 — Planning and Architecture**

The lab architecture, system requirements, network design, repository structure, and project scope are currently being finalized.

## Disclaimer

This project is intended exclusively for cybersecurity education and defensive security training. All simulations are performed against systems owned and controlled within an isolated lab environment. No real malware or unauthorized systems are used.
