# Docker 
### Docker compose

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

### Publish

`docker tag db abruno/db:1.0` 
Cette commande nous permet de donner un tag à une image docker existante, ici le tag est `1.0`. 

`docker push USERNAME/my-database:1.0`
On push l'image docker vers notre registre distant (docker Hub), la rendant ainsi accessible aux autres utilisateurs ou à d'autres machiens.


# Back-end API

### Dockerfile
    #Build
    FROM maven:3.8.6-amazoncorretto-17 AS myapp-build
    ENV MYAPP_HOME /opt/myapp
    WORKDIR $MYAPP_HOME
    COPY pom.xml .
    COPY src ./src
    RUN mvn package -DskipTests
    
    #Run
    FROM amazoncorretto:17
    ENV MYAPP_HOME /opt/myapp
    WORKDIR $MYAPP_HOME
    COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar

    ENTRYPOINT java -jar myapp.jar

Le multistage build nous permet de séparer l'étape de build de l'étape de run.
Cela permet de créer une image plus efficace et petite.

Dans l'étape build:

* On récupère une image image d'amazon-corretto avec maven
* On définit une variable d'environnement MYAPP_HOME, en lui définissant le répertoire de l'application.
* Run d'une commande maven pour le build de l'application

Dans l'étape du run :

* On utilise une image de base Amazon Corretto 17 plus légère (sans maven).
* On définit la variable d'environnement MYAPP_HOME et le répertoire de travail.
* On copie le fichier JAR compilé de l'étape de build vers l'étape d'exécution.
* On définit un point d'entrée qui exécute l'application

Le résultat de ce dockerfile à plusieurs étapes est une image Docker qui ne contient que les dépendances d'exécution nécessaires pour exécuter l'application Java, ce qui donne une image plus petite et plus efficace.

# Github actions

#### Test containers


C'est une bibliothèque Java qui aide à configurer et gérer des éléments tels que des bases de données et d'autres services dans les tests.

#### Github actions configurations
1. Création d'un repository github
2. Création des dossiers .github/workflows et du fichier main.yml

#### Quality gate configurations
1. Création d'un compte Sonar Cloud
2. On récupère project key & organization key, on crée les secret correspondant dans gitHub.
3. Modification de main.yml avec l'ajout de :
   `mvn -B verify sonar:sonar -Dsonar.projectKey=devops-2023 -Dsonar.organization=devops-school -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${{ secrets.SONAR_TOKEN }}  --file ./simple-api/pom.xml`
   Cette commande compile le code, exécute des tests, puis envoie les résultats à SonarCloud pour l'analyse de la qualité du code, en utilisant nos token. 


# Ansible


    all:
        vars:
            ansible_user: centos
            ansible_ssh_private_key_file: ../../../id_rsa

Cette section définit des variables globales.
On indique le chemin d'accès vers la clé privé SSH à utiliser pour l'authentification.

        children:
            prod:
                hosts: arno.bruno.takima.cloud

Ici on indique l'hôte "arno.bruno.takima.cloud" dans le groupe "prod".

#### Commandes de base :
`ansible` permet l'exécution de commandes sur des hôtes distants.

`ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"`
Par exemple cette commande exécute setup.yml sur tous les hôtes répertoriés.

### Playbooks

    - hosts: all

Spécifie le groupe d'hôtes sur lesquels le playbook est exécuté.

    gather_facts: false

désactive la collecte des facts

    become: true

Indique à Ansible d'acquérir des droits administrateurs pour certaines actions.

    # Install Docker
    roles:
        - docker
        - network
        - db
        - backend
        - httpd

La section "roles" spécifie les rôles Ansible à exécuter.

#### Docker role documentation

Installation du package `device-mapper-persistent-data` en utilisant le module `yum`. Ce package est utilisé pour la gestion du stockage (gestion des volumes logiques).

    - name: Install device-mapper-persistent-data
        yum:
            name: device-mapper-persistent-data
            state: latest

Installation du package `lvm2`, complémentaire au package `device-mapper-persistent-data` :

    - name: Install lvm2
        yum:
            name: lvm2
            state: latest

Execution d'une commande shell pour ajouter le repo git au container :

    - name: add repo docker
        command:
            cmd: sudo yum-config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo

Installation du package `docker-ce` :

    - name: Install Docker
        yum:
            name: docker-ce
            state: present

On s'assure que le service Docker est démarré :

    - name: Make sure Docker is running
        service: name=docker state=started
            tags: docker

Le compte rendu présente la configuration Docker Compose pour trois services (backend, database, httpd) avec des dépendances et des réseaux. Il explique un Dockerfile en utilisant un multistage build pour le backend API. Il mentionne également l'utilisation de GitHub Actions pour les tests et la qualité du code, ainsi que la configuration d'Ansible pour la gestion d'hôtes distants et l'installation de Docker.