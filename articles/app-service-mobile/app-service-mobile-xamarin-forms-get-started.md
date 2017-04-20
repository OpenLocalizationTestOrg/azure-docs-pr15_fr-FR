<properties
    pageTitle="Prise en main applications Mobile à l’aide de Xamarin.Forms"
    description="Suivez ce didacticiel pour commencer à utiliser les applications Mobile Azure pour le développement Xamarin.Forms"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>

#<a name="create-a-xamarinforms-app"></a>Créer une application Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter un service de nuage principal à une application mobile Xamarin.Forms à l’aide d’une application Mobile Azure principal. Vous allez créer un nouveau contexte de l’application Mobile et une simple _liste Todo_ application Xamarin.Forms qui stocke les données d’application dans Azure.

Fin de ce didacticiel est requis pour tous les autres didacticiels applications Mobile pour Xamarin.Forms.

##<a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez les éléments suivants :

* Un compte Azure actif. Si vous n’avez pas un compte, vous pouvez vous inscrire une Azure d’évaluation et obtenir jusqu'à 10 gratuites applications Mobile que vous pouvez continuer à utiliser même après la fin de votre version d’évaluation. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, voir [le programme d’installation et d’installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) . 

* Un Mac avec Xcode v7.0 ou version ultérieure et Communauté Studio Xamarin installé. Consultez [le programme d’installation et d’installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [le programme d’installation, installer et vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).
 
>[AZURE.NOTE] Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile), où vous pouvez créer un application Mobile de starter courte immédiatement dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="create-a-new-azure-mobile-app-backend"></a>Créer un nouveau contexte de l’application Mobile Azure

Suivez ces étapes pour créer un nouveau contexte de l’application Mobile.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]


Vous avez maintenant sa mise en service un serveur principal application Mobile Azure qui peut être utilisée par vos applications de client mobile. Ensuite, vous allez télécharger un projet de serveur pour une simple « liste todo » principal et publiez-le sur Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

Suivez les étapes ci-dessous pour configurer le projet serveur pour utiliser la version serveur .NET ou Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

##<a name="download-and-run-the-xamarinforms-solution"></a>Télécharger et exécuter la solution Xamarin.Forms

Ici, vous avez deux options. Vous pouvez télécharger la solution sur un Mac et ouvrez-le dans Xamarin Studio, ou vous pouvez télécharger la solution sur un ordinateur Windows et ouvrez-la dans Visual Studio à l’aide d’un Mac en réseau pour la création de l’application iOS. Voir [le programme d’installation et installer pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) si vous avez besoin d’instructions plus détaillées sur les scénarios d’installation Xamarin.

Nous allons procéder :

 1. Sur votre Mac ou sur votre ordinateur Windows, ouvrez le [Portail Azure] dans une fenêtre de navigateur.
 2. Dans la carte de paramètres pour votre application Mobile, cliquez sur **Mise en route** (sous Mobile) > **Xamarin.Forms**. Sous étape 3, cliquez sur **créer une nouvelle application** si ce n’est pas déjà fait.  Cliquez ensuite sur le bouton **Télécharger** .

    Ceci permet de télécharger un projet qui contient une application cliente qui est connectée à votre application mobile. Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

 3. Extraire le projet que vous avez téléchargé et puis ouvrez-le dans Xamarin Studio ou Visual Studio.

    ![][9]

    ![][8]


##<a name="optional-run-the-ios-project"></a>(Facultatif) Exécutez le projet iOS

Cette section s’applique à l’exécution du projet iOS Xamarin pour les appareils iOS. Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils iOS.

####<a name="in-xamarin-studio"></a>Dans Xamarin Studio

1. Droit sur le projet iOS, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **exécution** , cliquez sur **Démarrer le débogage** pour générer le projet et lancer l’application dans l’émulateur iPhone.

####<a name="in-visual-studio"></a>Dans Visual Studio
1. Droit sur le projet iOS, puis cliquez sur **définir comme projet de démarrage**.
2. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
3. Dans la boîte de dialogue **Gestionnaire de Configuration** , sélectionnez les cases à cocher **créer** et **déployer** du projet iOS.
4. Appuyez sur la touche **F5** pour générer le projet et lancer l’application dans l’émulateur iPhone.

    >[AZURE.NOTE] Si vous rencontrez des problèmes créer, exécuter NuGet Gestionnaire de package et mise à jour vers la dernière version de la Xamarin prennent en charge des packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans mis à jour vers la dernière.    

Dans l’application, tapez le texte significatif, tel que _Xamarin en savoir plus_ , puis cliquez sur le **+** bouton.

![][10]

Cela envoie une demande de publication pour le nouveau système principal de l’application mobile hébergé dans Azure. Données de la demande sont insérées dans la table TodoItem. Éléments stockés dans la table sont retournés par le système principal de l’application mobile, et les données sont affichées dans la liste.

>[AZURE.NOTE]
> Vous trouverez le code qui accède à votre serveur principal de l’application mobile dans le fichier TodoItemManager.cs c# du projet de bibliothèque de classes portable de votre solution.

##<a name="optional-run-the-android-project"></a>(Facultatif) Exécutez le projet Android

Cette section s’adresse en cours d’exécution du projet droid Xamarin pour Android. Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils Android.

####<a name="in-xamarin-studio"></a>Dans Xamarin Studio

1. Cliquez sur le projet Android, puis cliquez sur **Définir comme projet de démarrage**.
2. Dans le menu **exécution** , cliquez sur **Démarrer le débogage** pour générer le projet et lancer l’application dans un émulateur Android.

####<a name="in-visual-studio"></a>Dans Visual Studio
1. Droit sur le projet Android (Droid), puis cliquez sur **définir comme projet de démarrage**.
4. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
5. Dans la boîte de dialogue **Gestionnaire de Configuration** , sélectionnez les cases à cocher **créer** et **déployer** du projet Android.
6. Appuyez sur la touche **F5** pour générer le projet et lancer l’application dans un émulateur Android.

    >[AZURE.NOTE] Si vous rencontrez des problèmes créer, exécuter NuGet Gestionnaire de package et mise à jour vers la dernière version de la Xamarin prennent en charge des packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans mis à jour vers la dernière.    


Dans l’application, tapez le texte significatif, tel que _Xamarin en savoir plus_ , puis cliquez sur le **+** bouton.

![][11]

Cela envoie une demande de publication pour le nouveau système principal de l’application mobile hébergé dans Azure. Données de la demande sont insérées dans la table TodoItem. Éléments stockés dans la table sont retournés par le système principal de l’application mobile, et les données sont affichées dans la liste.

> [AZURE.NOTE]
> Vous trouverez le code qui accède à votre serveur principal de l’application mobile dans le fichier TodoItemManager.cs c# du projet de bibliothèque de classes portable de votre solution.


##<a name="optional-run-the-windows-project"></a>(Facultatif) Exécutez le projet de Windows


Cette section s’applique à l’exécution du projet Xamarin WinApp pour les appareils Windows. Vous pouvez ignorer cette section si vous ne travaillez pas avec des appareils Windows.


####<a name="in-visual-studio"></a>Dans Visual Studio
1. Cliquez sur un des projets Windows, puis cliquez sur **définir comme projet de démarrage**.
4. Dans le menu **Générer** , cliquez sur **Gestionnaire de Configuration**.
5. Dans la boîte de dialogue **Gestionnaire de Configuration** , sélectionnez les cases à cocher **créer** et **déployer** du projet Windows que vous avez choisi.
6. Appuyez sur la touche **F5** pour générer le projet et lancer l’application dans un émulateur Windows.

    >[AZURE.NOTE] Si vous rencontrez des problèmes créer, exécuter NuGet Gestionnaire de package et mise à jour vers la dernière version de la Xamarin prennent en charge des packages. Parfois, les projets de démarrage rapide peuvent rester derrière dans mis à jour vers la dernière.    


Dans l’application, tapez le texte significatif, tel que _Xamarin en savoir plus_ , puis cliquez sur le **+** bouton.

Cela envoie une demande de publication pour le nouveau système principal de l’application mobile hébergé dans Azure. Données de la demande sont insérées dans la table TodoItem. Éléments stockés dans la table sont retournés par le système principal de l’application mobile, et les données sont affichées dans la liste.

![][12]

> [AZURE.NOTE]
> Vous trouverez le code qui accède à votre serveur principal de l’application mobile dans le fichier TodoItemManager.cs c# du projet de bibliothèque de classes portable de votre solution.

##<a name="next-steps"></a>Étapes suivantes

* [Ajouter une authentification dans votre application](app-service-mobile-xamarin-forms-get-started-users.md)  
Découvrez comment authentifier les utilisateurs de votre application avec un fournisseur d’identité.

* [Ajouter les notifications push dans votre application](app-service-mobile-xamarin-forms-get-started-push.md)  
Découvrez comment ajouter push notifications prennent en charge dans votre application et configurer la principale application Mobile pour Azure Notification Hubs permet d’envoyer les notifications push.

* [Activer la synchronisation hors connexion pour votre application](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Découvrez comment ajouter une prise en charge en mode hors connexion votre application à l’aide d’un serveur principal application Mobile. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau.

* [Comment utiliser le client géré pour applications Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)  
Découvrez comment utiliser le client géré SDK dans votre application Xamarin. 


<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps


<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart.png
[8]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-vs.png
[9]: ./media/app-service-mobile-xamarin-forms-get-started/xamarin-forms-quickstart-xs.png
[10]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-android.png
[12]: ./media/app-service-mobile-xamarin-forms-get-started/mobile-quickstart-startup-windows.png


<!-- URLs. -->
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile app SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Portail Azure]: https://portal.azure.com/

