<properties
   pageTitle="WebApplicationVM publier | Microsoft Azure"
   description="Découvrez comment déployer une application web sur un ordinateur virtuel. Ce script crée les ressources nécessaires dans votre abonnement Azure si elles n’existent pas."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publier-WebApplicationVM (script Windows PowerShell)

Déployer une application web pour une machine virtuelle. Le script crée les ressources nécessaires dans votre abonnement Azure si elles n’existent pas.

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>Configuration

Le chemin d’accès au fichier de configuration JSON qui décrit les détails du déploiement.

|Alias|Aucun|
|---|---|
|Obligatoire ?|Vrai|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="subscriptionname"></a>SubscriptionName

Le nom de l’abonnement Azure dans lequel vous souhaitez créer la machine virtuelle.

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Utilise le premier abonnement dans le fichier d’abonnement|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="webdeploypackage"></a>WebDeployPackage

Le chemin d’accès au package de déploiement web pour publier sur l’ordinateur virtuel. Vous pouvez créer ce package à l’aide de l’Assistant Publier le site Web dans Visual Studio. Voir [Comment : créer un Package de déploiement Web dans Visual Studio](https://msdn.microsoft.com/library/dd465323.aspx).

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="allowuntrusted"></a>AllowUntrusted

Si vrai, autoriser l’utilisation de certificats qui ne sont pas signés par une autorité de certification racine de confiance.

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|faux|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="vmpassword"></a>VMPassword

Les informations d’identification du compte machine virtuelle. Exemple : - VMPassword @{Name = « admin » ; Mot de passe = « mot de passe »}

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="databaseserverpassword"></a>DatabaseServerPassword

Les informations d’identification pour la base de données SQL Azure. Exemple : - DatabaseServerPassword @{Name = « admin » ; Mot de passe = « mot de passe »}

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si vrai, imprime des messages à partir du script dans le flux de sortie.

|Alias|Aucun|
|---|---|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|faux|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="remarks"></a>Remarques

Pour obtenir une explication complète de la façon d’utiliser le script pour créer environnements de développement et de Test, voir [Utilisation de Scripts Windows PowerShell à publier sur le développement et les environnements de Test](vs-azure-tools-publishing-using-powershell-scripts.md).

Le fichier de configuration JSON spécifie les détails des fonctionnalités à déployer. Il inclut les informations que vous avez spécifié lors de la création du projet, tels que le nom de groupe affinité, image de disque dur virtuel et taille de la machine virtuelle. Il inclut les extrémités de la machine virtuelle, les bases de données de mise en service, le cas échéant et vous web des paramètres de déploiement. Le code suivant montre un exemple de fichier de configuration JSON :

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

Vous pouvez modifier le fichier de configuration JSON pour modifier ce qui est mis en service. Une machine virtuelle et un service cloud sont obligatoires, mais la section de base de données est facultative.
