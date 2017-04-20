<properties 
    pageTitle="Déplacer des données à une base de données SQL Azure pour apprentissage automatique Azure | Azure" 
    description="Créer une Table SQL et charger les données à Table SQL" 
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

# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>Déplacer des données à une base de données SQL Azure pour apprentissage automatique Azure

Cette rubrique décrit les options de déplacement des données à partir de fichiers plats (formats CSV ou TSV) ou à partir des données stockées dans un serveur SQL sur site à une base de données SQL Azure. Ces tâches de déplacement des données dans le cloud font partie du processus d’équipe données scientifique.

Pour une rubrique qui décrit les options de déplacement des données à un serveur SQL locales pour apprentissage automatique, voir [déplacer des données vers SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Les liens de **menu** suivants vers des rubriques qui décrivent l’acquisition de données dans des environnements cible où les données peuvent être stockées et traitées pendant le processus de Science de données (TDSP) d’équipe.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Le tableau suivant résume les options de déplacement des données vers une base de données SQL Azure.

<b>SOURCE</b> |<b>DESTINATION : Base de données SQL Azure</b> |
-------------- |--------------------------------|
<b>Fichier plat (CSV ou TSV mis en forme)</b> |<a href="#bulk-insert-sql-query">Requête SQL d’insertion en bloc |
<b>SQL Server localement</b> | 1. <a href="#export-flat-file">exporter vers un fichier plat<br> 2. <a href="#insert-tables-bcp">l’Assistant Migration SQL de base de données<br> 3. <a href="#db-migration">précédent de base de données vers le haut et de restauration<br> 4. <a href="#adf">usine données azure |


## <a name="prereqs"></a>Conditions préalables
Les procédures décrites ici requièrent que vous ayez :

* Un **abonnement Azure**. Si vous ne disposez pas d’un abonnement, vous pouvez vous inscrire à une [version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).
* Un **compte de stockage Azure**. Vous utilisez un compte de stockage Azure pour stocker les données dans ce didacticiel. Si vous n’avez pas un compte de stockage Azure, voir l’article [créer un compte de stockage](storage-create-storage-account.md#create-a-storage-account) . Après avoir créé le compte de stockage, vous devez obtenir la clé de compte utilisée pour accéder au stockage. Voir [Afficher, copier et stockage régénérer les touches d’accès](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Accès à une **base de données SQL Azure**. Si vous devez configurer une base de données SQL Azure, [Mise en route de la base de données SQL Microsoft Azure](../sql-database/sql-database-get-started.md) fournit des informations sur la mise en service d’une nouvelle instance d’une base de données SQL Azure.
* Installé et configuré **PowerShell Azure** localement. Pour plus d’informations, voir [comment installer et configurer Azure PowerShell](../powershell-install-configure.md).

**Données**: les processus de migration sont illustrées à l’aide du [jeu de données NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/). Le dataset NYC Taxi contient des informations sur les données de voyage et salons et n’est disponible, comme indiqué ce poste, sur le stockage blob Azure : [NYC Taxi données](http://www.andresmh.com/nyctaxitrips/). Un exemple et une description de ces fichiers sont fournies dans la zone [NYC Taxi voyages Dataset Description](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Vous pouvez adapter les procédures décrites ici à un ensemble de vos propres données ou suivez les étapes comme décrit à l’aide du dataset NYC Taxi. Pour télécharger le dataset Taxi NYC dans votre base de données SQL Server sur site, suivez la procédure décrite dans [En bloc importer des données dans la base de données SQL Server](machine-learning-data-science-process-sql-walkthrough.md#dbload). Ces instructions s’appliquent à un serveur SQL Server sur une Machine virtuelle Azure, mais la procédure pour le téléchargement vers le serveur SQL sur site est la même.


## <a name="file-to-azure-sql-database"></a>Déplacement de données à partir d’une source de fichier plat à une base de données SQL Azure

Données dans le fichier plat (CSV ou TSV mis en forme) peuvent être déplacées vers une base de données SQL Azure à l’aide d’une requête SQL d’Insérer en bloc.

### <a name="bulk-insert-sql-query"></a>Requête SQL d’insertion en bloc

Les étapes de la procédure à l’aide de la requête SQL d’Insérer en bloc sont semblables à ceux traités dans les sections de déplacement des données à partir d’une source de fichier plat à SQL Server sur un ordinateur virtuel Azure. Pour plus d’informations, voir [Requête SQL d’Insérer en bloc](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).


##<a name="sql-on-prem-to-sazure-sql-database"></a>Déplacement de données locales SQL Server à une base de données SQL Azure

Si la source de données est stocké dans un SQL Server localement, il existe différentes possibilités pour déplacer les données à une base de données SQL Azure :

1. [Exporter vers un fichier plat](#export-flat-file) 
2. [Assistant Migration SQL de base de données](#insert-tables-bcp)
3. [Base de données en arrière vers le haut et de restauration](#db-migration)
4. [Données Azure usine](#adf)

Les étapes pour les trois premières sont très semblables aux ces sections de [déplacement des données vers SQL Server sur une machine virtuelle Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) qui présentent les mêmes procédures. Des liens vers les sections appropriées dans cette rubrique sont fournies dans les instructions suivantes.

###<a name="export-flat-file"></a>Exporter vers un fichier plat

Les étapes pour cette exportation vers un fichier plat sont semblables à ceux de [Exporter vers un fichier plat](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file).

###<a name="insert-tables-bcp"></a>Assistant Migration SQL de base de données

La procédure d’utilisation de l’Assistant de Migration de base de données SQL est semblable à ceux de [L’Assistant Migration SQL de base de données](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration).

###<a name="db-migration"></a>Base de données en arrière vers le haut et de restauration

Les étapes d’utilisation de base de données sauvegarder et restaurer sont semblables à ceux de [base de données sauvegarder et restaurer](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup).

###<a name="adf"></a>Données Azure usine

La procédure de déplacement des données vers une base de données SQL Azure avec Azure données usine (chargeur) est fournie dans la rubrique [déplacer des données d’un serveur SQL localement à SQL Azure avec Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). Cette rubrique indique comment déplacer des données d’une base de données SQL Server localement à une base de données SQL Azure via Azure Blob Storage à l’aide de la définition d’application. 

Envisagez d’utiliser chargeur lorsque données devant être migrées en permanence dans un scénario hybride qui accède à des ressources locales et cloud, et lorsque les données sont traitées ou devant être modifié ou que la logique métier pour l’ajouter en cours de migration. Définition d’application permet la planification et le suivi des tâches à l’aide des scripts JSON simples que gérer le déplacement des données de façon régulière. Chargeur a également d’autres fonctionnalités, telles que la prise en charge pour les opérations complexes.




