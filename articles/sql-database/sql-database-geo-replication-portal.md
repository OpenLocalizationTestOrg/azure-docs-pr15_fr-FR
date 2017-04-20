<properties 
    pageTitle="Configurer la Geo réplication de base de données SQL Azure avec le portail Azure | Microsoft Azure" 
    description="Configurer la Geo-réplication de base de données SQL Azure à l’aide du portail Azure" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="NA"
    ms.date="10/18/2016"
    ms.author="sstein"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurer la Geo réplication de base de données SQL Azure avec le portail Azure


> [AZURE.SELECTOR]
- [Vue d’ensemble](sql-database-geo-replication-overview.md)
- [Portail Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Cet article vous explique comment configurer Active Geo-réplication de base de données SQL grâce au [portail Azure](http://portal.azure.com).

Pour déclencher le basculement grâce au portail Azure, voir [lancer un basculement planifié ou de base de données SQL Azure avec le portail Azure](sql-database-geo-replication-failover-portal.md).

>[AZURE.NOTE] Réplication Geo active (lisibles secondaires) est désormais disponible pour toutes les bases de données de tous les niveaux de service. Dans avril 2017, le type secondaire illisible validité prend fin et bases de données non lisible existantes passeront automatiquement vers secondaires lisibles.

Pour configurer Geo-réplication à l’aide du portail Azure, vous devez la ressource suivante :

- Une base de données SQL Azure - la base de données principale que vous souhaitez répliquer dans une autre région géographique.

## <a name="add-secondary-database"></a>Ajouter une base de données secondaire

Les étapes suivantes créent une nouvelle base de données secondaire dans un partenariat Geo réplication.  

Pour ajouter un secondaire, vous devez être le propriétaire de l’abonnement ou copropriétaire. 

La base de données secondaire a le même nom que la base de données principale et créera par défaut, le même niveau de service. La base de données secondaire peut être une seule base de données ou une base de données élastique. Pour plus d’informations, voir [Niveaux de Service](sql-database-service-tiers.md).
Une fois l’image secondaire est créée et mises en culture, données commenceront réplication à partir de la base de données principale vers la nouvelle base de données secondaire. 

> [AZURE.NOTE] Si la base de données partenaire existe déjà (par exemple - résulte termine une relation Geo réplication précédente) la commande échoue.

### <a name="add-secondary"></a>Ajouter le moniteur secondaire

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données que vous souhaitez configurer pour la réplication de Geo.
2. Dans la page de base de données SQL, sélectionnez **Geo réplication**, puis la région pour créer la base de données secondaire. Vous pouvez sélectionner n’importe quelle région différent de la région qui héberge la base de données principale, mais la [région appariée](../best-practices-availability-paired-regions.md) est recommandé.

    ![configurer la réplication de Geo](./media/sql-database-geo-replication-portal/configure-geo-replication.png)


4. Sélectionner ou configurer le serveur et le niveau de tarification pour la base de données secondaire.

    ![configurer le moniteur secondaire](./media/sql-database-geo-replication-portal/create-secondary.png)

5. Si vous le souhaitez, vous pouvez ajouter une base de données secondaire à un pool élastique de base de données :

 Pour créer la base de données secondaire dans une liste, cliquez sur **base de données élastique pool** et sélectionner un pool sur le serveur cible. Un pool doit déjà exister sur le serveur cible comme ce flux de travail ne crée pas un pool.

6. Cliquez sur **créer** pour ajouter le moniteur secondaire.
 
6. La base de données secondaire est créé et le processus d’amorçage commence. 
 
    ![configurer le moniteur secondaire](./media/sql-database-geo-replication-portal/seeding0.png)

7. Une fois le processus d’amorçage terminée, la base de données secondaire affiche son statut.

    ![ensemencement complète](./media/sql-database-geo-replication-portal/seeding-complete.png)


## <a name="remove-secondary-database"></a>Supprimer la base de données secondaire

L’opération termine la réplication de base de données secondaire définitivement et change le rôle de secondaire à une base de données en lecture / écriture normal. Si la connectivité à la base de données secondaire est interrompue, la commande a réussi, mais l’est secondaire ne devient en lecture / écriture jusqu'à ce que la connectivité est restauré.  

1. Dans le [portail Azure](http://portal.azure.com), accédez à la base de données principale du partenariat Geo réplication.
2. Dans la page de base de données SQL, sélectionnez **Geo réplication**.
3. Dans la liste **secondaires** , sélectionnez la base de données que vous voulez supprimer le partenariat Geo réplication.
4. Cliquez sur **Arrêter la réplication**.

    ![supprimer le moniteur secondaire](./media/sql-database-geo-replication-portal/remove-secondary.png)

5. Cliquez sur **Arrêter la réplication** ouvre une fenêtre de confirmation cliquez sur **Oui** pour supprimer la base de données à partir du partenariat Geo réplication (définissez-la à une base de données en lecture / écriture ne faisant pas partie de toute réplication).


## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur Geo-réplication Active, consultez - [Geo-réplication](sql-database-geo-replication-overview.md) Active
- Pour une vue d’ensemble de la continuité de gestion et scénarios, voir [vue d’ensemble de la continuité de gestion](sql-database-business-continuity.md)

