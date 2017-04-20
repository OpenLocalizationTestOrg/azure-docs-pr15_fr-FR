<properties
    pageTitle="Configuration de Windows applications universel Engagement SDK avancée"
    description="Options de Configuration pour Azure Mobile Engagement avec Windows universel applications avancées"                    
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows-store"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="piyushjo;ricksal" />

# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Configuration de Windows applications universel Engagement SDK avancée

> [AZURE.SELECTOR]
- [Universel de Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advanced-configuration.md)

Cette procédure explique comment configurer les différentes options de configuration pour les applications Azure Mobile Engagement Android.

## <a name="prerequisites"></a>Conditions préalables

[AZURE.INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

##<a name="advanced-configuration"></a>Configuration avancée

### <a name="disable-automatic-crash-reporting"></a>Désactiver le signalement de blocage automatique

Vous pouvez désactiver la fonctionnalité d’Engagement de rapport d’incident automatique. Puis, lorsqu’une exception non gérée se produit, Engagement n’a aucun effet.

> [AZURE.WARNING] Si vous désactivez cette fonctionnalité, puis lorsqu’un blocage non gérée se produit dans votre application, Engagement n’envoie pas que l’incident **et** ne ferme pas la session et les tâches.

Pour désactiver le signalement de blocage automatique, personnaliser votre configuration selon la façon dont vous l’avez déclaré :

#### <a name="from-engagementconfigurationxml-file"></a>À partir de `EngagementConfiguration.xml` fichier

Blocage de rapport la valeur `false` entre `<reportCrash>` et `</reportCrash>` balises.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>À partir de `EngagementConfiguration` objet en cours d’exécution

Blocage de rapport la valeur false à l’aide de votre objet EngagementConfiguration.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="disable-real-time-reporting"></a>Désactiver le rapport en temps réel

Par défaut, les rapports de service Engagement ouvre une session en temps réel. Si votre application signale les journaux fréquemment, il est préférable de la mémoire tampon les journaux et les rapports en une seule fois sur une base de temps standard. Cette option est appelée « mode rafale ».

Pour ce faire, appelez la méthode :

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

L’argument est une valeur en **millisecondes**. Chaque fois que vous souhaitez réactiver la journalisation en temps réel, appelez la méthode sans paramètres ou avec la valeur 0.

Mode rupture légèrement augmente la durée de vie de batterie mais a un impact sur l’analyseur d’Engagement : toutes les sessions et travaux la durée sont arrondis à la valeur seuil rupture (ainsi, les sessions et les tâches plus courtes que le seuil rupture peut ne pas être visible). Nous vous recommandons d’utiliser un seuil rupture n’est plus à 30000 (30 s). Journaux enregistrés sont limités à 300 éléments. Si l’envoi est trop longue, vous pouvez perdre certains journaux.

> [AZURE.WARNING] Le seuil de rupture ne peut pas être configuré pour une période inférieure à une seconde. Si vous procédez ainsi, le Kit de développement affiche une trace de l’erreur et reconfigure automatiquement à la valeur par défaut, zéro secondes. Cela déclenche le Kit de développement pour signaler les journaux en temps réel.

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
