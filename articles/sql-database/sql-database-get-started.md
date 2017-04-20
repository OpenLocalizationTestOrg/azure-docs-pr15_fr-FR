<properties
    pageTitle="Didacticiel de base de données SQL : créer une base de données SQL | Microsoft Azure"
    description="Découvrez comment configurer un serveur logique de base de données SQL, serveur pare-feu règle, base de données SQL et exemples de données. En outre, découvrez comment vous connecter avec les outils clients, configurer des utilisateurs et configurer une règle de pare-feu de base de données."
    keywords="didacticiel de base de données SQL, créer une base de données sql"
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/07/2016"
    ms.author="carlrab"/>


# <a name="sql-database-tutorial-create-a-sql-database-in-minutes-by-using-the-azure-portal"></a>Didacticiel de base de données SQL : créer une base de données SQL en minutes à l’aide du portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Dans ce didacticiel, vous allez apprendre à utiliser le portail Azure à :

- Créer une base de données SQL Azure avec des exemples de données.
- Créer une règle de pare-feu au niveau du serveur pour une seule adresse IP ou une plage d’adresses IP.

Vous pouvez effectuer les mêmes tâches à l’aide de [c#](sql-database-get-started-csharp.md) ou [PowerShell](sql-database-get-started-powershell.md).

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]

<a name="create-logical-server-bk"></a>

## <a name="create-your-first-azure-sql-database"></a>Créer votre première base de données SQL Azure 

1. Si vous n’êtes pas actuellement connecté, vous connecter au [portail Azure](http://portal.azure.com).
2. Cliquez sur **Nouveau**et cliquez sur **données + stockage**, puis recherchez la **Base de données SQL**.

    ![Nouvelle base de données sql 1](./media/sql-database-get-started/sql-database-new-database-1.png)

3. Cliquez sur **Base de données SQL** pour ouvrir la carte de base de données SQL. Le contenu de cette carte varie selon le nombre de vos abonnements et vos objets existants (par exemple, les serveurs existants).

    ![Nouvelle base de données sql 2](./media/sql-database-get-started/sql-database-new-database-2.png)

4. Dans la zone de texte **nom de la base de données** , attribuez un nom à votre première base de données - par exemple « ma base de données ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 3](./media/sql-database-get-started/sql-database-new-database-3.png)

5. Si vous avez plusieurs abonnements, sélectionnez un abonnement.
6. Sous le **groupe de ressources**, cliquez sur **Créer nouveau** et donnez un nom à votre groupe de ressources première - par exemple « Mes--groupe de ressources ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 4](./media/sql-database-get-started/sql-database-new-database-4.png)

7. Sous **Sélectionner la source**, cliquez sur **exemples** , puis sous **Sélectionnez exemple** **AdventureWorksLT [V12]**.

    ![Nouvelle base de données sql 5](./media/sql-database-get-started/sql-database-new-database-5.png)

8. Sous **serveur**, cliquez sur **configurer les paramètres requis**.

    ![Nouvelle base de données sql 6](./media/sql-database-get-started/sql-database-new-database-6.png)

9. Dans la carte de serveur, cliquez sur **créer un nouveau serveur**. Une base de données SQL Azure est créée au sein d’un objet de serveur, qui peut être un nouveau serveur ou un serveur existant.

    ![Nouvelle base de données sql 7](./media/sql-database-get-started/sql-database-new-database-7.png)

10. Passez en revue la carte **nouveau serveur** pour mieux comprendre les informations que nécessaires pour fournir pour votre nouveau serveur.

    ![Nouvelle base de données sql 8](./media/sql-database-get-started/sql-database-new-database-8.png)

11. Dans la zone de texte **nom du serveur** , indiquez un nom pour votre premier serveur - par exemple « Mes-nouvelle-server-object ». Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 9](./media/sql-database-get-started/sql-database-new-database-9.png)
 
12. Sous **connexion au serveur d’administration**, fournissent un nom d’utilisateur pour la connexion de l’administrateur de ce serveur - par exemple « de compte mon administrateur ». Cette connexion est appelée à l’ouverture de session serveur principal. Une coche verte indique que vous avez fourni un nom valide.

    ![Nouvelle base de données sql 10](./media/sql-database-get-started/sql-database-new-database-10.png)

13. Sous **mot de passe** et **Confirmer le mot de passe**, fournissent un mot de passe pour le serveur de compte de connexion principal - telles que "p@ssw0rd1". Une coche verte indique que vous avez fourni un mot de passe valide.

    ![Nouvelle base de données sql 11](./media/sql-database-get-started/sql-database-new-database-11.png)
 
14. Sous **emplacement**, sélectionnez un centre de données correspondant à votre emplacement - tel que « Australie nord-est ».

    ![Nouvelle base de données sql 12](./media/sql-database-get-started/sql-database-new-database-12.png)

15. Sous ** V12 créer server (dernière mise à jour), notez que vous avez uniquement l’option pour créer une version actuelle de Azure SQL server.

    ![Nouvelle base de données sql 13](./media/sql-database-get-started/sql-database-new-database-13.png)

16. Notez que, par défaut, la case à cocher pour **Autoriser azure services pour accéder à server** est sélectionnée et ne peut pas être modifié ici. Il s’agit d’une option avancée. Vous pouvez modifier ce paramètre dans les paramètres du pare-feu serveur pour cet objet serveur, bien que la plupart des scénarios, il n’est pas nécessaire.

    ![Nouvelle base de données sql 14](./media/sql-database-get-started/sql-database-new-database-14.png)

17. Sur la nouvelle carte serveur, passez en revue vos sélections, puis sur **Sélectionner** pour sélectionner ce nouveau serveur pour votre nouvelle base de données.

    ![Nouvelle base de données sql 15](./media/sql-database-get-started/sql-database-new-database-15.png)

18. Dans la carte de base de données SQL, sous **tarification niveau**, cliquez sur **S2 Standard** , puis sur **base** pour choisir le niveau de tarification moins coûteux pour votre première base de données. Vous pouvez toujours modifier le niveau de tarification ultérieurement.

    ![Nouvelle base de données sql 16](./media/sql-database-get-started/sql-database-new-database-16.png)

19. Dans la carte de base de données SQL, passez en revue vos sélections, puis sur **créer** pour créer votre premier serveur et base de données. Les valeurs que vous avez fournies sont validées et déploiement démarre.

    ![Nouvelle base de données sql 17](./media/sql-database-get-started/sql-database-new-database-17.png)

20. Dans la barre d’outils du portail, cliquez sur les éléments de **Notifications** pour vérifier l’état de votre déploiement.

    ![Nouvelle base de données sql 18](./media/sql-database-get-started/sql-database-new-database-18.png)

>[AZURE.IMPORTANT]Lorsque le déploiement est terminé, votre nouveau serveur SQL Azure et la base de données sont créés dans Azure. Vous ne pouvez pas vous connecter à votre nouveau serveur ou la base de données à l’aide des outils SQL Server jusqu'à ce que vous créez une règle de pare-feu serveur pour ouvrir le pare-feu de base de données SQL pour les connexions depuis en dehors d’Azure.

[AZURE.INCLUDE [Create server firewall rule](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="next-steps"></a>Étapes suivantes
À présent que vous avez terminé ce didacticiel de base de données SQL et créé une base de données avec des exemples de données, vous êtes prêt à Explorer à l’aide de vos outils préférés.

- Si vous avez l’habitude de Transact-SQL et SQL Server Management Studio (SSMS), découvrez comment [se connecter et requête une base de données SQL SSMS](sql-database-connect-query-ssms.md).

- Si vous connaissez Excel, découvrez comment [se connecter à une base de données SQL Azure avec Excel](sql-database-connect-excel.md).

- Si vous êtes prêt à commencer à coder, sélectionnez votre langue programmation dans [les bibliothèques de connexions de base de données SQL et SQL Server](sql-database-libraries.md).

- Si vous voulez déplacer vos bases de données SQL Server en local sur Azure, voir la [migration de la base de données de base de données SQL](sql-database-cloud-migrate.md) pour en savoir plus.

- Si vous souhaitez charger des données dans une nouvelle table à partir d’un fichier CSV à l’aide de l’outil de ligne de commande BCP, voir [charger des données dans une base de données SQL à partir d’un fichier CSV à l’aide de BCP](sql-database-load-from-csv-with-bcp.md).

- Si vous voulez commencer à Explorer la sécurité de base de données SQL Azure, voir [prise en main de sécurité](sql-database-get-started-security.md)


## <a name="additional-resources"></a>Ressources supplémentaires

[Quelle est la base de données SQL ?](sql-database-technical-overview.md)
