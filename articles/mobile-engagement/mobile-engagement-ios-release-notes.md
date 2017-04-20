<properties
    pageTitle="Azure Engagement Mobile iOS Notes de publication SDK | Microsoft Azure"
    description="Dernières mises à jour et procédures pour iOS SDK pour Azure Mobile Engagement"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-ios"
    ms.devlang="objective-c"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="piyushjo" />

#<a name="azure-mobile-engagement-ios-sdk-release-notes"></a>Notes de publication Azure Engagement Mobile iOS SDK

##<a name="400-09122016"></a>4.0.0 (09/12/2016)

-   Notification fixe non traitée sur les appareils iOS 10.
-   Déconseiller XCode 7.

##<a name="324-06302016"></a>3.2.4 (30/06/2016)

-   Agrégation fixe entre journaux techniques et d’autres.

##<a name="323-06072016"></a>3.2.3 (06/07/2016)

-   Résolu le bogue dans lequel les commentaires de remise ne sont pas signalée lorsque l’application est en arrière-plan.
-   Optimisée pour l’envoi des journaux techniques.

##<a name="322-04072016"></a>3.2.2 (04/07/2016)

-   Bogue sur l’annulation demande HTTP parfois prospects bloquer.

##<a name="321-12112015"></a>3.2.1 (11/12/2015)

-   Fixe le délai lorsqu’une nouvelle instance de l’application est déclenchée par une notification de liens approfondies

##<a name="320-10082015"></a>3.2.0 (08/10/2015)

-   Activé Bitcode dans le Kit de développement pour qu’il fonctionne avec **Xcode 7**.
-   Bogues corrigés relatives aux notifications dans l’application.
-   Les notifications dans l’application rendu plus fiable en cas de batterie faible et autres cas de figure.
-   Supprimer les journaux de console supplémentaire générées par 3e partie bibliothèque.

##<a name="310-08262015"></a>3.1.0 (26/08/2015)

-   Corriger les bogues de compatibilité iOS 9 avec une bibliothèque de tiers. Il a été à l’origine se bloque lors de l’envoi d’interrogation des résultats, des informations sur l’application ou des données supplémentaires.

##<a name="300-06192015"></a>3.0.0 (19/06/2015)

-   Engagement mobile utilise les Notifications de transmission en mode silencieux.
-   Supprimée prise en charge pour iOS 4.X. À partir de cette version de la cible du déploiement de votre application doit contenir au moins iOS 6.

##<a name="220-05212015"></a>2.2.0 (21/05/2015)

-   L’id du périphérique Mobile Engagement pour appareils mobiles < iOS 6 est désormais basée sur un GUID généré au moment de l’installation.

##<a name="210-04242015"></a>2.1.0 (24/04/2015)

-   Grande compatibilité rapide.
-   Lorsque vous cliquez sur une notification, l’URL est désormais l’action exécutée droite une fois que l’application est ouverte.
-   Ajout de fichier en-tête manquant dans package SDK.
-   Correction d’un problème lors de la reporter blocage Engagement Mobile a été désactivé.

##<a name="200-02172015"></a>2.0.0 (17/02/2015)

-   Version initiale d’Engagement Mobile Azure
-   configuration identificateur/sdkKey est remplacée par une configuration de chaîne de connexion.
-   Supprimer l’API pour envoyer et recevoir des messages XMPP arbitraires à partir d’entités XMPP arbitraires.
-   Supprimé API pour envoyer et recevoir des messages entre les périphériques.
-   Améliorations de la sécurité.
-   Suivi SmartAd supprimé.
