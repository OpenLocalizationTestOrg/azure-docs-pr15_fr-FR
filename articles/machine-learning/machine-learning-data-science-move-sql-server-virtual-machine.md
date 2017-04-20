<properties 
    pageTitle="Déplacer des données vers SQL Server sur un ordinateur virtuel Azure | Azure" 
    description="Déplacer les données à partir de fichiers plats ou d’un serveur local SQL Server à SQL Server sur machine virtuelle Azure." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/14/2016" 
    ms.author="bradsev" /> 

# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Déplacer des données vers SQL Server sur un ordinateur virtuel Azure

Cette rubrique décrit les options de déplacement des données à partir de fichiers plats (formats CSV ou TSV) ou d’un serveur SQL localement à SQL Server sur une machine virtuelle Azure. Ces tâches de déplacement des données dans le cloud font partie du processus d’équipe données scientifique.

Pour une rubrique qui décrit les options de déplacement des données vers une base de données SQL Azure pour apprentissage automatique, voir [déplacer des données à une base de données SQL Azure pour apprentissage automatique Azure](machine-learning-data-science-move-sql-azure.md).

Le **menu** en dessous des liens vers des rubriques qui décrivent comment faire pour ajouter des données dans d’autres environnements cible où les données peuvent être stockées et traitées pendant le processus de Science de données (TDSP) d’équipe.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]


Le tableau suivant résume les options de déplacement des données vers SQL Server sur un ordinateur virtuel Azure.

<b>SOURCE</b> |<b>DESTINATION : SQL Server sur machine virtuelle Azure</b> |
------------------ |-------------------- |
<b>Fichier plat</b> |1. <a href="#insert-tables-bcp">utilitaire de copie de ligne de commande en bloc (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">en bloc requête SQL insertion</a><br> 3. <a href="#sql-builtin-utilities">graphiques utilitaires intégrés dans SQL Server</a>
<b>En local SQL Server</b> | 1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">déployer une base de données SQL Server à un Assistant machine virtuelle Microsoft Azure</a><br> 2. <a href="#export-flat-file">Exporter vers un fichier plat</a><br> 3. <a href="#sql-migration">l’Assistant Migration SQL de base de données</a> <br> 4. <a href="#sql-backup">précédent de base de données vers le haut et de restauration</a><br>

Notez que ce document suppose que les commandes SQL sont exécutées à partir de SQL Server Management Studio ou l’Explorateur de base de données Visual Studio.

> [AZURE.TIP] Comme alternative, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) pour créer et planifier un pipeline qui permet de déplacer des données vers une machine virtuelle de SQL Server sur Azure. Pour plus d’informations, voir [copier des données avec Azure Data Factory (activité copier)](../data-factory/data-factory-data-movement-activities.md).


## <a name="prereqs"></a>Conditions préalables
Ce didacticiel part du principe que vous avez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous allez utiliser un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devrez obtenir la clé de compte utilisée pour accéder au stockage. Voir [Afficher, copier et stockage régénérer les touches d’accès](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* **SQL Server sur un ordinateur virtuel Azure**configuré. Pour plus d’informations, voir [configurer une machine virtuelle Azure SQL Server comme un serveur IPython bloc-notes pour analytique avancées](machine-learning-data-science-setup-sql-server-virtual-machine.md).
* Installé et configuré **PowerShell Azure** localement. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).


## <a name="filesource_to_sqlonazurevm"></a>Déplacement de données à partir d’une source de fichier plat à SQL Server sur un ordinateur virtuel Azure

Si vos données se trouve dans un fichier plat (classée dans un format lignes/colonnes), il peut être déplacé vers machine virtuelle SQL Server dans Azure via les méthodes suivantes :

1. [Utilitaire de copie de ligne de commande en bloc (BCP)](#insert-tables-bcp) 
2. [Requête SQL d’insertion en bloc](#insert-tables-bulkquery)
3. [Graphiques utilitaires intégrés dans SQL Server (importation/exportation, SSIS)](#sql-builtin-utilities)


### <a name="insert-tables-bcp"></a>Utilitaire de copie de ligne de commande en bloc (BCP)

BCP est un utilitaire de ligne de commande installé avec SQL Server et est une des façons le plus rapide pour déplacer des données. Il peut fonctionner avec tous les trois variantes de SQL Server (en local SQL Server, SQL Azure et machine virtuelle SQL Server sur Azure). 

> [AZURE.NOTE]**Où mes données doivent être pour BCP ?**  
> Il n’est pas obligatoire, fichiers contenant les données source situées sur le même ordinateur que le serveur SQL cible permet pour les transferts plus rapides (réseau vitesse vs disque local vitesse IO). Vous pouvez déplacer les fichiers plats contenant les données à l’ordinateur où SQL Server est installé à l’aide d’outils tels que [AZCopy](../storage/storage-use-azcopy.md)de copie des fichiers différents, [Explorateur de stockage Azure](http://storageexplorer.com/) ou windows copier/coller via le protocole RDP (Remote Desktop).

1. Assurez-vous que la base de données et les tables sont créées dans la base de données SQL Server cible. Voici un exemple de la façon de le faire en utilisant la `Create Database` et `Create Table` commandes :

        CREATE DATABASE <database_name>
    
        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        ) 
    
2. Générer du format de fichier qui décrit le schéma pour la table à l’aide de la commande suivante à partir de la ligne de commande de l’ordinateur où bcp est installé.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n` 

3. Insérer les données dans la base de données à l’aide de la commande bcp comme suit. Cela doit fonctionner à partir de la ligne de commande en supposant que le serveur SQL Server est installé sur le même ordinateur :

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimisation de BCP insère** Consultez l’article suivant [« Guidelines pour optimiser l’importation en bloc »](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) pour optimiser ces insertions.


### <a name="insert-tables-bulkquery-parallel"></a>Mise en parallèle insertions plus rapide déplacement des données

Si les données que vous déplacez sont volumineuse, vous pouvez accélérer éléments en exécutant simultanément plusieurs commandes BCP en parallèle dans un PowerShell Script.

> [AZURE.NOTE]**Réception de données volumineux** Pour optimiser pour les jeux de données volumineux et très volumineux de chargement des données, partition vos tables de base de données logiques et physiques à l’aide de plusieurs tables de partition et groupes. Pour plus d’informations sur la création et de chargement des données dans des tables de partition, voir [Parallèle charge SQL Partition Tables](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).


L’exemple de script PowerShell ci-dessous montrent les insertions en parallèle à l’aide de bcp :
    
    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
           bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

            #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
            #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n 
      }
    

    # Background processing of all partitions
    for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
    {
      Write-Debug "Submit loading partition # $i"
      Start-Job $ScriptBlock -Arg $i      
    }
    

    # Wait for it all to complete
    While (Get-Job -State "Running")
    {
      Start-Sleep 10
      Get-Job
    }
    
    # Getting the information back from the jobs
    Get-Job | Receive-Job
    Set-ExecutionPolicy Restricted #reset the execution policy


### <a name="insert-tables-bulkquery"></a>Requête SQL d’insertion en bloc

[Requête SQL d’Insérer en bloc](https://msdn.microsoft.com/library/ms188365) peut servir à importer des données dans la base de données à partir de fichiers en fonction de lignes/colonnes (pris en charge sont décrites dans la[Préparation de données pour en bloc exporter ou importer (SQL Server)](https://msdn.microsoft.com/library/ms188609)) rubrique. 

Voici quelques exemples de commandes pour insérer en bloc sont les suivantes :  

1. Analyser vos données et définissez les options personnalisées avant d’importer pour vous assurer que la base de données SQL Server suppose que la même mise en forme des champs spéciaux tels que des dates. Voici un exemple de la façon de définir le format de date par année-mois-jour (si vos données contiennent la date au format année-mois-jour) :

        SET DATEFORMAT ymd; 
    
2. Importer des données à l’aide des instructions d’importation en bloc :

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH 
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )
      

### <a name="sql-builtin-utilities"></a>Utilitaires intégrés dans SQL Server

Vous pouvez utiliser SQL Server intégrations Services (SSIS) pour importer des données dans SQL Server virtuelle sur Azure à partir d’un fichier plat. SSIS est disponible dans les deux environnements studio. Pour plus d’informations, voir [Integration Services (SSIS) et les environnements Studio](https://technet.microsoft.com/library/ms140028.aspx):

- Pour plus d’informations sur SQL Server Data Tools, voir [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
- Pour plus d’informations sur l’Assistant Importation et exportation, voir [Assistant SQL Server Importation et exportation](https://msdn.microsoft.com/library/ms141209.aspx)


## <a name="sqlonprem_to_sqlonazurevm"></a>Déplacement de données locale SQL Server à SQL Server sur un ordinateur virtuel Azure

Vous pouvez également utiliser les stratégies de migration suivantes :

1. [Déployer une base de données SQL Server sur un Assistant machine virtuelle Microsoft Azure](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exporter vers un fichier plat](#export-flat-file) 
3. [Assistant Migration SQL de base de données](#sql-migration)
4. [Base de données en arrière vers le haut et de restauration](#sql-backup)

Nous décrire chacun d'entre eux ci-dessous :

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Déployer une base de données SQL Server sur un Assistant machine virtuelle Microsoft Azure

La **déployer une base de données SQL Server à un Assistant machine virtuelle Microsoft Azure** est un moyen simple et recommandé pour déplacer des données à partir d’une instance de SQL Server en local pour SQL Server sur un ordinateur virtuel Azure. Pour obtenir la procédure détaillée ainsi une discussion sur d’autres solutions, voir [migrer une base de données vers SQL Server sur une machine virtuelle Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exporter vers un fichier plat

Différentes méthodes peuvent être utilisés pour exporter les données à partir d’un ordinateur local sur SQL Server comme indiqué dans la rubrique [importation et exportation de données (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) . Ce document couvre le programme de copie en bloc (BCP) par exemple. Une fois que les données sont exportées dans un fichier plat, il peut être importé vers un autre SQL server à l’aide de l’importation en bloc. 

1. Exporter les données de serveur SQL local vers un fichier à l’aide de l’utilitaire bcp comme suit.

    `bcp dbname..tablename out datafile.tsv -S  servername\sqlinstancename -T -t \t -t \n -c`

2. Créer la base de données et la table sur machine virtuelle SQL Server sur Azure à l’aide de la `create database` et `create table` pour le schéma de table exporté à l’étape 1.

3. Créer un fichier au format pour décrire le schéma de table de données d’étant importé/exporté. Détails du fichier format sont décrites dans [créer un Format de fichier (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Mettre en forme de génération de fichier lors de l’exécution BCP à partir de l’ordinateur SQL Server 

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n 

    Mettre en forme de génération de fichier lors de l’exécution BCP à distance sur un serveur SQL 

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
        
    
4. Utilisez une des méthodes décrites dans la section [Déplacement de données à partir du fichier Source](#filesource_to_sqlonazurevm) pour déplacer les données dans le fichier plat à un serveur SQL Server.

### <a name="sql-migration"></a>Assistant Migration SQL de base de données

[Assistant de Migration de base de données SQL Server](http://sqlazuremw.codeplex.com/) fournit une manière conviviale pour déplacer des données entre deux instances de SQL server. Il permet à l’utilisateur mapper le schéma de données entre des sources et les tables de destination, choisissez les types de colonnes et diverses autres fonctionnalités. Il utilise la copie en bloc (BCP) en coulisses. Capture d’écran de l’écran d’accueil de l’Assistant Migration de base de données SQL est indiqué ci-dessous.  

![Assistant Migration SQL Server][2]

### <a name="sql-backup"></a>Base de données en arrière vers le haut et de restauration

SQL Server prend en charge : 

1. [Base de données principale vers le haut et restaurer la fonctionnalité](https://msdn.microsoft.com/library/ms187048.aspx) (les deux, à un fichier ou local à DOS exporter vers blob) et les [Applications de niveau de données](https://msdn.microsoft.com/library/ee210546.aspx) (à l’aide d’à DOS). 
2. Possibilité de créer directement des machines virtuelles SQL Server sur Azure avec une base de données copiée ou copier une base de données SQL Azure existante. Pour plus d’informations, voir [utiliser l’Assistant copie de base de données](https://msdn.microsoft.com/library/ms188664.aspx). 

Capture d’écran de l’arrière-plan de la base de données à distance/restaurer les options à partir de SQL Server Management Studio illustré ci-dessous.

![Outil d’importation de SQL Server][1]

## <a name="resources"></a>Ressources

[Migrer une base de données vers SQL Server sur un ordinateur virtuel Azure](../virtual-machines/virtual-machines-windows-migrate-sql.md)

[SQL Server dans la vue d’ensemble des Machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/machine-learning-data-science-move-sql-server-virtual-machine/database_migration_wizard.png
