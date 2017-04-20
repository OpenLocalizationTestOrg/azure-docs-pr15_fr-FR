<properties 
    pageTitle="Windows Phone SDK Silverlight procédures mise à niveau" 
    description="Windows Phone SDK Silverlight procédures mise à niveau pour Azure Engagement Mobile"                  
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="dwrede"
    editor="" />

<tags 
    ms.service="mobile-engagement" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="mobile-windows-phone" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/19/2016" 
    ms.author="piyushjo" />

#<a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone SDK Silverlight procédures mise à niveau

Si vous avez déjà intégré une version antérieure de notre SDK dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Vous devrez quelques procédures à suivre si vous avez manqué plusieurs versions du Kit de développement. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord, procédez comme « de 0.9.0 à 0.10.1 » ensuite la procédure « de 0.10.1 à 0.11.0 ».

##<a name="from-200-to-330"></a>À partir de 2.0.0 à 3.3.0

### <a name="test-logs"></a>Tester les journaux

Journaux de console obtenus par le Kit de développement peuvent désormais être activé, désactivé/filtrés. Pour personnaliser ce, mettre à jour la propriété `EngagementAgent.Instance.TestLogEnabled` à un de la valeur disponible à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

##<a name="from-111-to-200"></a>À partir de 1.1.1 à 2.0.0

Voici comment migrer une intégration SDK à partir du service Capptain offert par Capptain SAS dans une application optimisée par Azure Mobile Engagement. 

> [Azure.IMPORTANT] Capptain et Engagement Mobile ne sont pas les mêmes services et la procédure décrite ci-dessous clairs uniquement comment effectuer une migration de l’application client. Migrer le Kit de développement dans l’application ne doit pas migrer vos données à partir des serveurs Capptain aux serveurs Engagement Mobile

Si vous effectuez une migration depuis une version antérieure, consultez le site web Capptain pour migrer vers 1.1.1 tout d’abord, puis appliquez la procédure suivante

### <a name="nuget-package"></a>Package NuGet

Remplacez **Capptain.WindowsPhone** par **MicrosoftAzure.MobileEngagement** Nuget package.

### <a name="applying-mobile-engagement"></a>Application d’Engagement Mobile

Le Kit de développement utilise le terme `Engagement`. Vous devez mettre à jour votre projet pour correspondre à cette modification.

Vous devez désinstaller votre package nuget Capptain en cours. Envisagez de toutes vos modifications dans le dossier Capptain ressources sont supprimées. Si vous souhaitez conserver ces fichiers puis copiez-les d'entre eux.

Après cela, installez le nouveau package nuget Microsoft Azure Engagement sur votre projet. Vous pouvez trouver directement sur [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement). Cette action remplace tous les fichiers de ressources utilisées par Engagement et ajoute la nouvelle DLL Engagement à vos références de projet.

Vous devez effacer les références de votre projet en supprimant les références Capptain DLL. Si vous n’effectuez pas cette option, la version de Capptain créera un conflit et erreurs seront produit.

Si vous avez personnalisé Capptain ressources, copiez le contenu de votre ancien fichiers et collez-les dans les nouveaux fichiers Engagement. Notez que les fichiers xaml et SC doivent être mis à jour.

Une fois ces étapes terminées, il vous suffit de remplacer les anciennes références Capptain par les nouvelles références Engagement.

1. Tous les espaces de noms Capptain doivent être mis à jour.

    Avant la migration :
    
        using Capptain.Agent;
        using Capptain.Reach;
    
    Après la migration :
    
        using Microsoft.Azure.Engagement;

2. Toutes les classes Capptain qui contiennent « Capptain » doivent contenir « Engagement ».

    Avant la migration :
    
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
    
    Après la migration :
    
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }

3. Pour les fichiers xaml attributs et espace de noms Capptain également modifier.

    Avant la migration :
    
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
    
    Après la migration :
    
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>

4. Pour les autres ressources comme des images Capptain, notez qu’ils également ont été renommés pour utiliser « Engagement ».

### <a name="application-id--sdk-key"></a>ID de l’application / clé SDK

Engagement utilise une chaîne de connexion. Vous n’êtes pas obligé de spécifier un ID de l’application et une touche SDK avec Engagement Mobile, vous suffit spécifier une chaîne de connexion. Vous pouvez configurer celui-ci sur votre fichier EngagementConfiguration.

La configuration d’Engagement pouvant être définie dans votre `Resources\EngagementConfiguration.xml` fichier de votre projet.

Modifier ce fichier pour spécifier :

-   Chaîne de connexion votre application entre balises `<connectionString>` et `<\connectionString>`.

Si vous voulez spécifier à la place en cours d’exécution, vous pouvez appeler la méthode suivante avant l’initialisation de l’agent Engagement :

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        
        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

La chaîne de connexion pour votre application s’affiche dans le portail classique Azure.

### <a name="items-name-change"></a>Modification du nom des éléments

Tous les éléments nommés *capptain* ont été nommées *engagement*. De même pour *Capptain* à *Engagement*.

Exemples d’éléments Capptain fréquemment utilisées :

-   CapptainConfiguration nommé maintenant EngagementConfiguration
-   CapptainAgent nommé maintenant EngagementAgent
-   CapptainReach nommé maintenant EngagementReach
-   CapptainHttpConfig nommé maintenant EngagementHttpConfig
-   GetCapptainPageName nommé maintenant GetEngagementPageName

Notez que renommer concerne également les méthodes remplacées.



 
