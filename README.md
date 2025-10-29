# 💧 Pipeline Azure Databricks – Analyse de la Qualité de l’Eau en France  

> Un pipeline qui coule de source 💧  
> Projet réalisé dans le cadre d’un brief d’alternance en **Data Engineering**.

[![Build Status](https://github.com/hesolean/Brief-databricks/actions/workflows/release.yml/badge.svg)](https://github.com/<ton_user>/<ton_repo>/actions)
[![semantic-release](https://img.shields.io/badge/semantic--release-automated-brightgreen)](https://github.com/semantic-release/semantic-release)

---

## 📘 Contexte du projet

Ce projet vise à concevoir un **pipeline de données moderne** sur **Azure Databricks**, suivant l’architecture **Medallion (Bronze – Silver – Gold)**, pour analyser la **qualité de l’eau potable en France** à partir des données publiques de [data.gouv.fr](https://www.data.gouv.fr).

Les contrôles sanitaires de l’**ANSES** (Agence Nationale de Sécurité Sanitaire) servent de base à ce pipeline.  
Il permet d’**ingérer**, **transformer** et **analyser** les résultats de conformité de l’eau distribuée commune par commune.

---

## 🎯 Objectifs du projet

### Objectif principal
Construire un pipeline complet sur **Azure Databricks** avec **Delta Live Tables (DLT)** pour automatiser le traitement des données selon l’architecture **Medallion**.

### Objectifs secondaires
- ⚙️ **Infrastructure Azure :** Configurer Azure Data Lake Storage Gen2 et Databricks  
- 🚰 **Ingestion automatisée :** Implémenter DLT pour ingérer les données brutes  
- 🔁 **CI/CD :** Mettre en place un workflow GitHub Actions avec **Semantic Release**  
- 🧪 **Qualité des données :** *(prévu)* Intégration de **Great Expectations**  
- 📆 **Orchestration :** *(prévu)* Automatisation via Databricks Workflows  
- 🌐 **API :** *(prévu)* Exposition des données via l’API Databricks  

> 💡 Le projet est finalisé jusqu’à la mise en place de la CI/CD avec Semantic Release.  
> Les étapes suivantes sont planifiées comme évolutions.

---

## 🧱 Structure du projet

```bash
pipeline-qualite-eau/
├── .github/
│   └── workflows/
│       ├── ci.yml
│       └── release.yml
├── notebooks/
│   ├── 1-Import données source.ipynb
│   ├── 2-Initier la couche bronze.ipynb
│   └── 3-Initier la couche silver.ipynb
├── .gitignore
├── .releaserc.json
├── package.json
├── CHANGELOG.md
├── Brief Databrick.odt
└── formation-data/
```
---

## 📓 Description des notebooks
### 1️⃣ Import données source

Ce notebook gère l’ingestion initiale des fichiers .txt depuis un Azure Blob Storage.
Les identifiants du stockage sont transmis au cluster via des variables d’environnement pour éviter toute exposition de secrets.
Résultat : une table de travail brute, prête à être exploitée.

### 2️⃣ Initier la couche Bronze

Ce notebook crée la couche Bronze avec Delta Live Tables (DLT).
Les données y sont stockées telles qu’elles sont reçues, sans transformation, pour préserver une trace intégrale des sources.

### 3️⃣ Initier la couche Silver

Ce notebook définit la couche Silver, où les données sont nettoyées, normalisées et enrichies.
Une colonne conformite est ajoutée :

non si le texte "non conforme" est présent dans conclusionprel,

oui sinon.

Cette couche prépare les données pour la future analyse (couche Gold).

## 🔁 Intégration Continue & Semantic Release

Une pipeline GitHub Actions est configurée pour gérer automatiquement les versions du projet via Semantic Release.

### ⚙️ Fonctionnement

Workflow : .github/workflows/release.yml

Déclenchement : à chaque push sur main ou dev

Versioning automatique : basé sur le format Conventional Commits (feat:, fix:, docs:, etc.)

Changelog généré : mise à jour automatique de CHANGELOG.md

Tag Git : créé automatiquement à chaque release

### 🧩 Commande locale
npx semantic-release

### ✅ Avantages

Gestion de version automatisée et cohérente

Changelog toujours à jour

Transparence sur l’évolution du projet

Compatible avec les workflows de déploiement CI/CD

## 🧠 Environnement technique
Outil / Service	Rôle
Azure Databricks	Exécution des notebooks et pipelines
Azure Data Lake Gen2	Stockage des données brutes et transformées
DLT (Delta Live Tables)	Gestion automatisée des tables Bronze / Silver
GitHub Actions	Intégration continue et automatisation des versions
Semantic Release	Gestion sémantique du versioning et changelog
📸 Documentation et suivi

Le fichier Brief Databrick.odt contient :

des captures d’écran des notebooks et pipelines,

la configuration des clusters Databricks,

et des notes de travail sur le déroulé du projet.

## 🚀 Évolutions prévues

Création de la couche Gold (données prêtes pour la visualisation)

Mise en place de tests de qualité avec Great Expectations

Orchestration complète via Databricks Workflows

Publication d’une API Databricks pour accès temps réel

## 👩‍💻 Auteur

Hélène Dubourg
Alternante Ingénieur Data

Curieuse, rigoureuse et passionnée par les pipelines bien ficelés.
« Parce qu’une donnée propre, c’est une donnée heureuse. »
