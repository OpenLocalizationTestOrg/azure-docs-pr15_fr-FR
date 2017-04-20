<properties
    pageTitle="Surveiller le stockage en mémoire XTP | Microsoft Azure"
    description="Estimation et moniteur stockage en mémoire XTP utilisent, capacité ; résoudre l’erreur capacité 41823"
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="jodebrui"/>


# <a name="monitor-in-memory-oltp-storage"></a>Espace de stockage en mémoire OLTP moniteur

Lorsque vous utilisez [OLTP en mémoire](sql-database-in-memory.md), les données dans des tables de mémoire optimisée et variables de table résident dans stockage en mémoire OLTP. Chaque niveau de service Premium a une taille de stockage en mémoire OLTP maximale, qui est décrit dans l' [article de niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases). Une fois que cette limite est dépassée, insérer et mettre à jour les opérations peuvent démarrer ignore (avec l’erreur 41823). À ce stade serez soit supprimer des données pour récupérer la mémoire, ou que vous mettez à niveau le niveau de performances de votre base de données.

## <a name="determine-whether-data-will-fit-within-the-in-memory-storage-cap"></a>Déterminer si les données seront adaptée au sein de la lettrine de stockage en mémoire

Déterminer la lettrine stockage : consultez l' [article de niveaux de Service de base de données SQL](sql-database-service-tiers.md#service-tiers-for-single-databases) pour le texte en majuscules stockage des différents niveaux de service Premium.

Estimation des besoins de mémoire pour un works table mémoire optimisée la même façon pour SQL Server tel qu’il est le cas dans la base de données SQL Azure. Prendre quelques minutes pour examiner ce sujet sur [MSDN](https://msdn.microsoft.com/library/dn282389.aspx).

Notez que le tableau et lignes de la variable tableau, ainsi que les index, sont pris en compte la taille des données utilisateur max. En outre, ALTER TABLE doit suffisamment d’espace pour créer une nouvelle version de l’intégralité du tableau et ses index.

## <a name="monitoring-and-alerting"></a>Surveillance et alerte

Vous pouvez surveiller l’utilisation de stockage en mémoire sous forme de pourcentage de la [limiter de stockage pour votre niveau de performances](sql-database-service-tiers.md#service-tiers-for-single-databases) du [portail](https://portal.azure.com/)Azure : 

- Sur la carte de base de données, recherchez la zone de l’utilisation des ressources et cliquez sur Modifier.
- Puis sélectionnez la mesure `In-Memory OLTP Storage percentage`.
- Pour ajouter une alerte, cliquez sur la zone de l’utilisation des ressources pour ouvrir la carte métrique, puis cliquez sur Ajouter une alerte.

Ou la requête suivante permet de représenter l’utilisation du stockage en mémoire :

    SELECT xtp_storage_percent FROM sys.dm_db_resource_stats


## <a name="correct-out-of-memory-situations---error-41823"></a>Corriger des situations de mémoire insuffisante - erreur 41823

En cours d’exécution de mémoire insuffisante résultats dans les opérations d’insertion, mise à jour et créer ignore le message d’erreur 41823.

Message d’erreur 41823 indique que la mémoire optimisée des tables et des variables de table ont dépassé la taille maximale.

Pour corriger cette erreur, soit :


- Supprimer des données dans les tables de mémoire optimisée, potentiellement décharger les données aux tables traditionnels, sur le disque ; ou,
- Changer le niveau de service pour une avec suffisamment stockage en mémoire pour les données que vous souhaitez conserver dans les tables de mémoire optimisée.

## <a name="next-steps"></a>Étapes suivantes
Ressources supplémentaires sur à propos de la [surveillance de base de données SQL Azure à l’aide de vues de gestion dynamiques](sql-database-monitoring-with-dmvs.md)
