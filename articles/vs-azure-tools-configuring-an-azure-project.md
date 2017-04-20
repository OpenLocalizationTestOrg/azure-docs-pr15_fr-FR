<properties
   pageTitle="Configurer un projet de Service Cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment configurer un projet de service cloud Azure dans Visual Studio, selon vos besoins pour ce projet."
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

# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurer un projet de Service Cloud Azure avec Visual Studio

Vous pouvez configurer un projet de service cloud Azure, selon vos besoins pour ce projet. Vous pouvez définir les propriétés du projet pour les catégories suivantes :

- **Publier un service cloud sur Azure**

  Vous pouvez définir une propriété pour vous assurer qu’un service cloud existant déployé sur Azure n’est pas accidentellement supprimé.

- **Exécuter ou déboguer un service cloud sur l’ordinateur local**

  Vous pouvez sélectionner une configuration du service à utiliser et indiquer si vous souhaitez démarrer l’émulateur de stockage Azure.

- **Valider un package de service cloud lors de sa création**

  Vous pouvez décider de traiter tous les avertissements comme des erreurs de sorte que vous pouvez vous assurer que le package de service cloud déployer sans problème. Cela permet de réduire votre délai d’attente si vous déployez et puis découvrez qu’une erreur est survenue.

L’illustration suivante montre comment sélectionner une configuration à utiliser lorsque vous exécutez ou déboguez votre service cloud localement. Vous pouvez définir aucune des propriétés du projet dont vous avez besoin de cette fenêtre, comme le montre l’illustration.

![Configurer un projet de Microsoft Azure](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## <a name="to-configure-an-azure-cloud-service-project"></a>Pour configurer un projet de service cloud Azure

1. Pour configurer un projet de service cloud à partir de **L’Explorateur de solutions**, ouvrez le menu contextuel pour le projet de service cloud, puis sur **Propriétés**.

  Une page avec le nom du projet de service cloud s’affiche dans l’éditeur Visual Studio.

1. Sélectionnez l’onglet **développement** .

1. Pour vous assurer que vous n’avez accidentellement supprimé un déploiement existant dans Azure, dans l’invite avant de supprimer une liste de déploiement existante, sélectionnez **True**.

1. Pour sélectionner la configuration du service que vous souhaitez utiliser lorsque vous exécutez ou déboguez votre service cloud localement, dans la liste de **configuration du Service de** choisir la configuration du service.

  >[AZURE.NOTE] Si vous souhaitez créer une configuration du service à utiliser, consultez Comment : gérer les Configurations de Service et les profils. Si vous souhaitez modifier une configuration de service pour un rôle, Découvrez [comment configurer les rôles d’un service cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

1. Pour lancer le stockage Azure émulateur lorsque vous exécutez ou déboguez localement, votre service cloud dans l' **émulateur de stockage Azure commencer**, choisissez **True**.

1. Pour vous assurer que vous ne pouvez pas publier si présente des erreurs de validation de package, **considérer les avertissements comme des erreurs**, cliquez sur **True**.

1. Pour vous assurer que votre rôle web utilise le même port chaque fois qu’il démarre localement dans IIS Express, dans **utiliser des ports de project web**, cliquez sur **True**. Pour utiliser un port spécifique d’un projet web particulier, ouvrir le menu contextuel pour le projet web, sélectionnez l’onglet **Propriétés** , cliquez sur l’onglet **Web** et modifier le numéro de port dans le paramètre **Url de Project** dans la section **IIS Express** . Par exemple, entrez `http://localhost:14020` en tant que l’URL de project.

1. Pour enregistrer les modifications que vous avez apportées aux propriétés du projet de service cloud, cliquez sur le bouton **Enregistrer** dans la barre d’outils.

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration des projets de service cloud Azure dans Visual Studio, voir [configuration de Azure votre projet à l’aide de plusieurs configurations de service](vs-azure-tools-multiple-services-project-configurations.md).
