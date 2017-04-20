<properties 
    pageTitle="Windows Phone SDK Silverlight vue d’ensemble" 
    description="Vue d’ensemble du Kit de développement Windows Phone Silverlight pour Azure Engagement Mobile"                     
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

#<a name="windows-phone-silverlight-sdk-overview-for-azure-mobile-engagement"></a>Windows Phone vue d’ensemble du Kit de développement Silverlight pour Azure Engagement Mobile

Commencez ici pour en savoir plus sur l’intégration Azure Mobile Engagement dans une application Silverlight de Windows Phone. Si vous souhaitez faire un essai tout d’abord, vérifiez que vous avez effectué notre [didacticiel de 15 minutes](mobile-engagement-windows-phone-get-started.md).

Cliquez sur pour voir le [Kit de développement du contenu](mobile-engagement-windows-phone-sdk-content.md)

##<a name="integration-procedures"></a>Procédures d’intégration

1. Commencez ici : [comment intégrer Engagement Mobile dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement.md)

2. Pour les Notifications : [comment intégrer accessibles (Notifications) dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-integrate-engagement-reach.md)

3. Implémentation du plan de la balise : [comment utiliser l’Engagement Mobile avancées marquage API dans votre application Silverlight de Windows Phone](mobile-engagement-windows-phone-use-engagement-api.md)

##<a name="release-notes"></a>Notes de publication

###<a name="330-04192016"></a>3.3.0 (19/04/2016)
Partie de la nuget *MicrosoftAzure.MobileEngagement* package **v3.4.0**

-   Journaux de console ajouté « TestLogLevel « API à activer/désactiver/filtre émises par le Kit de développement.

Pour une version antérieure, consultez les [notes de publication terminée](mobile-engagement-windows-phone-release-notes.md)

##<a name="upgrade-procedures"></a>Procédures de mise à niveau

Si vous avez déjà intégré une version antérieure de notre SDK dans votre application, vous devez prendre en compte les points suivants lorsque le Kit de développement de la mise à niveau.

Vous devrez quelques procédures à suivre si vous avez manqué plusieurs versions du Kit de développement. Voir les [Procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)complètes. Par exemple, si vous migrez à partir de 0.10.1 vers 0.11.0, que vous devez tout d’abord, procédez comme « de 0.9.0 à 0.10.1 » ensuite la procédure « de 0.10.1 à 0.11.0 ».

###<a name="from-200-to-330"></a>À partir de 2.0.0 à 3.3.0

####<a name="test-logs"></a>Tester les journaux

Journaux de console obtenus par le Kit de développement peuvent désormais être activé, désactivé/filtrés. Pour personnaliser ce, mettre à jour la propriété `EngagementAgent.Instance.TestLogEnabled` à un de la valeur disponible à partir de la `EngagementTestLogLevel` énumération, par exemple :

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="upgrade-from-older-versions"></a>Mettre à niveau à partir de versions antérieures

Consultez [les procédures de mise à niveau](mobile-engagement-windows-phone-upgrade-procedure.md)
 
