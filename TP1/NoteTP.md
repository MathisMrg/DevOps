# TP1 Devops

docker build -t mathis/postgrestp1 .
docker images

docker run -d -p 8888:5432 --name postgresTP1 mathis/postgrestp1
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer
docker ps

docker network create app-network

docker run -d -p 8888:5432 --network app-network --name postgresTP1 mathis/postgrestp1
docker run -p "8090:8080" --network app-network --name=adminer -d adminer


| systeme | postgres |
| Serveur | postgresTP1 |
| Utilisateur | usr |
| Mot de passe | pwd |
| Base de données | db |

docker build -t mathis/postgrestp1 .
docker run -d -p 8888:5432 --env-file .env -v /home/mathis/project/DevOps/DevOps/TP1/dataDir:/var/lib/postgresql/data --network app-network --name postgresTP1 mathis/postgrestp1 
