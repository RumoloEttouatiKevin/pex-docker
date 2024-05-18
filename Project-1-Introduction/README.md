# Projet 1 - Introduction

## Installer Docker sur Linux

Suppression de ce qui pourrait exister sur le système.

```shell
sudo apt-get remove docker* containerd runc
```

Mise à jour et installation de curl si ce n'est pas déjà fait.

```shell
sudo apt-get update && sudo apt-get install curl
```

Téléchargement et exécution du script d'installation officiel de Docker.

```shell
curl -fsSL https://get.docker.com -o get-docker.sh
sh get-docker.sh
```

Pour avoir accès à docker depuis notre utilisateur sans passer par root, nous l'ajoutons au groupe docker.

```shell
sudo usermod -aG docker kevin
```

Pour que Docker soit exécuté au démarrage de Linux.

```shell
sudo systemctl enable docker
```

## Tester notre installation

On peut tester l'installation en lançant un conteneur. Ce conteneur est là pour nous retourner un `Hello from Docker!` puis se fermer.

```shell
docker run hello-world
```

On peut également checker les versions.

```shell
docker version
docker compose version
```

On peut lister les containers.

```shell
docker ps
```

## Documentation officielle

[Documentation officielle](https://docs.docker.com/)

## La commande docker run

Explication de la commande la plus utilisée. On peut retrouver tous les arguments possibles avec `docker run --help`.

```shell
docker run --name nginx -d -p 80:80 nginx

--name nginx # Pour définir un nom au container
-d # Pour détacher la fenêtre
-p 80:80 # Pour spécifier les ports du container
80:80 # Le port de gauche défini celui qui est exposé à l'utilisateur, celui de droite est le port interne au container
nginx # L'image que l'on souhaite utiliser
```

## Les variables d'environnement

Pour démarrer un container avec une variable d'environnement, on ajoute l'argument `-e`.

```shell
docker run --name web-color -e APP_COLOR="red" -d -p 8080:8080 kodekloud/webapp-color
```
