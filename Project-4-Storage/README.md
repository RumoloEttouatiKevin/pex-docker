# Projet 4 - Le stockage

## Les types de stockage

- bind mount : les données d'un container sont stockées dans un répertoire précis que je maîtrise.
- volume : les données d'un container sont stockées à un endroit dont nous n'avons pas forcément la connaissance, si le container est détruit, nous pouvons en recréer un sans se soucier d'où est la donnée, le nouveau container va récupérer la donnée.
- tmpfs mount : permet d'avoir un stockage temporaire pour échanger des données entre les containers, donnée de petite taille et de courte durée.

## Stockage de type volume

Pour créer un volume.

```shell
docker volume create --name share
```

## Stockage de type tmpfs mount

Ensuite, nous lançons notre container en lui précisant le volume à utiliser et notre destination de donnée temporaire.

```shell
docker run -it --name ubuntu1 -v share:/tmp -d ubuntu /bin/bash
docker run -it --name ubuntu2 -v share:/tmp -d ubuntu /bin/bash

-v share:/tmp # ici, on précise le nom du volume et où on souhaite que ce volume soit monté dans le container
```

On peut facilement tester de créer un fichier dans le container 1 à l'endroit `/tmp/` et voir si dans le container 2 le fichier est bien présent.

## Stockage de type bind mount

Pour spécifier où est la donnée source.

```shell
docker run --name webserver -p 80:80 -d -v ${PWD}/Project-2-Images/static-website-example:/usr/local/apache2/htdocs/ httpd

-v ${PWD}/Project-2-Images/static-website-example:/usr/local/apache2/htdocs/ # source:destination
```
