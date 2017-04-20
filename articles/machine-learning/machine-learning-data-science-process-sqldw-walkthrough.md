<properties
    pageTitle="Le processus d’équipe données scientifique en action : à l’aide de Data Warehouse SQL | Microsoft Azure"
    description="Processus Analytique avancée et technologie en Action"  
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
    ms.date="06/24/2016"
    ms.author="bradsev;hangzh;weig"/>


# <a name="the-team-data-science-process-in-action-using-sql-data-warehouse"></a>Le processus d’équipe données scientifique en action : à l’aide de Data Warehouse SQL

Dans ce didacticiel, nous vous aide à créer et déployer un ordinateur apprentissage du modèle à l’aide de SQL Data Warehouse (SQL DW) remonter d’un dataset accessible au public-- [NYC Taxi voyages](http://www.andresmh.com/nyctaxitrips/) dataset. Le modèle de classement binaire construit prévoit ou non une info-bulle est payée pour un voyage, et les modèles de classification multiclass et de régression sont également abordés qui prévoir la distribution pour les montants Conseil payés.

La procédure suit le flux de travail [D’équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/) . Nous montrent comment configurer un environnement scientifique de données, comment faire pour charger les données vers SQL DW et comment utiliser SQL DW ou un bloc-notes IPython pour Explorer les données et technicien fonctionnalités au modèle. Nous puis montrent comment créer et déployer un modèle avec apprentissage automatique Azure.


## <a name="dataset"></a>Le jeu de données NYC Taxi voyages

Les données NYC Taxi voyage se composent d’environ 20 Go de fichiers CSV compressés (~ 48 Go sans compression), l’enregistrement de plus de 173 millions individuels voyages et les tarifs payé pour chaque voyage. Chaque enregistrement voyage comprend les emplacements de collecte et remise et heures, numéro de licence piratage rendue (pilote) et le nombre de medallion (id unique de taxi). Les données couvre tous les déplacements de l’année 2013 et sont fournies dans les deux jeux de données suivants pour chaque mois :

1. Le fichier **trip_data.csv** contient des informations de voyage, telles que nombre de voyageurs, collecte et cette chute points, la durée du voyage et longueur de voyage. Voici quelques exemples d’enregistrements :

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Le fichier **trip_fare.csv** contient des détails sur le tarif payé pour chaque voyage, telles que type de paiement, prix, surcharge et taxes, conseils et péages et le montant total payé. Voici quelques exemples d’enregistrements :

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La **clé unique** utilisée pour joindre voyage\_des données et voyage\_tarif est composée de trois champs suivants :

- medallion,
- piratage\_licence et
- collecte\_date/heure.

## <a name="mltasks"></a>Trois types de tâches de prévision d’adresses

Nous formuler trois problèmes de prévision selon la *Conseil\_montant* afin d’illustrer les trois types de tâches de modélisation :

1. **Classification binaire**: prévoir ou non une info-bulle payée pour un voyage, c'est-à-dire un *Conseil\_montant* qui est supérieure à 0 f est un exemple positif, tandis que vous un *Conseil\_montant* de 0 € est un exemple négatif.

2. **Classification multiclass**: prévoir la plage de conseil payé pour le voyage. Nous diviser la *Conseil\_montant* dans cinq emplacements ou classes :

        Class 0 : tip_amount = $0
        Class 1 : tip_amount > $0 and tip_amount <= $5
        Class 2 : tip_amount > $5 and tip_amount <= $10
        Class 3 : tip_amount > $10 and tip_amount <= $20
        Class 4 : tip_amount > $20

3. **Tâche de régression**: prévoir la quantité de conseil payé pour un voyage.  


## <a name="setup"></a>Configurer l’environnement scientifique données Azure pour analytique avancées

Pour configurer votre environnement scientifique de données Azure, procédez comme suit.

**Créer votre propre compte de stockage blob Azure**

- Lorsque vous configurez votre propre stockage d’objets blob Azure, choisissez un emplacement de geo pour votre stockage d’objets blob Azure dans ou aussi près que possible aux **États-Unis centre sud**, ce qui correspond à l’endroit où les données NYC Taxi sont stockées. Les données sont copiées à l’aide de AzCopy à partir du conteneur de stockage blob publique dans un conteneur dans votre compte de stockage. Votre stockage d’objets blob Azure est proche Sud, plus vite fin de cette tâche (étape 4).
- Pour créer votre propre compte Azure stockage, suivez les étapes décrites en [comptes de stockage sur Azure](../storage/storage-create-storage-account.md). Veillez à prendre des notes sur les valeurs pour les informations d’identification de compte stockage suivantes qu’ils seront nécessaires plus loin dans cette procédure pas à pas.

  - **Nom de compte de stockage**
  - **Clé de compte de stockage**
  - **Nom du conteneur** (que vous voulez que les données sont stockées dans le stockage blob Azure)

**Mise en service de votre instance Azure SQL DW.**
Suivez la documentation à [créer un Data Warehouse SQL](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) pour une instance SQL Data Warehouse de mise en service. Assurez-vous que vous faites des notations scientifiques sur les informations d’identification SQL Data Warehouse suivantes qui seront utilisées dans les étapes suivantes.

  - **Nom du serveur**: <server Name>. database.windows.net
  - **Nom SQLDW (base de données)**
  - **Nom d’utilisateur**
  - **Mot de passe**

**Installez Visual Studio 2015 et outils de données SQL Server.** Pour plus d’informations, voir [installer Visual Studio 2015 et/ou SSDT (SQL Server Data Tools) pour Data Warehouse SQL](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md).

**Se connecter à votre DW SQL Azure avec Visual Studio.** Pour plus d’informations, consultez les étapes 1 et 2 de [se connecter à Data Warehouse SQL Azure avec Visual Studio](../sql-data-warehouse/sql-data-warehouse-connect-overview.md).

>[AZURE.NOTE] Exécuter la requête SQL suivante sur la base de données que vous avez créé votre SQL Data Warehouse (au lieu de la requête fournie à l’étape 3 de la rubrique se connecter,) pour **créer une clé principale**.

    BEGIN TRY
           --Try to create the master key
        CREATE MASTER KEY
    END TRY
    BEGIN CATCH
           --If the master key exists, do nothing
    END CATCH;

**Créer un espace de travail apprentissage automatique Azure sous votre abonnement Azure.** Pour plus d’informations, voir [créer un espace de travail d’apprentissage automatique Azure](machine-learning-create-workspace.md).

## <a name="getdata"></a>Charger les données dans Data Warehouse SQL

Ouvrez une console de commande Windows PowerShell. Exécuter la session PowerShell suivante fichiers que nous partager avec vous sur Github dans un répertoire local que vous spécifiez avec le paramètre de script commandes pour télécharger l’exemple SQL *- DestDir*. Vous pouvez modifier la valeur du paramètre *-DestDir* à n’importe quel répertoire local. Si *- DestDir* n’existe pas, il sera créé par le script PowerShell.

>[AZURE.NOTE] Vous devrez peut-être **Exécuter en tant qu’administrateur** lorsque vous exécutez le script PowerShell suivant si votre annuaire *DestDir* a besoin de privilèges d’administrateur pour créer ou pour y écrire.

    $source = "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/Download_Scripts_SQLDW_Walkthrough.ps1"
    $ps1_dest = "$pwd\Download_Scripts_SQLDW_Walkthrough.ps1"
    $wc = New-Object System.Net.WebClient
    $wc.DownloadFile($source, $ps1_dest)
    .\Download_Scripts_SQLDW_Walkthrough.ps1 –DestDir 'C:\tempSQLDW'

Après l’exécution réussie, votre répertoire de travail en cours devient *- DestDir*. Vous devez être en mesure de voir écran comme ci-dessous :

![][19]

Dans votre *-DestDir*, exécutez le script PowerShell suivant en mode administrateur :

    ./SQLDW_Data_Import.ps1

Lorsque le script PowerShell s’exécute pour la première fois, vous devrez entrer les informations à partir de votre DW SQL Azure et votre compte de stockage blob Azure. Lorsque ce script PowerShell se termine en cours d’exécution pour la première fois, les informations d’identification vous entrée auront été écrites dans un fichier de configuration SQLDW.conf dans le répertoire de travail présenter. L’exécution de ce fichier de script PowerShell future a la possibilité de lire que toutes les nécessaires paramètres à partir de ce fichier de configuration. Si vous devez modifier certains paramètres, vous pouvez choisir d’entrer les paramètres de l’écran après une invite de commandes en supprimant ce fichier de configuration et entrer les valeurs de paramètres à l’invite ou pour modifier les valeurs de paramètre en modifiant le fichier SQLDW.conf dans votre annuaire *- DestDir* .

>[AZURE.NOTE] Afin d’éviter les conflits de noms de schéma avec celles qui existent déjà dans votre DW SQL Azure, lors de la lecture des paramètres directement à partir du fichier SQLDW.conf, un nombre aléatoire 3 chiffres est ajouté au nom du schéma à partir du fichier SQLDW.conf en tant que le nom du schéma par défaut pour chaque exécution. Le script PowerShell peut vous inviter à fournir un nom de schéma : le nom peut être spécifié au choix de l’utilisateur.

Ce fichier de **script PowerShell** effectue les tâches suivantes :

- **Télécharge et installe des AzCopy**, le cas AzCopy n’est pas déjà installé.

        $AzCopy_path = SearchAzCopy
        if ($AzCopy_path -eq $null){
            Write-Host "AzCopy.exe is not found in C:\Program Files*. Now, start installing AzCopy..." -ForegroundColor "Yellow"
            InstallAzCopy
            $AzCopy_path = SearchAzCopy
        }
            $env_path = $env:Path
            for ($i=0; $i -lt $AzCopy_path.count; $i++){
                if ($AzCopy_path.count -eq 1){
                    $AzCopy_path_i = $AzCopy_path
                } else {
                    $AzCopy_path_i = $AzCopy_path[$i]
                }
                if ($env_path -notlike '*' +$AzCopy_path_i+'*'){
                    Write-Host $AzCopy_path_i 'not in system path, add it...'
                    [Environment]::SetEnvironmentVariable("Path", "$AzCopy_path_i;$env_path", "Machine")
                    $env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
                    $env_path = $env:Path
                }

- **Copie des données à votre compte de stockage blob privé** à partir du public blob avec AzCopy

        Write-Host "AzCopy is copying data from public blob to yo storage account. It may take a while..." -ForegroundColor "Yellow"
        $start_time = Get-Date
        AzCopy.exe /Source:$Source /Dest:$DestURL /DestKey:$StorageAccountKey /S
        $end_time = Get-Date
        $time_span = $end_time - $start_time
        $total_seconds = [math]::Round($time_span.TotalSeconds,2)
        Write-Host "AzCopy finished copying data. Please check your storage account to verify." -ForegroundColor "Yellow"
        Write-Host "This step (copying data from public blob to your storage account) takes $total_seconds seconds." -ForegroundColor "Green"


- **Charge les données à l’aide de Polybase (en exécutant LoadDataToSQLDW.sql) à votre DW SQL Azure** à partir de votre compte de stockage blob privé avec les commandes suivantes.

    - Créer un schéma

            EXEC (''CREATE SCHEMA {schemaname};'');

    - Créer des informations d’identification inclus dans l’étendue de la base de données

            CREATE DATABASE SCOPED CREDENTIAL {KeyAlias}
            WITH IDENTITY = ''asbkey'' ,
            Secret = ''{StorageAccountKey}''

    - Créer une source de données externes pour un blob Azure stockage

            CREATE EXTERNAL DATA SOURCE {nyctaxi_trip_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

            CREATE EXTERNAL DATA SOURCE {nyctaxi_fare_storage}
            WITH
            (
                TYPE = HADOOP,
                LOCATION =''wasbs://{ContainerName}@{StorageAccountName}.blob.core.windows.net'',
                CREDENTIAL = {KeyAlias}
            )
            ;

    - Créer un format de fichier externe pour un fichier csv. Les données sont compressées et champs sont séparés par le caractère barre verticale.

            CREATE EXTERNAL FILE FORMAT {csv_file_format}
            WITH
            (   
                FORMAT_TYPE = DELIMITEDTEXT,
                FORMAT_OPTIONS  
                (
                    FIELD_TERMINATOR ='','',
                    USE_TYPE_DEFAULT = TRUE
                )
            )
            ;

    - Créer des tarifs externes et des tableaux de voyage de NYC taxi dataset dans le stockage blob Azure.

            CREATE EXTERNAL TABLE {external_nyctaxi_fare}
            (
                medallion varchar(50) not null,
                hack_license varchar(50) not null,
                vendor_id char(3),
                pickup_datetime datetime not null,
                payment_type char(3),
                fare_amount float,
                surcharge float,
                mta_tax float,
                tip_amount float,
                tolls_amount float,
                total_amount float
            )
            with (
                LOCATION    = ''/nyctaxifare/'',
                DATA_SOURCE = {nyctaxi_fare_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12     
            )  


            CREATE EXTERNAL TABLE {external_nyctaxi_trip}
            (
                medallion varchar(50) not null,
                hack_license varchar(50)  not null,
                vendor_id char(3),
                rate_code char(3),
                store_and_fwd_flag char(3),
                pickup_datetime datetime  not null,
                dropoff_datetime datetime,
                passenger_count int,
                trip_time_in_secs bigint,
                trip_distance float,
                pickup_longitude varchar(30),
                pickup_latitude varchar(30),
                dropoff_longitude varchar(30),
                dropoff_latitude varchar(30)
            )
            with (
                LOCATION    = ''/nyctaxitrip/'',
                DATA_SOURCE = {nyctaxi_trip_storage},
                FILE_FORMAT = {csv_file_format},
                REJECT_TYPE = VALUE,
                REJECT_VALUE = 12         
            )

    - Charger les données de tables externe dans le stockage blob Azure SQL Data Warehouse

            CREATE TABLE {schemaname}.{nyctaxi_fare}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_fare}
            ;

            CREATE TABLE {schemaname}.{nyctaxi_trip}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            SELECT *
            FROM   {external_nyctaxi_trip}
            ;

    - Créer une table de données exemple (NYCTaxi_Sample) et insérer des données à celui-ci de sélectionner des requêtes SQL sur les tables de voyage et de prix. (Quelques étapes de cette procédure doit utiliser cet exemple de tableau).

            CREATE TABLE {schemaname}.{nyctaxi_sample}
            WITH
            (   
                CLUSTERED COLUMNSTORE INDEX,
                DISTRIBUTION = HASH(medallion)
            )
            AS
            (
                SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount,
                tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
                tip_class = CASE
                        WHEN (tip_amount = 0) THEN 0
                        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                        ELSE 4
                    END
                FROM {schemaname}.{nyctaxi_trip} t, {schemaname}.{nyctaxi_fare} f
                WHERE datepart("mi",t.pickup_datetime) = 1
                AND t.medallion = f.medallion
                AND   t.hack_license = f.hack_license
                AND   t.pickup_datetime = f.pickup_datetime
                AND   pickup_longitude <> ''0''
                AND   dropoff_longitude <> ''0''
            )
            ;

L’emplacement géographique de vos comptes de stockage affecte le temps de chargement.

>[AZURE.NOTE] Selon l’emplacement géographique de votre compte de stockage blob privé, le processus de copie des données à partir d’un blob publique à votre compte de stockage privé peut prendre environ 15 minutes, ou encore plus long, et le processus de chargement des données à partir de votre compte de stockage pour votre DW SQL Azure peut prendre 20 minutes ou plus.  

Vous devrez décider quelles si vous avez des fichiers source et de destination en double.

>[AZURE.NOTE] Si des fichiers .csv pour être copiée à partir du stockage d’objets blob public à votre compte de stockage blob privé existent déjà dans votre compte de stockage blob privé, AzCopy vous demande si vous souhaitez remplacer les. Si vous ne souhaitez pas les remplacer, d’entrée **n** lorsque vous y êtes invité. Si vous souhaitez remplacer **tous les** de saisie, leur **a** lorsque vous y êtes invité. Vous pouvez également d’entrée **y** pour remplacer les fichiers .csv individuellement.

![Tracer #21][21]

Vous pouvez utiliser vos propres données. Si vos données se trouve dans votre ordinateur localement dans votre application de vie réelle, vous pouvez toujours utiliser AzCopy pour télécharger des données locales sur le stockage blob Azure privé. Il suffit de modifier l’emplacement **Source** , `$Source = "http://getgoing.blob.core.windows.net/public/nyctaxidataset"`, dans la commande AzCopy du fichier de script PowerShell dans le répertoire local qui contient vos données.


>[AZURE.TIP] Si vos données sont déjà dans votre espace de stockage blob Azure privé dans votre application de vie réelle, vous pouvez ignorer l’étape AzCopy dans le script PowerShell et téléchargez directement les données Azure SQL DW. Cette opération nécessite des modifications supplémentaires du script pour l’adapter au format de vos données.


Ce script Powershell également s’intègre les informations Azure SQL DW dans les fichiers d’exemple de données de détails SQLDW_Explorations.sql, SQLDW_Explorations.ipynb et SQLDW_Explorations_Scripts.py afin que ces trois fichiers sont prêtes à être essayé instantanément une fois le script PowerShell terminé.

Après l’exécution réussie, vous verrez écran comme ci-dessous :

![][20]

## <a name="dbexplore"></a>Ingénierie fonctionnalité dans SQL Azure Data Warehouse et l’exploration de données

Dans cette section, effectuer des données et la fonctionnalité génération en exécutant des requêtes SQL sur Azure SQL DW directement à l’aide de **Visual Studio Data Tools**. Toutes les requêtes SQL utilisés dans cette section sont accessibles dans l’exemple de script nommé *SQLDW_Explorations.sql*. Ce fichier a déjà été téléchargé dans votre répertoire local par le script PowerShell. Vous pouvez également le récupérer à partir de [Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/SQLDW/SQLDW_Explorations.sql). Mais le fichier dans Github n’a pas les informations Azure SQL DW branchées.

Connectez-vous à votre DW SQL Azure à l’aide de Visual Studio avec le nom de connexion SQL DW et le mot de passe et ouvrez l' **Explorateur d’objets SQL** pour confirmer que la base de données et les tables ont été importés. Extraire le fichier *SQLDW_Explorations.sql* .

>[AZURE.NOTE] Pour ouvrir un éditeur de requête parallèle données Warehouse (PDW), utilisez la commande **Nouvelle requête** avec votre PDW est sélectionnée dans l' **Explorateur d’objets SQL**. L’éditeur de requête SQL standard n’est pas pris en charge par PDW.

Voici le type de données de détails et fonctionnalité de tâches de génération effectuée dans cette section :

- Explorez globales pour les données de plusieurs champs dans différentes fenêtres de temps.
- Examinez la qualité des données des champs de longitude et latitude.
- Générer des étiquettes de classification binaire et multiclass selon la **Conseil\_montant**.
- Générer des fonctionnalités et cluster/comparer distance de voyage.
- Joignez les deux tables et extraire un échantillon aléatoire qui servira à créer des modèles.

### <a name="data-import-verification"></a>Vérification d’importation de données

Ces requêtes fournissent une vérification rapide du nombre de lignes et colonnes dans les tableaux remplies précédemment à l’aide en bloc parallèle de Polybase importer,

    -- Report number of rows in table <nyctaxi_trip> without table scan
    SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')

    -- Report number of columns in table <nyctaxi_trip>
    SELECT COUNT(*) FROM information_schema.columns WHERE table_name = '<nyctaxi_trip>' AND table_schema = '<schemaname>'

**Sortie :** Vous devez obtenir 173,179,759 lignes et 14 colonnes.

### <a name="exploration-trip-distribution-by-medallion"></a>Exploration : Distribution de voyage par medallion

Cet exemple de requête identifie les medallions (nombres taxi) exécutées plus de 100 voyages au cours d’une période donnée. La requête pourra bénéficier de l’accès à la table partitionnée dans la mesure où elle est soumise par le schéma de partition de **collecte\_datetime**. Interroger le jeu de données complet fera également utiliser de la table partitionnée et/ou analyse d’index.

    SELECT medallion, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    GROUP BY medallion
    HAVING COUNT(*) > 100

**Sortie :** La requête doit renvoyer une table avec des lignes et en indiquant les 13,369 medallions (taxis) et le nombre de voyage effectuée par les dans 2013. La dernière colonne contienne le nombre du nombre de déplacements terminé.

### <a name="exploration-trip-distribution-by-medallion-and-hacklicense"></a>Exploration : Distribution de voyage en medallion et hack_license

Cet exemple identifie les medallions (nombres taxi) et hack_license numéros (pilotes) qui terminé plus de 100 voyages pendant une période spécifiée.

    SELECT medallion, hack_license, COUNT(*)
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
    GROUP BY medallion, hack_license
    HAVING COUNT(*) > 100

**Sortie :** La requête doit renvoyer une table avec 13,369 lignes spécifiant la voiture 13,369/pilote ID terminées que 100 les déplacements dans 2013. La dernière colonne contienne le nombre du nombre de déplacements terminé.

### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Évaluation de la qualité des données : Vérifiez que les enregistrements avec longitude incorrecte et/ou latitude

Cet exemple examine si un des champs longitude et/ou latitude soit contient une valeur non valide (degrés en radians doivent être comprise entre -90 et 90), ou que vous (0, 0) coordonnées.

    SELECT COUNT(*) FROM <schemaname>.<nyctaxi_trip>
    WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
    OR    (pickup_longitude = '0' AND pickup_latitude = '0')
    OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

**Sortie :** La requête renvoie 837,467 voyages qui comportent des champs longitude et/ou latitude non valides.

### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Exploration : Incliné et distribution voyages pas Bonhomme de neige

Cet exemple recherche le nombre de déplacements qui ont été incliné et le numéro qui n’ont pas incliné dans une période spécifiée (ou dans le groupe de données complet si portant sur l’année complète comme il est défini ici). Cette distribution reflète la distribution étiquette binaire à utiliser ultérieurement pour une modélisation classification binaire.

    SELECT tipped, COUNT(*) AS tip_freq FROM (
      SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
      FROM <schemaname>.<nyctaxi_fare>
      WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tipped

**Sortie :** La requête doit renvoyer la fréquence de conseil suivants pour l’année 2013 : 90,447,622 incliné et Bonhomme de neige non 82,264,709.

### <a name="exploration-tip-classrange-distribution"></a>Exploration : Distribution de classe/plage Conseil

Cet exemple calcule la répartition des plages de Conseil dans une période donnée (ou dans le groupe de données complet si portant sur l’année complète). Il s’agit de la distribution des classes étiquette qui seront utilisés ultérieurement pour une modélisation classification multiclass.

    SELECT tip_class, COUNT(*) AS tip_freq FROM (
        SELECT CASE
            WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_fare>
    WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
    GROUP BY tip_class

**Résultat :**

|tip_class  | tip_freq |
| --------- | -------|
|1  | 82230915 |
|2  | 6198803 |
|3  | 1932223 |
|0  | 82264625 |
|4  | 85765 |

### <a name="exploration-compute-and-compare-trip-distance"></a>Exploration : Calculer et comparer des distance de voyage

Cet exemple convertit la collecte et remise longitude et latitude géographie SQL pointe, calcule la distance de voyage à l’aide de SQL géographie points différence et retourne un échantillon aléatoire des résultats de la comparaison. L’exemple limite les résultats à coordonnées valides uniquement à l’aide de la requête d’évaluation de la qualité données évoquée précédemment.

    /****** Object:  UserDefinedFunction [dbo].[fnCalculateDistance] ******/
    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function to calculate the direct distance  in mile between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

### <a name="feature-engineering-using-sql-functions"></a>Ingénierie fonctionnalité à l’aide de fonctions SQL

Fonctions SQL peuvent parfois être une option permettant d’ingénierie fonctionnalité efficace. Dans cette procédure, nous avons défini une fonction SQL pour calculer la distance entre les emplacements de collecte et cette chute directe. Vous pouvez exécuter les scripts SQL suivants dans **Visual Studio Data Tools**.

Voici le script SQL qui définit la fonction distance.

    SET ANSI_NULLS ON
    GO

    SET QUOTED_IDENTIFIER ON
    GO

    IF EXISTS (SELECT * FROM sys.objects WHERE type IN ('FN', 'IF') AND name = 'fnCalculateDistance')
      DROP FUNCTION fnCalculateDistance
    GO

    -- User-defined function calculate the direct distance between two geographical coordinates.
    CREATE FUNCTION [dbo].[fnCalculateDistance] (@Lat1 float, @Long1 float, @Lat2 float, @Long2 float)

    RETURNS float
    AS
    BEGIN
        DECLARE @distance decimal(28, 10)
        -- Convert to radians
        SET @Lat1 = @Lat1 / 57.2958
        SET @Long1 = @Long1 / 57.2958
        SET @Lat2 = @Lat2 / 57.2958
        SET @Long2 = @Long2 / 57.2958
        -- Calculate distance
        SET @distance = (SIN(@Lat1) * SIN(@Lat2)) + (COS(@Lat1) * COS(@Lat2) * COS(@Long2 - @Long1))
        --Convert to miles
        IF @distance <> 0
        BEGIN
            SET @distance = 3958.75 * ATAN(SQRT(1 - POWER(@distance, 2)) / @distance);
        END
        RETURN @distance
    END
    GO

Voici un exemple d’appeler cette fonction pour générer des fonctionnalités dans votre requête SQL :

    -- Sample query to call the function to create features
    SELECT pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude,
    dbo.fnCalculateDistance(pickup_latitude, pickup_longitude, dropoff_latitude, dropoff_longitude) AS DirectDistance
    FROM <schemaname>.<nyctaxi_trip>
    WHERE datepart("mi",pickup_datetime)=1
    AND CAST(pickup_latitude AS float) BETWEEN -90 AND 90
    AND CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
    AND pickup_longitude != '0' AND dropoff_longitude != '0'

**Sortie :** Cette requête génère une table (avec 2,803,538 lignes) avec collecte et cette chute Latitude et longitudes et la distance directe correspondante en miles. Voici les résultats pour les 3 premières lignes :

||pickup_latitude | pickup_longitude    | dropoff_latitude |dropoff_longitude | DirectDistance |
|---| --------- | -------|-------| --------- | -------|
|1  | 40.731804 | -74.001083 | 40.736622 | -73.988953 | .7169601222 |
|2  | 40.715794 | -74,010635 | 40.725338 | -74.00399 | .7448343721 |
|3  | 40.761456 | -73.999886 | 40.766544 | -73.988228 | 0.7037227967 |



### <a name="prepare-data-for-model-building"></a>Préparer des données pour la création de modèles

La requête suivante jointures la **nyctaxi\_voyage** et **nyctaxi\_tarif** des tables, génère une classification binaire étiquette **Bonhomme de neige**, une étiquette de classification de classe à plusieurs **Conseil\_classe**et extrait un échantillon du jeu de données joints complète. L’échantillon est terminé en récupérant un sous-ensemble des déplacements en fonction de l’heure d’extraction.  Cette requête peut être copiée puis collée directement dans les [Azure Machine apprentissage Studio](https://studio.azureml.net) [Importer des données] [ import-data] module pour la réception de données directement à partir de l’instance de base de données SQL Azure. La requête exclut les enregistrements avec incorrecte (0, 0) coordonnées.

    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
        CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
        CASE WHEN (tip_amount = 0) THEN 0
            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
            ELSE 4
        END AS tip_class
    FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
    WHERE datepart("mi",t.pickup_datetime) = 1
    AND   t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude != '0' AND dropoff_longitude != '0'

Lorsque vous êtes prêt à passer à l’apprentissage automatique Azure, vous pouvez :  

1. Enregistrer la requête SQL finale pour extraire et exemples de données et copier-coller la requête directement dans un [Importer des données] [ import-data] module dans l’apprentissage automatique Azure, ou
2. Conserver les données échantillonnées et conçues que vous prévoyez d’utiliser pour le modèle de création dans une nouvelle table SQL DW et utilisez la nouvelle table dans les [Importer des données] [ import-data] module dans l’apprentissage automatique Azure. Le script PowerShell à l’étape précédente a terminé cette procédure pour vous. Vous pouvez lire directement à partir de ce tableau dans le module importer des données.


## <a name="ipnb"></a>Ingénierie fonctionnalité dans IPython bloc-notes et l’exploration de données

Dans cette section, nous allons exécuter la génération de fonctionnalité à l’aide de ces deux Python et l’exploration de données et des requêtes SQL sur la DW SQL créé précédemment. Un bloc-notes IPython exemple nommé **SQLDW_Explorations.ipynb** et un fichier de script Python **SQLDW_Explorations_Scripts.py** ont été téléchargés vers votre répertoire local. Ils sont également disponibles sur [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/SQLDW). Ces deux fichiers sont identiques dans les scripts Python. Le fichier de script Python est fourni au cas où vous ne disposez pas d’un serveur IPython bloc-notes. Ces deux exemples de fichiers Python sont conçus sous **Python 2.7**.

Les informations d’Azure SQL DW nécessaires dans l’échantillon IPython bloc-notes et le fichier de script Python téléchargement sur votre ordinateur local a été branchées précédemment par le script PowerShell. Elles peuvent être exécutées sans aucune modification.

Si vous avez déjà configuré un espace de travail AzureML, vous pouvez directement télécharger l’échantillon IPython bloc-notes au service AzureML IPython bloc-notes et démarrer son exécution. Voici la procédure pour télécharger sur service AzureML IPython bloc-notes :

1. Connectez-vous à votre espace de travail AzureML, cliquez sur « Studio » dans la partie supérieure, puis cliquez sur « Blocs-notes » sur le côté gauche de la page web.

    ![Tracer #22][22]

2. Cliquez sur « Nouveau » dans le coin inférieur gauche de la page web, puis sélectionnez « Python 2". Ensuite, entrez un nom pour le bloc-notes et cliquez sur la case à cocher pour créer le nouveau bloc-notes IPython vide.

    ![Tracer #23][23]

3. Cliquez sur le symbole « Jupyter » dans le coin supérieur gauche du nouveau bloc-notes IPython.

    ![Tracer #24][24]

4. Faites glisser et déposez l’échantillon IPython bloc-notes à la page **arborescence** de votre service AzureML IPython bloc-notes, puis cliquez sur **Télécharger**. Ensuite, l’échantillon Qu'ipython bloc-notes seront téléchargées sur le service AzureML IPython bloc-notes.

    ![Tracer #25][25]

Pour exécuter l’exemple IPython bloc-notes ou Python fichier de script, Python suivant packages sont nécessaires. Si vous utilisez le service AzureML IPython bloc-notes, ces packages ont été préinstallés.

    - pandas
    - numpy
    - matplotlib
    - pyodbc
    - PyTables

La séquence recommandée lors de la création des solutions analytiques avancées sur AzureML avec une grande quantité de données est la suivante :

- Lire dans un petit exemple de données dans un bloc de données en mémoire.
- Effectuer certaines visualisations explorations utilisent les données échantillonnées.
- Tester les ingénierie fonctionnalité utilisent les données échantillonnées.
- Pour Explorer les données plus grande, manipulation des données et ingénierie fonctionnalité, utilisez Python pour émettre des requêtes SQL directement sur la DW SQL.
- Déterminez la taille de l’échantillon pouvant être construction d’un modèle apprentissage automatique Azure.

Types suivants sont quelques des données, la visualisation des données et fonctionnalité ingénierie exemples. Vous trouverez plus explorations de données dans l’échantillon IPython bloc-notes et l’exemple de fichier de script Python.

### <a name="initialize-database-credentials"></a>Initialisation des informations d’identification de la base de données

Initialisation vos paramètres de connexion de base de données dans les variables suivantes :

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database driver>

### <a name="create-database-connection"></a>Créer la connexion de base de données

Voici la chaîne de connexion qui crée la connexion à la base de données.

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

### <a name="report-number-of-rows-and-columns-in-table-nyctaxitrip"></a>Nombre de rapport de lignes et colonnes de table < nyctaxi_trip >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_trip>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_trip>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Nombre total de lignes = 173179759  
- Nombre total de colonnes = 14

### <a name="report-number-of-rows-and-columns-in-table-nyctaxifare"></a>Nombre de rapport de lignes et colonnes de table < nyctaxi_fare >

    nrows = pd.read_sql('''
        SELECT SUM(rows) FROM sys.partitions
        WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_fare>')
    ''', conn)

    print 'Total number of rows = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''
        SELECT COUNT(*) FROM information_schema.columns
        WHERE table_name = ('<nyctaxi_fare>') AND table_schema = ('<schemaname>')
    ''', conn)

    print 'Total number of columns = %d' % ncols.iloc[0,0]

- Nombre total de lignes = 173179759  
- Nombre total de colonnes = 11

### <a name="read-in-a-small-data-sample-from-the-sql-data-warehouse-database"></a>Lecture d’un échantillon de données de petite taille de la base de données de magasin de données SQL

    t0 = time.time()

    query = '''
        SELECT TOP 10000 t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
            f.tolls_amount, f.total_amount, f.tip_amount
        FROM <schemaname>.<nyctaxi_trip> t, <schemaname>.<nyctaxi_fare> f
        WHERE datepart("mi",t.pickup_datetime) = 1
        AND   t.medallion = f.medallion
        AND   t.hack_license = f.hack_license
        AND   t.pickup_datetime = f.pickup_datetime
    '''

    df1 = pd.read_sql(query, conn)

    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)

    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Temps de lire que l’exemple de tableau est 14.096495 secondes.  
Nombre de lignes et colonnes récupérées = (1000, 21).

### <a name="descriptive-statistics"></a>Statistiques descriptives

Vous êtes maintenant prêt à Explorer les données échantillonnées. Commençons par examiner certaines statistiques descriptives pour la **voyage\_distance** (ou d’autres champs que vous choisissez de spécifier).

    df1['trip_distance'].describe()

### <a name="visualization-box-plot-example"></a>Visualisation : Exemple de traçage de zone

Ensuite, nous examinons le traçage de la zone pour la distance voyage visualiser l’étendue.

    df1.boxplot(column='trip_distance',return_type='dict')

![Tracer #1][1]

### <a name="visualization-distribution-plot-example"></a>Visualisation : Exemple de traçage de Distribution

Tracés qui visualiser la distribution et un histogramme pour la distance voyage échantillonnée.

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Tracer #2][2]

### <a name="visualization-bar-and-line-plots"></a>Visualisation : Barres et courbes tracés

Dans cet exemple, nous bin la distance de voyage dans des emplacements de cinq et visualiser les résultats de la mise au rebus.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

Nous pouvons tracer la distribution bin ci-dessus dans une barre ou tracé avec la ligne :

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Tracer #3][3]

et

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Tracer #4][4]

### <a name="visualization-scatterplot-examples"></a>Visualisation : Exemples de Scatterplot

Nous allons vous montrer nuage de points entre **voyage\_heure\_dans\_secondes** et **voyage\_distance** pour savoir s’il existe une corrélation

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Tracer #6][6]

De même, nous pouvons vérifier la relation entre **taux\_code** et **voyage\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Tracer #8][8]


### <a name="data-exploration-on-sampled-data-using-sql-queries-in-ipython-notebook"></a>Explorer les données dans les exemples de données à l’aide de requêtes SQL dans IPython bloc-notes

Dans cette section, nous Explorer globales pour les données en utilisant les données échantillonnées qui sont conservées dans la nouvelle table que nous avons créé ci-dessus. Notez qu’explorations similaires peuvent être exécutées en utilisant les tables d’origine.

#### <a name="exploration-report-number-of-rows-and-columns-in-the-sampled-table"></a>Exploration : Signaler le nombre de lignes et colonnes de la table échantillonnée

    nrows = pd.read_sql('''SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('<schemaname>.<nyctaxi_sample>')''', conn)
    print 'Number of rows in sample = %d' % nrows.iloc[0,0]

    ncols = pd.read_sql('''SELECT count(*) FROM information_schema.columns WHERE table_name = ('<nyctaxi_sample>') AND table_schema = '<schemaname>'''', conn)
    print 'Number of columns in sample = %d' % ncols.iloc[0,0]

#### <a name="exploration-tippednot-tripped-distribution"></a>Exploration : Incliné / n’est pas dépassé la Distribution

    query = '''
        SELECT tipped, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tipped
        '''

    pd.read_sql(query, conn)

#### <a name="exploration-tip-class-distribution"></a>Exploration : Distribution de classe Conseil

    query = '''
        SELECT tip_class, count(*) AS tip_freq
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY tip_class
    '''

    tip_class_dist = pd.read_sql(query, conn)

#### <a name="exploration-plot-the-tip-distribution-by-class"></a>Exploration : Tracer la distribution de Conseil en cours

    tip_class_dist['tip_freq'].plot(kind='bar')

![Tracer 26 #][26]


#### <a name="exploration-daily-distribution-of-trips"></a>Exploration : Distribution quotidienne de déplacements

    query = '''
        SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY CONVERT(date, dropoff_datetime)
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-per-medallion"></a>Exploration : Distribution de voyage par medallion

    query = '''
        SELECT medallion,count(*) AS c
        FROM <schemaname>.<nyctaxi_sample>
        GROUP BY medallion
    '''

    pd.read_sql(query,conn)

#### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Exploration : Distribution de voyage par licence medallion et piratage

    query = '''select medallion, hack_license,count(*) from <schemaname>.<nyctaxi_sample> group by medallion, hack_license'''
    pd.read_sql(query,conn)


#### <a name="exploration-trip-time-distribution"></a>Exploration : Répartition des heures voyage

    query = '''select trip_time_in_secs, count(*) from <schemaname>.<nyctaxi_sample> group by trip_time_in_secs order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-trip-distance-distribution"></a>Exploration : Distribution de distance voyage

    query = '''select floor(trip_distance/5)*5 as tripbin, count(*) from <schemaname>.<nyctaxi_sample> group by floor(trip_distance/5)*5 order by count(*) desc'''
    pd.read_sql(query,conn)

#### <a name="exploration-payment-type-distribution"></a>Exploration : Distribution du type de paiement

    query = '''select payment_type,count(*) from <schemaname>.<nyctaxi_sample> group by payment_type'''
    pd.read_sql(query,conn)

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Vérifiez que l’écran final de la table featurized

    query = '''SELECT TOP 100 * FROM <schemaname>.<nyctaxi_sample>'''
    pd.read_sql(query,conn)

## <a name="mlmodel"></a>Créer des modèles dans l’apprentissage automatique Azure

Nous sommes maintenant prêts à passer à la création de modèles et déploiement de modèles dans [l’Apprentissage automatique Azure](https://studio.azureml.net). Les données sont prêtes à utiliser un des problèmes de prévision identifiées précédemment, à savoir :

1. **Classement binaire**: prévoir ou non une info-bulle a été payée pour un voyage.

2. **Classification multiclass**: prévoir la plage de conseil payé, selon les classes définis précédemment.

3. **Tâche de régression**: prévoir la quantité de conseil payé pour un voyage.  



Pour commencer l’exercice de modélisation, connectez-vous à votre espace de travail **d’Apprentissage automatique Azure** . Si vous n’avez pas encore créé un apprentissage espace de travail de l’ordinateur, voir [créer un espace de travail ML Azure](machine-learning-create-workspace.md).

1. Pour commencer à utiliser l’apprentissage automatique Azure, voir [Nouveautés Azure Machine apprentissage Studio ?](machine-learning-what-is-ml-studio.md)

2. Se connecter à [Azure Machine d’apprentissage Studio](https://studio.azureml.net).

3. La page d’accueil Studio fournit une mine d’informations, vidéos, didacticiels, des liens vers la référence de Modules et d’autres ressources. Pour plus d’informations sur l’apprentissage automatique Azure, consultez la [Documentation Azure Machine formations](https://azure.microsoft.com/documentation/services/machine-learning/).

Une expérience de formation classique se compose des étapes suivantes :

1. Créer une expérience **+ Nouveau** .
2. Importer les données dans Azure ML.
3. Prétraitement, transformer et manipuler les données selon vos besoins.
4. Générer des fonctionnalités selon vos besoins.
5. Fractionner les données en formation/validation/tester les jeux de données (ou ont des jeux de données distincte pour chaque).
6. Sélectionnez un ou plusieurs algorithmes d’apprentissage machine selon l’apprentissage problème à résoudre. Par exemple, classement binaire, classification multiclass, régression.
7. Former un ou plusieurs modèles à l’aide de la formation dataset.
8. Score le dataset de validation à l’aide des ou les modèles formés.
9. Évaluer l’ou les modèles pour calculer les mesures pertinentes pour le problème de formation.
10. Optimiser régler l’ou les modèles et sélectionnez le meilleur modèle à déployer.

Dans cet exercice, nous avons déjà exploré conçu les données dans Data Warehouse SQL et décidé sur la taille de l’échantillon d’acquisition dans Azure ML. Voici la procédure permettant de créer une ou plusieurs des modèles de prévision :

1. Importer les données dans ML Azure à l’aide de l' [Importation de données] [ import-data] module, disponible dans la section **données d’entrée et sortie** . Pour plus d’informations, voir [Importer des données] [ import-data] page de référence du module.

    ![Azure ML importer des données][17]

2. Sélectionnez la **Base de données SQL Azure** comme **source de données** dans le volet de **Propriétés** .

3. Entrez le nom DNS de base de données dans le champ **nom du serveur de base de données** . Format :`tcp:<your_virtual_machine_DNS_name>,1433`

4. Entrez le **nom de la base de données** dans le champ correspondant.

5. Entrez le *nom d’utilisateur SQL* dans le **nom du compte utilisateur Server**et le *mot de passe* mot de **passe du compte serveur**.

6. Cochez l’option **accepter n’importe quel certificat de serveur** .

7. Dans la zone de texte de modifier **une requête** , collez la requête qui extrait les nécessaires (y compris les calculées telles que les étiquettes) des champs de base de données et vers le bas exemples les données à la taille de l’échantillon souhaité.

Exemple d’une expérience de classification binaire la lecture de données directement à partir de la base de données SQL Data Warehouse se trouve dans l’illustration suivante (n’oubliez pas de remplacer la table noms nyctaxi_trip et nyctaxi_fare par le nom de schéma et les noms de table que vous avez utilisées dans votre procédure pas à pas). Expériences similaires peuvent être créés pour classification multiclass et résoudre les problèmes de régression.

![Train ML Azure][10]

> [AZURE.IMPORTANT] Les données de modélisation d’extraction et d’échantillonnage exemples de requête fournies dans les sections précédentes, **toutes les étiquettes relatives aux exercices trois modélisation sont inclus dans la requête**. Un pas important (obligatoire) dans chacune des exercices modélisation consiste à **Exclure** les étiquettes pour les deux autres problèmes et toute autre **cible perd**inutiles. Par exemple, lorsque vous utilisez classification binaire, utilisez l’étiquette à **incliné** et exclure les champs **Conseil\_classe**, **Conseil\_montant**, et **total\_montant**. Ces derniers sont pertes cible dans la mesure où ils impliquent le Conseil payé.
>
> Pour exclure les colonnes inutiles ou cibler pertes, vous pouvez utiliser la [Sélection des colonnes dans le jeu de données] [ select-columns] module ou les [Modifier les métadonnées][edit-metadata]. Pour plus d’informations, consultez [Sélectionner des colonnes dans le jeu de données] [ select-columns] et [Modifier les métadonnées] [ edit-metadata] référence à des pages.

## <a name="mldeploy"></a>Déployer des modèles dans l’apprentissage automatique Azure

Lorsque votre modèle est prêt, vous pouvez facilement le déployer comme un service web directement à partir de l’expérience. Pour plus d’informations sur le déploiement des services web Azure ML, voir [déploiement d’un service web d’apprentissage automatique Azure](machine-learning-publish-a-machine-learning-web-service.md).

Pour déployer un nouveau service web, vous devez :

1. Créer une expérience score.
2. Déployer le service web.

Pour créer une expérience score à partir d’une expérience de formation **terminé** , cliquez sur **Créer des scores tester** dans la barre d’action inférieure.

![Notation Azure][18]

Azure apprentissage automatique tentera de créer une expérience score basée sur les composants de l’expérience de formation. En particulier, il sera :

1. Enregistrez le modèle formé et supprimez les modules de formation de modèle.
2. Identifier un logique **port d’entrée** pour représenter le schéma de données d’entrée attendu.
3. Identifier un logique **port de sortie** pour représenter le schéma de sortie de service web attendu.

Lors de la création de l’expérience score, consulter et faire ajuster selon vos besoins. Exemple d’ajustement classique consiste à remplacer le jeu de données d’entrée et/ou la requête par un qui exclut les champs d’étiquettes, comme celles-ci ne seront pas disponibles lorsque le service est appelé. Il est également judicieux de réduire la taille de la requête et/ou du jeu de données d’entrée à plusieurs enregistrements, juste assez pour indiquer le schéma d’entrée. Pour le port de sortie, il s’agit courant pour exclure des champs d’entrée tout et inclure uniquement les **Étiquettes a obtenu** et **Probabilités a obtenu** dans les résultats à l’aide de la [Sélection des colonnes dans le jeu de données] [ select-columns] module.

Un exemple de notation expérience est fourni dans l’illustration ci-dessous. Lorsque vous êtes prêt à déployer, cliquez sur le bouton **Publier un SERVICE WEB** dans la barre d’action inférieur.

![Publier ML Azure][11]


## <a name="summary"></a>Résumé
Pour résumer ce que nous avons fait dans ce didacticiel procédure pas à pas, vous avez créé un environnement scientifique données Azure, a travaillé avec un grand jeu de données public, en tenant dans le processus d’organisation scientifique de données d’équipe, tout à fait de l’acquisition de données au modèle de formation, puis vers le déploiement d’un service web apprentissage automatique Azure.

### <a name="license-information"></a>Informations de licence

Cette procédure pas à pas exemples et son qui accompagne les scripts et IPython notebook(s) sont partagées par Microsoft sous licence. Veuillez archiver ce fichier dans le répertoire des exemples de code sur GitHub pour plus d’informations.

## <a name="references"></a>Références

• [Andrés Monroy NYC Taxi voyages Page de téléchargement](http://www.andresmh.com/nyctaxitrips/)  
• [Blocage des données de voyage de NYC Taxi par Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi et briefings Commission recherche ainsi que les statistiques](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sqldw-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sqldw-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sqldw-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sqldw-walkthrough/amlscoring.png
[19]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_download_scripts.png
[20]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ps_load_data.png
[21]: ./media/machine-learning-data-science-process-sqldw-walkthrough/azcopy-overwrite.png
[22]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-1.png
[23]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-2.png
[24]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-3.png
[25]: ./media/machine-learning-data-science-process-sqldw-walkthrough/ipnb-service-aml-4.png
[26]: ./media/machine-learning-data-science-process-sqldw-walkthrough/tip_class_hist_1.png


<!-- Module References -->
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
