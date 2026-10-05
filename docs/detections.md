# Detection Engineering

## Overview

This document summarizes the detections developed, tuned, validated, and converted into scheduled Splunk alerts during the SOC/SIEM lab.

The detection-development workflow used throughout the project was:

**Baseline → Identify suspicious behavior → Build SPL → Tune known noise → Generate controlled activity → Validate telemetry → Create scheduled alert → Trigger alert → Document findings**

## Completed Detections

| # | Detection | Primary Data Source | MITRE ATT&CK | Status |
|---:|---|---|---|---|
| 1 | Suspicious PowerShell Execution | Sysmon Event ID 1 | T1059.001 PowerShell | Validated |
| 2 | Registry Run-Key Persistence | Sysmon Event ID 13 | T1547.001 Registry Run Keys / Startup Folder | Validated |
| 3 | Suspicious Rundll32 Execution | Sysmon Event ID 1 | T1218.011 Rundll32 | Validated |
| 4 | PowerShell Network Connection | Sysmon Event ID 3 | Network-behavior detection | Validated |
| 5 | Repeated Failed Windows Logons | Windows Security Event ID 4625 | T1110.001 Password Guessing | Validated |

## Detection 01 — Suspicious PowerShell Execution

Detects PowerShell command lines containing suspicious patterns such as encoded commands, execution-policy bypass, hidden execution, DownloadString, and Base64-decoding behavior.

Baseline analysis identified Splunk Universal Forwarder PowerShell activity and excluded it to reduce false positives.

[View full detection](../detections/01-suspicious-powershell.md)

## Detection 02 — Registry Run-Key Persistence

Detects modifications to Windows Run and RunOnce registry locations. Baseline analysis identified legitimate RunOnce GrpConv activity and tuned the detection to suppress that known behavior.

[View full detection](../detections/02-registry-runkey-persistence.md)

## Detection 03 — Suspicious Rundll32 Execution

Detects rundll32.exe executions involving suspicious paths or command-line characteristics, including Temp, AppData, URL, JavaScript, and UNC patterns.

[View full detection](../detections/03-suspicious-rundll32.md)

## Detection 04 — PowerShell Network Connection

Detects non-loopback network connections initiated by PowerShell. The controlled validation used a TCP connection from SOC-WIN01 to the lab Splunk receiver at 192.168.50.10:9997.

[View full detection](../detections/04-powershell-network-connection.md)

## Detection 05 — Repeated Failed Windows Logons

Detects five or more Windows Security Event ID 4625 failures for the same target username during the alert search window.

The rule was validated with a controlled nonexistent test account so the real administrative account was not placed at risk of lockout.

[View full detection](../detections/05-repeated-failed-logons.md)

## Alerting Standard

The lab detections were configured as scheduled alerts using a five-minute cadence.

Typical configuration:

| Setting | Value |
|---|---|
| Alert type | Scheduled |
| Cron | */5 * * * * |
| Search window | Last 5 minutes |
| Trigger condition | Number of Results > 0 |
| Trigger mode | Once |
| Suppression / throttle | 10 minutes |
| Alert action | Add to Triggered Alerts |

## Detection Engineering Lessons

- Baseline legitimate activity before suppressing it.
- Tune specific known-good patterns rather than broadly excluding processes.
- Use the alert search window itself for threshold-based detections when fixed time buckets can split related events.
- Validate both the SPL result and the scheduled alert action.
- Keep controlled tests benign and easy to clean up.
- Document the purpose, logic, false positives, validation evidence, and analyst investigation steps for each rule.

## Dashboard Integration

The completed detections are represented in the SOC Security Monitoring Dashboard alongside Sysmon volume, failed-logon activity, destination IPs, and Windows Security event trends.

![SOC Security Monitoring Dashboard](../images/54-soc-security-monitoring-dashboard.png)

## Current Status

**Complete — five detections validated and documented.**
