<properties
    pageTitle="Ajouter une authentification sur Android et applications Mobile | Service application Azure"
    description="Découvrez comment utiliser les applications Mobile dans le Service d’application Azure pour authentifier les utilisateurs de votre application Android via un grand nombre de fournisseurs d’identité, y compris Google, Facebook, Twitter et Microsoft."
    services="app-service\mobile"
    documentationCenter="android"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-authentication-to-your-android-app"></a>Ajouter une authentification dans votre application Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous ajoutez l’authentification vers le projet de démarrage rapide de liste des tâches sur Android à l’aide d’un fournisseur d’identité pris en charge. Ce didacticiel est basé sur le didacticiel [prise en main applications Mobile] , vous devez effectuer tout d’abord.

##<a name="register"></a>Enregistrer votre application pour l’authentification et configurer le Service d’application

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restreindre les autorisations pour les utilisateurs authentifiés

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Dans Studio Android, ouvrez le projet projeté vous terminé avec le didacticiel [prise en main applications Mobile]. Dans le menu **exécution** sur **exécuter l’application** et vérifiez qu’une exception non gérée avec un code d’état de 401 (non autorisé) est élevée après le démarrage de l’application.

     Cette exception se produit car l’application tente d’accéder à la version serveur en tant qu’utilisateur non authentifié, mais la table _TodoItem_ maintenant requiert une authentification.

Ensuite, vous mettez à jour l’application pour l’authentification des utilisateurs avant de demander des ressources à partir du serveur principal de l’application Mobile.

## <a name="add-authentication-to-the-app"></a>Ajouter une authentification à l’application

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Mettre en cache les jetons d’authentification sur le client

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Étapes suivantes

Maintenant que vous terminé ce didacticiel l’authentification de base, vous pouvez passer à un des didacticiels suivants :

+ [Ajouter les notifications de transmission dans votre application Android](app-service-mobile-android-get-started-push.md) Découvrez comment configurer votre serveur principal application Mobile pour Azure Notification Hubs permet d’envoyer les notifications push.

+ [Activer la synchronisation hors connexion pour votre application Android](app-service-mobile-android-get-started-offline-data.md) Découvrez comment ajouter une prise en charge en mode hors connexion votre application à l’aide d’une application Mobile principale. Synchronisation hors connexion permet aux utilisateurs finaux d’interagir avec une application mobile&mdash;l’affichage, l’ajout ou modification de données&mdash;même lorsqu’il n’y a aucune connexion réseau.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Prise en main applications Mobile]: app-service-mobile-android-get-started.md
