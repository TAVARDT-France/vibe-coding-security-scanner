# Vibe Coding Security Scanner & Auditeur d'Applications Générées par IA | TAVARDT France

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Vibe Coding Security](https://img.shields.io/badge/Vibe%20Coding-Security%20Hardened-00d26a.svg)](README.md)
[![Language](https://img.shields.io/badge/Language-Français-green.svg)](README.md)
[![Engineering](https://img.shields.io/badge/TAVARDT-Cybersecurity-5b3cf5.svg)](https://ag.tavardt.com/fr/cybersecurite/)

Scanner open source pour auditer les applications créées via vibe coding (Cursor, Lovable, Bolt, v0). Détecte les clés d'API exposées dans le frontend, les failles RLS Supabase et les sourcemaps.

## Application Web Interactive

Accédez à l'outil en ligne et auditez en temps réel :

- [https://tavardt-france.github.io/vibe-coding-security-scanner/](https://tavardt-france.github.io/vibe-coding-security-scanner/)

---

## 1. Vue d'Ensemble du Scanner de Sécurité Vibe Coding

L'essor du développement assisté par intelligence artificielle (*vibe coding*) permet de déployer des applications complètes en quelques heures grâce à **Cursor, Lovable.dev, Bolt.new, v0 et Replit**.

Cependant, l'absence d'audit de sécurité expose fréquemment des secrets d'infrastructure, des bases de données sans restriction et le code source propriétaire directement sur le navigateur.

Ce dépôt propose un utilitaire Python pour analyser **la périphérie publique de toute application web en ligne**, identifiant les failles réelles de façon déterministe.

---

## 2. Vulnérabilités et Vecteurs Audités

| Vecteur de Risque | Origine dans le Vibe Coding | Impact Technique |
| :--- | :--- | :--- |
| **Fuite de Clés API Frontend** | Clés privées OpenAI (`sk-...`), Stripe ou AWS insérées dans le `.env` client ou dans React. | Tout internaute peut récupérer la clé dans les DevTools et épuiser vos quotas payants. |
| **Supabase RLS Désactivé** | Création de tables dans Supabase sans activer le *Row Level Security* (RLS). | La `anon_key` publique permet d'exfiltrer ou de supprimer l'intégralité de la base via REST. |
| **Sourcemaps en Production** | Le bundler publie les fichiers `.js.map` en production. | Le code source complet TypeScript/JavaScript est accessible en clair. |
| **Fichiers Sensibles Exposés** | Déploiements laissant des chemins comme `/.env` ou `/.git/HEAD` publics. | Fuite directe de variables d'environnement et de jetons d'accès serveurs. |
| **Absence d'En-têtes de Sécurité**| Omission des politiques CSP, HSTS et X-Frame-Options. | Vulnérabilité aux attaques de Clickjacking et d'injection de scripts (XSS). |

---

## 3. Guide d'Installation et d'Exécution

### Installation Rapide

```bash
git clone https://github.com/TAVARDT-France/vibe-coding-security-scanner.git
cd vibe-coding-security-scanner
```

### Lancer l'Audit sur une URL

```bash
python scanner.py https://votre-application.vercel.app
```

### Paramètres de Ligne de Commande

```bash
# Générer un rapport structuré au format JSON
python scanner.py https://votre-application.vercel.app --json

# Désactiver la vérification des sourcemaps
python scanner.py https://votre-application.vercel.app --no-sourcemaps
```

---

## 4. Durcissement Professionnel et Support Technique

Si votre audit a mis en évidence des identifiants compromis, des bases de données Supabase ouvertes ou nécessite un durcissement perimétrique :

* **Portail Entreprise :** [TAVARDT France](https://ag.tavardt.com/fr/cybersecurite/)
* **Contact Technique Direct :** [Contacter l'Équipe d'Ingénierie](https://ag.tavardt.com/fr/contact/)

---

## Licence

Distribué sous licence open-source MIT. Consultez le fichier [`LICENSE`](LICENSE) pour plus d'informations.  
Copyright (c) 2026 TAVARDT France.
