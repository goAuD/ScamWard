# CrowdSec Windows Install Guide

![Platform](https://img.shields.io/badge/platform-windows-blue)
![Tool](https://img.shields.io/badge/tool-crowdsec-orange)

## 1. Installation

1. Download the latest MSI installer from:
   [https://github.com/crowdsecurity/crowdsec/releases](https://github.com/crowdsecurity/crowdsec/releases)
   (look for `crowdsec-x.y.z-windows-amd64.msi`)

2. Run the installer:
   - Right-click → **Run as Administrator**
   - Default settings are fine.

3. Installed paths:
   - Binaries: `C:\Program Files\CrowdSec\`
   - Config + logs: `C:\ProgramData\CrowdSec\`

4. Service:
   - Installed as `Crowdsec` (runs under **Local System account** by default).

## 2. Basic Commands

Open PowerShell **as Administrator** and go to the install folder:

```powershell
cd "C:\Program Files\CrowdSec"
```

## Service control

```powershell
Get-Service Crowdsec
Start-Service Crowdsec
Stop-Service Crowdsec
Restart-Service Crowdsec
```

## Metrics (check Local API / heartbeat)

```powershell
.\cscli.exe metrics
```

## List scenarios

```powershell
.\cscli.exe scenarios list
```

## Show active bans (decisions)

```powershell
.\cscli.exe decisions list
```

## Add a test ban

```powershell
.\cscli.exe decisions add --ip 1.2.3.4 --duration 5m --reason "test"
```

## Logs

CrowdSec main log (live tail):

```powershell
Get-Content "C:\ProgramData\CrowdSec\log\crowdsec.log" -Tail 50 -Wait
```

## Event Viewer

- Run eventvwr.msc
- Navigate to Applications and Services Logs → CrowdSec

## Quick Notes

- No email/notifier enabled → simpler, less error-prone setup.
- When CrowdSec bans an IP, it will automatically create a Windows Defender Firewall rule.

## To quickly verify bans, check

```powershell
.\cscli.exe decisions list
```

## Troubleshooting

### Service won't start

**Ensure it runs as Local System:**

- services.msc → Crowdsec → Properties → Log On → Local System account

**Or via PowerShell:**

```powershell
sc.exe config Crowdsec obj= "LocalSystem" password= ""
```

**Port 8080 already in use**

### CrowdSec's Local API defaults to 127.0.0.1:8080. Check if it's free

```powershell
Get-NetTCPConnection -LocalPort 8080 -ErrorAction SilentlyContinue
netstat -ano | findstr :8080
```

`Stop or reconfigure any process blocking 8080`

## YAML/config errors

**`If the service fails after editing configs:`**

- YAML must use spaces, not tabs.
- Check for proper indentation.

Look at the log for parse errors:

```powershell
Get-Content "C:\ProgramData\CrowdSec\log\crowdsec.log" -Tail 50
```

`With this clean install, CrowdSec runs as a background service, protects Windows, and you can monitor it via CLI and logs without extra notifiers.`
