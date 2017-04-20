<properties 
   pageTitle="Performances un aperçu de la base de données SQL Azure | Microsoft Azure" 
   description="La base de données SQL Azure fournit des outils de performance pour vous aider à identifier les domaines qui peuvent d’améliorer les performances des requêtes en cours." 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="07/19/2016"
   ms.author="sstein"/>

# <a name="sql-database-performance-insight"></a>Performances un aperçu de la base de données SQL

Base de données SQL Azure fournit des outils de performance pour vous aider à identifier et améliorer les performances de vos bases de données en fournissant des recommandations et actions réglage intelligentes. 

1. Accédez à votre base de données dans le [Portail Azure](http://portal.azure.com) et cliquez sur **tous les paramètres** > **performances **  >  **vue d’ensemble** pour ouvrir la page de **performances** . 


2. Cliquez sur **recommandations** pour ouvrir le [Gestionnaire de base de données SQL](#sql-database-advisor), puis cliquez sur **requêtes** pour ouvrir [Un aperçu de performances de requête](#query-performance-insight).

    ![Afficher les performances](./media/sql-database-performance/entries.png)



## <a name="performance-overview"></a>Vue d’ensemble des performances

**Vue d’ensemble** ou sur la vignette de **performances** vous redirigera au tableau de bord performances pour votre base de données. Cet affichage fournit un récapitulatif des performances de votre base de données et vous aide à l’optimisation des performances et résolution des problèmes. 

![Performances](./media/sql-database-performance/performance.png)

- La vignette **recommandations** propose une analyse de l’optimisation des recommandations pour votre base de données (recommandations haut 3 apparaissent s’il existe plus). Cliquez sur cette vignette pour accéder à **Conseiller de base de données SQL**. 
- La vignette de **l’activité de réglage** fournit un résumé de l’en cours et terminées réglage des actions pour votre base de données, ce qui vous donne une vue rapide dans l’historique de réglage activité. Cliquez sur cette vignette pour accéder à la vue de l’historique de réglage complète pour votre base de données.
- La vignette **réglage automatique** montre la configuration de réglage automatique pour votre base de données (les actions de réglage des périphériques sont configurées pour être automatiquement appliquées à votre base de données). Cliquez sur cette vignette pour ouvrir la boîte de dialogue configuration automation.
- La vignette de **requêtes de base de données** affiche le résumé des performances des requêtes pour votre base de données (générale DTU supérieure et l’utilisation ressource par d’autres programmes requêtes). Cliquez sur cette vignette pour accéder **d’Analyse des performances de requête**.



## <a name="sql-database-advisor"></a>Conseiller de base de données SQL


[Conseiller de base de données SQL](sql-database-advisor.md) fournit des recommandations réglage intelligentes qui peuvent vous aider à améliorer les performances de votre base de données. 

- Recommandations sur lequel index à créer ou supprimer des (et une option pour appliquer automatiquement sans interaction de l’utilisateur et automatiquement annulation des recommandations qui ont une incidence négative sur les performances des recommandations d’index).
- Recommandations lors de problèmes de schéma sont identifiés dans la base de données.
- Recommandations lors de requêtes peuvent bénéficier de requêtes paramétrées.




## <a name="query-performance-insight"></a>Aperçu des performances de requête

[Aperçu des performances de requête](sql-database-query-performance.md) permet de passer moins de temps résolution des problèmes de performances de base de données en fournissant :

- Plus claires sur votre utilisation des ressources (DTU) bases de données. 
- UC supérieure par d’autres programmes requêtes potentiellement pouvant être réglé pour améliorer les performances. 
- La fonctionnalité permettant d’Explorer les détails d’une requête. 


## <a name="additional-resources"></a>Ressources supplémentaires

- [Conseils pour les performances de base de données SQL Azure des bases de données unique](sql-database-performance-guidance.md)
- [Quand un pool élastique de base de données doit être utilisé ?](sql-database-elastic-pool-guidance.md)