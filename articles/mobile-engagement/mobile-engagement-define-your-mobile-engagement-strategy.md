<properties
    pageTitle="Définir votre stratégie d’Engagement Mobile | Microsoft Azure"
    description="Découvrez comment à intégré et optimiser votre Engagement Mobile avec les notifications push et d’analytique."
    services="mobile-engagement"
    documentationCenter="Mobile"
    authors="piyushjo"
    manager="dwrede"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="define-your-mobile-engagement-strategy"></a>Définir votre stratégie d’Engagement Mobile

*Vous avez écrit votre application pour une raison : pour que vos utilisateurs utilisez-le !*

Nous estimons que vous stockez certainement un beaucoup de temps dans tente de faire une application de rédaction utilisateurs seront aime. Vous investi également probablement un montant importants du budget marketing acquérir des utilisateurs. Mais après pointe agréable initial d’utilisateurs, il est possible que les lentement arrêter l’utilisation de votre application. *Il s’agit de quels Engagement Mobile Azure tout !*: difficulté les coller et vous permettent d’améliorer votre application par le biais test par incréments et découvrez.

Notre approche pour améliorer la conservation et l’utilisation est basé sur attrayants utilisateurs de l’application via les notifications push et messages dans l’application, mais d’une manière très spéciale, des messages et des communications personnalisées, chacun en fonction de leur comportement dans votre application. Notre objectif est de vous permettre de communiquer avec la bonne audience au bon moment et à l’emplacement approprié.

Mais, pour ce faire, vous devez commencer par *comprendre vos utilisateurs*, puis créer des groupes en fonction de leur avez-vous ou leurs caractéristiques (nous appeler segments) et puis créer des communications appropriées à chaque segment.

## <a name="mobile-engagement-serves-your-objectives"></a>Engagement mobile gère vos objectifs

*Nous avons sur la conservation et l’utilisation, mais pour que ?*

Élaborer votre stratégie d’Engagement Mobile nécessite regardant d’abord à votre application objectifs et indicateurs de performance clés (KPI).

Commencez par définir les objectifs et les indicateurs de performance clés qui permettent de définir votre cas d’utilisation engagement avec le prisme droite.

Votre cas d’utilisation sont une simple liste de campagnes que vous voulez rendre communiquer avec d’autres utilisateurs, allant de la simple d’accueil, à la notification utilitaire très avancé déclenchée par votre système informatique. Un cas d’utilisation Bien construit doit comporter au moins trois *what-qui-lorsque*:

1. Désignation très courte (par exemple, un « Bienvenue campagne marketing »).
2. **Quels**: un exemple de message (par exemple, « donc content d’avez-vous bord ! N’oubliez pas de se connecter pour obtenir votre 1er mois gratuits ! »). Ce message n’est pas final, vous pourrez modifier quand que vous le souhaitez, mais il faut généralement pour démarrer réflexion sur ce que nous voulons dire.
3. **Qui**: le segment qui reçoivent ce message (par exemple, « tous les utilisateurs qui a démarré l’application pour la première fois 3 jours, avez visités à la page de connexion mais n'êtes pas connecté à »).
    - Oui, vous pouvez le faire très facilement avec Azure Mobile Engagement  :)
    - Là encore, cela n’a pas à être final que vous pouvez définir vos segments à tout moment, mais il est important de définir au plus tôt dans vos critères de segmentation pour vous assurer que vous collecter les données appropriées.
4. **Quand**: le minutage de votre campagne. Il peut être une date donnée ou après une action spécifique, basé sur un déclencheur. Engagement Mobile offre une quantité importante de possibilités pour vos communications juste titre du temps.

Une fois que les scénarios d’utilisation et les segments sont définis, il vous donne une indication pour définir les données qui doivent être collectées dans une application. Il s’agit le rôle d’un *« plan balise »*. Un plan de balise permet de vous assurer que la collecte de données est spécifiée pour les développeurs. Par conséquent, les développeurs sont en mesure d’incorporer Mobile Engagement avec la configuration appropriée pour vous permettent d’utiliser vos campagnes avec les données appropriées. Il sera également très important exécuter des tests pour vous assurer que l’intégration est correcte et collecte ce dont vous avez besoin.

En fonction de l’intégration, une fois que les applications sont publiées, comme un sont seront en mesure de voir votre analytique en temps réel, segmenter votre audience et puis commencez à envoyer actives ciblé notification push pour collaborer avec les utilisateurs finaux dans ou se déconnecter de l’application.

### <a name="use-cases-to-get-started"></a>Exemples d’utilisation pour commencer
1. Bienvenue dans stratégie : créer des campagnes de notification push plusieurs basés sur le comportement de l’utilisateur final dans le menu de lancement de l’application afin de ré-s’engager à D + 2/5/10/15 après la première session et augmentez première exécution de rétention.
2. Promouvoir un nouveau contenu (fonctionnalité, article/vidéo ou produit) basé sur le comportement de l’utilisateur final pour envoyer les informations uniquement aux utilisateurs finaux qui sont plus susceptibles de s’engager.
3. Évaluer l’application : cibler moins de 1 % de votre base de l’utilisateur est très probablement lié à évaluer les étoiles application 5 sur le magasin.
4. Améliorer l’aspect abonnements : promouvoir précieux contenu aux utilisateurs finaux qui n’ont pas vu leur encore augmenter l’abonnement.
5. Didacticiel : Didacticiel plus obligatoire pour tout le monde. Pourquoi ne pas créer super didacticiels dans l’application, puis déclencheur eux par le biais de l’application uniquement si l’utilisateur semble ne pas utiliser l’application des messages ou rencontre des difficultés à l’aide d’une fonctionnalité ?

## <a name="why-do-you-need-analytics-to-engage"></a>Pourquoi dois-je analytique de s’engager ?

Comme vous pouvez constater à ce stade, effectuer une notification push diffusion uniquement n’est pas suffisamment. Le concept de base d’Engagement Mobile est d’aider les responsables et les développeurs engager avec l’utilisateur final droite au moment opportun et au bon endroit. Pour connaître les trois principaux concepts, il est essentiel pour collecter analytique à partir de votre application et puis utilisez-le pour segmenter votre public. C’est aussi encore plus puissant segments comportement complète des données à partir de votre autre base de données ou CRM ou d’un canal croisé. Engagement mobile permet la récupération des données à partir de n’importe quel emplacement et l’utilise pour cibler le bon public.

Pour être plus contextuelle possibles lorsque vous utilisez votre public, il est essentiel d’avoir les connaissances du comportement aux utilisateurs finaux, connaître leur état en temps réel. Collecte de données permet de marketing à consacrer vraiment à lire des exemples d’utilisation et d’atteindre leurs objectifs de stratégie engagement mobile. Pour atteindre les objectifs jeu précédemment est également la raison pour laquelle nous vous recommandons est en fait ne pas pour rassembler tout et dans l’analytique, mais seules celles que vous pouvez vous concentrer sur ce que vous voulez en savoir plus et votre cas d’utilisation. C’est la bonne méthode pour commencer, essayez, tester et apprenez à utiliser la solution et adresse de notification push smart et augmenter la rétention d’une application pour faire apparaître à un niveau de réussite.

>[AZURE.NOTE] N’oubliez pas : Trop de données supprime les données !

### <a name="use-cases-and-best-practices"></a>Exemples d’utilisation et des pratiques recommandées

Dans les sections suivantes, nous aborderons brièvement quelques touches-exemples d’utilisation nous avons fournis regard nos clients de mise en route.

#### <a name="media"></a>Éléments multimédias

Collecter le type de contenu qui est utilisé par l’utilisateur final et segment, puis l’audience en fonction de ce comportement à des types spécifiques de contenu uniquement à une audience qui sera censée de consommer cible. Elle permet d’éviter spam une base de l’utilisateur dans son ensemble et assurer une meilleure rétention.

#### <a name="m-commerce"></a>M-commerce

Collecter les catégories de produits plus visités au sein de l’application et cibles des participants pour promouvoir une remise ou un nouveau produit dans cette catégorie que l’utilisateur final sera plus susceptibles d’être achetés. Objectif est d’améliorer l’aspect de chiffre d’affaires. À nouveau l’objectif ne doit ne pas du courrier indésirable !

#### <a name="gaming"></a>Jeu

Collecter le niveau de jeu pour un utilisateur final et le temps passé dans une période donnée afin de cibler le public qui pourrait être bloqué et serait plus susceptible d’accéder à un niveau supérieur avec une offre bonus.

Communiquer des événements spécifiques inciter aux utilisateurs qui n’ont pas lu depuis un certain temps tenter d’Encouragez-les à retourner.

#### <a name="retail"></a>Vente au détail

Collecter les produits ou les marques une audience doit être censée de consommer basé sur Favoris ou le comportement et le lecteur l’audience à votre banque pour augmenter le chiffre d’affaires achat.

#### <a name="banking"></a>Bancaires

Collecter les données d’utilisateurs finaux qui créé un compte lors du premier lancement de l’application. Visent à déployer une stratégie de bienvenue avec une notification push ciblée et augmenter le nombre d’abonnements de compte.

### <a name="how-to-create-a-great-tag-plan"></a>Comment créer un plan de balise correctement ?

Un plan de balise doit être identique à une description de l’utilisateur-chemin d’accès ou un type de flux de travail de l’application, qui fournit toutes les balises nécessaires (données) qui doivent être collectés pour avoir suffisamment analytique à comprendre le comportement de l’utilisateur et des segments correctement la base d’utilisateurs. Il s’agit pas d’un processus technique. Par conséquent, marketing est en mesure de spécifier les données qu'auxquelles ils veulent collecte en fonction de leur stratégie Mobile Engagement.

La valeur minimale consiste à ajouter des balises au moins tous les écrans (appelées *activités* dans Mobile Engagement) d’une application. Cela permet de déterminer le chemin d’accès utilisateur.

Une activité peut incorporer des *événements* qui recueillir les informations sur l’action à un clic. Cela permet de la collection d’interactions au sein de l’application. Par conséquent, marketing est en mesure de savoir ce que les utilisateurs écran consultée et qu’ils effectuent.

`Jobs`sont des actions avec une durée. C’est très utile sont à comprendre la durée d’un utilisateur pour créer un compte ou se connecter par exemple. Cela peut également être utile pour les développeurs contrôler la durée d’appeler un service web.

`Errors`peut également être contrôlée pour savoir si les utilisateurs rencontrent des problèmes dans votre application. Par exemple, l’obtention des problèmes de connexion fréquents.

Tous de ce type de données peuvent être complétée avec des paramètres (*informations supplémentaire* lors de l’Engagement Mobile) qui vous permet de regrouper des données dynamiques à partir de l’application. Ceci est important permettre à segmentation Permissions. Par exemple, peuvent-ils peuvent segmenter un utilisateur en fonction du type de contenu qu’ils ont utilisé. Le type de contenu sera les informations dynamiques d’une activité ou un événement.

*Informations de l’application* sont des données qui vous permet de confirmer l’état de l’application ou de l’utilisateur en temps réel. Ceci permet également de classer une base de l’audience et cibler rapidement. Par exemple, il peut utiliser statut Vrai/faux si l’utilisateur se connecte ou non, ou la date d’expiration de son abonnement.

#### <a name="example-of-tags"></a>Exemple de balises

*Exemple d’utilisation : Segmenter comportement audience afin de cibler l’utilisateur final droite avec le contenu de notification push droite*

1.  Envoyer une notification push pour promouvoir une catégorie de produit : collecter les données de comportement audience segment basée sur la catégorie de produit qu’ils ont visités x heures dans une période donnée ou un élément spécifique qu’ils ont ajoutées dans un panier. Les données collectées vous permettra de segmenter et envoyer une notification push au public approprié.
2.  Évaluer l’application : collecter les données en fonction du contenu partagé par le public sur un réseau social. A pour but de segmenter l’audience en déterminant *ambassadeurs* de votre application. À l’aide d’une notification push-application, l’ambassadeurs sont l’audience meilleures de votre application à demander pour évaluer votre étoiles application 5 dans le magasin.

    ![][1]

*Exemple d’utilisation : Déclarative des données*
1.  Segment news alertes : collecter les données déclaratives audience segment en fonction de ses préférences. Il autorise l’envoi de notification d’émission d’un sujet spécifique qui vous intéresse vraiment une audience spécifique.
2.  Audience segment basé sur le statut de connexion. Collecter les données pour savoir si un utilisateur est connecté ou a créé un compte. Permet aux utilisateurs finaux cible qui n’ont pas encore connecté dans et envoie une notification push à promouvoir l’utilisateur final à convertir.
    ![][2]

### <a name="next-steps"></a>Étapes suivantes

- Visitez [Concepts Engagement Mobile] pour en savoir plus sur les concepts de base Mobile Engagement.
- Visitez [créer une application Engagement Mobile](mobile-engagement-create.md) pour créer une nouvelle application Collection Mobile Engagement dans Azure et commencer la gestion de vos applications grâce au portail Mobile Engagement.
- Visitez les [meilleures pratiques](mobile-engagement-getting-started-best-practices.md) pour accéder aux détails.
- Visitez le [scénario de l’application de jeu](mobile-engagement-gaming-scenario.md) pour en savoir plus sur l’implémentation d’Engagement Mobile avec un exemple d’application jeux. 
- Visitez le [scénario application support](mobile-engagement-media-scenario.md) pour en savoir plus sur l’implémentation d’Engagement Mobile avec un exemple d’application support. 
- Visitez [didacticiels] pour en savoir plus sur la mise en œuvre.

<!-- Images. -->
[1]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case1.png
[2]: ./media/mobile-engagement-define-your-mobile-engagement-strategy/use-case2.png

<!-- URLs. -->
[Concepts Engagement mobile]: http://azure.microsoft.com/documentation/articles/mobile-engagement-concepts/
[Didacticiels]: http://azure.microsoft.com/documentation/articles/mobile-engagement-ios-get-started/

