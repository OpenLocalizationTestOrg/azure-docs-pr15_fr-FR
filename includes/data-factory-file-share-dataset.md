## <a name="fileshare-dataset-type-properties"></a>Propriétés du type dataset partage de fichiers

Pour une liste complète des sections et les propriétés disponibles pour la définition des groupes de données, voir l’article [Création de groupes de données](../articles/data-factory/data-factory-create-datasets.md) . Sections telles que la structure, la disponibilité et stratégie d’un dataset JSON sont similaires pour tous les types de jeu de données. 

La section **typeProperties** est différente pour chaque type de jeu de données. Il fournit des informations spécifiques au type de jeu de données. La section typeProperties pour un jeu de données de type de **partage de fichiers** dataset comporte les propriétés suivantes :

Propriété | Description | Obligatoire
-------- | ----------- | --------
folderPath | Chemin d’accès Sub vers le dossier. Utiliser le caractère d’échappement ' \ ' des caractères spéciaux dans la chaîne. Pour obtenir des exemples, voir [exemple lié définitions de service et de jeu de données](#sample-linked-service-and-dataset-definitions) .<br/><br/>Vous pouvez combiner cette propriété avec **partitionBy** pour que les chemins d’accès basés sur secteur date et l’heure de début/fin. | Oui
nom de fichier | Spécifiez le nom du fichier dans la **folderPath** si vous souhaitez que le tableau pour faire référence à un fichier spécifique dans le dossier. Si vous ne spécifiez pas une valeur de cette propriété, la table pointe vers tous les fichiers dans le dossier.<br/><br/>Lorsque le nom de fichier n’est pas spécifié pour un dataset de sortie, le nom du fichier généré serait dans ce qui suit ce format : <br/><br/>Données. <Guid>.txt (exemple : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | N°
partitionedBy | partitionedBy peut servir à spécifier une dynamique folderPath, nom de fichier de données de série de date. Par exemple, folderPath paramétrée pour toutes les heures de données. | N°
Mettre en forme | Les types de formats suivants sont pris en charge : **format du texte**, **AvroFormat**, **JsonFormat**et **OrcFormat**. Définissez **la propriété sous format** à un de ces valeurs. Voir les sections [Spécifiant format du texte](#specifying-textformat), [AvroFormat spécifiant](#specifying-avroformat), [JsonFormat spécifiant](#specifying-jsonformat)et [Spécifiant OrcFormat](#specifying-orcformat) pour plus d’informations. Si vous voulez copier des fichiers en tant que-est entre magasins basés sur des fichiers (copie binaire), vous pouvez ignorer la section format dans des définitions de jeu de données d’entrée et de sortie. | N°
fileFilter | Spécifier un filtre à utiliser pour sélectionner un sous-ensemble de fichiers dans le folderPath plutôt que tous les fichiers.<br/><br/>Valeurs autorisées sont : `*` (plusieurs caractères) et `?` (un seul caractère).<br/><br/>Exemple 1 :`"fileFilter": "*.log"`<br/>Exemple 2 :`"fileFilter": 2014-1-?.txt"`<br/><br/> fileFilter est applicable pour un partage de fichiers de données d’entrée. Cette propriété n’est pas pris en charge avec HADOOP.  | N°
| compression | Spécifier le type et le niveau de compression pour les données. Types pris en charge sont : **GZip**, **Deflate**et **BZip2** et les niveaux de prise en charge : **Optimal** et **plus rapide**. Pour l’instant, les paramètres de compression ne sont pas prises en charge des données dans **AvroFormat** ou **OrcFormat**. Pour plus d’informations, reportez-vous à la section [prise en charge de la Compression](#compression-support) .  | N° |
| useBinaryTransfer | Spécifier si utiliser le mode de transfert binaire. Valeur True pour le mode binaire et ASCII false. Valeur par défaut : True. Cette propriété peut uniquement être utilisée lorsque service liées associé est du type : Serveur_ftp. | N° | 
 

> [AZURE.NOTE] nom de fichier et FiltreFichier ne peuvent pas être utilisés simultanément.

### <a name="using-partionedby-property"></a>À l’aide de la propriété partionedBy

Comme indiqué dans la section précédente, vous pouvez spécifier une dynamique folderPath, nom de fichier de données de série de date avec partitionedBy. Vous pouvez le faire avec les macros de données usine et la variable système SliceStart, SliceEnd qui indiquent la logique période pour une section donnée. 

Pour en savoir plus sur les jeux de données de série heure, en planifiant et secteurs, voir [Créer des groupes de données](../articles/data-factory/data-factory-create-datasets.md), [planification et l’exécution](../articles/data-factory/data-factory-scheduling-and-execution.md)et [Pipelines de création](../articles/data-factory/data-factory-create-pipelines.md) d’articles. 

#### <a name="sample-1"></a>Exemple 1 :

    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy": 
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

Dans cet exemple {section} est remplacée par la valeur de la variable Data Factory système SliceStart au format (YYYYMMDDHH) spécifiée. La SliceStart fait référence pour l’heure de début de la section. La folderPath est différente pour chaque secteur. Exemple : wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Exemple 2 :

    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

Dans cet exemple, année, mois, jour et l’heure de SliceStart sont extraits dans des variables distinctes qui sont utilisées par les propriétés folderPath et nom de fichier.
