<properties
    pageTitle="Copier des données depuis le stockage Blob de base de données SQL | Microsoft Azure"
    description="Ce didacticiel montre comment utiliser copie activité dans un pipeline Azure Data Factory pour copier des données depuis le stockage Blob dans la base de données SQL."
    Keywords="objets BLOB sql, stockage d’objets blob, copie des données"
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/26/2016"
    ms.author="spelluru"/>

# <a name="copy-data-from-blob-storage-to-sql-database-using-data-factory"></a>Copier des données depuis le stockage Blob à l’aide de données par défaut de la base de données SQL 
> [AZURE.SELECTOR]
- [Vue d’ensemble et conditions préalables](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
- [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modèle de gestionnaire de ressources Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Dans ce didacticiel, vous créez une usine de données avec un pipeline pour copier des données depuis le stockage Blob dans la base de données SQL.

L’activité de copie effectue le déplacement des données dans Azure Data Factory. Il est optimisé par un service globalement disponible qui peut copier des données entre différentes banques de données de façon sécurisée, fiable et format SVG. Consultez l’article des [Activités de déplacement des données](data-factory-data-movement-activities.md) pour plus d’informations sur l’activité de copie.  

> [AZURE.NOTE] Pour une présentation détaillée du service de données par défaut, voir l’article [Introduction aux Azure Data Factory](data-factory-introduction.md) .

##<a name="prerequisites-for-the-tutorial"></a>Conditions préalables pour le didacticiel
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Abonnement azure**.  Si vous n’avez un abonnement, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Consultez l’article de la [Version d’évaluation gratuite](http://azure.microsoft.com/pricing/free-trial/) pour plus d’informations.
- **Compte de stockage azure**. Vous utilisez le stockage blob en tant que **source de** données magasin dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) pour obtenir la procédure pour en créer un.
- **Base de données SQL azure**. Vous utilisez une base de données SQL Azure comme un magasin de données de **destination** dans ce didacticiel. Si vous n’avez pas une base de données SQL Azure que vous pouvez utiliser dans le didacticiel, Découvrez [comment créer et configurer une base de données SQL Azure](../sql-database/sql-database-get-started.md) en créer un.
- **SQL Server 2012/2014 ou Visual Studio 2013**. Vous utilisez SQL Server Management Studio ou Visual Studio pour créer une base de données exemple et d’afficher les données de résultat dans la base de données.  

## <a name="collect-blob-storage-account-name-and-key"></a>Collecte de clé et nom de compte de stockage blob 
Vous devez le nom de compte et la clé de compte de votre compte de stockage Azure pour effectuer ce didacticiel. Notez le **nom du compte** et la **clé de compte** pour votre compte de stockage Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **plusieurs services** dans le menu de gauche, puis sélectionnez **Comptes de stockage**.

    ![Parcourir - comptes de stockage](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\browse-storage-accounts.png)
3. Dans la carte de **Stockage comptes** , sélectionnez le **compte de stockage Azure** que vous souhaitez utiliser dans ce didacticiel.
4. Sélectionnez le lien sous **paramètres de** **touches d’accès rapide** .
5.  Cliquez sur **Copier** (image) en regard de la zone de texte **nom de compte de stockage** et enregistrer/collez-la ailleurs (par exemple : dans un fichier texte).
6. Répétez l’étape précédente pour copier ou de note vers le bas la **touche1**.
    
    ![Touche d’accès de stockage](media\data-factory-copy-data-from-azure-blob-storage-to-sql-database\storage-access-key.png)
7. Fermez toutes les cartes en cliquant sur **X**.

## <a name="collect-sql-server-database-user-names"></a>Collecte de SQL server, base de données, les noms d’utilisateur
Vous avez besoin des noms de serveur SQL Azure, base de données et des utilisateurs pour effectuer ce didacticiel. Notez le nom du **serveur**, de **base de données**et **d’utilisateur** pour votre base de données SQL Azure.

1. Dans le **portail Azure**, cliquez sur **plusieurs services de** gauche et sélectionnez les **bases de données SQL**.
2. Dans la **carte de bases de données SQL**, sélectionnez la **base de données** que vous souhaitez utiliser dans ce didacticiel. Notez le **nom de la base de données**.  
3. Dans la carte de **base de données SQL** , cliquez sur **Propriétés** sous **paramètres**.
4. Notez les valeurs pour **Nom du serveur** et la **Connexion au serveur d’administration**.
5. Fermez toutes les cartes en cliquant sur **X**.

## <a name="allow-azure-services-to-access-sql-server"></a>Autoriser des services Azure à accéder à SQL server 
Assurez-vous **que paramètre **Autoriser l’accès aux services Azure** activée pour votre serveur SQL Azure afin que le service de données usine peut accéder à votre serveur SQL Azure** . Pour vérifier et activez ce paramètre, procédez comme suit :

1. Cliquez sur concentrateur de **plusieurs services de** gauche, puis cliquez sur **serveurs SQL Server**.
2. Sélectionnez votre serveur, puis cliquez sur **pare-feu** sous **paramètres**. 
4. Dans la carte de **paramètres du pare-feu** , cliquez sur **activé** pour **Autoriser l’accès aux services Azure**.
5. Fermez toutes les cartes en cliquant sur **X**.

## <a name="prepare-blob-storage-and-sql-database"></a>Préparer le stockage d’objets Blob et base de données SQL 
À présent, préparer votre stockage d’objets blob Azure et de la base de données SQL Azure le didacticiel en effectuant les étapes suivantes :  

1. Ouverture du bloc-notes, collez le texte suivant et enregistrez-le en tant **emp.txt** au dossier **C:\ADFGetStarted** sur votre disque dur.

        John, Doe
        Jane, Doe

2. Utilisez les outils tels que [Explorateur de stockage Azure](https://azurestorageexplorer.codeplex.com/) pour créer le conteneur **adftutorial** et télécharger le fichier **emp.txt** dans le conteneur.

    ![Explorateur de stockage Azure. Copier des données depuis le stockage Blob dans la base de données SQL](./media/data-factory-copy-data-from-azure-blob-storage-to-sql-database/getstarted-storage-explorer.png)
3. Utilisez le script SQL ci-après pour créer la table **emp** dans votre base de données SQL Azure.  


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL,
            FirstName varchar(50),
            LastName varchar(50),
        )
        GO

        CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);

    **Si vous avez SQL Server 2012/2014 installé sur votre ordinateur :** suivez les instructions de [étape 2 : se connecter à la base de données SQL de la gestion des Azure SQL de base de données à l’aide de SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md#Step2) article pour vous connecter à votre serveur SQL Azure et exécuter le script SQL. Cet article utilise le [portail Azure classique](http://manage.windowsazure.com), pas le [nouveau portail Azure](https://portal.azure.com), pour configurer le pare-feu pour un serveur SQL Azure.

    Si votre client n’est pas autorisé à accéder au serveur SQL Azure, vous devez configurer le pare-feu de votre serveur SQL Azure pour autoriser l’accès à partir de votre ordinateur (adresse IP). Consultez [cet article](../sql-database/sql-database-configure-firewall-settings.md) pour obtenir la procédure configurer le pare-feu de votre serveur SQL Azure.

Vous avez terminé la configuration requise. Vous pouvez créer une usine de données à l’aide d’une des façons suivantes. Cliquez sur l’un des onglets en haut ou les liens suivants pour effectuer le didacticiel.     

- [Portail Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Assistant copie](data-factory-copy-data-wizard-tutorial.md)
