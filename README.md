# Airflow Weather Data Pipeline

Projet de Data Engineering réalisé avec Apache Airflow, Docker, PostgreSQL, pgAdmin, Metabase et l’API OpenWeatherMap.

## Objectif

Construire un pipeline ETL automatisé qui récupère des données météorologiques depuis l’API OpenWeatherMap, les stocke dans PostgreSQL et les visualise dans Metabase.

## Architecture

```text
OpenWeatherMap API
        |
        v
Apache Airflow
   weather_etl DAG
        |
        v
PostgreSQL
   weatherdb
      /   \
     /     \
pgAdmin   Metabase
            |
            v
     Weather Dashboard
```

## Technologies utilisées

* Apache Airflow 2.9.1
* Docker / Docker Compose
* PostgreSQL 13
* pgAdmin 4
* Metabase
* Python
* OpenWeatherMap API
* psycopg2
* requests

## Fonctionnement du pipeline

Le DAG Airflow `weather_etl` est planifié avec :

```text
@daily
```

Il récupère les données météo de plusieurs villes britanniques :

* London
* Birmingham
* Manchester
* Glasgow
* Liverpool
* Leeds
* Sheffield
* Bristol
* Newcastle
* Nottingham

Pour chaque ville, le pipeline récupère :

* la température
* l’humidité
* la description météo
* la date

Les données sont ensuite insérées dans la table PostgreSQL `weather`.

## Structure des données

La table `weather` contient les champs suivants :

```text
id
city
temperature
humidity
weather_description
date
```

## Ports utilisés

Pour éviter les conflits avec d’autres environnements Docker locaux, certains ports ont été modifiés.

```text
Airflow      : http://localhost:8083
pgAdmin      : http://localhost:5050
Metabase     : http://localhost:3000
PostgreSQL   : localhost:5433
```

À l’intérieur du réseau Docker, PostgreSQL reste accessible via :

```text
postgres:5432
```

## Installation

Cloner le projet :

```bash
git clone https://github.com/datasifaw/airflow-weather-pipeline.git
cd airflow-weather-pipeline
```

Créer le fichier `.env` :

```bash
cp .env.example .env
```

Puis renseigner les variables nécessaires :

```env
POSTGRES_USER=
POSTGRES_PASSWORD=
POSTGRES_DB=weatherdb

PGADMIN_DEFAULT_EMAIL=
PGADMIN_DEFAULT_PASSWORD=

AIRFLOW_USER=
AIRFLOW_PASSWORD=

OPENWEATHER_API_KEY=
```

Le fichier `.env` est ignoré par Git afin de protéger les informations sensibles.

## Lancement

Démarrer l’environnement :

```bash
docker compose up -d
```

Vérifier les conteneurs :

```bash
docker compose ps
```

## Apache Airflow

Interface :

```text
http://localhost:8083
```

DAG principal :

```text
weather_etl
```

Le pipeline est exécuté quotidiennement avec Airflow.

## PostgreSQL

La base utilisée est :

```text
weatherdb
```

Exemple de vérification :

```bash
docker compose exec postgres psql -U airflow_weather -d weatherdb -c "SELECT COUNT(*) FROM weather;"
```

Afficher quelques données :

```bash
docker compose exec postgres psql -U airflow_weather -d weatherdb -c "SELECT * FROM weather LIMIT 10;"
```

## pgAdmin

Interface :

```text
http://localhost:5050
```

Connexion PostgreSQL depuis pgAdmin :

```text
Host     : postgres
Port     : 5432
Database : weatherdb
```

## Metabase

Interface :

```text
http://localhost:3000
```

Metabase est connecté directement à PostgreSQL via le réseau Docker :

```text
Host     : postgres
Port     : 5432
Database : weatherdb
```

## Dashboard

Le dashboard final contient plusieurs visualisations :

* Température moyenne par ville
* Humidité moyenne par ville
* Évolution quotidienne de la température
* Répartition des conditions météo

## Pipeline complet

```text
OpenWeatherMap
      |
      | API REST
      v
Apache Airflow
      |
      | PythonOperator
      v
PostgreSQL
      |
      +------> pgAdmin
      |
      +------> Metabase
                   |
                   v
          Weather Dashboard
```

## Sécurité

Les secrets ne sont pas stockés dans Git.

Le fichier suivant est ignoré :

```text
.env
```

Il contient notamment :

* clé API OpenWeatherMap
* mot de passe PostgreSQL
* identifiants pgAdmin
* identifiants Airflow

## Auteur

Projet réalisé dans le cadre d’un cours Apache Airflow / Data Engineering.
