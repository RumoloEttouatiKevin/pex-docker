# Projet 3 - Les réseaux

## La documentation officielle

[Documentation officielle](https://docs.docker.com/network/drivers/)

## Les types de réseau sous Docker

- bridge : le réseau par défaut, permet aux containers sur le même hôte de communiquer entre eux.
- host : partage du réseau avec celui de l'hôte, il n'y a pas d'isolation du réseau entre l'hôte et le container.
- overlay : pour communiquer entre différent docker sur différent hôtes, ou lors de l'exécution de plusieurs application sur le même hôtes avec Swarm.
- ipvlan : donner l'accès total sur l'adressage IPv4 et IPv6.
- macvlan : donne les même avantages que ipvlan et la possibilité d'attribuer une adresse MAC au container.
- none : isolation complète entre le container et l'hôte.

## Créer un réseau

Pour créer un réseau.

```shell
docker network create --driver=bridge --subnet=192.168.2.0/24 sharenetwork

network # Pour définir qu'on souhaite travailler sur le réseau
--driver=bridge # Pour définir le type de réseau
--subnet=192.168.2.0/24 # Pour définir l'adressage du réseau
sharenetwork # Pour définir le nom du réseau
```

Pour voir les réseaux Docker existants.

```shell
docker network ls
```

## Créer un container sur un réseau spécifique

Pour créer un container sur un réseau spécifique.

```shell
docker run -it --name ubuntu1 --network sharenetwork -d ubuntu /bin/bash
```

Ici, `-it` permet de créer une session interactive dans laquelle on peut interagir et empêche l'exit du container.

On peut voir les informations d'un container.

```shell
docker inspect ubuntu1
```

## Accéder à notre container en mode interactif

Pour accéder au terminal d'un container en mode interactif.

```shell
docker exec -it ubuntu1 /bin/bash
```

## Ping entre les containers

On peut créer plusieurs containers sur le même réseau et on peut tester le ping entre eux soit par l'adresse ip soit par le nom du container.

```shell
ping ubuntu2
# ou
ping 192.168.2.3
```
