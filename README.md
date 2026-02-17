# LAB-3-Observation-du-trafic-HTTP-S-Android-avec-Burp-Suite
# Fiche de Trace — Audit d'Interception HTTPS (Labo)

## 1. Configuration et Périmètre
* **Périmètre :** Émulateur de labo (Pixel 6), cible autorisée.
* **Burp Version :** Burp Suite Community Edition v2026.1.3.
* **Configuration Réseau :**
    * **IP Hôte :** `192.168.1.5`.
    * **Port Proxy :** `8081`.
* **Date/Heure :** 17/02/2026 à 17:44.

<img width="623" height="155" alt="image" src="https://github.com/user-attachments/assets/b2813384-90e0-4eae-95f2-1e9d6463154c" />

---

## 2. Preuves Techniques

### Capture de l'historique (Flux déchiffrés)
L'installation du certificat CA a permis de lever l'erreur `NET::ERR_CERT_AUTHORITY_INVALID`. L'historique affiche désormais des requêtes HTTPS en clair vers `google.com` via le port `8081`.

<img width="306" height="626" alt="image" src="https://github.com/user-attachments/assets/373a487c-7e46-47e6-a383-98482c6dc568" />

<img width="1582" height="364" alt="image" src="https://github.com/user-attachments/assets/07581b4e-aa17-4f90-a4ff-b6b6065e908e" />

## Capture de Listener

<img width="1347" height="887" alt="image" src="https://github.com/user-attachments/assets/e9ee908f-6ccd-4787-910a-5cff5daccb6f" />

### Détails d'une requête (Headers + URL)
* **URL :** `http://www.google.com/gen_204`.
* **Méthode :** `GET`.
* **Headers clés :**
    * `Host: www.google.com`.
    * `User-Agent: Mozilla/5.0 (Linux; Android 10; K) ... Chrome/133.0.0.0`.
    * `Connection: keep-alive`.

<img width="715" height="861" alt="image" src="https://github.com/user-attachments/assets/296a04e2-bf04-4c35-8e85-42848985fce1" />

## Suppression prévue du certificat CA pour retour à un état sain

<img width="369" height="826" alt="image" src="https://github.com/user-attachments/assets/aede889b-f1a2-4ebf-a49e-412f31ba3ac2" />

## Nettoyage bien fait
<img width="317" height="702" alt="image" src="https://github.com/user-attachments/assets/329a4bcf-8cb1-490b-9485-d2bb71798589" />

---

## 3. Analyse

### Données observées (Ce qui est observé)
* **Transit :** Les requêtes révèlent le type d'appareil (Pixel 6 émulé), la version précise du navigateur (Chrome 133) et les endpoints sollicités (`/gen_204`, `/generate_204`).
* **Structure :** Le flux est déchiffré au niveau applicatif (L7), rendant visibles tous les en-têtes de requête.

### Risques potentiels (Ce qui est supposé)
* **Confidentialité :** En l'absence de "Certificate Pinning", n'importe quelle entité pouvant installer un certificat CA sur l'appareil peut intercepter les communications.
* **Exposition :** Si des tokens étaient présents dans l'URL (GET), ils seraient immédiatement visibles dans les logs du proxy et de l'historique du navigateur.

---

## 4. Recommandations Défensives (Ce qui est recommandé)

1. **Minimiser les données :** Éviter de transmettre des informations sensibles ou des jetons d'authentification directement dans l'URL (privilégier le corps des requêtes POST).
2. **Sécuriser les cookies :** Implémenter les attributs `Secure` (HTTPS uniquement) et `HttpOnly` (protection contre XSS) côté serveur.
3. **Pratiques Android :** Utiliser la configuration de sécurité réseau d'Android pour restreindre les autorités de certification acceptées et désactiver l'usage des certificats utilisateur en production.

> **Note d'hygiène :** Le certificat CA a été installé temporairement pour ce test et doit être retiré de l'émulateur à la fin de la session.

## 5. Checklist de Validation et Fin de Séance
* [x] **Burp Capture :** Requêtes visibles dans l'historique HTTP.
* [x] **Listener :** Actif sur `192.168.1.5:8081`.
* [x] **Proxy Android :** Configuré en mode "Manual".
* [x] **Intercept :** Désactivé après démonstration (`Intercept is off`).
* [x] **Nettoyage :** Suppression prévue du certificat CA pour retour à un état sain.
