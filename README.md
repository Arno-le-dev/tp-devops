# Docker compose

    version: '3.7'

    services:
        backend:
            build:
                context: ./simple-api-student-main
            networks:
                - app-network
            depends_on:
                - database
            container_name: backend-api

Service __backend__: 
* On indique le contexte du build du conteneur backend 
* On place ce conteneur au sein du network app-network
* On s'assure que ce service soit lancer après le service "database" 
* On nomme le conteneur backend-api

        database:
            build:
                context: ./db
        networks:
            - app-network
            container_name: postgres-epf

Opération similaire au service backend pour le service __database__ 

        httpd:
            build:
                context: ./front
            ports:
                - "80:80"
            networks:
                - app-network
            depends_on:
                - backend
                - database

Service __httpd__ : 
Similaire au services précédents à l'exception du port, on mappe ici le port 80 du conteneur au port 80 de l'hôte. 

        networks:
            app-network:
Définition du réseau app-network 

### Docker-compose most important commands 

* `docker-compose up` : démarre les conteneurs définis dans le fichier docker-compose.yaml
* `docker-compose build` : construit ou reconstruit les images docker définies dans le fichier docker-compose.yaml
* `docker-compose exec` : permet l'exécution d'une commande à l'intérieur d'un conteneur 
* `docker-compose -d` : "-d" permet le démarge en mode détaché 

# Publish

`docker tag db abruno/db:1.0` 
Cette commande nous permet de donner un tag à une image docker existante, ici le tag est `1.0`. 

`docker push USERNAME/my-database:1.0`
On push l'image docker vers notre registre distant (docker Hub), la rendant ainsi accessible aux autres utilisateurs ou à d'autres machiens. 