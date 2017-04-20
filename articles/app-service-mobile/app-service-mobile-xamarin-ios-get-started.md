<properties
    pageTitle="Prise en main Azure Application Service Mobile applications pour les applications Xamarin.iOS | Microsoft Azure"
    description="Suivez ce didacticiel pour commencer à utiliser à l’aide des applications mobiles pour le développement Xamarin.iOS."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="na"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha"/>


#<a name="create-a-xamarinios-app"></a>Créer une application Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter un service de nuage principal à une application mobile Xamarin.iOS à l’aide d’un serveur principal Azure application mobile.  Vous créez un nouveau contexte de l’application mobile et une simple _liste Todo_ application Xamarin.iOS qui stocke les données d’application dans Azure.

Fin de ce didacticiel est requis pour tous les autres didacticiels Xamarin.iOS sur l’utilisation de la fonctionnalité d’applications Mobile dans le Service d’application Azure.

##<a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez les conditions préalables suivantes :

* Un compte Azure actif. Si vous n’avez pas un compte, vous inscrire une version d’évaluation Azure et obtenez jusqu'à 10 applications mobiles gratuites que vous pouvez continuer à utiliser même après la fin de votre version d’évaluation. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, voir [le programme d’installation et d’installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

* Un Mac avec Xcode v7.0 ou version ultérieure et Communauté Studio Xamarin installé. Consultez [le programme d’installation et d’installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) et [le programme d’installation, installer et vérifications pour les utilisateurs de Mac](https://msdn.microsoft.com/library/mt488770.aspx) (MSDN).

>[AZURE.NOTE]Si vous voulez commencer avec le Service d’application Azure avant de vous inscrivez à un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile). Vous pouvez immédiatement créer une application mobile starter courte dans le Service d’application : aucune carte de crédit obligatoire et aucun engagements.

## <a name="create-an-azure-mobile-app-backend"></a>Créer une application Mobile Azure principal

Suivez ces étapes pour créer une application Mobile principale.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

Vous avez maintenant sa mise en service un serveur principal application Mobile Azure qui peut être utilisée par vos applications de client mobile. Ensuite, téléchargez un projet serveur pour une simple « liste todo » principal et publiez-le sur Azure.

Suivez les étapes suivantes pour configurer le projet serveur pour utiliser la version serveur .NET ou Node.js.

[AZURE.INCLUDE [app-service-mobile-configure-new-backend](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinios-app"></a>Téléchargez et exécutez l’application Xamarin.iOS

1. Ouvrez le [portail Azure] dans une fenêtre de navigateur.

2. Dans la carte de paramètres pour votre application Mobile, cliquez sur **Mise en route** > **Xamarin.iOS**. Sous étape 3, cliquez sur **créer une nouvelle application** si ce n’est pas déjà fait.  Cliquez ensuite sur le bouton **Télécharger** .

    Une application cliente qui se connecte à votre serveur principal mobile est téléchargée. Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

3. Extraire le projet que vous avez téléchargé et puis ouvrez-le dans Xamarin Studio (ou Visual Studio).

    ![][9]

    ![][8]

4. Appuyez sur la touche F5 pour générer le projet et lancer l’application dans l’émulateur iPhone.

5. Dans l’application, tapez du texte significatif, tel que _Xamarin en savoir plus_, puis cliquez sur le **+** bouton.

    ![][10]

    Données de la demande sont insérées dans la table TodoItem. Éléments stockés dans la table sont retournés par le système principal de l’application mobile, et les données sont affichées dans la liste.

>[AZURE.NOTE]Vous pouvez examiner le code qui accède à votre serveur principal application mobile pour les requêtes et insérer des données dans le fichier QSTodoService.cs c#.

##<a name="next-steps"></a>Étapes suivantes

* [Ajoutez la synchronisation hors connexion dans votre application](app-service-mobile-xamarin-ios-get-started-offline-data.md)
* [Ajouter une authentification dans votre application](app-service-mobile-xamarin-ios-get-started-users.md)
* [Ajouter les notifications push à votre application Xamarin.Android](app-service-mobile-xamarin-ios-get-started-push.md)
* [Comment utiliser le client géré pour applications Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)

<!-- Anchors. -->
[Getting started with mobile app backends]:#getting-started
[Create a new mobile app backend]:#create-new-service
[Next Steps]:#next-steps

<!-- Images. -->
[6]: ./media/app-service-mobile-xamarin-ios-get-started/xamarin-ios-quickstart.png
[8]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-vs.png
[9]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-xamarin-project-ios-xs.png
[10]: ./media/app-service-mobile-xamarin-ios-get-started/mobile-quickstart-startup-ios.png

<!-- URLs. -->
[Portail Azure]: https://portal.azure.com/
