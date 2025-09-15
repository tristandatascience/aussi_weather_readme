# AussieWeatherFlow

**Pipeline automatisé de prévision météo (pluie à J+1) en Australie avec déploiement MLOps complet**

## 🛠️ Stack Technique

![Python](https://img.shields.io/badge/Python-3.8+-blue?logo=python&logoColor=white)
![Airflow](https://img.shields.io/badge/Apache%20Airflow-017CEE?logo=apache-airflow&logoColor=white)
![MLflow](https://img.shields.io/badge/MLflow-0194E2?logo=mlflow&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?logo=fastapi&logoColor=white)
![Streamlit](https://img.shields.io/badge/Streamlit-FF4B4B?logo=streamlit&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-336791?logo=postgresql&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-F46800?logo=grafana&logoColor=white)

---

## 🎯 Vue d'ensemble

Pipeline ML end-to-end automatisant la prédiction de pluie en Australie :
- **Collecte automatisée** des données météo
- **Entraînement** et sélection du meilleur modèle (Random Forest)
- **Déploiement continu** avec API REST et interface utilisateur
- **Monitoring** complet des performances

## 🏗️ Architecture

![Architecture AussieWeatherFlow](./doc/images/FluxAussieWeatherFlow(1).svg)

**Flux de données :** Scraping → ETL/Airflow → PostgreSQL → MLflow → API FastAPI → Dashboard Streamlit → Monitoring

## 🔄 Pipelines Airflow

### DAGs et orchestration

| DAG | Fréquence | Description |
|-----|-----------|-------------|
| **Collecte quotidienne** | Quotidien | Scraping, nettoyage, insertion BDD |
| **Entraînement** | Hebdomadaire | Training, validation, déploiement auto |
| **Pipeline unifié** | Manuel | Orchestration complète via Streamlit |
| **Tests** | À la demande | Validation pipelines et modèles |

### Fonctionnalités détaillées

**🔍 DAG Collecte :**
- **1** : acquire_data - Scraping des données météo depuis les sources publiques (BOM Australia)
- **2** : clean_data - Nettoyage et validation des données (detection des valeurs aberrantes, imputation des données manquantes)
- **3** : feature_engineering - Feature engineering (calculs de variables dérivées, encodage des variables catégorielles)
- **4** : csv_to_postgres - Insertion en base PostgreSQL avec contrôles d'intégrité et validation des schémas

**🤖 DAG Entraînement :**
- **1** : sql_to_df - Extraction des données depuis PostgreSQL avec fenêtre temporelle glissante
- **2** : prepare_data - Préparation des features (normalisation, split train/test/validation)
- **3** : train_model - Entraînement Random Forest avec optimisation des hyperparamètres via GridSearchCV
- **4** : select_best_model - Sélection automatique basée sur comparaison des métriques F1-score avec le modèle en production
- **5** : deploy_model - Déploiement automatique si performance supérieure (MLflow Model Registry + API update)

**🧪 DAG Tests :**
- **1** : test_acquire_data - Tests unitaires du module d'acquisition de données
- **2** : test_clean_data - Tests unitaires du module de nettoyage de données
- **3** : test_model_functions - Tests unitaires des fonctions de modélisation
- **4** : test_pipeline_integration - Tests d'intégration du pipeline complet

**🔄 DAG Combiné :**
- **1** : full_pipeline - Exécution combinée collecte + entraînement en une seule orchestration

**⚡ Exécution à la demande :**
- **Airflow UI** : Trigger manuel de n'importe quel DAG et Task
- **Interface Streamlit** : 
  - Lancement pipeline complet en un clic
  - **Sélection de modèles** : choix parmi tous les modèles MLflow
  - **Comparaison interactive** : métriques côte-à-côte
  - **Rollback** : retour à un modèle précédent si nécessaire

**🎯 Principe de sélection du meilleur modèle :**
1. **Métriques primaires** : F1-score, Precision, Recall sur jeu de test
2. **Seuil de performance** : amélioration minimale de 1% pour déploiement

## 🚀 Démarrage rapide

```bash
# Production
make -f Makefile.prod init-airflow
make -f Makefile.prod start

# Développement  
make -f Makefile.dev init-airflow
make -f Makefile.dev start
```

## 🌐 Services disponibles

### Services applicatifs

| Service | URL | Description |
|---------|-----|-------------|
| **Airflow** | [localhost:8080](http://localhost:8080) | Orchestration des pipelines |
| **MLflow** | [localhost:5000](http://localhost:5000) | Gestion des modèles ML |
| **API** | [localhost:8000/docs](http://localhost:8000/docs) | Documentation API interactive |
| **Dashboard** | [localhost:8501](http://localhost:8501) | Interface utilisateur |
| **Monitoring** | [localhost:3000](http://localhost:3000) | Dashboards Grafana |

### Infrastructure & Données

| Service | URL | Description |
|---------|-----|-------------|
| **PostgreSQL** | localhost:5432 | Base de données relationnelle pour les données météo |
| **MinIO** | localhost:9000 | Stockage d'objets pour les données brutes et modèles |
| **Prometheus** | localhost:9090 | Monitoring des métriques système et applicatives |

## 🔧 CI/CD & Monitoring

- **GitHub Actions** : Tests automatisés + build Docker
- **Prometheus/Grafana** : Monitoring système et performances ML via Node Exporter
- **Docker** : Déploiement containerisé reproductible

## 📄 Documentation détaillée

### Interfaces utilisateur
- [**Streamlit**](doc/streamlit.md) - Interface utilisateur principale et administration
- [**Airflow**](doc/airflow.md) - Orchestration des DAGs et monitoring
- [**Grafana**](doc/grafana.md) - Monitoring système et infrastructure
- [**MLflow**](doc/mlflow.md) - Gestion des modèles et expérimentations

## 📄 Licence

MIT License - Projet réalisé par **Tristan Lozahic**
