<properties
    pageTitle="Suivi des modifications solution dans journal Analytique | Microsoft Azure"
    description="Vous pouvez utiliser la solution de suivi des modifications de Configuration dans journal Analytique pour vous aider à identifier facilement des logiciels et les modifications de Services de Windows qui se produisent dans votre environnement — identifiant ces modifications de configuration peut vous aider à identifier les problèmes de fonctionnement."
    services="operations-management-suite"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operations-management-suite"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="change-tracking-solution-in-log-analytics"></a>Modifier la solution de suivi dans le journal Analytique


Vous pouvez utiliser la solution de suivi des modifications de Configuration dans le journal Analytique pour vous aider à identifier facilement des logiciels et Services Windows modifications et les modifications de processus de Linux qui se produisent dans votre environnement — identifiant ces modifications de configuration peut vous aider à identifier les problèmes de fonctionnement.

Vous installez la solution pour mettre à jour le type de l’agent que vous avez installés. Modifications apportées aux installé un logiciel de lire les services Windows sur les serveurs analysés et puis les données sont envoyées au service de journal Analytique dans le cloud pour traitement. Logique est appliquée aux données reçues et le service cloud enregistre les données. Lorsque des modifications sont trouvées, serveurs avec les modifications apparaissent dans le tableau de bord de suivi des modifications. En utilisant les informations du tableau de bord de suivi des modifications, vous pouvez facilement repérer les modifications apportées à votre infrastructure de serveur.

## <a name="installing-and-configuring-the-solution"></a>Installation et configuration de la solution
Utilisez les informations suivantes pour installer et configurer la solution.

- Operations Manager est requis pour la solution de suivi des modifications.
- Vous devez disposer un agent Windows ou Operations Manager sur chaque ordinateur sur lequel vous souhaitez pour surveiller les modifications.
- Ajouter la solution de suivi des modifications dans votre espace de travail OMS à l’aide de la procédure décrite dans [solutions ajouter journal Analytique à partir de la galerie de Solutions](log-analytics-add-solutions.md).  Il n’existe aucune autre configuration requise.


## <a name="change-tracking-data-collection-details"></a>Modifier les détails de collection de données de suivi

Suivi des modifications collecte inventaire logiciel et les métadonnées de Service Windows à l’aide d’agents que vous avez activés.

Le tableau suivant montre les méthodes de collecte de données et autres informations sur la façon dont les données sont collectées pour le suivi des modifications.

| plateforme | Agent directe | Agent SCOM | Stockage Azure | SCOM obligatoire ? | Données de l’agent SCOM envoyées par groupe d’administration | fréquence de collection de sites |
|---|---|---|---|---|---|---|
|Windows|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)|![N°](./media/log-analytics-change-tracking/oms-bullet-red.png)|            ![N°](./media/log-analytics-change-tracking/oms-bullet-red.png)|![Oui](./media/log-analytics-change-tracking/oms-bullet-green.png)| toutes les heures|

## <a name="use-change-tracking"></a>Utiliser le suivi des modifications

Une fois la solution est installée, vous pouvez afficher le résumé des modifications pour vos serveurs analysés à l’aide de la vignette de **Suivi des modifications** dans la page **vue d’ensemble** dans OMS.

![image d’une vignette de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking-tile.png)

Vous pouvez afficher les modifications apportées à votre infrastructure, puis sur Détails dans une exploration des niveaux pour les catégories suivantes :

- Modifications apportées par type de configuration pour les logiciels et services Windows
- Logiciel changements aux applications et mises à jour pour les serveurs individuels
- Nombre total de modifications logicielles pour chaque application
- Modifications du service Windows pour les serveurs individuels

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking01.png)

![image du tableau de bord de suivi des modifications](./media/log-analytics-change-tracking/oms-changetracking02.png)

### <a name="to-view-changes-for-any-change-type"></a>Pour afficher les modifications pour les modifier le type

1. Dans la page **vue d’ensemble** , cliquez sur la vignette de **Suivi des modifications** .
2. Dans le tableau de bord **Modifier suivi** , passez en revue les informations de synthèse dans un des cartes de type de modification, puis sur un pour afficher des informations détaillées dans la page de **recherche dans un journal** .
3. Sur n’importe lequel des pages de recherche de journal, vous pouvez afficher des résultats par heure, les résultats détaillés et votre historique de recherche de journal. Vous pouvez également filtrer par facettes pour limiter les résultats.

## <a name="next-steps"></a>Étapes suivantes

- [Recherches de journaux dans journal Analytique](log-analytics-log-searches.md) permet d’afficher des données de suivi des modifications détaillées.
