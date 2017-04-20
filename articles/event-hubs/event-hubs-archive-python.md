<properties
    pageTitle="Procédure pas à pas événement Hubs Archive Azure | Microsoft Azure"
    description="Exemple qui utilise le Kit de développement de Python Azure pour illustrer l’utilisation de la fonctionnalité d’événement Hubs Archive."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Procédure pas à pas Hubs Archive événement : Python

Événement Hubs Archive est une nouvelle fonctionnalité de Hubs événement qui vous permet d’effectuer automatiquement les données de flux dans votre plateforme d’événement à un compte de stockage d’objets Blob Azure de votre choix. Cela facilite effectuer le traitement par lots sous flux des données en temps réel. Cet article décrit comment utiliser des événements Hubs Archive avec Python. Pour plus d’informations sur l’événement Hubs Archive, voir l' [article de présentation](event-hubs-archive-overview.md).

Cet exemple utilise le Kit de développement de Python Azure pour illustrer l’utilisation de la fonctionnalité d’archivage. La sender.py envoie télémétrie l’environnement simulé aux événements Hubs au format JSON. Le Hub de l’événement est configuré pour utiliser la fonctionnalité d’Archive pour écrire ces données pour le stockage par lots blob. La archivereader.py puis lit ces objets BLOB et crée un fichier ajout par unité et écrit les données dans des fichiers .csv.

Quelle sera effectué

1.  Créer un compte de stockage d’objets Blob Azure et un conteneur blob qu’il contient, à l’aide du portail Azure

2.  Créer un espace de noms Hub de l’événement, à l’aide du portail Azure

3.  Créer un concentrateur événement avec la fonctionnalité d’archivage activée, à l’aide du portail Azure

4.  Envoyer des données vers le Hub de l’événement avec un script Python

5.  Lire les fichiers à partir de l’archivage et les traiter avec un autre script Python

Conditions préalables

1.  Python 2.7.x

2.  Un abonnement Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Créer un compte de stockage Azure

1.  Connectez-vous au [portail Azure][].

2.  Dans le volet de navigation de gauche du portail, cliquez sur Nouveau, puis cliquez sur données + stockage, puis cliquez sur compte de stockage.

3.  Renseignez les champs de la carte de compte de stockage et cliquez sur **créer**.

    ![][1]

4.  Une fois que vous voyez le message **Déploiements a réussi** , cliquez sur le nouveau compte de stockage et dans la carte **Essentials** cliquez sur **des objets BLOB**. Lorsque la carte de **service d’objets Blob** s’ouvre, cliquez sur **+ conteneur** dans la partie supérieure. Nommez conteneur **archive**, puis sur Fermer la carte **Blob service** .

5.  Cliquez sur **les touches d’accès** dans la carte gauche et copiez le nom du compte de stockage et la valeur de **touche1**. Enregistrer les valeurs suivantes dans le bloc-notes ou un autre emplacement temporaire.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Créer un script Python pour envoyer des événements à votre plateforme d’événement

1.  Ouvrez votre éditeur Python préféré, tel que [Code Visual Studio][].

2.  Créer un script appelé **sender.py**. Ce script envoie 200 événements à votre plateforme d’événement. Ils sont des valeurs de l’environnement simples envoyés dans JSON.

3.  Collez le code suivant dans sender.py :

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Mettre à jour le code précédent pour utiliser votre nom d’espace de noms et les valeurs de clé que vous avez obtenu lorsque vous avez créé l’espace de noms Hubs événement.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Créer un script Python pour lire vos fichiers d’archive

1.  Remplissez la carte et cliquez sur **créer**.

2.  Créer un script appelé **archivereader.py**. Ce script lire les fichiers d’archive et créer un fichier par unité pour écrire les données uniquement pour cet appareil.

3.  Collez le code suivant dans archivereader.py :

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  N’oubliez pas de coller les valeurs appropriées pour votre nom de compte de stockage et la clé dans l’appel à `startProcessing`.

## <a name="run-the-scripts"></a>Exécuter les scripts

1.  Ouvrez une invite de commande avec Python dans son chemin d’accès, puis exécutez ces commandes pour installer les packages prérequises Python :

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Si vous avez une version antérieure de soit stockage azure ou azure vous devrez peut-être utiliser la **--mettre à niveau** option

    Vous devrez peut-être également exécuter ce qui suit (pas nécessairement sur la plupart des systèmes) :

    ```
    pip install cryptography
    ```

2.  Modifier votre annuaire à l’endroit où vous avez enregistré sender.py et archivereader.py, puis exécutez la commande suivante :

    ```
    start python sender.py
    ```
    
    Cela démarre un nouveau processus Python pour exécuter l’expéditeur.

3. Maintenant Patientez quelques minutes pour l’archive à exécuter. Puis tapez la commande suivante dans la fenêtre de commande d’origine :

    ```
    python archivereader.py
    ```

Ce processeur archive utilise le répertoire local pour télécharger tous les objets BLOB à partir du compte de stockage/conteneur. Il traite ceux qui ne sont pas vides et consigner les résultats en tant que fichiers .csv dans le répertoire local.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez plus d’informations sur événement Hubs en consultant les liens suivants :

- [Vue d’ensemble des événements Hubs archiver][]
- Un [exemple d’application qui utilise l’événement Hubs][]complète.
- L’exemple de [mise à l’échelle traitement de l’événement avec Hubs de l’événement][] .
- [Vue d’ensemble des Hubs événement][]
 

[Portail Azure]: https://portal.azure.com/
[Vue d’ensemble des événements Hubs archiver]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Code de Visual Studio]: https://code.visualstudio.com/
[Vue d’ensemble des Hubs événement]: event-hubs-overview.md
[exemple d’application qui utilise l’événement Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Évoluer de traitement de l’événement avec Hubs d’événement]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
