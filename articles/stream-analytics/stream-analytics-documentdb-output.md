<properties
    pageTitle="Sortie JSON pour flux Analytique | Microsoft Azure"
    description="Découvrez comment flux Analytique pouvez cibler DocumentDB Azure pour sortie JSON, pour l’archivage des données et faible latence des requêtes sur des données JSON non structurées."
    keywords="Sortie JSON"
    documentationCenter=""
    services="stream-analytics,documentdb"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="stream-analytics"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-services"
    ms.date="09/26/2016"
    ms.author="jeffstok"/>

# <a name="target-azure-documentdb-for-json-output-from-stream-analytics"></a>Cible Azure DocumentDB à la sortie JSON de flux Analytique

Flux Analytique pouvez cibler [DocumentDB Azure](https://azure.microsoft.com/services/documentdb/) pour JSON de sortie, l’activation de requêtes d’archivage et faible latence de données sur les données JSON non structurées. Découvrez comment appliquer au mieux cette intégration.

Pour ceux qui ne savent pas DocumentDB, consultez prise en main [DocumentDB rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/documentdb/) .

## <a name="basics-of-documentdb-as-an-output-target"></a>Notions de base de DocumentDB en tant que sortie cible
La sortie DocumentDB Azure dans flux Analytique qui permet d’écrire votre flux de traitement des résultats sous forme de sortie JSON dans votre celle ou DocumentDB. Flux Analytique ne crée pas collections dans votre base de données, à la place que vous ayez besoin créer les frais. Il s’agit afin que les coûts de facturation de collections de sites DocumentDB sont transparentes pour vous, et que vous pouvez optimiser les performances, la cohérence et la capacité de vos collections directement à l’aide de l' [API DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). Nous recommandons d’utiliser une base de données DocumentDB par flux de travail pour séparer logiquement vos collections de sites pour une tâche de diffusion en continu.

Certaines de ces options de collection de sites DocumentDB sont détaillées ci-dessous.

## <a name="tune-consistency-availability-and-latency"></a>Régler la cohérence, la disponibilité et latence

Conformément à vos besoins d’application, DocumentDB vous permet d’optimiser régler la base de données et les collections de sites et des compromis entre la cohérence, disponibilité et latence. Selon les niveaux de cohérence de lecture vos besoins scénario par rapport à lire et écrire latence, que vous pouvez choisir un niveau de cohérence sur votre compte de base de données. Par défaut, DocumentDB permet également l’indexation synchrone sur chaque opération CRUD à votre collection de sites. Il s’agit d’une autre option utile pour contrôler les performances de lecture/écriture dans DocumentDB. Pour plus d’informations sur ce sujet, consultez l’article [modifier votre niveau de cohérence de base de données et requête](../documentdb/documentdb-consistency-levels.md) .

## <a name="choose-a-performance-level"></a>Choisissez un niveau de performance

Collections DocumentDB peuvent être créées à 3 niveaux de performances différentes (S1, S2 ou S3), qui déterminent le débit disponible pour CRUDs à cette collection. En outre, performances affectée par les niveaux d’indexation/cohérence dans votre collection de sites. Consultez [cet article](../documentdb/documentdb-performance-levels.md) pour comprendre ces performances en détail.

## <a name="upserts-from-stream-analytics"></a>Upserts à partir de flux Analytique

Flux Analytique intégration avec DocumentDB vous permet d’insérer ou mettre à jour des enregistrements dans votre collection de DocumentDB basée sur une colonne d’ID de Document donnée. Il est également appelé un *Upsert*.

Flux Analytique utilise une approche Upsert durées optimiste, où les mises à jour sont effectuées uniquement lors de l’insertion échoue en raison d’un conflit d’ID de Document. Cette mise à jour s’effectue par flux Analytique comme un correctif, afin qu’il active mises à jour partielles au document, par exemple, ajout de nouvelles propriétés ou remplacer qu'une propriété existante est effectuée par incréments. Notez que les modifications dans les valeurs de propriétés du tableau dans le résultat de document JSON dans le tableau entier prise remplacé, c'est-à-dire le tableau ne soit pas fusionné.

## <a name="data-partitioning-in-documentdb"></a>Partitions dans DocumentDB de données

DocumentDB collections vous permettent de partition vos données en fonction des modèles de requête et des besoins de performances de votre application. Chaque collection de sites peut contenir jusqu'à 10 Go de données (maximum) et il n’existe actuellement aucun moyen d’évoluer (ou de dépassement de capacité) une collection de sites. Pour faire évoluer, Analytique de flux de données vous permet d’écrire sur plusieurs collections de sites avec un préfixe donné (voir les détails d’utilisation ci-dessous). Flux Analytique utilise la stratégie [Hachage Partition vider](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) cohérente en fonction de l’utilisateur de colonne PartitionKey à partition ses enregistrements de sortie. Le nombre de collections de sites avec le préfixe spécifié lors du démarrage de la tâche en continu est utilisé en tant que le nombre de partition sortie, dans lequel le travail écrit en parallèle (Collections DocumentDB = sortie Partitions). Pour une collection de S3 unique avec cette méthode d’indexation différée uniquement insère, sur 0,4 débit en écriture Mo/s peut s’attendre. À l’aide de plusieurs collections de sites peut vous permettre d’atteindre un débit plus élevé et augmentation de la capacité.

Si vous avez l’intention d’augmenter le nombre de partitions à l’avenir, vous devrez peut-être arrêter votre travail, repartitionnement les données à partir de vos collections existantes dans nouvelles collections et redémarrez la tâche de flux de données Analytique. Obtenir plus d’informations sur l’utilisation de PartitionResolver et repartitionnement ainsi que des exemples de code, sera être inclus dans un billet de suivi. L’article [partition dans DocumentDB](../articles/documentdb-partition-data.md#developing-a-partitioned-application) fournit également des détails sur ce problème.

## <a name="documentdb-settings-for-json-output"></a>Paramètres de DocumentDB pour la sortie JSON

Création DocumentDB en tant que résultat dans le flux de données Analytique génère une invite pour plus d’informations, comme indiqué ci-dessous. Cette section fournit une explication de la définition de propriétés.

![écran de sortie documentdb flux analytique](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output.png)  

-   **Alias de sortie** – un alias pour faire référence à ce résultat dans votre requête ASA  
-   **Nom du compte** -le nom ou le point de terminaison URI du compte DocumentDB.  
-   **Clé de compte** – la touche d’accès partagé pour le compte DocumentDB.  
-   **Base de données** – DocumentDB la base de données nom.  
-   **Modèle de nom de collection de sites** – le modèle de nom de collection de sites pour les collections à utiliser. Format du nom de la collection de sites peut être créé à l’aide du jeton facultatif {partition}, où partitions démarrer à partir de 0. Voici des entrées valides exemple :  
   1\) MyCollection – une collection nommée « MyCollection » doit exister.  
   2\) MyCollection {partition} – ces collections doivent exister – « MyCollection0 », « MyCollection1 », « MyCollection2 » et ainsi de suite.  
-   **Clé de partition** – le nom du champ dans les événements de sortie permet de spécifier la clé de division sortie provenant de plusieurs collections. Pour la sortie collection unique, n’importe quelle colonne sortie arbitraire peut être utilisé par exemple IDPartition.  
-   **ID de document** – facultatif. Le nom du champ dans les événements de sortie permet de spécifier la clé primaire basées sur des insertion ou mise à jour opérations.  
