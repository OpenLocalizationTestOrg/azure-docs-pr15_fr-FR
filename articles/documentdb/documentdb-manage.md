<properties
    pageTitle="DocumentDB stockage et performances | Microsoft Azure" 
    description="Obtenir des informations sur le stockage de données et de stockage des documents dans DocumentDB et comment vous pouvez mettre à l’échelle DocumentDB pour répondre aux besoins de capacité de votre application." 
    keywords="stockage de documents"
    services="documentdb" 
    authors="syamkmsft" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/18/2016" 
    ms.author="syamk"/>

# <a name="learn-about-storage-and-predictable-performance-provisioning-in-documentdb"></a>En savoir plus sur le stockage et la mise en service des performances prévisibles dans DocumentDB
DocumentDB Azure est un format SVG, entièrement géré orientés document NoSQL de base de données service pour les documents JSON. Avec DocumentDB, vous n’êtes pas obligé louer machines virtuelles, déployer des logiciels ou contrôler les bases de données. DocumentDB est géré et surveiller en permanence par les ingénieurs de Microsoft pour offrir disponibilité du monde, performances et protection des données.  

Vous pouvez commencer avec DocumentDB en [créant un compte de base de données](documentdb-create-account.md) et une [base de données DocumentDB](documentdb-create-database.md) via le [portail Azure](https://portal.azure.com/). Bases de données DocumentDB sont proposés en intensité d’utilisation du lecteur SSD (SSD) sauvegardée stockage et de débit. Ces unités de stockage sont mis en service en [créant des collections de base de données](documentdb-create-collection.md) au sein de votre compte de base de données, chaque collection de sites avec débit réservé qui peut être mise à l’échelle vers le haut ou vers le bas à tout moment pour répondre aux besoins de votre application. 

Si votre application dépasse votre débit réservé d’une ou plusieurs collections, demandes sont limitées sur une base par collection de sites. Cela signifie que certaines demandes d’application peuvent réussir tandis que d’autres personnes peuvent être limitées.

Cet article fournit une vue d’ensemble des ressources et des mesures disponibles pour le plan de stockage des données et de gestion de la capacité. 

## <a name="database-account"></a>Compte de base de données
En tant qu’Azure abonné, vous pouvez configurer un ou plusieurs comptes de base de données DocumentDB pour gérer les ressources de votre base de données. Chaque abonnement est associé à un abonnement Azure unique. 

Comptes DocumentDB peuvent être créés via le [portail Azure](documentdb-create-account.md), ou en utilisant [un modèle de processeur ou Azure infrastructure du langage commun](documentdb-automation-resource-manager-cli.md).

## <a name="databases"></a>Bases de données
Une seule base de données DocumentDB peut contenir pratiquement une quantité illimitée de stockage des documents regroupé en collections de sites. Collections permettent d’isoler performances - chaque collection de sites peut être configurée avec débit n’est pas partagé avec d’autres collections de sites dans la même base de données ou d’un compte. Une base de données DocumentDB est élastique taille, allant du Go to SSD sauvegardée stockage des documents et débit mis en service. Contrairement à une base de données SGBDR traditionnel, une base de données dans DocumentDB n’est pas associé à un seul ordinateur et peut s’étalent sur plusieurs ordinateurs ou groupes.  

Avec DocumentDB, que nécessaire à l’échelle de vos applications, vous pouvez créer plusieurs collections de sites ou bases de données ou les deux. Bases de données pouvant être créées à partir du [portail Azure](documentdb-create-database.md) ou par le biais de la [SDK DocumentDB](documentdb-dotnet-samples.md).   

## <a name="database-collections"></a>Collections de base de données
Chaque base de données DocumentDB peut contenir une ou plusieurs collections. Collections agissent en tant que partitions de disponibilité des données de stockage des documents et de traitement. Chaque collection de sites peut stocker des documents avec des schémas hétérogènes. L’indexation automatique de DocumentDB et des fonctions de requête permettent de facilement filtrer et récupérer des documents. Une collection de sites fournit l’étendue des document l’exécution de stockage et de requête. Une collection de sites est également un domaine transaction pour tous les documents qu’il contient. Collections de sites sont affectées débit basé sur la valeur définie dans le portail Azure ou via le SDK. 

Collections de sites sont automatiquement répartis dans un ou plusieurs serveurs physiques en DocumentDB. Lorsque vous créez une collection de sites, vous pouvez spécifier le débit généré en termes d’unités demande par seconde et une propriété de clé partition. La valeur de cette propriété est utilisée par DocumentDB pour distribuer des documents entre les partitions et les demandes d’itinéraire comme les requêtes. La valeur de clé partition sert également la limite de transaction des procédures stockées et des déclencheurs. Chaque collection de sites possède un espace réservé débit spécifique à cette collection, qui n’est pas partagée avec d’autres collections de sites dans le même compte. Par conséquent, vous pouvez évoluer votre application en termes de stockage et de débit. 

Collections de sites peuvent être créées via le [portail Azure](documentdb-create-collection.md) ou par le biais de la [SDK DocumentDB](documentdb-sdk-dotnet.md).   
 
## <a name="request-units-and-database-operations"></a>Demander l’intensité d’utilisation et les opérations de base de données

Lorsque vous créez une collection de sites, vous réservez débit en termes d' [unités demande (demandeur)](documentdb-request-units.md) par seconde. À la place de demandant et la gestion des ressources matérielles, vous pouvez envisager d’une **unité de demande** comme une seule mesure pour les ressources requises pour effectuer diverses opérations de base de données et une demande d’application de service. Lecture d’un document 1 Ko utilise la même 1 demandeur quel que soit le nombre d’éléments stockés dans la collection de sites ou le nombre de demandes simultanées en cours d’exécution simultanément. Toutes les demandes de contre DocumentDB, y compris les opérations complexes telles que des requêtes SQL ont une valeur demandeur prévisible qui peut être déterminée au moment de développement. Si vous connaissez la taille de vos documents et la fréquence de chaque opération (lectures, écritures et requêtes) pour prendre en charge pour votre application, vous pouvez configurer le nombre exact de débit pour répondre aux besoins de votre application et adapter votre base de données vers le haut ou vers le bas selon les performances de vos besoins. 

Chaque collection de sites peut être réservés avec débit dans des blocs de 100 unités demande par seconde, à partir des centaines jusqu'à millions d’unités demande par seconde. Vous pouvez ajuster le débit généré pendant toute la durée d’une collection de sites pour s’adapter aux besoins de traitement modifier et accéder aux modèles de votre application. Pour plus d’informations, voir [DocumentDB des niveaux de performance](documentdb-performance-levels.md). 

>[AZURE.IMPORTANT] Les collections sont entités facturables. Le coût est déterminé par le débit généré de la collection mesurée en unités demande par seconde ainsi que le stockage total consommé en gigaoctets. 

Le nombre d’unités demande une opération particulière comme insertion, suppression, requête ou l’exécution de la procédure stockée utilisera ? Une unité de demande est une mesure normalisée de coût de traitement de requête. Lecture d’un document 1 Ko est 1 demandeur, mais une demande d’insérer, remplacer ou supprimer le même document utilisera plus de traitement à partir du service et par conséquent autres unités de demande. Chaque réponse à partir du service inclut un en-tête personnalisé (`x-ms-request-charge`) qui signale les unités demande consommées pour la demande. Cet en-tête est également accessible via le [SDK](documentdb-sdk-dotnet.md). Dans le Kit de développement .NET, [RequestCharge](https://msdn.microsoft.com/library/azure/dn933057.aspx#P:Microsoft.Azure.Documents.Client.ResourceResponse`1.RequestCharge) est une propriété de l’objet [ResourceResponse](https://msdn.microsoft.com/library/azure/dn799209.aspx) . Si vous voulez évaluer les besoins de votre débit avant d’effectuer un appel unique, vous pouvez utiliser le [Planificateur de capacité](documentdb-request-units.md#estimating-throughput-needs) pour vous aider à cette estimation. 

>[AZURE.NOTE] Le planning de référence correspondant à 1 unité de demande pour un document de base de connaissances 1 correspond à un simple GET du document avec [La cohérence de Session](documentdb-consistency-levels.md). 

Il existe plusieurs facteurs qui ont une incidence sur les unités de demande consommées pour une opération par rapport à un compte de base de données DocumentDB. Ces facteurs incluent :

- Taille du document. À mesure que les tailles de document augmentent les unités consommées pour lire ou écrire que les données augmente également.
- Nombre de propriétés. En supposant que par défaut de l’indexation de toutes les propriétés, les unités consommées pour écrire un document augmente en tant que l’augmentation du nombre de propriété.
- Cohérence des données. Lorsque vous utilisez des niveaux de cohérence des données de fortes ou obsolescence délimitée, unités supplémentaires seront consommées pour lire des documents.
- Propriétés indexées. Une stratégie d’index sur chaque collection détermine quelles sont les propriétés sont indexées par défaut. Vous pouvez réduire votre consommation d’unités demande en limitant le nombre de propriétés indexées. 
- L’indexation de documents. Par défaut que chaque document est automatiquement indexé, vous allez consommer moins d’unités demande si vous choisissez de ne pas indexer certains de vos documents.

Pour plus d’informations, consultez [DocumentDB demande unités](documentdb-request-units.md). 

Par exemple, Voici un tableau qui indique le nombre d’unités demande mise en service à trois tailles autre document (1 Ko, 4 Ko et 64 Ko) et à deux niveaux de performance différents (500 lectures/seconde + écritures/seconde 100 et 500 lectures/seconde + 500 écrit/seconde). La cohérence des données a été configurée au Session, et la stratégie d’indexation a été définie sur aucune.

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top"><p><strong>Taille du document</strong></p></td>
            <td valign="top"><p><strong>Lectures/seconde</strong></p></td>
            <td valign="top"><p><strong>Écritures/seconde</strong></p></td>
            <td valign="top"><p><strong>Unités de demande</strong></p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1) + (100* 5) = 1 000 demandeur/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>1 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *5) + (100* 5) = 3 000 demandeur/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *1.3) + (100* 7) = 1,350 demandeur/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>4 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *1.3) + (500* 7) = 4,150 demandeur/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>100</p></td>
            <td valign="top"><p>(500 *10) + (100* 48) = 9 800 demandeur/s</p></td>
        </tr>
        <tr>
            <td valign="top"><p>64 KO</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>500</p></td>
            <td valign="top"><p>(500 *10) + (500* 48) = 29,000 demandeur/s</p></td>
        </tr>
    </tbody>
</table>

Requêtes, des procédures stockées et des déclencheurs consomment unités demande en fonction de la complexité des opérations en cours d’exécution. Lorsque vous développez votre application, vérifiez que l’en-tête de frais de requête pour mieux comprendre comment chaque opération consomme demande unité capacité.  


## <a name="choice-of-consistency-level-and-throughput"></a>Choix du niveau de cohérence et débit
Le choix du niveau de cohérence par défaut a un impact sur le débit et la latence. Vous pouvez définir le niveau de cohérence par défaut par programme et via le portail d’Azure. Vous pouvez également remplacer le niveau de cohérence sur une base par demande. Par défaut, le niveau de cohérence est défini à la **Session**, qui fournit des opérations de lecture/écriture monotone et lire vos garanties écriture. Cohérence de session est idéal pour les applications centré sur l’utilisateur et fournit un équilibre parfait entre la cohérence et performance compromis.    

Pour obtenir des instructions sur la modification de votre niveau de cohérence dans le portail Azure, voir [comment gérer un compte DocumentDB](documentdb-manage-account.md#consistency). Ou, pour plus d’informations sur les niveaux de cohérence, voir [niveaux de cohérence à l’aide](documentdb-consistency-levels.md).

## <a name="provisioned-document-storage-and-index-overhead"></a>Frais de stockage et d’index de document généré
DocumentDB prend en charge la création de collections de partition unique et partitionnées. Chaque partition dans DocumentDB prend en charge pouvant atteindre 10 Go de stockage SSD sauvegardée. Le 10 Go de stockage de documents inclut l’espace de stockage pour l’index et les documents. Par défaut, une collection de sites DocumentDB est configuré pour indexer automatiquement tous les documents sans explicitement toute indices secondaires ou le schéma. Les coûts des index classique basées sur les applications à l’aide de DocumentDB, sont comprise entre 2-20 %. La technologie d’indexation utilisée par DocumentDB garantit qu’indépendamment des valeurs de propriétés, les coûts des index ne dépassent pas plus de 80 % de la taille des documents avec les paramètres par défaut. 

Par défaut tous les documents sont indexés par DocumentDB automatiquement. Toutefois, si vous souhaitez ajuster les coûts des index, vous pouvez choisir de supprimer certains documents de l’indexation au moment de l’insertion ou remplacement d’un document, comme décrit dans les [stratégies d’indexation DocumentDB](documentdb-indexing-policies.md). Vous pouvez configurer une collection DocumentDB pour exclure tous les documents dans la collection d’en cours d’indexation. Vous pouvez également configurer une collection DocumentDB pour indexer sélectivement uniquement certaines propriétés ou les chemins d’accès avec des caractères génériques de vos documents JSON, comme décrit dans la [configuration de la stratégie d’indexation d’une collection de sites](documentdb-indexing-policies.md#configuring-the-indexing-policy-of-a-collection). Excluant des propriétés ou des documents améliore également le débit écriture – ce qui signifie que vous utiliserez moins d’unités demande.   

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir sur le fonctionnement de DocumentDB, voir [partitionnement et la même échelle dans Azure DocumentDB](documentdb-partition-data.md).

Pour obtenir des instructions sur l’analyse des performances dans le portail Azure, voir [moniteur un compte DocumentDB](documentdb-monitor-accounts.md). Pour plus d’informations sur le choix des niveaux de performance pour les collections de sites, voir [niveaux de performances dans DocumentDB](documentdb-performance-levels.md).
 
