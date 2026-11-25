# pfsense-webmin-lab

Lab d'administration réseau : **pfSense + Webmin sous VirtualBox**

## 📋 Sommaire

- [Objectifs](#objectifs)
- [Architecture du lab](#architecture-du-lab)
- [Partie 1 : Webmin](#partie-1--webmin)
  - [Présentation](#présentation-de-webmin)
  - [Préparation du travail](#préparation-du-travail)
  - [Accès à l'interface Webmin](#accès-à-linterface-webmin)
  - [Informations système affichées](#informations-système-affichées)
  - [Détails techniques](#détails-techniques)
  - [Menu Webmin](#explication-des-éléments-du-menu-webmin)
- [Partie 2 : pfSense](#partie-2--pfsense)
  - [Lancement des VMs](#après-linstallation-lancer-les-vms)
  - [Accès à l'interface web](#accès-à-linterface-web)
  - [Règles Firewall](#règles-firewall)
  - [NAT et redirection de port](#nat-et-redirection-de-port)
- [Technologies utilisées](#technologies-utilisées)

---

## Objectifs

### Webmin
- Installer et configurer l'interface Webmin sur un serveur Linux.
- Administrer les services et ressources via une interface web.
- Comparer l'administration manuelle et l'automatisation par Ansible.
- Déployer automatiquement Webmin.

### pfSense
- Installer pfSense sur une machine virtuelle.
- Comprendre le rôle d'un firewall/routeur dans un réseau.
- Configurer les interfaces WAN et LAN.
- Accéder à l'interface web de pfSense.
- Configurer le serveur DHCP.
- Créer des règles de filtrage (firewall).
- Mettre en place du NAT (LAN → WAN et port forwarding).
- Tester et superviser le trafic réseau.

## Architecture du lab

| Machine | Rôle | Réseau |
|---------|------|--------|
| pfSense (VM) | Firewall / Routeur (WAN + LAN) | VMnet0 (WAN) / VMnet2 (LAN) |
| srv-master (Ubuntu 24.04, VM) | Serveur Webmin + Apache2 | VMnet2 (LAN) — IP : 192.168.1.51 |
| Kali Linux (VM) | Machine de test | VMnet2 (LAN) |

---

# Partie 1 : Webmin

## Présentation de Webmin

> Webmin est un outil d'administration système accessible via navigateur.  
> Il permet de gérer les utilisateurs, services, réseaux, pare-feu, sauvegardes, etc.  
> Interface d'accès : `https://<IP>:10000`

## Préparation du travail

![Préparation du travail](images/Préparation%20du%20travail.png)

## Accès à l'interface Webmin

Connexion via `https://<IP>:10000` depuis le navigateur.

![Accès à l’interface Webmin](images/Accès%20à%20l’interface%20Webmin.png)

![Accès à l’interface Webmin 2](images/Accès%20à%20l’interface%20Webmin2.png)

## Informations système affichées

- **Utilisation CPU** : 17 %
- **Mémoire réelle** : 12 %
- **Mémoire virtuelle** : 0 %
- **Espace disque local** : 56 %

## Détails techniques

| Élément | Valeur |
|---------|--------|
| **Nom d'hôte** | srv-master |
| **OS** | Ubuntu Linux 24.04.3 |
| **Webmin** | Version 2.600, thème Authentic v26.10 |
| **Date & heure système** | 22 novembre 2025, 20:28 |
| **Noyau et CPU** | Linux 6.8.0-87-generic sur Intel Core i7-1265U (2 cœurs) |
| **Uptime** | 30 minutes |
| **Processus actifs** | 215 |
| **Charge CPU (1, 5, 15 min)** | 3.58, 0.94, 0.36 |

## Explication des éléments du menu Webmin

| Menu | Description |
|------|-------------|
| **Webmin** | Accès aux paramètres et modules Webmin : gestion des utilisateurs et configuration principale du serveur. |
| **System** | Configuration et surveillance du système d'exploitation : pare-feu, tâches planifiées (cron), gestion des comptes. |
| **Servers** | Administration des serveurs installés : web (Apache/Nginx), bases de données, serveurs mail, etc. |
| **Tools** | Outils de maintenance : éditeur de texte, gestionnaire de fichiers, gestion de paquets. |
| **Networking** | Configuration réseau : interfaces, routage, VPN, pare-feu, DNS. |
| **Hardware** | Informations et gestion du matériel : stockage, partitions, monitoring des performances physiques. |
| **Cluster** | Administration de plusieurs serveurs/VM ensemble via la fonctionnalité de cluster. |
| **Un-used Modules** | Modules installés mais non utilisés ou non configurés. |
| **Refresh Modules** | Recharger ou mettre à jour la liste des modules disponibles. |

---

# Partie 2 : pfSense

## Après l'installation, lancer les VMs

Lancer pfSense, puis redémarrer la VM Kali Linux.

![Après l'installation, lancer les VMs](images/Après%20l'installation%2C%20lancer%20les%20VMs.png)

![après redémarrer le VM kali-linux](images/après%20redémarrer%20le%20VM%20kali-linux.png)

## Accès à l'interface web

1. Lancer la VM Ubuntu, vérifier que son interface est sur **VMnet2**.
2. Confirmer l'obtention d'une IP du DHCP pfSense : `ip a`
3. Accéder à **https://192.168.1.51** depuis Ubuntu, se connecter (`admin / pfsense`).
4. Suivre l'assistant (hostname : `pfsense`, timezone : `Africa/Algiers`, etc.) et **changer le mot de passe admin**.

![Accès à l'interface web](images/Accès%20à%20l'interface%20web.jpg)

## Règles Firewall

1. Créer un alias URL nommé *`blocked_sites`** pointant vers Facebook.
2. Ajouter une règle LAN :
   - **Action** : Block
   - **Source** : LAN net
   - **Destination** : `blocked_sites`
3. Tester depuis Ubuntu : l'accès à Facebook doit être bloqué, les autres sites restent accessibles.

## NAT et redirection de port

1. Installer Apache2 sur Ubuntu :
   ```bash
   sudo apt install apache2 -y
