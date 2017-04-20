<properties
   pageTitle="Gestion des conteneurs conteneur Service Azure via l’API REST | Microsoft Azure"
   description="Déployer des conteneurs à un cluster Azure conteneur Service Mesos à l’aide de l’API REST Marathon."
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

# <a name="container-management-through-the-rest-api"></a>Gestion des conteneurs via l’API REST

Contrôleur de domaine/système d’exploitation fournit un environnement de déploiement et de mise à l’échelle de charges de travail groupés, tout en s’extrayant du matériel sous-jacent. En haut DC/système d’exploitation, il est une infrastructure qui gère la planification et de l’exécution de charges de travail de calcul.

Bien que les cadres sont disponibles pour les nombreuses charges de travail courantes, ce document décrit comment vous pouvez créer et mettre à l’échelle des déploiements conteneur à l’aide de Marathon. Avant de suivre ces exemples, vous avez besoin d’un cluster DC/système d’exploitation qui n’est configuré dans Azure conteneur de Service. Vous devez également disposer d’une connectivité à distance à ce cluster. Pour plus d’informations sur ces éléments, voir les articles suivants :

- [Déployer un cluster de Service de conteneur Azure](container-service-deployment.md)
- [Connexion à un cluster de Service de conteneur Azure](container-service-connect.md)

Une fois que vous êtes connecté au cluster Azure conteneur Service, vous pouvez accéder à la DC/système d’exploitation et des API REST connexes via http://localhost:local port. Les exemples dans ce document part du principe que vous créez un tunnel sur le port 80. Par exemple, le point de terminaison Marathon pouvez être contacté `http://localhost/marathon/v2/`. Pour plus d’informations sur les différentes API, voir la documentation mésosphère de l' [API Marathon](https://mesosphere.github.io/marathon/docs/rest-api.html) et l' [API Chronos](https://mesos.github.io/chronos/docs/api.html)et la documentation Apache de l' [API du Planificateur de Mesos](http://mesos.apache.org/documentation/latest/scheduler-http-api/).

## <a name="gather-information-from-dcos-and-marathon"></a>Collectez les informations DC/système d’exploitation et Marathon

Avant de déployer conteneurs au cluster DC/OS, recueillir des informations sur le cluster DC/système d’exploitation, tels que les noms et l’état actuel d’agents DC/OS. Pour ce faire, interroger le `master/slaves` point de terminaison de l’API REST DC/système d’exploitation. Si tout se passe bien, vous verrez une liste des agents DC/système d’exploitation et diverses propriétés pour chacun.

```bash
curl http://localhost/mesos/master/slaves
```

À présent, utilisez le Marathon `/apps` point de terminaison pour vérifier les déploiements d’application en cours pour le cluster DC/système d’exploitation. S’il s’agit d’un nouveau cluster, vous verrez un tableau vide pour les applications.

```
curl localhost/marathon/v2/apps

{"apps":[]}
```

## <a name="deploy-a-docker-formatted-container"></a>Déployer un conteneur mis en forme Docker

Vous déployez mis en forme Docker conteneurs via Marathon en utilisant un fichier JSON qui décrit le déploiement initial. L’exemple suivant déployez le conteneur Nginx, port de liaison 80 de l’agent DC/OS vers le port 80 du conteneur. Notez également que la propriété « acceptedResourceRoles » est définie sur « slave_public ». Cette action déploie le conteneur vers un agent dans l’ensemble d’échelle agent au public.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
    "acceptedResourceRoles": [
    "slave_public"
  ],
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Pour déployer un conteneur mis en forme Docker, créer votre propre fichier JSON, ou utilisez l’échantillon fournie au [Service de conteneur Azure démo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Enregistrer dans un emplacement accessible. Ensuite, pour déployer le conteneur, exécutez la commande suivante. Spécifiez le nom du fichier JSON.

```
curl -X POST http://localhost/marathon/v2/apps -d @marathon.json -H "Content-type: application/json"
```

Le résultat sera semblable à ce qui suit :

```json
{"version":"2015-11-20T18:59:00.494Z","deploymentId":"b12f8a73-f56a-4eb1-9375-4ac026d6cdec"}
```

À présent, si vous interrogez Marathon pour les applications, cette nouvelle application s’affichent dans le résultat.

```
curl localhost/marathon/v2/apps
```

## <a name="scale-your-containers"></a>Mettre à l’échelle vos conteneurs

Vous pouvez également utiliser l’API Marathon pour évoluer ou mettre à l’échelle dans les déploiements d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons mettre à l’échelle cette absence à trois instances d’une application. Pour ce faire, créez un fichier JSON en utilisant le texte JSON et stocker dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante à l’échelle de l’application.

>[AZURE.NOTE] L’URI sera http://localhost/marathon/v2/apps/, puis l’ID de l’application à l’échelle. Si vous utilisez l’exemple Nginx qui est fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.

```json
curl http://localhost/marathon/v2/apps/nginx -H "Content-type: application/json" -X PUT -d @scale.json
```

Enfin, la requête le point de terminaison Marathon pour les applications. Vous verrez qu’il existe ait désormais trois des conteneurs Nginx.

```
curl localhost/marathon/v2/apps
```

## <a name="use-powershell-for-this-exercise-marathon-rest-api-interaction-with-powershell"></a>Utiliser PowerShell pour cet exercice : interaction API REST Marathon avec PowerShell

Vous pouvez effectuer ces mêmes actions à l’aide des commandes PowerShell sur un système de Windows.

Pour recueillir des informations sur le cluster DC/système d’exploitation, tels que les noms des agents et l’état de l’agent, exécutez la commande suivante.

```powershell
Invoke-WebRequest -Uri http://localhost/mesos/master/slaves
```

Vous déployez mis en forme Docker conteneurs via Marathon en utilisant un fichier JSON qui décrit le déploiement initial. L’exemple suivant déployez le conteneur Nginx, port de liaison 80 de l’agent DC/OS vers le port 80 du conteneur.

```json
{
  "id": "nginx",
  "cpus": 0.1,
  "mem": 16.0,
  "instances": 1,
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "nginx",
      "network": "BRIDGE",
      "portMappings": [
        { "containerPort": 80, "hostPort": 80, "servicePort": 9000, "protocol": "tcp" }
      ]
    }
  }
}
```

Créer votre propre fichier JSON, ou utilisez l’échantillon fournie au [Service de conteneur Azure démo](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/marathon/marathon.json). Enregistrer dans un emplacement accessible. Ensuite, pour déployer le conteneur, exécutez la commande suivante. Spécifiez le nom du fichier JSON.

```powershell
Invoke-WebRequest -Method Post -Uri http://localhost/marathon/v2/apps -ContentType application/json -InFile 'c:\marathon.json'
```

Vous pouvez également utiliser l’API Marathon pour évoluer ou mettre à l’échelle dans les déploiements d’application. Dans l’exemple précédent, vous avez déployé une instance d’une application. Nous allons mettre à l’échelle cette absence à trois instances d’une application. Pour ce faire, créez un fichier JSON en utilisant le texte JSON et stocker dans un emplacement accessible.

```json
{ "instances": 3 }
```

Exécutez la commande suivante à l’échelle de l’application.

> [AZURE.NOTE] L’URI sera http://localhost/marathon/v2/apps/, puis l’ID de l’application à l’échelle. Si vous utilisez l’exemple Nginx fourni ici, l’URI serait http://localhost/marathon/v2/apps/nginx.

```powershell
Invoke-WebRequest -Method Put -Uri http://localhost/marathon/v2/apps/nginx -ContentType application/json -InFile 'c:\scale.json'
```

## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus sur les points de terminaison HTTP Mesos]( http://mesos.apache.org/documentation/latest/endpoints/).
- [En savoir plus sur l’API REST Marathon]( https://mesosphere.github.io/marathon/docs/rest-api.html).
