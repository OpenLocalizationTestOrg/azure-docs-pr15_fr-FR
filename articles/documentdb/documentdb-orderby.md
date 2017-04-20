<properties 
    pageTitle="Tri des données DocumentDB à l’aide de Order By | Microsoft Azure" 
    description="Découvrez comment utiliser ORDER BY dans les requêtes DocumentDB dans LINQ et SQL et comment spécifier une stratégie d’indexation pour les requêtes ORDER BY." 
    services="documentdb" 
    authors="arramac" 
    manager="jhubbard" 
    editor="cgronlun" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="arramac"/>

# <a name="sorting-documentdb-data-using-order-by"></a>Tri des données DocumentDB à l’aide de Order By
Microsoft Azure DocumentDB prend en charge l’interrogation des documents à l’aide de SQL sur documents JSON. Résultats de la requête peuvent être classés à l’aide de la clause ORDER BY dans les instructions de requête SQL.

Lisez cet article, vous serez en mesure de répondre aux questions suivantes : 

- Comment interroger avec Order By ?
- Comment configurer une politique d’indexation pour Order By ?
- Ce qui se passe suivant ?

[Exemples](#samples) et [Forum aux questions](#faq) sont également fournis.

Pour une référence complète interrogation SQL, consultez le [didacticiel DocumentDB requête](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Comment faire pour interroger avec Order By
Comme dans SQL ANSI, vous pouvez maintenant inclure une clause Order By facultative dans les instructions SQL lors de l’interrogation DocumentDB. La clause peut contenir un argument facultatif croissant/décroissant pour spécifier l’ordre dans lequel les résultats doivent être récupérées. 

### <a name="ordering-using-sql"></a>Classement avec SQL
Par exemple, voici une requête pour récupérer les livres 10 premiers dans l’ordre décroissant de leur titre. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Utilisation de SQL avec filtrage le classement
Vous pouvez trier à l’aide de n’importe quelle propriété imbriquée au sein de documents comme Books.ShippingDetails.Weight, et vous pouvez spécifier des filtres supplémentaires dans la clause WHERE en combinaison avec Order By comme dans cet exemple :

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Classement à l’aide du fournisseur LINQ pour .NET
Utilisation du SDK .NET version 1.2.0 et version ultérieure, vous pouvez également utiliser la clause OrderBy() ou OrderByDescending() dans les requêtes LINQ comme dans cet exemple :

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

DocumentDB prend en charge le classement avec un seul numérique, la chaîne ou la propriété booléenne par la requête, avec des types de requête supplémentaires bientôt disponible. Voir [ce qui se passe suivant](#Whats_coming_next) pour plus d’informations.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurer une stratégie d’indexation pour Order By

Rappelez-vous que DocumentDB prend en charge les deux types d’index (hachage et plage), qui peuvent être définis pour les chemins d’accès/des propriétés spécifiques, types de données (chaînes/nombres) et à des valeurs de précision différente (précision maximale ou une valeur de précision fixe). Dans la mesure où DocumentDB utilise hachage l’indexation par défaut, vous devez créer une nouvelle collection avec une stratégie d’indexation personnalisée une plage de nombres, des chaînes ou les deux, pour pouvoir utiliser Order By. 

>[AZURE.NOTE] Chaîne plage index introduits sur 7 juillet 2015 avec l’API REST version 2015-06-03. Afin de créer des stratégies pour Order By contre les chaînes, vous devez utiliser SDK version 1.2.0 du Kit de développement .NET ou de la version 1.1.0 Python, Node.js ou Java SDK.
>
>Avant de l’API REST version 2015-06-03, la stratégie d’indexation de collection de sites par défaut était hachage pour les chaînes et nombres. Cela a été modifié au hachage pour les chaînes et plage pour composer des numéros. 

Pour plus d’informations, voir [règles d’indexation DocumentDB](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>L’indexation pour Order By par rapport à toutes les propriétés
Voici comment vous pouvez créer une collection de sites avec « Tout plage » l’indexation pour Order By par rapport à toutes les pièces numériques ou propriétés de chaîne qui apparaissent dans les documents JSON qu’il contient. Ici, nous ignorer le type d’index par défaut pour les valeurs de chaîne en plage et la précision maximale (-1).
                   
    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

>[AZURE.NOTE] Notez que Order By uniquement renvoie des résultats de types de données (chaîne et numéro) sont indexés avec un RangeIndex. Par exemple, si vous avez l’indexation de la stratégie qui n’a RangeIndex sur des nombres par défaut, une clause Order By par rapport à un chemin d’accès avec les valeurs de chaîne ne retournera aucun document.

### <a name="indexing-for-order-by-for-a-single-property"></a>L’indexation pour Order By d’une seule propriété
Voici comment vous pouvez créer une collection de sites avec l’indexation pour Order By contre simplement la propriété titre, qui est une chaîne. Il existe deux chemins d’accès, l’autre pour la propriété titre (« titre / ? ») avec l’indexation de la plage et l’autre pour toutes les autres propriétés avec le modèle d’indexation par défaut, qui est hachage pour les chaînes et plage pour composer des numéros.                    
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Exemples
Consultez ce [projet d’exemples Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) qui montre comment utiliser Order By, y compris l’indexation stratégies de création et la pagination à l’aide de Order By. Les exemples sont libres et nous vous invitons à envoyer des demandes d’extraction avec dons pouvant bénéficier des autres développeurs DocumentDB. Reportez-vous aux [instructions de Contribution](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) pour obtenir des instructions sur la façon de contribuer.  

## <a name="faq"></a>FAQ

**Quelle est la consommation unité demander (demandeur) prévue de requêtes Order By ?**

Dans la mesure où Order By utilise l’index DocumentDB pour les recherches, le nombre d’unités demande utilisés par les requêtes Order By sera semblable aux requêtes équivalents sans Order By. Comme toute autre opération sur DocumentDB, le nombre d’unités demande dépend les tailles/formes de documents, ainsi que la complexité de la requête. 


**Quelle est l’attendu l’indexation aérienne pour Order By ?**

Les frais de stockage d’indexation sera proportionnelle au nombre de propriétés. Dans le scénario Pire des cas, les coûts des index sera 100 % des données. Il n’existe aucune différence surcharge débit (unités demander) entre plage/Order By l’indexation et l’indexation de hachage par défaut.

**Comment interroger mes données existantes dans DocumentDB à l’aide de Order By ?**

Pour trier les résultats d’une requête à l’aide de Order By, vous devez modifier la stratégie d’indexation de la collection d’utiliser un type d’index plage par rapport à la propriété utilisée pour trier. Voir la rubrique [modification l’indexation de la stratégie](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quelles sont les limites en cours de Order By ?**

Order By peut être spécifiée uniquement sur une propriété, soit numérique ou chaîne lorsqu’elle est plage indexées avec la précision maximale (-1).

Vous ne pouvez pas effectuez les opérations suivantes :
 
- Order By avec les propriétés de chaîne interne comme id, _rid et _self (bientôt disponible).
- Order By avec des propriétés dérivées à partir du résultat d’une jointure internes au document (bientôt disponible).
- Trier par plusieurs propriétés (bientôt disponible).
- Order By avec des requêtes sur les bases de données, collections de sites, les utilisateurs, autorisations ou les pièces jointes (bientôt disponible).
- Trier par, avec les propriétés calculées, par exemple, le résultat d’une expression ou une fonction UDF/intégré-in.

Order By n'est pas actuellement pris en charge pour les requêtes entre partitions lors de l’utilisation de l’Explorateur de requête dans le portail Azure.

## <a name="troubleshooting"></a>Résolution des problèmes

Si vous recevez un message d’erreur que Order By n'est pas pris en charge, vérifiez que vous utilisez une version du [Kit de développement logiciel](documentdb-sdk-dotnet.md) qui prend en charge Order By. 

## <a name="next-steps"></a>Étapes suivantes

Branche dans le [projet d’exemples Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) et démarrer le classement vos données ! 

## <a name="references"></a>Références
* [Référence de requête DocumentDB](documentdb-sql-query.md)
* [Référence de la stratégie DocumentDB l’indexation](documentdb-indexing-policies.md)
* [Guide de référence DocumentDB SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Commande DocumentDB par exemples](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)
 

