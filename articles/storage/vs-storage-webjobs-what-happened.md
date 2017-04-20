<properties
    pageTitle="Qu’est-il advenu de mon projet WebJob (Visual Studio le stockage Azure connecté service) ? | Microsoft Azure"
    description="Décrit où est passée dans un projet Azure WebJob une fois la connexion à un compte de stockage à l’aide de Visual Studio services connectés"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-what-happened"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="tarcher"/>

# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il advenu de mon projet WebJob (Visual Studio le stockage Azure connecté service) ?

## <a name="references-added"></a>Références ajoutées

Le package NuGet de stockage Azure a été ajouté ou mis à jour dans votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

- **Microsoft.Data.Edm**
- **Microsoft.Data.OData**
- **Microsoft.Data.Services.Client**
- **Microsoft.WindowsAzure.ConfigurationManager**
- **Microsoft.WindowsAzure.Storage**
- **Newtonsoft.Json**
- **System.Data**
- **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour le stockage Azure ajouté
Dans le fichier App.config de votre projet, les entrées **AzureWebJobsStorage** et **AzureWebJobsDashboard** ont été mis à jour avec la chaîne de connexion de compte de stockage sélectionné et la clé.

Pour plus d’informations, consultez [ressources de documentation WebJobs Azure](http://go.microsoft.com/fwlink/?linkid=390226).
