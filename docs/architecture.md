# Architecture du back

## Le flux

**source → collecte → normalisation → stockage → diffusion → la carte change**

| Étape | Rôle | Dossier |
|---|---|---|
| **Collecte** | Une source = un module isolé. API ou flux d'abord, scraping en dernier recours. Collecte idempotente : la relancer ne crée pas de doublons. Elle écrit le brut dans `data/raw/`. | `src/archives/ingest/` |
| **Normalisation** | Rattachement à une commune (code INSEE + millésime COG), géocodage, dédoublonnage. Elle produit `data/staging/`. | `src/archives/normalize/` |
| **Modèle** | Le schéma commun : territoires, sources, scrutins, résultats, mentions médiatiques. | `src/archives/models/` |
| **Stockage** | DuckDB + Parquet en local, PostgreSQL + PostGIS ensuite. | `src/archives/storage/` |
| **Export** | Fichiers précalculés (JSON, Parquet, plus tard PMTiles) servis par un CDN. Ils vont dans `data/exports/`. | `src/archives/export/` |
| **API** | FastAPI, routes versionnées `/v1`, pour ce qui ne peut pas être précalculé : le direct et la recherche. | `src/archives/api/` |

Le contrat avec le front, c'est la spec OpenAPI générée par FastAPI. Le front en tire ses types TypeScript avec `openapi-typescript`.

## Le territoire comme clé

Toute donnée se rattache à un **code INSEE de commune, avec le millésime du COG** dans lequel il est exprimé. Les communes fusionnent et changent de code avec le temps : une donnée de 1965 et une donnée de 2027 ne parlent pas forcément du même découpage.

## Dimensionnement

- **Précalculer plutôt que servir à la demande.** L'historique ne bouge plus. Il part en fichiers statiques sur un CDN, et ni serveur ni base ne tournent pour lui.
- **Le CDN absorbe les pics.** Le soir de l'élection, le pipeline publie des instantanés à TTL court plutôt que de faire tenir la charge à l'API.
- **Dès le premier jour** : routes versionnées, API sans état, configuration par variables d'environnement, couche d'authentification prête pour des clés API.

## Montée en charge

Projet étudiant à petit budget, pensé pour pouvoir devenir un produit (dashboard ou API payante).

| Palier | Quand | Ce qui tourne |
|---|---|---|
| **0. Local** | Maintenant → Noël 2026 | DuckDB + Parquet, FastAPI en local |
| **1. Lancement public** | Avril 2027 | Postgres + PostGIS, collectes planifiées, CDN + une instance FastAPI |
| **2. Produit** | Si vente | Clés API, quotas, observabilité, préproduction, sauvegardes |
| **3. Échelle** | Charge avérée | Réplicas, workers, API répliquée |

## Encore à décider

- Les hébergeurs.
- La fréquence de rafraîchissement le soir de l'élection : elle dimensionne tout le serveur.
- Les territoires pilotes.
- La carte médiatique : superposée au vote, ou à côté. Le choix change les agrégats à servir.
