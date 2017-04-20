<properties
   pageTitle="Guide d’utilisation des PolyBase dans SQL Data Warehouse | Microsoft Azure"
   description="Instructions et recommandations sur l’utilisation de PolyBase dans les scénarios Data Warehouse SQL."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/30/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guide d’utilisation des PolyBase dans Data Warehouse SQL

Ce guide vous donne des informations pratiques pour l’utilisation de PolyBase dans SQL Data Warehouse.

Pour commencer, consultez le didacticiel [charger les données avec PolyBase][] .


## <a name="rotating-storage-keys"></a>Rotation des clés de stockage

De temps à autre à modifier la touche d’accès rapide pour le stockage blob pour des raisons de sécurité.

Pour effectuer cette tâche, le plus élégantes consiste à suivre un processus appelé « faisant pivoter les touches ». Vous avez peut-être remarqué que vous avez deux clés de stockage pour votre compte de stockage blob. Il s’agit de sorte que vous pouvez effectuer la transition

Faire pivoter vos clés de compte de stockage Azure est un processus simple en trois étapes

1. Créer la deuxième inclus dans l’étendue de la base de données d’informations d’identification en fonction de la touche d’accès de stockage secondaire
2. Créer la deuxième source de données externe en fonction de ces nouvelles informations d’identification
3. Supprimer et créer les tables externes pointant vers la nouvelle source de données externes

Lorsque vous avez migré tous vos externes des tableaux à la nouvelle source de données externes, puis vous pouvez effectuer les tâches de nettoyage suivantes :

1. Déposez la première source de données externes
2. Première base de données dans le menu déroulant portée d’informations d’identification en fonction de la touche d’accès de stockage principal
3. Connectez-vous à Azure et régénérer la clé primaire access prête pour la prochaine fois

## <a name="query-azure-blob-storage-data"></a>Interroger des données de stockage blob Azure
Requêtes sur des tables externes utilisent simplement le nom de la table comme s’il s’agissait d’une table relationnelle.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE] Une requête sur une table externe peut échouer avec l’erreur *« Requête abandonnées--la valeur maximale rejeter seuil a été atteinte pendant la lecture d’une source externe »*. Cela signifie que vos données externes contient des enregistrements *incorrectes* . Un enregistrement de données est considéré comme « modifié » si le types de données réelles/le nombre de colonnes ne correspondent pas les définitions de colonne de la table externe ou si les données n’est pas conforme au format de fichier externe spécifié. Pour résoudre ce problème, vérifiez que votre table externe et les définitions de format de fichier externe sont correctes et que vos données externes est conforme à ces définitions. Au cas où un sous-ensemble des enregistrements de données externes sont incorrectes, vous pouvez choisir de refuser ces enregistrements pour vos requêtes en utilisant les options de refuser dans créer externes TABLE DDL.


## <a name="load-data-from-azure-blob-storage"></a>Charger des données depuis le stockage blob Azure
Cet exemple charge des données depuis le stockage blob Azure à base de données SQL Data Warehouse.

Le stockage de données directement supprime le temps de transfert de données pour les requêtes. Le stockage de données avec un index columnstore améliore les performances de requête pour les requêtes d’analyse en jusqu'à 10 x.

Cet exemple utilise l’instruction CREATE TABLE AS SELECT pour charger les données. La nouvelle table hérite des colonnes nommées dans la requête. Il hérite les types de données de ces colonnes de la définition de la table externe.

CREATE TABLE AS SELECT est un très performantes à instruction Transact-SQL qui charge les données en parallèle à tous les nœuds de calcul de votre Data Warehouse SQL.  Il a été développé pour le moteur de traitement hautement parallèle (MPP) dans le système de plateforme Analytique et figure à présent dans Data Warehouse SQL.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Voir [CREATE TABLE AS SELECT (Transact-SQL)][].

## <a name="create-statistics-on-newly-loaded-data"></a>Créer des statistiques sur les données que vous venez chargées

Azure SQL Data Warehouse ne prend pas encore prise en charge automatique créer ou mettre à jour les statistiques automatiquement chaque année.  Afin d’obtenir les meilleures performances de vos requêtes, il est important que créer des statistiques sur toutes les colonnes de toutes les tables après le premier chargement ou des modifications substantielles apparaissent dans les données.  Pour une explication détaillée des statistiques, consultez la rubrique [statistiques][] dans le groupe développer des rubriques.  Voici un exemple rapide montrant comment créer des statistiques sur le tableau chargé dans cet exemple.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exporter des données à Azure blob storage
Cette section montre comment exporter des données à partir de SQL Data Warehouse à Azure blob storage. Cet exemple utilise créer externes TABLE AS SELECT qui est un très performantes à instruction Transact-SQL pour exporter les données en parallèle à partir de tous les nœuds de calcul.

L’exemple suivant crée une table externe Weblogs2014 à l’aide des définitions de colonne et les données de dbo. Table de blogs. La définition de table externe est stockée dans Data Warehouse SQL et les résultats de l’instruction SELECT sont exportées dans l’annuaire « / archiver/log2014 / » sous le conteneur blob spécifié par la source de données. Les données sont exportées dans le format de fichier texte spécifié.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


## <a name="working-around-the-polybase-utf-8-requirement"></a>Travail autour de la configuration minimale requise PolyBase UTF-8
Présenter PolyBase prend en charge du chargement de fichiers de données qui ont été codé UTF-8. Comme UTF-8 utilise le même codage de caractères en tant que PolyBase ASCII seront également prennent en charge les données de chargement sont encodée en ASCII. Toutefois, PolyBase ne prend pas en charge autre codage de caractère comme UTF-16 / Unicode ou des caractères ASCII étendus. ASCII étendu contient des caractères accentués tels que le signe situé courantes en allemand.

Pour contourner cette exigence la meilleure réponse consiste à ré-écrire dans le codage UTF-8.

Il existe plusieurs façons de le faire. Vous trouverez ci-dessous deux approches à l’aide de Powershell :

### <a name="simple-example-for-small-files"></a>Exemple simple pour les petits fichiers

Voici un simple d’une ligne de script Powershell qui crée le fichier.

```PowerShell
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Cependant, s’il s’agit d’un moyen simple de ré-encoder les données il n’est pas la plus efficace. L’exemple de diffusion en continu io ci-dessous est bien beaucoup plus rapidement et donne le même résultat.

### <a name="io-streaming-example-for-larger-files"></a>Exemple IO diffusion en continu de fichiers volumineux

L’exemple ci-dessous est plus complexe mais qu’il utilise les lignes de données à partir de la source avec la cible il est beaucoup plus efficace. Utilisez cette approche pour les fichiers plus grandes.

```PowerShell
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path\","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path\","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur le déplacement de données vers SQL Data Warehouse, consultez la [vue d’ensemble de migration de données][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Charger les données avec PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistiques]: ./sql-data-warehouse-tables-statistics.md
[vue d’ensemble de migration de données]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[FORMAT de fichier externe créer (Transact-SQL)] : https://msdn.microsoft.com/library/dn935026) .aspx [créer une TABLE externe (Transact-SQL)] : https://msdn.microsoft.com/library/dn935021.aspxx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CRÉER la TABLE en tant que SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->
