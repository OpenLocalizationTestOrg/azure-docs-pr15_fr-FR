<properties
   pageTitle="Gestion de vos applications dans Visual Studio | Microsoft Azure"
   description="Utilisez Visual Studio pour créer, développer, du package, déployer et déboguer vos applications de Service tissu et services."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="seanmck;mikhegn"/>

# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilisez Visual Studio pour simplifier la rédaction et la gestion de vos applications de Service TISSU

Vous pouvez gérer vos Azure Service TISSU applications et services via Visual Studio. Une fois que vous avez [configuré votre environnement de développement](service-fabric-get-started.md), vous pouvez utiliser Visual Studio pour créer des applications de Service tissu, ajouter des services, ou package, Registre et déployer des applications dans votre cluster de développement local.

## <a name="deploy-your-service-fabric-application"></a>Déployer votre application de Service TISSU

Par défaut, le déploiement d’une application combine les étapes suivantes dans une seule opération simple :

1. Créer le package d’application
2. Téléchargement du package d’application dans le magasin d’image
3. Enregistrer le type d’application
4. Suppression d’une exécution d’instances de l’application
5. Créer une nouvelle instance de l’application

Dans Visual Studio, appuyez sur **F5** également déployer votre application et joindre le débogueur à toutes les instances de l’application. Vous pouvez utiliser **Ctrl + F5** pour déployer une application sans déboguer, ou vous pouvez publier vers un cluster local ou distant en utilisant le profil de publication. Pour plus d’informations, voir [publication d’une application sur un cluster distant à l’aide de Visual Studio](service-fabric-publish-app-remote-cluster.md).

### <a name="application-debug-mode"></a>Mode de débogage d’application

Par défaut, Visual Studio supprime les instances existantes de votre type d’application lorsque vous arrêtez le débogage ou (si vous avez déployé l’application sans attacher le débogueur), lorsque vous redéployez l’application. Dans ce cas, toutes les données de l’application sont supprimées. Pendant le débogage localement, vous souhaiterez peut-être conserver les données que vous avez déjà créé lorsque vous testez une nouvelle version de l’application, vous souhaitez conserver l’exécution de l’application ou vous souhaitez que les sessions de débogage suivantes pour mettre à niveau de l’application. Outils TISSU du Service Visual Studio fournissent une propriété appelée **Mode débogage d’Application**, les contrôles si le **F5** est préférable de désinstaller l’application, conservent l’application en cours d’exécution après une session de débogage se termine ou permettre à l’application à être mis à jour sur les sessions de débogage suivantes, plutôt que supprimés et redéployé.

#### <a name="to-set-the-application-debug-mode-property"></a>Pour définir la propriété Mode débogage d’Application

1. Dans le menu contextuel du projet de l’application, cliquez sur **Propriétés** (ou appuyez sur la touche **F4** ).
2. Dans la fenêtre **Propriétés** , définissez la propriété **Mode débogage d’Application** .

    ![Définir la propriété de Mode débogage d’Application][debugmodeproperty]

Voici les options de **Mode de débogage des applications** disponibles.

1. **Mise à niveau automatique**: l’application continue à exécuter lorsque la session de débogage se termine. La prochaine **F5** traitera le déploiement comme une mise à niveau en utilisant le mode non contrôlée automatique pour rapidement mettre à niveau de l’application à une version plus récente avec une chaîne de date ajoutée. Le processus de mise à niveau conserve toutes les données que vous avez entrées dans une session de débogage précédent.

2. **Conserver une Application**: l’application continue à fonctionner dans le cluster expiration de la session de débogage. Dans la prochaine **F5** l’application est supprimée et l’application nouvellement créée sera déployée sur le cluster.

3. **Supprimer une Application** , l’application à être supprimées lors de la session de débogage se termine.

Pour **Mettre à niveau automatique** les données sont conservées en appliquant les fonctionnalités de mise à niveau application du Service tissu, mais il est réglé pour optimiser les performances plutôt que de sécurité. Pour plus d’informations sur la mise à niveau des applications et comment vous pouvez effectuer une mise à niveau dans un environnement réel, voir [mettre à niveau de l’application de Service TISSU](service-fabric-application-upgrade.md).

![Exemple de la nouvelle version de l’application dont la date ajoutée][preservedata]

>[AZURE.NOTE] Cette propriété n’existe pas antérieures à la version 1.1 des outils TISSU Service pour Visual Studio. Avant 1.1, utilisez la propriété **Conserver les données sur Start** pour obtenir le même comportement. L’option « Conserver l’Application » a été introduite dans la version 1.2 des outils TISSU Service pour Visual Studio.

## <a name="add-a-service-to-your-service-fabric-application"></a>Ajouter un service à votre application de Service TISSU

Vous pouvez ajouter de nouveaux services à votre application pour étendre ses fonctionnalités.  Pour vous assurer que le service est inclus dans votre package d’application, ajoutez le service via l’élément de menu **Nouveau Service tissu...** .

![Ajouter un nouveau service tissu à votre application][newservice]

Sélectionnez un type de projet tissu de Service à ajouter à votre application, puis spécifiez un nom pour le service.  Consultez [en choisissant une structure à votre service](service-fabric-choose-framework.md) pour vous aider à décider quel type de service à utiliser.

![Sélectionnez un type de projet Service tissu à ajouter à votre application][addserviceproject]

Le nouveau service est ajouté à votre solution et le package d’application existant. Les références de service et une instance de service par défaut sont ajoutés au manifeste d’application. Le service sera créé et mise en route de la prochaine fois que vous déployez l’application.

![Le nouveau service est ajouté à votre manifeste d’application][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Empaqueter votre application de Service TISSU

Pour déployer l’application et ses services sur un cluster, vous devez créer un package d’application.  Le package organise le manifeste d’application, manifest(s) de service et autres fichiers nécessaires dans une disposition spécifique.  Visual Studio configure et gère le package dans l’annuaire « pkg » du dossier du projet de l’application.  En cliquant sur **Package** dans le menu contextuel **Application** crée ou met à jour le package d’application.  Vous souhaiterez peut-être procéder ainsi si vous déployez l’application à l’aide des scripts PowerShell personnalisés.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Supprimer des applications et des types d’application à l’aide de l’Explorateur de Cloud

Vous pouvez effectuer les opérations de gestion de cluster de base de dans Visual Studio à l’aide de Cloud Explorer, vous pouvez lancer dans le menu **affichage** . Par exemple, vous pouvez supprimer des applications et annule la configuration des types d’applications sur les clusters locales ou distantes.

![Supprimer une application](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

>[AZURE.TIP] Pour les fonctionnalités de gestion de cluster plus riche, consultez [visualiser votre cluster avec l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Étapes suivantes

- [Modèle d’application tissu de service](service-fabric-application-model.md)
- [Déploiement des applications de service TISSU](service-fabric-deploy-remove-applications.md)
- [Gestion des paramètres de l’application pour les environnements multiples](service-fabric-manage-multiple-environment-app-configuration.md)
- [Débogage de votre application de Service TISSU](service-fabric-debugging-your-application.md)
- [Visualiser votre cluster à l’aide de l’Explorateur de tissu de Service](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]:./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
