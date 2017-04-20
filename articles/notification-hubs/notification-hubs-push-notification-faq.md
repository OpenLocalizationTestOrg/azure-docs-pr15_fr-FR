<properties
    pageTitle="Notification Azure Hubs - Forum aux Questions (FAQ)"
    description="Forum aux questions sur la conception/implémentation de solutions sur centraux de Notification"
    services="notification-hubs"
    documentationCenter="mobile"
    authors="ysxu"
    manager="erikre"
    keywords="notifications Push, les notifications push, les notifications push iOS, les notifications push android, push ios, android push"
    editor="" />

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-multiple"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="yuaxu" />

#<a name="push-notifications-with-azure-notification-hubs---frequently-asked-questions"></a>Notifications avec Hubs de Notification Azure - push Forum aux Questions

##<a name="general"></a>Général
###<a name="1---what-is-the-price-model-for-notification-hubs"></a>1. qu’est le modèle de Notification Hubs prix ?
Notification Hubs est proposé en trois niveaux :

* **Gratuit** - obtenir pousse jusqu'à 1 million par abonnement mois.
* **Base** - obtenir 10 millions push par abonnement un mois sous la forme d’un planning de référence, avec les options de croissance quota.
* **Standard** - obtenir des campagnes de 10 millions par abonnement un mois sous la forme d’un planning de référence, avec quota augmenter options, ainsi que de télémétrie enrichi démo.

Vous trouverez des informations récentes sur la page de [Notification Hubs prix] . Le prix est établie au niveau de l’abonnement et est basé sur le nombre de déclenchements de notification push afin qu’il n’a pas d’importance combien hubs espaces de noms ou d’une notification vous avez créé dans votre abonnement Azure.

Niveau **Free** est proposé à des fins de développement avec aucune garantie SLA. Bien que ce niveau soit un bon point de départ pour ceux qui à Explorer les fonctionnalités des notifications push via Azure Notification Hubs, peut-être pas le meilleur choix pour les moyennes et les applications à grande échelle.

**Base** & niveaux**Standard** est proposés pour l’utilisation de production avec les fonctionnalités clés suivantes activé *uniquement pour la norme de niveau*:

- *Télémétrie enrichi* - offre de Notification Hubs un certain nombre de fonctionnalités d’exporter vos données de télémétrie ainsi que la transmission des informations d’enregistrement de notification pour l’affichage hors connexion et l’analyse.
- *Location multi* - idéal si vous créez une application mobile à l’aide de Notification Hubs pour prendre en charge de plusieurs clients. Cela vous permet de définir des informations d’identification de Services de Notification Push (solution) au niveau de l’espace de noms concentrateur de Notification pour l’application, puis vous pouvez séparer les clients fournissant les hubs individuels sous cet espace de noms commun. Cela permet de simplifier la maintenance tandis que tout en conservant les touches associations de sécurité pour envoyer et recevoir des notifications push des hubs de notification séparées pour chaque client garantissant chevauchement entre non-client.
- *Push planifié* - permet de planifier les notifications push, qui seront par la suite la file d’attente et envoyées.
- *Importer en bloc* - vous permet d’importer les enregistrements en bloc.

###<a name="2---what-is-the-notification-hubs-sla"></a>2. Quelle est la SLA Hubs Notification ?
Pour couches de **base** et Hubs Notification **Standard** , nous garantit qu’au moins 99,9 % du temps, applications correctement configurées seront en mesure d’envoyer les notifications push ou effectuer des opérations de gestion de l’inscription par rapport à un concentrateur de Notification déployée au sein d’un niveau de prise en charge. Pour en savoir plus sur notre délai d’intervention, visitez la page de [Notification Hubs SLA] .

> [AZURE.NOTE] Il n’existe aucune garantie SLA pour les pieds entre le Service de Notification de plateforme et le périphérique dans la mesure où Hubs de Notification dépendent des fournisseurs de plateforme externes d’effectuer la notification push à l’appareil.

###<a name="3---which-customers-are-using-notification-hubs"></a>3. les clients qui utilisent Hubs Notification ?
Nous avons créé un grand nombre de clients à l’aide de Notification Hubs avec quelques remarquables ceux présentés ci-dessous :

* Sochi 2014 – 100 s de groupes d’intérêt, 3 + millions d’appareils, une notification de 150 millions expédiées de 2 semaines. [CaseStudy - Sochi]
* Skanska - [CaseStudy - Skanska]
* Heures de Seattle - [CaseStudy - heures Seattle]
* Mural.ly - [CaseStudy - Mural.ly]
* 7Digital - [CaseStudy - 7Digital]
* Bing applications – 10 millions d’appareils, envoi de 3 millions de notifications par jour.

###<a name="4-how-do-i-upgrade-or-downgrade-my-notification-hubs-to-change-my-service-tier"></a>4. comment mettre à niveau ou mettre à niveau mon Hubs de Notification pour modifier mon niveau de service ?
Accédez au [Portail classique Azure], cliquez sur Bus des services et cliquez sur votre espace de noms puis votre concentrateur de notification. Sous l’onglet échelle, vous ne pourrez pas modifier votre niveau de service de Notification Hubs.

![](./media/notification-hubs-faq/notification-hubs-classic-portal-scale.png)

##<a name="design--development"></a>Création et développement
###<a name="1---which-server-side-platforms-do-you-support"></a>1. les plateformes côté serveur vous prennent en charge ?
Nous fournissons SDK et [exemples complètes] pour .NET, Java, PHP, Python, Node.js afin qu’un serveur principal application puisse être le programme d’installation pour communiquer avec Hubs de Notification à l’aide d’un de ces plateformes. Notification Hubs API sont basées sur les interfaces reste afin que vous pouvez choisir à parler directement à ceux à la place si vous ne voulez pas ajouter une dépendance supplémentaire. Vous trouverez plus d’informations sur la page [NH - API REST] .

###<a name="2---which-client-platforms-do-you-support"></a>2. les plateformes client support ?
Nous envoyer des notifications push à [Apple iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-gcm-get-started.md), [Universel Windows](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Chine Android (par le biais Baidu)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin prend en charge ([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) & [Android](xamarin-notification-hubs-push-notifications-android-gcm.md)), [Les applications de Chrome](notification-hubs-chrome-push-notifications-get-started.md) et [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari) plateformes. Pour une liste complète des prise didacticiels s’attaquer envoyer des notifications push sur ces plateformes, visitez notre page [NH - prise en main les didacticiels] .

###<a name="3---do-you-support-smsemailweb-notifications"></a>3., vous prenez en charge les notifications de messagerie/SMS/web ?
Notification Hubs est destiné à envoyer des notifications aux applications mobiles à l’aide des plateformes répertoriées ci-dessus. Que nous ne fournissons pas encore la possibilité d’envoyer le message électronique ou alertes SMS ; Toutefois plateformes tiers qui fournissent ces fonctionnalités peuvent être intégrées avec Hubs de Notification pour envoyer les notifications push native à l’aide [d’Applications Mobile Azure].

Notification Hubs ne fournissent pas également une épingle dans le navigateur notification remise service-de-l’emploi. Les clients peuvent choisir de procéder à l’aide de SignalR sur les plateformes prises en charge côté serveur. Si vous recherchez pour envoyer des notifications aux applications de navigateur dans le bac à sable Chrome, consultez le [didacticiel applications de Chrome].

###<a name="4---what-is-the-relation-between-azure-mobile-apps-and-azure-notification-hubs-and-when-do-i-use-what"></a>4. Quelle est la relation entre les applications Mobile Azure et Azure Notification Hubs et quand utiliser que ?
Si vous avez un serveur principal de l’application mobile existant et que vous souhaitez ajouter la possibilité d’envoyer les notifications push uniquement vous pouvez utiliser Azure Notification Hubs. Si vous souhaitez configurer la principale application mobile à partir de zéro puis vous envisagez d’utiliser les applications Mobile Azure. Une application Mobile Azure met automatiquement en un concentrateur de Notification pour vous permettre d’envoyer les notifications push facilement à partir de la version de serveur de l’application mobile. Tarifs pour applications Mobile Azure inclut les frais de base d’un concentrateur de Notification et que vous payez uniquement lorsque vous passez au-delà de la pousse inclus. Plus d’informations sur les coûts sont disponibles dans la page [Application Service tarifs] .

###<a name="5---how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>5. combien de périphériques puis-je prennent en charge si j’envoie des notifications push via Hubs Notification ?
Reportez-vous à la page [Notification Hubs tarification] pour plus d’informations sur le nombre d’appareils pris en charge.

Pour certains scénarios, si vous devez prendre en charge plus de 10 000 000 appareils inscrits, veuillez [nous contacter](https://azure.microsoft.com/overview/contact-us/) directement et nous vous aideront évoluer votre solution.

###<a name="6---how-many-push-notifications-can-i-send-out"></a>6. le nombre de notifications push peut envoyer ?
Selon le niveau choisi, Azure redimensionne automatiquement vers le haut selon le nombre de notifications de transmission au sein du système.

>[AZURE.NOTE] Le coût d’utilisation globale peut remonter en fonction du nombre de notifications de transmission pris en charge. Vérifiez que vous connaissez des limites de niveau existantes sous forme de plan dans la page [Notification Hubs tarifs] .

Nos clients existants utilisent Hubs de Notification pour envoyer tous les jours des millions de notifications de transmission. Vous n’avez pas à effectuer d’action particulière à l’échelle votre push notifications atteint dans la mesure où vous utilisez Azure Notification Hubs.

###<a name="7---how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>7. combien de temps faut-il pour envoyés transmission des notifications pour accéder à mon appareil ?
Azure Hubs de Notification est en mesure de traiter au moins **1 million de notification d’émission envoie une minute** en un mode normal utilisez scénario où la charge entrante est assez cohérente et n’est pas spikey par nature. Ce taux peut-être varier selon le nombre de balises, nature des envois entrants et d’autres facteurs externes.

Pendant la durée estimée de remise, le service est en mesure de calculer les cibles par plateforme et acheminer les messages aux services de livraison de notification push respectifs basées sur les expressions de balises/balise enregistrée. À partir de là, il est la responsabilité des services de Notifications de transmission (solution) pour envoyer la notification à l’appareil.

Une solution ne garantit pas les SLA permettant de notifications ; Toutefois, généralement majorité des notifications de transmission sont remis à équipements cibles après quelques minutes (généralement dans la limite de 10 minutes) entre le moment où ils sont envoyées à notre plateforme. Il peut y avoir plusieurs valeurs aberrantes qui peuvent prendre plus de temps.

>[AZURE.NOTE] Azure Hubs Notification contient une stratégie en place pour supprimer les notifications de transmission qui ne sont pas en mesure d’être remis à la solution en 30 minutes. Ce retard peut se produire pour plusieurs raisons, plus fréquemment, car la solution est la limitation de votre application.

###<a name="8---is-there-any-latency-guarantee"></a>8. existe-t-il aucune garantie latence ?
En raison de la nature des notifications push (ils sont remis par un Service de Notification Push externes, spécifique à la plateforme), il n’existe aucune garantie latence. En règle générale, la plupart des notifications push obtenir remis dans quelques minutes.

###<a name="9---what-are-the-considerations-i-need-to-take-into-account-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>9. Quelles sont les considérations qu'ai-je besoin pour prendre en compte lors de la conception d’une solution avec des espaces de noms et Hubs Notification ?

####<a name="mobile-appenvironment"></a>Environnement/application mobile

* Il doit être un concentrateur de Notification par l’application mobile, par environnement.
* Dans un scénario de client multiples, chaque client doit avoir un concentrateur distinct.
* Vous devez jamais partager la même concentrateur de Notification entre environnements de test et de production que cela peut entraîner des problèmes la ligne vers le bas lors de l’envoi des notifications. par exemple, Apple propose des points de terminaison Sandbox et la transmission de Production disposant chacun d’informations d’identification séparées.
* Par défaut, vous pouvez envoyer les notifications de test sur vos périphériques inscrits via le portail Azure ou le composant Azure intégré dans Visual Studio. Le seuil est défini sur 10 périphériques sont sélectionnés au hasard à partir du pool d’inscription.

>[AZURE.NOTE] Si le hub a été configuré à l’origine avec un certificat sandbox Apple et puis reconfiguré pour utiliser un certificat de production Apple, les jetons appareil ancien seraient ne sont plus valides avec le nouveau certificat et entraîner pousse à échouer. Il est préférable de séparer votre production et environnements de test de hubs différentes pour les différents environnements.

####<a name="pns-credentials"></a>Informations d’identification de la solution

Lorsqu’une application mobile est enregistrée auprès développeur portal d’une plateforme (par exemple, Apple ou Google etc.) puis vous obtenez une application jetons identificateur et la sécurité d’une version de l’application serveur doit fournir aux services de Notification Push de la plateforme pour pouvoir envoyer les notifications push pour les appareils. Ces jetons de sécurité qui peuvent être sous la forme de certificats (par exemple pour Apple iOS ou Windows Phone) ou des clés de sécurité (Google Android, Windows etc.) doivent être configurés dans Hubs de Notification. Cela s’effectue généralement au niveau du concentrateur de notification mais peut également être effectuée au niveau de l’espace de noms dans un scénario de client multiples.

####<a name="namespaces"></a>Espaces de noms

Espaces de noms peut être utilisé pour le regroupement de déploiement.  Il peut également être utilisé pour représenter tous les Hubs de Notification pour toutes les installations de l’application même dans le scénario client multiples.

####<a name="geo-distribution"></a>Geo Student

Geo Student n’est pas toujours critique dans les scénarios de notification push. Il est à noter que divers Push Notification Services (par exemple, APNS, GCM etc.), qui en définitive, fournissent les notifications push pour les appareils, ne sont pas de manière égale distribué des options.

Si vous avez une application qui est utilisée dans le monde entier, vous pouvez créer plusieurs hubs différents espaces de noms en tirant parti de la disponibilité du service de Notification Hubs dans différentes régions Azure dans le monde.

>[AZURE.NOTE] Cela augmente le coût de gestion - particulièrement autour des enregistrements, afin que ce n’est pas recommandé vraiment et doit être effectué uniquement s’il existe un besoin explicit.

###<a name="10--should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>10. faire des enregistrements à partir de la version de serveur d’application ou directement par le biais de client appareils ?
Les enregistrements de la version de serveur d’application sont utiles lorsque vous devez effectuer l’authentification du client avant de créer l’enregistrement ou lorsque vous avez balises qui doivent être créées ou modifiées par le serveur principal application basé sur une logique de l’application. Pour plus d’informations, vous pouvez en savoir plus dans les pages [d’enregistrement de serveur principal conseils] et [recommandations d’enregistrement de serveur principal - 2] .

###<a name="11--what-is-the-push-notification-delivery-security-model"></a>11. quel est le modèle de sécurité de remise de notification push ?
Azure Notification Hubs utiliser une [Signature accès partagé (sa)](../storage/storage-dotnet-shared-access-signature-part-1.md)-en fonction du modèle de sécurité. Vous pouvez utiliser les jetons associations de sécurité au niveau de l’espace racine ou au niveau de la Notification Hubs granulaire. Ces jetons associations de sécurité peut être définie avec des règles d’autorisation différent par exemple, envoyer message autorisations, écouter des autorisations pour les notifications etc.. Obtenir plus d’informations sont disponibles dans le document de [modèle de sécurité NH] .

###<a name="12--how-should-i-handle-sensitive-payload-in-the-push-notifications"></a>12. Comment dois-je traiter sensible charge utile dans les notifications push ?
Toutes les notifications sont remises à équipements par la plate-forme transmission Notification Services (solution). Lorsqu’un expéditeur envoie une notification à Azure Notification Hubs nous traiter et transmettre la notification à la solution correspondantes.

Toutes les connexions de l’expéditeur à la Hubs de Notifications Azure, puis à la solution utilisent HTTPS.

>[AZURE.NOTE] Azure Hubs Notifications n’enregistre pas la charge utile du message d’aucune manière.

Pour l’envoi charges sensibles alors que nous vous recommandons d’un motif de transmission sécurisé où l’expéditeur propose une notification « ping » avec un identificateur de message à l’appareil sans la charge utile sensible et lorsque l’application sur le périphérique reçoit cette charge utile, il est en mesure d’appeler une API sécurisée directement pour récupérer les détails du message. Un repère sur la façon d’implémenter le modèle ci-dessus est disponible dans la page [NH - didacticiel de Push banque d’informations sécurisé] .

##<a name="operations"></a>Opérations
###<a name="1---what-is-the-disaster-recovery-dr-story"></a>1. qu’en est-il la récupération après sinistre (DR) ?
Nous fournissons la couverture de sinistre métadonnées sur notre fin (nom Hub de Notification, chaîne de connexion et autres informations critiques). Lorsqu’un scénario DR se produit, les données des enregistrements sont le **segment uniquement** de l’infrastructure Hubs de Notification qui est perdue. Vous devrez implémenter une solution pour remplir à nouveau ces données dans votre nouvelle après la reprise concentrateur.

- *Étape 1* - créer un concentrateur de Notification secondaire dans un centre de données différent. Vous pouvez créer cette à la volée au moment de l’événement DR ou vous pouvez en créer un dans le volet get accédez. Il n’y beaucoup de différence option que vous choisissez, car la mise en service de Notification concentrateur est un processus relativement rapide dans l’ordre de quelques secondes. Avoir une à partir du début pour protéger à partir de l’événement DR ayant un impact sur les fonctions de gestion, il s’agit l’option vivement recommandé.

- *Étape 2* - Hydrate le Hub de Notification secondaire avec les enregistrements à partir du Hub Notification principal. Il n’est pas recommandé pour essayer de conserver les enregistrements sur les deux centraux et essayez de synchroniser les à la volée que les enregistrements sont bientôt dans - généralement qui n’a pas fonctionné bien en raison de tendance intrinsèque à des enregistrements à la date d’expiration sur le côté de la solution. Notification Hubs les nettoyer comme nous recevoir des commentaires de solution à propos des enregistrements a expiré ou non valides.  

Recommandation consiste à utiliser une version de serveur d’application qui :

- Met à jour un jeu d’enregistrements à la fin de son donné afin qu’il peut faire une insertion en bloc dans le hub secondaire notification en cas de DR

**OR**

- Obtient un standard vider des enregistrements à partir du hub primaire comme copie de sauvegarde et puis sont un bloc insérées dans le NH secondaire.

>[AZURE.NOTE] Fonctionnalité d’exportation/importation des enregistrements disponible dans la couche Standard est décrite dans le document [D’exportation/importation des enregistrements] .

Si vous n’avez pas un serveur principal, puis au démarrage de l’application sur un des équipements cibles, ils effectue un nouvel enregistrement dans le Hub de Notification secondaire, et finalement secondaire concentrateur de Notification créera tous les appareils actives enregistrés.

L’inconvénient est qu’il y aura une période de temps lorsque appareils où les applications n’avez pas ouvert la ne recevront pas les notifications.

###<a name="2---is-there-any-audit-log-capability"></a>2. existe-t-il une fonctionnalité de journal d’audit ?
Toutes les opérations de gestion des notifications Hubs atteindre journaux opération qui sont présentés dans le [Portail classique Azure].

##<a name="monitoring--troubleshooting"></a>Surveillance et résolution des problèmes
###<a name="1---what-troubleshooting-capabilities-are-available"></a>1. Quelles capacités de résolution des problèmes sont disponibles ?
Azure Hubs Notification fournit plusieurs fonctionnalités pour effectuer la résolution des problèmes courants, en particulier dans le scénario le plus courant liés aux notifications ignorés. Afficher des détails dans notre [NH - résolution des problèmes] livre blanc.

###<a name="2---what-telemetry-features-are-available"></a>2. quelles fonctionnalités de télémétrie sont disponibles ?
Azure Hubs de Notification vous permettent de conserver l’affichage des données de télémétrie dans le [Portail classique Azure]. Détails des mesures disponibles sont disponibles dans la page [NH - indicateurs] .

>[AZURE.NOTE] Notifications réussies, cela signifie simplement que les notifications push ont été remises au service de Notification Push externes (par exemple, APNS pour les ordinateurs Apple, GCM pour Google, etc.). C’est la solution d’effectuer la notification aux périphériques cible. En règle générale, la solution n’expose pas les mesures de remise pour les fournisseurs tiers.  

Nous fournissons également la possibilité d’exporter les données de télémétrie par programme (dans la couche **Standard** ). Voir [NH - exemple métriques] pour plus d’informations.

[Portail classique Azure]: https://manage.windowsazure.com
[Notification Hubs tarifs]: http://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: http://azure.microsoft.com/support/legal/sla/
[CaseStudy - Sochi]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=7942
[CaseStudy - Skanska]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=5847
[CaseStudy - Seattle heures]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=8354
[CaseStudy - Mural.ly]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=11592
[CaseStudy - 7Digital]: https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=3684
[NH - API REST]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[NH - didacticiels de prise en main]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Didacticiel des applications de chrome]: http://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: http://azure.microsoft.com/pricing/details/mobile-services/
[Conseils d’enregistrement de serveur principal]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[Guide de l’enregistrement de serveur principal - 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Modèle de sécurité NH]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[NH - didacticiel Push sécurisé]: http://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[NH - résolution des problèmes]: http://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[NH - indicateurs]: https://msdn.microsoft.com/library/dn458822.aspx
[NH - exemples d’indicateurs]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/FetchNHTelemetryInExcel
[Les enregistrements d’exportation/importation]: https://msdn.microsoft.com/library/dn790624.aspx
[Azure Portal]: https://portal.azure.com
[exemples complètes]: https://github.com/Azure/azure-notificationhubs-samples
[Applications mobiles Azure]: https://azure.microsoft.com/en-us/services/app-service/mobile/
[Service d’application tarifs]: https://azure.microsoft.com/en-us/pricing/details/app-service/
