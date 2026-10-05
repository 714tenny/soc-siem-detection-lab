# Lessons Learned

## Overview

This project reinforced the importance of building a SOC lab as an end-to-end monitoring system rather than treating Splunk, Sysmon, alerts, and investigations as separate tasks.

## Architecture and Networking

- A dedicated VMnet2 subnet kept telemetry and management traffic separate from normal Internet access.
- Dual-NIC virtual machines allowed trusted updates through VMware NAT while preserving a dedicated lab path.
- Host-based firewall rules should be tested from the exact source system that needs access.
- A blocked connection is not necessarily a telemetry failure. During testing, SOC-WIN01 could not reach Splunk Web on TCP 8000 because UFW intentionally allowed that port only from the physical host. The test was moved to the permitted Splunk receiver port 9997 rather than weakening the firewall.

## Splunk and Telemetry

- Successful log ingestion does not guarantee useful search-time fields.
- Sysmon and Windows Security XML events required explicit field extraction before searches such as EventCode=4625 worked reliably.
- Building the custom TA-soc-lab-sysmon add-on made investigations cleaner and removed the need for repeated manual rex commands.
- Separate windows and sysmon indexes simplified searches and troubleshooting.

## Sysmon Tuning

- Collecting everything can reduce visibility rather than improve it.
- Registry Event IDs 12 and 13 initially dominated the dataset.
- Tuning registry monitoring around security-relevant persistence paths substantially reduced noise while preserving useful Run-key visibility.
- Controlled validation after tuning is essential to prove that important telemetry was not accidentally removed.

## Detection Engineering

- Baseline first, then tune.
- Suppress specific known-good patterns rather than excluding broad process categories.
- Legitimate administration tools such as PowerShell and rundll32.exe need behavioral context before they become meaningful detections.
- Threshold detections must account for how the alert search window is defined. The repeated-failed-logon rule worked more reliably when the five-minute alert window handled the time boundary instead of binning events into fixed five-minute buckets.
- A detection is not complete until the scheduled alert has actually triggered and been reviewed.

## Investigation and Correlation

- Process GUID correlation was one of the most useful investigation techniques in the lab.
- Process IDs alone can be reused, while Sysmon ProcessGuid provides a stronger way to link process creation, file activity, registry changes, network connections, and child processes.
- A short sequence of individually explainable events becomes much more suspicious when they are correlated to the same process within seconds.
- UTC timestamps in Splunk must be reconciled with local endpoint time when building a timeline.

## Dashboard Development

- A dashboard should combine high-level metrics with investigation-oriented views.
- Single-value panels are useful for quick detection counts, while tables and time charts provide context.
- Readability matters more than forcing every visualization into a small space.
- A global time picker makes the dashboard reusable for both routine monitoring and focused review.

## Documentation and Portfolio Design

- Evidence screenshots are most useful when the key fields fit on-screen and tell one clear story.
- Dedicated detection documents are easier to review than one large page containing every SPL query.
- A concise root README should summarize the project and link to detailed detection and incident documents.
- Old planning placeholders should be removed or updated once the implementation is complete so the repository reflects the actual environment.

## Current Status

**Complete**

The lab progressed from architecture planning through deployment, telemetry engineering, detection development, alerting, dashboard creation, incident investigation, cleanup validation, and final documentation.
