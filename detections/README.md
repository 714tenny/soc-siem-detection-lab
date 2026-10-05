# Detection Engineering

This directory contains the completed SPL detections developed, baselined, tuned, validated, and converted into scheduled Splunk alerts during the SOC/SIEM Detection & Incident Response Home Lab.

## Detection Index

| # | Detection | Primary Data Source | MITRE ATT&CK | Status |
|---:|---|---|---|---|
| 1 | [Suspicious PowerShell Execution](01-suspicious-powershell.md) | Sysmon Event ID 1 | T1059.001 — PowerShell | Validated |
| 2 | [Registry Run-Key Persistence](02-registry-runkey-persistence.md) | Sysmon Event ID 13 | T1547.001 — Registry Run Keys / Startup Folder | Validated |
| 3 | [Suspicious Rundll32 Execution](03-suspicious-rundll32.md) | Sysmon Event ID 1 | T1218.011 — Rundll32 | Validated |
| 4 | [PowerShell Network Connection](04-powershell-network-connection.md) | Sysmon Event ID 3 | T1059.001 — PowerShell | Validated |
| 5 | [Repeated Failed Windows Logons](05-repeated-failed-logons.md) | Windows Security Event ID 4625 | T1110.001 — Password Guessing | Validated |

## Detection Development Workflow

Each detection follows the same general engineering process:

1. Establish a baseline.
2. Identify suspicious behavior.
3. Build the SPL search.
4. Tune known legitimate noise.
5. Generate controlled benign test activity.
6. Validate the expected telemetry.
7. Convert the search into a scheduled Splunk alert.
8. Confirm the alert triggers.
9. Document false-positive considerations and analyst investigation steps.
10. Map the behavior to MITRE ATT&CK where appropriate.

## Alerting Standard

Typical scheduled alert configuration used in the lab:

| Setting | Value |
|---|---|
| Alert Type | Scheduled |
| Cron | `*/5 * * * *` |
| Search Window | Last 5 minutes |
| Trigger Condition | Number of Results > 0 |
| Trigger Mode | Once |
| Suppression / Throttle | 10 minutes |
| Alert Action | Add to Triggered Alerts |

## Supporting Evidence

Detection validation screenshots are stored in the repository's [images](../images/) directory.

The completed detection set is also summarized in:

- [Detection Engineering Summary](../docs/detections.md)
- [SOC Security Monitoring Dashboard](../images/54-soc-security-monitoring-dashboard.png)
- [Controlled PowerShell Incident Investigation](../incidents/01-controlled-powershell-incident.md)

## Status

**Complete — five detections validated and documented.**
