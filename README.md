# Archives — back

> **Les données qui font parler la carte.**

Archives est un site immersif pour explorer les archives médiatiques et politiques françaises à travers le territoire. Ce dépôt en est la couche de données : il **collecte, normalise, conserve et sert** ce qu'on a raconté et voté en France, commune par commune. La carte, elle, vit dans le dépôt front (`map`).

## Ce qu'il apporte à la carte

La carte sait déjà montrer un territoire. Ce dépôt lui donne quelque chose à raconter.

| Couche | Ce qu'on y trouve |
|---|---|
| **Élections** | Les résultats à la commune, des législatives de 1848 à la présidentielle de 2027 en direct. |
| **Audiovisuel** | Les territoires cités à l'antenne, et les temps de parole des campagnes. |
| **Presse** | Ce que les journaux ont titré, où et quand. |
| **Méthode** | Pour chaque donnée, d'où elle vient et comment elle a été produite. |

## Ce que le back s'engage à faire

- **Citer ses sources.** Chaque donnée remonte à son origine : producteur, date, scrutin, méthode, licence.
- **Séparer ce qui se mesure de ce qui se discute.** La **donnée**, l'**interprétation** et l'**opinion** ne sont jamais confondues.
- **Traiter la tonalité comme une hypothèse.** Aucune mesure de tonalité sans méthode publiée.
- **Respecter le droit de la presse.** Pas de texte intégral d'article : seulement des métadonnées publiques et des partenariats.
- **Préparer plutôt que calculer à la volée.** Ce qui ne change plus est préparé une fois, puis servi à tous.

## D'où viennent les données

En priorité des données publiques et ouvertes :
- **data.gouv.fr** et le **ministère de l'Intérieur** pour les élections ;
- les travaux de **Julia Cagé et Thomas Piketty** pour l'historique électoral depuis 1848 ;
- l'**INA** et l'**Arcom** pour l'audiovisuel.

La presse viendra ensuite, par ses métadonnées publiques et des partenariats.

Chaque source est décrite, avec sa licence, dans le [registre des sources](docs/sources.md).

## Où en est le dépôt

La structure est posée ; aucune donnée n'est encore collectée.

- **Stack** : Python 3.12, FastAPI, polars, DuckDB en local puis PostgreSQL + PostGIS.
- **Premier chantier** : les résultats électoraux historiques, pour remplacer les chiffres de maquette de la carte d'ici Noël 2026.
- **Ensuite** : la carte médiatique à partir de l'INA, puis la présidentielle 2027 en direct.

Documents :
- [architecture](docs/architecture.md) : le flux de données, la clé territoriale, la montée en charge ;
- [feuille de route](docs/feuille-de-route.md) : Noël 2026, avril 2027, novembre 2027 ;
- [registre des sources](docs/sources.md) : licences et vérifications juridiques.

## Lancer le projet

Prérequis : Python 3.12 et [uv](https://docs.astral.sh/uv/).

```bash
uv sync
```

```bash
cp .env.example .env
```

```bash
uv run pytest
```

```bash
uv run ruff check .
```
