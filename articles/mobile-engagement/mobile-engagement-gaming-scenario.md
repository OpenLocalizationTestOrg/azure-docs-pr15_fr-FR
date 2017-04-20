<properties 
    pageTitle="Implémentation Mobile Engagement Azure application jeux"
    description="Scénario d’application pour mettre en œuvre Azure Mobile Engagement de jeu" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
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

#<a name="implement-mobile-engagement-with-gaming-app"></a>Mettre en œuvre Mobile Engagement avec application de jeu

## <a name="overview"></a>Vue d’ensemble

Un démarrage jeu a lancé une nouvelle application de jeu de role play/stratégie accrocheur en fonction. Le jeu a été en cours d’exécution pour 6 mois. Ce jeu est un succès considérable et qu’il possède des millions de téléchargements et la rétention est très haute par rapport aux autres applications de jeu de démarrage. Lors de la réunion de révision trimestrielles, les parties prenantes conviennent que dont elles ont besoin augmenter le chiffre d’affaires moyen par utilisateur (revenu moyen par abonné). Dans le jeu Premium sont disponibles en tant que des offres spéciales. Ces packs jeu autoriser les utilisateurs à mettre à niveau l’apparence et les performances de leurs lignes accrocheur et appâts ou de s’attaque dans le jeu. Toutefois, les ventes de package sont très faibles. Ils décident tout d’abord analyser l’expérience client avec un outil analytique, et puis pour développer un engagement de programme pour augmenter à l’aide de ventes avancées segmentation.

Basé sur l' [Azure Mobile Engagement - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md) ils créent une stratégie d’engagement.

##<a name="objectives-and-kpis"></a>Objectifs et indicateurs de performance clés

Les principales parties prenantes pour le jeu rencontre. Tout conviennent d’un objectif principal - pour accroître les ventes de package premium par 15 %. Ils créent des indicateurs de Performance entreprise clés (KPI) à mesurer et amener cet objectif

* Sur quel niveau de la partie ces packages achetées ?
* Quel est le chiffre d’affaires par utilisateur, par session, par semaine et par mois ?
* Quels sont les types d’achat favoris ?

1E partie du [Guide de démarrage](mobile-engagement-getting-started-best-practices.md) explique comment définir les objectifs et les indicateurs de performance clés. 

Avec des indicateurs de performance clés entreprise maintenant défini, le Gestionnaire de produit Mobile crée Engagement indicateurs de performance clés pour déterminer la rétention et les tendances de nouvel utilisateur.

* Surveillez rétention et utiliser les intervalles suivants : tous les jours, tous les 2 jours, hebdomadaire, mensuelle et tous les trois mois
* Nombre d’utilisateurs actifs
* Le niveau d’application dans le magasin

Selon les recommandations de l’équipe informatique, les indicateurs de performance clés techniques suivantes ont été ajoutés pour répondre aux questions suivantes :

* Quel est mon chemin d’accès utilisateur (quelle page visitée, comment les utilisateurs débutants consacrent dessus)
* Nombre de se bloque ou bogues rencontrés par session
* Quelles sont les versions du système d’exploitation exécutez mes utilisateurs ?
* Quelle est la taille moyenne de l’écran pour mes utilisateurs ?
* Quel type de la connectivité internet dois-je mes utilisateurs ?

Chaque indicateur de performance clé le Gestionnaire de produit Mobile spécifie les données elle doit et son emplacement dans son manuel.

## <a name="engagement-program-and-integration"></a>Intégration et programme engagement

Avant de générer un programme engagement avancées, le directeur de projet Mobile responsable du projet doit avoir une bonne connaissance de l’et produits sont utilisés par les utilisateurs.

Après 3 mois, le directeur de projet Mobile collecte suffisamment d’informations pour améliorer ses ventes de notification push intégrée dans l’application. Il apprend au fur qui :

* Le premier achat généralement qui se produit au niveau du 14. 90 % des cas, l’achat concerne nouvelles armes reposant $3.
* 80 % des cas, les utilisateurs ayant effectué un achat, continuez avec le produit et effectuez plus achats.
* Les utilisateurs qui se sont écoulés le niveau 20, démarrer intégralement supérieure à $10/ semaine.
* Les utilisateurs ont tendance à acheter packages premium au niveau de 16, 24 et 32.

Grâce à cette analyse le directeur de projet Mobile décide de créer push spécifique séquences de notification pour augmenter dans les ventes de l’application. Il crée trois séquences push qu’il appelle : Bienvenue dans le programme, de programme de vente et programme inactif. Pour plus d’informations, reportez-vous aux [règles](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
