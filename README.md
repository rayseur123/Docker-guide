# Docker-guide

## Docker ? c'est quoi ?
Docker est un gestionnaire de **containers**.
L’intérêt est de rendre des outils et des environnements de travail **portables**.
Imaginons que vous vouliez travailler sur un projet de groupe avec un ami. Qu’est-ce qui vous garantit d’avoir la même version du langage que votre camarade ? De même pour le système d’exploitation ou encore pour le compilateur.
Docker permet ainsi de créer des environnements **isolés**, **légers** et **facilement partageables**.

### Container
On peut voir un conteneur comme une **application isolée**.
Celui-ci embarque tout le nécessaire pour fonctionner (code, dépendances, bibliothèques, fichiers de configuration, etc.).

#### Container vs Virtual machine
Une machine virtuelle contient tout ce qui est nécessaire pour simuler un système d’exploitation complet.
Là où un conteneur cherche, en théorie, à inclure uniquement ce qui est nécessaire au fonctionnement de l’application qui lui est dédiée.

### Images
Une image est un fichier permettant de créer un conteneur **préconfiguré**.
C’est le cœur du mécanisme de partage des conteneurs : on distribue une image, et chacun peut créer un conteneur identique à partir de celle-ci.
Dans l’écosystème de Docker, une image sert de modèle **immuable** pour lancer des conteneurs.

### Dockerfile
Un dockerfile permet de représenter les différentes couches à mettre en place pour construire une image.

#### Voici un guide :
```
**FROM** : Permet de spécifier l'image qui servira de base pour notre propre image.

**RUN**  : Permet d'exécuter des commandes. Il servira surtout à installer / créer
           l'arborescence de notre image.

**EXPOSE** : Permet d'ouvrir un port.

**CMD**  : Permet de spécifier la commande à exécuter à la création du conteneur.

**ENTRYPOINT** : Permet de spécifier l'exécutable par défaut à la création du conteneur.

**COPY**  : Permet de copier un fichier/ un ensemble de fichiers dans le container.
```
