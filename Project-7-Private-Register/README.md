# Projet 7 - Registre privé

## Création d'un registre privé

Nous commençons par créer un réseau.

```shell
docker network create privateRegister
```

Création de notre container back-end, c'est lui qui contiendra nos images privées.

```shell
docker run -d -p 5000:5000 -e REGISTRY_STORAGE_DELETE_ENABLED=true --net privateRegister --name privateRegister-back registry:2
```

Création de notre container front-end, ça nous donnera un accès visuel à notre registre privé.

```shell
docker run -d -p 8090:80 -e REGISTRY_URL=http://private-register:5000 -e DELETE_IMAGES=true -e REGISTRY_TITLE=privateRegister --net privateRegister --name privateRegister-front joxit/docker-registry-ui:main
```

## Push une image registre local

On peut push une image dans notre registre privé en local.

On commence par avoir une image, ici nginx pour le test.

```shell
docker pull nginx
```

Il faut modifier le tag de notre image, par défaut une image est push sur docker hub et là, il faut que lui préciser sur quel registre on souhaite push notre image.

On récupère l'id de notre image.

```shell
docker images
```

On modifie le tag de notre image.

```shell
docker tag 4f67c83422ec localhost:5000/nginx:local

4f67c83422ec # id de l'image
localhost:5000 # url du registre
nginx:local # nom de l'image dans le registre
```

Push de l'image dans le registre.

```shell
docker push localhost:5000/nginx:local
```

## Push une image registre distant

Il faut modifier le tag de notre image et lui donner la bonne url qui pointe sur le port 5000.

```shell
docker tag 4f67c83422ec test.fr/mon-registre/nginx:remote
```
