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
 
Check the Wazuh dashboard → **Agents Summary**: the agent `ubuntu-victim` should
appear as **Active**.
 
---
 
## Windows-Victim agent
 
> ⏳ To be completed
 
### VM Specs (planned)
 
| Field | Value |
|-------|-------|
| Name | Windows-Victim |
| OS | Windows 11 64-bit |
| RAM | 4096 MB |
| CPU | 2 |
| Disk | 50 GB |
| Network | NAT Network — SOC-Lab-Net — TBD |
 
### Steps (planned)
1. Download the Wazuh agent MSI installer from the dashboard
2. Run with `WAZUH_MANAGER=10.0.0.5` parameter
3. Install **Sysmon** with the SwiftOnSecurity config for rich telemetry
4. Verify the agent appears in the dashboard
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
 
### Install (planned)
```powershell
# Download Sysmon
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile Sysmon.zip
Expand-Archive Sysmon.zip
 
# Download SwiftOnSecurity config
Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile sysmonconfig.xml
 
# Install
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```
 
---
 
## Agent status summary
 
| VM | Agent installed | Status |
|----|----------------|--------|
| Ubuntu-Victim (10.0.0.6) | ✅ | Active |
| Windows-Victim | 🔜 Planned | — |
 
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
 
Controlla nella dashboard Wazuh → **Agents Summary**: l'agent `ubuntu-victim` deve
apparire come **Active**.
 
---
 
## Agent su Windows-Victim
 
> ⏳ Da completare
 
### Specifiche della VM (pianificate)
 
| Campo | Valore |
|-------|--------|
| Nome | Windows-Victim |
| OS | Windows 11 64-bit |
| RAM | 4096 MB |
| CPU | 2 |
| Disco | 50 GB |
| Rete | NAT Network — SOC-Lab-Net — TBD |
 
### Passaggi (pianificati)
1. Scaricare l'installer MSI dell'agent Wazuh dalla dashboard
2. Eseguirlo con il parametro `WAZUH_MANAGER=10.0.0.5`
3. Installare **Sysmon** con la config di SwiftOnSecurity per telemetria ricca
4. Verificare che l'agent appaia nella dashboard
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
 
### Installazione (pianificata)
```powershell
# Download Sysmon
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -OutFile Sysmon.zip
Expand-Archive Sysmon.zip
 
# Download config SwiftOnSecurity
Invoke-WebRequest -Uri https://raw.githubusercontent.com/SwiftOnSecurity/sysmon-config/master/sysmonconfig-export.xml -OutFile sysmonconfig.xml
 
# Installazione
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```
 
---
 
## Riepilogo stato degli agent
 
| VM | Agent installato | Stato |
|----|-----------------|-------|
| Ubuntu-Victim (10.0.0.6) | ✅ | Attivo |
| Windows-Victim | 🔜 Pianificato | — |
