# CrowdSec Windows Install Guide – Cheatsheet

## 1. Telepítés

1. Töltsd le a legfrissebb MSI-t innen:  
   👉 [https://github.com/crowdsecurity/crowdsec/releases](https://github.com/crowdsecurity/crowdsec/releases)  
   (fájlnév: `crowdsec-x.y.z-windows-amd64.msi`)

2. Telepítés:
   - Jobb klikk → **Run as Administrator**
   - Alapértelmezett beállítások jók.

3. Könyvtárak:
   - Binárisok: `C:\Program Files\CrowdSec\`
   - Konfigok + logok: `C:\ProgramData\CrowdSec\`

4. Szolgáltatás:  
   - `Crowdsec` néven települ (alapból **Local System** account alatt fut).

## 2. Alap parancsok

PowerShellt **Admin módban** nyisd, és lépj a program könyvtárába:

```powershell
cd "C:\Program Files\CrowdSec"
```

## Szolgáltatás állapot

```powershell
Get-Service Crowdsec
Start-Service Crowdsec
Stop-Service Crowdsec
Restart-Service Crowdsec
```

## Metrikák (LAPI ellenőrzés)

```poweshell
.\cscli.exe metrics
```

## Szenáriók listázása

```powershell
.\cscli.exe scenarios list
```

## Aktív tiltások (döntések)

```powershell
.\cscli.exe decisions list
```

## Teszt tiltás létrehozása

```poweshell
.\cscli.exe decisions add --ip 1.2.3.4 --duration 5m --reason "test"
```

## Logok figyelése

CrowdSec log:

```powershell
Get-Content "C:\ProgramData\CrowdSec\log\crowdsec.log" -Tail 50 -Wait
```

## Event Viewer

- Nyisd: eventvwr.msc
- Menj: Applications and Services Logs → CrowdSec

## Gyors tippek

- Nincs email/notifier → kevesebb hibaforrás, egyszerűbb működés.
- Tűzfal szabályok automatikusan létrejönnek tiltásnál (Windows Defender Firewall → Inbound Rules).

### Rendszeresen nézd

```poweshell
.\cscli.exe decisions list
```
