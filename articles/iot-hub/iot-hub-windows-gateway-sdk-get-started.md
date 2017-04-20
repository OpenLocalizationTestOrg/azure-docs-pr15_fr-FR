<properties
    pageTitle="Prise en main du Kit de développement IoT concentrateur passerelle | Microsoft Azure"
    description="Azure IoT passerelle SDK procédure pas à pas pour illustrer les concepts clés que vous devez comprendre lorsque vous utilisez le Kit de développement Azure IoT passerelle à l’aide de Windows."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-windows"></a>Azure IoT passerelle Kit de développement logiciel (version bêta) - prise en main de Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Découvrez comment créer l’exemple

Avant de commencer, vous devez [configurer votre environnement de développement] [ lnk-setupdevbox] pour travailler avec le Kit de développement de Windows.

1. Ouvrez une invite de commande **d’invite de commandes pour les développeurs pour VS2015** .
2. Accédez au dossier racine dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
3. Exécuter le **outils\\build.cmd** script. Ce script crée un fichier de solution Visual Studio, génère la solution et exécute les tests. Vous trouverez la solution Visual Studio dans le dossier **créer** dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.

## <a name="how-to-run-the-sample"></a>L’exécution de l’échantillon

1. Le script **build.cmd** crée un dossier nommé **créer** dans votre copie locale du référentiel. Ce dossier contient les deux modules utilisés dans cet exemple.

    Le script de compilation place **logger_hl.dll** dans la **créer\\modules\\journal\\déboguer** dossier et **hello_world_hl.dll** dans la **créer\\modules\\Bonjour_monde\\déboguer** dossier. Utilisez ces chemins d’accès pour la valeur de **chemin d’accès du module** comme indiqué dans le fichier de paramètres JSON ci-dessous.

2. Le fichier **hello_world_win.json** dans les **exemples\\Bonjour_monde\\src** dossier est un exemple de fichier de paramètres JSON pour Windows que vous pouvez utiliser pour exécuter l’exemple. Les paramètres de JSON exemple ci-dessous suppose que vous cloner le référentiel IoT passerelle SDK à la racine de votre lecteur **C:** . Si vous avez téléchargé vers un autre emplacement, vous devez régler les valeurs de **chemin d’accès du module** dans le **exemples\\Bonjour_monde\\src\\hello_world_win.json** de fichiers en conséquence.

3. Pour le module **logger_hl** , dans la section **arguments** , définissez la valeur de **nom de fichier** pour le nom et le chemin d’accès du fichier contenant les données du journal.

    Il s’agit d’un exemple d’un fichier de paramètres JSON pour Windows qui écrit au fichier **log** dans la racine de votre lecteur **C:** .

    ```
    {
      "modules" :
      [
        {
          "module name" : "logger_hl",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
          "args" : 
          {
            "filename":"C:\\log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "C:\\azure-iot-gateway-sdk\\build\\\\modules\\hello_world\\Debug\\hello_world_hl.dll",
          "args" : null
        }
      ],
      "links" :
      [
        {
          "source": "hello_world",
          "sink": "logger_hl"
        }
      ]
    }
    ```

3. À l’invite de commandes, accédez au dossier racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
4. Exécutez la commande suivante :
  
  ```
  build\samples\hello_world\Debug\hello_world_sample.exe samples\hello_world\src\hello_world_win.json
  ```

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md