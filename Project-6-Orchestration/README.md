# Projet 6 - L'orchestration

## Création d'un cluster avec docker-swarm

Pour initialiser un cluster swarm.

Ici, nous créons un environnement dit "manager" et la sortie de la commande, nous fournit une commande docker-swarm pour pouvoir initialiser un environnement dit "worker".

```shell
docker swarm init
```

```exit
Swarm initialized: current node (03h8isq3l9dntliejrla4vuy5) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-209lpf1lq6yqpx06pfdx8xl8zscsc9fnobjw87n6umuws9mjdl-chtjl5l3sgefdedmr7u9l9bq7 172.17.64.144:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
```

Ici, l'adresse IP du cluster est l'adresse interne du Docker, si nous restons sur le même hôte pas de souci, pour fournir l'adresse IP de l'hôte et pouvoir communiquer entre plusieurs hôtes.

```shell
docker swarm init --advertise-addr IP.DE.LA.MACHINE
```

On peut voir les nodes "worker" depuis l'hôte "manager".

```shell
docker node ls
```

## Utilisation de docker-compose

Nous pouvons utiliser docker-compose (IaC) pour déployer une infrastructure swarm. À noter que pour swarm la notion de docker-compose s'appelle docker-stack.

```yaml
version: "3.9"
services:
  redis:
    image: redis:alpine
    networks:
      - frontend
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "postgres"
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - backend
  vote:
    image: dockersamples/examplevotingapp_vote
    ports:
      - 5000:80
    networks:
      - frontend
    deploy: # défini les paramètres du déploiement du cluster
      replicas: 2 # défini le nombre de replica de l'application
  result:
    image: dockersamples/examplevotingapp_result
    ports:
      - 5001:80
    networks:
      - backend
  worker:
    image: dockersamples/examplevotingapp_worker
    networks:
      - frontend
      - backend
    deploy:
      replicas: 2
networks:
  frontend:
  backend:
volumes:
  db-data:
```

Pour déployer notre stack.

```shell
docker stack deploy -c docker-stack.yml vote-stack

-c docker-stack.yml # fichier docker-stack à utiliser
vote-stack # nom de la stack de déploiement
```

Pour voir les stack.

```shell
docker stack ls
```

Pour avoir plus d'information.

```shell
docker stack ps vote-stack
```

Avec cette dernière commande, on peut voir sur quel nodes sont installé les différents replicas, on peut donc facilement voir si les replicas se sont déployé sur les "workers".
