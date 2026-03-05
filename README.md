# Docker Guide

## Docker, c’est quoi ?
Docker est un gestionnaire de **conteneurs**.  
Son intérêt principal est de rendre des outils et des environnements de travail **portables** et reproductibles.  

Par exemple, si vous travaillez sur un projet en groupe avec un ami, comment être sûr que vous avez exactement la même version du langage, du système d’exploitation ou du compilateur ?  
Docker permet de créer des environnements **isolés**, **légers** et **facilement partageables** pour résoudre ce problème.

---

## Conteneur
Un conteneur peut être vu comme une **application isolée**.  
Il contient tout ce dont elle a besoin pour fonctionner : le code, les dépendances, les bibliothèques, les fichiers de configuration, etc.

---

## Volumes
Les volumes permettent de stocker des données **persistantes**.  
Ils se connectent aux conteneurs et permettent de conserver les informations même si le conteneur est supprimé.  
Ils sont particulièrement utiles pour les bases de données.

---

## Conteneur vs Machine virtuelle
- Une **machine virtuelle** simule un système d’exploitation complet avec tous ses composants.  
- Un **conteneur** n’inclut que ce qui est nécessaire pour exécuter l’application qui lui est dédiée, ce qui le rend plus léger et rapide à lancer.

---

## Images
Une image est un fichier permettant de créer un conteneur **préconfiguré**.  
C’est le cœur du partage des conteneurs : on distribue une image, et chacun peut créer un conteneur identique à partir de celle-ci.  
Dans l’écosystème Docker, une image sert de modèle **immuable** pour lancer des conteneurs.

---

## Dockerfile
Un Dockerfile décrit les différentes étapes pour construire une image.  

Principales instructions :  
```
FROM     : Spécifie l'image de base à utiliser pour construire notre image.
RUN      : Exécute des commandes, principalement pour installer des logiciels ou configurer l’image.
EXPOSE   : Ouvre un port pour permettre la communication avec le conteneur.
CMD      : Définit la commande à exécuter lors du démarrage du conteneur.
ENTRYPOINT: Spécifie l’exécutable par défaut du conteneur.
COPY     : Copie des fichiers ou dossiers depuis l’hôte vers le conteneur.
ADD      : Similaire à COPY, avec quelques fonctionnalités supplémentaires (extraction d’archives, téléchargement depuis une URL).
```

---

## Docker compose
Docker Compose est un outil permettant de gérer un ensemble de services répartis dans plusieurs conteneurs.
Il fournit plusieurs commandes pour gérer vos conteneurs sans avoir à les manipuler individuellement.

Exemples de commandes :
```
docker compose build   : Crée l'image de tous les services définis dans le compose.yaml.
docker compose run     : Construit et lance tous les conteneurs nécessaires à l'exécution des services.
docker compose down    : Arrête et supprime les conteneurs.
docker compose stop    : Arrête les conteneurs sans les supprimer.
```

exemple d'un compose.yaml :
```
services:
  nginx:
    build:
      nginx/
    ports:
      - "443:443"

  mariadb:
    build:
      mariadb/
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:

```
