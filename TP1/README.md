# TP1 Devops Docker

## Explication gloabals du projet

- La racine : Un docker-compose qui se base sur les 3 dockerfiles
- dataDir : volumes de la DB qui permet la resilience
- db : Dockerfile, Scripts init et .env
- java : Java project avec db connection, .env, et Dockerfile
- httpd : conf contient la conf override pour le proxy, public-html contient un simple fichier html, et Dockerfile
- docker_compose_using_publish : un docker-compose qui utilise son propre .env et utilise les images push sur docker hub
- TD1 : TD d'entrainement du matin

## DB

### Commdands finals

docker network create app-network

docker build -t mathis/postgrestp1 .

docker images

docker run -p "8090:8080" --net=app-network --name=adminer -d adminer

docker run -d -p 5432:5432 --env-file .env -v /home/mathis/project/DevOps/DevOps/TP1/dataDir:/var/lib/postgresql/data --network app-network --name postgresTP1 mathis/postgrestp1

docker ps

### Question : Why should we run the container with a flag -e to give the environment variables?


L'utilisation du drapeau -e lors de l'exécution d'un conteneur permet de fournir des variables d'environnement essentielles au bon fonctionnement de l'application à l'intérieur du conteneur. Ces variables définissent des paramètres tels que les clés secrètes utiliser -e permet donc une plus grand sécuriter et une flexibilitée.

### Question : Why do we need a volume to be attached to our postgres container?

Les volumes permettent une persistance des données afin de conserver les modifications apporter à la base de données

### Question : 1-1 Document your database container essentials: commands and Dockerfile.

Voir Dockerfile dans dossier db.

### Utile : Connexion au adminer

| syteme          | postgres    |
|-----------------|-------------|
| serveur         | postgresTP1 |
| Utilisateur     | usr         |
| Mot de passe    | pwd         |
| Base de données | db          |

## JAVA

### Commandes finals

docker build -t mathis/javatp1 .

docker run -d -p 8080:8080 --env-file .env --network app-network --name javaTP1 mathis/javatp1

### Question : 1-2 Why do we need a multistage build? And explain each step of this dockerfile.

L'utilisation du multistage build permet de minimiser la taille de l'image finale en éliminant les dépendances de construction et les artefacts temporaires qui ne sont pas nécessaires à l'exécution de l'application lors du build maven.

FROM maven:3.8.6-amazoncorretto-17 AS javaTP1
- we get a docker image with maven and a jdk to build a jar

ENV MYAPP_HOME /opt/myapp
- Définit la variable d'environnement 

WORKDIR $MYAPP_HOME
- Définit le répertoire de travail dans l'image comme étant /opt/myapp.

COPY ./simpleapi/simpleapi/pom.xml .
- Copie le fichier pom.xml du projet dans le répertoire de travail.

COPY ./simpleapi/simpleapi/src ./src
- Copie le répertoire source du projet (./simpleapi/simpleapi/src) dans le répertoire de travail.

RUN mvn package -DskipTests
- Exécute la commande Maven pour construire le package JAR de l'application, en sautant l'exécution des tests.

FROM amazoncorretto:17
- On utilise au final une image plus legere qui contient que de quoi utiliser le jar

ENV MYAPP_HOME /opt/myapp
- Définit à nouveau la variable d'environnement MYAPP_HOME avec la valeur /opt/myapp

WORKDIR $MYAPP_HOME
- Définit le répertoire de travail 

COPY --from=javaTP1 $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
- Copie le fichier JAR généré lors de l'étape de construction (javaTP1) vers le répertoire de travail de cette étape d'exécution.

ENTRYPOINT java -jar myapp.jar
- Définit la commande d'entrée à exécuter lorsqu'un conteneur basé sur cette image est démarré. Dans ce cas, il exécute l'application Java en lançant le fichier JAR myapp.jar.

## Http server

### commandes finals

docker build -t mathis/httpdtp1 .

docker run -d -p 8081:80 --network app-network --name httpdTP1 mathis/httpdtp1 

docker exec -it httpdTP1 /bin/bash

recuperer le fichier httpd.conf, en faire une copie et le rajouter dans le dockerfile de quoi le copier avec les modif de proxy

### Question : Why do we need a reverse proxy?

Un reverse proxy est nécessaire pour plusieurs raisons, notamment pour améliorer la sécurité, optimiser les performances et simplifier la gestion des applications web. IL agit comme une interface entre les clients et les serveurs, le reverse proxy peut fournir une couche de sécurité supplémentaire

## docker-compose

### commandes finals

Il y a 3 docker file et un docker-compose.yml
docker-compose up -d

### Question : Why is docker-compose so important?

Docker-compose est important car il simplifie le déploiement et la gestion d'applications. Il permet la définition et la configuration des services, réseaux et volumes dans un fichier YAML unique

## docker login

### commandes finals

docker tag tp1-backend mathismrg/tp1-backend:1.0
 - permet de creer le tag du backend
docker push mathismrg/tp1-backend:1.0
 - permet de push le tag de l'image que l'on vient de creer

docker tag tp1-database mathismrg/tp1-database:1.0
- permet de creer le tag de la database
docker push mathismrg/tp1-database:1.0
 - permet de push le tag de l'image que l'on vient de creer

docker tag tp1-httpd mathismrg/tp1-httpd:1.0
- permet de creer le tag du proxy
docker push mathismrg/tp1-httpd:1.0
 - permet de push le tag de l'image que l'on vient de creer

### question 1-3 Document docker-compose most important commands. 1-4 Document your docker-compose file. (cf dockerFile à la racine)

- docker-compose up -d: permet de lancer les services qui sont down et de se détacher pour que les containers tournent en arrière-plan

- docker-compose down: permet de stopper les services du docker compose ainsi que les networks ect...

- docker-compose logs -f: permet de voir les logs de tous les services et de follow

- docker-compose ps: permet de voir les services en cours

### Question 1-5 Document your publication commands and published images in dockerhub. (cf commandes finals de la sous partie docker login)

Une fois que tout fonctionne on peut retirer les ports pour avoir accès que au reverse proxy