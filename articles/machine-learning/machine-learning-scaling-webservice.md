<properties
   pageTitle="Mise à l’échelle de service web | Microsoft Azure"
   description="Découvrez comment mettre à l’échelle d’un service web en augmentant concurrence et ajout de nouveaux points de terminaison."
   services="machine-learning"
   documentationCenter=""
   authors="neerajkh"
   manager="srikants"
   editor="cgronlun"
   keywords="Azure apprentissage de l’ordinateur, les services web, operationalization, mise à l’échelle, point de terminaison, concurrence"
   />
<tags
   ms.service="machine-learning"
   ms.devlang="NA"
   ms.workload="data-services"
   ms.tgt_pltfrm="na"
   ms.topic="article"
   ms.date="10/05/2016"
   ms.author="neerajkh"/>

# <a name="scaling-a-web-service"></a>Mise à l’échelle d’un service Web

>[AZURE.NOTE] Cette rubrique décrit les techniques applicables à un service Web de formation Machine classique. 

Par défaut, chaque service Web publié est configuré pour prendre en charge 20 demandes simultanées et peut être aussi élevée que 200 demandes simultanées. Pendant que le portail classique Azure fournit une façon de définir cette valeur, apprentissage automatique Azure optimise automatiquement le paramètre pour améliorer les performances de votre service web et la valeur du portail est ignorée. 

Si vous envisagez d’appeler l’API avec une charge plus élevée à une valeur Max simultanées appels de 200 prend en charge, vous devez créer plusieurs points de terminaison sur le même service Web. Vous pouvez répartir puis aléatoirement votre charge sur tous les.

## <a name="add-new-endpoints-for-same-web-service"></a>Ajouter de nouveaux points de terminaison de service web même

La mise à l’échelle d’un service Web est une tâche courante. Certaines raisons à l’échelle sont à prendre en charge plus de 200 demandes simultanées, renforcez la disponibilité avec plusieurs points de terminaison ou fournir des points de terminaison séparés pour le service web. Vous pouvez augmenter l’échelle en ajoutant des points de terminaison du service Web même [Azure portail classique](https://manage.windowsazure.com/) ou le portail [Azure Machine apprentissage serviceWeb](https://services.azureml.net/) supplémentaires.

Pour plus d’informations sur l’ajout de nouveaux points de terminaison, voir [Création d’une extrémité](machine-learning-create-endpoint.md).

N’oubliez pas qu’à l’aide d’un nombre élevé d’opérations simultanées peut être négatif si vous appelez pas l’API avec un taux élevé en conséquence. Vous pouvez voir délais interruptions et/ou pointes dans la latence si vous placez une charge relativement faible sur une API configurée pour une charge élevée.

Les API synchrones sont généralement utilisés dans les cas où un faible latence est souhaitée. Latence ici implique le temps nécessaire pour l’API effectuer une demande et ne tenant pas compte les retards réseau. Supposons que vous avez une API avec une latence de 50 ms. Entièrement consommer la capacité disponible avec le niveau de limitation haute et les appels concurrents Max = 20, vous devez appeler cette API 20 * 1 000 / 50 = 400 fois par seconde. Étendre davantage, un appels simultanées Max de 200 vous permet d’appelez les heures et 4 000 API par seconde, en supposant qu’une latence 50 ms.

<!--Image references-->
[1]: ./media/machine-learning-scaling-webservice/machlearn-1.png
[2]: ./media/machine-learning-scaling-webservice/machlearn-2.png
