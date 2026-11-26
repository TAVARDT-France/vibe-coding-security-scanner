<p align="center">
  <a href="https://ag.tavardt.com/fr/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# Auditeur de Sécurité Vibe Coding et Architecture IA

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-5b3cf5.svg)](https://tavardt-france.github.io/vibe-coding-security-scanner/)
[![Python](https://img.shields.io/badge/Python-3.8%2B-blue.svg)](https://www.python.org/)
[![Security](https://img.shields.io/badge/Security-AI%20Architecture-blue.svg)](https://tavardt-france.github.io/vibe-coding-security-scanner/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Guide corporatif d'ingénierie défensive, atténuation des fuites d'identifiants et utilitaire CLI déterministe pour l'audit de sécurité des applications créées avec des assistants d'intelligence artificielle (Vibe Coding).

***

## Vue d'Ensemble Exécutive: La Réalité Sécuritaire du Vibe Coding

La transition vers le développement logiciel accéléré par intelligence artificielle (au moyen d'outils tels que Cursor, Lovable, v0, Bolt, Windsurf, Replit, Claude Code et GitHub Copilot) offre aux équipes techniques la capacité de concevoir des applications web complètes avec une rapidité sans précédent. Néanmoins, les moteurs génératifs optimisent en priorité la fonctionnalité immédiate au détriment de l'isolation périmétrique, du principe du moindre privilège et de l'assainissement des livrables de production.

Lorsque les développeurs sollicitent des modèles d'IA pour déployer des fonctionnalités rapidement, les solutions générées héritent de vulnérabilités architecturales récurrentes:
* Des configurations de débogage transitent vers les environnements de production sans barrières de compilation.
* Des clés d'administration de bases de données et des jetons d'IA sont intégrés dans les fichiers statiques téléchargés par le navigateur.
* Des tables relationnelles sont créées sans politiques déclaratives d'autorisation (Row Level Security).
* Les en-têtes défensifs essentiels de sécurité web sont omis des réponses applicatives.

Ce dépôt définit une norme d'architecture défensive pour les entreprises, fournissant des modèles de durcissement prêts à l'emploi pour les frameworks modernes ainsi qu'un utilitaire déterministe en Python (`audit_vibe_security.py`) afin d'identifier et neutraliser ces vulnérabilités avant toute mise en ligne.

***

## Surface de Menace dans les Applications Générées par IA

### 1. Exposition des Sourcemaps en Production (.js.map)
Les assistants d'IA configurent fréquemment les outils d'empaquetage frontend (Vite, Webpack, Turbopack, Rollup) avec des paramètres par défaut qui génèrent des cartes de code source aux côtés des fichiers minifiés. Déployés sur des réseaux de diffusion de contenu (CDN) publics, ces fichiers `.js.map` autorisent la rétro-ingénierie totale du code:
* Restitution exacte du code source original en TypeScript et JavaScript.
* Visibilité intégrale sur les modèles de données internes, schémas de bases de données et interfaces d'API.
* Révélation d'adresses d'administration masquées, de routes internes et de commentaires techniques de développement.

### 2. Clés de Fournisseurs d'IA et Jetons BaaS Compilés dans le Client
En l'absence de contextualisation sur la scission physique entre le serveur et le navigateur client, les générateurs d'IA insèrent couramment des secrets au sein des composants frontend. Lorsque des variables portant les préfixes `NEXT_PUBLIC_` ou `VITE_` sont appelées, leurs valeurs littérales sont compilées dans les fichiers JavaScript statiques:
* Clés d'API OpenAI, Anthropic et Google Gemini exposées à une consommation illimitée.
* Jetons administratifs `service_role` et JWT Supabase neutralisant tout contrôle d'accès.
* Clés d'accès cloud AWS, identifiants Google Cloud et points de terminaison de paiement.

### 3. Absence de Row Level Security (RLS) sur les Bases de Données
Le paradigme du Vibe Coding favorise les interactions directes entre le client web et les bases de données via des bibliothèques modernes. Cependant, les modèles d'IA créent régulièrement des tables PostgreSQL sans appliquer la directive `ALTER TABLE ... ENABLE ROW LEVEL SECURITY;`. En l'absence de politiques RLS:
* Tout internaute inspectant les requêtes réseau peut formuler des requêtes REST et GraphQL non authentifiées.
* Des bases entières comprenant données d'utilisateurs, informations personnelles (RGPD) et transactions peuvent être extraites sans entrave.

### 4. Omission des En-têtes HTTP de Sécurité Périmétrique
Les configurations de serveurs créées par IA intègrent rarement les en-têtes défensifs indispensables:
* `Content-Security-Policy` (CSP): Empêche l'exécution de scripts tiers non autorisés et l'exfiltration de données.
* `Strict-Transport-Security` (HSTS): Impose l'usage exclusif de communications chiffrées via HTTPS.
* `X-Frame-Options` et `X-Content-Type-Options`: Bloquent les attaques par détournement de clic (Clickjacking) et l'interprétation abusive des types MIME.

### 5. Exposition de Fichiers d'Environnement (.env)
Des répertoires racines mal restreints autorisent l'accès public direct à des fichiers tels que `/.env`, `/.env.local` et `/.env.production`, permettant le vol instantané d'identifiants d'infrastructure par de simples requêtes HTTP.

***

## Utilitaire Déterministe d'Audit CLI

Les navigateurs web imposent des mécanismes stricts d'isolation et des politiques de partage de ressources entre origines multiples (CORS) qui empêchent les scripts exécutés côté client d'examiner librement les en-têtes ou de sonder des hôtes tiers. Afin de garantir des audits techniques reproductibles et sans transmission de télémétrie, TAVARDT met à disposition un utilitaire Python conçu pour les terminaux d'administration et les chaînes de CI/CD.

### Processus d'Inspection Déterministe

Le programme (`audit_vibe_security.py`) réalise une analyse structurée en cinq étapes:
1. **Audit de Périmètre Racine:** Établit une liaison TLS, contrôle les codes de réponse HTTP et vérifie les en-têtes de sécurité.
2. **Découverte des Scripts dans le DOM:** Analyse le code HTML pour identifier dynamiquement l'ensemble des scripts chargés dans la page.
3. **Contrôle des Sourcemaps:** Teste l'accessibilité des fichiers `.js.map` associés à chaque script identifié.
4. **Recherche de Clés dans les Fichiers Compilés:** Applique des filtres d'expressions régulières précis pour détecter la présence de jetons d'IA et de clés cloud.
5. **Vérification des Fichiers d'Environnement:** Contrôle si les fichiers d'environnement (`/.env`, `/.env.local`, `/.env.production`) sont correctement inaccessibles.

### Guide d'Exécution

Téléchargez et lancez l'utilitaire directement depuis votre terminal contre l'adresse cible:

```bash
# 1. Recuperer le script officiel depuis le depot
curl -sO https://raw.githubusercontent.com/TAVARDT-France/vibe-coding-security-scanner/main/audit_vibe_security.py

# 2. Executer l audit deterministe
python audit_vibe_security.py https://votre-application.com
```

***

## Modèles de Durcissement pour la Production

Ce dépôt fournit des configurations éprouvées pour sécuriser l'infrastructure applicative:

### Configuration Vite (`vite.config.ts`)
Supprime totalement les sourcemaps en production et assainit les livrables:

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  build: {
    sourcemap: false,
    chunkSizeWarningLimit: 1000,
    rollupOptions: {
      output: {
        manualChunks: undefined
      }
    }
  }
});
```

### Configuration Next.js (`next.config.js`)
Désactive les cartes de code côté client et impose des en-têtes sécurisés:

```javascript
/** @type {import('next').NextConfig} */
const nextConfig = {
  productionBrowserSourceMaps: false,
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: [
          { key: 'X-Frame-Options', value: 'DENY' },
          { key: 'X-Content-Type-Options', value: 'nosniff' },
          { key: 'Referrer-Policy', value: 'strict-origin-when-cross-origin' },
          { key: 'Strict-Transport-Security', value: 'max-age=63072000; includeSubDomains; preload' }
        ]
      }
    ];
  }
};

module.exports = nextConfig;
```

### Sécurisation de Row Level Security sur Supabase (`supabase-rls.sql`)
Garantit l'isolation stricte des données et révoque les privilèges anonymes:

```sql
/* 1. Activation imperativedu Row Level Security sur les tables publiques */
ALTER TABLE public.profiles ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.organizations ENABLE ROW LEVEL SECURITY;
ALTER TABLE public.transactions ENABLE ROW LEVEL SECURITY;

/* 2. Revocation des autorisations par defaut du role anon */
REVOKE ALL ON ALL TABLES IN SCHEMA public FROM anon;

/* 3. Creation d une politique stricte limitant la lecture a l utilisateur authentifie */
CREATE POLICY "Les utilisateurs ne consultent que leurs propres profils" 
ON public.profiles FOR SELECT 
TO authenticated 
USING (auth.uid() = id);
```

### Règle de Filtrage Périmétrique Cloudflare WAF
Règle de bordure pour le rejet immédiat des sondes automatisées:

```text
(http.request.uri.path contains ".js.map") or 
(http.request.uri.path contains ".env") or 
(http.request.uri.path contains ".git")
Action: Block (Rejeter la connexion immediatement)
```

***

## Foire Aux Questions Techniques (Architecture & Sécurité)

### Quels sont les risques inhérents au développement via Vibe Coding?
Les moteurs génératifs créent du code par correspondance statistique sans modélisation formelle des cybermenaces. Ils placent fréquemment des clés secrètes au sein des fichiers frontend, omettent d'activer le Row Level Security sur les bases de données et laissent subsister des paramètres de diagnostic en production.

### Pourquoi la mise en ligne de sourcemaps (.js.map) est-elle dangereuse?
Les fichiers sourcemap offrent la conversion inverse exacte du code minifié vers le code source originel. Leur diffusion publique permet aux attaquants de déchiffrer la logique applicative, d'identifier les règles de validation et de découvrir les chemins administratifs sans difficulté technique.

### Comment sécuriser la consommation d'APIs d'intelligence artificielle sur le frontend?
Les applications exécutées dans le navigateur ne doivent jamais solliciter directement les fournisseurs d'IA avec des jetons d'administration. Les requêtes doivent impérativement transiter par des points d'entrée backend sécurisés ou des fonctions serverless en bordure qui valident la session de l'utilisateur, appliquent un filtrage de débit et conservent les secrets sur le serveur.

### L'utilitaire déterministe en Python perturbe-t-il le service en ligne ausculté?
Non. Le programme effectue exclusivement des analyses de lecture passives et non destructives. Il émet des requêtes HTTP GET et HEAD standards pour évaluer les en-têtes et les fichiers exposés, sans modifier les données ni altérer la disponibilité de l'infrastructure.

***

## Cadres de Conformité et Normes d'Entreprise

Architecture de sécurité et mesures techniques alignées sur le Règlement Général sur la Protection des Données (RGPD, Règlement UE 2016/679), les recommandations de la CNIL et les guides de sécurité applicative de l'ANSSI.

***

## Dépôt & Documentation Officielle

* **Page Officielle:** [https://tavardt-france.github.io/vibe-coding-security-scanner/](https://tavardt-france.github.io/vibe-coding-security-scanner/)
* **Portail TAVARDT:** [Portail Institutionnel](https://ag.tavardt.com/fr/)
* **Outils:** [Centre d'Outils](https://tools.tavardt.com/fr/)

***

## Liens Utiles

* **Portail Corporatif:** https://ag.tavardt.com/fr/
* **Suite de Diagnostics:** https://tools.tavardt.com/fr/
* **Contact Technique:** https://ag.tavardt.com/fr/contact/

***

© 2026 TAVARDT. Tous droits réservés.
