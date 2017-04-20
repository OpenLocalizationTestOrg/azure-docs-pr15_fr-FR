<properties
   pageTitle="Création d’un projet Azure avec Visual Studio | Microsoft Azure"
   description="Création d’un projet Azure avec Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="creating-an-azure-project-with-visual-studio"></a>Création d’un projet Azure avec Visual Studio

La Azure Tools pour Visual Studio fournir un modèle qui vous permet de créer un service cloud pour Azure. Les outils également vous aident à configurer, déboguer et déployer le service cloud sur Azure.

Une solution de service cloud Azure contient les types de projets suivants :

- **Projet Azure**

    Le projet Azure a associations aux projets rôle dans la solution. Il inclut également la définition de service et les fichiers de configuration de service. Le fichier de définition de service définit les paramètres pour l’exécution de votre application, y compris les rôles requis, points de terminaison et la taille de la machine virtuelle. Le fichier de configuration de service configure le nombre d’instances d’un rôle est exécuté et les valeurs des paramètres définis pour un rôle. Pour plus d’informations sur ces paramètres, voir [Comment : configurer les rôles d’un Service Cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

- **Projet de rôle Web**

    Un rôle de collaborateur effectue le traitement d’arrière-plan. Un rôle de collaborateur peut communiquer avec les services de stockage et avec d’autres services basées sur Internet. Un rôle de collaborateur peut avoir n’importe quel nombre de points de terminaison HTTP, HTTPS ou TCP.

    - **Rôle de Web ASP.NET**, pour la création d’une application ASP.NET avec un site web frontal
    - **Rôle de Web ASP.NET MVC5**
    - **Rôle de Web ASP.NET MVC4**
    - **Rôle de Web ASP.NET MVC3**
    - **Rôle de Web Service WCF**, pour la création d’un service WCF
    - **Rôle de Silverlight Business Application Web** (nécessite Visual Studio 2012)

- **Rôle de travail du cache**

    Rôle qui fournit un cache dédié à votre application.

- **Rôle de travail avec file d’attente de Bus de Service**

    Un service bus file d’attente qui fournit une fonctionnalité de files d’attente de message pour communiquer avec le processus de travail. Pour plus d’informations, voir [comment utiliser Service Bus files d’attente](http://go.microsoft.com/fwlink/?LinkId=260560).

## <a name="to-create-an-azure-cloud-service-project-in-visual-studio"></a>Pour créer un projet de service cloud Azure dans Visual Studio

1. Démarrez Microsoft Visual Studio en tant qu’administrateur.

1. Dans la barre de menus, cliquez sur **fichier**, **Nouveau**, **projet**.

1. Dans le volet **Types de projets** , choisissez **Cloud** dans le projet Visual c# ou Visual Basic nœuds du modèle.

1. Dans le volet **modèles** , choisissez **Azure Cloud Service**.

1. Spécifiez la version du .NET Framework que vous voulez utiliser pour développer votre projet.

1. Entrez un nom et un emplacement pour votre projet et un nom pour la solution. Cliquez sur le bouton **OK** .

1. Dans la boîte de dialogue **Nouveau projet Azure** , sélectionnez les rôles que vous voulez ajouter, puis cliquez sur le bouton flèche droite pour les ajouter à votre solution. Vous pouvez ajouter autant de rôles que nécessaire.

1. Pour renommer un rôle que vous avez ajoutés à votre projet, placez le curseur sur le rôle dans la boîte de dialogue **Nouveau projet Azure** , puis cliquez sur l’icône à droite du rôle **Renommer** . Vous pouvez également renommer un rôle au sein de votre solution une fois qu’il a été ajouté.
