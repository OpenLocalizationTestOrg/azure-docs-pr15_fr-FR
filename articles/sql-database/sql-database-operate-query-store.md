<properties
   pageTitle="Fonctionnement du magasin de requête de base de données SQL Azure"
   description="Découvrez comment utiliser le magasin de requête dans la base de données SQL Azure"
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="08/16/2016"
   ms.author="carlrab"/>

# <a name="operating-the-query-store-in-azure-sql-database"></a>Fonctionnement du magasin de requête dans la base de données SQL Azure 

Requête Store dans Azure est une fonctionnalité de base de données entièrement gérée qui en permanence collecte et présente des informations détaillées historiques sur toutes les requêtes. Vous pouvez envisager banque de requête comme enregistreur de données de vol d’avion qui sensiblement simplifie la résolution des problèmes à la fois pour cloud les performances des requêtes et clients local. Cet article décrit les aspects spécifiques du fonctionnement requête Store dans Azure. À l’aide de ces données requête préalable recueillies, vous pouvez rapidement diagnostiquer et résoudre les problèmes de performances et par conséquent davantage de temps se concentrer sur leurs activités. 

Banque de requête a été [globalement disponibles](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) dans la base de données SQL Azure depuis novembre 2015. Magasin de requête est la base pour l’analyse des performances et optimisation des fonctionnalités, telles que [le Conseiller de base de données SQL et tableau de bord performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Pour le moment de la publication de cet article, banque de requête s’exécute en plus de 200 000 bases de données utilisateur dans Azure, collecte des informations liées à la requête pour plusieurs mois, sans interruption.

> [AZURE.IMPORTANT] Microsoft travaille actuellement à l’activation de magasin de requête pour toutes les bases de données SQL Azure (anciens et nouveaux). 

## <a name="optimal-query-store-configuration"></a>Configuration de la banque de requête optimale

Cette section décrit les valeurs par défaut configuration optimale qui ont été conçues pour garantir le fonctionnement fiable de la banque de requête et fonctionnalités dépendantes, telles que [le Conseiller de base de données SQL et tableau de bord performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Configuration par défaut est optimisée pour la collecte de données en continu, qui est un minimum de temps passé dans les États désactivé/READ_ONLY.

| Configuration | Description | Par défaut | Commentaire |
| ------------- | ----------- | ------- | ------- |
| MAX_STORAGE_SIZE_MB | Spécifie la limite de l’espace de données Store requête peut prendre à l’intérieur de base de données client z | 100 | Appliquées pour les nouvelles bases de données |
| INTERVAL_LENGTH_MINUTES | Définit la taille de la fenêtre de temps pendant lequel les statistiques d’exécution collectées pour les plans de requête sont agrégés et conservées. Chaque plan de requête active a au plus une ligne pour une période de temps défini à l’aide de cette configuration | 60   | Appliquées pour les nouvelles bases de données |
| STALE_QUERY_THRESHOLD_DAYS | Stratégie de nettoyage temporelles qui détermine la période de rétention des statistiques d’exécution permanentes et des requêtes inactives | 30 | Mises en œuvre pour les nouvelles bases de données et les bases de données avec précédent par défaut (367) |
| SIZE_BASED_CLEANUP_MODE | Spécifie si nettoyage automatique des données exécutée lorsque la limite est proche de taille des données de magasin de requête | Auto | Mises en œuvre pour toutes les bases de données |
| QUERY_CAPTURE_MODE | Spécifie si toutes les requêtes ou uniquement un sous-ensemble des requêtes sont suivies | Auto | Mises en œuvre pour toutes les bases de données |
| FLUSH_INTERVAL_SECONDS | Spécifie la durée maximale pendant laquelle capturé runtime statistiques sont conservées dans la mémoire, avant le vidage sur le disque | 900 | Appliquées pour les nouvelles bases de données |
||||||

> [AZURE.IMPORTANT] Ces paramètres par défaut sont appliquées automatiquement dans la dernière étape de l’activation de requête Store dans toutes les bases de données SQL Azure (voir précédente note importante). Après cette lumière vers le haut, base de données SQL Azure ne changez les valeurs de configuration définis par les clients, sauf si elles nuire la charge de travail principal ou opérations fiables de la banque de requête.

Si vous souhaitez utiliser vos paramètres personnalisés, permet de [Modifier la base de données avec les options de requête Store](https://msdn.microsoft.com/library/bb522682.aspx) pour rétablir configuration à l’état précédent. Consultez les [Meilleures pratiques avec le magasin de requête](https://msdn.microsoft.com/library/mt604821.aspx) afin de découvrir comment haut choisi les paramètres de configuration optimale.

## <a name="next-steps"></a>Étapes suivantes

[Performances un aperçu de la base de données SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Ressources supplémentaires

Pour plus d’informations extraire les articles suivants :

- [Un enregistreur de données de vol pour votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Analyse des performances à l’aide de la banque de requête](https://msdn.microsoft.com/library/dn817826.aspx)

- [Scénarios d’utilisation du magasin de requête](https://msdn.microsoft.com/library/mt614796.aspx)

- [Analyse des performances à l’aide de la banque de requête](https://msdn.microsoft.com/library/dn817826.aspx) 
