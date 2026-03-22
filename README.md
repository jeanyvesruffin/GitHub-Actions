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
