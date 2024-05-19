# Projet 5 - Infrastructure as code (IaC)

## Installation de docker-compose

docker-compose docker-engine et docker-ci sont déjà installé avec getDocker, mais nous pouvons procéder à une installation manuelle.

Il existe plusieurs façons d'installer docker-compose.

Le plus simple et recommandé par Docker est d'installer docker desktop, télécharger et installer le paquet, voir à mettre à jour le lient pour utiliser la dernière/bonne version. [Documentation officielle](https://docs.docker.com/desktop/install/ubuntu/).

```shell
curl -fsSL "https://desktop.docker.com/linux/main/amd64/149282/docker-desktop-4.30.0-amd64.deb?utm_source=docker&utm_medium=webreferral&utm_campaign=docs-driven-download-linux-amd64&_gl=1*d3a9g2*_ga*MjE2OTU0ODMuMTcxNTk5ODEyOQ..*_ga_XJWPQMJYHQ*MTcxNjA4Njk2Ni41LjEuMTcxNjA4NzA1Ni41Ni4wLjA." -o docker-desktop-4.30.0-amd64.deb
sudo apt-get install ./docker-desktop-4.30.0-amd64.deb
```

Démarrer docker-desktop.

```shell
systemctl --user start docker-desktop
```

Pour que Docker-desktop soit exécuté au démarrage de Linux.

```shell
systemctl --user enable docker-desktop
```

## fichier docker-compose

docker-compose va nous permettre d'automatiser de pouvoir faire du redéploiement et du versioning

La structure d'un fichier docker-compose

```yaml
version: '2' # défini la version de docker-compose
services: # défini les containers qui seront lancés pour cette application
  web: # défini le premier container qui portera le nom "web"
    image: odoo:12.0 # image de base
    depends_on: # permet de définir quel autre container doit être démarré pour que celui-ci démarre
      - db
    ports: # défini les ports exposés
      - "8069:8069"
    volumes: # défini les volumes du containers
      - odoo-web-data:/var/lib/odoo # type volumes "odoo-web-data" est le nom du volume
      - ./config:/etc/odoo # type bind mount
      - ./addons:/mnt/extra-addons
    networks: # défini le réseau à utiliser
      - odoo_network

  db: # défini le second container qui portera le nom "db"
    image: postgres:10
    environment: # défini les variables d'environnement
      - POSTGRES_DB=postgres
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_USER=odoo
      - PGDATA=/var/lib/postgresql/data/pgdata
    volumes:
      - odoo-db-data:/var/lib/postgresql/data/pgdata
    networks:
      - odoo_network

volumes: # défini les volumes à créer
  odoo-web-data: # nom du volume
  odoo-db-data:
networks: # défini les réseaux à créer
  odoo_network: # nom du réseau
```

## Exécuter le fichier

Pour exécuter le fichier `docker-compose.yml`.

```shell
docker compose up -d
```
