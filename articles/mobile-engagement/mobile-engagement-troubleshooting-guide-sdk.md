<properties 
   pageTitle="Azure Engagement Mobile Guide - Kit de développement de dépannage" 
   description="Résolution des problèmes d’intégration SDK dans Azure Mobile Engagement" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-sdk-integration-issues"></a>Guide de dépannage pour les problèmes d’intégration SDK

Les éléments suivants sont les éventuels problèmes que vous pouvez rencontrer avec comment Azure Mobile Engagement s’intègre à votre application.

## <a name="sdk-issues-discovered-by-a-failure-in-another-area-of-your-application"></a>Problèmes de kit de développement logiciel découverts par une défaillance dans une autre zone de votre application

### <a name="issue"></a>Problème
- Interface utilisateur données collection échec (Analytique, surveillance, Segmentation ou des tableaux de bord).
- Échecs de transmission (push ne fonctionne pas dans l’application, se déconnecter de l’application, ou les deux).
- Options avancées échecs de fonction (suivi, géolocalisation ou plate-forme que pousse spécifique ne fonctionne pas).
- Échecs de l’API (API fail souvent silencieusement sans message d’erreur).
- Échecs de service (aucune des Azure Mobile Engagement fonctionne pour votre application).

### <a name="causes"></a>Causes

- La plupart des problèmes qui doivent être résolus avec le Kit de développement Azure Mobile Engagement sera découvert par une défaillance dans votre application (par exemple, un échec de collecte des données de l’interface utilisateur, échec du push, échecs d’une fonction avancée, Échec de l’API, Application se bloque ou une panne de service apparente).  
- Si une fonctionnalité particulière d’Azure Mobile Engagement n’a jamais fonctionné dans votre application avant, vous avez besoin terminer l’intégration. 
- Si une fonctionnalité particulière d’Azure Mobile Engagement travaillait et arrêté, vous devrez peut-être mettre à niveau vers la dernière version avec le Kit de développement Azure Mobile Engagement. N’oubliez pas qu’il existe une autre version du SDK Azure Mobile Engagement pour chaque plate-forme pris en charge par Azure Mobile Engagement (Android, iOS, Windows et Windows Phone).

#### <a name="sdk-integration"></a>Intégration du Kit de développement

- Azure Engagement Mobile intégré n’est pas correctement dans le Kit de développement logiciel (Analytique).
- Atteindre intégré n’est pas correctement dans le Kit de développement logiciel (dans l’application et application pousse).
- Certificat expiré ou incorrect produit et développement (iOS uniquement).
- GCM ou ADM intégré n’est pas correctement dans le Kit de développement logiciel (Android uniquement - Service spécifique pousse).
- Suivi n’est pas correctement intégré dans le Kit de développement logiciel (store installer suivi).
- Jattes emplacement ou GPS intégré n’est pas correctement dans le Kit de développement logiciel (ciblage par géolocalisation).


**Voir aussi :**

- [Documentation SDK - Guides d’intégration][Link 5] 
- [Guide de dépannage des - Push][Link 23]

#### <a name="sdk-upgrade"></a>Mise à niveau du Kit de développement

- Vous devez mettre SDK pour résoudre les problèmes avec les anciennes versions du Kit de développement (souvent liées à des versions plus récentes du périphérique du système d’exploitation).
- Désinstaller toutes les versions précédentes de votre application à partir de votre appareil et de réinstaller la version la plus récente de votre application, le nouveau livre de votre ID d’appareil à partir de l’interface utilisateur de Azure Mobile Engagement pour confirmer que votre périphérique est à l’aide de la version la plus récente de votre application.

**Voir aussi :**

- [Documentation SDK - Notes de publication](http://go.microsoft.com/fwlink/?LinkId= 525554) 
- [Documentation SDK - repères de mise à niveau](http://go.microsoft.com/fwlink/?LinkId= 525554)

#### <a name="sdk-other"></a>Kit de développement logiciel autres

- Erreurs dans manifeste d’Application « AndroidManifest.xml » peuvent entraîner Azure Mobile Engagement ne pas pour l’utiliser (Android uniquement).
- Un problème courant avec l’intégration du Kit de développement et l’utilisation de l’API est confondre les touches SDK et API.

**Voir aussi :**

- [Concepts - glossaire][Link 6]

## <a name="advanced-coding-issues"></a>Avancé des problèmes de code

### <a name="issue"></a>Problème
-  Code spécifique plateforme pas directement lié à Azure Mobile Engagement peut entraîner des problèmes sur iOS, Android et Windows Phone.

### <a name="causes"></a>Causes

- Nombreuses avancées de codage problèmes avec Azure Mobile Engagement sont dues code spécifique plateforme écrite de manière incorrecte pas directement lié à Azure Mobile Engagement. Vous avez besoin de consulter documentation spécifique à la plateforme que vous développez pour outre documentation Azure Mobile Engagement (Android, iOS, Web, Windows et Windows Phone).
- N’est pas correctement configuré « catégories », empêche la liaison à partir d’une notification à un autre emplacement à l’intérieur ou en dehors de l’application (Android uniquement). 
- Ne pas définir « UIKit.framework » « facultatif » dans votre code iOS, affiche un « Symbole non trouvé d’erreur » et/ou se bloque sur les appareils iOS plus anciens (iOS uniquement).
- Expiré certificats ou n’est pas correctement à l’aide de la version de développement ou produit du certificat, causes push problèmes (iOS uniquement).
- Il existe certaines limites intégrées à une plateforme Azure Mobile Engagement ne pouvez pas contrôler (par exemple, fonctionne le Centre pour déconnecter de l’application pousse dans Android et iOS).
- Azure Mobile Engagement publie une liste complète des packages internes utilisé par Azure Mobile Engagement pour iOS et Android pour référence. N’oubliez pas que certaines fonctionnalités d’Azure Mobile Engagement sont spécifiques à la plateforme (Android, iOS, Web, Windows et Windows Phone).

### <a name="see-also"></a>Voir aussi

 - [Guide de dépannage des - Push][Link 23] 
 - [Documentation SDK - Notes de publication][Link 5]
 - [Documentation SDK - repères de mise à niveau][Link 5]

## <a name="application-crashes"></a>Blocage d’applications

### <a name="issue"></a>Problème
- Votre application se bloque sur périphérique de l’utilisateur final.

### <a name="causes"></a>Causes

- Informations de blocage peuvent être affichées dans l' *Interface utilisateur Analytique* ou l' *API Analytique*
- Vous pouvez rechercher l’ID du périphérique de votre périphérique de test et effectuer l’action même qui a provoqué votre application blocage d’un utilisateur final aider à identifier la cause de votre blocage.
- Problèmes connus avec le Kit de développement Azure Mobile Engagement occasionnant applications arrêté sont parfois résolus par la mise à niveau vers la dernière version du Kit de développement. Veillez à vérifier les notes de publication sur votre plateforme lors de l’investigation se bloque.

### <a name="see-also"></a>Voir aussi

- [Documentation SDK - Notes de publication][Link 5]
- [Documentation SDK - repères de mise à niveau][Link 5]

## <a name="app-store-upload-failures"></a>Échecs de téléchargement App store

### <a name="issue"></a>Problème
- Erreurs liées à télécharger la version la plus récente de votre application Apple, Google ou l’App Windows store.

### <a name="causes"></a>Causes

- Application stocke parfois empêche les applications avec certaines fonctionnalités activées (par exemple, l’Apple Store empêche l’utilisation de IDFV dans les applications dans le magasin et la banque GooglePlay empêche le partage d’informations sur l’application entre les applications). 
- Veillez à consulter les notes de publication sur votre plateforme et la version actuelle du Kit de développement si vous avez des difficultés à télécharger une application dans le magasin.

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/en-us/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/en-us/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/en-us/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
