<properties
 pageTitle="Créer votre concentrateur IoT et inscrire votre framboises Pi 3 | Microsoft Azure"
 description="Créer un groupe de ressources, créer un concentrateur IoT Azure et inscrire votre Pi dans le hub IoT Azure à l’aide de l’infrastructure du langage commun Azure."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="22-create-your-iot-hub-and-register-your-raspberry-pi-3"></a>2.2 créer votre concentrateur IoT et inscrire votre framboises Pi 3

## <a name="221-what-you-will-do"></a>2.2.1 ce que vous ferez

- Créer un groupe de ressources.
- Créer votre plateforme d’Azure IoT dans le groupe de ressources.
- Ajouter votre framboises Pi 3 au concentrateur IoT Azure à l’aide de l’infrastructure du langage commun Azure.

Lorsque vous utilisez l’infrastructure du langage commun Azure pour ajouter votre Pi à votre concentrateur IoT, le service génère une clé pour votre Pi pour vous authentifier avec le service. Si vous répondez à des problèmes, recherchent des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="222-what-you-will-learn"></a>2.2.2 enseignements de cet article

- Découvrez comment utiliser l’infrastructure du langage commun Azure pour créer un concentrateur IoT Azure.
- Découvrez comment créer une identité appareil pour votre Pi dans votre IoT Hub Azure.

## <a name="223-what-you-need"></a>2.2.3 ce dont vous avez besoin

- Un compte Azure
- Un Mac ou un ordinateur Windows avec l’infrastructure du langage commun Azure installé

## <a name="224-create-your-azure-iot-hub"></a>2.2.4 créer votre concentrateur IoT Azure

Concentrateur IoT Azure vous permet de vous connecter, surveiller et gérer des millions de biens IoT. Pour créer votre plateforme d’Azure IoT, procédez comme suit :

1. Connectez-vous à votre compte Azure en exécutant la commande suivante :

    ```bash
    az login
    ```

    Tous vos abonnements Azure disponibles sont répertoriées à la connexion.

2. Définir la valeur par défaut Azure abonnement que vous souhaitez utiliser en exécutant la commande suivante :

    ```bash
    az account set -n {subscription id or name}
    ```

    Le nom ou l’ID de l’abonnement sont accessibles dans le résultat de `az login`.

3. Enregistrer le fournisseur en exécutant la commande suivante :

    ```bash
    az resource provider register -n "Microsoft.Devices"
    ```

    Vous devez enregistrer le fournisseur avant de déployer la ressource Azure fournissant le fournisseur.

    > [AZURE.NOTE] La plupart des fournisseurs sont enregistrées automatiquement par le portail Azure ou l’infrastructure du langage commun Azure que vous utilisez, mais pas toutes. Pour plus d’informations sur le fournisseur, consultez [résoudre les problèmes courants déploiement d’Azure erreurs avec le Gestionnaire de ressources Azure](../resource-manager-common-deployment-errors.md)

4. Créer un groupe de ressources nommé iot échantillons dans la région Ouest US en exécutant la commande suivante :

    ```bash
    az resource group create --name iot-sample --location westus
    ```

5. Créer un concentrateur IoT dans le groupe de ressources iot échantillons en exécutant la commande suivante :

    ```bash
    az iot hub create --name {my hub name} --resource-group iot-sample
    ```

    L’édition par défaut du concentrateur IoT que vous créez **F1**, est **gratuit**. Pour plus d’informations, voir [tarifs Azure IoT concentrateur](https://azure.microsoft.com/pricing/details/iot-hub/).

> [AZURE.NOTE] Le nom de votre plateforme IoT doit être globalement unique.
>
> Vous pouvez créer qu’une seule édition **F1** d’Azure IoT concentrateur sous votre abonnement Azure.

## <a name="225-register-your-pi-in-your-iot-hub"></a>2.2.5 inscrire votre Pi dans votre concentrateur IoT

Chaque appareil qui envoie/reçoit les messages vers ou à partir de votre plateforme d’Azure IoT doit être enregistré avec un identificateur unique.

Enregistrez votre Pi dans votre plateforme d’Azure IoT en cours d’exécution de commande suivante :

```bash
az iot device create --device-id myraspberrypi --hub {my hub name} --resource-group iot-sample
```

## <a name="226-summary"></a>2.2.6 résumé

Vous avez créé un concentrateur IoT Azure et enregistré votre Pi avec une identité de périphérique dans votre hub IoT Azure. Vous êtes prêt à passer à la leçon suivante pour apprendre à envoyer des messages à partir de votre Pi à votre concentrateur IoT.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes maintenant prêt à commencer leçon 3 qui commence par [créer une application de la fonction Azure et d’un compte de stockage Azure pour traiter et stocker les messages de concentrateur IoT](iot-hub-raspberry-pi-kit-node-lesson3-deploy-resource-manager-template.md).