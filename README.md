# TP5 – VTAP, Wireshark, HTTPS et Antivirus (Oracle Cloud)

**Cours :** 420‑06C‑FX – Réseaux
**Enseignant :** Jean‑Sébastien Nadeau  
**Session :** Automne 2025

**Équipe :**  
- Nom étudiant 1  
- Nom étudiant 2

---

## 🎯 Objectifs du travail
- Configurer un **VTAP** sur Oracle Cloud Infrastructure (OCI)
- Capturer et analyser du trafic **HTTP** avec **Wireshark**
- Héberger un site **HTTPS** avec **SSL/TLS (CertBot / Let's Encrypt)**
- Installer et tester un **antivirus ClamAV** sur une instance OCI
- Documenter toutes les étapes en **Markdown avec captures d’écran**

---

## 🧱 Architecture générale
- 1 instance **Oracle Cloud Compute (Ubuntu)**
- 1 **VTAP** attaché à l’instance
- 1 machine de capture avec **Wireshark**
- 1 serveur web Python (HTTP & HTTPS)
- 1 antivirus **ClamAV**

![Architecture OCI](imagesTP5/connexionOracle.png)

---

## 1️⃣ Configuration du serveur HTTP

### Démarrage du serveur HTTP
```bash
sudo python3 -m http.server 80
```

### Vérification dans le navigateur
- Accès via l’IP publique de l’instance
- Affichage du fichier `index.html`

![Serveur HTTP actif](imagesTP5/cmd-serv.png)

---

## 2️⃣ Création et configuration du VTAP

### Étapes réalisées dans OCI
1. Création du **VTAP**
2. Sélection de l’instance comme **source**
3. Définition du **VTAP target**
4. Configuration des **filtres (HTTP / TCP)**

![Création du VTAP](imagesTP5/vtap.png)
![Source et Target du VTAP](imagesTP5/vtap%20%282%29.png)
![Détails du VTAP en cours d'exécution](imagesTP5/vtap%20%283%29.png)

---

## 3️⃣ Capture et analyse avec Wireshark

### Filtres utilisés
- Filtre d’affichage :
```
http || tcp.port == 80
```

### Analyse effectuée
- Requêtes HTTP GET
- Réponses HTTP (codes 200, 304, etc.)
- En‑têtes HTTP
- Cookies
- Suivi de flux TCP

![Wireshark – trafic TLS / HTTPS](imagesTP5/wireshark.png)
![Wireshark – requêtes HTTP GET](imagesTP5/wireshark%20%282%29.png)
![Wireshark – VXLAN / UDP 4789](imagesTP5/wireshark%20%283%29.png)

---

## 4️⃣ Hébergement du site HTTPS (SSL/TLS)

### Installation de CertBot
```bash
sudo apt update
sudo apt install certbot -y
```

### Génération du certificat
```bash
sudo certbot certonly --standalone -d votre-domaine-ou-ip
```

### Configuration du serveur HTTPS Python
- Utilisation du certificat généré par **Let's Encrypt**
- Exécution en mode `sudo`

![Installation de Certbot](imagesTP5/9-cmd.png)
![Serveur HTTPS actif](imagesTP5/serveur_index.png)

---

## 5️⃣ Installation et configuration de ClamAV

### Installation
```bash
sudo apt install clamav clamav-daemon -y
```

### Mise à jour des signatures
```bash
sudo freshclam
```

### Test de scan
```bash
clamscan test.txt
```

![Installation de ClamAV](imagesTP5/4-cmd.png)
![Mise à jour des signatures ClamAV](imagesTP5/6-cmd.png)
![Service clamd actif](imagesTP5/7-cmd.png)

---

## 🔐 Sécurité et bonnes pratiques
- Pas d’utilisation de **ufw** (recommandation OCI)
- Accès SSH sécurisé par clé
- HTTPS avec certificat valide
- Analyse antivirus régulière

---

## 📁 Contenu du dépôt Git
- `README.md`
- Dossier `imagesTP5/`
- Captures d’écran de toutes les étapes

---

## 📎 Informations de remise
- IP publique du serveur HTTPS
- Clé SSH privée
- Lien vers le dépôt GitHub **privé**
- Enseignant ajouté comme contributeur : **jsteach**

---

## ✅ Conclusion
Ce travail nous a permis de comprendre concrètement :
- La surveillance réseau avec un **VTAP**
- L’analyse de trafic avec **Wireshark**
- La sécurisation d’un serveur avec **HTTPS**
- Le renforcement de la sécurité via **ClamAV**

Le document sert également de **backup complet** en cas de problème avec les instances OCI.

