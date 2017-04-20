<properties
   pageTitle="Interface utilisateur de Azure Engagement Mobile - Analytique"
   description="Apprenez à analyser les données historiques relatives à votre application à l’aide d’Azure Mobile Engagement"
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

# <a name="how-to-analyze-historical-data-about-your-application"></a>Comment faire pour analyser les données historiques relatives à votre application

Cet article décrit l’onglet **analyse** du portail **Mobile Engagement** . Le portail **d’Engagement Mobile** vous permet de contrôler et de gérer vos applications mobiles. Notez que pour commencer à utiliser le portail vous devez tout d’abord créer un compte **Azure Mobile Engagement** .


La section Analytique de l’interface utilisateur fournit des informations agrégées sur votre application basée sur des données historiques sont mis à jour toutes les 24 heures. Les informations sont affichées dans différents tableaux de bord composé de cartes, des quadrillages et graphiques en courbes/barre/secteurs. Les données peuvent également être téléchargées en tant que fichiers .csv. La plupart de ces informations sont disponibles en temps réel dans la section de l’écran de l’interface utilisateur, et il peut également accéder à l’API Analytique.

>[AZURE.NOTE] Nombre de sections du portail **Mobile Engagement** interface utilisateur contiennent le bouton **Afficher l’aide** . Appuyez sur ce bouton pour obtenir plus d’informations contextuelles sur une section.

## <a name="standard-and-custom-analytics"></a>Analytique standard et personnalisés

Azure Engagement Mobile fournit un ensemble de base informations standards analytiques sur vos applications qui peut être représentée sous forme graphique dès que vous intégrez votre application avec le Kit de développement. Azure Engagement Mobile permet également de recueillir des informations supplémentaires analytique personnalisé que vous voulez sur le comportement de vos utilisateurs finaux. Vous pouvez le faire en créant un plan de balise de « Balises personnalisées (app info) », créé à partir des **paramètres** de sorte que Azure Mobile Engagement recueille ces données supplémentaires pour vous.



## <a name="analytics"></a>Analytique
- Tableau de bord : Affiche des informations générales sur vos utilisateurs nouvelles et actives et leurs tendances.
- Utilisateurs : Les utilisateurs sont identifiés par leur identificateur d’appareil : cet identificateur est unique pour chaque appareil (un nouvel utilisateur est en réalité un nouvel appareil). Un utilisateur est considéré comme un nouveau sur un intervalle de temps donné si il a effectué sa première session pendant cet intervalle de temps. Un utilisateur est considéré comme conservé si tel qu’il a effectué au moins une session au cours des 7 derniers jours. Utilisateurs actifs sont ceux qui a créé au moins une session pendant une période donnée. Vous pouvez trier par, mensuelle, hebdomadaire, quotidien ou toutes les heures des périodes de temps. Tous les graphiques similaires, mais vous permettent de filtrer par des fonctionnalités, telles que la version de votre application, puis sur Trier par une période donnée. Les informations standards collectées par intégration le Kit de développement incluent les actions suivantes : utilisateurs actifs, nouvel utilisateur, le nombre de sessions et durée de chaque session, les informations techniques sur les pays, variables locales, emplacement, opérateur de langue, appareils, microprogramme, réseau (Wi-Fi), les versions de l’application et le Kit de développement logiciel, utilisé par les clients. Ces informations peuvent être affichées en temps réel à partir de la section moniteur.

> Remarque : La période de temps est basée sur la date à partir des paramètres de périphérique de l’utilisateur, un utilisateur dont téléphone est incorrectement défini pour la date peut s’affiche pas dans la période de temps incorrecte.

- Rétention : Un utilisateur est considéré comme conservées sur un intervalle de temps donné si il a effectué sa première session pendant cet intervalle de temps. Vous pouvez modifier les intervalles de temps pendant lequel les utilisateurs conservés (et nouveaux utilisateurs) sont prises en compte pour les heures, jours, semaines ou mois. L’analytique de rétention utilisateur repose sur cohortes. Un cohorte est un ensemble de tous les nouveaux utilisateurs détecté pour une période donnée (par exemple, l’ensemble des utilisateurs effectuent leur première session pendant cette période). Nous utilisons cohortes de jours 1, 2 jours, 4 jours, 7 jours ou 1 mois. Étant donné un cohorte, chaque jour 1, 2 jours, 4 jours, 7 jours, ou 1 mois, Azure Engagement Mobile calcule le jeu de tous les utilisateurs qui appartiennent à la cohorte et sont toujours actif (c'est-à-dire, l’ensemble d’utilisateurs ayant effectué au moins une session pendant la période). Cet ensemble d’utilisateurs s’appelle une version cohorte. (Engagement Mobile azure peut vous indiquer le nombre de vos utilisateurs est toujours à l’aide de votre application, mais uniquement le magasin spécifique plateforme peut vous indiquer le nombre de vos utilisateurs désinstallé votre application - par exemple, GooglePlay, iTunes, du Windows Store, etc..).
- Sessions : Une utilisation de l’application par un utilisateur. Sessions sont générées à partir de la séquence d’activités effectuées par les utilisateurs (une activité est généralement associée à l’utilisation d’un écran de l’application, mais cela peut varier selon la façon dont le Kit de développement a été intégré dans l’application). Un utilisateur peut effectuer uniquement une activité à la fois : une session débute dès que l’utilisateur démarre son activité première et s’arrête lorsqu’il termine à sa dernière activité. Si un utilisateur reste plus de quelques secondes sans activité, sa séquence d’activités est divisée en deux sessions distinctes.
- Activités : Les noms de chaque écran dans votre application et la longueur des utilisateurs heure consacrent à chaque écran. Les activités sont une option analytique personnalisée qui correspondre aux balises « app info » que vous avez configuré votre propre application :
- Chemin d’accès de l’utilisateur : Montre comment vos utilisateurs naviguer dans les activités de votre application (écrans). Vous pouvez déplacer le curseur pour régler le niveau de détails. Les nœuds bleus représentent des activités de votre application. Leur taille est proportionnelle à la fois les utilisateurs passées qu’il contient. Nœuds blancs représentent arrêter et démarrage d’une session. Les nœuds rouge représentent se bloque. Les liens représentent des transitions entre les activités de votre application (ou entre les activités et incidents). Cliquez sur un nœud ou un lien pour afficher une info-bulle avec plus d’informations sur vos données : le temps passé dans un écran particulier, le nombre de transitions et le pourcentage de transition de l’activité de source à l’activité de destination. (Une---60 %---> B signifie que les utilisateurs sur activité A accède à activité B 60 % du temps.) Vous pouvez réorganiser le graphique que vous voulez clarifier sa position est enregistrée chaque fois que vous apportez des modifications. Vous pouvez afficher ou masquer les blocages pour éclaircir le graphique.
- Événements : Actions spécifiques effectuées par un utilisateur dans l’application. La distribution des événements est affichée en tant que le nombre d’événements par utilisateur et par session. Un événement représente une action instantanée, par exemple, un clic sur un bouton ou la réception d’une notification. (La signification des événements dépend comment le Kit de développement a été intégré dans l’application.) Un événement peut se produire pendant une session ou d’une tâche ou peut être autonome.
- Tâches : Semblables aux événements à ceci près qu’ils se concentrent sur la longueur de l’action. Par exemple, travaux pouvez demander des informations techniques sur la durée du contenu à charger ou un appel de service web. Il peut également afficher la durée d’un utilisateur à remplir un formulaire, créez un compte ou effectuer un achat. Une tâche représente la durée d’une tâche, par exemple, la durée d’une tâche de téléchargement ou l’heure une bannière s’affiche à l’écran. (La signification des travaux dépend comment le Kit de développement a été intégré dans l’application.) Tâches sont généralement associées à des tâches en arrière-plan qui sont exécutées en dehors de l’étendue d’une session (c'est-à-dire, sans aucune activité utilisateur).
- Technicals : Des informations techniques sur les appareils des utilisateurs de votre application que vous pouvez effectuer le suivi, tels que les paramètres régionaux, réseau, appareil, opérateur microprogramme et de l’écran taille des appareils utilisateurs et la Version de votre application et le Kit de développement logiciel utilisé dans votre application.
- Erreurs : Des informations sur les erreurs techniques à l’intérieur de l’application qui n’impliquent pas l’application à se bloquer. Une erreur représente un problème instantané, par exemple, une panne du réseau ou une mauvaise manipulation. (La signification des événements dépend comment le Kit de développement a été intégré dans l’application.) Une erreur peut se produire pendant une session ou d’une tâche ou peut être autonome.
- Se bloque : Les informations sur les erreurs occasionnant votre application arrêté. Un blocage est une condition inattendue dans lequel l’application cesse d’effectuer ses fonctions attendues et doit être arrêtée. Un blocage est généralement en raison d’un bogue dans l’application.

![Analytics2][11]

## <a name="accessing-the-retention-overview"></a>Accéder à la vue d’ensemble de rétention
![Analytics3][12]

La vue d’ensemble de rétention se décompose du milieu dans plusieurs cartes, chacun présentant la vue d’ensemble d’une certaine période de rétention. La période de rétention de 2 jours est visible dans l’exemple. Les autres cartes affichent les périodes de rétention 4 jours et de 7 jours.

## <a name="understanding-the-retention-overview-cards"></a>Comprendre les cartes de vue d’ensemble de rétention
![Analytics4][13]

### <a name="each-card-is-composed-of-3-main-parts"></a>Chaque carte est composée de 3 parties principales :
1. 1 : la cohorte et la période considérée comme
2. 2 à 4 : la rétention pour la période en cours
3. 5 : un graphique Sparkline de l’historique

### <a name="here-is-detailed-information-about-each-element"></a>Voici des informations détaillées sur chaque élément :
1.    Cohorte et période : ce titre indique le type de cohorte. « 2 jours » signifie que nous allons étudier le comportement des utilisateurs plus de 2 jours, les utilisateurs arrivés sur une période de 2 jours et si elles se connectent en blocs de 2 jours suivants. L’exemple ci-dessus considère l’activité des utilisateurs entre le 21 et le 22 novembre.
2.    Cela donne le taux de rétention sur le 21 et 22 novembre pour les utilisateurs qui se présentent 19 et 20 novembre. Ici, nous avons rencontré 1 utilisateur actif entre le 21 et le 22, sur les 3 qui ont été nouveaux utilisateurs entre le 19 et 20.
3.    Cet indicateur visuel indique les mêmes informations qu’auparavant représentés sous forme graphique. (La troisième du cercle est à partir du nombre de 33 %). La couleur donne des informations supplémentaires : vert indique ce nombre augmente du calcul précédent. Jaune signifie moyens stables et rouge en diminuant.
4.    Cela indique que les valeurs utilisées pour le calcul.
5.    Il s’agit d’un graphique Sparkline de l’historique des valeurs de rétention. Il vous permet de voir les valeurs par le passé d’avoir une vue générale du comment il a évolué.


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
