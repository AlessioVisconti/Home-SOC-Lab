# 02 — Wazuh SIEM Installation
 
**[🇬🇧 English](#english) · [🇮🇹 Italiano](#italiano)**
 
---
 
<a id="english"></a>
 
## Goal
Deploy Wazuh (all-in-one: Manager + Indexer + Dashboard) on Ubuntu 22.04 LTS,
accessible from the host browser at `https://192.168.56.102`.
 
## VM Specs
 
| Field | Value |
|-------|-------|
| Name | WazuhSiem |
| OS | Ubuntu 22.04.5 LTS (Jammy Jellyfish) |
| RAM | 8192 MB |
| CPU | 2 |
| Disk | 50 GB |
| Network 1 | NAT Network — SOC-Lab-Net — 10.0.0.5 |
| Network 2 | Host-only — 192.168.56.102 |
 
> **Note:** Ubuntu 22.04 LTS is required. Ubuntu 24.x/26.x are not yet officially
> supported by Wazuh and the dashboard installation will fail.
 
> **Note:** The disk must be at least 50 GB. With a 25 GB disk, the LVM logical
> volume defaults to ~12 GB which is insufficient for the dashboard package.
 
## Installation steps
 
### 1. Download the install script
```bash
wget https://packages.wazuh.com/4.12/wazuh-install.sh
```
 
### 2. Run the all-in-one installer
```bash
sudo bash wazuh-install.sh -a
```
 
The script installs and starts three services:
- `wazuh-indexer` — OpenSearch-based data store
- `wazuh-manager` — analysis engine and agent manager
- `wazuh-dashboard` — web UI (port 443)
Installation takes ~10-15 minutes.
 
### 3. Retrieve credentials
```bash
sudo tar -xOf ~/wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```
 
### 4. Reset the admin password (optional)
```bash
sudo /usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh \
  -u admin -p 'YourNewPassword1*'
```
 
Password requirements: 8-64 characters, at least one uppercase, one lowercase,
one number and one symbol from `.*?-`.
 
## Verify all services are running
```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```
 
All three should show `active (running)`.
 
## Access the dashboard
```
https://192.168.56.102
```
 
Accept the self-signed certificate warning, then log in with `admin` credentials.
 
## Troubleshooting
 
| Issue | Cause | Fix |
|-------|-------|-----|
| Dashboard install fails on Ubuntu 26.04 | Unsupported OS | Use Ubuntu 22.04 LTS |
| Disk full during install | LVM uses only half the disk by default | Use 50 GB disk |
| Ports 1515/55000 busy | Leftover processes from failed install | `sudo fuser -k 1515/tcp && sudo fuser -k 55000/tcp` then reboot |
| enp0s8 goes DOWN after reboot | cloud-init overwrites netplan | Create `/etc/netplan/99-host-only.yaml` with `chmod 600` |
 
<br>
---
---
 
<br>
<a id="italiano"></a>
 
## Obiettivo
Installare Wazuh (all-in-one: Manager + Indexer + Dashboard) su Ubuntu 22.04 LTS,
accessibile dal browser del portatile host su `https://192.168.56.102`.
 
## Specifiche della VM
 
| Campo | Valore |
|-------|--------|
| Nome | WazuhSiem |
| OS | Ubuntu 22.04.5 LTS (Jammy Jellyfish) |
| RAM | 8192 MB |
| CPU | 2 |
| Disco | 50 GB |
| Rete 1 | NAT Network — SOC-Lab-Net — 10.0.0.5 |
| Rete 2 | Host-only — 192.168.56.102 |
 
> **Nota:** è richiesto Ubuntu 22.04 LTS. Ubuntu 24.x/26.x non sono ancora supportati
> ufficialmente da Wazuh e l'installazione della dashboard fallisce.
 
> **Nota:** il disco deve essere almeno 50 GB. Con 25 GB il volume logico LVM viene
> creato a ~12 GB, insufficiente per il pacchetto dashboard.
 
## Passaggi di installazione
 
### 1. Scarica lo script di installazione
```bash
wget https://packages.wazuh.com/4.12/wazuh-install.sh
```
 
### 2. Esegui l'installer all-in-one
```bash
sudo bash wazuh-install.sh -a
```
 
Lo script installa e avvia tre servizi:
- `wazuh-indexer` — storage basato su OpenSearch
- `wazuh-manager` — motore di analisi e gestore degli agent
- `wazuh-dashboard` — interfaccia web (porta 443)
L'installazione richiede ~10-15 minuti.
 
### 3. Recupera le credenziali
```bash
sudo tar -xOf ~/wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```
 
### 4. Reset della password admin (opzionale)
```bash
sudo /usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh \
  -u admin -p 'NuovaPassword1*'
```
 
Requisiti password: 8-64 caratteri, almeno una maiuscola, una minuscola,
un numero e un simbolo tra `.*?-`.
 
## Verifica che tutti i servizi siano attivi
```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```
 
Tutti e tre devono mostrare `active (running)`.
 
## Accesso alla dashboard
```
https://192.168.56.102
```
 
Accetta l'avviso del certificato auto-firmato, poi accedi con le credenziali `admin`.
 
## Risoluzione dei problemi
 
| Problema | Causa | Soluzione |
|----------|-------|-----------|
| Dashboard fallisce su Ubuntu 26.04 | OS non supportato | Usa Ubuntu 22.04 LTS |
| Disco pieno durante l'installazione | LVM usa solo metà del disco per default | Usa un disco da 50 GB |
| Porte 1515/55000 occupate | Processi rimasti da installazione fallita | `sudo fuser -k 1515/tcp && sudo fuser -k 55000/tcp` poi riavvia |
| enp0s8 va DOWN dopo il riavvio | cloud-init sovrascrive netplan | Crea `/etc/netplan/99-host-only.yaml` con `chmod 600` |
