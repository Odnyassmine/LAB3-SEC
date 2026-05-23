# LAB3-SEC
# Inspection HTTP/HTTPS avec Burp Suite et Android Emulator

## 1. Présentation du laboratoire

Ce laboratoire a pour objectif de comprendre le fonctionnement d’un proxy d’analyse réseau dans un environnement Android contrôlé.

L’étude porte sur :

- la configuration de Burp Suite comme proxy,
- la capture de trafic HTTP,
- l’observation des requêtes réseau,
- le principe de l’interception HTTPS via certificat CA de laboratoire,
- la production d’une preuve d’analyse reproductible.

> ⚠️ Ce laboratoire doit être réalisé uniquement dans un environnement de test autorisé (émulateur Android ou appareil de labo).

---

# 2. Objectifs pédagogiques

À la fin du laboratoire, vous serez capable de :

- Configurer un proxy réseau dans Burp Suite
- Relier un Android Emulator à un proxy HTTP
- Capturer des requêtes réseau HTTP
- Lire et analyser une requête HTTP
- Comprendre le rôle d’un certificat CA en HTTPS
- Produire une trace d’audit simple et reproductible

---

# 3. Prérequis

## Logiciels nécessaires

- Burp Suite
- Android Emulator
- Navigateur Android dans l’émulateur
- Réseau local fonctionnel

## Connaissances recommandées

- Bases du protocole HTTP/HTTPS
- Adresse IP et ports réseau
- Utilisation basique d’Android Emulator

---

# 4. Architecture du laboratoire

```text
Android Emulator
       │
       │ HTTP / HTTPS
       ▼
+-------------------+
|   Burp Suite      |
|  Proxy Listener   |
+-------------------+
       │
       ▼
Internet / Cible de test
```

---

# 5. Étape 1 — Préparer Burp Suite

## Procédure

1. Lancer Burp Suite
2. Créer ou ouvrir un projet temporaire
3. Ouvrir l’onglet :

```text
Proxy
```

4. Vérifier que :

```text
Intercept is off
```

apparaît.

---

## Pourquoi ?

Le proxy doit d’abord fonctionner en mode passif afin de valider la capture du trafic sans bloquer la navigation.

---

## À observer

- Présence de :
  - `HTTP history`
  - `Intercept`
- Bouton d’interception accessible

---

## Erreurs fréquentes

- Activer l’interception dès le début
- Bloquer tout le trafic involontairement
- Confondre :
  - Proxy
  - Repeater
  - Target

---

# 6. Étape 2 — Vérifier le Proxy Listener

## Procédure

Dans Burp :

```text
Proxy → Proxy settings
```

ou :

```text
Proxy → Options
```

Vérifier qu’un listener est :

```text
Enabled
```

---

## Noter

```text
<PORT_PROXY>
<IP_HOTE>
```

Exemple :

```text
Port : 8080
Address : All interfaces
```

---

## Pourquoi ?

L’émulateur Android doit savoir où envoyer le trafic réseau.

---

## À observer

- Listener actif
- Port défini
- Adresse d’écoute correcte

---

## Erreurs fréquentes

- Listener désactivé
- Mauvais port
- Listener limité à Loopback uniquement

---

# 7. Étape 3 — Identifier l’adresse IP de la machine hôte

## Procédure

### Windows

```bash
ipconfig
```

### Linux / macOS

```bash
ip addr
```

ou :

```bash
ifconfig
```

---

## Identifier

Une adresse IPv4 locale :

```text
A.B.C.D
```

Exemple :

```text
192.168.X.X
```

---

## Pourquoi ?

Le proxy tourne sur la machine hôte.  
L’émulateur doit pouvoir atteindre cette machine via le réseau local.

---

## Erreurs fréquentes

- Utiliser une IP VPN
- Utiliser une IP publique
- Choisir une interface inactive

---

# 8. Étape 4 — Configurer le proxy dans Android Emulator

## Procédure

Dans l’émulateur Android :

```text
Settings → Network & Internet → Wi-Fi
```

1. Ouvrir le Wi-Fi actif
2. Modifier le réseau
3. Ouvrir :

```text
Advanced options
```

4. Configurer :

```text
Proxy → Manual
```

---

## Renseigner

```text
Proxy hostname : <IP_HOTE>
Proxy port     : <PORT_PROXY>
```

Puis enregistrer.

---

## Pourquoi ?

Sans proxy, le trafic contourne Burp Suite et n’apparaît pas dans l’historique.

---

## À observer

- Proxy configuré en mode :
  ```text
  Manual
  ```
- IP et port corrects

---

## Erreurs fréquentes

- Mauvais port
- Hostname vide
- Mauvais réseau Wi-Fi

---

# 9. Étape 5 — Premier test HTTP

## Procédure

1. Ouvrir le navigateur Android
2. Accéder à une cible de test autorisée
3. Revenir dans Burp
4. Ouvrir :

```text
HTTP history
```

---

## Résultat attendu

Des requêtes apparaissent :

```text
GET / POST
URL
Status
Size
```

---

## Pourquoi ?

Cette étape valide le bon fonctionnement du proxy avant HTTPS.

---

## Erreurs fréquentes

- Aucun trafic visible
- Proxy inaccessible
- Interception activée

---

## Dépannage rapide

Vérifier :

- listener Burp
- IP hôte
- port proxy
- configuration Android

---

# 10. Étape 6 — Lire une requête HTTP

## Procédure

Sélectionner une requête dans :

```text
HTTP history
```

Observer :

```text
Raw
```

et :

```text
Inspector
```

---

## Éléments à analyser

### Méthode HTTP

```text
GET
POST
PUT
```

### URL et paramètres

```text
?id=123
```

### Headers

```text
User-Agent
Accept
Cookie
Authorization
```

### Cookies

- session
- préférences
- authentification

---

## Pourquoi ?

L’objectif principal d’un audit est de comprendre les échanges réseau.

---

## Erreurs fréquentes

- Ignorer les headers
- Se focaliser uniquement sur le body
- Interpréter un cookie sans contexte

---

# 11. Étape 7 — Interception contrôlée

## Procédure

1. Activer :

```text
Intercept is on
```

2. Rafraîchir une page
3. Observer la requête bloquée
4. Désactiver immédiatement l’interception

---

## Pourquoi ?

Comprendre le rôle du proxy comme point de passage du trafic.

---

## Différence importante

| Mode | Fonction |
|---|---|
| Passif | Observation |
| Intercept | Blocage temporaire |

---

## Erreurs fréquentes

- Oublier de désactiver Intercept
- Bloquer toute navigation

---

# 12. Étape 8 — HTTPS et certificat CA

## Principe

HTTPS empêche l’interception directe du trafic.

Pour observer le trafic HTTPS dans un laboratoire, le navigateur doit faire confiance à un certificat CA de test.

---

## Procédure d’observation

Dans Android :

```text
Settings → Security → Install certificate
```

Observer les catégories :

- CA Certificate
- VPN & App User Certificate
- Wi-Fi Certificate

---

## Pourquoi ?

Le navigateur refuse normalement tout proxy interceptant HTTPS.

Le certificat CA de labo crée une confiance temporaire contrôlée.

---

## Points importants

⚠️ Installer un certificat uniquement :

- dans un émulateur
- dans un environnement de test
- temporairement

---

## Risques

Un certificat CA permet une capacité d’observation importante.

Il doit être :

- documenté,
- supprimé après le laboratoire,
- jamais installé sur un téléphone personnel.

---

# 13. Étape 9 — Mini rapport d’analyse

## Objectif

Produire une preuve simple et reproductible du laboratoire.

---

# Structure recommandée

## Périmètre

```text
Émulateur Android de laboratoire
Cible de test autorisée
```

---

## Configuration

```text
Burp Suite version :
IP hôte :
Port proxy :
Date :
```

---

## Preuves

Inclure :

- capture de HTTP history
- détails d’une requête
- headers observés
- URL analysée

---

## Analyse

Identifier :

- paramètres visibles
- cookies
- éventuels tokens
- données sensibles

---

## Recommandations défensives

- Réduire les données exposées
- Sécuriser les cookies côté serveur
- Utiliser HTTPS correctement
- Appliquer les bonnes pratiques Android

---

# 14. Conclusion

Ce laboratoire introduit les bases de l’analyse réseau mobile avec Burp Suite dans un environnement contrôlé.

Les compétences essentielles acquises sont :

- configuration proxy,
- capture de trafic,
- lecture des requêtes,
- compréhension du HTTPS,
- documentation d’une preuve technique.

L’objectif principal reste l’analyse et la compréhension du trafic, non la modification agressive des communications.

---

# 15. Bonnes pratiques de laboratoire

✅ Utiliser uniquement un environnement autorisé  
✅ Documenter IP, ports et versions  
✅ Désactiver les certificats après usage  
✅ Garder l’interception désactivée hors test  
✅ Produire des preuves reproductibles

---

# 16. Outils utilisés

- Burp Suite
- Android Emulator
- Android Network Security Configuration
