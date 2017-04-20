<properties 
   pageTitle="Azure Engagement Mobile Guide - Push/portée de dépannage" 
   description="Dépannage des problèmes de notification et d’interaction utilisateur dans Azure Mobile Engagement" 
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

# <a name="troubleshooting-guide-for-push-and-reach-issues"></a>Guide de dépannage des notifications Push et communiquer aux problèmes

Les éléments suivants sont les éventuels problèmes que vous pouvez rencontrer avec comment Azure Mobile Engagement envoie des informations à vos utilisateurs.
 
## <a name="push-failures"></a>Échecs de push

### <a name="issue"></a>Problème
- Push ne fonctionne pas (dans l’application, se déconnecter de l’application, ou les deux).

### <a name="causes"></a>Causes
- Une défaillance push autant de fois est une indication que Azure Mobile Engagement, portée ou une autre fonctionnalité avancée de l’Engagement Azure Mobile n’est pas correctement intégrée ou qu’une mise à niveau est requise dans le Kit de développement pour résoudre un problème connu avec une nouvelle plateforme de système d’exploitation ou appareil.
- Tester simplement une épingle dans l’application et simplement un push d’application pour déterminer si un élément est un problème dans l’application ou d’application.
- Testez à partir de l’interface utilisateur et l’API cadre du dépannage pour voir les informations d’erreur supplémentaires disponibles aux deux endroits.
- Déconnecter de l’application push ne fonctionne pas sauf si Azure Mobile Engagement et accessibles sont intégrées dans le Kit de développement.
- Push ne fonctionne pas si les certificats ne sont pas valides, ou utilisent produit et développement correctement (iOS uniquement). (**Remarque :** « déconnecter de l’application » les notifications push ne soient pas remises sur iOS, si vous avez à la fois le développement (développement) et les versions de production (production) de votre application installée sur le même appareil étant donné que le jeton de sécurité associée à votre certificat peuvent être annulées par Apple. Pour résoudre ce problème, désinstallez les versions nomenclature de production et de développement de votre application et réinstallez uniquement la version sur votre appareil.)
- Se déconnecter de push application détermine est gérées différemment dans différentes plateformes (iOS affiche moins d’informations que Android si pousse native est désactivés sur un appareil, l’API peut fournir davantage d’informations que l’interface utilisateur sur les statistiques push).
- Déconnecter de l’application push peut être bloqué par les clients au niveau du système d’exploitation (iOS et Android).
- Déconnecter de l’application pousse est affichés comme désactivée dans l’interface utilisateur de Azure Mobile Engagement s’ils ne sont pas correctement intégrées, mais peuvent échouer en mode silencieux à partir de l’API.
- Dans l’application push ne fonctionne pas sauf si Azure Mobile Engagement et accessibles sont intégrées dans le Kit de développement.
- Pousse GCM et ADM ne fonctionne pas sauf si Azure Mobile Engagement et le serveur spécifique sont intégrés dans le Kit de développement logiciel (Android uniquement).
- Dans l’application et d’application pousse doit être testée séparément pour déterminer il s'existe un problème Push ou accessibles.
- Dans l’application pousse requièrent que l’application soit ouvert à recevoir.
- Dans l’application push est souvent le programme d’installation à filtrer par une balise informations application dans l’inscription ou annulations.
- Si vous utilisez une catégorie personnalisée dans accessibles pour afficher les notifications de dans l’application, vous devez suivre le cycle de vie correct de la notification, sinon la notification ne peut pas être désactivée lorsque l’utilisateur faire disparaître.
- Si vous démarrez une campagne avec aucune date de fin et un périphérique reçoit l’application en notification mais ne s’affiche pas il encore, l’utilisateur continueront recevoir la notification la prochaine fois qu’ils se connectent à l’application, même si vous arrêtez manuellement la campagne.
- Pour des problèmes avec l’API Push, vérifiez que vous souhaitez réellement utiliser l’API de transmission au lieu de l’API atteigne (dans la mesure où l’API atteigne est plus fréquemment utilisé) et que vous ne soyez pas confondre les paramètres « charge utile » et « notificateur ».
- Tester votre campagne push avec les deux un appareil connecté via Wi-Fi et 3G pour éliminer la connexion de réseau de source des problèmes de synchronisation.

## <a name="push-testing"></a>Notifications Push test

### <a name="issue"></a>Problème
- Pousse peut être envoyés à un périphérique spécifique basé sur un ID de périphérique.

### <a name="causes"></a>Causes

- Test dispositifs sont configurés différemment pour chaque plate-forme, mais à l’origine d’un événement dans votre application sur un appareil de test et recherchez votre ID de périphérique dans le portail doivent utiliser pour trouver votre ID d’appareil pour toutes les plateformes.
- Test appareils fonctionnent différemment avec IDFA et IDFV (iOS uniquement).


## <a name="push-customization"></a>Personnalisation de push

### <a name="issue"></a>Problème
- Advanced push contenu élément ne fonctionne pas (badge, en anneau, vibrations, image, etc.).
- Liens à partir de push ne fonctionnent pas (déconnecter de l’application, dans l’application, pour un site Web, à un emplacement dans l’application).
- Statistiques push montrent qu’un push pas envoyé à autant de personnes comme prévu (trop ou pas assez).
- Push dupliqué et reçu à deux reprises.
- Impossible d’inscrire dispositif de test pour Azure Mobile Engagement pousse (avec votre propre application produit ou développement).

### <a name="causes"></a>Causes

- Pour créer un lien vers un emplacement spécifique dans l’application nécessite « catégories » (Android uniquement).
- Jeux de liaison approfondie pour rediriger les utilisateurs vers un autre emplacement après avoir cliqué sur une notification push doivent être créés dans et gérées par votre application et le périphérique du système d’exploitation non par Engagement Mobile directement. (**Remarque :** déconnecter de l’application notifications ne peut pas créer un lien directement à des endroits application avec iOS comme pour Android.)
- Serveurs d’images externes doivent être en mesure d’utiliser HTTP « GET » et « Tête » pour la vue d’ensemble pousse à utiliser (Android uniquement).
- Dans votre code, vous pouvez désactiver l’agent Azure Mobile Engagement lorsque le clavier est ouvert et que votre code réactiver l’agent Azure Mobile Engagement une fois que le clavier est fermé afin que le clavier n’affecte pas l’apparence de votre notification (iOS uniquement).
- Certains éléments ne fonctionnent pas dans simulations de test, mais uniquement les campagnes réelles (badge, en anneau, vibrations, image, etc.).
- Aucune donnée du côté serveur n’est enregistrée lorsque vous utilisez le bouton « Tester » push. Données sont enregistrées uniquement pour les campagnes push réel.
- Pour aider à isoler le problème, résoudre les problèmes avec : tester, simuler et une campagne réelle dans la mesure où ils fonctionnent différemment.
- La durée pendant laquelle que votre « dans l’application » et les campagnes « tout moment » sont planifiées pour exécuter affecte les numéros de remise dans la mesure où une campagne est uniquement remise à des utilisateurs qui se trouvent « dans l’application » pendant l’exécution de la campagne (et les utilisateurs qui ont leurs paramètres du périphérique définis pour recevoir des notifications « déconnecter de l’application »).
- Les différences entre comment traiter les Android et iOS déconnecter les notifications d’application complique la comparer directement des statistiques push entre la version Android et iOS de votre application. Android fournit des informations de notification au niveau du système d’exploitation qu’iOS. Rapports Android lorsqu’une notification native est reçue, un clic sur ou supprimée dans le centre de notification, mais iOS ne signale pas ces informations, sauf si vous cliquez sur la notification. 
- La principale raison qui sont les numéros de « poussés » différentes de celle de l’autre que « remis » nombres pour communiquer aux campagnes est que « dans l’application » et « déconnecter de l’application » notifications sont comptées différemment. « Dans l’application » notifications sont gérées par Engagement Mobile, mais « déconnecter de l’application » notifications sont gérées par le centre de notification dans le système d’exploitation de votre appareil.

## <a name="push-targeting"></a>Notifications Push ciblage

### <a name="issue"></a>Problème
- Intégré ciblage ne fonctionne pas comme prévu.
- Ciblage de balise App Info ne fonctionne pas comme prévu.
- GÉOLOCALISATION ciblage ne fonctionne pas comme prévu.
- Options de langue ne fonctionnent pas comme prévu.

### <a name="causes"></a>Causes

- Vérifiez que vous avez téléchargées balises d’informations application via la Azure Mobile Engagement l’interface utilisateur ou API.
- La limitation de la vitesse de push ou quota push au niveau de l’application, ou pour limiter le public au niveau de la campagne peut empêcher une personne de recevoir un push spécifique même s’ils répondent à vos critères de ciblage. 
- Définition d’une langue » » est différent de celui ciblage en fonction de pays ou de paramètres régionaux, qui sont également différente de celle de ciblage selon géolocalisation basé sur un téléphone emplacement ou GPS.
- Le message dans la langue « par défaut » est envoyé à tous les clients qui n’a pas leur périphérique défini à un des autres langues que vous spécifiez.


## <a name="push-scheduling"></a>Notifications Push planification

### <a name="issue"></a>Problème
- Planification push ne fonctionne comme prévu (envoyé trop au plus tôt ou différé).

### <a name="causes"></a>Causes

- Fuseaux horaires pouvez problèmes grâce à la planification, en particulier lors de l’utilisation de fuseau horaire de l’utilisateur final.
- Fonctionnalités avancées push peuvent retarder push.
- Ciblage basé sur téléphone paramètres (au lieu de balises informations d’application) peuvent retarder envoie depuis Azure Mobile Engagement peut-être demander des données à partir du téléphone en temps réel avant d’envoyer un push.
- Campagnes marketing créées sans date de fin stocke localement le push sur l’appareil et l’affiche la prochaine ouverture de l’application même si la campagne est terminée manuellement.
- Démarrage de plusieurs campagnes en même temps peut prendre plus de temps à analyser votre base de l’utilisateur (try uniquement commencer une campagne marketing à la fois avec un maximum de quatre, également cible uniquement à vos utilisateurs actifs afin que les anciens utilisateurs ne doivent pas être analysés).
- Si vous utilisez l’option « Ignorer l’Audience, push sera envoyé aux utilisateurs via l’API » dans la section « Campagne » d’une campagne accessibles, la campagne vous envoie pas automatiquement, vous devrez envoyer manuellement via l’API atteindre.
- Si vous utilisez une catégorie personnalisée dans accessibles pour afficher les notifications de dans l’application, vous devez suivre le cycle de vie correct d’une notification, sinon la notification ne peut pas être désactivée lorsque l’utilisateur faire disparaître.

 
