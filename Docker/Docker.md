# Cheatsheet Docker

- [Index](/Readme.md)
- [Docs](https://docs.docker.com/)

## Commandes de base
### Lister les commandes Docker disponibles:
    docker 

### Obtenir de l'aide sur une commande:
    docker <commande> --help

## Images
### Télécharger une image:
    docker pull <nom_image>

### Lister les images disponibles
    docker images

### Supprimer une image
    docker rmi <image_id>

## Conteneurs
### Créer et exécuter un conteneur:
    docker run <nom_image>

### Créer et exécuter un conteneur en arrière-plan:
    docker run -d <nom_image>

### Nommer un conteneur:
    docker run --name <nom_conteneur> <nom_image>

### Lister les conteneurs en cours d'exécution:
    docker ps

### Lister tous les conteneurs (actifs et arrêtés):
    docker ps -a

### Arrêter un conteneur:
    docker stop <conteneur_id>

### Démarrer un conteneur arrêté:
    docker start <conteneur_id>

## Supprimer un conteneur:
    docker rm <conteneur_id>

## Accéder à un conteneur en cours d'exécution:
    docker exec -it <conteneur_id> /bin/bash


## Réseaux
### Lister les réseaux:
    docker network ls

### Créer un réseau:
    docker network create <nom_reseau>

### Connecter un conteneur à un réseau:
    docker network connect <nom_reseau> <conteneur_id>

### Déconnecter un conteneur d'un réseau:
    docker network disconnect <nom_reseau> <conteneur_id>

## Volumes
### Lister les volumes:
    docker volume ls

### Créer un volume:
    docker volume create <nom_volume>

### Attacher un volume à un conteneur:
    docker run -v <nom_volume>:/chemin_dans_conteneur <nom_image>

### Supprimer un volume:
    docker volume rm <nom_volume>

## Docker Compose
### Démarrer des services définis dans un fichier docker-compose.yml:
    docker-compose up

### Démarrer en arrière-plan:
    docker-compose up -d

### Arrêter des services:
    docker-compose down

### Afficher les logs des services:
    docker-compose logs

### Exécuter une commande dans un service:
    docker-compose exec <nom_service> <commande>

## Nettoyage
### Supprimer les conteneurs arrêtés:
    docker container prune

### Supprimer les images inutilisées:
    docker image prune

### Supprimer les volumes non utilisés:
    docker volume prune

### Supprimer tout (conteneurs, images, volumes, réseaux):
    docker system prune -a
