# Auditeur de Sécurité Vibe Coding et Architecture IA

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-5b3cf5.svg)](https://tavardt-france.github.io/vibe-coding-security-scanner/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Security](https://img.shields.io/badge/Security-AI%20Architecture-blue.svg)](https://tavardt-france.github.io/vibe-coding-security-scanner/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Guide corporatif d architecture défensive, atténuation des fuites d identifiants et outillage CLI déterministe pour l audit de sécurité des applications générées par des outils d IA (Vibe Coding).

## Surface de Risque dans les Applications Générées par IA

Le développement assisté par IA (Cursor, Lovable, v0, Bolt) introduit trois vulnérabilités architecturales récurrentes :

1. **Fuites de Sourcemaps (.js.map) :** Les compilations de production qui conservent des fichiers de cartographie permettent la décompilation intégrale de la logique métier TypeScript et des modèles backend.
2. **Exposition de Clés d IA dans les Bundles Client :** Clés OpenAI, Anthropic ou jetons `service_role` de Supabase préfixés par `NEXT_PUBLIC_` ou `VITE_` intégrés directement au code statique téléchargé par le navigateur.
3. **Absence de Row Level Security (RLS) :** Bases de données relationnelles interrogées directement via des points de terminaison REST/GraphQL sans RLS actif permettant l exfiltration non autorisée de données multi-utilisateurs.

***

## Outil Officiel d Audit CLI

Pour s affranchir des contraintes de bac à sable des navigateurs et des restrictions CORS, TAVARDT met à disposition un utilitaire Python pour une inspection directe via terminal ou pipeline CI/CD :

```bash
# 1. Telecharger l utilitaire officiel depuis le depot
curl -sO https://raw.githubusercontent.com/TAVARDT-France/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Executer l audit direct contre votre application
python audit_vibe_security.py https://votre-application.com
```

### Capacités Principales d Inspection

* **Audit des Sourcemaps :** Analyse automatique des répertoires de compilation standards pour détecter les fichiers `.js.map` accessibles publiquement.
* **Inspection des En-têtes HTTP :** Validation rigoureuse des politiques `Content-Security-Policy`, `Strict-Transport-Security`, `X-Content-Type-Options` et `X-Frame-Options`.
* **Détection des Secrets Compilés :** Analyse regex haute précision pour repérer les clés d API d IA et les identifiants d administration enfouis dans les bundles JavaScript.

***

## Blueprints de Mitigative Défensive

Ce dépôt inclut des modèles de configuration homologués pour sécuriser votre pile applicative :

* **Vite (`vite.config.ts`) :** Désactivation stricte des sourcemaps en production et assainissement des noms de modules.
* **Next.js (`next.config.js`) :** Désactivation de `productionBrowserSourceMaps` et durcissement des en-têtes défensifs.
* **Supabase SQL (`supabase-rls.sql`) :** Activation obligatoire de `ROW LEVEL SECURITY` sur les schémas publics et révocation des accès aveugles du rôle `anon`.
* **Cloudflare WAF :** Règles de filtrage en périphérie pour bloquer instantanément les requêtes vers `.js.map`, `.env` et `.git`.

***

## Référentiels de Conformité & Normes

Architecture défensive et contrôles de cybersécurité conformes au RGPD, recommandations ANSSI, CIS Benchmarks et standards Zero Trust.

***

## Liens Officiels & Ressources

* **Page Officielle :** [https://tavardt-france.github.io/vibe-coding-security-scanner/](https://tavardt-france.github.io/vibe-coding-security-scanner/)
* **Portail TAVARDT :** [Portal Institucional](https://ag.tavardt.com/fr/)
* **Outils :** [Central de Ferramentas](https://tools.tavardt.com/fr/)

&copy; 2026 TAVARDT. Tous droits réservés.

***

© 2026 TAVARDT. Tous droits réservés.
