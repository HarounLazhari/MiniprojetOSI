# 🔧 Projet de Lab Cybersécurité : Sécurisation par couches OSI (Etapes 1 à 4)

## ✅ Objectif global

Réaliser un lab pratique de cybersécurité en simulant une architecture réseau typique entreprise (LAN / DMZ) et en reproduisant des attaques réseau réelles jusqu'à la couche 4 du modèle OSI.

---

## 🛠️ Etape 1 : Mise en place du réseau (Couches 1, 2, 3)

### Objectif

* Créer deux réseaux séparés (LAN et DMZ)
* Installer pfSense comme firewall entre les deux
* Configurer les adresses IP statiques

### Topologie

```
Kali      Windows         pfSense            Ubuntu
 [LAN] --- [LAN] ---- [LAN | DMZ] ---- [DMZ]
192.168.1.20   192.168.1.10   192.168.1.1 | 192.168.2.1   192.168.2.100
```

### Configuration IP

* Kali : `192.168.1.20/24` via `eth0`
* Windows : `192.168.1.10/24`
* Ubuntu Server : `192.168.2.100/24` via `enp0s3`
* pfSense :

  * LAN : `192.168.1.1/24`
  * DMZ : `192.168.2.1/24`

### NAT / Routage

* NAT activé sur l'interface WAN (si Internet requis)
* Routes statiques configurées automatiquement par pfSense

### Test de connectivité

* Ping entre Kali et Windows : ✅ OK
* Ping vers pfSense LAN / DMZ : ✅ OK
* Ping Ubuntu → pfSense : ✅ OK (après modification des règles pfSense)

---

## 🔒 Etape 2 : Sécuriser la couche 2 - ARP Spoofing

### Objectif

* Simuler une attaque de type ARP Spoofing depuis Kali vers Windows

### Commandes utilisées

```bash
sudo apt install dsniff
sudo arpspoof -i eth0 -t 192.168.1.10 192.168.1.1
```

### Vérification sur Windows

```cmd
arp -a
```

➜ Adresse MAC du gateway usurpée par Kali

### Contre-mesures (non réalisées mais mentionnées)

* Simulation de DHCP Snooping / ARP statique
* Proxy ARP désactivé sur pfSense

---

## 🔒 Etape 3 : Sécuriser la couche 3 - IP Spoofing & Firewall

### Objectif

* Tester le comportement du firewall face à des paquets IP falsifiés
* Simuler un accès non autorisé à la DMZ

### Commande depuis Kali

```bash
sudo hping3 -a 1.2.3.4 -S 192.168.2.100 -p 80
```

### Analyse avec Wireshark (sur Ubuntu)

* Aucun paquet reçu ➜ pfSense a bien bloqué les paquets non sollicités (règle de "stateful firewall")

### Règle appliquée

* LAN → DMZ : seulement les connexions initiées par DMZ autorisées

---

## 🛋️ Etape 4 : Sécuriser la couche 4 - TCP SYN Flood

### Objectif

* Simuler une attaque par inondation de paquets SYN
* Observer les protections de pfSense

### Commande depuis Kali

```bash
sudo hping3 -S 192.168.2.100 -p 80 --flood
```

### Observation

* Charge artificielle sur l'interface DMZ
* Possibilité d'activer la fonction `pf synproxy` (non testée)

---

## 🔹 Conclusion

Jusqu'à l'étape 4, nous avons :

* Mis en place une vraie segmentation réseau
* Simulé 3 attaques réseau classiques (ARP spoof, IP spoof, SYN flood)
* Utilisé pfSense pour contrôler, analyser et bloquer le trafic

> Ce lab constitue une base très solide pour la compréhension de la sécurité réseau appliquée aux couches 2 à 4 du modèle OSI.

✅ Prêt à être utilisé dans un portfolio, une soutenance ou comme base pour de futurs labs (IDS, HTTPS, XSS...).
