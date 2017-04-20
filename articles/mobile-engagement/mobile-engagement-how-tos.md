<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - Reach comment"
   description="Présentation de l’Interface utilisateur pour Azure Engagement Mobile" 
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

# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Comment procéder à l’aide et la gestion des pousse d’accéder à vos utilisateurs finaux

Une fois que le Kit de développement est totalement intégré à votre application, vous pouvez commencer à utiliser la la section portée de l’interface utilisateur pour les notifications Push aux utilisateurs de votre application.  

## <a name="do-your-first-push-notification-campaign"></a>Effectuez votre première campagne de Notification Push
-    Vérifiez que votre présence est intégré à votre application avec le Kit de développement. 
-    Sélectionnez votre application
 
![First1][1]

-    Accédez à la Section « Portée », puis cliquez sur « nouvelle annonce »
 
![First2][2]

-    Créer une campagne et nommez-la
 
 ![First3][3]

-    Sélectionnez le mode de remise la notification, comme dans l’application uniquement
 
![First4][4]

-    Créer le message que vous souhaitez distribuer
 
![First5][5]

-    Vous pouvez écrire un titre de la notification (facultatif).
-    Rédiger un contenu push message.
-    Vous pouvez télécharger une image. N’oubliez pas que la taille du fichier ne peut pas dépasser 32 768 octets.
-    Vous avez également la possibilité de sélectionner des options supplémentaires, mais le focus de ce didacticiel, nous verra s’afficher qui ultérieurement.

-    Sélectionnez le type de contenu en tant que Notification uniquement
 
![First6][6]

-    Créer votre campagne push et il apparaîtra dans votre liste des campagnes.
 
![First7][7]

## <a name="test-your-push-notification-campaign"></a>Tester votre campagne de Notification Push
![Test1][8]

-    Inscrire votre appareil.
-    Cliquez sur la case à cocher de l’appareil que vous souhaitez distribuer.
-    Cliquez sur le bouton « Test » pour envoyer la diffusion à l’appareil.
 
![Test2][9]

-    Activer la campagne marketing
 
![Test3][10]

-    Maintenant que vous avez créé votre campagne il vous suffit d’activer la notification vers vos utilisateurs.
 
## <a name="send-personalized-pushes"></a>Envoyer pousse personnalisés
-    Cet exemple crée un push où un code personnalisé remise est entré dans la notification push.
 
![Personalize1][11]

Personnalisation works en remplaçant un marqueur en à partir d’une balise informations application ainsi, vous aurez pour vous assurer que l’utilisateur possède les informations de l’application appropriées définies en premier. Dans cet exemple, les utilisateurs ciblées auront une balise informations application nommée rebate_code définie.
Comme vous le voir au-dessus le contenu de notification push inclut le $ marqueur {rebate_code} qui indique qu’il doit être remplacée par le contenu de la balise informations application réel.

> Avertissement : Si la balise informations application n’est pas définie pour l’utilisateur, l’utilisateur ne recevrez pas la diffusion.

-    Résultat
 
![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>Vous pouvez personnaliser davantage le texte votre notification
![Personalize3][13]

-    Y compris le titre de la notification,
-    Et le contenu du message.
-    Choisissez le type d’annonce (affichage de texte ou en mode Web)
 
![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>Le corps d’une annonce peut également être personnalisé avec :
-    L’URL d’action, si vous souhaitez personnaliser la page d’accueil
-    Le titre
-    Le corps du message.
 
 
## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>Différencier votre Notification Push (dans ou déconnecter de l’application)
-    Choisissez le type de notification push, sélectionnez votre application, accédez à la section « Joindre », sélectionnez ou créer une campagne push et accédez à la section « Notification ».
 
-    Cliquez sur le mode de remise » » souhaitée.
-    Cliquez sur la case à cocher « Activités restreindre » lorsque vous souhaitez que la notification se produit sur activités spécifiques (écrans).

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>Mode de remise de « Déconnecter de l’application uniquement »
![Differentiate2][16]

« Se déconnecter de l’application » mode de remise fournit une notification push lors de l’application est fermée. Il s’agit de la notification push standard.
Lorsque vous sélectionnez « déconnecter de l’application uniquement », vous devez déjà avoir indiqué les certificats à partir de la plateforme que votre application crée sur (APNS ou GCM).

### <a name="see-also"></a>Voir aussi
-  [Service de Notification – certificats de Push Apple](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), Google Cloud messagerie – certificat] (http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>« dans l’application uniquement » mode de remise
![Differentiate3][17]

Mode de remise « Dans l’application uniquement » fournit une notification push lors de l’application est exécutée.
Pour cette notification, vous n’avez pas besoin accéder au sein du système APNS et GCM.
Vous pouvez utiliser le système de livraison dans l’application pour accéder à vos utilisateurs finaux.
Vous pouvez entièrement personnaliser la notification et décider d’apparition des activités (écran) la notification.

### <a name="anytime-delivery-mode"></a>Mode de remise « À tout moment »
Vous pouvez choisir un mode de remise « À tout moment », vous permet de conserver pour atteindre l’utilisateur final si l’application est exécutée ou non.
Lorsque vous sélectionnez « À tout moment », vous devez déjà avoir indiqué les certificats à partir de la plateforme que votre application est s’appuyant sur (APNS ou GCM). 
 
## <a name="schedule-a-push-campaign"></a>Planifier une campagne marketing Push
### <a name="plan-to-start-a-campaign"></a>Envisagez de commencer une campagne
![Shedule1][18]

Il est le 21 mars et vous disposez d’une annonce pour rendre et prévu pour le 22 mars à minuit. Vous n’êtes pas obligé de rester devant l’interface pour mener une campagne ! Vous pouvez de planifier à l’avance la minute exacte notifications sont envoyées.
-    Désactivez la case à cocher « Aucun » case à cocher, puis sélectionnez une heure de début 
-    Sélectionnez la date et l’heure que vous voulez commencer la campagne push.

### <a name="plan-to-end-a-campaign"></a>Planifier mettre fin à une campagne
![Shedule2][19]

Vous souhaitez votre campagne arrête le 25 mars au 15 h 00, mais vous savez que vous ne pouvez il y parvenir.
Vous n’êtes pas obligé de rester devant l’interface distribuer ! Vous pouvez de planifier à l’avance la minute exacte que votre campagne s’arrête.
-    Cliquez sur « Aucun » case à cocher ou sélectionner une heure de fin
-    Sélectionnez la date et l’heure que vous souhaitez terminer la campagne push.

### <a name="end-a-campaign-manually"></a>Mettre fin à une campagne manuellement
![Shedule3][20]

Par défaut, la « aucun » cases à cocher sont activées.
Cela signifie que la campagne commence dès que vous activez dans la section accessibles et se termine lorsque arrêtera la section accessibles.
 
> Remarque : Campagnes marketing créées sans date de fin stocke localement le push sur l’appareil et l’affiche la prochaine ouverture de l’application même si la campagne est terminée manuellement.

## <a name="enhance-a-push-notification-with-a-text-view"></a>Améliorer une Notification Push avec une vue de texte
### <a name="what-is-a-text-view"></a>Qu’est une vue de texte ?
![TextView1][21]

Une vue de texte est une fenêtre contextuelle avec du contenu de texte. Cette fenêtre contextuelle apparaît une fois que l’utilisateur final a cliqué sur les notifications.
Une vue de texte vous permet de présenter du contenu supplémentaire à l’utilisateur final. Il s’agit également la possibilité de présenter un appel à l’action tels que le passage à une page de votre application, rediriger vers un magasin, ouverture d’une page web, envoyer un message électronique, lancer une recherche localisées geo, etc....

### <a name="example-text-view"></a>Exemple : Affichage de texte
-    Créer votre campagne de notification Push dans la section « Portée » et donnez un nom à votre campagne
 
![TextView2][22]

-    Rédiger le message qui s’affichera sur la notification.
-    Sélectionnez le Type de contenu annonce de « texte »
 
![TextView3][23]

> Remarque : lorsque vous appuyez sur une vue de texte, il toujours est fourni avec une notification tout d’abord. 

- Définir le texte (après avoir sélectionné le contenu annonce du texte, la section apparaît, ce qui vous permet de définir le texte que vous souhaitez afficher.)
 
![TextView4][24]

-    Écrire le titre qui s’affichera en haut du message.
-    Rédiger le contenu principal de l’affichage de texte.
-    Rédiger le contenu qui s’affichera sur le bouton d’action (bouton d’action permet à l’application effectuer une action spécifique comme l’ouverture d’une page de l’application, rediriger vers un magasin d’applications ou tous les types de sources, que vous pouvez fournir).
-    Rédiger le contenu qui s’affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, l’affichage de texte disparaît.)
 
-    Créer votre campagne de notification push et il apparaîtra dans la liste des campagnes.
 
![TextView5][25]

-    Activer votre campagne de notification push pour envoyer l’affichage de texte à vos utilisateurs.
 
![TextView6][26]

-    Résultat
 
![TextView7][27]

-    L’utilisateur reçoit la notification, puis cliquez dessus.
-    L’affichage de texte apparaît sous la forme d’une fenêtre contextuelle permettant à l’utilisateur d’interagir avec lui.

## <a name="enhance-a-push-notification-with-a-web-view"></a>Améliorer une Notification d’émission à une page Web
### <a name="what-is-a-web-view"></a>Qu’est un affichage Web ?
![WebView1][28]

Un affichage web est une fenêtre contextuelle avec contenu web. Cette fenêtre contextuelle s’affiche lorsque l’utilisateur final a cliqué sur les notifications.
Un affichage web permet que vous ayez plus grande interaction avec l’utilisateur final.
Il s’agit également la possibilité de présenter un appel à l’action telles que la redirection à App Store, ouverture d’une page web, envoyer un message électronique, lancer une recherche localisées geo, etc....

### <a name="example-web-view"></a>Exemple : L’affichage Web
-    Créer votre campagne Push dans la section « Portée » et donnez un nom à votre campagne.
 
![WebView2][29]

-    Rédiger le message qui s’affichera sur la notification.
-    Sélectionnez le Type de contenu annonce comme « web »
 
![WebView3][30]

### <a name="about-announcement-types"></a>À propos des types d’annonce :
- Notification uniquement : il s’agit d’une notification standard simple. Ce qui signifie que si un utilisateur clique dessus, aucune vue supplémentaire ne s’affichera, mais uniquement l’action associée à celui-ci se produit.
- Annonce de texte : il s’agit d’une notification qui s’exécute automatiquement l’utilisateur d’un coup de œil à un affichage de texte.
- Annonce Web : il s’agit d’une notification qui s’exécute automatiquement l’utilisateur d’un coup de œil à un affichage web.
Sélectionnez le contenu de « Annonce Web ».

> Remarque : Lorsque vous appuyez sur un affichage web, elle toujours est fourni avec une notification tout d’abord.

- Définir le contenu web (après avoir sélectionné le contenu d’annonce web, la section apparaît, ce qui vous permet de définir l’afficher le contenu web que vous souhaitez afficher.)

 
![WebView4][31]

-    Écrire le titre qui s’affichera en haut du message (facultatif).
-    Écrire du code HTML ici.
-    Cliquez sur la bouton mode pour basculer edition et voir comment il ressemble d’édition de source.
-    Rédiger le contenu qui s’affichera sur le bouton d’action (bouton d’action permet à l’application effectuer une action spécifique comme l’ouverture d’une page de l’application, rediriger vers un magasin ou tous les types de sources, que vous pouvez fournir).
-    Rédiger le contenu qui s’affichera sur le bouton Quitter (en cliquant sur le bouton Quitter, l’affichage web disparaissent).
 
-    Résultat
 
![WebView5][32]

-    Les utilisateurs reçoivent une notification et cliquez dessus.
-    L’affichage de texte apparaît sous la forme d’une fenêtre contextuelle permettant à l’utilisateur d’interagir avec lui.

<!--Image references-->
[1]: ./media/mobile-engagement-how-tos/First1.png
[2]: ./media/mobile-engagement-how-tos/First2.png
[3]: ./media/mobile-engagement-how-tos/First3.png
[4]: ./media/mobile-engagement-how-tos/First4.png
[5]: ./media/mobile-engagement-how-tos/First5.png
[6]: ./media/mobile-engagement-how-tos/First6.png
[7]: ./media/mobile-engagement-how-tos/First7.png
[8]: ./media/mobile-engagement-how-tos/Test1.png
[9]: ./media/mobile-engagement-how-tos/Test2.png
[10]: ./media/mobile-engagement-how-tos/Test3.png
[11]: ./media/mobile-engagement-how-tos/Personalize1.png
[12]: ./media/mobile-engagement-how-tos/Personalize2.png
[13]: ./media/mobile-engagement-how-tos/Personalize3.png
[14]: ./media/mobile-engagement-how-tos/Personalize4.png
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png
[18]: ./media/mobile-engagement-how-tos/Schedule1.png
[19]: ./media/mobile-engagement-how-tos/Schedule2.png
[20]: ./media/mobile-engagement-how-tos/Schedule3.png
[21]: ./media/mobile-engagement-how-tos/TextView1.png
[22]: ./media/mobile-engagement-how-tos/TextView2.png
[23]: ./media/mobile-engagement-how-tos/TextView3.png
[24]: ./media/mobile-engagement-how-tos/TextView4.png
[25]: ./media/mobile-engagement-how-tos/TextView5.png
[26]: ./media/mobile-engagement-how-tos/TextView6.png
[27]: ./media/mobile-engagement-how-tos/TextView7.png
[28]: ./media/mobile-engagement-how-tos/WebView1.png
[29]: ./media/mobile-engagement-how-tos/WebView2.png
[30]: ./media/mobile-engagement-how-tos/WebView3.png
[31]: ./media/mobile-engagement-how-tos/WebView4.png
[32]: ./media/mobile-engagement-how-tos/WebView5.png

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
 
