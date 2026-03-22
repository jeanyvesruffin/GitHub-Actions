# GitHub-Actions

## Description

Mettre en place les workflows GitHub Actions pour build, test et lint de tous les services.

## Workflows à créer

* ci-backend.yml : Maven build + tests Spring Boot
* ci-frontend.yml : npm install + ng build + ng test
* ci-workers.yml : pip install + pytest FastAPI
* ci-crossmatch.yml : pytest service cross-match
* ci-spectral.yml : pytest service spectral

## Critères d'acceptation

* Chaque push sur main et PR déclenche les CI
* Build fail → merge bloqué
* Artifacts de build archivés
* Badge CI visible sur README

## ⚠️ Points d'attention sur ces versions très récentes

Java 25 — Version en cours de développement (EA). actions/setup-java avec la distribution temurin ne la propose peut-être pas encore. Si le workflow échoue, utilise la distribution oracle ou passe par un step manuel avec un JDK EA :

```yaml
distribution: oracle   # ou "zulu"
java-version: "25-ea"
```

Python 3.15.0a7 — Version alpha, non disponible sur les runners GitHub-hosted via actions/setup-python. Il faudra probablement compiler depuis les sources ou utiliser deadsnakes/action :

```yaml
- uses: deadsnakes/action@v3
  with:
    python-version: "3.15-dev"
```

Node 25.8.1 — Disponible mais hors LTS. actions/setup-node le supporte via node-version: "25.8.1", ça devrait fonctionner sans problème.

## Badges CI — à ajouter dans ton README.md

> Remplace `TON_ORG` et `TON_REPO` par les vraies valeurs GitHub.

```markdown
## CI Status

| Service     | Status |
|-------------|--------|
| Backend     | [![CI – Backend](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-backend.yml/badge.svg)](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-backend.yml) |
| Frontend    | [![CI – Frontend](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-frontend.yml/badge.svg)](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-frontend.yml) |
| Workers     | [![CI – Workers](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-workers.yml/badge.svg)](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-workers.yml) |
| Crossmatch  | [![CI – Crossmatch](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-crossmatch.yml/badge.svg)](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-crossmatch.yml) |
| Spectral    | [![CI – Spectral](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-spectral.yml/badge.svg)](https://github.com/TON_ORG/TON_REPO/actions/workflows/ci-spectral.yml) |
```

## Emplacement des fichiers dans le repo

Les workflows doivent être placés dans `.github/workflows/` à la **racine** du repo
(pas dans `docs/.github/workflows/`) pour être reconnus par GitHub Actions :

```
.github/
└── workflows/
    ├── ci-backend.yml
    ├── ci-frontend.yml
    ├── ci-workers.yml
    ├── ci-crossmatch.yml
    └── ci-spectral.yml
```

## Points importants à vérifier

1. Emplacement des fichiers — GitHub Actions ne lit que .github/workflows/ à la racine du repo. Ton dossier actuel est docs/.github/workflows/ — il faudra déplacer les fichiers ou recréer la structure à la racine.
2. requirements.txt — Les workflows Python supposent que ce fichier existe dans chaque service ainsi qu'un fichier test.py. Exemples :

* [requirements.txt](requirements.txt)
* [ampty test_placeholder.py ](test_placeholder.py)


1. Bloquer le merge — Une fois les workflows poussés, va dans Settings → Branches → Branch protection rules sur main et active "Require status checks to pass before merging" en sélectionnant les jobs CI.
2. Frontend — Le workflow suppose un script lint dans package.json. Si ce n'est pas encore le cas : "lint": "ng lint".

## Bloquer le merge en cas d'échec

Dans **Settings → Branches → Branch protection rules** sur `main` :

* ✅ Require status checks to pass before merging
* Ajoute chaque job : `Maven Build & Test`, `Lint, Build & Test`, `Lint & Pytest`
* ✅ Require branches to be up to date before merging.
