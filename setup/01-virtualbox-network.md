# 01 — VirtualBox Network Setup
 
**[🇬🇧 English](#english) · [🇮🇹 Italiano](#italiano)**
 
---
 
<a id="english"></a>
 
## Goal
Create an isolated internal network so all lab VMs can communicate with each other
without exposing attack traffic to the public internet or the home LAN.
 
## Network type: NAT Network
 
VirtualBox offers several network modes. The one used in this lab is **NAT Network**:
 
| Mode | VMs see each other | Internet access | Traffic isolated |
|------|--------------------|-----------------|-----------------|
| NAT (simple) | ❌ | ✅ | ✅ |
| **NAT Network** | ✅ | ✅ | ✅ |
| Bridge | ✅ | ✅ | ❌ |
| Host-only | ✅ | ❌ | ✅ |
 
NAT Network is the right choice: VMs can reach each other (agents → SIEM) and can
download packages from the internet, but attack traffic never reaches the real network.
 
## Steps
 
### 1. Open the Network Manager
In VirtualBox, switch to **Expert Mode**, then go to:
`File → Tools → Network Manager → NAT Networks tab`
 
### 2. Create the network
Click **Create (+)** and set:
 
| Field | Value |
|-------|-------|
| Name | `SOC-Lab-Net` |
| IPv4 Prefix | `10.0.0.0/24` |
| DHCP | Enabled ✓ |
| IPv6 | Disabled |
 
Click **Apply**.
 
### 3. Assign the network to each VM
For every VM in the lab, go to:
`Settings → Network → Adapter 1`
 
Set:
- **Attached to:** NAT Network
- **Name:** SOC-Lab-Net
## Dashboard access (Wazuh only)
To reach the Wazuh dashboard from the host browser, a second adapter is needed:
 
`Settings → Network → Adapter 2`
- **Attached to:** Host-only Adapter
- **Name:** VirtualBox Host-Only Ethernet Adapter
Then configure it in `/etc/netplan/99-host-only.yaml` inside the VM:
 
```yaml
network:
    version: 2
    ethernets:
        enp0s8:
            dhcp4: true
```
 
Apply with:
```bash
sudo chmod 600 /etc/netplan/99-host-only.yaml
sudo netplan apply
```
 
## IP addresses assigned
 
| VM | Interface | IP |
|----|-----------|-----|
| Wazuh-SIEM | enp0s3 (SOC-Lab-Net) | 10.0.0.5 |
| Wazuh-SIEM | enp0s8 (Host-only) | 192.168.56.102 |
| Ubuntu-Victim | enp0s3 (SOC-Lab-Net) | 10.0.0.6 |
| Windows-Victim | TBD | TBD |
| Kali-Attacker | TBD | TBD |
 
<br>
---
---
 
<br>
<a id="italiano"></a>
 
## Obiettivo
Creare una rete interna isolata in modo che tutte le VM del lab possano comunicare tra loro
senza esporre il traffico di attacco a internet o alla LAN di casa.
 
## Tipo di rete: NAT Network
 
VirtualBox offre diverse modalità di rete. Quella usata in questo lab è la **NAT Network**:
 
| Modalità | Le VM si vedono | Accesso internet | Traffico isolato |
|----------|----------------|-----------------|-----------------|
| NAT (semplice) | ❌ | ✅ | ✅ |
| **NAT Network** | ✅ | ✅ | ✅ |
| Bridge | ✅ | ✅ | ❌ |
| Host-only | ✅ | ❌ | ✅ |
 
La NAT Network è la scelta giusta: le VM si raggiungono tra loro (agent → SIEM) e possono
scaricare pacchetti da internet, ma il traffico di attacco non raggiunge mai la rete reale.
 
## Passaggi
 
### 1. Apri il Network Manager
In VirtualBox, passa alla **Modalità Esperto**, poi vai su:
`File → Strumenti → Network Manager → scheda Reti con NAT`
 
### 2. Crea la rete
Clicca **Crea (+)** e imposta:
 
| Campo | Valore |
|-------|--------|
| Nome | `SOC-Lab-Net` |
| Prefisso IPv4 | `10.0.0.0/24` |
| DHCP | Abilitato ✓ |
| IPv6 | Disabilitato |
 
Clicca **Applica**.
 
### 3. Assegna la rete a ogni VM
Per ogni VM del lab vai su:
`Impostazioni → Rete → Scheda 1`
 
Imposta:
- **Connessa a:** Rete con NAT
- **Nome:** SOC-Lab-Net
## Accesso alla dashboard (solo Wazuh)
Per raggiungere la dashboard Wazuh dal browser del portatile host, serve un secondo adattatore:
 
`Impostazioni → Rete → Scheda 2`
- **Connessa a:** Scheda solo host
- **Nome:** VirtualBox Host-Only Ethernet Adapter
Poi configurarlo in `/etc/netplan/99-host-only.yaml` dentro la VM:
 
```yaml
network:
    version: 2
    ethernets:
        enp0s8:
            dhcp4: true
```
 
Applicare con:
```bash
sudo chmod 600 /etc/netplan/99-host-only.yaml
sudo netplan apply
```
 
## Indirizzi IP assegnati
 
| VM | Interfaccia | IP |
|----|-------------|-----|
| Wazuh-SIEM | enp0s3 (SOC-Lab-Net) | 10.0.0.5 |
| Wazuh-SIEM | enp0s8 (Host-only) | 192.168.56.102 |
| Ubuntu-Victim | enp0s3 (SOC-Lab-Net) | 10.0.0.6 |
| Windows-Victim | TBD | TBD |
| Kali-Attacker | TBD | TBD |
