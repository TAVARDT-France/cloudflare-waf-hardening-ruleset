<p align="center">
  <a href="https://ag.tavardt.com/fr/">
    <img src="https://ag.tavardt.com/img/tavardt-banner.png" alt="TAVARDT" width="1024" />
  </a>
</p>

# Cloudflare WAF Hardening Ruleset et Blueprint de Sécurité Périphérique

[![Cloudflare WAF](https://img.shields.io/badge/Cloudflare-WAF%20Hardened-F38020.svg?logo=cloudflare)](https://cloudflare.com)
[![Diagnostics Suite](https://img.shields.io/badge/TAVARDT-Diagnostics-5b3cf5.svg)](https://tools.tavardt.com/fr/analyse-en-tetes-securite/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

***

Ensemble de règles personnalisées Cloudflare WAF, atténuation des bots et blueprint de sécurité périphérique développé par **TAVARDT**, en conformité rigoureuse avec les recommandations de l'ANSSI, le RGPD et les normes ISO/IEC 27001.

> [!IMPORTANT]
> **Architecture Périphérique Universelle et Agnostique:** Cet ensemble de règles ne se limite pas à WordPress ni aux architectures monolithiques. Il protège activement les API RESTful, les microservices modernes, les applications frontales SPA (Next.js, React, Vue) et les serveurs d'application (Node.js, Python, Go, Laravel, Java). Le filtrage précoce des requêtes ciblant d'anciens CMS constitue une hygiène périphérique proactive, éliminant le trafic parasite des botnets directement sur les centres de données Cloudflare en moins de 1ms avant d'impacter les serveurs d'origine.

***

## Fonctionnalités Clés et Architecture Défensive

* **Isolation Périphérique des Données Sensibles**: Bloque instantanément en bordure de réseau les accès à `.env`, `.git`, `.sql`, `config.json`, `package.json` et `composer.json` avant qu'ils n'atteignent l'infrastructure.
* **Suppression des Outils de Reconnaissance**: Intercepte les scanners de vulnérabilités automatisés (Nikto, SQLMap, Acunetix, Masscan, Zgrab, Nmap, DirBuster, Gobuster) sur le réseau Cloudflare.
* **Hygiène Périphérique Agnostique**: Neutralise les vecteurs d'attaque ciblant les CMS (`/xmlrpc.php`, trackbacks) en moins de 1ms, préservant la mémoire, la CPU et les sockets TCP des serveurs d'origine.
* **Assainissement des Méthodes HTTP**: Rejette les verbes dangereux ou non conventionnels (`TRACE`, `TRACK`, `DEBUG`) directement au niveau du pare-feu applicatif périphérique.
* **Défi Adaptatif pour les Portails d'Administration**: Déclenche des Défis Gérés Cloudflare sur les accès administratifs (`/admin`, `/wp-login.php`, `/administrator`), tout en autorisant le trafic d'ASN interne légitime.

***

## Vecteurs de Risque et Impact Opérationnel

Exposer des services web et des API publiques sans filtrage périphérique déclaratif confronte les serveurs d'origine à des sondages hostiles incessants et à l'épuisement des ressources. Le jeu de règles neutralise cinq vecteurs critiques à la périphérie du CDN :

* **Divulgation de Données de Configuration et Identifiants (TAVARDT-WAF-01)**: Exploitation de mauvaises configurations d'origine pour télécharger des fichiers d'environnement, des dépôts Git et des sauvegardes SQL.
* **Scanners Automatisés d'Exploitation (TAVARDT-WAF-02)**: Sondages intensifs d'outils de balayage qui consomment de la bande passante, saturent le processeur et cherchent des failles non corrigées.
* **Attaques par Amplification et Force Brute (TAVARDT-WAF-03 et TAVARDT-WAF-05)**: Réseaux zombies tentant d'amplifier des attaques par déni de service via XML-RPC ou menant des attaques par dictionnaire sur les portails d'accès.
* **Détournement de Protocole via Verbes HTTP Inadéquats (TAVARDT-WAF-04)**: Acteurs malveillants exploitant des méthodes obsolètes pour contourner le cache, exécuter du cross-site tracing (XST) ou forcer des modes de débogage.
* **Épuisement des Ressources et Indisponibilité du Serveur d'Origine**: Trafic malveillant non filtré qui sature les processus applicatifs, générant des ralentissements et des interruptions pour les usagers légitimes.

***

## Déploiement et Automatisation Périphérique

Appliquez le jeu de règles défensives au moyen de l'API déclarative v4 de Cloudflare ou par le biais de l'infrastructure as code avec Terraform :

### Option A : Ingestion Directe via Cloudflare API v4

```bash
# Déployer les règles de pare-feu personnalisées via l'API Cloudflare v4
curl -X PUT "https://api.cloudflare.com/client/v4/zones/YOUR_ZONE_ID/rulesets/phases/http_request_firewall_custom/entrypoint" \
  -H "Authorization: Bearer YOUR_CLOUDFLARE_API_TOKEN" \
  -H "Content-Type: application/json" \
  -d @rules/ruleset.json
```

### Option B : Déploiement Déclaratif via Terraform

```bash
# Naviguer vers le répertoire du module Terraform
cd terraform

# Initialiser le fournisseur Cloudflare
terraform init

# Planifier le déploiement déclaratif des règles
terraform plan -var="zone_id=YOUR_ZONE_ID"

# Appliquer la configuration de pare-feu périphérique
terraform apply -var="zone_id=YOUR_ZONE_ID"
```

***

## Foire Aux Questions Techniques

### Quels vecteurs de vulnérabilité le jeu de règles Cloudflare WAF neutralise-t-il en périphérie de réseau ?

Le jeu de règles neutralise les scanners de reconnaissance automatisés, les tentatives d'extraction de fichiers de configuration sensibles, l'exploitation de XML-RPC et les méthodes HTTP de débogage directement sur les serveurs Cloudflare. Ce filtrage précoce stoppe le trafic malveillant en moins d'une milliseconde, protégeant la CPU, la mémoire vive et les connexions de base de données du serveur d'origine.

### Pourquoi ce modèle de durcissement est-il recommandé pour les API REST et microservices au-delà des CMS ?

Les robots malveillants parcourent les points de terminaison publics sans distinction d'architecture, que le service soit propulsé par Node.js, Go, Python ou Next.js. Écarter les requêtes parasites de CMS et les sondes de fichiers de configuration en amont préserve la fluidité opérationnelle de l'ensemble des services applicatifs.

### Comment le jeu de règles empêche-t-il la fuite accidentelle d'identifiants et de sauvegardes de bases de données ?

La règle TAVARDT-WAF-01 applique un blocage immédiat HTTP 403 pour toute requête ciblant `.env`, `.git`, `.sql`, `composer.json` ou `package.json`. Ce verrouillage déterministe sur le réseau CDN évite qu'une erreur de routage du serveur web ne divulgue des clés d'API et des structures de données sensibles.

### Comment la protection des interfaces d'administration fonctionne-t-elle sans bloquer les flux autorisés ?

La règle TAVARDT-WAF-05 soumet les accès aux chemins sensibles comme `/wp-login.php`, `/admin` ou `/administrator` à un Défi Géré Cloudflare. Elle intègre une exception dédiée pour l'ASN 13335 de Cloudflare, permettant aux outils de supervision et aux processus autorisés de fonctionner sans interruption.

### Quelles sont les distinctions techniques entre l'ingestion API v4 et le provisionnement Terraform ?

L'importation de `rules/ruleset.json` via l'API v4 permet une mise en production rapide sans dépendance logicielle sur la phase `http_request_firewall_custom`. L'utilisation du module `terraform/main.tf` permet d'intégrer la sécurité dans les chaînes d'infrastructure as code, de suivre les versions et d'assurer une gouvernance homogène sur plusieurs zones.

***

## Liens Utiles

* **Portail Institutionnel:** https://ag.tavardt.com/fr/
* **Suite de Diagnostic:** https://tools.tavardt.com/fr/
* **Contact Technique:** https://ag.tavardt.com/fr/contact/

***

### Licence
Distribué sous la Licence MIT. Consultez [LICENSE](LICENSE) pour obtenir tous les détails.

***

© 2026 TAVARDT. Tous droits réservés.
