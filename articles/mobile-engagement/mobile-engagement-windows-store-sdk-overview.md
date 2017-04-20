<properties
    pageTitle="Intégration universel SDK Windows"
    description="Intégration universel Windows pour SDK pour Azure Engagement Mobile"                                     
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/12/2016"
    ms.author="piyushjo;ricksal" />

#<a name="windows-universal-sdk-integration-for-azure-mobile-engagement"></a>Intégration d’universel SDK Windows pour Azure Engagement Mobile

Ce document décrit toutes les intégration et configuration options disponibles pour le SDK Azure Mobile Engagement Windows universel.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez d’abord achever notre [didacticiel de 15 minutes](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="advanced-features"></a>Fonctionnalités avancées

### <a name="reporting-features"></a>Fonctionnalités de création de rapports
Vous pouvez ajouter les fonctionnalités suivantes :

1. [Options de création de rapports avancées](mobile-engagement-windows-store-advanced-reporting.md)

2. [Options de configuration avancée](mobile-engagement-windows-store-advanced-configuration.md)

### <a name="notifications"></a>Notifications

[Comment intégrer accessibles (Notifications) dans votre application Windows universel](mobile-engagement-windows-store-integrate-engagement-reach.md)

### <a name="tag-plan-implementation"></a>Implémentation du plan de balise :

[Comment utiliser l’Engagement Mobile avancées marquage API dans votre application Windows universel](mobile-engagement-windows-store-use-engagement-api.md)

##<a name="release-notes"></a>Notes de publication

###<a name="340-04192016"></a>3.4.0 (19/04/2016)

-   Atteindre améliorations superposition.
-   Journaux de console ajouté « TestLogLevel « API à activer/désactiver/filtre émises par le Kit de développement.
-   Démarrer des notifications dans activité fixes ciblage la première activité s’affichée ne pas dans l’application.

Pour les versions antérieures, voir les [notes de publication terminée](mobile-engagement-windows-store-release-notes.md)

##<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure d’Engagement dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Si vous avez manqué plusieurs versions du Kit de développement, vous devrez quelques procédures à suivre. Voir les [Procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)complètes. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord, procédez comme « de 0.9.0 à 0.10.1 » ensuite la procédure « de 0.10.1 à 0.11.0 ».

###<a name="from-330-to-340"></a>À partir de 3.3.0 à 3.4.0

####<a name="test-logs"></a>Tester les journaux

Journaux de console obtenus par le Kit de développement peuvent désormais être activé, désactivé/filtrés. Pour personnaliser, mettre à jour la propriété `EngagementAgent.Instance.TestLogEnabled` à l’une des valeurs disponibles à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

####<a name="resources"></a>Ressources

La superposition portée a été améliorée. Il fait partie des ressources de package NuGet SDK.

Lors de la mise à niveau vers la nouvelle version du Kit de développement, vous pouvez choisir si vous souhaitez conserver vos fichiers à partir du dossier superposition de vos ressources ou non :

* Si la superposition précédente fonctionne pour vous, ou si vous intégrez le `WebView` éléments manuellement, puis vous pouvez décider de conserver votre fermeture de fichiers, il fonctionnera toujours.
* Pour mettre à jour à la nouvelle superposition, remplacez la totalité `overlay` dossier à partir de vos ressources par le nouveau à partir du package Kit de développement logiciel (applications UWP : après la mise à niveau, vous pouvez obtenir le nouveau dossier superposition de %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [AZURE.WARNING] L’utilisation de la nouvelle superposition remplace toutes les personnalisations apportées à la version précédente.

### <a name="upgrade-from-older-versions"></a>Mettre à niveau à partir de versions antérieures

Consultez [les procédures de mise à niveau](mobile-engagement-windows-store-upgrade-procedure.md)
