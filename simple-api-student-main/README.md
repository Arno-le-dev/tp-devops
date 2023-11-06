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



