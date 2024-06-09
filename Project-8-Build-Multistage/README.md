# Projet 8 - Build multistage

Le build multistage permet de générer une image avec le strict nécessaire pour faire fonctionner l'application, c'est-à-dire que nous allons créer plusieurs couches de build de notre image qui comporteront ce qui est nécessaire à la création d'une image final qui aura le strict nécessaire.

```Dockerfile
# La première couche/image qui sera nommée "base" comportera toutes les instructions suivantes jusqu'au prochain "FROM"
FROM ubuntu:18.04 AS base
WORKDIR /opt
RUN apt update && apt-get -y install software-properties-common
RUN add-apt-repository ppa:deadsnakes/ppa && apt-get update
RUN apt-get install -y python3.6
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 1
RUN apt-get install -y python3-pip locales
COPY requirements.txt /opt
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
RUN pip3 install -r requirements.txt

# La seconde couche/image qui vient de "base" et qui sera nommée "src", ce sera l'image qui contiendra le code source de l'application
FROM base as src
COPY . /opt

# La troisième couche/image qui vient de "src" et qui sera nommée "test", cette image va nous permettre de tester notre application python
FROM src as test
COPY requirements.dev.txt /opt
RUN pip3 install -r requirements.dev.txt
RUN python3 -m pytest

# La quatrième couche/image qui vient également de "src" et qui sera nommée "prod", cette image ne comportera pas tout ce qui est fait dans test
FROM src as prod
ENTRYPOINT FLASK_APP=/opt/app.py FLASK_RUN_PORT=8080 flask run --host=0.0.0.0
```

L'idée principale est de factoriser les images pouvant être réutilisées et permet d'alléger au maximum les images que l'on souhaite.

Pour lancer spécifiquement le build de l'image que l'on souhaite dans cette multicouche d'image.

```shell
docker build --target test --tag test .

--target test # Permet de spécifier qu'elle image, on souhaite builder
```

Cette commande est également utilisable depuis un fichier `docker-compose`.

```shell
version: "3.8"
services:
  web:
    build:
      context: ./app
      target: prod
    volumes:
      - "${PWD}/app:/opt"
    ports:
      - "8080:8080"
```

Nous pouvons spécifier un nom de fichier `docker-compose` qui doit être exécuté lors de l'utilisation de la commande `docker compose`.

```shell
docker compose -f docker-compose.dev.yml up -d
```

On peut aller plus loin et par exemple copier un contenu précis d'une image dans une autre avec le multistage.

```Dockerfile
FROM node:8.11.4-alpine as builder
ARG ENV=prod
COPY package.json ./
RUN yarn install --ignore-engines && mkdir /app && cp -R ./node_modules ./app
WORKDIR /app
COPY . .
RUN source .env.${ENV} && yarn run build

FROM nginx:1.15.2-alpine
COPY nginx/default.conf /etc/nginx/conf.d/
RUN rm -rf /usr/share/nginx/html/*
# Ici, on vient copier le contenu qui se trouve dans "/app/dist" de l'image "builder" pour l'insérer dans cette nouvelle image
COPY --from=builder /app/dist /usr/share/nginx/html
CMD ["nginx", "-g", "daemon off;"]
```
