# Database

__Dockerfile__

*On utilise l'image de base "postgres:14.1-alpine"*
FROM postgres:14.1-alpine

*On définit les variables d'environnement de la bdd Postgresql*
ENV POSTGRES_DB=db \
POSTGRES_USER=usr \
POSTGRES_PASSWORD=pwd

*On copie les fichiers sql dans le répertoire d'initialisation de la bdd*
COPY *.sql /docker-entrypoint-initdb.d

__Network__

        docker network create app-network

__Adminer__


    docker run \
    -p "8090:8080" \
    --net=app-network \
    --name=adminer \
    -d \
    adminer

__Run adminer & database__

    docker run -p 8888:5000 --name mydatabase —network app-network juliedlg/mydatabase


