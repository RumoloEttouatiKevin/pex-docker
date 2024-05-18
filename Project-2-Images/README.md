# Projet 1 - Les images

## Dockerfile

Le Dockerfile va nous permettre de mettre en place notre image en suivant une suite logique de commande.

```Dockerfile
# FROM : défini depuis quelle est l'image de base
FROM ubuntu:18.04

# LABEL : permet de définir des métadonnées à une image, ici on défini qui contacter au sujet de cette image
LABEL maintainer="Kevin kevin.test@test.com"

# RUN : permet d'exécuter des commandes shell lors de la création du container
RUN apt-get update

# EXPOSE : permet d'ouvrir le port 80 du container depuis l'extérieur
EXPOSE 80

# ADD : permet de copier le fichier/dossier source vers une destination du container
ADD static-website-example/ /var/www/html/

# ENTRYPOINT : permet de définir comment l'application va être lancée
ENTRYPOINT [ "/usr/sbin/nginx", "-g", "daemon off;" ]
```

Pour builder une image en reprenant les instructions du Dockerfile.

```shell
docker build -t webapp:v1 .

build # Pour définir qu'on souhaite construire une image
-t # Pour définir le nom de l'image ainsi que sa version
. # Pour définir le context qui contient le Dockerfile (ici le Dockerfile est là où on exécute la commande)
```

Pour voir les images construites sur notre système.

```shell
docker images
```

On peut maintenant créer un container depuis cette image.

```shell
docker run --name webapp -d -p 80:80 webapp:v1
```

## Push notre image sur dockerhub

Pour connecter notre machine à notre dockerhub, il faut ensuite suivre les instructions.

```shell
docker login
# ou
docker login -u monIdentifiantDockerHub
```

On peut utiliser le mot de passe lié au compte, mais il est préférable d'utiliser un token qui est generable depuis le compte dockerhub.

On peut modifier le tag d'une image.

```shell
docker tag idDeLImage monIdentifiantDockerHub/webapp:v1
# l'id de l'image est trouvable avec la commande `docker images`
```

On push l'image sur notre repositories dockerhub.

```shell
docker push monIdentifiantDockerHub/webapp:v1
```

## Automatiser la récupération du code depuis GitHub

Le fichier actuel utilise `ADD` pour récupérer le code depuis une source locale. Ici, nous allons modifier notre fichier pour lui indiquer de récupérer le code depuis un repo distant.

```Dockerfile
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y git
RUN rm -R /var/www/html/*
RUN git clone git@github.com:diranetafen/static-website-example.git /var/www/html/
```

Bien évidemment, ici, il s'agit d'un repo public, il faut adapter le Dockerfile si on utilise des repos privés.
