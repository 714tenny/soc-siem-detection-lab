# Detection 01 — Suspicious PowerShell Execution

## Overview

This detection identifies potentially suspicious PowerShell execution on the monitored Windows endpoint `SOC-WIN01`.

PowerShell is a legitimate administrative tool but is also commonly abused by attackers for execution, downloading payloads, encoded commands, and defense evasion.

This detection focuses on suspicious command-line characteristics while excluding known legitimate Splunk Universal Forwarder PowerShell activity.

---

## Detection Objective

Identify PowerShell processes using command-line arguments commonly associated with suspicious or malicious execution.

The detection looks for:

- Encoded PowerShell commands
- Execution policy bypass
- Hidden PowerShell windows
- `DownloadString`
- `FromBase64String`

---

## Data Source

| Component | Value |
|---|---|
| Endpoint | `SOC-WIN01` |
| Telemetry Source | Sysmon |
| Sysmon Event ID | `1` — Process Creation |
| Splunk Index | `sysmon` |
| Splunk Host | `soc-win01` |
| SIEM | Splunk Enterprise |

---

## Baseline Analysis

Before creating the detection, normal PowerShell activity was reviewed.

The baseline showed frequent execution of:

`C:\Program Files\SplunkUniversalForwarder\bin\splunk-powershell.exe`

under:

`NT SERVICE\SplunkForwarder`

This activity was identified as expected Splunk Universal Forwarder behavior and excluded from the detection to reduce false positives.

Evidence:

![PowerShell Baseline](../images/39-powershell-baseline-analysis.png)

---

## Detection SPL

```spl
index=sysmon host="soc-win01" EventCode=1
(Image="*\\powershell.exe" OR Image="*\\pwsh.exe")
NOT Image="*\\SplunkUniversalForwarder\\bin\\splunk-powershell.exe"
(
    CommandLine="*-EncodedCommand*"
    OR CommandLine="*-enc *"
    OR CommandLine="*ExecutionPolicy Bypass*"
    OR CommandLine="*-w hidden*"
    OR CommandLine="*DownloadString*"
    OR CommandLine="*FromBase64String*"
)
| table _time User Image CommandLine ParentImage ParentCommandLine
| sort - _time
