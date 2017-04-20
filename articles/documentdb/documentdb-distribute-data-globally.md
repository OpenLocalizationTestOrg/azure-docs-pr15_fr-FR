<properties
   pageTitle="Distribuer des données globalement avec DocumentDB | Microsoft Azure"
   description="En savoir plus sur échelle de la planète geo réplication, basculement et récupération de données à l’aide de bases de données globales de Azure DocumentDB, un service de base de données NoSQL entièrement géré."
   services="documentdb"
   documentationCenter=""
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="kipandya"/>
   
   
# <a name="distribute-data-globally-with-documentdb"></a>Distribuer des données globalement avec DocumentDB

> [AZURE.NOTE] Distribution globales des bases de données DocumentDB est généralement disponible et automatiquement activée pour tous les comptes DocumentDB nouvellement créés. Nous travaillons pour activer la distribution globale sur tous les comptes existants, mais en attendant, si vous souhaitez que la distribution globale activée pour votre compte, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) et nous allons activez-le pour vous maintenant.

Azure DocumentDB est conçu pour répondre aux besoins des applications IoT composée de millions de périphériques globalement distribués et les applications échelle internet expériences hautement injoignable aux utilisateurs du monde entier. Ces systèmes de base de données sont confrontées problème d’atteindre access faible latence aux données d’application à partir de plusieurs régions géographiques avec garanties la cohérence et la disponibilité des données bien définie. Comme un système de base de données distribuée globalement, DocumentDB simplifie la distribution globale des données en proposant base de données entièrement géré, région à plusieurs comptes fournissant effacer compromis entre la cohérence et les performances, toutes les garanties correspondantes. Comptes de base de données DocumentDB sont proposés avec une grande disponibilité, latence de ms à un seul chiffre, plusieurs [niveaux de cohérence bien définies] [consistency], basculement régionaux transparent avec API hébergement multiple et la possibilité de mise à l’échelle ELASTIQUEMENT débit et stockage dans le monde. 

  
## <a name="configuring-multi-region-accounts"></a>Configuration de la région à plusieurs comptes

Configuration de votre compte DocumentDB à l’échelle dans le monde peut être effectué en moins d’une minute via le [portail Azure](documentdb-portal-global-replication.md). Il vous souhaitez est de sélectionner le niveau de cohérence droite entre plusieurs niveaux de cohérence précis pris en charge et associer un nombre quelconque de régions Azure à votre compte de base de données. Niveaux de cohérence DocumentDB offrent effacer compromis entre garantie de cohérence spécifique et les performances. 

![DocumentDB propose plusieurs, bien définies choix à partir de modèles de cohérence (souple)][1]

DocumentDB propose plusieurs, bien définies choix à partir de modèles de cohérence (moyenne).

Sélection du niveau de cohérence droite dépend des données garantir la cohérence nécessaires à votre application. DocumentDB réplique vos données dans toutes les régions déterminées automatiquement et garantit la cohérence que vous avez sélectionnée pour votre compte de base de données. 


## <a name="using-multi-region-failover"></a>À l’aide de plusieurs région basculement 

Azure DocumentDB est en mesure de comptes de base de données de basculement en toute transparence dans plusieurs régions Azure – les nouvelles [API hébergement multiple] [ developingwithmultipleregions] garantie que votre application peut continuer à utiliser un point de terminaison logique et sans interruption par le basculement. Basculement est contrôlée par vous, fournir la flexibilité pour récupérer votre base de données du compte dans l’événement une partie de la plage de conditions d’erreur possibles se produire, y compris l’application, infrastructure, service ou défaillances régionaux (réels ou simulés). En cas de panne régionale DocumentDB, le service de manière transparente bascule vers votre compte de base de données et votre application continue à accéder aux données sans perdre sa disponibilité. Tandis que le DocumentDB offre [disponibilité de 99,99 % SLA][sla], vous pouvez tester fin de votre application à la fin des propriétés de disponibilité en simuler une défaillance régionale à la fois, [par programme] [ arm] , ainsi que via le portail d’Azure.


## <a name="scaling-across-the-planet"></a>Mise à l’échelle au sein de la planète
DocumentDB vous permet d’indépendamment mise en service de débit et l’utilisation de stockage pour chaque collection DocumentDB à n’importe quelle échelle, globalement dans toutes les régions associées à votre compte de base de données. Une collection de sites DocumentDB est automatiquement distribué globalement et gérées dans l’ensemble des régions associées à votre compte de base de données. Collections au sein de votre compte de base de données peuvent être distribuées sur une des régions Azure dans lequel [service DocumentDB n’est disponible][serviceregions]. 

Le débit acheté et le stockage utilisé pour chaque collection DocumentDB est configuré automatiquement dans toutes les régions également. Cela permet à votre application à l’échelle en toute transparence dans l’ensemble de globe [d’un abonnement payant uniquement de débit et de stockage au sein de chaque heure][pricing]. Par exemple, si vous avez mis en service RUs de 2 millions d’une collection de DocumentDB, puis chacune des régions associées à votre compte de base de données obtient 2 millions RUs pour cette collection. Voici une illustration.

![Mise à l’échelle débit pour une collection de DocumentDB sur quatre zones][2]

DocumentDB garantit < 10 ms Lecture et < 15 ms écriture latence en P99. Les demandes de lecture couvrant jamais limite du centre de données pour vous assurer [que vous avez sélectionné des exigences de cohérence][consistency]. Les écritures sont toujours quorum validée localement avant qu’ils sont reconnus aux clients. Chaque compte de base de données est configuré avec une priorité écriture région. La zone désignée par priorité la plus élevée servira de la zone d’écriture en cours pour le compte. Tous les SDK achemine en toute transparence écritures de compte de base de données à la zone d’écriture en cours. 

Enfin, étant donné que DocumentDB est entièrement [indépendant du schéma] [ vldb] -vous n’avez pas à vous soucier des schémas gestion/mettez à jour ou index secondaires sur plusieurs centres de données. Vos [requêtes SQL] [ sqlqueries] continuer à travailler pendant vos modèles d’application et des données continuent d’évoluer. 


## <a name="enabling-global-distribution"></a>Activer la distribution globale 

Vous pouvez décider de rendre vos données localement ou globalement distribuées par l’association d’une ou plusieurs zones Azure avec un DocumentDB de base de données de compte. Vous pouvez ajouter ou supprimer des régions à votre compte de base de données à tout moment. Pour activer la distribution globale à l’aide du portail, voir [comment effectuer la réplication de base de données globale DocumentDB à l’aide du portail Azure](documentdb-portal-global-replication.md). Pour activer la distribution globale par programmation, voir [développement avec des comptes de DocumentDB plusieurs régions](documentdb-developing-with-multiple-regions.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les données de distribution globalement avec DocumentDB dans les articles suivants :

* [Mise en service de débit, de stockage pour une collection de sites] [throughputandstorage]
* [Hébergement multiple d’API via le reste. .NET, Java, Python et nœud SDK] [developingwithmultipleregions]
* [Niveaux de cohérence dans DocumentDB] [consistency]
* [Disponibilité SLA] [sla]
* [Gérer le compte de base de données] [manageaccount]

[1]: ./media/documentdb-distribute-data-globally/consistency-tradeoffs.png
[2]: ./media/documentdb-distribute-data-globally/collection-regions.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[pcolls]: documentdb-partition-data.md
[consistency]: documentdb-consistency-levels.md
[consistencytradeooffs]: ./documentdb-consistency-levels/#consistency-levels-and-tradeoffs
[developingwithmultipleregions]: documentdb-developing-with-multiple-regions.md
[createaccount]: documentdb-create-account.md
[manageaccount]: documentdb-manage-account.md
[manageaccount-consistency]: documentdb-manage-account.md#consistency
[throughputandstorage]: documentdb-manage.md
[arm]: documentdb-automation-resource-manager-cli.md
[regions]: https://azure.microsoft.com/regions/
[serviceregions]: https://azure.microsoft.com/en-us/regions/#services 
[pricing]: https://azure.microsoft.com/pricing/details/documentdb/
[sla]: https://azure.microsoft.com/support/legal/sla/documentdb/ 
[vldb]: http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf
[sqlqueries]: documentdb-sql-query.md

