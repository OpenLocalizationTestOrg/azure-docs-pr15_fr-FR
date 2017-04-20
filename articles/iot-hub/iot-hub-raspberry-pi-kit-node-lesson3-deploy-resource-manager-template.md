<properties
 pageTitle="Créer une application de fonction Azure et un compte de stockage Azure | Microsoft Azure"
 description="L’application de la fonction Azure écoute des événements de concentrateur Azure IoT traite les messages entrants et les écrit à Azure table storage."
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

# <a name="31-create-an-azure-function-app-and-azure-storage-account"></a>3.1 créer une application de fonction Azure et un compte de stockage Azure

[Fonctions Azure](../../articles/azure-functions/functions-overview.md) est une solution pour l’exécution facilement des petits fragments de code, appelées « fonctions », dans le cloud. Une application de la fonction Azure héberge l’exécution de vos fonctions dans Azure.

## <a name="311-what-will-you-do"></a>3.1.1 que souhaitez-vous faire

Utiliser un modèle Azure le Gestionnaire de ressources pour créer une application de la fonction Azure et un compte de stockage Azure. L’application de la fonction Azure écoute des événements de concentrateur Azure IoT traite les messages entrants et les écrit à Azure table storage. Si vous répondez à des problèmes, recherchent des solutions dans la [page de résolution des problèmes](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="312-what-will-you-learn"></a>3.1.2 vous apprendrez

- Comment utiliser [Le Gestionnaire de ressources Azure](../../articles/azure-resource-manager/resource-group-overview.md) pour déployer des ressources Azure.
- Comment utiliser une application de la fonction Azure pour traiter les messages de concentrateur IoT et les enregistrer dans un tableau dans le stockage de table Azure.

## <a name="313-what-do-you-need"></a>3.1.3 qu’avez-vous besoin

- Vous devez avoir terminé correctement les leçons précédentes : [prise en main votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-get-started.md) et [créer votre concentrateur IoT Azure](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="314-open-the-sample-app"></a>3.1.4 ouvrir l’application d’exemple

Ouvrez le projet dans le Code de Visual Studio en exécutant les commandes suivantes :

```bash
cd Lesson3
code .
```

![Structure mis en pension](media/iot-hub-raspberry-pi-lessons/lesson3/repo_structure.png)

- La `app.js` de fichier dans le `app` sous-dossier est le fichier source clés. Ce fichier source contient le code pour envoyer un message 20 fois à votre concentrateur IoT et clignoter le LED pour chaque message qu'il envoie.
- La `arm-template.json` fichier est le modèle de gestionnaire de ressources Azure qui contient une application de la fonction Azure et d’un compte de stockage Azure.
- La `arm-template-param.json` fichier se trouve le fichier de configuration utilisé par le modèle Azure le Gestionnaire de ressources.
- La `ReceiveDeviceMessages` sous-dossier contient le code Node.js pour la fonction Azure.

## <a name="315-configure-azure-resource-manager-templates-and-create-resources-in-azure"></a>3.1.5 configurer les modèles Azure Gestionnaire de ressources et créer des ressources dans Azure

Mise à jour la `arm-template-param.json` fichier dans le Code de Visual Studio.

![Paramètres de modèle Azure Gestionnaire de ressources](media/iot-hub-raspberry-pi-lessons/lesson3/arm_para.png)

- Remplacez **[nom de votre concentrateur IoT]** **{Mon nom concentrateur}** que vous avez spécifié dans la [leçon 2](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md).
- Remplacez **[chaîne préfixe des nouvelles ressources]** avec n’importe quel préfixe souhaité. Le préfixe garantit que le nom de la ressource est globalement unique pour éviter les conflits. N’utilisez pas tiret ou le numéro initial dans le préfixe.

> [AZURE.NOTE] Vous n’avez pas besoin `azure_storage_connection_string` dans cette section. Conserver tel quel.

Une fois que vous mettez à jour la `arm-template-param.json` de fichiers, déployer les ressources sur Azure en exécutant la commande suivante :

```bash
az resource group deployment create --template-file-path arm-template.json --parameters-file-path arm-template-param.json -g iot-sample -n mydeployment
```

Prend environ 5 minutes pour créer ces ressources. Alors que la création de la ressource est en cours, vous pouvez déplacer sur à la section suivante.

## <a name="316-summary"></a>3.1.6 résumé

Vous avez créé votre application de la fonction Azure pour traiter les messages de concentrateur IoT et un compte de stockage Azure pour stocker ces messages. Vous pouvez déplacer à la section suivante pour le déploiement et l’exécution de l’échantillon pour envoyer des messages appareil-nuage sur votre Pi.

## <a name="next-steps"></a>Étapes suivantes

[3.2 exécuter exemple d’application pour envoyer des messages appareil-nuage sur votre framboises Pi 3](iot-hub-raspberry-pi-kit-node-lesson3-run-azure-blink.md)

