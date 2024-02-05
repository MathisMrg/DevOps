# TP1 Devops

## DB

docker build -t mathis/postgrestp1 .
docker images

docker run -d -p 5432:5432 --name postgresTP1 mathis/postgrestp1
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer
docker ps

docker network create app-network

docker run -d -p 5432:5432 --network app-network --name postgresTP1 mathis/postgrestp1
docker run -p "8090:8080" --network app-network --name=adminer -d adminer


| systeme | postgres |
| Serveur | postgresTP1 |
| Utilisateur | usr |
| Mot de passe | pwd |
| Base de données | db |

docker build -t mathis/postgrestp1 .
docker run -d -p 5432:5432 --env-file .env -v /home/mathis/project/DevOps/DevOps/TP1/dataDir:/var/lib/postgresql/data --network app-network --name postgresTP1 mathis/postgrestp1 

## JAVA

docker build -t mathis/javatp1 .
docker run -d -p 8080:8080 --env-file .env --network app-network --name javaTP1 mathis/javatp1

Question :
1-2 Why do we need a multistage build? And explain each step of this dockerfile.

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

docker build -t mathis/httpdtp1 .
docker run -d -p 8081:80 --network app-network --name httpdTP1 mathis/httpdtp1 

docker exec -it httpdTP1 /bin/bash
recuperer le fichier httpd.conf, en faire une copie et le rajouter dans le dockerfile de quoi le copier avec les modif de proxy

## env