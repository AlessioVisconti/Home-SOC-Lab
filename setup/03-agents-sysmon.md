# 03 — Wazuh Agents & Sysmon
 
**[🇬🇧 English](#english) · [🇮🇹 Italiano](#italiano)**
 
---
 
<a id="english"></a>
 
## Goal
Install the Wazuh agent on each victim VM so the SIEM receives their logs in real time.
On Windows, also install Sysmon to enrich telemetry with detailed process,
network and file activity events.
 
---
 
## Ubuntu-Victim agent
 
### VM Specs
 
| Field | Value |
|-------|-------|
| Name | Ubuntu-Victim |
| OS | Ubuntu 22.04.5 LTS |
| RAM | 2048 MB |
| CPU | 1 |
| Disk | 20 GB |
| Network | NAT Network — SOC-Lab-Net — 10.0.0.6 |
 
### Install the agent
```bash
# Download
wget -O wazuh-agent.deb \
  https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.12.0-1_amd64.deb
 
# Install and point to the SIEM
sudo WAZUH_MANAGER='10.0.0.5' dpkg -i wazuh-agent.deb
 
# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```
 
### Verify
```bash
sudo systemctl status wazuh-agent
```
Should show `active (running)`.
 
Check the Wazuh dashboard → **Endpoints**: the agent `ubuntu-victim` should
appear as **Active**.
 
---
 
## Windows-Victim agent
 
### VM Specs
 
| Field | Value |
|-------|-------|
| Name | Windows-Victim |
| OS | Windows 11 Pro 64-bit |
| RAM | 4096 MB |
| CPU | 2 |
| Disk | 50 GB |
| Network | NAT Network — SOC-Lab-Net |
 
### Notes on installation
 
Windows-Victim runs on an isolated internal network (SOC-Lab-Net) with no internet access.
The Wazuh agent MSI was downloaded on the host machine and transferred to the VM via
VirtualBox drag-and-drop (Guest Additions required).
 
The VM was installed without UEFI/Secure Boot (disabled in VirtualBox settings) and without
a Microsoft account (local account created using the `oobe\bypassnro` workaround).
TPM checks were bypassed during setup using the LabConfig registry keys:
 
```
reg add HKLM\SYSTEM\Setup\LabConfig /v BypassTPMCheck /t REG_DWORD /d 1 /f
reg add HKLM\SYSTEM\Setup\LabConfig /v BypassSecureBootCheck /t REG_DWORD /d 1 /f
```
 
### Install the agent
 
1. Download `wazuh-agent-4.12.0-1.msi` from the host and drag it into the VM desktop.
2. Open **PowerShell as Administrator** and run:
```powershell
msiexec.exe /i C:\Users\Alessio\Desktop\wazuh-agent-4.12.0-1.msi /q WAZUH_MANAGER='10.0.0.5' WAZUH_AGENT_NAME='windows-victim'
```
 
3. Start the service:
```powershell
NET START WazuhSvc
```
 
### Verify
 
Check the Wazuh dashboard → **Endpoints**: the agent `windows-victim` (ID 002)
should appear as **Active**.
 
---
 
## Sysmon (Windows only)
 
Sysmon (System Monitor) is a Windows service that logs detailed events not captured
by the default Windows Event Log:
 
| Event ID | What it logs |
|----------|-------------|
| 1 | Process creation |
| 3 | Network connections |
| 7 | Image/DLL loaded |
| 11 | File creation |
| 12/13 | Registry changes |
| 22 | DNS queries |
 
### Install
 
Files downloaded on the host and transferred to the VM via drag-and-drop:
- `Sysmon.zip` from [Sysinternals](https://download.sysinternals.com/files/Sysmon.zip)
- `sysmonconfig-export.xml` from [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)
```powershell
# Extract
Expand-Archive C:\Users\Alessio\Desktop\Sysmon.zip -DestinationPath C:\Users\Alessio\Desktop\Sysmon
 
# Install with SwiftOnSecurity config
C:\Users\Alessio\Desktop\Sysmon\Sysmon64.exe -accepteula -i C:\Users\Alessio\Desktop\sysmonconfig-export.xml
```
 
Expected output:
```
Sysmon64 installed.
SysmonDrv installed.
SysmonDrv started.
Sysmon64 started.
```
 
---
 
## Agent status summary
 
| VM | Agent installed | Status |
|----|----------------|--------|
| Ubuntu-Victim (10.0.0.6) | ✅ | Active |
| Windows-Victim | ✅ + Sysmon | Active |
 
<br>
---
---
 
<br>
<a id="italiano"></a>
 
## Obiettivo
Installare l'agent Wazuh su ogni VM vittima in modo che il SIEM riceva i loro log in tempo reale.
Su Windows, installare anche Sysmon per arricchire la telemetria con eventi dettagliati
su processi, connessioni di rete e attività sui file.
 
---
 
## Agent su Ubuntu-Victim
 
### Specifiche della VM
 
| Campo | Valore |
|-------|--------|
| Nome | Ubuntu-Victim |
| OS | Ubuntu 22.04.5 LTS |
| RAM | 2048 MB |
| CPU | 1 |
| Disco | 20 GB |
| Rete | NAT Network — SOC-Lab-Net — 10.0.0.6 |
 
### Installazione dell'agent
```bash
# Download
wget -O wazuh-agent.deb \
  https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.12.0-1_amd64.deb
 
# Installazione con puntamento al SIEM
sudo WAZUH_MANAGER='10.0.0.5' dpkg -i wazuh-agent.deb
 
# Abilitazione e avvio
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```
 
### Verifica
```bash
sudo systemctl status wazuh-agent
```
Deve mostrare `active (running)`.
 
Controlla nella dashboard Wazuh → **Endpoints**: l'agent `ubuntu-victim` deve
apparire come **Active**.
 
---
 
## Agent su Windows-Victim
 
### Specifiche della VM
 
| Campo | Valore |
|-------|--------|
| Nome | Windows-Victim |
| OS | Windows 11 Pro 64-bit |
| RAM | 4096 MB |
| CPU | 2 |
| Disco | 50 GB |
| Rete | NAT Network — SOC-Lab-Net |
 
### Note sull'installazione
 
Windows-Victim gira sulla rete interna isolata (SOC-Lab-Net) senza accesso a internet.
L'MSI dell'agent Wazuh è stato scaricato sul PC host e trasferito nella VM tramite
drag-and-drop di VirtualBox (richiede le Guest Additions).
 
La VM è stata installata senza UEFI/Secure Boot (disabilitati nelle impostazioni VirtualBox)
e senza account Microsoft (account locale creato tramite il workaround `oobe\bypassnro`).
I controlli TPM sono stati bypassati durante il setup tramite le chiavi di registro LabConfig:
 
```
reg add HKLM\SYSTEM\Setup\LabConfig /v BypassTPMCheck /t REG_DWORD /d 1 /f
reg add HKLM\SYSTEM\Setup\LabConfig /v BypassSecureBootCheck /t REG_DWORD /d 1 /f
```
 
### Installazione agent
 
1. Scaricare `wazuh-agent-4.12.0-1.msi` dall'host e trascinarlo sul desktop della VM.
2. Aprire **PowerShell come Amministratore** e lanciare:
```powershell
msiexec.exe /i C:\Users\Alessio\Desktop\wazuh-agent-4.12.0-1.msi /q WAZUH_MANAGER='10.0.0.5' WAZUH_AGENT_NAME='windows-victim'
```
 
3. Avviare il servizio:
```powershell
NET START WazuhSvc
```
 
### Verifica
 
Controlla nella dashboard Wazuh → **Endpoints**: l'agent `windows-victim` (ID 002)
deve apparire come **Active**.
 
---
 
## Sysmon (solo Windows)
 
Sysmon (System Monitor) è un servizio Windows che registra eventi dettagliati non catturati
dal Windows Event Log predefinito:
 
| Event ID | Cosa registra |
|----------|--------------|
| 1 | Creazione processi |
| 3 | Connessioni di rete |
| 7 | DLL/immagini caricate |
| 11 | Creazione file |
| 12/13 | Modifiche al registro |
| 22 | Query DNS |
 
### Installazione
 
File scaricati sull'host e trasferiti nella VM tramite drag-and-drop:
- `Sysmon.zip` da [Sysinternals](https://download.sysinternals.com/files/Sysmon.zip)
- `sysmonconfig-export.xml` da [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)
```powershell
# Estrazione
Expand-Archive C:\Users\Alessio\Desktop\Sysmon.zip -DestinationPath C:\Users\Alessio\Desktop\Sysmon
 
# Installazione con config SwiftOnSecurity
C:\Users\Alessio\Desktop\Sysmon\Sysmon64.exe -accepteula -i C:\Users\Alessio\Desktop\sysmonconfig-export.xml
```
 
Output atteso:
```
Sysmon64 installed.
SysmonDrv installed.
SysmonDrv started.
Sysmon64 started.
```
 
---
 
## Riepilogo stato degli agent
 
| VM | Agent installato | Stato |
|----|-----------------|-------|
| Ubuntu-Victim (10.0.0.6) | ✅ | Attivo |
| Windows-Victim | ✅ + Sysmon | Attivo |
