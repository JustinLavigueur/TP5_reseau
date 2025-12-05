# TP5 – Réseau
**Cours : Réseau**  
**Travail pratique 5 :  Configurer un VTAP, utilisation de Wireshark,
configuration d'un Serveur https et installation d'un antivirus sur Oracle cloud**

**Coéquipiers : Justin Lavigueur et Alexandre Sweeney**

---

# === VTAP ===
Un point d'accès virtuel de test (VTAP) reproduit le trafic d'une source choisie vers une
destination définie afin de faciliter le dépannage, l'analyse de sécurité et la surveillance. Le
VTAP s'appuie sur un filtre d'entrée — un ensemble de règles déterminant quel trafic est
dupliqué.

Étapes pour la création de VTAP sur oracle Cloud  :
1. Connexion à Oracle Cloud:


- On se connecte à notre compte OCI via la console web.
- Par la suite, on accéde au tableau de bord principal d’`Oracle Cloud`.

![Connexion oracle cloud](imagesTP5/connexionOracle.png)

2. On regarde dans la VCN de notre instance la liste de sécurité en défaut pour vérifier les règles de sorties et les règles entrantes.

- On va dans Networking ➜ Virtual Cloud Networks.
- On ouvre la VCN associée à votre instance.
- On vérifie les Security Lists par défaut pour les **règles entrantes (Ingress)** et **les règles sortantes (Egress)**

![Default list VCN for TP5](imagesTP5/1.png)

![Listener Creation](imagesTP5/2.png)
![Network load balancer name](imagesTP5/3.png)
![Backend IPv4 compute instances](imagesTP5/4.png)
![Specify health check policy](imagesTP5/5.png)
![Create capture filter](imagesTP5/6.png)
![Window Create VTAP](imagesTP5/7.png)
