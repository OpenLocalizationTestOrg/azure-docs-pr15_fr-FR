<properties
    pageTitle="Utiliser le stockage Azure dans les applications du Windows Store | Microsoft Azure"
    description="Apprenez à créer une application du Windows Store qui utilise le stockage Blob Azure, file d’attente, un tableau ou fichier."
    services="storage"
    documentationCenter=""
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>
    
# <a name="how-to-use-azure-storage-in-windows-store-apps"></a>Comment utiliser le stockage Azure dans les applications du Windows Store

## <a name="overview"></a>Vue d’ensemble

Ce guide montre comment commencer à utiliser le développement d’une application du Windows Store qui utilise le stockage Azure.

## <a name="download-required-tools"></a>Téléchargez les outils requis

- [Visual Studio](https://www.visualstudio.com/en-us/visual-studio-homepage-vs.aspx) facilite la générer, déboguer, localiser, un package et déployer des applications du Windows Store. Visual Studio 2012 ou version ultérieure est requise.
- La [Bibliothèque de Client de stockage Azure](https://www.nuget.org/packages/WindowsAzure.Storage) fournit une bibliothèque de classes Windows Runtime pour travailler avec le stockage Azure.
- [WCF données Services outils pour Windows Store applications](http://www.microsoft.com/download/details.aspx?id=30714) étend l’expérience d’ajouter une référence de Service avec prise en charge de OData côté client pour les applications du Windows Store dans Visual Studio.

## <a name="develop-apps"></a>Développement d’applications

### <a name="getting-ready"></a>Vous vous préparez

Créer un nouveau projet d’application du Windows Store dans Visual Studio 2012 ou version ultérieure :

![projet du vs stockage Store applications][store-apps-storage-vs-project]

Ensuite, ajoutez une référence à la bibliothèque de Client de stockage Azure par clic droit sur **références**, en cliquant sur **Ajouter une référence**et en accédant au stockage Client bibliothèque pour Windows Runtime que vous avez téléchargé :

![Store--stockage-sélectionnez-bibliothèque d’applications][store-apps-storage-choose-library]

### <a name="using-the-library-with-the-blob-and-queue-services"></a>À l’aide de la bibliothèque avec les services Blob et file d’attente

À ce stade, votre application est prête à appeler les services Blob Azure et file d’attente. Ajoutez les instructions suivantes **à l’aide de** sorte que les types de stockage Azure peuvent être référencés directement :

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;

Ensuite, ajoutez un bouton à votre page. Ajoutez le code suivant à son événement **Click** et modifier votre méthode de gestionnaire d’événements en utilisant le [mot clé asynchrone](http://msdn.microsoft.com/library/vstudio/hh156513.aspx):

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var blobClient = account.CreateCloudBlobClient();
    var container = blobClient.GetContainerReference("container1");
    await container.CreateIfNotExistsAsync();

Ce code suppose que vous avez variables de deux chaînes, *nom de compte* et *accountKey*. Elles représentent le nom de votre compte de stockage et de la clé du compte associé à ce compte.

Créer et exécuter l’application. Cliquez sur le bouton Vérifier l’existence d’un conteneur nommé *Conteneur1* dans votre compte et créez-la si pas.

### <a name="using-the-library-with-the-table-service"></a>À l’aide de la bibliothèque avec le service de tableau

Types qui sont utilisés pour communiquer avec le service de Table Azure dépendent WCF Data Services pour la bibliothèque de l’application du Windows Store. Ensuite, ajoutez une référence aux bibliothèques WCF nécessaires à l’aide de la Console du Gestionnaire de Package :

![responsable du package stockage magasin applications][store-apps-storage-package-manager]

Utilisez la commande suivante pour le Gestionnaire de Package de point à l’emplacement sur votre ordinateur :

    Install-Package Microsoft.Data.OData.WindowsStore -Source "C:\Program Files (x86)\Microsoft WCF Data Services\5.0\bin\NuGet"

Cette commande ajoute automatiquement toutes les références nécessaires à votre projet. Si vous ne souhaitez pas utiliser la Console du Gestionnaire de Package, vous pouvez ajouter le dossier NuGet de Services de données WCF sur votre ordinateur local à la liste des sources de package, puis ajoutez la référence via l’interface utilisateur, comme décrit dans [Gestion NuGet Packages à l’aide de la boîte de dialogue](http://docs.nuget.org/docs/start-here/Managing-NuGet-Packages-Using-The-Dialog).

Lorsque vous avez référencé le package NuGet de Services de données WCF, modifiez le code dans votre événement **Click** bouton :

    var credentials = new StorageCredentials(accountName, accountKey);
    var account = new CloudStorageAccount(credentials, true);
    var tableClient = account.CreateCloudTableClient();
    var table = tableClient.GetTableReference("table1");
    await table.CreateIfNotExistsAsync();

Ce code vérifie si une table nommée *table1* existe dans votre compte et il crée ensuite si pas.

Vous pouvez également ajouter une référence à Microsoft.WindowsAzure.Storage.Table.dll, qui est disponible dans le même package que vous avez téléchargé. Cette bibliothèque contient des fonctionnalités supplémentaires, telles que sérialisation basées sur la réflexion et requêtes génériques. Notez que cette bibliothèque ne prend pas en charge JavaScript.



[store-apps-storage-vs-project]: ./media/storage-use-store-apps/store-apps-storage-vs-project.png
[store-apps-storage-choose-library]: ./media/storage-use-store-apps/store-apps-storage-choose-library.png
[store-apps-storage-package-manager]: ./media/storage-use-store-apps/store-apps-storage-package-manager.png
