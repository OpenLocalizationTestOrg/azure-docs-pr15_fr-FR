<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - Reach campagne" 
   description="Laern comment faire pour créer et gérer les notifications push campagnes à l’aide d’Azure Mobile Engagement" 
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


# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Comment créer et gérer des campagnes de notification push
Vous pouvez utiliser la section portée de l’interface utilisateur pour créer une campagne Push avec une formule complexe en fournissant toutes les informations que nécessaires pour envoyer une notification push. Les options d’une campagne Push varient légèrement selon les types de quatre campagne : annonces, les sondages, données pousse et mosaïques (Windows Phone uniquement).

### <a name="option-applies-to"></a>Option s’applique à :
- Langues : Toutes les (annonces, sondages, données push, mosaïques)
- Campagne : Toutes les (annonces, sondages, données push, mosaïques)
- Notification : Annonces, des sondages
- Content : Unique pour chaque type de campagne
- Public : Tous les (annonces, sondages, données push, mosaïques)
- Intervalle de temps : annonces, sondages, mosaïques
- Test : Toutes les (annonces, sondages, données push, mosaïques)
 
![Portée Campaign1][20]

## <a name="languages"></a>Traduction
Vous pouvez utiliser le menu déroulant langues pour envoyer une autre version de votre Push aux périphériques qui sont configurés pour utiliser différentes langues. Par défaut, tous les périphériques recevront le lancement même quelle que soit la langue qu’ils sont définis pour utiliser. Les utilisateurs avec leur appareil défini sur une autre langue recevront la version linguistique par défaut de la diffusion. De nombreuses options de campagne push permettent de spécifier un contenu de remplacement pour chacune des langues supplémentaires que vous sélectionnez. 
 
![Portée Campaign2][21]

### <a name="language-differences-apply-to"></a>Différences entre les langages s’appliquent à :
- Langues : Langues Unique peuvent être sélectionnées en plus de la langue par défaut
- Campagne : Identique pour toutes les langues
- Notification : Unique pour chaque langue en plus de la langue par défaut
- Content : Unique pour chaque langue en plus de la langue par défaut
- Public : Peut être filtrée par un critère de langage distinct
- Intervalle de temps : même pour toutes les langues
- Test : Peuvent être envoyés à chaque langue à la fois
 
### <a name="supported-languages"></a>Langues prises en charge :
- Arabe (ar) 
- Bulgare (bg) 
- Catalan (ca) 
- Chinois (zh) 
- Croate (h) 
- Czech (cs) 
- Danois (da) 
- Néerlandais (en naturel) 
- Anglais (US) 
- Finnois (fi) 
- Français (fr) 
- Allemand (de) 
- Grec (el) 
- Hébreu (he) 
- Hindi (Bonjour) 
- Hongrois (HOU) 
- Indonésien (id) 
- Italien (it) 
- Japonais (ja) 
- Coréen (ko) 
- Letton (lv) 
- Lituanien (lt) 
- Malais (macrolanguage) (ms) 
- Norvégien (Bokmål) (nb) 
- Polonais (pl) 
- Portugais (Portugal) 
- Roumain (ligne) 
- Russe (demandeur) 
- Serbe (sr) 
- Slovaque (sk) 
- Slovène (sl) 
- Espagnol (es) 
- Suédois (VP) 
- TAGALOG (tl) 
- Thaï (l) 
- Turc (tr) 
- Ukrainien (Royaume-Uni) 
- Vietnamien (vi) 
 
## <a name="campaign"></a>Campagne marketing
Vous pouvez utiliser la section campagne pour définir le nom et la catégorie de votre campagne ainsi que si vous prévoyez d’ignorer la section audience d’une campagne Push et envoyer cette campagne via l’API atteigne (et certains éléments avec le niveau faible transmission API) à la place. Catégories peuvent être utilisées avec un modèle de notification personnalisé pour les notifications de dans l’application de contrôle en fonction des paramètres prédéfinis. Vous pouvez obtenir une liste de vos « catégories » via l’API atteindre.

> Avertissement : Si vous utilisez l’option « Ignorer l’Audience, push sera envoyé aux utilisateurs via l’API » dans la section « Campagne » d’une campagne accessibles, la campagne vous envoie pas automatiquement, vous devez envoyer manuellement via l’API atteindre.
 
![Portée Campaign3][22]
 
### <a name="option-applies-to"></a>Option s’applique à :
- Nom : toutes les
- Catégorie : Annonces, des sondages
- Ignorer l’Audience, push sera envoyé aux utilisateurs via l’API : toutes les
 
## <a name="notification"></a>Notification
Vous pouvez utiliser la section Notification pour définir des paramètres de base de votre push, notamment : le titre de la diffusion, le message, une image intégrée dans l’application, ou s’il s’agit Halo. Paramètres de notification de nombreux sont spécifiques à la plateforme de votre appareil. Vous pouvez choisir votre push sera envoyé « dans l’application » ou « déconnecter de l’application » ou les deux. (N’oubliez pas que les utilisateurs peuvent « participer » ou « annulations « de « se déconnecter de l’application » pousse au niveau du système d’exploitation niveau sur leurs appareils, et Azure Mobile Engagement ne pourront pas remplacer ce paramètre. N’oubliez pas également que l’API atteigne gère « dans l’application » et « hors de l’application » pousse. L’API Push peut être utilisé pour gérer « déconnecter de l’application » pousse trop.) Push peut être personnalisée avec des images ou du contenu HTML, y compris les liens profondes pour la liaison en dehors de votre application ou vers un autre emplacement dans votre application (SDK Android 2.1.0 ou version ultérieure catégories intention requis). Vous pouvez modifier le badge icône ou iOS et envoyer du contenu de texte ou web (une fenêtre contextuelle avec html contenu, lien URL vers un autre emplacement à l’intérieur ou en dehors de l’application). Vous pouvez également effectuer simultanée appareils Android ou vibrations avec la diffusion. (N’oubliez pas que vous devez le bon fichier pour sonner ou vibrations un appareil manifeste autorisations SDK dans votre téléphone Android.) Il n’existe actuellement aucune industrie standard pour les formats de Android « vue d’ensemble », dans la mesure où les tailles écran sont différentes sur chaque appareil, mais 400 x 100 images fonctionnent sur presque n’importe quelle taille de l’écran.

### <a name="delivery-types"></a>Types de remise :
-    Déconnecter de l’application uniquement : la notification est envoyée lorsque l’utilisateur n’utilise pas l’application.
- L’extérieur de la notification seule application nécessite un certificat à partir d’Apple ou Google (certificat APNS ou GCM).
- Dans l’application uniquement : la notification s’affiche uniquement lorsque l’application est exécutée.
- La notification utilise le système de livraison Capptain pour joindre l’utilisateur. Vous pouvez entièrement personnaliser la mise en page/Affichage visuel de votre push.
- À tout moment : Cette option garantit que vous envoyez une notification que soit l’application est en cours d’exécution ou non.

 
![Portée Campaign4][23]

### <a name="option-applies-to"></a>Option s’applique à :
- Notification : Annonces, des sondages
 
## <a name="content"></a>Contenu
Vous pouvez utiliser la section du contenu pour modifier le contenu de vos annonces, sondages, données pousse et mosaïques (Windows Phone uniquement). Le paramètre contenu des campagnes Push est spécifique au type de campagne. 

### <a name="see-also"></a>Voir aussi
- [Documentation de l’interface utilisateur - atteigne - transmission du contenu][Link 29]
 
![Portée Campaign5][24]

## <a name="audience"></a>Audience
Vous pouvez utiliser la section Audience pour définir une liste d’éléments dans votre campagne ou les limites de votre campagne en fonction de critères personnalisés standard. Le jeu d’options permettant de limiter votre public standard vous permet de transmission aux utilisateurs de nouveau ou anciens ou push natif uniquement. Vous pouvez également définir un quota pour limiter le nombre d’utilisateurs qui reçoivent la diffusion. Vous pouvez modifier manuellement l’expression pour la façon dont votre campagne est filtrée pour inclure un ou plusieurs critères pour cibler les utilisateurs. Vous pouvez taper manuellement une expression audience. Une telle expression doit définir explicitement la relation entre les critères. Un critère est décrite par un identificateur qui doit commencer par une lettre majuscule et ne peut pas contenir d’espaces. La relation entre les critères pouvant être décrite par « et », « ou », « non » les opérateurs ainsi que '(',')'. Exemple : « Criterion1 ou (Criterion1 et non Criterion2) ».

> Remarque : Avec un large public inclus dans les campagnes, côté serveur ciblage analyse peut être lent, surtout si vous essayez de démarrer plusieurs campagnes en même temps.

- Si possible, que démarrez une campagne marketing à la fois.
- Ne démarrer au maximum, quatre campagnes à la fois.
- Notifications push uniquement à vos utilisateurs actifs (case à cocher « engager uniquement les utilisateurs sont accessibles à l’aide de Push Native » et « Engager uniquement les utilisateurs actifs ») afin que seuls vos utilisateurs qui toujours ont installé l’application et utilisent auront besoin à analyser.
Une fois que votre public est définie, vous pouvez utiliser le bouton simuler pour déterminer le nombre d’utilisateurs recevront cette Push. Cela sera calculer le nombre d’utilisateurs connus potentiellement ciblées par cette audience (il s’agit d’une estimation basée sur un échantillon aléatoire des utilisateurs). N’oubliez pas que les utilisateurs qui ont désinstallé l’application sont également partie de cette audience, mais ne peuvent pas être atteintes.

### <a name="see-also"></a>Voir aussi
- [Interface utilisateur Documentation - Reach - nouveau Push critère][Link 28]

![Portée Campaign6][25]

### <a name="edit-expression"></a>Modifier l’expression
![Portée Campaign7][26]
 
### <a name="limit-your-audience-option-applies-to"></a>Limite de : que l' option de l’audience s’applique :
- Engager uniquement un sous-ensemble d’utilisateurs : toutes les (annonces, sondages, données pousse, mosaïques)
- Engager seuls les utilisateurs anciens : toutes les (annonces, sondages, données pousse, mosaïques)
- Engager seuls les nouveaux utilisateurs : toutes les (annonces, sondages, données pousse, mosaïques)
- Engager uniquement les utilisateurs inactifs : annonces, sondages, mosaïques
- Engager uniquement les utilisateurs actifs : toutes les (annonces, sondages, données pousse, mosaïques)
- Engager uniquement les utilisateurs sont accessibles à l’aide de transmission Native : annonces, des sondages
 
## <a name="time-frame"></a>Intervalle de temps
Vous pouvez utiliser la section intervalle de temps pour définir lorsque le push sera envoyé ou vous pouvez laisser l’intervalle de temps vide pour lancer la campagne immédiatement. N’oubliez pas que fuseau horaire les utilisateurs finaux peut-être commencer à la campagne un jour plus tôt que vous prévoyez pour vos utilisateurs finaux en Asie et envoyer des petits lots de pousse à la fois jusqu'à ce que tous les fuseaux horaires dans le monde correspondent à l’intervalle de temps défini pour votre campagne. Fuseau horaire de l’utilisateur final peut également entraîner retards dans les campagnes car l’option demander l’heure à partir du téléphone avant de démarrer la diffusion.

> Remarque : Campagnes sans une date de fin peut mettre en cache pousse localement et toujours affiche les après avoir achevé manuellement campagnes. Pour éviter ce problème, spécifique à une heure de fin pour les campagnes marketing.

### <a name="see-also"></a>Voir aussi
- [Atteindre - comment OT – planification][Link 3] 
 
![Portée Campaign8][27]

### <a name="settings-apply-to"></a>Paramètres s’appliquent à :
- Intervalle de temps : annonces, sondages, mosaïques
 
## <a name="test"></a>Test
Vous pouvez utiliser la section Test pour envoyer cette push à votre propre périphérique de test avant d’enregistrer la campagne. Si vous avez configuré les langues personnalisées pour cette campagne marketing, vous pouvez tester le push dans chaque langue. Vous pouvez configurer un appareil de test de « Mon compte ».
> Remarque : Aucun côté serveur les données sont enregistrées lorsque vous utilisez le bouton « tester » ne pousse, les données sont enregistrées uniquement pour les campagnes push réel.

### <a name="see-also"></a>Voir aussi
- [Documentation de l’interface utilisateur - mon compte][Link 14]
 
![Portée Campaign9][28]

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
 
