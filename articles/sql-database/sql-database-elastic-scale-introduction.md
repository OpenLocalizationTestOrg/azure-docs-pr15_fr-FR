<properties
    pageTitle="L’évolution horizontale avec la base de données SQL Azure | Microsoft Azure"
    description="Logiciel en tant qu’un développeurs de Service (SaaS) pouvez facilement créer élastiques, scalable bases de données dans le cloud à l’aide de ces outils"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="ddove"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="ddove"/>

# <a name="scaling-out-with-azure-sql-database"></a>Mise à l’échelle avec la base de données SQL Azure

Vous pouvez facilement évoluer bases de données SQL Azure avec les outils de **Base de données élastique** . Ces fonctionnalités et outils permettent d’utiliser les ressources de base de données quasiment illimitée de **Base de données SQL Azure** pour créer des solutions pour les charges de travail transactions et en particulier les logiciels en tant qu’une applications de Service (SaaS). Fonctionnalités de base de données élastiques sont composent des opérations suivantes :

* [Bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md): la bibliothèque cliente est une fonctionnalité qui vous permet de créer et gérer des bases de données sharded.  Voir [prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).
* [Outil de fusion et fractionnement de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md): déplace des données entre les bases de données sharded. Ceci est utile pour déplacer des données d’une base de données client multiples à une base de données unique client (ou inversement). Voir [base de données élastique didacticiel outil de fusion et fractionner](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Tâches de base de données élastique](sql-database-elastic-jobs-overview.md) (preview) : utiliser des tâches pour gérer un grand nombre de bases de données SQL Azure. Facilement effectuent des opérations d’administration tels que les modifications de schéma, gestion des informations d’identification, mises à jour des données de référence, collecte de données de performance ou collection de télémétrie client (client) à l’aide de tâches.
* [Requête de base de données élastique](sql-database-elastic-query-overview.md) (preview) : vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données. Cela permet de connexion à des outils de création de rapports comme Excel, PowerBI, Tableau, etc..
* [Transactions élastiques](sql-database-elastic-transactions-overview.md): cette fonctionnalité permet d’exécuter des transactions qui s’étalent sur plusieurs bases de données dans la base de données SQL Azure. Transactions de base de données élastique sont disponibles pour les applications .NET à l’aide d’ADO.NET et intégrant l’expérience de programmation familier en utilisant les [classes System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

L’illustration ci-dessous montre une architecture qui inclut les **fonctionnalités de base de données élastique** par rapport à une collection de bases de données.

Dans ce graphique, les couleurs de la base de données représentent des schémas. Bases de données avec la même couleur partagent le même schéma.

1. Un ensemble de **bases de données SQL Azure** sont hébergés sur Azure à l’aide d’architecture ont.
2. La **bibliothèque de client de base de données élastique** est utilisée pour gérer un ensemble partagé.
3. Un sous-ensemble des bases de données sont placées dans une **base de données élastique pool**. (Voir [qu’est un pool ?](sql-database-elastic-pool.md)).
4. Une **tâche de base de données élastique** s’exécute planifiée ou ad hoc T-SQL scripts sur toutes les bases de données.
5. L' **outil de fusion de fractionnement** est utilisé pour déplacer des données à partir d’un seul partagé à l’autre.
6. La **requête de base de données élastique** vous permet d’écrire une requête qui s’étend sur toutes les bases de données dans l’ensemble partagé.
7. **Transactions élastiques** vous autorise à exécuter les transactions qui s’étalent sur plusieurs bases de données. 


![Outils de base de données élastiques][1]


## <a name="why-use-the-tools"></a>Pourquoi utiliser les outils ?

Obtention élasticité et échelle pour les applications en nuage a été simple pour les ordinateurs virtuels et stockage d’objets blob--simplement ajouter ou soustraire des unités ou augmentent power. Mais il est resté compliqué avec état traitement des données dans les bases de données relationnelles. Défis apparu dans ces scénarios :

* Croissance et réduction des capacités pour la partie de la base de données relationnelle de votre charge de travail.
* Gestion des points d’accès qui peuvent survenir affectant un sous-ensemble spécifique des données, par exemple une fin particulièrement occupé (e)-client (client).

En règle générale, scénarios comme ceux-ci ont été pris en charge par avoir à investir dans les serveurs de base de données plus grande échelle pour prendre en charge l’application. Toutefois, cette option est limitée dans le cloud où tout le traitement se passe-t-il sur matériel prédéfini. En revanche, distribuer des données et traitement sur plusieurs bases de données structurées identique (un motif horizontale appelé « ont ») fournit une alternative à traditionnels approches échelle à distance en termes de coût et élasticité.

## <a name="horizontal-and-vertical-scaling"></a>Mise à l’échelle horizontale et verticale

L’illustration suivante montre les dimensions horizontales et verticales de mise à l’échelle, qui sont les méthodes de base que les bases de données élastiques peuvent être mise à l’échelle.

![Horizontal ou Vertical évolution][2]

Mise à l’échelle horizontale fait référence à ajouter ou supprimer des bases de données afin d’adapter les performances globales ou capacité. Cette option est également appelée « mise à l’échelle ». Ont, dans lequel les données sont réparties au sein d’une collection de bases de données structurées identique, est une pratique courante pour implémenter l’échelle horizontale.  

Échelle verticale fait référence à augmenter ou diminuer le niveau de performance d’une base de données individuel, cela est également connu sous le nom « échelle vers le haut ».

La plupart des applications de base de données à l’échelle du cloud utilise une combinaison de ces deux stratégies. Par exemple, un logiciels comme une application de Service peuvent utiliser la mise à l’échelle horizontale pour la mise en service de nouveaux clients finaux et la même échelle verticale pour permettre de base de données de chaque client fin agrandir ou réduire les ressources selon les besoins de la charge de travail.

* Mise à l’échelle horizontale est géré à l’aide de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md).

* Échelle verticale est effectuée à l’aide des applets de commande PowerShell Azure pour modifier le niveau de service, ou en plaçant les bases de données dans un pool élastique.

## <a name="sharding"></a>Ont

*Ont* est une technique répartir de grandes quantités de données structurées identique sur un certain nombre de bases de données indépendantes. Il est particulièrement populaire auprès des développeurs cloud création logiciels comme un offres de Service (SAAS) pour les clients de fin ou entreprises. Ces utilisateurs finaux sont souvent appelées « clients ». Ont peut être nécessaire pour plusieurs raisons :  

* Le montant total des données est trop volumineux pour s’adapter aux contraintes d’une seule base de données
* Le débit des transactions de la charge globale dépasse les capacités d’une seule base de données
* Clients peuvent nécessiter physique isolement entre eux, afin que les bases de données distinctes sont nécessaires pour chaque client
* Vous devrez résider dans différentes zones géographiques pour des raisons géopolitiques, de performances ou de conformité différentes sections d’une base de données.

Dans d’autres scénarios, tels que la réception de données à partir d’appareils distribués, ont peut servir à remplir un jeu de bases de données qui sont organisées temporairement. Par exemple, une base de données distinct peut être dédié à chaque jour ou semaine. Dans ce cas, la touche ont peut être un nombre entier représentant la date (présente dans toutes les lignes des tables sharded) et requêtes récupération des informations pour une plage de dates doivent être routés par l’application au sous-ensemble de bases de données portant sur la plage en question.

Ont fonctionne mieux lorsque toutes les transactions dans une application peuvent être limitée à une valeur unique d’une clé ont. Qui garantit que toutes les transactions seront locales dans une base de données.

## <a name="multi-tenant-and-single-tenant"></a>Client multiples et unique client

Certaines applications utilisent l’approche la plus simple de création d’une base de données distincte pour chaque client. Il s’agit de **motif d’ont client unique** qui fournit isolement, possibilité de sauvegarde et de restauration et ressources mise à l’échelle avec la précision du client. Avec ont client unique, chaque base de données est associé à une valeur de l’ID de client spécifique (ou valeur clé client), mais cette clé ne sont pas toujours nécessairement présente dans les données lui-même. Il est responsabilité de l’application acheminer chaque demande vers la base de données approprié, et la bibliothèque cliente peut simplifier ceci.

![Seul client par rapport à plusieurs utilisateurs][4]

D’autres scénarios compresser plusieurs clients ensemble dans les bases de données, plutôt que de les isoler dans les bases de données distinctes. Il s’agit d’un **motif de plusieurs utilisateurs ont** typique – et il peut être piloté par le fait qu’une application gère grand nombre de clients très petits. Dans plusieurs utilisateurs ont, les lignes dans les tables de base de données sont conçus pour effectuer une touche identifier l’ID de client ou ont rapide. Là encore, la couche application est responsable du routage de demande d’un client à la base de données appropriée et cela peut être pris en charge par la bibliothèque cliente élastique de base de données. En outre, la sécurité au niveau de la ligne peut servir à filtrer les lignes de chaque client peut accéder – pour plus d’informations, consultez [applications clients à plusieurs grâce aux outils de base de données élastique et sécurité au niveau de la ligne](sql-database-elastic-tools-multi-tenant-row-level-security.md). Redistribuer des données entre les bases de données peut-être être nécessaires sur le modèle de plusieurs utilisateurs ont et cela est facilité par l’outil de fusion et fractionnement élastique de base de données. Pour en savoir plus sur les modèles de conception pour les applications SaaS utilisant des pools élastiques, voir [Modèles de conception pour les Applications de SaaS client à plusieurs avec la base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Déplacer des données à partir de plusieurs bases de données single location

Lorsque vous créez une application SaaS, il est courant pour offrir aux clients potentiels une version d’évaluation du logiciel. Dans ce cas, il est plus rentable d’utiliser une base de données partagée pour les données. Cependant, lorsqu’un prospect devienne un client, une base de données unique client est préférable car il offre de meilleures performances. Si le client a créé les données pendant la période d’évaluation, utilisez l' [outil de fusion de fractionnement](sql-database-elastic-scale-overview-split-and-merge.md) pour déplacer les données à partir du client multiples vers la nouvelle base de données unique client.

## <a name="next-steps"></a>Étapes suivantes

Pour un exemple d’application qui montre la bibliothèque du client, voir [prise en main Datababase élastique outils](sql-database-elastic-scale-get-started.md).

Pour convertir des bases de données existantes pour utiliser les outils, voir [migrer des bases de données existantes à l’horizontale](sql-database-elastic-convert-to-use-elastic-tools.md).

Pour afficher les détails du pool élastique de base de données, consultez [Considérations relatives aux prix et des performances pour un pool élastique de base de données](sql-database-elastic-pool-guidance.md), ou créer un nouveau pool avec le [didacticiel](sql-database-elastic-pool-create-portal.md).  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png

