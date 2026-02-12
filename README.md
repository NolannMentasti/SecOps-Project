# 🛡️ Projet SecOps : Architecture Haute Disponibilité & Supervision SI

Ce dépôt documente la réalisation de la **SAÉ 53**, un projet académique visant à concevoir, déployer et sécuriser une infrastructure d'entreprise critique selon une approche **Zero Trust**.

L'objectif : maintenir la continuité de service (Cluster HA) tout en assurant une visibilité totale sur les menaces (SIEM).

🔗 **Voir le portfolio interactif :** www.nolann-mentasti.fr

## 🏗️ Architecture & Topologie

Le réseau est segmenté en **6 zones de sécurité** étanches pour limiter les mouvements latéraux en cas de compromission.

![Schéma Architecture](images/archi.png)
*(Ajoutez votre capture d'écran PNETLab ici)*

* 🔴 **WAN (VLAN 10) :** Frontière externe non fiable.
* 🟠 **DMZ (VLAN 30) :** Services exposés (Web, Mail, DNS).
* 🟢 **LAN (VLAN 50/70) :** Zone utilisateurs et Active Directory.
* 🔵 **MGMT/SIEM (VLAN 60) :** Zone de supervision isolée (Hors bande).

## 🛠️ Stack Technique

### Cœur de Réseau (Sécurité)
* **Firewalls :** PfSense (Master) + OPNsense (Backup).
* **Protocole HA :** CARP (Common Address Redundancy Protocol) pour la redondance IP (VIP).
* **Synchro :** XMLRPC (Config) + PFSync (États des connexions TCP).

### Supervision & SOC
* **SIEM :** Splunk Enterprise (Centralisation Logs Syslog UDP 514).
* **XDR/EDR :** Wazuh (Détection d'intrusions sur les endpoints).
* **Monitoring :** Zabbix / SNMP v3 (État des switchs Arista).

## 🚀 Fonctionnalités Clés

### 1. Haute Disponibilité (Cluster HA)
Mise en place d'un basculement automatique (Failover) :
* Si le pare-feu Maître tombe, l'Esclave prend le relais en < 1 seconde.
* **Transparence :** Les sessions TCP des utilisateurs ne sont pas coupées (State Synchronization).
* **NAT Outbound :** Configuration manuelle pour sortir avec l'IP Virtuelle (VIP) et non l'IP physique.

### 2. Supervision Active (Blue Team)
* Remontée des logs de blocage Firewall vers **Splunk**.
* Alerting temps réel sur **Wazuh** (ex: modification de fichiers critiques, tentatives SSH échouées).

### 3. Tests d'Intrusion (Red Teaming)
Validation de la robustesse via une machine attaquante (Kali Linux) :
* ❌ **Reconnaissance :** Scan Nmap (`-sS`) détecté et bloqué.
* ❌ **Déni de Service :** Syn Flood absorbé par le cluster.
* ❌ **Mouvement Latéral :** Bloqué par les règles de segmentation VLAN.

## 📂 Contenu du dépôt

* `/configs` : Sauvegardes XML des configurations PfSense et OPNsense.
* `/siem` : Exemples de règles de détection Wazuh customisées.
* `/docs` : Schémas logiques et matrice des flux.

## 💻 Installation / Lab

Ce projet a été virtualisé sous **PNETLab/GNS3**.
Pour reproduire le lab :
1.  Importer les images QEMU (PfSense, Debian, Arista).
2.  Configurer les vSwitchs selon le plan d'adressage (voir `/docs`).
3.  Injecter les configurations firewall.

---
*Auteur : Nolann Mentasti - BUT R&T*
