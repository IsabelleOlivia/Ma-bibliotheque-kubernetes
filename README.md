

# Bibliothèque App

## Description

L'application **Bibliothèque App** est une application Flask qui permet de gérer une bibliothèque (emprunt, retour de livres, etc.). Elle est orchestrée avec Docker et se connecte à une base de données PostgreSQL pour la gestion des données des livres et des utilisateurs.

---

## Structure du projet

```
Bibliothèque/
│
├── app.py                  # Code principal de l'application Flask
├── templates/              # Contient les fichiers HTML (interface utilisateur)
├── data/                   # Répertoire pour persister les données PostgreSQL
├── Dockerfile              # Définition de l'image Docker de l'application
├── docker-compose.yml      # Orchestration des services avec Docker Compose
└── README.md               # Documentation
```

---

## Prérequis

Avant de démarrer l'application, assurez-vous d'avoir installé les outils suivants :

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

---

## Instructions d'exécution avec Docker

### 1. Créer un réseau Docker

Nous devons créer un réseau personnalisé pour permettre à l'application Flask et à la base de données PostgreSQL de communiquer.

```bash
docker network create mynetwork
```

---

### 2. Lancer la base de données PostgreSQL

Lancez le conteneur PostgreSQL en utilisant la commande suivante. Cela crée une base de données PostgreSQL avec un utilisateur `user` et un mot de passe `password`, et les données seront persistées dans le répertoire `./data`.

```bash
docker run -d --name postgres_db --network mynetwork \
  -e POSTGRES_USER=user \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=bibliotheque \
  -v ./data:/var/lib/postgresql/data \
  postgres:latest
```

Cela démarre le conteneur PostgreSQL et le connecte au réseau `mynetwork`.

---

### 3. Lancer l'application Flask

Une fois le conteneur de la base de données en place, vous pouvez maintenant démarrer l'application Flask. Utilisez l'image Docker hébergée sur Docker Hub pour l'application.

```bash
docker run -d --name Ma_bibliotheque --network mynetwork \
  -e DATABASE_URL=postgresql://user:password@postgres_db:5432/bibliotheque \
  -p 5000:5000 \
  isaolivia/ma_bibliotheque:latest
```

- **Nom du conteneur** : `Ma_bibliotheque`
- **Réseau** : `mynetwork`
- **Port d'exposition** : L'application sera accessible sur le port `5000` de votre machine locale.
- **Variable d'environnement `DATABASE_URL`** : Indique l'URL de connexion à la base de données PostgreSQL.

---

### 4. Accéder à l'application

Une fois les conteneurs démarrés, ouvrez votre navigateur et accédez à l'application via :

- **URL** : [http://localhost:5000](http://localhost:5000)

---

## Notes de dépannage

### Problème : Base de données inaccessible

Si l'application Flask ne parvient pas à se connecter à la base de données, voici quelques vérifications possibles :
- **Variables d'environnement** : Assurez-vous que les variables `POSTGRES_USER`, `POSTGRES_PASSWORD`, et `POSTGRES_DB` dans la commande `docker run` de la base de données correspondent à celles utilisées dans l'URL de connexion (`DATABASE_URL`).
- **Dépendance entre les conteneurs** : Vérifiez que le conteneur PostgreSQL est bien lancé avant l'application Flask. Vous pouvez utiliser `docker ps` pour voir si tous les conteneurs sont en cours d'exécution.

---

## Résumé

Avec ces instructions, vous pouvez facilement lancer l'application **Bibliothèque App** en utilisant Docker :

1. Créez un réseau Docker pour la communication entre les services.
2. Démarrez la base de données PostgreSQL.
3. Lancez l'application Flask en utilisant l'image Docker disponible sur Docker Hub.

Bonne utilisation de l'application **Bibliothèque App** ! 🎉

