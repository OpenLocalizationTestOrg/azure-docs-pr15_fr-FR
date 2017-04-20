<properties
    pageTitle="Prise en main des applications mobiles Azure pour Xamarin.Android"
    description="Suivez ce didacticiel pour commencer à utiliser les applications Mobile Azure pour le développement Xamarin Android"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="adrianhall"
    manager="erikre"
    editor="" />

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="10/01/2016"
    ms.author="adrianha" />

#<a name="create-a-xamarinandroid-app"></a>Créer une application Xamarin.Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started](../../includes/app-service-mobile-selector-get-started.md)]

##<a name="overview"></a>Vue d’ensemble

Ce didacticiel montre comment ajouter un service de nuage principal à une application Xamarin.Android. Pour plus d’informations, voir [Quels sont les applications Mobile](app-service-mobile-value-prop.md).

Capture d’écran de l’application finale est inférieur à :

![][0]

Fin de ce didacticiel est requis pour tous les autres didacticiels applications Mobile pour les applications Xamarin.Android.

##<a name="prerequisites"></a>Conditions préalables

Pour effectuer ce didacticiel, vous devez les conditions préalables suivantes :

* Un compte Azure actif. Si vous n’avez pas un compte, vous inscrire une Azure d’évaluation et obtenez jusqu'à 10 applications Mobile gratuit. Pour plus d’informations, voir [Azure la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

* Visual Studio avec Xamarin. Pour obtenir des instructions, voir [le programme d’installation et d’installation pour Visual Studio et Xamarin](https://msdn.microsoft.com/library/mt613162.aspx) .

>[AZURE.NOTE]Si vous voulez commencer à utiliser le Service d’application Azure avant de vous inscrire pour un compte Azure, accédez à [Essayer le Service application](https://tryappservice.azure.com/?appServiceName=mobile).  Vous pouvez immédiatement créer un application Mobile de starter courte dans le Service d’application. Aucune carte de crédit obligatoire ; Aucune engagements.

## <a name="create-an-azure-mobile-app-backend"></a>Créer une application Mobile Azure principal

Suivez ces étapes pour créer une application Mobile principale.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Vous avez maintenant sa mise en service un serveur principal application Mobile Azure qui peut être utilisée par vos applications de client mobile. Ensuite, téléchargez un projet serveur pour une simple « liste todo » principal et publiez-le sur Azure.

## <a name="configure-the-server-project"></a>Configurer le projet de serveur

[AZURE.INCLUDE [app-service-mobile-configure-new-backend.md](../../includes/app-service-mobile-configure-new-backend.md)]

## <a name="download-and-run-the-xamarinandroid-app"></a>Téléchargez et exécutez l’application Xamarin.Android

1. Sous **télécharger et exécuter votre projet Xamarin.Android**, cliquez sur le bouton **Télécharger** .

    Enregistrer le fichier de projet compressés sur votre ordinateur local et prenez note de l’endroit où vous l’enregistrez.

2. Appuyez sur la touche **F5** pour générer le projet et lancer l’application.

3. Dans l’application, tapez du texte significatif, tel que de _Terminer le didacticiel_ , puis sur le bouton **Ajouter** .

    ![][10]

    Données de la demande sont insérées dans la table TodoItem. Éléments stockés dans la table sont retournés par le système principal de l’application mobile, et les données s’affichent dans la liste.

    > [AZURE.NOTE] Vous pouvez examiner le code qui accède à votre serveur principal application mobile pour les requêtes et insérer des données, qui sont trouve dans le fichier ToDoActivity.cs c#.

##<a name="next-steps"></a>Étapes suivantes

* [Ajoutez la synchronisation hors connexion dans votre application](app-service-mobile-xamarin-android-get-started-offline-data.md)
* [Ajouter une authentification dans votre application](app-service-mobile-xamarin-android-get-started-users.md)
* [Ajouter les notifications push à votre application Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md)
* [Comment utiliser le client géré pour applications Mobile Azure](app-service-mobile-dotnet-how-to-use-client-library.md)


<!-- Images. -->
[0]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/app-service-mobile-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[8]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/app-service-mobile-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/app-service-mobile-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Azure Portal]: https://azure.portal.com/
[Visual Studio]: https://go.microsoft.com/fwLink/p/?LinkID=534203
