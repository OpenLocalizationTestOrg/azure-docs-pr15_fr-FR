<properties
    pageTitle="Créer un concentrateur IoT Azure infrastructure du langage commun en utilisant | Microsoft Azure"
    description="Suivez cet article pour créer un concentrateur IoT à l’aide de l’Interface de ligne de commande Azure."
    services="iot-hub"
    documentationCenter=".net"
    authors="BeatriceOltean"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/21/2016"
     ms.author="boltean"/>

# <a name="create-an-iot-hub-using-azure-cli"></a>Créer un concentrateur IoT à l’aide d’infrastructure du langage commun Azure

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introduction

Vous pouvez utiliser Azure de ligne de commande Interface pour créer et gérer des hubs Azure IoT par programme. Cet article vous explique comment utiliser l’infrastructure du langage commun Azure pour créer un concentrateur IoT.

Pour effectuer ce didacticiel, vous devez les éléments suivants :

- Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez créer un [compte gratuit] [ lnk-free-trial] en quelques minutes.
- [Azure infrastructure du langage commun 0.10.4] [ lnk-CLI-install] ou version ultérieure. Si vous avez déjà Azure infrastructure du langage commun, vous pouvez valider la version actuelle à l’invite de commandes avec la commande suivante :
```
    azure --version
```

> [AZURE.NOTE] Azure comporte deux modèles de déploiement différents pour la création et utilisation des ressources : [Gestionnaire de ressources Azure et classique](../resource-manager-deployment-model.md). L’infrastructure du langage commun Azure doit être en mode Azure le Gestionnaire de ressources :
```
    azure config mode arm
```

## <a name="set-your-azure-account-and-subscription"></a>Configurer votre compte Azure et abonnement 

1. Lors de la connexion d’invite de commandes en tapant la commande suivante
```
    azure login
```
Utiliser le navigateur web suggéré et le code s’authentifier.

2. Si vous avez plusieurs abonnements Azure, connexion à Azure accorde l’accès à tous les abonnements Azure associés à vos informations d’identification. Vous pouvez afficher les abonnements Azure, ainsi que celui qui est la valeur par défaut, à l’aide de la commande
```
    azure account list 
```

Pour définir le contexte d’abonnement dans lesquelles vous souhaitez exécuter le reste de l’utilisation de commandes

```
    azure account set <subscription name>
```

3. Si vous ne disposez pas d’un groupe de ressources que vous pouvez créer une nommée **exampleResourceGroup** 
```
    azure group create -n exampleResourceGroup -l westus
```

> [AZURE.TIP] L’article [utiliser l’infrastructure du langage commun Azure pour gérer les ressources et groupes ressources Azure] [ lnk-CLI-arm] donne des informations supplémentaires sur l’utilisation d’Azure infrastructure du langage commun pour gérer les ressources Azure. 


## <a name="create-an-iot-hub"></a>Créer un concentrateur IoT

Paramètres requis :

```
 azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>  
    - <resourceGroup> The resource group name (case insensitive alphanumeric, underscore and hyphen, 1-64 length)
    - <name> (The name of the IoT hub to be created. The format is case insensitive alphanumeric, underscore and hyphen, 3-50 length )
    - <location> (The location (azure region/datacenter) where the IoT hub will be provisioned.
    - <sku-name> (The name of the sku, one of: [F1, S1, S2, S3] etc. For the latest full list refer to the pricing page for IoT Hub.
    - <units> (The number of provisioned units. Range : F1 [1-1] : S1, S2 [1-200] : S3 [1-10]. IoT Hub units are based on your total message count and the number of devices you want to connect.)
```
Pour afficher tous les paramètres disponibles pour la création de vous pouvez utiliser la commande aide dans l’invite de commandes
```
    azure iothub create -h 
```
Exemple rapide :

 Pour créer un concentrateur IoT appelé **exampleIoTHubName** dans de groupe de ressources **exampleResourceGroup** simplement exécuter la commande suivante
```
    azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [AZURE.NOTE] Cette commande Azure infrastructure du langage commun crée un concentrateur IoT Standard S1 que vous avez facturée. Vous pouvez supprimer de concentrateur IoT **exampleIoTHubName** à l’aide de commande suivante 
```
    azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
```


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le développement pour IoT concentrateur, voir les rubriques suivantes :
- [Concentrateur IoT SDK][lnk-sdks]

Pour en savoir davantage les fonctionnalités du concentrateur IoT, voir :

- [Utiliser le portail Azure pour gérer IoT concentrateur][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]: ../xplat-cli-install.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-CLI-arm]: ../xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
