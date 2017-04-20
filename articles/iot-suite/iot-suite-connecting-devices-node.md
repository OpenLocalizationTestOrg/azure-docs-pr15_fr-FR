<properties
   pageTitle="Connecter un périphérique utilisant Node.js | Microsoft Azure"
   description="Décrit comment connecter un appareil à la Suite de IoT Azure préconfiguré à distance solution d’analyse à l’aide d’une application écrite en Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Connectez votre appareil à la solution préconfigurée contrôle à distance (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Créer et exécuter la solution node.js

1. Pour dupliquer le référentiel GitHub *Microsoft Azure IoT SDK* et installez le *périphérique Microsoft Azure IoT SDK pour Node.js* dans votre environnement de bureau, suivez la [préparer votre environnement de développement] [ lnk-github-prepare] instructions.

2. À partir de votre copie locale de la [azure-iot-SDK] [ lnk-github-repo] référentiel, fichiers à partir du dossier nœud/appareil/exemples copie les deux suivants dans un dossier sur votre appareil :

  - packages.JSON
  - remote_monitoring.js

3. Ouvrez le fichier remote_monitoring.js et recherchez la variable suivante :

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Remplacez **[chaîne de connexion d’un appareil de concentrateur IoT]** par la chaîne de connexion appareil. Vous pouvez rechercher les valeurs pour votre plateforme IoT hostname, l’id de l’appareil et clé de périphérique dans le tableau de bord solution contrôle à distance. Une chaîne de connexion appareil a le format suivant :

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Si votre nom d’hôte IoT concentrateur est **contoso** et l’id de votre appareil est **mydevice**, votre chaîne de connexion se présente comme :

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Enregistrez le fichier. Exécutez les commandes suivantes à l’invite de commandes dans le dossier qui contient ces fichiers pour installer les packages nécessaires, puis exécutez l’exemple d’application :

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md