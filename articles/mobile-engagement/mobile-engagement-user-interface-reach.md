<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - accessibles" 
   description="Découvrez comment aux utilisateurs de votre application communiquer grâce aux notifications push à l’aide de Azure Mobile Engagement" 
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


# <a name="how-to-reach-out-to-the-users-of-your-application-with-push-notifications"></a>Comment faire pour contacter des utilisateurs de votre application avec les notifications push

Cet article décrit l’onglet **ATTEIGNE** du portail **Mobile Engagement** . Le portail **d’Engagement Mobile** vous permet de contrôler et de gérer vos applications mobiles. Notez que pour commencer à utiliser le portail vous devez tout d’abord créer un compte **Azure Mobile Engagement** . Pour plus d’informations, voir [créer un compte Azure Mobile Engagement](mobile-engagement-create.md).

La section portée de l’interface utilisateur est l’outil de gestion de campagne Push dans laquelle vous pouvez créer/modifier/activer/fin/moniteur et obtenir les statistiques sur campagnes de notification Push et les fonctionnalités qui sont également accessibles via l’API atteigne (et certains éléments de niveau inférieur transmission API). N’oubliez pas que si vous utilisez les API ou l’interface utilisateur, vous devez intégrer Azure Mobile Engagement et accessibles dans votre application pour chaque plate-forme avec le Kit de développement avant de pouvoir utiliser communiquer aux campagnes marketing.

>[AZURE.NOTE] Nombre de sections du portail **Mobile Engagement** interface utilisateur contiennent le bouton **Afficher l’aide** . Appuyez sur ce bouton pour obtenir plus d’informations contextuelles sur une section.

## <a name="four-types-of-push-notifications"></a>Quatre types de notifications de transmission
1.    Annonces - vous permet d’envoyer des messages publicitaires aux utilisateurs qui les rediriger vers un autre emplacement à l’intérieur de votre application ou leur envoyer à une page Web ou d’un magasin en dehors de votre application. 
2.    Sondages - permettent de collecter les informations sur les utilisateurs finaux par leur poser des questions.
3.    Données push - permettent d’envoyer un fichier de données en base 64 ou binaire. Les informations contenues dans un push de données sont envoyées à votre application pour modifier l’expérience actuelle de vos utilisateurs dans votre application. Votre application doit être en mesure de traiter les données d’une diffusion de données.

## <a name="campaign-details"></a>Détails de la campagne

Vous pouvez modifier, cloner, supprimer ou activer les campagnes qui n’ont pas encore été activés en pointant sur leurs noms ou vous pouvez cliquer pour les ouvrir. Vous pouvez cloner campagnes qui ont déjà été activés en pointant sur leurs noms ou vous pouvez cliquer pour les ouvrir. Toutefois, vous ne pouvez pas modifier une campagne une fois qu’il a été activé.
 
![Reach1][18]

## <a name="reach-feedback"></a>Atteindre les commentaires

Cliquez sur **statistiques** pour afficher les détails d’une campagne accessibles. La vue **Simple** fournit une représentation visuelle sous la forme d’un graphique à barres colonne sur ce qui est arrivé après qu’une campagne a été activée. L’affichage **Avancé** fournit des détails plus précis sur la campagne push. Ces informations ne sera pas disponibles si vous envoyez une campagne de test c'est-à-dire un push envoyé à un périphérique de test. Voici comment vous devez interpréter ces détails :

1. **Pushed** - Spécifie le nombre de messages vers les appareils. Ce nombre dépend de l’audience cible que vous avez spécifié lors de la création de la campagne push. Si vous ne spécifiez pas n’importe quel public cible, cette push est envoyée pour tous les appareils inscrits. Comme tous les autres services push, nous faire pas les notifications de transmission directement aux périphériques mais à la place les pousser à la plate-forme correspondante des Services de Notification Push spécifiques (solution - APNS/GCM/WNS) afin qu’elles pour pouvoir délivrer les notifications pour les appareils. 

2.  **Remis** - Spécifie le nombre de messages qui sont correctement remis par la solution à l’appareil et reconnu comme reçu par le Kit de développement Mobile Engagement. 
        
    *Raisons pour remis compter le nombre est inférieure à count poussé :*
    
    1. Si l’utilisateur a désinstallé l’application à partir du périphérique, mais ne sait pas la solution à ce sujet au moment où que nous envoyer la diffusion à la solution le message est supprimé.
    2. Si l’appareil est l’application, mais les périphériques eux-mêmes ont été en mode hors connexion pour les longues périodes de temps, la solution échouera remettre le message à l’appareil. 
    3. Si le message obtenir remis à l’appareil, mais le Kit de développement Mobile Engagement dans l’application ne reconnaît pas le contenu du message, il supprime ce message. Cela peut se produire si la personnalisation de la notification dans l’application génère une exception que nous intercepter dans le Kit de développement logiciel-déplacer le message. Cela peut également se produire si l’application sur l’appareil est une version du SDK Engagement Mobile qui n’est pas en mesure de comprendre la nouvelle version du message push envoyé à partir de la plateforme, mais il s’agit uniquement lorsque l’application a été mis à niveau après que la notification a été expédiée à partir de la plateforme de service. L’onglet **Avancé** vous indique le nombre de messages ont été supprimé. 
    4. Sur les appareils iOS, messages parfois ne pas remis au destinataire si soit l’appareil est sur batterie faible ou si l’application consomme une quantité importante de puissance lors du traitement des notifications à distance. Il s’agit d’une limitation des appareils iOS.   

3.  **Affiché** - Spécifie le nombre de messages qui s’affichent correctement à l’utilisateur d’application sur le périphérique sous la forme d’une notification push/hors-de-app système dans le centre de notification ou une notification dans l’application dans l’application mobile.  L’onglet **Avancé** vous signale combien ont été notifications système et combien ont été notifications dans l’application. 
    
    *Raisons pour affiché compter le nombre est inférieure à count remis (en attente d’être affichée)*
    
    1. Si la campagne notification avait une date de fin dessus, il est possible que la notification a été remise mais lorsque le temps a été fourni pour ouvrir et afficher à l’utilisateur de l’application, il a été déjà expiré afin qu’il a été affiche jamais.   
    2. Si la notification est une notification dans l’application la notification s’affiche uniquement lorsque l’utilisateur application ouvre l’application. Dans les cas où l’utilisateur de l’application n’a pas ouvert l’application, le Kit de développement signalera que la notification a été remise mais pas encore affichée jusqu'à ce que l’application est ouvert. 
    2. Si la notification est une notification dans l’application et configuré pour être affiché dans une activité spécifique/écran puis également la notification sera signalée comme remis mais ne pas encore remis jusqu'à ce que l’utilisateur ouvre l’application sur un écran spécifique. 
    
4.  **Interactions de l’utilisateur** - cette Spécifie le nombre de messages dont l’utilisateur de l’application a d’interagir avec incluent les messages qui sont activées ou terminé. 

    - *L’utilisateur de l’application peut action une notification d’une des façons suivantes :*
            
        1. Si la notification est une notification système/hors-de-app ou une notification dans l’application envoyé en notification seule puis l’utilisateur application clique sur la notification.
        2. Si la notification est une notification dans l’application avec un texte ou le mode web ou sondages l’utilisateur application clique sur le bouton d’Action dans la notification.
        3. Si la notification est une notification dans l’application présentant un affichage web, l’utilisateur application clique sur une URL dans l’affichage web [Android uniquement]
    
    - *L’utilisateur de l’application peut quitter une notification d’une des façons suivantes :*
    
        1. Cliquez sur le bouton Fermer la notification directement. 
        2. Balayant absent (e) ou la suppression de la notification. 
        3. Notifications dans l’application avec contenu texte/web et les sondages sont généralement affichées à l’utilisateur d’application dans une procédure. Ils visualiser d’abord une notification et lorsqu’ils cliquent sur celui-ci, verra pas la suite du contenu texte/web/sondage. L’utilisateur de l’application peut quitter une notification d’une de ces étapes et les détails dans la vue avancée capture ceci. 

5.  **Actioned** - Spécifie le nombre de messages qui ont été explicitement initié par l’utilisateur de l’application. Ceci est le nombre plus intéressant que cela indique le nombre d’utilisateurs application intéressé par le message repoussée dans la notification. 
 
> [AZURE.NOTE] Dans iOS et Windows ouvrir plateformes, si l’utilisateur dispose de l’application et la campagne a été une campagne « À tout moment », puis il est possible que les deux se déconnecter de l’application et dans l’application notifications sont affichés en même temps. Cela peut entraîner un compteur affiché supérieur à le remis. Si l’utilisateur interagit ou actions la notification, puis même le compte d’utilisateur Interactions/Actioned peut être supérieur à remis. 


![Reach2][19]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md
 
