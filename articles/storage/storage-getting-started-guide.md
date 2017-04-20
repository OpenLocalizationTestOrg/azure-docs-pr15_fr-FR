<properties
    pageTitle="Prise en main le stockage Azure dans cinq minutes | Microsoft Azure"
    description="Progresser rapidement sur Microsoft Azure BLOB et Table des files d’attente à l’aide de démarrage rapide de stockage Azure, Visual Studio et l’émulateur de stockage Azure. Exécuter votre première application Azure stockage de cinq minutes."
    services="storage"
    documentationCenter=".net"
    authors="tamram"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="tamram"/>

# <a name="get-started-with-azure-storage-in-five-minutes"></a>Prise en main le stockage Azure dans cinq minutes

## <a name="overview"></a>Vue d’ensemble

Il est facile de route pour le développement avec stockage Azure. Ce didacticiel montre comment obtenir une application stockage Azure et rapidement opérationnel. Vous allez utiliser les modèles de démarrage rapide inclus dans le Kit de développement Azure pour .NET. Ces Démarrages rapides contiennent prêts à l’exécution de code qui illustre certains scénarios de programmation simples avec le stockage Azure.

Pour en savoir plus sur le stockage Azure avant de plonger dans le code, consultez les [Étapes suivantes](#next-steps).

## <a name="prerequisites"></a>Conditions préalables

Vous devez les conditions préalables suivantes avant de commencer :

1. Pour compiler et générer l’application, vous aurez besoin d’une version de [Visual Studio](https://www.visualstudio.com/) installée sur votre ordinateur.

2. Installer la dernière version [Azure SDK pour .NET](https://azure.microsoft.com/downloads/). Le Kit de développement inclut les exemples de démarrage rapide Azure projet, l’émulateur de stockage Azure et la [Bibliothèque de Client de stockage Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

3. Vérifiez que vous avez [.NET Framework 4.5](http://www.microsoft.com/download/details.aspx?id=30653) installé sur votre ordinateur, comme cela est requis par les exemples de démarrage rapide Azure projet que nous utiliserons dans ce didacticiel.

    Si vous ne savez pas quelle version du .NET Framework est installée sur votre ordinateur, voir [Comment : déterminer lequel .NET Framework Versions sont installés](https://msdn.microsoft.com/vstudio/hh925568.aspx). Ou bien, appuyez sur le bouton **Démarrer** ou sur la touche Windows, tapez **Le panneau de configuration**. Ensuite, cliquez sur **programmes** > **programmes et fonctionnalités**et déterminez si le .NET Framework 4.5 figure parmi les programmes installés.

4. Vous avez besoin d’un abonnement Azure et un compte de stockage Azure.

    - Pour obtenir un abonnement Azure, voir [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/), [Options d’achat](https://azure.microsoft.com/pricing/purchase-options/)et [Propose des membres](https://azure.microsoft.com/pricing/member-offers/) (pour les membres de MSDN, Microsoft Partner Network, BizSpark et des autres programmes Microsoft).
    - Pour créer un compte de stockage dans Azure, Découvrez [comment créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account).

## <a name="run-your-first-azure-storage-application-against-azure-storage-in-the-cloud"></a>Exécuter votre première application Azure stockage sur le stockage Azure dans le cloud

Une fois que vous avez un compte, vous pouvez créer une application de stockage Azure simple à l’aide d’un des exemples de démarrage rapide d’Azure projet dans Visual Studio. Ce didacticiel se concentre sur les exemples de projet pour le stockage Azure : **stockage Azure : objets BLOB**, **stockage Azure : fichiers**, **stockage Azure : files d’attente**, et **stockage Azure : Tables**:

1. Démarrez Visual Studio.
2. Dans le menu **fichier** , cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **installer** > **modèles** > **c#** > **Cloud** > **Démarrages rapides** > **Data Services**.
    un. Choisissez un des modèles suivants : **stockage Azure : objets BLOB**, **stockage Azure : fichiers**, **stockage Azure : files d’attente**, ou **stockage Azure : Tables**.
    b. Assurez-vous que **.NET Framework 4.5** est sélectionné comme framework cible.
    - 3 c. Attribuez un nom à votre projet et créer la solution Visual Studio, comme indiqué :

    ![Démarrage rapide d’Azure][Image1]

Vous voudrez peut-être examiner le code source avant l’exécution de l’application. Pour consulter le code, sélectionnez **Explorateur de solutions** dans le menu **affichage** dans Visual Studio. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l’exemple d’application :

1.  Dans Visual Studio, sélectionnez **Explorateur de solutions** dans le menu **affichage** . Ouvrez le fichier App.config et commentez la chaîne de connexion pour l’émulateur de stockage Azure :

    `<!--<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>-->`

2.  Supprimez les marques de la chaîne de connexion pour le Service de stockage Azure et fournir le stockage accès et le nom clé de compte dans le fichier App.config :`<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]"`

    Pour récupérer votre clé de l’accès de compte de stockage, voir [gérer vos clés d’accès de stockage](storage-create-storage-account.md#manage-your-storage-access-keys).

3.  Une fois que vous indiquez le nom de compte de stockage et touche d’accès rapide dans le fichier App.config, dans le menu **fichier** , cliquez sur **Enregistrer tout** pour enregistrer tous les fichiers de projet.
4.  Dans le menu **Générer** , cliquez sur **Générer la Solution**.
5.  Dans le menu **Déboguer** , appuyez sur **F11** pour exécuter la solution étape par étape ou appuyez sur **F5** pour exécuter la solution.


## <a name="run-your-first-azure-storage-application-locally-against-the-azure-storage-emulator"></a>Exécuter votre première application Azure stockage localement contre l’émulateur de stockage Azure

L' [Émulateur de stockage Azure](storage-use-emulator.md) fournit un environnement local qui émule les services Blob Azure, file d’attente et de Table pour le développement. Vous pouvez utiliser l’émulateur de stockage pour tester votre application de stockage localement, sans création d’un abonnement Azure ou un compte de stockage et sans que tous les coûts.

Pour essayer, nous allons créer une application de stockage Azure simple à l’aide d’un des exemples de démarrage rapide d’Azure projet dans Visual Studio. Ce didacticiel se concentre sur les exemples de projet **Azure Blob Storage**, **Stockage de Table Azure**et **Azure file d’attente de stockage** :

1. Démarrez Visual Studio.
2. Dans le menu **fichier** , cliquez sur **Nouveau projet**.
3. Dans la boîte de dialogue **Nouveau projet** , cliquez sur **installer** > **modèles** > **c#** > **Cloud** > **Démarrages rapides** > **Data Services**.
    un. Choisissez un des modèles suivants : **stockage Azure : objets BLOB**, **stockage Azure : fichiers**, **stockage Azure : files d’attente**, ou **stockage Azure : Tables**.
    b. Assurez-vous que **.NET Framework 4.5** est sélectionné comme framework cible.
    c. Attribuez un nom à votre projet et créer la solution Visual Studio, comme indiqué :

    ![Démarrage rapide d’Azure][Image1]

4.  Dans Visual Studio, sélectionnez **Explorateur de solutions** dans le menu **affichage** . Ouvrez le fichier App.config et commentez la chaîne de connexion pour votre compte de stockage Azure si vous avez déjà ajouté une. Puis ne commentez pas la chaîne de connexion pour l’émulateur de stockage Azure :

    `<add key="StorageConnectionString" value = "UseDevelopmentStorage=true;"/>`

Vous voudrez peut-être examiner le code source avant l’exécution de l’application. Pour consulter le code, sélectionnez **Explorateur de solutions** dans le menu **affichage** dans Visual Studio. Ensuite, double-cliquez sur le fichier Program.cs.

Ensuite, exécutez l’exemple d’application dans l’émulateur de stockage Azure :

1.  Appuyez sur la touche Windows, recherchez *émulateur de Microsoft Azure stockage*, ou sur le bouton **Démarrer** et lancer l’application. Lorsque l’émulateur démarre, vous verrez une icône et une notification dans la zone d’affichage des tâches Windows.
2.  Dans Visual Studio, cliquez sur **Générer la Solution** dans le menu **Générer** .
3.  Dans le menu **Déboguer** , appuyez sur **F11** pour exécuter la solution étape par étape, ou appuyez sur **F5** pour exécuter la solution de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Consultez ces ressources pour en savoir plus sur le stockage Azure :

* [Introduction au stockage de Microsoft Azure](storage-introduction.md)
* [Prise en main avec l’Explorateur de stockage Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Prise en main stockage d’objets Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md)
* [Prise en main stockage de Table Azure à l’aide de .NET](storage-dotnet-how-to-use-tables.md)
* [Prise en main Azure Queue Storage à l’aide de .NET](storage-dotnet-how-to-use-queues.md)
* [Prise en main le stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md)
* [Transférer des données avec l’utilitaire de ligne de commande AzCopy](storage-use-azcopy.md)
* [Documentation du stockage Azure](https://azure.microsoft.com/documentation/services/storage/)
* [Bibliothèque de Client de stockage de Microsoft Azure pour .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)
* [Services de stockage Azure API REST](https://msdn.microsoft.com/library/azure/dd179355.aspx)

[Image1]: ./media/storage-getting-started-guide/QuickStart.png
