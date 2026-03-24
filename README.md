# Prédiction de consommation énergétique des bâtiments — Seattle

> Modèle de Machine Learning (Random Forest) entraîné sur les données de
> benchmarking énergétique de Seattle, exposé via une API BentoML prête
> à l'emploi.

## Contexte & objectif

La ville de Seattle publie chaque année des données de consommation énergétique
de ses bâtiments commerciaux. Ce projet vise à prédire la consommation
énergétique réelle (`SiteEnergyUseWN` en kBtu) à partir des caractéristiques
d'un bâtiment, afin d'anticiper les besoins et d'identifier les bâtiments
énergivores.

## Stack technique

`Python` `Scikit-learn` `Random Forest` `BentoML` `Pandas`

## Architecture du projet
```
├── analyse_features.ipynb      # EDA + feature engineering
├── ml_testing.ipynb            # Comparaison des modèles ML
├── building_features.csv       # Dataset transformé
├── 2016_Building_Energy_Benchmarking.csv  # Données brutes Seattle
├── appli/                      # API BentoML
│   └── ...
└── pyproject.toml
```

## Démarche

**1. Analyse exploratoire (EDA)**
- Exploration et visualisation des distributions de consommation
- Identification des valeurs aberrantes et des corrélations clés

**2. Feature Engineering**
- Sélection et transformation des variables pertinentes
- Features retenues : `PrimaryPropertyType`, `Neighborhood`,
  `NumberofBuildings`, `NumberofFloors`, `PropertyGFABuilding(s)`,
  `UsesSteam`, `UsesElectricity`, `UsesNaturalGas`, `BuildingAge`

**3. Modélisation**
- Comparaison de plusieurs algorithmes ML
- Modèle retenu : **Random Forest Regressor**

| Métrique | Score |
|---|---|
| R² (cross-validation) | **0.82** |
| RMSE (cross-validation) | **1 407 516 kBtu** |

**4. Déploiement**
- Exposition du modèle via une **API REST BentoML**
- Endpoint de prédiction : envoyer les caractéristiques d'un bâtiment,
  recevoir la consommation prédite en kBtu

## Lancer le projet

Prérequis : Python + [Poetry](https://python-poetry.org/docs/#installation) installés

**Étape 1 — Entraîner et exporter le modèle**

Exécuter les notebooks dans l'ordre :
1. `analyse_features.ipynb` — EDA et feature engineering
2. `ml_testing.ipynb` — Entraînement et export du modèle dans le store BentoML

> L'export du modèle (dernière cellule de `ml_testing.ipynb`) est obligatoire
> avant de lancer l'API.

**Étape 2 — Lancer l'API**
```bash
cd appli
poetry install
poetry run bentoml serve service
```

L'API est accessible sur `http://localhost:3000` — une interface Swagger UI
est disponible directement dans le navigateur pour tester les requêtes.

Exemple de requête :
```json
{
  "PrimaryPropertyType": "Office",
  "Neighborhood": "DOWNTOWN",
  "NumberofBuildings": 1,
  "NumberofFloors": 10,
  "PropertyGFABuilding(s)": 50000,
  "UsesSteam": 1,
  "UsesElectricity": 1,
  "UsesNaturalGas": 0,
  "BuildingAge": 35
}
```

## Apprentissages clés

- Pipeline complet de bout en bout : EDA → feature engineering →
  modélisation → déploiement API
- Comparaison rigoureuse de modèles ML avec validation croisée
- Déploiement d'un modèle ML en production avec BentoML
- Travail sur des données réelles de politique publique (Open Data Seattle)
