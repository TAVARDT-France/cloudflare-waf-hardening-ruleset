# Cloudflare WAF Hardening Ruleset & Guide de Sécurité Edge

[![Licence MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Language](https://img.shields.io/badge/Language-Français-green.svg)](README.md)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/fr/analyse-en-tetes-securite/)

Règles de filtrage WAF et sécurité de bordure Cloudflare, conformes aux exigences du RGPD et aux recommandations d'hygiène informatique de l'ANSSI.

Développé et maintenu par l'équipe d'ingénierie en cybersécurité périphérique de **TAVARDT**.

> [!IMPORTANT]
> **Architecture Universelle et Agnostique :** Ce dépôt **n'est pas réservé à WordPress**. Il a été pensé pour sécuriser les **APIs RESTful, microservices modernes, applications web (Next.js, React, Vue) et backends d'entreprise (Node.js, Python, Go, Laravel, Java)**. Le blocage des sondes CMS agit comme une **couche d'hygiène périphérique active**, supprimant le trafic parasite des botnets sur le réseau Cloudflare (< 1ms) avant qu'il ne sature la mémoire, les sockets ou le processeur de vos serveurs d'origine.

***

### Matrice de Protection Multi-Stack

| Couche de Défense | Vecteurs Neutralisés | Applications et Stacks Protégées |
| :- | :- | :- |
| **Isolation des Identifiants** | Blocage en périphérie de `.env`, `.git`, `.sql`, `package.json`, `composer.json` | **APIs, Next.js, Node.js, Python, Go, Laravel, React** |
| **Suppression des Scanners** | Blocage de Nikto, SQLMap, Acunetix, Masscan, Gobuster, Nmap | **Toute application web, microservice ou passerelle d'API** |
| **Filtrage des Verbes HTTP** | Rejet systématique des méthodes dangereuses (`TRACE`, `TRACK`, `DEBUG`) | **APIs RESTful et architectures microservices** |
| **Accès Privilégiés Sécurisés** | Challenge comportemental sur les consoles d'accès (`/admin`, `/login`) | **Tableaux de bord d'administration, ERPs et consoles web** |
| **Hygiène de Bordure Sans Bruit**| Rejet direct des requêtes vers `/xmlrpc.php` et les trackbacks | **Soulagement immédiat des ressources serveurs en amont** |

***

## 1. Vue d'Ensemble de l'Architecture Périphérique

Ce dépôt fournit des règles déclaratives prêtes pour le déploiement sur la bordure de réseau Cloudflare, assurant la protection préventive des plateformes critiques d'entreprise :

* **Isolation des Fichiers et Variables Confidentiels :** Blocage en périphérie des requêtes ciblant les fichiers `.env`, les répertoires `.git`, les sauvegardes de bases de données `.sql` et les manifestes de paquets (`composer.json`, `package.json`).
* **Blocage des Outils d'Analyse et d'Exploitation :** Neutralisation en temps réel des scanners automatisés malveillants (Nikto, SQLMap, Acunetix, Masscan, Nmap, Gobuster, DirBuster).
* **Durcissement et Blindage des CMS :** Interception préventive des abus dirigés contre `/xmlrpc.php` et `/wp-trackback.php`.
* **Filtrage des Verbes HTTP Dangereux :** Rejet systématique des méthodes de débogage risquées (`TRACE`, `TRACK`, `DEBUG`).
* **Challenge Géré sur les Accès d'Administration :** Déploiement de défis comportementaux transparents pour sécuriser les points d'entrée privilégiés (`/wp-login.php`, `/admin`).

***

## 2. Audit et Diagnostic de Sécurité Préalable

Avant toute modification en production, évaluez la configuration de vos en-têtes de sécurité et la posture de votre domaine avec la suite ouverte TAVARDT :

* **Audit des En-têtes de Sécurité et TLS :** Analyseur d'En-têtes de Sécurité TAVARDT
* **Catalogue des Outils de Diagnostic :** **TAVARDT Tools France**

***

## 3. Guide de Déploiement Rapide

### Option A : Déploiement via l'API v4 Cloudflare

Appliquez directement le jeu de règles [`rules/ruleset.json`](rules/ruleset.json) :

```bash
curl -X PUT "https://api.cloudflare.com/client/v4/zones/VOTRE_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
     -H "Authorization: Bearer VOTRE_CLOUDFLARE_API_TOKEN" \
     -H "Content-Type: application/json" \
     -data @rules/ruleset.json
```

### Option B : Automatisation Déclarative via Terraform

Utilisez le module Terraform situé dans [`terraform/main.tf`](terraform/main.tf) :

```bash
cd terraform
terraform init
terraform plan -var="zone_id=VOTRE_ZONE_ID"
terraform apply -var="zone_id=VOTRE_ZONE_ID"
```

***

## 4. Conseil Stratégique & Support d'Ingénierie SOC

Pour des audits d'infrastructure approfondis, la mise en conformité réglementaire (RGPD / NIS 2) ou la protection d'agents d'intelligence artificielle :

* **Portail Entreprise :** **TAVARDT**
* **Contact et Échange Technique :** [Contacter l'Équipe d'Ingénierie](https://ag.tavardt.com/fr/contact/)

***

## Licence

Distribué sous licence open-source MIT. Consultez le fichier [`LICENSE`](LICENSE) pour plus d'informations.  
Copyright

***

© 2026 TAVARDT. Tous droits réservés.
