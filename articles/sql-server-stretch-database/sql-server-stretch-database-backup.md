<properties
    pageTitle="Sauvegarder les bases de données compatibles avec étirement | Microsoft Azure"
    description="Découvrez comment faire pour sauvegarder étirement\-activé les bases de données."
    services="sql-server-stretch-database"
    documentationCenter=""
    authors="douglaslMS"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-server-stretch-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/14/2016"
    ms.author="douglasl"/>

# <a name="backup-stretch-enabled-databases"></a>Bases de données compatibles avec étirement de sauvegarde

Sauvegardes de base de données vous aident à récupérer de nombreux types d’échecs, des erreurs et des incidents.  

-   Vous devez sauvegarder votre étirement\-activé les bases de données SQL Server.  

-   Microsoft Azure sauvegarde automatiquement les données distantes étirement de base de données a migré à partir de SQL Server à Azure.  

>    [AZURE.NOTE] Sauvegarde est uniquement une partie d’une disponibilité complète et la solution de continuité. Pour plus d’informations sur la disponibilité, voir [Haute disponibilité des Solutions](https://msdn.microsoft.com/library/ms190202.aspx).

## <a name="back-up-your-sql-server-data"></a>Sauvegarder vos données SQL Server  

Pour sauvegarder votre étirement\-activé les bases de données SQL Server, vous pouvez continuer à utiliser les méthodes de sauvegarde de SQL Server que vous utilisez actuellement. Pour plus d’informations, voir [sauvegarder et restaurer de SQL Server bases de données](https://msdn.microsoft.com/library/ms187048.aspx).

Sauvegarder une base de données compatibles avec étirement SQL Server contiennent uniquement les données locales et les données éligibles pour la migration à l’endroit dans le temps lorsque la sauvegarde s’exécute. \(Données éligibles sont des données qui n’a pas encore été migrées, mais qui vont être migrées vers Azure selon les paramètres de migration des tables.\) Il est convenu d’une sauvegarde **partielle** et n’inclut pas les données déjà migré vers Azure.  

## <a name="back-up-your-remote-azure-data"></a>Sauvegarder vos données Azure à distance   

Microsoft Azure sauvegarde automatiquement les données distantes étirement de base de données a migré à partir de SQL Server à Azure.  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure réduit le risque de perte de données avec la sauvegarde automatique  
Le service de base de données SQL Server étirer sur Azure protège vos bases de données distantes avec des instantanés de stockage automatique au moins toutes les 8 heures. Elle conserve chaque instantané pendant 7 jours à vous fournir une plage de points de restauration possible.  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure réduit le risque de perte de données avec geo\-redondance  
Base de données Azure sauvegardes sont stockées sur geo\-stockage Azure redondants (RA\-GRS) et sont donc geo\-redondants par défaut. Geo\-stockage redondants réplique vos données sur une zone secondaire des centaines de miles en dehors de la région principale. Dans les zones principales et secondaires, vos données sont répliquées trois fois chacune, sur défaillance séparé les domaines et mise à niveau. Cela garantit que vos données sont résistants même en cas de panne régionale complète ou de sinistre qui rend l’une des régions Azure indisponible.

### <a name="stretchRPO"></a>Base de données étirement réduit le risque de perte de données pour vos données Azure en conservant les lignes migrés temporairement
Une fois que la base de données étirement migre lignes éligibles à partir de SQL Server vers Azure, elle conserve les lignes dans la table intermédiaire pour un minimum de 8 heures. Si vous restaurez une sauvegarde de votre base de données Azure, base de données étirement utilise les lignes enregistrées dans la table intermédiaire rapprocher SQL Server et les bases de données Azure.

Une fois que vous restaurez une sauvegarde de vos données Azure, vous devez exécuter la procédure stockée [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) pour vous reconnecter l’étirement\-activé base de données SQL Server pour la base de données Azure à distance. Lorsque vous exécutez **sys.sp_rda_reauthorize_db**, base de données étirement résout automatiquement SQL Server et les bases de données Azure.

Pour augmenter le nombre d’heures de données migrées étirement de base de données conserve temporairement dans la table intermédiaire, exécutez la procédure stockée [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) et spécifiez un nombre d’heures supérieures à 8. Pour déterminer la quantité de données à conserver, tenez compte des facteurs suivants :
-   La fréquence des sauvegardes Azure automatiques (au moins toutes les heures 8).
-   Le temps nécessaire après un problème pour identifier le problème et décider de restaurer une sauvegarde.
-   La durée de la restauration Azure.

> [AZURE.NOTE] Augmenter la quantité de données étirement de base de données conserve temporairement dans la table intermédiaire augmente la quantité d’espace sur le serveur SQL Server.

Pour vérifier le nombre d’heures de données en base de données étirement conserve actuellement temporairement dans la table intermédiaire, exécutez la procédure stockée [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx).

## <a name="see-also"></a>Voir aussi

[Gérer et résoudre les problèmes de base de données étirement](sql-server-stretch-database-manage.md)

[Sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[Sauvegarder et restaurer des bases de données SQL Server](https://msdn.microsoft.com/library/ms187048.aspx)
