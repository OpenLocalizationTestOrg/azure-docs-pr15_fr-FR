<properties
   pageTitle="Gestion des conteneurs conteneur Service Azure avec Docker par essaim | Microsoft Azure"
   description="Déployer des conteneurs sur un Docker par essaim dans Azure conteneur Service"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, conteneurs, Micro-services, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="timlt"/>

# <a name="container-management-with-docker-swarm"></a>Gestion des conteneurs avec Docker par essaim

Docker essaim fournit un environnement pour le déploiement des charges de travail sur un ensemble d’hôtes Docker pool. Docker essaim utilise l’API Docker native. Flux de travail de gestion des conteneurs sur un Docker par essaim est presque identique à celle qui serait sur un hôte seul conteneur. Ce document fournit des exemples simples de déploiement de charges de travail en conteneur dans une instance de Service de conteneur Azure de Docker par essaim. Pour plus d’informations approfondie sur Docker par essaim, voir [Docker par essaim sur Docker.com](https://docs.docker.com/swarm/).

Conditions préalables pour les exercices dans ce document :

[Créer un cluster essaim dans Azure conteneur Service](container-service-deployment.md)

[Se connecter avec le cluster essaim dans Azure conteneur Service](container-service-connect.md)

## <a name="deploy-a-new-container"></a>Déployer un nouveau conteneur

Pour créer un nouveau conteneur dans le Docker par essaim, utilisez la `docker run` commande (s’assurer que vous avez ouvert un tunnel SSH aux masques selon les conditions préalables ci-dessus). Cet exemple crée un conteneur à partir de la `yeasy/simple-web` image :


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Une fois le conteneur a été créé, utilisez `docker ps` pour renvoyer des informations sur le conteneur. Notez ici que l’agent essaim qui héberge le conteneur est répertorié :


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Vous pouvez maintenant accéder à l’application qui s’exécute dans ce conteneur via le nom de l’équilibrage de charge de l’agent essaim DNS public. Ces informations sont disponibles dans le portail Azure :  


![Résultats de la visite réel](media/real-visit.jpg)  

Par défaut, l’équilibrage de charge contient les ports 80, 443 et 8080 ouvert. Si vous voulez vous connecter sur un autre port, vous devez ouvrir ce port sur l’équilibrage de charge Azure pour le Pool de l’Agent.

## <a name="deploy-multiple-containers"></a>Déployer plusieurs conteneurs

Étant donné que plusieurs conteneurs sont démarrées, en exécutant 'exécuter docker' à plusieurs reprises, vous pouvez utiliser la `docker ps` commande pour voir qui héberge les conteneurs sont en cours d’exécution. Dans l’exemple ci-dessous, trois conteneurs sont réparties de manière égale sur les trois agents essaim :  


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## <a name="deploy-containers-by-using-docker-compose"></a>Déployer des conteneurs à l’aide de composer Docker

Vous pouvez utiliser Docker composer pour automatiser le déploiement et la configuration de plusieurs conteneurs. Pour ce faire, assurez-vous qu’un tunnel SSH (Secure Shell) a été créé et que la variable DOCKER_HOST a été définie (voir les conditions préalables ci-dessus).

Créer un fichier docker compose.yml sur votre système local. Pour ce faire, utilisez cet [exemple](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Exécuter `docker-compose up -d` pour démarrer les déploiements conteneur :


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Enfin, la liste de l’exécution de conteneurs est retournée. Cette liste répertorie les conteneurs qui ont été déployés à l’aide de Docker composer :


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

Bien entendu, vous pouvez utiliser `docker-compose ps` pour examiner uniquement les conteneurs définis dans votre `compose.yml` fichier.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus sur Docker par essaim](https://docs.docker.com/swarm/)
