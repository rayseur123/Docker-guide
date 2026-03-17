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

### Volumes vs Bind mount
Il existe deux façons principales de persister des données avec Docker : les volumes et les bind mounts.
- **Un volume** est **géré directement par Docker**. Il est stocké dans un emplacement spécifique sur le host (souvent dans /var/lib/docker/volumes).
Docker s’occupe entièrement de sa gestion (création, suppression, isolation).

- **Un bind mount** consiste à lier un dossier ou fichier du host à un chemin dans le conteneur.
Ici, les données restent entièrement contrôlées par le **système hôte, pas par Docker.**
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
Un Dockerfile est constitué d’une succession d’instructions (FROM, RUN, COPY, etc.).
Chaque instruction crée une couche (layer).

Une couche correspond à une modification du système de fichiers de l’image.
Ces couches sont empilées les unes sur les autres pour former l’image finale. 

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
Ici, nous parlons de couches : l’intérêt est donc d’empiler des instructions.
Une fois une couche exécutée, elle n’est plus modifiable.
L’avantage est de gagner du temps lors du rebuild : si la 3eme couche est modifiée, la reconstruction reprendra à partir de celle-ci et évitera de réexécuter les couches précédentes.

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
Exemple d'un compose.yaml complet pour inception :
```
services:
  mariadb:
    build: mariadb/
    networks:
      - wp_net
    ports:
      - "3306:3306"
    volumes:
      - db_data:/var/lib/mysql
    env_file:
      - .env
    secrets:
      - db_password
    restart: always
    healthcheck:
      test: ["CMD", "mariadb-admin", "ping", "-h", "localhost", "--password=$$(cat /run/secrets/db_password)"]
      interval: 5s
      timeout: 5s
      retries: 10

  wordpress:
    build: wordpress/
    depends_on:
      mariadb:
        condition: service_healthy
    networks:
      - wp_net
    volumes:
      - wp_data:/var/www/html
    env_file:
      - .env
    secrets:
      - db_password
      - wp_admin_pwd
      - wp_admin_mail
      - wp_admin_user
      - wp_user_pwd
      - wp_user_mail
      - wp_user_user
    restart: always

  nginx:
    build: nginx/
    depends_on:
      wordpress:
        condition: service_started
    networks:
      - wp_net
    ports:
      - "443:443"
    volumes:
      - wp_data:/var/www/html
    restart: always

volumes:
  db_data:
    driver_opts:
      type: none
      device: "/home/njooris/data/db/"
      o: bind
  wp_data:
    driver_opts:
      type: none
      device: "/home/njooris/data/wordpress/"
      o: bind

secrets:
  db_password:
    file: ./.secrets/db_password.txt
  wp_admin_pwd:
    file: ./.secrets/wp_admin_password.txt
  wp_admin_mail:
    file: ./.secrets/wp_admin_mail.txt
  wp_admin_user:
    file: ./.secrets/wp_admin_user.txt
  wp_user_pwd:
    file: ./.secrets/wp_user_password.txt
  wp_user_mail:
    file: ./.secrets/wp_user_mail.txt
  wp_user_user:
    file: ./.secrets/wp_user_user.txt
networks:
  wp_net:
    driver: bridge

```

## Docker networking
Docker propose un système de réseau virtuel entre les conteneurs.
C’est grâce à cet outil que l’on peut connecter les conteneurs entre eux et leur permettre de communiquer.

Docker intègre un DNS interne qui permet de remplacer les adresses IP par les noms de services.
Ainsi, un conteneur peut accéder à un autre simplement en utilisant son nom (ex : database, backend, etc.), sans avoir besoin de connaître son IP.

Docker propose plusieurs types de réseaux, chacun ayant un usage spécifique :

```
bridge   : Réseau par défaut. Permet la communication entre conteneurs sur une même machine.
host     : Le conteneur partage directement le réseau de la machine hôte (pas d’isolation réseau).
none     : Aucun réseau (le conteneur est totalement isolé).
overlay  : Permet de connecter des conteneurs sur plusieurs machines.
macvlan  : Attribue une adresse IP du réseau physique au conteneur (comme une machine réelle).
```
