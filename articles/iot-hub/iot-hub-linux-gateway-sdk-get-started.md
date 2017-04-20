<properties
    pageTitle="Prise en main du Kit de développement IoT concentrateur passerelle | Microsoft Azure"
    description="Cette procédure pas à pas Azure IoT passerelle SDK utilise Linux pour illustrer les concepts clés que vous devez comprendre lorsque vous utilisez le Kit de développement Azure IoT passerelle."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta---get-started-using-linux"></a>Azure IoT passerelle Kit de développement logiciel (version bêta) - commencer à utiliser Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-selector](../../includes/iot-hub-gateway-sdk-getstarted-selector.md)]

## <a name="how-to-build-the-sample"></a>Découvrez comment créer l’exemple

Avant de commencer, vous devez [configurer votre environnement de développement] [ lnk-setupdevbox] pour travailler avec le Kit de développement sous Linux.

1. Ouvrez un shell.
2. Accédez au dossier racine dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.
3. Exécuter le script **tools/build.sh** . Ce script utilise l’utilitaire **cmake** pour créer un dossier nommé **créer** dans le dossier racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel et générer un makefile. Ensuite, le script génère la solution et exécute les tests.

> [AZURE.NOTE]  Chaque fois que vous exécutez le script **build.sh** , il supprime et recrée ensuite le dossier **créer** dans le dossier racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.

## <a name="how-to-run-the-sample"></a>L’exécution de l’échantillon

1. Le script **build.sh** génère le résultat dans le dossier **créer** dans votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel. Cela inclut les deux modules utilisés dans cet exemple.

    Le script de compilation place **liblogger_hl.so** dans la **/modules/journal de build/** dossier et **libhello_world_hl.so** dans la **build/modules/Bonjour_monde/** dossier. Utilisez ces chemins d’accès pour la valeur de **chemin d’accès du module** comme indiqué dans le fichier de paramètres JSON ci-dessous.

2. Le fichier **hello_world_lin.json** dans le dossier **exemples/Bonjour_monde/src** est un exemple de fichier de paramètres JSON pour Linux que vous pouvez utiliser pour exécuter l’exemple. Les paramètres de JSON exemple ci-dessous suppose que vous exécuterez l’échantillon depuis la racine de votre copie locale du **Kit de développement logiciel iot azure passerelle** référentiel.

3. Pour le module **logger_hl** , dans la section **arguments** , définissez la valeur de **nom de fichier** pour le nom et le chemin d’accès du fichier contenant les données du journal.

    Il s’agit d’un exemple d’un fichier de paramètres JSON pour Linux écrit dans **log.txt** dans le dossier où vous exécutez l’échantillon.

    ```
    {
      "modules" :
      [ 
        {
          "module name" : "logger_hl",
          "module path" : "./build/modules/logger/liblogger_hl.so",
          "args" : 
          {
            "filename":"./log.txt"
          }
        },
        {
          "module name" : "hello_world",
          "module path" : "./build/modules/hello_world/libhello_world_hl.so",
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

3. Dans votre shell, accédez au dossier **Kit de développement logiciel iot azure passerelle** .
4. Exécutez la commande suivante :
  
  ```
  ./build/samples/hello_world/hello_world_sample ./samples/hello_world/src/hello_world_lin.json
  ``` 

[AZURE.INCLUDE [iot-hub-gateway-sdk-getstarted-code](../../includes/iot-hub-gateway-sdk-getstarted-code.md)]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
