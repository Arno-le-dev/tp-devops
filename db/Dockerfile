# On utilise l'image de base "postgres:14.1-alpine"
FROM postgres:14.1-alpine

# On définit les variables d'environnement de la bdd Postgresql
ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

# On copie les fiichiers sql dans le répertoire d'initialisation de la bdd
COPY *.sql /docker-entrypoint-initdb.d