# Security Configuration Artifacts

This directory contains sanitized configuration artifacts used in the SOC/SIEM Detection & Incident Response Home Lab.

These files reflect the completed lab configuration and are included so reviewers can inspect the implementation behind the screenshots and documentation.

## Contents

```text
scripts/
├── README.md
├── sysmonconfig.xml
└── splunk/
    ├── inputs.conf
    ├── outputs.conf
    └── TA-soc-lab-sysmon/
        ├── local/
        │   ├── props.conf
        │   └── transforms.conf
        └── metadata/
            └── default.meta
```

## Sysmon

`sysmonconfig.xml` is the tuned Sysmon configuration used on `SOC-WIN01`.

The configuration:

- enables SHA256 hashing
- enables certificate revocation checks
- enables DNS lookup
- collects process creation, network connection, file creation, DNS query, process tampering, and WMI telemetry
- limits registry telemetry to security-relevant persistence and policy locations
- reduces the high-volume registry noise observed during baseline analysis

Deployment path used in the lab:

```text
C:\Tools\Sysmon\sysmonconfig.xml
```

## Splunk Universal Forwarder

`inputs.conf` and `outputs.conf` represent the configuration used by the Splunk Universal Forwarder on `SOC-WIN01`.

Lab path:

```text
C:\Program Files\SplunkUniversalForwarder\etc\system\local\
```

The forwarder sends Windows and Sysmon telemetry to:

```text
192.168.50.10:9997
```

## Custom Splunk Technical Add-on

`TA-soc-lab-sysmon` contains the search-time field extraction configuration used on `SOC-SPLUNK01`.

Lab path:

```text
/opt/splunk/etc/apps/TA-soc-lab-sysmon/
```

It provides automatic extraction for:

- `XmlWinEventLog:Microsoft-Windows-Sysmon/Operational`
- `XmlWinEventLog:Security`

Fields include:

- `EventCode`
- `User`
- `Image`
- `CommandLine`
- `ParentImage`
- `ParentCommandLine`
- `TargetFilename`
- `TargetObject`
- `DestinationIp`
- `DestinationPort`
- `TargetUserName`
- `IpAddress`
- `WorkstationName`
- `LogonType`
- `Status`
- `SubStatus`

## Safety

These files contain only lab configuration. No passwords, API keys, private certificates, or production credentials are included.
