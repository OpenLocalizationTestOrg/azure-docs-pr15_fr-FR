<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - contenu accessibles" 
   description="Découvrez comment gérer le contenu des différents types de campagnes de notification push dans Azure Mobile Engagement unique" 
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

# <a name="how-to-manage-the-unique-content-of-the-different-types-of-push-notification-campaigns"></a>Comment gérer le contenu des différents types de campagnes de notification push unique
 
Vous pouvez utiliser la section contenue d’une nouvelle campagne accessibles pour modifier le contenu de vos annonces, sondages, données pousse et mosaïques (Windows Phone uniquement). Le paramètre contenu des campagnes Push est spécifique au type de campagne. 
 
### <a name="content-types"></a>Types de contenu :
- Annonces
- Sondages
- Données push
- Vignettes (Windows Phone uniquement)
 
## <a name="content-of-announcements"></a>Contenu des annonces
 ![Portée Content1][30] 

### <a name="choose-the-type-of-your-announcement"></a>Choisissez le type de votre annonce :
-    Notification uniquement : il s’agit d’une notification standard simple. Ce qui signifie que si un utilisateur clique dessus, aucune vue supplémentaire ne s’affichera, mais uniquement l’action associée à celui-ci se produit.
-    Annonce de texte : il s’agit d’une notification qui s’exécute automatiquement l’utilisateur d’un coup de œil à un affichage de texte.
-    Annonce Web : il s’agit d’une notification qui s’exécute automatiquement l’utilisateur d’un coup de œil à un affichage web.

### <a name="see-also"></a>Voir aussi
- [Atteindre - comment OT - annonces][Link 3] 

### <a name="about-web-view-announcements"></a>Informations sur les annonces d’affichage Web :
Occurrences du modèle « {ID de périphérique} » dans le code HTML ou le code JavaScript que vous fournissez ici seront automatiquement remplacés par l’identificateur de l’appareil affichant l’annonce. Il s’agit d’un moyen facile de récupérer les identificateurs de périphérique Azure Mobile Engagement dans un service web externe hébergés sur votre bureau précédent.
Si vous souhaitez créer un affichage web en mode plein écran (sans les boutons Action et de sortie par défaut nous fournissons) vous pouvez utiliser les fonctions suivantes à partir du code JavaScript de votre annonce affichage web : 

-    exécuter l’action annonce : ReachContent.actionContent()
-    quitter l’annonce : ReachContent.exitContent()
 
### <a name="choose-your-action"></a>Sélectionner une Action :

### <a name="about-action-urls"></a>À propos des URL d’Action :
Les URL qui peut être interprété par le système d’exploitation d’un périphérique ciblée peut être utilisée comme une URL de l’action.
Dédiés URL que votre application peut prendre en charge (par exemple, pour que les utilisateurs à accéder à un écran donné) peut également être utilisé comme URL d’action.
Chaque occurrence du modèle {ID de périphérique} est automatiquement remplacé par l’identificateur de l’appareil exécute l’action. Cela peut servir à récupérer facilement les identificateurs de périphérique Azure Mobile Engagement via un service web externe hébergés sur votre bureau précédent.

- **Android + iOS actions**
    - Ouvrez une page web
    - http://\[domaine du site web\] 
    - Exemple : http://www.azure.com
    - Envoyer un message électronique
    - mailto :\[destinataire de courrier électronique\]?subject =\[objet\]& corps =\[message\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS
    - SMS :\[numéro de téléphone\] 
    - Exemple : sms:2125551212
    - Composer un numéro de téléphone
    - protocole :\[numéro de téléphone\] 
    - Exemple : tel:2125551212
- **Seuls les actions Android**
    - Télécharger une application dans le magasin de lecture
    - Market://details?id=\[package d’application\] 
    - Exemple : market://details?id=com.microsoft.office.word
    - Démarrez une recherche localisées geo
    - geo:0, 0?q =\[requête de recherche\] 
    - Exemple : geo:0, 0 ? q = starbucks, paris
- **Seuls les actions iOS**
    - Télécharger une application sur l’App Store
    - http://iTunes.Apple.com/ [pays] /app/ [nom de l’application] /id [id application] ? mt = 8 
    - Exemple : http://itunes.apple.com/fr/app/briquet-virtuel/id430154748?mt=8
    - Actions de Windows
    - Ouvrez une page web
    - http://\[domaine du site web\] 
    - Exemple : http://www.azure.com
    - Envoyer un message électronique
    - mailto :\[destinataire de courrier électronique\]?subject =\[objet\]& corps =\[message\] 
    - Example:mailto:foo@example.com?subject=Greetings%20from%20Azure%20Mobile%20Engagement!&body=Good%20stuff!
    - Envoyer un SMS (application du Store Skype obligatoire)
    - SMS :\[numéro de téléphone\] 
    - Exemple : sms:2125551212
    - Composer un numéro de téléphone (application du Store Skype obligatoire)
    - protocole :\[numéro de téléphone\] 
    - Exemple : tel:2125551212
    - Télécharger une application dans le magasin de lecture
    - MS-windows-store : PDP?PFN =\[ID de package d’application\] 
    - Exemple : ms-windows-store : déclenchement ? PFN = 4d91298a-07cb-40fb-aecc-4cb5615d53c1
    - Démarrez une recherche bingmaps
    - bingmaps:?q =\[requête de recherche\] 
    - Exemple : bingmaps : ? q = starbucks, paris
    - Utiliser un modèle personnalisé
    - \[modèle personnalisé\]://\[paramètres de couleurs personnalisé\] 
    - Exemple : myCustomProtocol://myCustomParams
    - Utiliser une package des données (application du Store pour le poste lire obligatoire)
    - \[dossier\]\[données\]. \[extension\] 
    - Example:myfolderdata.txt
 
### <a name="build-a-tracking-url"></a>Générer une URL de suivi :
-    Consultez la section « Paramètres » de la <UI Documentation> pour obtenir des instructions sur la création d’une URL de suivi qui permettra aux utilisateurs de télécharger l’un de vos autres applications.
 
### <a name="define-the-texts-of-your-announcement"></a>Définir les textes de votre annonce
Renseignez le titre, le contenu et les textes de bouton de votre annonce. Vous pouvez cibler un public allant d’une campagne future en fonction du compte-rendu portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage d’audience peut être en fonction du compte-rendu de si cette campagne a été simplement poussée, réponse, actionnées ou terminé.

### <a name="see-also"></a>Voir aussi
- [Interface utilisateur Documentation - Reach - nouveau Push critère][Link 28]

## <a name="content-of-polls"></a>Contenu des sondages
![Portée Content2][31] renseignez le titre, description et les textes de bouton de votre annonce. Ensuite, ajoutez des questions et des choix pour les réponses à vos questions.
Vous pouvez cibler un public allant d’une campagne future en fonction du compte-rendu portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage d’audience peut basé sur si cette campagne a été simplement poussée, réponse, actionnées ou terminé. Ciblage d’audience peut également être basé les commentaires des réponses sondage, où la question et réponse choix sont utilisés comme critères.

### <a name="see-also"></a>Voir aussi
- [Interface utilisateur Documentation - Reach - nouveau Push critère][Link 28]
 
## <a name="content-of-data-pushes"></a>Contenu de données pousse
![Portée Content3][32] 

### <a name="choose-the-type-of-your-data"></a>Choisissez le type de données :
- Texte
- Données binaires
- Données en base 64

### <a name="define-the-content-of-your-data"></a>Définir le contenu de vos données
- Si vous avez sélectionné transmission des données de texte, copiez et collez le texte dans la zone « contenu ».
- Si vous avez sélectionné pour envoyer des données binaires ou en base 64, utilisez le bouton « Télécharger votre fichier » pour télécharger votre fichier.
- Vous pouvez cibler un public allant d’une campagne future en fonction du compte-rendu portée de la façon dont les utilisateurs répondu à cette campagne. Ciblage d’audience peut basé sur si cette campagne a été simplement poussée, réponse, actionnées ou terminé.

### <a name="see-also"></a>Voir aussi
- [Interface utilisateur Documentation - Reach - nouveau Push critère][Link 28]

## <a name="content-of-tiles-windows-phone-only"></a>Contenu des vignettes (Windows Phone uniquement)
![Portée Content4][33]

### <a name="define-the-content-of-your-tile"></a>Définir le contenu de la mosaïque
La charge utile vignette est le texte à afficher dans la vignette de votre application sur les appareils Windows Phone.
Une vignette push est la version de Service de Notification Push Microsoft (MPNS) d’un native push pour Windows Phone. Le type push vignette est le seul type push qui n’a pas de réponse, et donc l’audience des campagnes futures ne peut pas être générée sur les résultats d’une campagne push vignette. 

### <a name="see-also"></a>Voir aussi
- [API Documentation - Reach API - Native Push][Link 4]

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
 
