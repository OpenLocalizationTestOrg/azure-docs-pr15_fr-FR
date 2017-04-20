<properties 
    pageTitle="Base de données DocumentDB Questions - Forum aux Questions | Microsoft Azure" 
    description="Obtenez des réponses aux questions fréquemment posées sur Azure DocumentDB un service de base de données NoSQL document pour JSON. Répondre aux questions de base de données sur capacité, niveaux de performances et de mise à l’échelle." 
    keywords="Questions de base de données, Forum aux questions, documentdb, azure, Microsoft azure"
    services="documentdb" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar" 
    documentationCenter=""/>

<tags 
    ms.service="documentdb" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016" 
    ms.author="mimig"/>


#<a name="frequently-asked-questions-about-documentdb"></a>Forum aux questions sur DocumentDB

## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Questions de base de données sur les principes de base de Microsoft Azure DocumentDB

### <a name="what-is-microsoft-azure-documentdb"></a>Nouveautés de Microsoft Azure DocumentDB ? 
Microsoft Azure DocumentDB est surprenante rapidement, à l’échelle de la planète NoSQL document de base de données-comme-a-service qui propose des requêtes enrichies sur exempt de schéma de données, permet de fournir des performances configurable et fiables et permet le développement rapide, l’aide d’une plateforme managée sauvegardée par la puissance et atteindre de Microsoft Azure. DocumentDB est la solution idéale pour le jeu mobile, web, et applications IoT lorsque sont prévisible débit, haute disponibilité, faible latence et un modèle de données sans schéma clé exigences. DocumentDB offre une flexibilité schéma et enrichi indexation via un modèle de données natif JSON et prend en charge les transactions multidocument avec JavaScript intégré.  
  
Pour plus d’informations de base de données, des réponses et des instructions sur le déploiement et l’utilisation de ce service, voir la [page de la documentation DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>Quel type de base de données est DocumentDB ?
DocumentDB est un document orienté base de données NoSQL qui stocke les données au format JSON.  DocumentDB prend en charge les structures imbriqués, données de contained automatique qui peuvent être interrogées via une DocumentDB enrichi [Grammaire requête SQL](documentdb-sql-query.md). DocumentDB fournit haute performance transactions traitement côté serveur JavaScript par le biais [de procédures stockées, déclencheurs et les fonctions définies par l’utilisateur](documentdb-programming.md). La base de données prennent également en charge niveaux de cohérence ACCORDABLE développeur avec associé [des niveaux de performance](documentdb-performance-levels.md).
 
### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>Bases de données DocumentDB dois-je tables comme une base de données relationnel (SGBDR) ?
Non, DocumentDB stocke les données dans des collections de documents JSON.  Pour plus d’informations sur les ressources DocumentDB, voir [concepts et le modèle de ressources DocumentDB](documentdb-resources.md). Pour plus d’informations sur comment solutions NoSQL comme DocumentDB diffèrent des solutions relationnelles, voir [NoSQL vs SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>Bases de données DocumentDB prennent en charge les données sans schéma ?
Oui, DocumentDB permet aux applications stocker des documents JSON arbitraires sans définition de schéma et des conseils. Données sont immédiatement disponibles pour la requête via l’interface de requête DocumentDB SQL.   

### <a name="does-documentdb-support-acid-transactions"></a>DocumentDB ne prend en charge les transactions ACID ?
Oui, DocumentDB prend en charge les transactions entre plusieurs documents exprimées sous forme de déclencheurs et des procédures stocké de JavaScript. Portée à une partition unique au sein de chaque collection et exécutées avec sémantique ACID que toutes les transactions ou rien isolé à partir d’autres utilisateurs et le code des demandes s’exécutant simultanément.  Si les exceptions sont levées à travers l’exécution côté serveur de code de l’application JavaScript, toute la transaction est annulée. Pour plus d’informations sur les transactions, voir [programme les transactions de base de données](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>Quels sont les scénarios d’utilisation classiques pour DocumentDB ?  
DocumentDB constitue un bon choix pour le nouveau jeu mobile, web, et applications IoT où automatique de l’échelle, des performances prévisibles, fast ordre des temps de réponse millisecondes et la possibilité d’interrogation sur exempt de schéma de données est important. DocumentDB prête à développement rapide et prenant en charge l’itération continue de modèles de données d’application. Les applications qui gèrent les données et des contenus générés par l’utilisateur sont des [scénarios d’utilisation courants pour DocumentDB](documentdb-use-cases.md).  

### <a name="how-does-documentdb-offer-predictable-performance"></a>Comment DocumentDB offrent des performances prévisibles ?
Une [unité de demande](documentdb-request-units.md) est la mesure de débit dans DocumentDB. 1 demandeur correspond au débit de l’obtention d’un document 1 Ko. Chaque opération dans DocumentDB, y compris les lectures, écritures, requêtes SQL et exécutions de procédures stockées contient une valeur demandeur déterministe en fonction du débit nécessaire pour terminer l’opération. Au lieu de réflexion sur processeur, IO et la mémoire et chaque leur incidence sur votre débit de l’application, vous pouvez considérer en termes d’une seule mesure demandeur.

Chaque collection de sites DocumentDB peut être réservée avec débit généré en termes de RUs de débit par seconde. Pour les applications de n’importe quelle échelle, vous pouvez évaluer des requêtes individuelles mesurer leur demandeur valeurs et mise en service des collections de sites pour gérer la somme des unités de demande pour toutes les requêtes. Vous pouvez également évoluer ou mettre à l’échelle vers le bas débit de votre collection selon les besoins de votre evolve application. Pour plus d’informations sur les unités de demande et d’assistance pour déterminer votre collection de sites doit, lisez [Gérer les performances et la capacité](documentdb-manage.md) et essayez la [Calculatrice de débit](https://www.documentdb.com/capacityplanner). 

### <a name="is-documentdb-hipaa-compliant"></a>DocumentDB HIPAA est conforme ?
Oui, DocumentDB est conformité HIPAA. HIPAA établit configuration requise pour l’utilisation, divulgation et protection des informations d’intégrité identifiable individuellement. Pour plus d’informations, voir le [Centre de gestion de la confidentialité de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>Quelles sont les limites de stockage de DocumentDB ? 
Il n’existe aucune limite théorique au montant total des données une collection de sites peut stocker dans DocumentDB. Si vous voulez stocker plus de 250 Go de données au sein d’une collection unique, veuillez [contacter le support technique](documentdb-increase-limits.md) pour disposer d’espace disponible dans votre compte est augmenté. 

### <a name="what-are-the-throughput-limits-of-documentdb"></a>Quelles sont les limites de débit de DocumentDB ? 
Il n’existe aucune limite théorique au montant total de débit une collection de sites peut prendre en charge dans DocumentDB, si votre charge de travail peut être distribué à peu près uniformément entre un nombre suffisant de clés. Si vous souhaitez excède demande 250 000 unités/seconde par collection de sites ou compte, veuillez [contacter le support technique](documentdb-increase-limits.md) pour disposer d’espace disponible dans votre compte est augmenté. 

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>Combien coûte Microsoft Azure DocumentDB ?
Reportez-vous à la page [DocumentDB pour plus d’informations](https://azure.microsoft.com/pricing/details/documentdb/) pour plus d’informations. Frais d’utilisation DocumentDB sont déterminées par le nombre de collections de sites en cours d’utilisation, le nombre d’heures les collections ont été en ligne, et le stockage consommée et les débit généré pour chaque collection de sites. 

### <a name="is-there-a-free-account-available"></a>Un compte gratuit est-il disponible ?
Si vous débutez dans Azure, vous pouvez vous inscrire pour un [compte gratuit Azure](https://azure.microsoft.com/free/), qui vous donne des 30 derniers jours et 200 $ pour essayer de tous les services Azure. Ou, si vous avez un abonnement Visual Studio, que vous êtes éligible pour [150 $ dans crédits Azure gratuits par mois](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) à utiliser sur n’importe quel service Azure.  

### <a name="how-can-i-get-additional-help-with-documentdb"></a>Comment puis-je obtenir une aide supplémentaire sur DocumentDB ?
Si vous avez besoin d’aide, veuillez contacter des nous sur [Débordement de pile](http://stackoverflow.com/questions/tagged/azure-documentdb), les [Forums de développeurs Azure DocumentDB MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB), ou planifier une [conversation de 1:1 avec l’équipe d’ingénierie DocumentDB](http://www.askdocdb.com/). Pour rester à jour sur les dernières actualités DocumentDB et fonctionnalités, contactez-nous sur [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configurer Microsoft Azure DocumentDB

### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>Comment puis-je d’abonnement pour Microsoft Azure DocumentDB ?
Microsoft Azure DocumentDB est disponible dans le [Portail Azure][azure-portal].  Tout d’abord vous devez vous inscrire pour un abonnement Microsoft Azure.  Une fois que vous vous inscrivez pour un abonnement Microsoft Azure, vous pouvez ajouter un compte DocumentDB à votre abonnement Azure. Pour obtenir des instructions sur l’ajout d’un compte DocumentDB, voir [créer un compte de base de données DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>Qu’est une clé principale ?
Une clé principale est un jeton de sécurité pour accéder à toutes les ressources pour un compte. Personnes avec la touche ont lire et écrire des accès aux toutes les ressources dans le compte de base de données. Soyez prudent lors de la distribution des clés principales. La clé maître primaire et clé principale secondaire ne sont disponibles dans la carte de **clés **du [Portail Azure][azure-portal]. Pour plus d’informations sur les clés, voir [Afficher, copier et les touches d’accès régénérer](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>Comment créer une base de données ?
Vous pouvez créer des bases de données à l’aide du [Portail Azure]() comme décrit dans [créer une base de données DocumentDB](documentdb-create-database.md), un de l' [DocumentDB SDK](documentdb-sdk-dotnet.md), ou via les [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>Qu’est une collection de sites ?
Une collection de sites est un conteneur de documents JSON et la logique d’application JavaScript associée. Une collection de sites est une entité facturable, où le [coût](documentdb-performance-levels.md) est déterminé par le débit et storaged utilisée. Collections de sites peuvent s’étendre sur un ou plusieurs partitions/serveurs et pouvant évoluer pour gérer des volumes quasi illimitées de stockage ou débit.

Collections de sites sont également les entités facturation pour DocumentDB. Chaque collection de sites est facturé toutes les heures en fonction du débit généré et l’espace de stockage utilisé. Pour plus d’informations, voir [DocumentDB tarifs](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>Comment définir des utilisateurs et des autorisations ?
Vous pouvez créer des utilisateurs et autorisations en utilisant l’un de l' [DocumentDB SDK](documentdb-sdk-dotnet.md) ou via l' [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Questions de base de données sur le développement par rapport à Microsoft Azure DocumentDB

### <a name="how-to-do-i-start-developing-against-documentdb"></a>Comment faire puis-je commencer le développement sur DocumentDB ?
[SDK](documentdb-sdk-dotnet.md) sont disponibles pour .NET, Python, Node.js, JavaScript et Java.  Les développeurs peuvent également exploiter les [API HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) pour interagir avec les ressources DocumentDB provenant de plusieurs plateformes et langues. 

Exemples pour le DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md)et [Python](documentdb-python-samples.md) SDK sont disponibles sur GitHub.

### <a name="does-documentdb-support-sql"></a>DocumentDB ne prend en charge SQL ?
Le langage de requête DocumentDB SQL est un sous-ensemble amélioré de la fonctionnalité de requête pris en charge par SQL. Le langage de requête DocumentDB SQL fournit enrichi hiérarchique et opérateurs relationnels et extensibilité via JavaScript, en fonction utilisateur défini par les fonctions. Grammaire JSON permet de modélisation des documents JSON comme arborescences avec les étiquettes en tant que les nœuds d’arbre, qui est utilisé par les DocumentDB des techniques d’indexation automatiques, ainsi que le langage de requête SQL de DocumentDB.  Pour plus d’informations sur l’utilisation de la grammaire SQL, reportez-vous à la [Requête DocumentDB] [ query] article.

### <a name="what-are-the-data-types-supported-by-documentdb"></a>Quels sont les types de données pris en charge par DocumentDB ?
Types de données primitifs pris en charge dans DocumentDB sont les mêmes que JSON. JSON dispose d’un système de type simple qui se compose de chaînes, des nombres (IEEE754 double précision) et booléens - vrais, faux et valeurs NULL.  Types de données plus complexes, tels que géométrie, Int64, DateTime et Guid peuvent être représentées dans JSON et DocumentDB grâce à la création d’objets imbriqués à l’aide de l’opérateur {} et les matrices à l’aide de l’opérateur []. 

### <a name="how-does-documentdb-provide-concurrency"></a>En quoi DocumentDB offre concurrence ?
DocumentDB prend en charge le contrôle de concurrence optimiste (OCC) via HTTP entité balises ou etags. Chaque ressource DocumentDB a un etag, et l’etag est défini sur le serveur chaque fois qu’un document est mis à jour. L’en-tête etag et la valeur actuelle sont inclus dans tous les messages de réponse. ETags peut être utilisé avec l’en-tête If-Match pour permettre au serveur décider si une ressource doit être mis à jour. La valeur If-Match est la valeur etag être comparés. Si elle correspond à la valeur etag du serveur, la ressource est mise à jour. Si l’etag n’est plus active, le serveur refuse l’opération avec un « HTTP 412 condition préalable échec « code de réponse. Le client devra puis rechercher à nouveau la ressource pour trouver la valeur etag actuel pour la ressource. En outre, etags peut être utilisé avec en-tête If-None-Match pour déterminer si une ré-récupérer d’une ressource est nécessaire. 

Pour utiliser simultané dans .NET, utilisez la classe [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Pour obtenir un exemple .NET, voir [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) dans l’échantillon DocumentManagement sur github.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>Comment effectuer des transactions dans DocumentDB ?
DocumentDB prend en charge les transactions intégrées au langage via procédures JavaScript stocké et des déclencheurs. Toutes les opérations de base de données à l’intérieur des scripts sont exécutées en isolement de capture instantanée portée à la collection s’il est un ensemble de partition unique ou des documents avec la même valeur clé partition au sein d’une collection de sites, si la collection de sites est segmenté. Un instantané des versions de document (ETags) est considérée au début de la transaction et validé uniquement si le script a réussi. Si le code JavaScript génère une erreur, celle-ci est annulée. Pour plus d’informations, voir [programmation DocumentDB côté serveur](documentdb-programming.md) .

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>Comment puis-je bloc des documents insertion dans DocumentDB ? 
Il existe trois manières en bloc des documents insertion dans DocumentDB :

- L’outil de migration de données, comme décrit dans [Importer des données à DocumentDB](documentdb-import-data.md).
- Explorateur de document dans le portail Azure, comme décrit dans [les documents avec l’Explorateur de Document Ajout en bloc](documentdb-view-json-document-explorer.md#BulkAdd).
- Les procédures stockées, comme décrit dans [DocumentDB côté serveur de programmation](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>DocumentDB ne prend en charge le lien de la ressource mise en cache ?
Oui, car DocumentDB est un service RESTful, liens vers les ressources sont immuables et peuvent être mis en cache. DocumentDB clients peuvent spécifier un en-tête « If-None-Match » pour les lectures sur n’importe quelle ressource comme document ou de collection de sites et de mise à jour uniquement lorsque la version du serveur a modifiez des copies de leurs locales. 

### <a name="is-a-local-instance-of-documentdb-available"></a>Une instance locale de DocumentDB est-elle disponible ?
Une instance locale de DocumentDB n’est pas disponible pour le moment. Vous pouvez suivre l’état d’un émulateur local et de vote pour qu’elle sur le [forum de commentaires](https://feedback.azure.com/forums/263030-documentdb/suggestions/6328798-standalone-local-instance).


[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 
