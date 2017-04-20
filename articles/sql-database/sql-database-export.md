<properties
    pageTitle="Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure"
    description="Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="08/15/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="archive-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Archiver une base de données SQL Azure vers un fichier à DOS à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Cet article fournit des instructions pour l’archivage de votre base de données SQL Azure vers un fichier à DOS (stocké dans le stockage blob Azure) à l’aide du [portail Azure](https://portal.azure.com).

Lorsque vous avez besoin créer une archive d’une base de données SQL Azure, vous pouvez exporter le schéma de base de données et les données dans un fichier à DOS. Un fichier à DOS est simplement un fichier ZIP avec une extension d’à DOS. Un fichier à DOS ultérieurement pouvant être stocké dans le stockage blob Azure ou dans le stockage local dans un emplacement en local et plus tard importées précédent dans la base de données SQL Azure ou dans un serveur SQL Server locale d’installation. 

***Considérations relatives à la***

- Pour une archive être cohérentes, vous ne devez vous assurer qu’aucune écriture activité est en cours lors de l’exportation, ou que vous exportez à partir d’une [copie cohérente](sql-database-copy.md) de votre base de données SQL Azure.
- La taille maximale d’un fichier à DOS archivé à Azure Blob storage est 200 Go. Pour archiver un fichier à DOS plus volumineux sur le stockage local, exécutez l’utilitaire d’invite de commandes [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Cet utilitaire est fourni avec Visual Studio et SQL Server. Vous pouvez également [Télécharger](https://msdn.microsoft.com/library/mt204009.aspx) la dernière version de SQL Server Data Tools pour obtenir cet utilitaire.
- Archivage au stockage Azure premium à l’aide d’un fichier à DOS n’est pas pris en charge.
- Si l’opération d’exportation dépasse 20 heures, elle peut être annulée. Pour améliorer les performances lors de l’exportation, vous pouvez :
 - Augmenter temporairement le niveau du service.
 - Arrêt tous les lire et écrire des activités lors de l’exportation.
 - Utiliser un [index groupé](https://msdn.microsoft.com/library/ms190457.aspx) avec des valeurs non null sur toutes les tables de grande taille. Sans index groupés, une exportation peut échouer s’il faut plu de 6 à 12 heures. C’est parce que le service d’exportation doit effectuer une analyse de table pour tenter d’exporter le tableau entier. Un bon moyen de déterminer si vos tables sont optimisés pour l’exportation consiste à exécuter **DBCC SHOW_STATISTICS** et vérifiez que la *RANGE_HI_KEY* n’est pas null et sa valeur a distribution appropriée. Pour plus d’informations, voir [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).


> [AZURE.NOTE] BACPACs ne sont pas destinées à être utilisé pour la sauvegarde et de restauration opérations. Base de données SQL Azure crée automatiquement des sauvegardes pour chaque base de données utilisateur. Pour plus d’informations, voir [Vue d’ensemble de la continuité d’entreprise](sql-database-business-continuity.md).

Pour terminer cet article vous besoin des éléments suivants :

- Un abonnement Azure.
- Une base de données SQL Azure. 
- Un [compte de stockage Standard Azure](../storage/storage-create-storage-account.md) avec un conteneur blob pour stocker l’à dos de stockage standard.

## <a name="export-your-database"></a>Exporter votre base de données

Ouvrez la carte de base de données SQL pour la base de données que vous voulez exporter.

> [AZURE.IMPORTANT] Pour garantir un fichier à DOS cohérent, vous devez tout d’abord [créer une copie de votre base de données](sql-database-copy.md) et puis exportez la copie de la base de données. 

1.  Accédez au [portail Azure](https://portal.azure.com).
2.  Cliquez sur **bases de données SQL**.
3.  Cliquez sur la base de données à archiver.
4.  Dans la carte de base de données SQL, cliquez sur **Exporter** pour ouvrir la carte **Exporter la base de données** :

    ![bouton Exporter][1]

5.  Cliquez sur **stockage** et sélectionnez votre compte et blob réservoir où l’à DOS sera stocké :

    ![Exporter la base de données][2]

6. Sélectionnez le type d’authentification. 
7.  Entrez les informations d’identification d’authentification appropriée pour le serveur SQL Azure qui contient la base de données que vous exportez.
8.  Cliquez sur **OK** pour archiver la base de données. Cliquer sur **OK** crée une requête de base de données exporter et il a été envoyé au service. La durée de que l’exportation prendra dépend de la taille et la complexité de votre base de données et le niveau du service. Vous recevrez une notification.

    ![notification d’exportation][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Surveiller l’avancement de l’opération d’exportation

1.  Cliquez sur **SQL Server**.
2.  Cliquez sur le serveur qui contient la base de données (source) d’origine que vous venez d’archivage.
3.  Faites défiler jusqu'à opérations.
4.  Dans l’instruction SQL carte serveur, cliquez sur **Importer/exporter historique**:

    ![importer, exporter l’historique][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Vérifiez que la à DOS se trouve dans le conteneur de votre espace de stockage

1.  Cliquez sur **comptes de stockage**.
2.  Cliquez sur le compte de stockage où vous avez enregistré l’archive à DOS.
3.  Cliquez sur **conteneurs** et sélectionnez le conteneur que vous avez exporté la base de données dans pour plus d’informations (vous pouvez télécharger et enregistrer l’à DOS à partir de là).

    ![Détails du fichier .bacpac][5]  

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur l’importation à un DOS dans une base de données SQL Azure, voir [Importer un BACPCAC à une base de données SQL Azure](sql-database-import.md)
- Pour en savoir plus sur l’importation à un DOS dans une base de données SQL Server, voir [Importer un BACPCAC à une base de données SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)



<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png

