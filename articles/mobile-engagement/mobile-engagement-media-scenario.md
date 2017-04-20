<properties 
    pageTitle="Implémentation Mobile Engagement Azure Media application"
    description="Scénario d’application multimédia pour mettre en œuvre Azure Mobile Engagement" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>Mettre en œuvre Mobile Engagement avec l’application multimédia

## <a name="overview"></a>Vue d’ensemble

John est responsable de projet mobile société multimédia volumineux. Il a récemment lancé une nouvelle application qui comporte un compteur de téléchargement très haute. Il a atteint sa objectifs du téléchargement, mais toujours son retour sur Investment(ROI) par utilisateur ne respecte pas ses exigences. 

John a déjà identifié pourquoi son retour sur investissement est trop bas. Utilisateurs arrêter fréquemment à l’aide de son application uniquement 2 semaines et la plupart d'entre eux jamais revenir. Il souhaite augmenter la rétention de son application.

Une fois que certains initiale test, qu’il a apprises lorsqu’il s’engage ses utilisateurs avec les notifications push, ils tendent à continuer à utiliser son application. Également les utilisateurs qui ont été inactifs renverra souvent à l’application selon qu’il les envoie des notifications. John décide d’investir dans un certain du programme d’Engagement pour son application qui utilise un ciblage avancées avec les notifications push.

John a récemment lire les [Azure Mobile Engagement - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md) et a décidé d’implémenter les recommandations à partir du guide.

##<a name="objectives-and-kpis"></a>Objectifs et indicateurs de performance clés

Les principales parties prenantes pour l’application de John rencontre. Entreprise est généré à partir des annonces que les utilisateurs utiliser son multimédia. En augmentant le contenu consommé par utilisateur, John augmente son chiffre d’affaires. Tous les approbateurs acceptent sur l’un des objectifs principaux : À accroître les ventes des annonces de 25 %. Ils créent des indicateurs de Performance entreprise clés (KPI) à mesurer et amener cet objectif

* Nombre d’annonces cliqué par utilisateur
* Nombre de pages article visités (par utilisateur / session / par semaine /mois par...)
* Quelles sont les catégories favoris

En fonction de la réunion de John avec les principales parties prenantes il a défini son KPI d’entreprise. Il suit partie 1 de l' [Azure Mobile Engagement - Guide de démarrage rapide avec les meilleures pratiques](mobile-engagement-getting-started-best-practices.md). 

Ensuite, il crée le KPI Engagement suivants pour vous assurer que des objectifs :

* Surveiller l’activité rétention sur les intervalles suivants : quotidienne, hebdomadaire, aux deux semaines et mensuels.
* Nombre d’utilisateurs actifs
* Enregistre le niveau d’application dans l’application

Selon les recommandations de l’équipe informatique, les indicateurs de performance clés techniques suivantes ont été ajoutés pour répondre aux questions suivantes :

* Quel est mon chemin d’accès utilisateur (quelle page visitée, combien d’utilisateurs temps passé sur il)
* Nombre de se bloque ou bogues rencontrés par session ?
* Quelles sont les versions du système d’exploitation exécutez mes utilisateurs ?
* Quelle est la taille moyenne de l’écran pour mes utilisateurs ?
* Quel type de connexions internet dois-je mes utilisateurs ?

Pour chaque indicateur de performance clé, il considère les données nécessaires et il enregistre dans l’emplacement approprié de son manuel.

## <a name="engagement-program-and-integration"></a>Intégration et programme engagement

À présent que John a terminé de définir son indicateurs de performance clés, il commence sa phase de stratégie Engagement en définissant 4 engagement programmes et leurs objectifs :    ![][1]

Puis John va plus approfondie en détaillant les notifications push pour chaque programme. Notifications Push sont définies par cinq éléments :

1. Objectif : quel est l’objectif de la notification
2. Comment l’objectif sera atteint
3. Cible : qui reçoivent une notification ?
4. Content : Quelle est la formulation et le format de la notification (dans App/Out d’application)
5. Quand : qu’est le moment meilleures envoyer cette notification push

    ![][2]

Pour plus d’informations, reportez-vous aux [règles](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Conformément à la partie 2 du livre blanc John utilise la section cible pour définir les données qu’il comporte pour recueillir et écrit planifier sa balise conjointement avec l’équipe informatique à mettre en œuvre la solution. 1 semaine de l’implémentation et le test d’acceptation utilisateur, John pouvez lancer enfin son programmes.

##<a name="program-results"></a>Résultats du programme

4 mois plus tard, John examine les performances des programmes. Le programme accueil et le programme hebdomadaire sont les objectifs son. Le nombre d’utilisateurs avec une seule session diminue, plus de fonctionnalités de l’application sont utilisés et le nombre de connexions par semaine a double.

Le **Programme Inactive** est d’aider John comprendre les tendances de l’utilisateur. Il semble que 15 % des utilisateurs inactifs y revenir à l’application. Toutefois la plupart d'entre elles ne reste active plus de 1 mois. John prévoit une optimisation potentielle de cette séquence avec des notifications supplémentaires et développer son contenu choix.

Le **Programme découvrir** ne fonctionne pas correctement. Il augmente la vente mais pas assez pour atteindre ses objectifs croisée. John identifie que le destinataire ne dispose de suffisamment de données pour rendre pertinents ciblage et proposer un contenu approprié. Il s’arrête ce programme et se concentre sur l’envoi de « notifications push éditoriale » avec Azure Mobile Engagement. Son journalistes disposent déjà d’une solution CMS pour envoyer les notifications push et ne voulez pas modifier.

John décide d’utiliser l’API atteigne qui est une API REST HTTP qui permet de gérer des campagnes marketing accessibles sans utiliser l’interface AZME Web. Cette approche John pouvez collecter les données qu’il a besoin et autorise les son auteurs à garder à l’aide de la solution CMS.

Pour vous assurer que cette fonctionnalité fonctionne correctement, John vous demande d’équipe informatique en permanence sur les points suivants :

1. **Systèmes d’exploitation** : ont tous leurs propres règles pour gérer les notifications push, afin de John décide de consulter tous les cas et vérifie si les API traitez-le.
Par exemple : Push Android système permet de vue d’ensemble qui n’est pas le cas avec iOS.

2. **Intervalle de temps**: John souhaite d’API, laquelle définie l’intervalle de temps et de définie une fin aux campagnes. Il souhaite conserver les utilisateurs à partir de n’importe quel bombing notification interruption de service.

3. **Catégories**: équipe Marketing prépare modèle pour chaque type d’alerte. John vous demande d’équipe informatique pour définir les catégories à l’intérieur de l’API.

Une fois que certains tests John est satisfait. Grâce à cette API, journalistes peuvent toujours envoyer les notifications push avec leurs CMS et Azure Mobile Engagement collecte toutes les données comportement pour les

Après ces 4 tout d’abord mois, résultats reflètent une bonne globale performances et donne la confiance pour John et sa carte, retour sur investissement par utilisateur augmente par 15 % et ventes mobiles représentent 17,5 % du total des ventes, une augmentation des pourcentages 7.5 quatre mois seulement.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
