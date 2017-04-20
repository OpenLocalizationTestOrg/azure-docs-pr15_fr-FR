<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - paramètres" 
   description="Découvrez comment gérer les paramètres globaux de votre application à l’aide d’Azure Mobile Engagement" 
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

# <a name="how-to-manage-the-global-settings-of-your-application"></a>Comment gérer les paramètres globaux de votre application

Les options du menu **paramètres** disponibles pour une application dépendent de la plateforme de l’application et les autorisations que vous avez obtenu pour l’application. Paramètres sont les suivants : détails, projets, transmission Native, vitesse de transmission, balise (informations de l’application) et la pression commerciale. L’option de menu balise (informations application) de la section Paramètres peut être gérée par votre application (en utilisant le Kit de développement) ou par votre principale (à l’aide de l’API Device). 


>[AZURE.NOTE] Nombre de sections du portail **Mobile Engagement** interface utilisateur contiennent le bouton **Afficher l’aide** . Appuyez sur ce bouton pour obtenir plus d’informations contextuelles sur une section.

## <a name="details"></a>Plus d’informations

Vous pouvez modifier le nom et la description de votre application, permet d’afficher le propriétaire de votre application et des autorisations de rôle. 

Configuration Analytique vous permet d’afficher ou modifier le jour de démarrage de semaines et la durée de conservation dans jour (s).
 
  ![paramètres requis1][46]
 
## <a name="projects"></a>Projets

Vous permet de sélectionner tous les projets que vous souhaitez que votre application s’affichent dans. 

Vous pouvez également rechercher un projet et afficher le nom, description, propriétaire et des autorisations de rôle de n’importe quel projet d' que votre application fait partie.

Pour plus d’informations, voir : [Documentation de l’interface utilisateur – famille][Link 13]
 
  ![settings3][48]

## <a name="native-push"></a>Push natif

Vous permet d’enregistrer un nouveau certificat ou le supprimer et le certificat existant pour une utilisation avec push native. Push native permet Azure Mobile Engagement pousser à votre application à tout moment, même quand il ne fonctionne pas. 

Après avoir fourni les informations d’identification ou des certificats pour au moins un service de transmission Native, vous pouvez sélectionner « Tout moment » lors de la création de communiquer aux campagnes et également utiliser le paramètre « notificateur » dans l’API de transmission.



### <a name="apple-push-notification-service-apns"></a>Service de Notification Push Apple (APNS)

Pour activer la transmission Native à l’aide du Service de Notification Push Apple, vous devrez enregistrer votre certificat. Vous avez besoin spécifier le type de certificat développement (développement) ou de production (production). Vous allez puis devez télécharger votre certificat et le mot de passe.

Pour plus d’informations, voir : [Documentation SDK - iOS - comment préparer votre Application pour les notifications de transmission Apple][Link 5]
 
![settings4][49]
 
### <a name="windows-push-notification-service-wpns"></a>Service de Notification Push Windows (WPNS)

Pour activer la transmission Native à l’aide du Service de Notification de Windows, vous devez fournir les informations d’identification de votre application. Vous aurez besoin votre Package sécurité identificateur et la clé secrète.
 
![settings5][50]
 
### <a name="google-cloud-messaging-for-android-gcm"></a>Google Cloud messagerie pour Android (GCM)

Pour activer la transmission Native à l’aide de GCM, vous devez suivre les instructions à partir de Google. Puis vous devez le coller une clé de l’API simple serveur, configuré sans restrictions IP. Nécessite l’intégration avec le Kit de développement pour Android v1.12.0 +.

Pour plus d’informations, voir : 

- [Kit de développement logiciel Documentation Android comment intégrer GCM][Link 5]
- [Guide GCM développeur Google](http://developer.android.com/guide/google/gcm/gs.html)
 
### <a name="amazon-device-messaging-for-android-adm"></a>APPAREIL Amazon messagerie pour Android (ADM)

Pour activer la transmission Native à l’aide de ADM, vous devez fournir Amazon <OAuth credentials> composée d’un ID de Client et Secret Client (nécessite l’intégration avec SDK pour Android v2.1.0 +).

Pour plus d’informations, voir : 

- [Kit de développement logiciel Documentation Android comment intégrer ADM][Link 5]
- [Documentation présentée sous d’Amazon développeur ADM](https://developer.amazon.com/sdk/adm/credentials.html#Getting)
 
![settings6][51]

## <a name="push-speed"></a>Vitesse de transmission

Affiche la vitesse push actuelle de votre application et vous permet de définir la vitesse push de votre application.
 
  ![settings7][52]

## <a name="tag-app-info"></a>Balise (informations de l’application)

![settings11][56]
  
## <a name="commercial-pressure"></a>Pression commerciale


![settings12][57]


## <a name="see-also"></a>Voir aussi

- [Concepts][Link 6]
- [Résolution des problèmes de Service du Guide][Link 24]

 

<!--Image references-->
[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png
[2]: ./media/mobile-engagement-user-interface-home/home1.png
[3]: ./media/mobile-engagement-user-interface-home/home2.png
[4]: ./media/mobile-engagement-user-interface-home/home3.png
[5]: ./media/mobile-engagement-user-interface-home/home4.png
[6]: ./media/mobile-engagement-user-interface-home/home5.png
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
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
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md
 
