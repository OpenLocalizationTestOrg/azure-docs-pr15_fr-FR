<properties 
pageTitle="Les opérations d’indexeur (l’API REST de Service de recherche Azure : 2015-02-28-Preview) | Aperçu de la recherche Azure API" 
description="Les opérations d’indexeur (l’API REST de Service de recherche Azure : 2015-02-28-Preview)" 
services="search" 
documentationCenter="" 
authors="chaosrealm" 
manager="pablocas"
editor="" />

<tags 
ms.service="search" 
ms.devlang="rest-api" 
ms.workload="search" 
ms.topic="article"  
ms.tgt_pltfrm="na" 
ms.date="09/07/2016" 
ms.author="eugenesh" />

#<a name="indexer-operations-azure-search-service-rest-api-2015-02-28-preview"></a>Les opérations d’indexeur (l’API REST de Service de recherche Azure : 2015-02-28-Preview)#

> [AZURE.NOTE] Cet article décrit les indexeurs dans l' [API REST 2015-02-28-aperçu](search-api-2015-02-28-preview.md). Cette version de l’API ajoute des versions d’aperçu des indexeur de stockage d’objets Blob Azure avec l’extraction des documents et indexeur de stockage de Table Azure, ainsi que d’autres améliorations. L’API prend également en charge sera-t-il disponibles indexeurs (disponibilité générale), y compris les indexeurs pour la base de données SQL Azure, SQL Server sur machines virtuelles Azure et Azure DocumentDB.

## <a name="overview"></a>Vue d’ensemble ##

Recherche Azure pouvez intégrer directement à certaines sources de données courantes, évite d’avoir à écrire du code pour indexer vos données. Pour configurer cette installation, vous pouvez appeler l’API de recherche Azure pour créer et gérer des **sources de données**et **indexeurs** . 

Un **indexeur** est une ressource qui se connecte des sources de données avec l’index de recherche cible. Un indexeur est utilisé comme suit : 

- Effectuer une copie des données pour remplir un index unique.
- Synchroniser un index avec les modifications dans la source de données sur un planning. La planification fait partie de la définition d’indexeur.
- L’appel à la demande pour mettre à jour un index selon vos besoins. 

Un **indexeur** est utile lorsque vous souhaitez que les mises à jour régulières à un index. Vous pouvez définir un calendrier en ligne dans le cadre d’une définition indexeur ou exécuter à la demande à l’aide de [Indexeur exécuter](#RunIndexer). 

Une **source de données** indique quelles données doivent être indexées, les informations d’identification pour accéder aux données et des stratégies pour activer la recherche Azure pour identifier efficacement les modifications dans les données (tel que modifié ou supprimé des lignes dans une table de base de données). Elle est définie comme ressources indépendantes afin qu’il peut être utilisé par plusieurs indexeurs.

Sources de données suivantes sont actuellement prises en charge :

- **Base de données SQL Azure** et **SQL Server sur machines virtuelles Azure**. Pour une vue d’ensemble ciblée, voir [cet article](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md). 
- **DocumentDB azure**. Pour une vue d’ensemble ciblée, voir [cet article](../documentdb/documentdb-search-indexer.md). 
- **Stockage d’objets Blob azure**, y compris les formats de document suivants : (y compris JSON) des fichiers PDF, Microsoft Office (DOCX/document, XSLX/XLS, PPTX/PPT, message), HTML, XML, ZIP et texte brut. Pour une vue d’ensemble ciblée, voir [cet article](search-howto-indexing-azure-blob-storage.md).
- **Stockage de Table azure**. Pour une vue d’ensemble ciblée, voir [cet article](search-howto-indexing-azure-tables.md).
     
Nous améliorons envisage d’ajouter à l’avenir prise en charge des sources de données supplémentaires. Pour nous aider à hiérarchiser ces décisions, indiquez vos commentaires sur le [forum de commentaires Azure recherche](http://feedback.azure.com/forums/263029-azure-search).

Voir [Limites de Service](search-limits-quotas-capacity.md) pour les limites maximales liées aux données et indexeur ressources source.

## <a name="typical-usage-flow"></a>Utilisation classique flux ##

Vous pouvez créer et gérer des indexeurs et sources de données via des requêtes HTTP simples (POST, GET, place, supprimer) par rapport à une donnée `data source` ou `indexer` ressource.

Configuration de l’indexation automatique est généralement un processus en quatre étapes :

1. Identifiez la source de données qui contient les données devant être indexées. N’oubliez pas que Azure recherche ne peut-être pas en charge tous les types de données présents dans votre source de données. Voir les [types de données pris en charge](https://msdn.microsoft.com/library/azure/dn798938.aspx) pour la liste.

2. Créer un index de recherche Azure dont le schéma est compatible avec votre source de données.
  
3. Créer une source de données Azure recherche comme décrit dans [Créer une Source de données](#CreateDataSource).
  
4. Créer un indexeur Azure recherche comme décrit plus [Indexeur créer](#CreateIndexer).

Vous devez planifier sur la création d’un indexeur pour chaque combinaison de source de données et d’index cible. Vous pouvez avoir plusieurs indexeurs écriture dans le même index, et vous pouvez réutiliser la même source de données pour plusieurs indexeurs. Toutefois, un indexeur peut uniquement utiliser une source de données à la fois et ne peut écrire un index unique. 

Après avoir créé un indexeur, vous pourrez le retrouver son état d’exécution à l’aide de l’opération [d’Obtenir l’état indexeur](#GetIndexerStatus) . Vous pouvez également exécuter un indexeur à tout moment (au lieu ou en exécutant régulièrement sur un planning) à l’aide de l’opération [Indexeur exécuter](#RunIndexer) .

<!-- MSDN has 2 art files plus a API topic link list -->


## <a name="create-data-source"></a>Créer la Source de données ##

Dans Rechercher Azure, une source de données est utilisée avec les indexeurs, qui fournit les informations de connexion pour l’actualisation des données ad hoc ou planifiée d’un index cible. Vous pouvez créer une nouvelle source de données dans un service de recherche Azure à l’aide d’une demande HTTP POST.
    
    POST https://[service name].search.windows.net/datasources?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Par ailleurs, vous pouvez utiliser place et spécifiez le nom de source de données sur l’URI. Si la source de données n’existe pas, il sera créé.

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]

**Remarque**: le nombre maximal de sources de données autorisées varie selon le niveau de prix. Le service gratuit permet jusqu'à 3 sources de données. Service standard permet de 50 sources de données. Pour plus d’informations, voir [Limites de Service](search-limits-quotas-capacity.md) .

**Demande**

HTTPS est requis pour toutes les demandes de service. La demande de **Créer une Source de données** peut être créée à l’aide de la méthode un billet ou le placer. Lorsque vous utilisez billet, vous devez fournir un nom de source de données dans le corps de la requête ainsi que la définition de source de données. À la place, le nom fait partie de l’URL. Si la source de données n’existe pas, il est créé. S’il existe déjà, il est mis à jour à la nouvelle définition. 

Le nom de source de données doit être en minuscule, commencer par une lettre ou un chiffre, comporter aucun barres obliques ou points et être dépasser 128 caractères. Après avoir démarré le nom de source de données avec une lettre ou un chiffre, le reste du nom peut inclure une lettre, le numéro et tirets, dans la mesure où les tirets ne sont pas consécutifs. Pour plus d’informations, voir [règles d’affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) .

La `api-version` est requis. La version actuelle est `2015-02-28`.

**En-têtes de requête**

La liste suivante décrit les en-têtes de demande obligatoires et facultatifs. 

- `Content-Type`: Obligatoire. Cette valeur`application/json`
- `api-key`: Obligatoire. La `api-key` est utilisé pour authentifier la demande à votre service de recherche. Il est une valeur de chaîne unique à votre service. La demande de **Créer une Source de données** doit comporter une `api-key` en-tête défini sur votre clé d’administration (plutôt que d’utiliser une clé de requête). 
 
Vous devez également le nom du service pour créer l’URL de la demande. Vous pouvez obtenir le nom du service et `api-key` à partir de votre tableau de bord de service dans le [Portail Azure](https://portal.azure.com/). Consultez [créer un service de recherche dans le portail](search-create-service-portal.md) pour la navigation de page à l’aide.

<a name="CreateDataSourceRequestSyntax"></a>
**Syntaxe de la demande de corps**

Le corps de la requête contient une définition de source de données, qui inclut des informations d’identification pour lire les données, ainsi que les données une optionnels modifier la détection et les stratégies de détection de suppression de données qui sont utilisées pour identifier efficacement modifié ou supprimé dans la source de données lorsqu’il est utilisé avec un indexeur régulièrement planifié des données de type de la source de données. 

La syntaxe de la charge utile de demande de structuration est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the data source",
        "description" : "Optional. Anything you want, or nothing at all",
        "type" : "Required. Must be one of 'azuresql', 'documentdb', 'azureblob', or 'azuretable'",
        "credentials" : { "connectionString" : "Required. Connection string for your data source" },
        "container" : { "name" : "Required. The name of the table, collection, or blob container you wish to index" },
        "dataChangeDetectionPolicy" : { Optional. See below for details }, 
        "dataDeletionDetectionPolicy" : { Optional. See below for details }
    }

Demande contient les propriétés suivantes : 

- `name`: Obligatoire. Le nom de la source de données. Un nom de source de données doit uniquement contenir toutes les lettres minuscules, des chiffres ou des tirets, ne peut pas commencer ou se terminent par les tirets et est limité à 128 caractères.
- `description`: Description facultative. 
- `type`: Obligatoire. Doit être un des types de source de données pris en charge :
    - `azuresql`-Base de données azure SQL ou SQL Server sur machines virtuelles Azure
    - `documentdb`-DocumentDB azure
    - `azureblob`-Stockage d’objets Blob azure
    - `azuretable`-Stockage de Table azure
- `credentials`:
    - Requis pour le `connectionString` propriété spécifie la chaîne de connexion pour la source de données. Le format de la chaîne de connexion varie selon le type de source de données : 
        - Pour SQL Azure, il s’agit de la chaîne de connexion SQL Server habituelle. Si vous utilisez le portail Azure pour extraire la chaîne de connexion, utilisez la `ADO.NET connection string` option.
        - Pour DocumentDB, la chaîne de connexion doit être au format suivant : `"AccountEndpoint=https://[your account name].documents.azure.com;AccountKey=[your account key];Database=[your database id]"`. Toutes les valeurs sont nécessaires. Vous pouvez les retrouver facilement dans le [portail Azure](https://portal.azure.com/).  
        - Pour les objets Blob Azure et stockage de tables, il s’agit de la chaîne de connexion de compte de stockage. Le format est décrit [ici](https://azure.microsoft.com/documentation/articles/storage-configure-connection-string/). Protocole de point de terminaison HTTPS est requis.  
- `container`, requis : Spécifie les données à indexer à l’aide de la `name` et `query` propriétés : 
    - `name`Obligatoire :
        - SQL Azure : Spécifie la table ou la vue. Vous pouvez utiliser des noms qualifiés par schéma, tel que `[dbo].[mytable]`.
        - DocumentDB : Spécifie la collection de sites. 
        - Stockage d’objets Blob Azure : Spécifie le conteneur de stockage.
        - Stockage de Table Azure : Spécifie le nom de la table. 
    - `query`, facultatif :
        - DocumentDB : permet de spécifier une requête qui fusionne une disposition de document JSON arbitraire dans un schéma à plat Azure recherche peut indexer.  
        - Stockage d’objets Blob Azure : vous permet de spécifier un dossier virtuel au sein du conteneur blob. Par exemple, pour le chemin d’accès blob `mycontainer/documents/blob.pdf`, `documents` peut être utilisé comme le dossier virtuel.
        - Stockage de Table Azure : vous permet de spécifier une requête qui filtre le jeu de lignes à importer.
        - SQL Azure : requête n’est pas pris en charge. Si vous avez besoin de cette fonctionnalité, veuillez voter pour [cette suggestion](https://feedback.azure.com/forums/263029-azure-search/suggestions/9893490-support-user-provided-query-in-sql-indexer)
- L’option `dataChangeDetectionPolicy` et `dataDeletionDetectionPolicy` propriétés sont décrites ci-dessous.

<a name="DataChangeDetectionPolicies"></a>
**Stratégies de détection de modification de données**

L’objectif d’une stratégie de détection de modification de données est d’identifier efficacement les éléments de données modifiées. Stratégies prises en charge peuvent varier en fonction du type de source de données. Sections suivantes décrivent chaque stratégie. 

***Stratégie de détection de changement de limite supérieure*** 

Utilisez cette stratégie lorsque votre source de données contient une colonne ou la propriété qui répond aux critères suivants :
 
- Toutes les insertions spécifier une valeur pour la colonne. 
- Toutes les mises à jour pour un élément modifient également la valeur de la colonne. 
- La valeur de cette colonne augmente avec chaque modification.
- Les requêtes qui utilisent une clause filtre semblable au suivant `WHERE [High Water Mark Column] > [Current High Water Mark Value]` pouvant être exécutées efficacement.

Par exemple, lors de l’utilisation de sources de données SQL Azure, un indexés `rowversion` colonne est le candidat idéal pour une utilisation avec la stratégie seuil supérieur. 

Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "[a row version or last_updated column name]" 
    } 

> [AZURE.NOTE] Lorsque vous utilisez DocumentDB des sources de données, vous devez utiliser le `_ts` propriété fournie par DocumentDB. 

> [AZURE.NOTE] Lors de l’utilisation de sources de données Azure Blob Azure recherche automatiquement utilise un filigrane haut Modifier stratégie de détection basée sur horodatage de dernière modification d’un blob ; vous n’avez pas besoin de spécifier une telle stratégie vous-même.   

***SQL intégrés de stratégie de détection de modification***

Si votre base de données SQL prend en charge [le suivi des modifications](https://msdn.microsoft.com/library/bb933875.aspx), nous recommandons d’utiliser SQL les règles de suivi intégré modifier. Cette stratégie permet le suivi des modifications plus efficace et permet de Azure la recherche pour identifier les lignes supprimées sans que vous ayez avoir une colonne explicite « suppression temporaire » dans votre schéma.

Suivi des modifications intégrée sont pris en charge avec les versions de base de données SQL Server suivantes : 
- SQL Server 2008 R2, si vous utilisez SQL Server sur machines virtuelles Azure.
- Azure SQL de base de données V12, si vous utilisez une base de données SQL Azure.  

À l’aide de la stratégie de suivi des modifications intégrée SQL, ne spécifiez pas une stratégie de détection de suppression de données distinct lorsque : cette stratégie est prise en charge intégrée pour identifier les lignes sont supprimées. 

Cette stratégie peut uniquement être utilisée avec des tableaux ; Il ne peut pas être utilisé avec des vues. Vous devez activer le suivi des modifications pour la table que vous utilisez avant de pouvoir utiliser cette stratégie. Pour plus d’informations, voir [Activer et désactiver le suivi des modifications](https://msdn.microsoft.com/library/bb964713.aspx) .    
 
Lorsque structuration la demande de **Créer une Source de données** , la stratégie de suivi des changements SQL intégrée peut être spécifié comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy" 
    }

<a name="DataDeletionDetectionPolicies"></a>
**Stratégies de détection de suppression de données**

L’objectif d’une stratégie de détection de suppression de données est d’identifier efficacement les éléments de données supprimées. Pour l’instant, la seule stratégie pris en charge est la `Soft Delete` stratégie, qui permet d’identifier les éléments supprimés en fonction de la valeur d’un `soft delete` colonne ou une propriété dans la source de données. Cette stratégie peut être spécifiée comme suit :

    { 
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the column that specifies whether a row was deleted", 
        "softDeleteMarkerValue" : "the value that identifies a row as deleted" 
    }

**Remarque :** Seules les colonnes avec chaîne, entier ou valeurs booléennes sont prises en charge. La valeur utilisée comme `softDeleteMarkerValue` doit être une chaîne, même si la colonne correspondante contienne des entiers ou des booléens. Par exemple, si la valeur qui apparaît dans votre source de données est 1, utilisez `"1"` en tant que la `softDeleteMarkerValue`.    

<a name="CreateDataSourceRequestExamples"></a>
**Exemples de corps de requête**

Si vous prévoyez d’utiliser la source de données avec un indexeur qui s’exécute sur une planification, cet exemple montre comment spécifier des stratégies de détection de modification et suppression : 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy", "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }

Si vous souhaitez uniquement destiné à la source de données unique copie des données, les stratégies peuvent être omis :

    { 
        "name" : "asqldatasource",
        "description" : "anything you want, or nothing at all",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" }
    } 

**Réponse**

Pour une demande réussie : 201 créé. 

<a name="UpdateDataSource"></a>
## <a name="update-data-source"></a>Mettre à jour de la Source de données ##

Vous pouvez mettre à jour une source de données existante à l’aide d’une demande HTTP placer. Vous indiquez le nom de la source de données à mettre à jour sur l’URI de requête :

    PUT https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

La `api-version` est requis. La version actuelle est `2015-02-28`. [Le contrôle de version Azure recherche](https://msdn.microsoft.com/library/azure/dn864560.aspx) contient les détails et plus d’informations sur les autres versions.

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Demande**

La syntaxe de la demande corps est identique à celle des [demandes de créer une Source de données](#CreateDataSourceRequestSyntax).

> [AZURE.NOTE] Certaines propriétés ne peut pas être mis à jour sur une source de données existante. Par exemple, vous ne pouvez pas modifier le type de source de données existante.  

> [AZURE.NOTE] Si vous ne voulez pas modifier la chaîne de connexion pour une source de données existante, vous pouvez spécifier le littéral `<unchanged>` pour la chaîne de connexion. C’est utile lorsque vous avez besoin pour mettre à jour des données source mais n’avez pas facilement accès à la chaîne de connexion car il s’agit des données de sécurité sensibles.

**Réponse**

Pour une demande réussie : 201 créé si une nouvelle source de données a été créée et 204 aucun contenu si une source de données a été mis à jour.

<a name="ListDataSource"></a>
## <a name="list-data-sources"></a>Liste des Sources de données ##

L’opération de la **Liste des Sources de données** renvoie une liste des sources de données dans votre service de recherche Azure. 

    GET https://[service name].search.windows.net/datasources?api-version=[api-version]
    api-key: [admin key]

La `api-version` est requis. La version actuelle est `2015-02-28`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Pour une demande réussie : 200 OK.

Voici un corps de réponse exemple :

    {
      "value" : [
        {
          "name": "datasource1",
          "type": "azuresql",
          ... other data source properties
        }]
    }

Notez que vous pouvez filtrer la réponse vers le bas jusqu'à uniquement les propriétés que qui vous intéresse. Par exemple, si vous voulez seulement une liste de noms de sources de données, utiliser la OData `$select` option de requête :

    GET /datasources?api-version=205-02-28&$select=name

Dans ce cas, la réponse de l’exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "datasource1" }, ... ]
    }

Il s’agit d’une technique utile pour enregistrer la bande passante si vous avez un grand nombre de sources de données dans votre service de recherche.

<a name="GetDataSource"></a>
## <a name="get-data-source"></a>Obtenir la Source de données ##

L’opération **Obtenir la Source de données** Obtient la définition de source de données à partir d’Azure recherche.

    GET https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

La `api-version` est requis. La version actuelle est `2015-02-28`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

La réponse est semblable aux exemples dans les [requêtes d’exemple de créer une Source de données](#CreateDataSourceRequestExamples): 

    { 
        "name" : "asqldatasource",
        "description" : "a description",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:....database.windows.net,1433;Database=...;User ID=...;Password=...;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "sometable" },
        "dataChangeDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName" : "RowVersion" }, 
        "dataDeletionDetectionPolicy" : { 
            "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted", 
            "softDeleteMarkerValue" : "true" }
    }

> [AZURE.NOTE] Ne définissez pas la `Accept` en-tête de demande pour `application/json;odata.metadata=none` quand l’appel de cette API en tant que cela provoque `@odata.type` attribut à exclure de la réponse et vous ne pouvez pas faire la distinction entre les modifications apportées aux données et stratégies de détection de suppression de données de différents types. 

<a name="DeleteDataSource"></a>
## <a name="delete-data-source"></a>Supprimer la Source de données ##

L’opération de **Supprimer une Source de données** supprime une source de données à partir de votre service de recherche Azure.

    DELETE https://[service name].search.windows.net/datasources/[datasource name]?api-version=[api-version]
    api-key: [admin key]

> [AZURE.NOTE] Si les indexeurs faire référence à la source de données que vous souhaitez supprimer, l’opération de suppression continue. Toutefois, ceux-ci passera dans un état d’erreur lors de leur prochaine exécution.  

La `api-version` est requis. La version actuelle est `2015-02-28`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 aucun contenu est renvoyé pour une réponse réussie.

<a name="CreateIndexer"></a>
## <a name="create-indexer"></a>Créer indexeur ##

Vous pouvez créer un nouvel indexeur dans un service de recherche Azure à l’aide d’une demande HTTP POST.
    
    POST https://[service name].search.windows.net/indexers?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

Par ailleurs, vous pouvez utiliser place et spécifiez le nom de source de données sur l’URI. Si la source de données n’existe pas, il sera créé.

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]

> [AZURE.NOTE] Le nombre maximal d’indexeurs autorisé varie en fonction du niveau de prix. Le service gratuit permet jusqu'à 3 indexeurs. Service standard permet de 50 indexeurs. Pour plus d’informations, voir [Limites de Service](search-limits-quotas-capacity.md) .

La `api-version` est requis. La version actuelle est `2015-02-28`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.


<a name="CreateIndexerRequestSyntax"></a>
**Syntaxe de la demande de corps**

Le corps de la requête contient une définition indexeur, qui spécifie la source de données et l’index cible pour l’indexation, ainsi que facultative planification d’indexation et les paramètres. 


La syntaxe de la charge utile de demande de structuration est comme suit. Une demande d’exemple est fournie plus loin dans cette rubrique.

    { 
        "name" : "Required for POST, optional for PUT. The name of the indexer",
        "description" : "Optional. Anything you want, or null",
        "dataSourceName" : "Required. The name of an existing data source",
        "targetIndexName" : "Required. The name of an existing index",
        "schedule" : { Optional. See Indexing Schedule below. },
        "parameters" : { Optional. See Indexing Parameters below. },
        "fieldMappings" : { Optional. See Field Mappings below. },
        "disabled" : Optional boolean value indicating whether the indexer is disabled. False by default.  
    }

**Planification indexeur**

Un indexeur peut vous pouvez également spécifier une planification. Si une planification est présente, l’indexeur s’exécutera régulièrement selon une planification. Planification comporte les attributs suivants :

- `interval`: Obligatoire. Valeur de durée qui spécifie un intervalle ou une période d’indexation s’exécute. Le plus petit intervalle autorisé est de 5 minutes ; la plus longue est un jour. Il doit être mis en forme comme une valeur de « dayTimeDuration » XSD (un sous-ensemble restreint d’une valeur de [durée ISO8601](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Le modèle pour cela est : `"P[nD][T[nH][nM]]"`. Exemples : `PT15M` pour toutes les 15 minutes, `PT2H` pour toutes les 2 heures. 

- `startTime`: Obligatoire. Une date/heure au format UTC lorsque l’indexeur exécution doit commencer. 

**Paramètres d’indexeur**

Un indexeur permet d’indiquer plusieurs paramètres qui affectent son comportement. Tous les paramètres sont facultatives.  

- `maxFailedItems`: Le nombre d’éléments qui peut ne pas être indexés avant toute une série d’indexeur est considéré comme un échec. Par défaut est égal à 0. Informations sur les éléments échecs sont renvoyées par l’opération [d’Obtenir l’état indexeur](#GetIndexerStatus) . 

- `maxFailedItemsPerBatch`: Le nombre d’éléments qui peut ne pas être indexés dans chaque lot avant toute une série d’indexeur est considéré comme un échec. Par défaut est égal à 0.

- `base64EncodeKeys`: Spécifie qu’ou non clés document sera codé en base 64. Recherche Azure impose des restrictions sur les caractères qui peuvent être présents dans une clé de document. Toutefois, les valeurs de votre source de données peuvent contenir des caractères qui ne sont pas valides. S’il est nécessaire de ces valeurs en tant que clés de document d’index, cet indicateur peut être défini sur true. Valeur par défaut est `false`.

- `batchSize`: Spécifie le nombre d’éléments qui sont lire à partir de la source de données et indexés comme un seul lot afin d’améliorer les performances. La valeur par défaut dépend du type de source de données : il s’agit 1000 pour SQL Azure et DocumentDB et 10 pour le stockage d’objets Blob Azure.

**Mappages de champs**

Vous pouvez utiliser les mappages de champs pour mapper un nom de champ dans la source de données à un autre nom de champ dans l’index cible. Par exemple, considérez un tableau source avec un champ `_id`. Recherche Azure n’autorise pas un nom de champ en commençant par un trait de soulignement, afin que le champ doit être renommé. Cela peut être effectuée à l’aide de la `fieldMappings` propriété de l’indexeur comme suit : 
    
    "fieldMappings" : [ { "sourceFieldName" : "_id", "targetFieldName" : "id" } ] 

Vous pouvez spécifier plusieurs mappages de champs : 

    "fieldMappings" : [ 
        { "sourceFieldName" : "_id", "targetFieldName" : "id" },
        { "sourceFieldName" : "_timestamp", "targetFieldName" : "timestamp" },
     ]

Noms des champs source et cible respectent la casse.

<a name="FieldMappingFunctions"></a>
***Fonctions de mappage de champ***

Champ mappages peuvent également être utilisés pour transformer les valeurs des champs source à l’aide de *fonctions de mappage*.

Une seule cette fonction est pris en charge : `jsonArrayToStringCollection`. Il traite un champ qui contient une chaîne sous forme de tableau JSON dans un champ Collection(Edm.String) dans l’index cible. Il est destinée aux indexeur SQL Azure en particulier, dans la mesure où SQL n’a pas un type de données native collection de sites. Il peut être utilisé comme suit : 

    "fieldMappings" : [ { "sourceFieldName" : "tags", "mappingFunction" : { "name" : "jsonArrayToStringCollection" } } ] 

Par exemple, si le champ source contient la chaîne `["red", "white", "blue"]`, puis le champ cible de type `Collection(Edm.String)` est remplie avec les trois valeurs `"red"`, `"white"` et `"blue"`.

Notez que la `targetFieldName` propriété est facultative ; Si omis, la `sourceFieldName` valeur est utilisée. 

<a name="CreateIndexerRequestExamples"></a>
**Exemples de corps de requête**

L’exemple suivant crée un indexeur qui copie des données à partir de la table référencée par la `ordersds` de source de données à la `orders` index selon un calendrier qui commence le 1er janvier 2015 UTC et exécute toutes les heures. Chaque appel indexeur sera réussie si pas plus de 5 éléments échouent à indexer dans chaque lot, et pas plus de 10 éléments ne parviennent pas à être indexées au total. 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }

**Réponse**

201 créé pour une demande réussie.


<a name="UpdateIndexer"></a>
## <a name="update-indexer"></a>Mettre à jour indexeur ##

Vous pouvez mettre à jour un indexeur existant à l’aide d’une demande HTTP placer. Vous indiquez le nom de l’indexeur pour mettre à jour sur l’URI de requête :

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    Content-Type: application/json
    api-key: [admin key]

La `api-version` est requis. La version actuelle est `2015-02-28`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Demande**

La syntaxe de la demande corps est identique à celle [indexeur créer des requêtes](#CreateIndexerRequestSyntax).

**Réponse**

Pour une demande réussie : 201 créé si un nouvel indexeur a été créée et 204 aucun contenu si un indexeur existant a été mis à jour.


<a name="ListIndexers"></a>
## <a name="list-indexers"></a>Liste indexeurs ##

L’opération **Liste indexeurs** renvoie la liste des indexeurs dans votre service de recherche Azure. 

    GET https://[service name].search.windows.net/indexers?api-version=[api-version]
    api-key: [admin key]


La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. [Le contrôle de version Azure recherche](https://msdn.microsoft.com/library/azure/dn864560.aspx) contient les détails et plus d’informations sur les autres versions.

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Pour une demande réussie : 200 OK.

Voici un corps de réponse exemple :

    {
      "value" : [
      {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        ... other indexer properties
      }]
    }

Notez que vous pouvez filtrer la réponse vers le bas jusqu'à uniquement les propriétés que qui vous intéresse. Par exemple, si vous ne voulez qu’une liste de noms indexeur, utilisez la OData `$select` option de requête :

    GET /indexers?api-version=2014-10-20-Preview&$select=name

Dans ce cas, la réponse de l’exemple ci-dessus apparaît comme suit : 

    {
      "value" : [ { "name": "myindexer" } ]
    }

Il s’agit d’une technique utile pour enregistrer la bande passante si vous avez un grand nombre d’indexeurs dans votre service de recherche.


<a name="GetIndexer"></a>
## <a name="get-indexer"></a>Obtenir indexeur ##

L’opération **Obtenir indexeur** Obtient la définition indexeur de la recherche Azure.

    GET https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 200 OK est renvoyée pour une réponse réussie.

La réponse est semblable aux exemples dans les [requêtes d’exemple indexeur créer](#CreateIndexerRequestExamples): 

    {
        "name" : "myindexer",
        "description" : "a cool indexer",
        "dataSourceName" : "ordersds",
        "targetIndexName" : "orders",
        "schedule" : { "interval" : "PT1H", "startTime" : "2015-01-01T00:00:00Z" },
        "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 5, "base64EncodeKeys": false }
    }


<a name="DeleteIndexer"></a>
## <a name="delete-indexer"></a>Supprimer indexeur ##

L’opération **Indexeur supprimer** supprime un indexeur de votre service de recherche Azure.

    DELETE https://[service name].search.windows.net/indexers/[indexer name]?api-version=[api-version]
    api-key: [admin key]

Lorsqu’un indexeur est supprimé, les exécutions indexeur en cours à ce moment seront exécute jusqu'à son terme, mais aucun exécutions supplémentaires ne sont planifiées. Tente d’utiliser un indexeur inexistante entraînera le code d’état HTTP 404 introuvable. 
 
La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 aucun contenu est renvoyé pour une réponse réussie.

<a name="RunIndexer"></a>
## <a name="run-indexer"></a>Exécuter indexeur ##

Outre les exécute régulièrement sur une planification, un indexeur peut également être appelé à la demande via l’opération **Indexeur exécuter** : 

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=[api-version]
    api-key: [admin key]

La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 202 accepté est renvoyée pour une réponse réussie.

<a name="GetIndexerStatus"></a>
## <a name="get-indexer-status"></a>Obtenir le statut de l’indexation ##

L’opération **Get indexeur Status** récupère l’historique de statut et l’exécution en cours d’un indexeur : 

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=[api-version]
    api-key: [admin key]


La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 200 OK pour une réponse réussie.

Le corps de réponse contient des informations sur l’état indexeur général, le dernier appel indexeur, ainsi que l’historique des appels indexeur récentes (le cas échéant). 

Corps de réponse exemple ressemble à ceci : 

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

**Statut de l’indexation**

Statut de l’indexation peut être une des valeurs suivantes :

- `running`Indique que l’indexeur s’exécute normalement. Notez que certaines des exécutions indexeur peuvent toujours être défectueux, il est recommandé de vérifier la `lastResult` propriété également. 

- `error`Indique que l’indexeur a rencontré une erreur qui ne peut pas être corrigée sans intervention humaine. Par exemple, les informations d’identification de source de données a peut-être expiré ou le schéma de la source de données ou de l’index cible a changé dans un saut de manière. 

**Résultat de l’exécution indexeur**

Un résultat de l’exécution indexeur contient des informations sur l’exécution d’une seule indexeur. Le dernier résultat est exposé en tant que la `lastResult` propriété de l’état indexeur. Autres résultats récentes, le cas échéant, sont retournés sous la `executionHistory` propriété de l’état indexeur. 

Résultat de l’exécution indexeur contient les propriétés suivantes : 

- `status`: l’état de l’exécution. Pour plus d’informations, voir [Statut de l’exécution de l’indexation](#IndexerExecutionStatus) en dessous. 

- `errorMessage`: message d’erreur pour un échec de l’exécution. 

- `startTime`: l’heure au format UTC au démarrage de cette exécution.

- `endTime`: l’heure au format UTC lorsque cette exécution s’est terminée. Cette valeur n’est pas définie si l’exécution est toujours en cours.

- `errors`: un tableau d’erreurs au niveau des éléments, le cas échéant. Chaque entrée contient une clé de document (`key` propriété) et un message d’erreur (`errorMessage` propriété). 

- `itemsProcessed`: le nombre de données source éléments (par exemple, les lignes de tableau) que l’indexeur tenté d’indexer pendant cette exécution. 

- `itemsFailed`: le nombre d’éléments qui a échoué lors de cette exécution.  
 
- `initialTrackingState`: toujours `null` pour la première exécution indexeur, ou si les données changent suivi stratégie n’est pas activé sur la source de données utilisée. Si une telle stratégie est activée, cette valeur indique la première (minimum) suivi des modifications valeur traitée par cette exécution dans les exécutions suivantes. 

- `finalTrackingState`: toujours `null` si les données changent suivi stratégie n’est pas activé sur la source de données utilisée. Dans le cas contraire, indique la dernière modification (plus élevée) suivi de valeur correctement traitée par cette exécution. 

<a name="IndexerExecutionStatus"></a>
**Statut d’exécution de l’indexation**

L’état d’exécution indexeur capture l’état de l’exécution d’une seule indexeur. Il peut avoir les valeurs suivantes :

- `success`Indique que l’exécution indexeur est terminée avec succès.

- `inProgress`Indique que l’exécution indexeur est en cours. 

- `transientFailure`Indique qu’une erreur d’exécution indexeur a échoué. Voir `errorMessage` pour plus d’informations. L’échec peut nécessite ou non intervention humaine à résoudre : par exemple, résolution une incompatibilité de schéma entre la source de données et l’index cible requiert une action utilisateur, n’est pas le cas d’un temps d’arrêt de source de données temporaires. Appels indexeur continuera par prévisions, s’il en existe. 

- `persistentFailure`Indique que l’indexeur a échoué d’une manière qui nécessite l’intervention humaine. Arrêter d’exécutions indexeur planifiée. Après avoir le problème, utilisez réinitialiser indexeur API pour redémarrer les exécutions planifiées. 

- `reset`Indique que l’indexeur a été réinitialisé par un appel à réinitialiser indexeur API (voir ci-dessous). 

<a name="ResetIndexer"></a>
## <a name="reset-indexer"></a>Réinitialiser indexeur ##

L’opération **Réinitialiser indexeur** réinitialise l’état associé à l’indexeur du suivi des modifications. Cela vous permet de déclencher réindexation (par exemple, si votre schéma de source de données a changé) à partir de zéro, ou pour modifier la stratégie de détection de modification de données pour une source de données associée à l’indexeur.   

    POST https://[service name].search.windows.net/indexers/[indexer name]/reset?api-version=[api-version]
    api-key: [admin key]

La `api-version` est requis. La version d’évaluation est `2015-02-28-Preview`. 

La `api-key` doit être une clé d’administration (plutôt que d’utiliser une clé de requête). Reportez-vous à la section authentification [L’API REST de Service de recherche](https://msdn.microsoft.com/library/azure/dn798935.aspx) pour en savoir plus sur les clés. [Créer un service de recherche dans le portail](search-create-service-portal.md) explique comment obtenir l’URL du service et propriétés de clé utilisées dans la demande.

**Réponse**

Code d’état : 204 aucun contenu pour une réponse réussie.

## <a name="mapping-between-sql-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données de recherche Azure et Types de données SQL ##

<table style="font-size:12">
<tr>
<td>Type de données SQL</td>  
<td>Les types de champs autorisés index cible</td>
<td>Notes</td>
</tr>
<tr>
<td>bit</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>int, smallint, tinyint</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>bigint</td>
<td>Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Flottement réel,</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>smallmoney, argent<br>Decimal<br>numérique
</td>
<td>Edm.String</td>
<td>Recherche Azure ne reconnaît pas convertissant Edm.Double types décimaux, car cela perd la précision
</td>
</tr>
<tr>
<td>car, nchar, varchar, nvarchar</td>
<td>Edm.String<br/>Collection(EDM.String)</td>
<td>Consultez [Fonctions de mappage de champ](#FieldMappingFunctions) dans ce document pour connaître la procédure transformer une colonne de chaîne en un Collection(Edm.String)</td>
</tr>
<tr>
<td>smalldatetime, datetime, datetime2, date, datetimeoffset</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>uniqueidentifer</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>emplacement géographique</td>
<td>Edm.GeographyPoint</td>
<td>Seules les instances de geography de type POINT avec 4326 SRID (qui est la valeur par défaut) sont prises en charge</td>
</tr>
<tr>
<td>rowversion</td>
<td>N/A</td>
<td>Colonnes de la version de ligne ne peuvent pas être stockés dans l’index de recherche, mais ils peuvent être utilisés pour le suivi des modifications</td>
</tr>
<tr>
<td>heure, timespan<br>binaire, varbinary, image<br>XML, géométrie, types de CLR</td>
<td>N/A</td>
<td>Non pris en charge</td>
</tr>
</table>

## <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>Mappage entre les Types de données JSON et Types de données de recherche Azure ##

<table style="font-size:12">
<tr>
<td>Type de données JSON</td> 
<td>Les types de champs autorisés index cible</td>
<td>Notes</td>
</tr>
<tr>
<td>bool</td>
<td>Edm.Boolean, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Nombres entiers</td>
<td>Edm.Int32, Edm.Int64, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Nombres à virgule flottante</td>
<td>Edm.Double, Edm.String</td>
<td></td>
</tr>
<tr>
<td>chaîne</td>
<td>Edm.String</td>
<td></td>
</tr>
<tr>
<td>types de tableaux de base, par exemple [« a », « b », « c »]</td>
<td>Collection(EDM.String)</td>
<td></td>
</tr>
<tr>
<td>Chaînes qui ressemblent à des dates</td>
<td>Edm.DateTimeOffset, Edm.String</td>
<td></td>
</tr>
<tr>
<td>Objets de point de GeoJSON</td>
<td>Edm.GeographyPoint</td>
<td>Points GeoJSON sont des objets JSON au format suivant : {« type » : « Point », « coordonnées » : [long, locales]} </td>
</tr>
<tr>
<td>D’autres objets JSON</td>
<td>N/A</td>
<td>Non pris en charge ; Recherche Azure prend actuellement en charge uniquement des types primitifs et des collections de chaînes</td>
</tr>
</table>