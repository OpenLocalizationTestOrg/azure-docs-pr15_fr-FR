<properties 
   pageTitle="Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio | Microsoft Azure"
   description="Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="storage"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="tarcher" />

# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Configuration et l’utilisation de l’émulateur de stockage avec Visual Studio

[AZURE.INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>Vue d’ensemble
L’environnement de développement Azure SDK inclut l’émulateur de stockage, un utilitaire qui simule les services de stockage Blob, file d’attente et Table disponibles dans Azure sur votre ordinateur de développement local. Si vous n’êtes création d’un service cloud qui utilise les services de stockage Azure ou écrivez n’importe quelle application externe qui appelle les services de stockage, vous pouvez tester votre code localement par rapport à l’émulateur de stockage. Les outils Azure pour Microsoft Visual Studio intégrer la gestion de l’émulateur de stockage dans Visual Studio. Les outils Azure initialisation de la base de données stockage émulateur lors de la première utilisation, démarre le service de stockage sur émulateur lorsque vous exécutez ou déboguez votre code de Visual Studio et offre un accès en lecture seule aux données d’émulateur du stockage via l’Explorateur d’espace de stockage Azure.

Pour plus d’informations sur l’émulateur de stockage, y compris la configuration système requise et les instructions de configuration personnalisés, voir [utiliser l’émulateur de stockage Azure de développement et de test](./storage/storage-use-emulator.md).

>[AZURE.NOTE] Il existe quelques différences de fonctionnalités entre la simulation émulateur de stockage et les services de stockage Azure. Dans la documentation Azure SDK pour plus d’informations sur les différences spécifiques, voir [différences entre l’émulateur de stockage et de Services de stockage Azure](./storage/storage-use-emulator.md) .

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>Configuration d’une chaîne de connexion pour l’émulateur de stockage

Pour accéder à l’émulateur de stockage à partir du code dans un rôle, vous souhaiterez configurer une chaîne de connexion qui pointe vers l’émulateur de stockage et qui peut être modifiée ultérieurement pour pointer vers un compte de stockage Azure. Une chaîne de connexion est un paramètre de configuration que votre rôle peut lire en cours d’exécution pour vous connecter à un compte de stockage. Pour plus d’informations sur la création des chaînes de connexion, voir [configuration de l’Application Azure](https://msdn.microsoft.com/library/azure/2da5d6ce-f74d-45a9-bf6b-b3a60c5ef74e#BK_SettingsPage).

>[AZURE.NOTE] Vous pouvez renvoyer une référence à ce compte émulateur de stockage à partir de votre code à l’aide de la propriété **DevelopmentStorageAccount** . Cette approche fonctionne correctement si vous souhaitez accéder l’émulateur de stockage à partir de votre code, mais si vous envisagez de publier votre application sur Azure, vous devrez créer une chaîne de connexion pour accéder à votre compte de stockage Azure et modifiez votre code pour utiliser cette chaîne de connexion avant de les publier. Si vous utilisiez auparavant entre le compte émulateur de stockage et d’un compte de stockage Azure fréquemment, une chaîne de connexion simplifie ce processus.

## <a name="initializing-and-running-the-storage-emulator"></a>Lors de l’initialisation et en exécutant l’émulateur de stockage

Vous pouvez spécifier que lorsque vous exécutez ou déboguez votre service dans Visual Studio, Visual Studio lance automatiquement l’émulateur de stockage. Dans l’Explorateur de solutions, ouvrez le menu contextuel pour votre projet **Azure** , puis sélectionnez **Propriétés**. Sous l’onglet **développement** , dans la liste **Démarrer émulateur de stockage Azure** , choisissez **True** (si elle n’est pas déjà définie à cette valeur).

La première fois que vous exécutez ou déboguez votre service à partir de Visual Studio, l’émulateur de stockage lance un processus d’initialisation. Ce processus réserve ports locaux pour l’émulateur de stockage et crée la base de données de stockage émulateur. Une fois terminé, ce processus n’a pas besoin d’exécuter à nouveau, à moins que la base de données de stockage émulateur est supprimé.

>[AZURE.NOTE] En commençant par la version de juin 2012 des outils Azure, l’émulateur de stockage s’exécute, par défaut, dans SQL Express LocalDB. Dans les versions antérieures des outils Azure, l’émulateur de stockage s’exécute sur une instance par défaut de SQL Express 2005 ou 2008, que vous devez installer avant d’installer le Kit de développement Azure. Vous pouvez également exécuter l’émulateur de stockage par rapport à une instance nommée de SQL Express ou portant un nom ou une instance par défaut de Microsoft SQL Server. Si vous avez besoin configurer l’émulateur de stockage à utiliser pour exécuter une instance autre que l’instance par défaut, voir [utiliser l’émulateur de stockage Azure de développement et de test](./storage/storage-use-emulator.md).

L’émulateur de stockage fournit une interface utilisateur pour afficher l’état des services de stockage local et de démarrer, arrêter et réinitialiser les. Une fois que le service de stockage sur émulateur a commencé, vous pouvez afficher l’interface utilisateur ou démarrer ou arrêter le service par clic droit sur l’icône de zone de notification pour Microsoft Azure Emulator dans la barre des tâches Windows.

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>Affichage des données émulateur stockage dans l’Explorateur de serveurs

Le nœud de stockage Azure dans l’Explorateur de serveur permet d’afficher les données et modifier les paramètres des données blob et table dans vos comptes de stockage, y compris l’émulateur de stockage. Pour plus d’informations, voir [la navigation et gestion du stockage des ressources avec l’Explorateur de serveur](https://msdn.microsoft.com/library/azure/ff683677.aspx) .
