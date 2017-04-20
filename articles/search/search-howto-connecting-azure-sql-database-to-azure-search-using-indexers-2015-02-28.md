<properties 
    pageTitle="Connexion de base de données SQL Azure à Azure recherche à l’aide d’indexeurs | Microsoft Azure | Indexeurs" 
    description="Découvrez comment extraire des données à partir de la base de données SQL Azure à un index de recherche Azure à l’aide d’indexeurs." 
    services="search" 
    documentationCenter="" 
    authors="chaosrealm" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="05/26/2016" 
    ms.author="eugenesh"/>

#<a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Connexion de base de données SQL Azure à recherche Azure à l’aide d’indexeurs

Service de recherche Azure est un service de recherche cloud hébergé qui facilite la fournir une expérience de recherche exceptionnelle. Vous pouvez rechercher, vous devez remplir un index de recherche Azure avec vos données. Si les données sont situées dans une base de données SQL Azure, la nouvelle **recherche Azure indexeur de base de données SQL Azure** (ou **SQL Azure indexeur** abrégé) dans Azure recherche automatiser le processus d’indexation. Cela signifie que vous avez moins de code pour écrire et moins infrastructure intéresser.

Pour l’instant, les indexeurs fonctionnent uniquement avec la base de données SQL Azure, SQL Server sur machines virtuelles Azure et [Azure DocumentDB](../documentdb/documentdb-search-indexer.md). Dans cet article, nous allons nous concentrer sur indexeurs fonctionnant avec la base de données SQL Azure. Si vous voulez voir prise en charge des sources de données supplémentaires, veuillez fournir vos commentaires sur le [forum de commentaires Azure recherche](https://feedback.azure.com/forums/263029-azure-search/).

Cet article aborde les mécanismes de l’utilisation d’indexeurs, mais nous allons également Explorer les fonctionnalités et les comportements sont uniquement disponibles avec les bases de données SQL (par exemple, intégrée suivi des modifications).

## <a name="indexers-and-data-sources"></a>Indexeurs et sources de données

Pour installer et configurer un indexeur SQL Azure, vous pouvez appeler l' [API REST de recherche Azure](http://go.microsoft.com/fwlink/p/?LinkID=528173) pour créer et gérer des **sources de données**et **indexeurs** . 

Vous pouvez également utiliser la [classe indexeur](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.indexer.aspx) dans l’Assistant Importation de données ou le [Kit de développement .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx)dans le [portail Azure](https://portal.azure.com) pour créer et planifier un indexeur.

Une **source de données** spécifie les données à indexer, les informations d’identification nécessaires pour accéder aux données, ainsi que les stratégies qui permettent d’Azure recherche identifier efficacement les modifications des données (nouveaux, modifiés ou supprimés lignes). Elle est définie comme ressources indépendantes afin qu’il peut être utilisé par plusieurs indexeurs.

Un **indexeur** est une ressource qui se connecte des sources de données avec l’index de recherche cible. Un indexeur est utilisé comme suit :
 
- Effectuer une copie des données pour remplir un index unique.
- Mettre à jour un index des modifications de la source de données sur un planning.
- Exécuter à la demande pour mettre à jour un index selon vos besoins. 

## <a name="when-to-use-azure-sql-indexer"></a>Quand utiliser indexeur SQL Azure

En fonction de plusieurs facteurs relatives à vos données, l’utilisation d’indexeur SQL Azure peut ou ne peut pas être appropriée. Si vos données est adaptée à la configuration requise suivante, vous pouvez utiliser SQL Azure indexeur : 

- Toutes les données proviennent d’une seule table ou une vue
    - Si les données sont réparties sur plusieurs tables, vous pouvez créer un affichage et utiliser cette vue avec l’indexeur. Toutefois, n’oubliez pas que si vous utilisez un affichage, vous ne pourrez peut-être utiliser la détection de modification intégrée de SQL Server. Consultez cette section pour plus d’informations. 
- Les types de données utilisés dans la source de données sont pris en charge par l’indexeur. La plupart des mais pas la totalité de l’instruction SQL types sont pris en charge. Pour plus d’informations, consultez [mappage des types de données dans Azure recherche](http://go.microsoft.com/fwlink/p/?LinkID=528105). 
- Vous ne devez à proximité des mises à jour en temps réel à l’index lorsqu’une ligne est modifiée. 
    - L’indexeur permet de réindexer votre tableau au maximum 5 minutes. Si vos données changent fréquemment et les modifications doivent être reflétées dans l’index quelques secondes ou minutes unique, nous vous recommandons d’à l’aide de [L’API Index de recherche Azure](https://msdn.microsoft.com/library/azure/dn798930.aspx) directement. 
- Si vous possédez un jeu de données volumineux et que vous souhaitez exécuter l’indexeur sur un planning, votre schéma permet d’identifier efficacement modifié (et supprimée, le cas échéant) lignes. Pour plus d’informations, voir « Capturant et supprimés lignes modifiées » ci-dessous. 
- La taille des champs indexés dans une ligne ne dépasse pas la taille maximale d’une recherche Azure l’indexation de la demande, ce qui correspond à 16 Mo. 

## <a name="create-and-use-an-azure-sql-indexer"></a>Créer et utiliser un indexeur SQL Azure

Commencez par créer la source de données : 

    POST https://myservice.search.windows.net/datasources?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }


Vous pouvez obtenir la chaîne de connexion à partir du [Portail classique Azure](https://portal.azure.com); utiliser la `ADO.NET connection string` option.

Ensuite, créez l’index de recherche Azure cible si vous n’en avez pas déjà. Vous pouvez le faire à partir du [portail de l’interface utilisateur](https://portal.azure.com) ou à l’aide de l' [API de Index créer](https://msdn.microsoft.com/library/azure/dn798941.aspx).  Assurez-vous que le schéma de votre index cible est compatible avec le schéma de la table source - voir [mappage SQL Azure recherche types de données et](#TypeMapping) pour plus d’informations.

Enfin, créez l’indexeur en lui donner un nom et en référençant l’index de données sources et cibles :

    POST https://myservice.search.windows.net/indexers?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key
    
    { 
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }

Un indexeur créé de cette manière n’est pas une planification. Il s’exécute automatiquement une fois dès qu’il est créé. Vous pouvez l’exécuter à tout moment à l’aide d’une demande **d’exécuter indexeur** :

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2015-02-28 
    api-key: admin-key

Vous pouvez personnaliser plusieurs aspects du comportement d’indexeur, telles que la taille du lot et le nombre de documents peut être ignoré avant l’échec de l’exécution d’une indexeur. Pour plus d’informations, voir [Créer des API indexeur](https://msdn.microsoft.com/library/azure/dn946899.aspx).
 
Vous devrez peut-être autoriser des services Azure pour vous connecter à votre base de données. Pour obtenir des instructions sur la procédure à suivre, voir [Se connecter à partir d’Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) .

Pour contrôler le statut de l’indexation et l’exécution de l’historique (nombre d’éléments indexés, échecs, etc.), utilisez une demande de **statut de l’indexation** : 

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2015-02-28 
    api-key: admin-key

La réponse doit ressembler à ce qui suit : 

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null 
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null 
            },
            ... earlier history items 
        ]
    }

L’historique d’exécution contient jusqu'à 50 des exécutions effectuées récemment, qui sont triées dans l’ordre chronologique inverse (de sorte que l’exécution de dernière vient en premier dans la réponse). Vous trouverez plus d’informations sur la réponse dans [Statut obtenir de l’indexation](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Exécuter indexeurs sur une planification

Vous pouvez également organiser l’indexeur à exécuter régulièrement sur une planification. Pour ce faire, il suffit d’ajouter la propriété de **planification** lorsque vous créez ou mise à jour de l’indexeur. L’exemple ci-après affiche une demande de mettre à jour l’indexeur :

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2015-02-28 
    Content-Type: application/json
    api-key: admin-key 

    { 
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Le paramètre **intervalle** est obligatoire. L’intervalle fait référence à la durée entre le début de deux exécutions indexeur consécutives. Le plus petit intervalle autorisé est de 5 minutes ; la plus longue est un jour. Il doit être mis en forme comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `P(nD)(T(nH)(nM))`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures.

L' option **heure de début** indique lorsque les exécutions planifiées doivent commencer ; Si elle est omis, l’heure UTC actuelle est utilisée. Cette durée peut être passée – dans lequel la première exécution cas sera planifiée comme si l’indexeur est exécuté en permanence depuis l’heure de début.  

Seul l’exécution d’un indexeur donné peut être exécutée à la fois. Si un indexeur est déjà en cours d’exécution lorsque l’option suivante est censée pour démarrer, l’exécution est ignorée et CV à l’intervalle suivant, en supposant qu’aucune autre tâche indexeur est en cours d’exécution.

Prenons un exemple en faire plus concret. Supposons que nous la planification horaire suivante configurée : 

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Voici ce qui se produit : 

1. La première exécution indexeur démarre à ou autour du 1er mars 2015 12:00 AM UTC.
1. Supposons que cette exécution prend 20 minutes (ou à tout moment inférieur à 1 heure).
1. La deuxième exécution démarre à ou autour du 1er mars 2015 01:00. 
1. Supposons à présent que cette exécution prend plus d’une heure (cela nécessite une grande quantité de documents pour que cela se produise réellement, mais il est une illustration utile) – par exemple, 70 minutes – afin qu’elle se termine environ 2 h 10.
1. Il est à présent 2:00 AM, heure de la troisième exécution Démarrer. Toutefois, étant donné que la seconde exécution de 1 h est toujours en cours d’exécution, l’exécution du troisième est ignorée. La troisième exécution commence à 3 h

Vous pouvez ajouter, modifier ou supprimer une planification pour un indexeur existant à l’aide d’une demande de **placer indexeur** . 

## <a name="capturing-new-changed-and-deleted-rows"></a>Capture nouveau, modifier et à supprimer des lignes

Si vous utilisez un échéancier et votre table contient un nombre important de lignes, vous devez utiliser une stratégie de détection de modification de données, afin que l’indexeur puissiez récupérer uniquement les lignes nouvelles ou modifiées sans avoir à réindexer l’intégralité du tableau.

### <a name="sql-integrated-change-tracking-policy"></a>SQL intégrés de stratégie de suivi des modifications

Si votre base de données SQL prend en charge [le suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons d’utiliser **SQL les règles de suivi intégré modifier**. Cette stratégie permet le suivi des modifications plus efficace, et vous permet également d’Azure la recherche pour identifier les lignes supprimées sans avoir à ajouter une colonne explicite « suppression temporaire » à votre tableau.

Suivi des modifications intégrée sont pris en charge avec les versions de base de données SQL Server suivantes :
 
- SQL Server 2008 R2 et versions ultérieures, si vous utilisez SQL Server sur machines virtuelles Azure. 
- Azure SQL de base de données V12, si vous utilisez une base de données SQL Azure.

À l’aide de la stratégie, suivi des modifications SQL intégré ne spécifiez pas une stratégie de détection de suppression de données distinct lorsque : cette stratégie est prise en charge intégrée pour identifier les lignes sont supprimées.

Cette stratégie peut uniquement être utilisée avec des tableaux ; Il ne peut pas être utilisé avec des vues. Vous devez activer le suivi des modifications pour la table que vous utilisez avant de pouvoir utiliser cette stratégie. Pour plus d’informations, voir [Activer et désactiver le suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) . 

Pour utiliser cette stratégie, créer ou mettre à jour votre source de données comme suit :
 
    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
      }
    }

### <a name="high-water-mark-change-detection-policy"></a>Stratégie de détection de changement de seuil supérieur

Bien que la stratégie de suivi des modifications intégrée SQL est recommandée, il se peut que vous ne pourrez pas utiliser vos données dans une vue, ou si vous utilisez une version antérieure de base de données SQL Azure. Dans ce cas, envisagez d’utiliser la stratégie seuil supérieur. Cette stratégie peut être utilisée si votre table contient une colonne qui répond aux critères suivants :

- Toutes les insertions spécifier une valeur pour la colonne. 
- Toutes les mises à jour pour un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente avec chaque modification.
- Requêtes qui utilisent un `WHERE` clause semblable à `WHERE [High Water Mark Column] > [Current High Water Mark Value]` pouvant être exécutées efficacement.

Par exemple, une colonne indexée **rowversion** est un candidat idéal pour la colonne seuil supérieur. Pour utiliser cette stratégie, créer ou mettre à jour votre source de données comme suit : 

    { 
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" }, 
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
      }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Stratégie de détection de suppression de colonne Supprimer bordures

Lorsque des lignes sont supprimées de la table source, vous voudrez probablement supprimer les lignes d’ainsi que l’index de recherche. Si vous utilisez la stratégie de suivi intégré de SQL, il est prise en charge pour vous. Toutefois, le seuil supérieur du suivi des modifications stratégie ne vous aide avec des lignes supprimées. Que faire ? 

Si les lignes sont physiquement supprimées de la table, vous êtes absent chance : il n’existe aucun moyen de déduire la présence d’enregistrements qui n’existent plus.  Toutefois, vous pouvez utiliser la technique « supprimer flottant » pour marquer les lignes comme logiquement supprimé sans les supprimer de la table en ajoutant une colonne et le marquage des lignes comme supprimés à l’aide d’une valeur de marque dans cette colonne.

Lorsque vous utilisez la technique de suppression de bordures, vous pouvez spécifier les bordures Supprimer stratégie comme suit lorsque vous créez ou mise à jour de la source de données : 

    { 
        …, 
        "dataDeletionDetectionPolicy" : { 
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]", 
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]" 
        }
    }

Notez que le **softDeleteMarkerValue** doit être une chaîne – utiliser la représentation de chaîne de votre valeur réelle. Par exemple, si vous avez une colonne entière dans laquelle les lignes supprimées sont marqués avec la valeur 1, utilisez `"1"`; Si vous avez une colonne BIT dans lequel les lignes supprimées sont marqués avec la valeur vrai booléen, utilisez `"True"`. 

<a name="TypeMapping"></a>
## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données SQL et types de données Azure recherche

|Type de données SQL | Les types de champs autorisés index cible |Notes 
|------|-----|----|
|bit|Edm.Boolean, Edm.String| |
|int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String| |
| bigint | Edm.Int64, Edm.String | |
| Flottement réel, |Edm.Double, Edm.String | |
| smallmoney, numérique décimal argent | Edm.String| Recherche Azure ne reconnaît pas convertissant Edm.Double types décimaux, car cela perd la précision |
| car, nchar, varchar, nvarchar | Edm.String<br/>Collection(EDM.String)|Transformer une colonne de chaîne en Collection(Edm.String), vous devez utiliser un aperçu de l’API version 2015-02-28-aperçu. Consultez [cet article](search-api-indexers-2015-02-28-preview.md#CreateIndexer) pour plus d’informations| 
|smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String| |
|uniqueidentifer | Edm.String | |
|emplacement géographique | Edm.GeographyPoint | Seules les instances de geography de type POINT avec 4326 SRID (qui est la valeur par défaut) sont prises en charge | | 
|rowversion| N/A |Colonnes de la version de ligne ne peuvent pas être stockés dans l’index de recherche, mais ils peuvent être utilisés pour le suivi des modifications | |
| heure, timespan, binaire, varbinary, image, xml, géométrie, types de CLR | N/A |Non pris en charge |


## <a name="frequently-asked-questions"></a>Forum aux questions

**Q:** Puis-je utiliser indexeur SQL Azure avec les bases de données SQL s’exécutant sur machines virtuelles IaaS dans Azure ?

R : Oui. Toutefois, vous devez autoriser votre service de recherche pour vous connecter à votre base de données en effectuant les deux tâches suivantes. Voir l’article [configurer une connexion à partir d’un indexeur recherche Azure à SQL Server sur un ordinateur virtuel Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md) pour plus d’informations.

1. Vous devrez peut-être configurer votre base de données avec un certificat de confiance afin que le service de recherche peut ouvrir des connexions SSL à la base de données.
2. Configurer le pare-feu pour autoriser l’accès à l’adresse IP de votre service de recherche.

**Q:** Puis-je utiliser indexeur SQL Azure avec les bases de données SQL en cours d’exécution en local ? 

R : nous ne recommandons ou prennent en charge, comme cela vous permet d’ouvrir vos bases de données pour le trafic Internet amener. 

**Q:** Puis-je utiliser indexeur SQL Azure les bases de données différent de SQL Server dans IaaS sous Azure ? 

R : nous ne prend en charge ce scénario, car nous n’avons pas testé l’indexeur avec les bases de données différent de SQL Server.  

**Q:** Puis-je créer plusieurs indexeurs en cours d’exécution sur un planning ? 

R : Oui. Toutefois, seul indexeur peut s’exécuter sur un seul nœud en même temps. Si vous avez besoin de plusieurs indexeurs qui s’exécutent simultanément, envisagez l’évolution votre service de recherche à plusieurs unité de recherche. 

**Q:** Exécution d’un indexeur affecte-t-elle mon travail requête ? 

R : Oui. Indexeur s’exécute sur l’un des nœuds dans votre service de recherche et ressources de ce nœud sont partagées entre l’indexation et servant le trafic de la requête et d’autres demandes API. Si vous exécutez intensives charges de requête et d’indexation et que vous rencontrez un taux élevé de 503 erreurs ou croissant de temps de réponse, envisagez de mise à l’échelle votre service de recherche.
