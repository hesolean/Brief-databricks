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
- 🧪 **Qualité des données :** Intégration de **Great Expectations**  
- 📆 **Orchestration :** Automatisation via Databricks Workflows  
- 🌐 **API :** *(prévu)* Exposition des données via l’API Databricks  

> 💡 Le projet est finalisé jusqu’à la mise en place de l'orchestration.  
> La dernière étape est planifiée comme évolution.

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
│   ├── 2-1-Initier la couche bronze.ipynb
│   ├── 2-2-Qualite données bronze.ipynb
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
Les identifiants du stockage sont transmis au cluster via des variables d’environnement pour éviter toute exposition de secrets (voir captures d'écran à la fin de Brief Databrick.odt).
Résultat : une table de travail brute, prête à être exploitée.

### 2️⃣ Initier la couche Bronze

Ce notebook crée la couche Bronze avec Delta Live Tables (DLT).
Les données y sont stockées telles qu’elles sont reçues, sans transformation, pour préserver une trace intégrale des sources.

### 2️⃣ Qualité données bronze

ce notebook teste les données de la table bronze et enregistre les résultats dans une nouvelle table qualite_donnees_plv.

### 3️⃣ Initier la couche Silver

Ce notebook définit la couche Silver, où une colonne "conformité" est ajoutée :

- "non" si le texte "non conforme" est présent dans conclusionprel,

- "oui" sinon.

Cette couche à compléter, prépare les données pour la future analyse (couche Gold).

# Catalogue des Tables

Notre pipeline ETL organise les données en **trois couches** : **bronze**, **silver** et **qualité**, suivant le modèle classique Lakehouse. Chaque couche a un rôle précis dans la transformation et la valorisation des données.

## 🟫 Bronze – Données brutes
Les tables de la couche bronze contiennent les données **directement issues des fichiers TXT du Blob Azure**, sans transformation ni enrichissement. Elles servent de source primaire pour les couches supérieures.

| Table | Description |
|-------|-------------|
| `bronze_com` | Données brutes des fichiers `COM` |
| `bronze_plv` | Données brutes des fichiers `PLV` |

---

## 🟦 Silver – Données enrichies
La couche silver transforme et enrichit certaines données brutes pour les rendre **prêtes à l’usage analytique**.  

| Table | Description |
|-------|-------------|
| `silver_qualite_eau_conformite` | Données issues de `bronze_plv` avec ajout de la colonne **conformité**, indiquant si les enregistrements respectent les règles métier |

---

## 🟩 Qualité – Données prêtes pour exploitation
La couche qualité est **la cerise sur le gâteau** : elle regroupe les résultats des **tests de qualité des données** exécutés via Great Expectations. Cette table est **immédiatement exploitable** pour du reporting, par exemple dans Power BI.

| Table | Description |
|-------|-------------|
| `qualite_donnees_plv` | Résultats détaillés des validations qualité, avec la date de test et la conformité par colonne |

---

## 💡 Flux global des données

```bash
TXT Azure Blob
│
▼
Bronze (bronze_com, bronze_plv)
│
├────────────► Silver (silver_qualite_eau_conformite)
│
└────────────► Qualité (qualite_donnees_plv) → Reporting / Power BI
```

Chaque table est **cumulable** et permet de tracer le chemin des données depuis la source brute jusqu’aux indicateurs de qualité exploitable.

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

## 🧩 Orchestration du pipeline

L’orchestration du pipeline est assurée directement dans Databricks Workflows ⚙️
Un job a été configuré pour enchaîner automatiquement les notebooks :
1️⃣ Import données source →
2️⃣ Initier la couche Bronze →
3️⃣ Qualite des données de la couche bronze / Initier la couche Silver

Chaque étape est exécutée dans l’ordre, garantissant la mise à jour cohérente des tables et la traçabilité complète du flux.

🪶 Simple, efficace, et parfaitement intégré à l’écosystème Databricks : un pipeline qui s’exécute tout seul, pendant que le café infuse ☕

## 🧠 Environnement technique
Outil / Service	Rôle
Azure Databricks	        Exécution des notebooks et pipelines
Azure Data Lake Gen2	    Stockage des données brutes
DLT (Delta Live Tables)	    Gestion automatisée des tables Bronze / Silver
GitHub Actions	            Intégration continue et automatisation des versions
Semantic Release	        Gestion sémantique du versioning et changelog
Great Expectation           Gestion des tests sur la qualité des données

## 📸 Documentation et suivi

Le fichier Brief Databrick.odt contient :

- un sommaire,
- des captures d’écran de toute l'architecture Azure et Databricks,
- la configuration des clusters Databricks,
- des notes de travail sur le déroulé du projet,
- et des captures d’écran de toute l'architecture Azure et Databricks.

## 🚀 Évolutions prévues

Création de la couche Gold (données prêtes pour la visualisation)

Publication d’une API Databricks pour accès temps réel

## 👩‍💻 Auteur

Hélène Dubourg
Alternante Ingénieur Data

Curieuse, rigoureuse et passionnée par les pipelines bien ficelés.
« Parce qu’une donnée propre, c’est une donnée heureuse. »
