<properties
   pageTitle="Publier-WebApplicationWebSite (script Windows PowerShell) | Microsoft Azure"
   description="Découvrez comment publier un projet web sur un site Web Azure. Ce script crée les ressources nécessaires dans votre abonnement Azure si elles n’existent pas."
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

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publier-WebApplicationWebSite (script Windows PowerShell)

##<a name="syntax"></a>Syntaxe

Publie un projet web vers un site Web Azure. Le script crée les ressources nécessaires dans votre abonnement Azure si elles n’existent pas.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuration

Le chemin d’accès au fichier de configuration JSON qui décrit les détails du déploiement.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|Vrai|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="subscriptionname"></a>SubscriptionName

Le nom de l’abonnement Azure auquel vous voulez créer le site Web dans.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="webdeploypackage"></a>WebDeployPackage

Le chemin d’accès au package de déploiement web à publier sur le site Web. Vous pouvez créer ce package à l’aide de l’Assistant Publier le site Web dans Visual Studio. Pour plus d’informations, voir [prise en main Azure Cloud Services et ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

Le nom d’utilisateur et mot de passe pour la base de données SQL Azure.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|Aucun|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Si vrai, imprime des messages à partir du script dans le flux de sortie.

|Paramètre|Valeur par défaut|
|---|---|
|Alias|Aucun|
|Obligatoire ?|faux|
|Position|nommé|
|Valeur par défaut|faux|
|Accepter les entrées de pipeline ?|faux|
|Accepter les caractères génériques ?|faux|

## <a name="remarks"></a>Remarques

Pour obtenir une explication complète de la façon d’utiliser le script pour créer environnements de développement et de Test, voir [Utilisation de Scripts Windows PowerShell à publier sur le développement et les environnements de Test](vs-azure-tools-publishing-using-powershell-scripts.md).

Le fichier de configuration JSON spécifie les détails des fonctionnalités à déployer. Il inclut les informations que vous avez spécifié lors de la création du projet, tels que le nom et le nom d’utilisateur pour le site Web. Il inclut également la base de données mise en service, le cas échéant. Le code suivant montre un exemple de fichier de configuration JSON :

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Vous pouvez modifier le fichier de configuration JSON pour modifier ce qui est déployé. Une section de site Web est requise, mais la section de base de données est facultative.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, voir [Publier-WebApplicationVM (script Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
