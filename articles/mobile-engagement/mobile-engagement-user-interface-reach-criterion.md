<properties 
   pageTitle="Interface utilisateur de Azure Engagement Mobile - Reach critère" 
   description="Découvrez comment utiliser des critères de ciblage pour envoyer des campagnes push à sélectionner un sous-ensemble de vos utilisateurs à l’aide d’Azure Mobile Engagement" 
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


# <a name="how-to-use-targeting-criteria-to-send-push-campaigns-to-a-select-subset-of-your-users"></a>Comment utiliser des critères de ciblage pour envoyer des campagnes push pour sélectionner un sous-ensemble de vos utilisateurs

Ciblage de votre public selon des critères spécifiques avec le bouton « Autres critères » est un des concepts plus puissantes dans Azure Mobile Engagement vous aide à vous envoyez pertinents des notifications de transmission que les clients répondra au lieu de spam tout le monde. Vous pouvez limiter votre audience en fonction de critères standards et simuler push pour déterminer le nombre de personnes qui reçoivent une notification.

**Voir aussi :**

- [Interface utilisateur Documentation - Reach - nouvelle Push campagne][Link 27]

## <a name="audience-criteria-can-include"></a>Critères audience peuvent inclure :
- **Technicals :** Vous pouvez cibler en fonction des informations techniques mêmes vous pouvez voir dans les sections Analytique et un moniteur. **Voir aussi :** [Documentation de l’interface utilisateur - Analytique] [ Link 15], [L’interface utilisateur Documentation - moniteur][Link 16]
- **Emplacement :** Applications qui utilisent « reporting sur l’emplacement en temps réel » avec Geo clôture permet géolocalisation comme critère de ciblez une audience à partir de l’emplacement GPS. « Jattes zone emplacement Reporting » appel également être utilisées pour ciblez une audience à partir de l’emplacement des téléphones portables (« en temps réel emplacement rapports » et « Jattes zone Reporting sur l’emplacement » doivent être activé depuis le Kit de développement). **Voir aussi :** [Documentation SDK - iOS - intégration] [ Link 5], [SDK Documentation - Android - intégration][Link 5]
- **Atteigne commentaires :** Vous pouvez cibler votre audience en fonction de leurs commentaires à partir de précédent notifications accessibles via commentaires accessibles à partir des annonces, les sondages et envoie des données. Cela permet de mieux cibler votre public après deux ou trois communiquer aux campagnes que vous pourriez la première fois. Il peut également être utilisé pour filtrer les utilisateurs qui ont déjà reçu une notification ayant un contenu similaire, en définissant une campagne ne pas soient envoyées aux utilisateurs qui ont déjà reçu une campagne précédente spécifique. Vous pouvez même permet d’exclure les utilisateurs qui sont inclus une campagne spécifique est toujours active de recevoir des campagnes de nouveau. **Voir aussi :** [Documentation de l’interface utilisateur - atteigne - transmission contenue][Link 29]
- **Installer suivi :** Vous pouvez effectuer le suivi des informations basées sur lequel vos utilisateurs installé votre application. **Voir aussi :** [Documentation de l’interface utilisateur - paramètres][Link 20]
- **Profil utilisateur :** Vous pouvez cibler standard en fonction des informations utilisateur et que vous pouvez cible basée sur les informations d’application personnalisée que vous avez créé. Cela inclut les utilisateurs qui ne sont actuellement connectés et les utilisateurs qui ont des questions spécifiques que vous avez demandé les définir dans l’application elle-même au lieu de simplement comment ils ont répondu aux campagnes précédents. Toutes vos informations de l’application défini pour votre application s’affichent dans cette liste.
- Segments : Vous pouvez également cible basée sur les segments que vous avez créé basée sur le comportement d’utilisateur spécifique contenant plusieurs critères. Tous vos segments définis pour votre application apparaissant sur cette liste. **Voir aussi :** [Documentation de l’interface utilisateur - Segments][Link 18]
- **Informations application :** Balises d’informations personnalisées application peut être créés à partir de « Paramètres » pour suivre le comportement de l’utilisateur. **Voir aussi :** [Documentation de l’interface utilisateur - paramètres][Link 20]

## <a name="example"></a>Exemple : 
Si vous souhaitez distribuer une annonce uniquement à l’ensemble de vos utilisateurs que vous avez effectué une action dans l’application achat sous-adresse.

1. Accédez à votre page de paramètres d’application et sélectionnez le menu « App info » « Nouveau app info »
2. Enregistrer une nouvelle info application booléenne appelée « inAppPurchase »
3. Rendre votre application définir les informations de cette application « True » lorsque l’utilisateur exécute correctement un achat dans l’application (à l’aide de la sendAppInfo (« inAppPurchase »,...) fonction)
4. Si vous ne voulez pas le faire à partir de votre application, vous pouvez le faire à partir de votre serveur principal à l’aide de l’API de l’appareil)
5. Ensuite, vous devez simplement créer votre annonce, avec un critère de limitation de votre public pour les utilisateurs possédant « inAppPurchase » est défini sur « true »)
 
> Remarque : Cibler en fonction de critères différent de balises d’informations application nécessite Azure Mobile Engagement recueillir des informations à partir d’appareils de vos utilisateurs avant l’envoi de la diffusion et peut provoquer un délai. Configuration complexe push options (par exemple, mise à jour des touches accélératrices) peuvent également retarder pousse. À l’aide d’une campagne « une seule fois » à partir de l’API Push est la méthode la plus rapide absolue push dans Azure Mobile Engagement. En utilisant uniquement les balises application informations comme critères de push pour une campagne portée (soit à partir de l’API atteigne ou de l’interface utilisateur) est la méthode la plus rapide suivante dans la mesure où l’application informations balises sont enregistrées sur le côté serveur. À l’aide d’autres critères de ciblage pour une campagne push est la méthode push plus flexible mais plus lente Azure Mobile Engagement ayant interroger les appareils afin de pouvoir pour envoyer la campagne.
 
![Portée Criterion1][29] 

## <a name="criterion-options-apply-to"></a>Options de critère s’appliquent à :
- **Technicals**     
- Nom du microprogramme : nom du microprogramme
- Version du microprogramme : version du logiciel
- Modèle d’appareil : modèle d’appareil
- Fabricant : fabricant du périphérique
- Version de l’application : version de l’Application
- Nom de l’opérateur : nom de transport, non défini
- Pays Carrier : pays Carrier, non défini
- Type de réseau : type de réseau
- Paramètres régionaux : paramètres régionaux
- Taille de l’écran : taille de l’écran
- **Emplacement**      
- Dernière connue zone : pays, région, localisation
- En temps réel geo-clôture : liste de POIs (nom, Actions), circulaire vers (nom, Latitude, Longitude, Radius en mètres)
- **Atteindre les commentaires**     
- Commentaires annonce : annonce, commentaires
- Interroger des commentaires : sondage, commentaires
- Interroger answer commentaires : interroger des commentaires de réponse, question, choix
- Commentaires Push de données : données Push, commentaires
- **Installer le suivi**     
- : Un magasin, non défini
- Source : Source, non définie
- **Profil utilisateur**     
- Sexe : homme ou femme, non défini
- Date de naissance : opérateur, date, non défini
- Désactiver cette connexion : vrai ou faux, non défini
- **Informations de l’application**      
- Chaîne : Chaîne non défini
- Date : opérateur, date, non définie
- : Opérateur nombre entier, non défini
- Valeur booléenne : vrai ou faux, non défini
- **Segment**    
- Nom de Segments (à partir de liste déroulante), Exclusion (utilisateurs cible qui ne font pas partie de ce segment).

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
 
