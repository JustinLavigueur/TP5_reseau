# TP5 – VTAP, Wireshark, HTTPS et Antivirus (Oracle Cloud)

**Cours :** 420‑06C‑FX – Réseaux
**Enseignant :** Jean‑Sébastien Nadeau  
**Session :** Automne 2025

**Équipe :**  
- Nom étudiant 1  
- Nom étudiant 2



---

## Architecture générale
- 2 instance **Oracle Cloud Compute (Ubuntu)**
- 1 **VTAP** attaché à l’instance analyseur
- 1 serveur web Python (HTTP & HTTPS) attaché à l'instance serveur
- 1 antivirus **ClamAV**

![Architecture OCI](imagesTP5/connexionOracle.png)




![Instances](imagesTP5/les_instances.png)
---

## Configuration du serveur HTTP

### Démarrage du serveur HTTP
```bash
sudo python3 -m http.server 80
```

### Vérification dans le navigateur
- Accès via l’IP publique de l’instance
- Affichage du fichier `index.html`

![Serveur HTTP actif](imagesTP5/cmd-serv.png)

---

## Création et configuration du VTAP

### Étapes réalisées dans OCI
1. Création du **VTAP**
2. Sélection de l’instance comme **source**
3. Définition du **VTAP target**
4. Configuration des **filtres (HTTP / TCP)**

![Création du VTAP](imagesTP5/vtap.png)
![Source et Target du VTAP](imagesTP5/vtap%20%282%29.png)
![Détails du VTAP en cours d'exécution](imagesTP5/vtap%20%283%29.png)

---

## Capture et analyse avec Wireshark

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

## Hébergement du site HTTPS (SSL/TLS)

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

## Installation et configuration de ClamAV

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

## Sécurité et bonnes pratiques
- Pas d’utilisation de **ufw** (recommandation OCI)
- Accès SSH sécurisé par clé
- HTTPS avec certificat valide
- Analyse antivirus régulière

---

## Contenu du dépôt Git
- `README.md`
- Dossier `imagesTP5/`
- Captures d’écran de toutes les étapes

---



