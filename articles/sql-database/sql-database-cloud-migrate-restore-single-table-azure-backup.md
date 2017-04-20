<properties
    pageTitle="Restaurer une seule table à partir de sauvegarde de la base de données SQL Azure | Microsoft Azure"
    description="Découvrez comment restaurer une seule table à partir de la sauvegarde de la base de données SQL Azure."
    services="sql-database"
    documentationCenter=""
    authors="dalechen"
    manager="felixwu"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/31/2016"
    ms.author="daleche"/>


# <a name="how-to-restore-a-single-table-from-an-azure-sql-database-backup"></a>Comment restaurer une seule table à partir d’une sauvegarde de base de données SQL Azure

Vous pouvez rencontrer une situation dans laquelle vous avez modifié par inadvertance des données dans une base de données SQL et maintenant que vous souhaitez récupérer la seule table concernée. Cet article décrit comment restaurer une table unique dans une base de données à partir d’une des [sauvegardes automatiques](sql-database-automated-backups.md)de base de données SQL.

## <a name="preparation-steps-rename-the-table-and-restore-a-copy-of-the-database"></a>Étapes de préparation : renommer la table et de restaurer une copie de la base de données
1. Identifiez la table dans votre base de données SQL Azure que vous souhaitez remplacer par la copie restaurée. Utilisez Microsoft SQL Management Studio pour renommer la table. Par exemple, renommer la table en tant que &lt;nom de la table&gt;_old.

    **Remarque** Pour éviter tout blocage, assurez-vous qu’il n’existe aucune activité en cours d’exécution sur la table que vous renommez. Si vous rencontrez des problèmes, vérifiez qu’effectuer cette procédure pendant une période de maintenance.

2. Restaurer une sauvegarde de votre base de données à un point dans le temps que vous voulez récupérer à l’aide de la procédure [Point In_Time restaurer](sql-database-recovery-using-backups.md#point-in-time-restore) .

    **Notes**:
    - Le nom de la base de données restaurée sera au format DBName + horodatage ; par exemple, **Adventureworks2012_2016-01-01T22-12Z**. Cette étape ne remplacer le nom de la base de données existant sur le serveur. Il s’agit d’une mesure de sécurité, et il a destinés à vous permettent de vérifier la base de données restaurée avant de supprimer leur base de données active et renommer la base de données restaurée de production.
    - Tous les niveaux de performances de base à Premium sont automatiquement sauvegardés par le service, avec des variables métriques de rétention de sauvegarde, selon le niveau :

| Restauration de la base de données | Niveau de base | Niveaux standard | Niveaux Premium |
| :-- | :-- | :-- | :-- |
|  Restaurer du point dans le temps |  Un point de restauration sous 7 jours|Un point de restauration au sein de 35 jours| Un point de restauration au sein de 35 jours|

## <a name="copying-the-table-from-the-restored-database-by-using-the-sql-database-migration-tool"></a>Copiez le tableau à partir de la base de données restaurée à l’aide de l’outil de Migration de base de données SQL
1. Téléchargez et installez l' [Assistant de Migration de base de données SQL](https://sqlazuremw.codeplex.com).

2. Ouvrir l’Assistant de Migration de base de données SQL, sur la page **Sélectionnez le processus** , sélectionnez **la base de données sous analyse/migrer**, puis cliquez sur **suivant**.
![Assistant de Migration de base de données SQL - Select Process](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/1.png)
3. Dans la boîte de dialogue **se connecter au serveur** , appliquer les paramètres suivants :
 - **Nom du serveur**: instance votre SQL Azure
 - **Authentification**: **authentification SQL Server**. Entrez vos informations d’identification.
 - **Base de données**: **base de données maître (toutes les bases de données de la liste)**.
 - **Remarque** Par défaut, l’Assistant enregistre vos informations de connexion. Si vous ne voulez pas qu’il, sélectionnez **Oublier les informations de connexion**.
![Étape 1 de l’Assistant - Sélectionner la Source - de Migration de base de données SQL](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/2.png)
4. Dans la boîte de dialogue **Sélectionner la Source** , sélectionnez le nom de la base de données restaurée dans la section **étapes de préparation** comme source de, puis cliquez sur **suivant**.

    ![Étape 2 de l’Assistant - Sélectionner la Source - de Migration de base de données SQL](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/3.png)

5. Dans la boîte de dialogue **Choisir les objets** , sélectionnez l’option **Sélectionner les objets de base de données** , puis table(or tables) que vous voulez migrer vers le serveur cible.
![Assistant de Migration de base de données SQL - choisir les objets](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/4.png)

6. Dans la page de **Résumé de l’Assistant de Script** , cliquez sur **Oui** lorsque vous êtes invité indiquant si vous êtes prêt à générer un script SQL. Vous avez également la possibilité d’enregistrer le Script TSQL pour une utilisation ultérieure.
![Assistant de Migration de base de données SQL - Résumé de l’Assistant Script](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/5.png)

7. Dans la page **Résumé des résultats** , cliquez sur **suivant**.
![Assistant de Migration de base de données SQL - Résumé des résultats](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/6.png)

8. Dans la page de **Connexion au serveur cible le programme d’installation** , cliquez sur **se connecter au serveur**et entrez les détails comme suit :
    - **Nom du serveur**: instance du serveur cible
    - **Authentification**: **l’authentification SQL Server**. Entrez vos informations d’identification.
    - **Base de données**: **base de données maître (toutes les bases de données de la liste)**. Cette option répertorie toutes les bases de données sur le serveur cible.

    ![Assistant de Migration de base de données SQL - connexion au serveur cible le programme d’installation](./media/sql-database-cloud-migrate-restore-single-table-azure-backup/7.png)

9. Cliquez sur **se connecter**, sélectionnez la base de données cible que vous voulez déplacer la table à, puis sur **suivant**. Cela doit terminer le script précédemment généré en cours d’exécution et vous devriez voir la table que vous venez déplacée copiée dans la base de données cible.

## <a name="verification-step"></a>Étape de vérification
1. Interroger et tester le tableau pour vous assurer que les données sont intactes nouvellement copié. Après confirmation, vous pouvez supprimer le formulaire de table renommée section **étapes de préparation** . (par exemple, &lt;nom de la table&gt;_old).

## <a name="next-steps"></a>Étapes suivantes

[Sauvegardes automatiques de base de données SQL](sql-database-automated-backups.md)
