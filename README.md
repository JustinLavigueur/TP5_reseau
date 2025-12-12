# TP5 – VTAP, Wireshark, HTTPS et Antivirus (Oracle Cloud)

**Cours :** 420-06C-FX – Réseaux  
**Enseignant :** Jean‑Sébastien Nadeau  
**Session :** Automne 2025

**Travail pratique 5 :  Configurer un VTAP, utilisation de Wireshark,
configuration d'un Serveur https et installation d'un antivirus sur Oracle cloud**

**Coéquipiers : Justin Lavigueur et Alexandre Sweeney**

---

## Objectifs du TP
- Héberger un site web **HTTP (80)** et **HTTPS (443)** sur Oracle Cloud
- Générer un **certificat SSL auto‑signé avec OpenSSL** (en remplacement de Certbot)
- Configurer un **VTAP** afin de miroiter le trafic réseau
- Analyser le trafic **HTTP / HTTPS** avec **tcpdump** et **Wireshark**
- Installer et configurer un **antivirus ClamAV** sur l’instance serveur

---

## Architecture du laboratoire
Deux instances Oracle Cloud sont utilisées :
- **Instance serveur** : hébergement du site web (HTTP/HTTPS)
- **Instance analyseur (VTAP)** : réception et analyse du trafic miroir

![Deux instances OCI](imagesTP5/deux_instance.png)

---

## Création et configuration du site web

### Création du fichier `index.html`
Le fichier HTML a été créé et modifié directement sur l’instance serveur.

![Création index.html](imagesTP5/creation_index.png)
![Contenu du fichier index.html](imagesTP5/fichier_index.png)

### Lancement du serveur HTTP (port 80)
```bash
sudo python3 -m http.server 80
```

![Serveur HTTP actif](imagesTP5/lancement_python80.png)
![Page index – HTTP](imagesTP5/serveur_80_index.png)

---

## HTTPS avec certificat SSL auto‑signé (OpenSSL)

**Certbot n’a pas été utilisé**. Il a été **remplacé par OpenSSL**.

### Installation d’OpenSSL
```bash
sudo apt install openssl
```

### Génération du certificat
```bash
sudo openssl genrsa -out server.key 2048
sudo openssl req -new -key server.key -out server.csr
sudo openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
```

![Création certificat SSL](imagesTP5/openssl_certificate.png)
![CSR et informations SSL](imagesTP5/openssl_certificate_2.png)
![Certificat auto‑signé créé](imagesTP5/openssl_certificate_3.png)
![Copie des certificats SSL](imagesTP5/openssl_certificate_4.png)

### Lancement du serveur HTTPS (port 443)
```bash
sudo python3 https_server.py
```

![Serveur HTTPS actif](imagesTP5/lancement_python443.png)
![Page index – HTTPS](imagesTP5/serveur_443_index.png)

> Le navigateur affiche « Non sécurisé », ce qui est **normal** avec un certificat auto‑signé, mais le chiffrement TLS est fonctionnel.

---

## Configuration du Network Load Balancer (NLB)

Le NLB est utilisé comme **cible du VTAP**.

![NLB – Vue générale](imagesTP5/nlb.png)

### Backend set VTAP
![Backend NLB](imagesTP5/nlb_backend.png)

### Listener UDP 4789 (VXLAN)
![Listener UDP 4789](imagesTP5/nlb_listener_4789.png)

---

## Configuration du VTAP

Le VTAP permet de **miroiter le trafic** de l’instance serveur vers l’instance analyseur. En fait, un point d'accès virtuel de test (VTAP) reproduit le trafic d'une source choisie vers une
destination définie afin de faciliter le dépannage, l'analyse de sécurité et la surveillance. Le
VTAP s'appuie sur un filtre d'entrée — un ensemble de règles déterminant quel trafic est
dupliqué.

### Connexion à Oracle Cloud
- On se connecte à notre compte OCI via le navigateur web.
- Par la suite, on accéde au tableau de bord principal d’`Oracle Cloud`.
![Connexion oracle cloud](imagesTP5/connexionOracle.png)

![Création du VTAP](imagesTP5/vtap_creation.png)
![VTAP actif](imagesTP5/vtap.png)

- **Source** : VNIC de l’instance serveur
- **Target** : Network Load Balancer
- **Filtre** : ANY

---

## Capture du trafic réseau

### Installation de tcpdump
```bash
sudo apt install tcpdump
```

![Installation tcpdump](imagesTP5/install_tcpdump.png)

### Capture du trafic
```bash
sudo tcpdump -i ens3 -w capture.pcap
```

Le fichier de capture est ensuite analysé avec **Wireshark**.

On curl pour envoyé des paquets

```bash
curl http://151.145.40.146
```
![Curl port80](imagesTP5/curl_80.png)


```bash
curl -k https://151.145.40.146
```
![Curl port443](imagesTP5/curl_443.png)

---

## Analyse avec Wireshark

### Capture du trafic HTTP
Dans Wireshark, les requêtes HTTP GET sont visibles.

![Capture HTTP](imagesTP5/wireshark_capture.png)

**10.0.0.2 correspond à l’adresse IP privée de l’instance serveur**, dont le trafic est capturé via le VTAP par l’instance analyseur.

### Capture du trafic VTAP (VXLAN)
Le filtre suivant a été utilisé :
```
udp.port == 4789
```

![Capture VXLAN 4789](imagesTP5/capture_wireshark_4789.png)

Cela confirme que le trafic est bien **miroité par le VTAP**.

---

## Installation et configuration de l’antivirus ClamAV

### Installation
```bash
sudo apt install clamav clamav-daemon
```

![Installation ClamAV](imagesTP5/install_antivirus.png)
![Installation clamd](imagesTP5/Install_clamav.png)

### Activation et mise à jour
```bash
sudo systemctl start clamav-daemon
sudo freshclam
```

![Démarrage antivirus](imagesTP5/start_antivirus.png)
![Mise à jour signatures](imagesTP5/sudo_freshcam.png)

---

## Sécurité réseau

Les règles suivantes ont été configurées dans les **Security Lists** :
- TCP 80 (HTTP)
- TCP 443 (HTTPS)
- UDP 4789 (VTAP / VXLAN)

![Security List](imagesTP5/secure_list.png)

---



