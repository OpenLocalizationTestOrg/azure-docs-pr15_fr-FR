<properties
    pageTitle="Utiliser télémétrie dynamique | Microsoft Azure"
    description="Suivez ce didacticiel pour apprendre à utiliser télémétrie dynamique avec la solution préconfigurée d’analyse à distance."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Solution préconfigurée de télémétrie dynamique utiliser avec le contrôle à distance

## <a name="introduction"></a>Introduction

Télémétrie dynamique permet de visualiser les télémétrie envoyée à la solution préconfigurée d’analyse à distance. Les appareils simulés déploiement la solution préconfigurés envoient télémétrie température et humidité, qui vous permet d’afficher dans le tableau de bord. Si vous personnalisez les périphériques simulés existants, créez de nouveaux périphériques simulés ou connectez périphériques physiques à la solution préconfigurée que vous pouvez envoyer d’autres valeurs de télémétrie, telles que la température externe, t/mn ou vitesse du vent. Vous pouvez visualiser puis cette télémétrie supplémentaire dans le tableau de bord.

Ce didacticiel utilise un périphérique simulé Node.js simple que vous pouvez facilement modifier pour expérimenter télémétrie dynamique.

Pour effectuer ce didacticiel, vous devez :

- Un abonnement Azure actif. Si vous n’avez pas un compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, voir [Version d’évaluation gratuite Azure][lnk_free_trial].
- [Node.js] [ lnk-node] version 0.12.x ou version ultérieure.

Vous pouvez effectuer ce didacticiel sur les systèmes d’exploitation, tels que Windows ou Linux, où vous pouvez installer Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurer le périphérique simulé Node.js

1. Dans le tableau de bord surveillance à distance, cliquez sur **+ Ajouter un périphérique** , puis ajoutez un périphérique personnalisé. Notez le IoT Hub hostname, l’id de l’appareil et clé de périphérique. Vous avez besoin plus loin dans ce didacticiel lors de la préparation de l’application cliente de périphérique remote_monitoring.js.

2. Assurez-vous que la version Node.js 0.12.x ou version ultérieure est installé sur votre ordinateur de développement. Exécuter `node --version` à une invite de commandes ou dans un shell pour vérifier la version. Pour plus d’informations sur l’utilisation d’un gestionnaire de package pour installer Node.js sur Linux, voir [Installer Node.js via le Gestionnaire de package][node-linux].

3. Lorsque vous avez installé Node.js, cloner la dernière version de la [azure-iot-SDK] [ lnk-github-repo] référentiel à votre ordinateur de développement. Toujours utiliser la branche **maître** pour la version la plus récente des bibliothèques et des exemples.

4. À partir de votre copie locale de la [azure-iot-SDK] [ lnk-github-repo] référentiel, fichiers à partir du dossier nœud/appareil/exemples copie les deux suivants dans un dossier vide sur votre ordinateur de développement :

  - packages.JSON
  - remote_monitoring.js

5. Ouvrez le fichier remote_monitoring.js et recherchez la définition de variable suivante :

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Remplacez **[chaîne de connexion d’un appareil de concentrateur IoT]** par la chaîne de connexion appareil. Utilisez les valeurs pour votre nom d’hôte IoT concentrateur, id de périphérique et clé appareil que vous avez apportées à une note d’à l’étape 1. Une chaîne de connexion appareil a le format suivant :

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si votre nom d’hôte IoT concentrateur est **contoso** et l’id de votre appareil est **mydevice**, votre chaîne de connexion se présente comme suit :

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Enregistrez le fichier. Exécutez les commandes suivantes dans une shell ou une invite de commandes dans le dossier qui contient ces fichiers pour installer les packages nécessaires, puis exécutez l’exemple d’application :

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observez télémétrie dynamique en action

Le tableau de bord affiche la température et humidité télémétrie à partir des périphériques simulés existants :

![Le tableau de bord par défaut][image1]

Si vous sélectionnez le périphérique simulé Node.js que vous avez exécuté le dans la section précédente, vous voyez température et le télémétrie températures externes :

![Ajouter des températures externes au tableau de bord][image2]

La solution d’analyse à distance détecte le type de télémétrie températures externes supplémentaires automatiquement et le place dans le graphique dans le tableau de bord.

## <a name="add-a-telemetry-type"></a>Ajouter un type de télémétrie

L’étape suivante consiste à remplacer la télémétrie généré par le périphérique simulé Node.js avec un nouvel ensemble de valeurs :

1. Arrêter le périphérique simulé Node.js en tapant **Ctrl + C** dans votre invite de commandes ou shell.

2. Dans le fichier remote_monitoring.js, vous pouvez voir les valeurs de données de base pour la température existant et le télémétrie température externe. Ajoutez une valeur de base de données pour **t/min** comme suit :

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. Le périphérique simulé Node.js utilise la fonction **generateRandomIncrement** dans le fichier remote_monitoring.js pour ajouter un incrément aléatoire pour les valeurs de base de données. Aléatoire la valeur **t/min** en ajoutant une ligne de code après les randomizations existantes comme suit :

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Ajouter la nouvelle valeur t/min à la charge utile JSON que le périphérique envoie à IoT concentrateur :

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Exécutez le périphérique simulé Node.js à l’aide de la commande suivante :

    ```
    node remote_monitoring.js
    ```

6. Examinez le nouveau type de télémétrie t/min qui s’affiche sur le graphique dans le tableau de bord :

![Ajouter t/min au tableau de bord][image3]

> [AZURE.NOTE] Vous devrez peut-être désactivez, puis activez le périphérique Node.js dans la page **appareils** dans le tableau de bord pour voir les modifications immédiatement.

## <a name="customize-the-dashboard-display"></a>Personnaliser l’affichage du tableau de bord

Le message **d’Informations du périphérique** peut inclure des métadonnées sur la télémétrie que le périphérique peut envoyer à IoT concentrateur. Ces métadonnées peuvent spécifier les types de télémétrie qu'envoie le périphérique. Modifiez la valeur **deviceMetaData** dans le fichier remote_monitoring.js pour inclure une définition de **télémétrie** après la définition de **commandes** . L’extrait de code suivant illustre la définition de **commandes** (n’oubliez pas d’ajouter un `,` après la définition de **commandes** ) :

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] La solution d’analyse à distance utilise une correspondance sans respecter la casse pour comparer la définition des métadonnées avec des données dans le flux de données de télémétrie.

Ajout d’une définition de **télémétrie** comme le montre l’extrait de code précédent ne change pas le comportement du tableau de bord. Toutefois, les métadonnées peuvent également inclure un attribut **DisplayName** pour personnaliser l’affichage dans le tableau de bord. Mettre à jour la définition des métadonnées **télémétrie** comme le montre l’extrait de code suivante :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La capture d’écran suivante montre comment ce changement modifie la légende de graphique dans le tableau de bord :

![Personnaliser la légende de graphique][image4]

> [AZURE.NOTE] Vous devrez peut-être désactivez, puis activez le périphérique Node.js dans la page **appareils** dans le tableau de bord pour voir les modifications immédiatement.

## <a name="filter-the-telemetry-types"></a>Filtrer les types de télémétrie

Par défaut, le graphique dans le tableau de bord affiche les séries de données dans le flux de données de télémétrie. Vous pouvez utiliser les métadonnées de **l’Appareil-Info** pour supprimer l’affichage des types de télémétrie spécifiques sur le graphique. 

Pour rendre le graphique à afficher uniquement les télémétrie température et humidité, omettre **ExternalTemperature** dans les métadonnées de **télémétrie** **Appareil-Info** comme suit :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

La **Température extérieur** affiche n’est plus sur le graphique :

![Filtrer la télémétrie du tableau de bord][image5]

Cette modification n’affecte que l’affichage du graphique. Les valeurs de données **ExternalTemperature** sont toujours stockés et disponibles pour tout traitement principal.

> [AZURE.NOTE] Vous devrez peut-être désactivez, puis activez le périphérique Node.js dans la page **appareils** dans le tableau de bord pour voir les modifications immédiatement.

## <a name="handle-errors"></a>Gérer les erreurs

Pour un flux de données à afficher sur le graphique, son **Type** dans les métadonnées de **l’Appareil-Info** doit correspondre au type de données des valeurs de télémétrie. Par exemple, si les métadonnées spécifiant que le **Type** de données humidité est de **type int** et un **double** se trouve dans le flux de données de télémétrie puis la télémétrie humidité n’affiche pas sur le graphique. Toutefois, les valeurs **humidité** sont toujours stockés et disponibles pour tout traitement principal.

## <a name="next-steps"></a>Étapes suivantes

À présent que vous avez appris à utiliser télémétrie dynamique, vous pouvez en savoir plus sur l’utilisation des informations sur les périphériques par les solutions préconfigurées : [solution préconfigurée de surveillance appareil informations métadonnées dans la distance][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
