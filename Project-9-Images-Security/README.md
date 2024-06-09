# Projet 9 - Sécurité des images

Nous allons utiliser l'utilitaire `snyk` qui permet de checker les vulnérabilités qui peuvent être présentes dans les images que nous utilisons pour nos développements, l'outil est entre autres capable de checker les vulnérabilités de l'application elle-même et bien d'autre utilisation possibles.

Nous commençons par ajouter notre token API de `snyk` aux variables d'environnement de notre hôte.

```shell
export SNYK_TOKEN=829j239n-s3k4-d390-di38-7idoi4820d4o
```

Lancer un test de vulnérabilité sur notre application.

```shell
docker run --rm -it --env SNYK_TOKEN -v $(pwd):/app snyk/snyk:python-2.7

--rm # Permet de préciser de supprimer le container après son exécution
```

Lancer un test de vulnérabilité sur notre image, il faut que l'image soit au préalable buildé.

```shell
docker run --rm -it --env SNYK_TOKEN -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd):/app snyk/snyk:docker snyk test --docker simple-webapp-docker

simple-webapp-docker # Est le nom de l'image que l'on souhaite tester
```

Lancer un test de vulnérabilité sur notre Dockerfile.

```shell
docker run --rm -it --env SNYK_TOKEN -v /var/run/docker.sock:/var/run/docker.sock -v $(pwd):/app snyk/snyk:docker snyk test --docker simple-webapp-docker --file=Dockerfile
```
