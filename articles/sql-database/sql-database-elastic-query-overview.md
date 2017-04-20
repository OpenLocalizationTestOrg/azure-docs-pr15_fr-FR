<properties
    pageTitle="Vue d’ensemble de Azure SQL de base de données de base de données élastique requête | Microsoft Azure"
    description="Vue d’ensemble de la fonctionnalité de requête élastique"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/27/2016"
    ms.author="torsteng" />

# <a name="azure-sql-database-elastic-database-query-overview-preview"></a>Azure SQL de base de données de base de données élastique requête vue d’ensemble (preview)

La fonctionnalité de requête de base de données élastique (en mode Aperçu avant) vous permet d’exécuter une requête Transact-SQL qui s’étend sur plusieurs bases de données dans Azure SQL de base de données (SQLDB). Il vous permet d’exécuter des requêtes de bases de données croisées pour accéder aux tables à distance et connecter des outils Microsoft et tiers (Excel, PowerBI, Tableau, etc.) pour interroger entre les niveaux de données avec plusieurs bases de données. À l’aide de cette fonctionnalité, vous pouvez mettre à l’échelle des requêtes aux niveaux de grande quantité de données dans la base de données SQL et visualiser les résultats dans les rapports de décision d’entreprise.

## <a name="documentation"></a>Documentation

* [Prise en main des requêtes de bases de données croisées](sql-database-elastic-query-getting-started-vertical.md)
* [Dans d’autres bases de données plus grande échelle cloud](sql-database-elastic-query-getting-started.md)
* [Interrogation de bases de données sharded cloud (horizontalement partitionnées)](sql-database-elastic-query-horizontal-partitioning.md)
* [Interrogation de bases de données cloud avec différents schémas (verticalement partitionnées)](sql-database-elastic-query-vertical-partitioning.md)
* [SP\_exécuter \_à distance](https://msdn.microsoft.com/library/mt703714)


## <a name="why-use-elastic-queries"></a>Pourquoi utiliser des requêtes élastiques ?

**Base de données SQL Azure**

Interrogation de bases de données SQL Azure complètement dans T-SQL. Cela permet en lecture seule interrogation des bases de données distantes. Il propose une option pour les clients de SQL Server locale actuelles migrer des applications à l’aide de noms de trois et quatre partiel ou serveur lié à une base de données SQL.

**Disponible sur couche standard** Requête élastique est pris en charge sur le niveau de performances Standard outre le niveau de performance Premium. Limitations des performances pour les niveaux inférieurs de performances, voir la section Limitations d’aperçu en dessous.

**Notifications push pour les bases de données distantes**

Requêtes élastiques peuvent distribuer les paramètres SQL pour les bases de données distantes pour l’exécution.

**Exécution de la procédure stockée**

Exécuter des fonctions à distance à l’aide ou des appels de procédure stockée distante [sp\_exécuter \_distant](https://msdn.microsoft.com/library/mt703714).

**Flexibilité**

Tables externes élastique Query peuvent désormais renvoyer des tables distantes avec un schéma différent ou le nom de la table.

## <a name="elastic-database-query-scenarios"></a>Scénarios de requête de base de données élastique

L’objectif consiste à faciliter l’interrogation scénarios où plusieurs bases de données contribuer aux lignes dans un seul résultat global. La requête peut être composée soit par l’utilisateur ou l’application directement ou indirectement grâce à des outils qui sont connectés à la base de données. Ceci est particulièrement utile lors de la création de rapports, à l’aide des outils d’intégration BI ou de données professionnelle — ou n’importe quelle application qui ne peuvent pas être modifiée. Avec une requête élastique, vous pouvez interrogation de plusieurs bases de données à l’aide de l’expérience de connectivité SQL Server familier dans les outils comme Excel, PowerBI, Tableau ou Cognos.
Une requête élastique permet d’accéder facilement à une collection complète de bases de données via des requêtes émises par SQL Server Management Studio ou Visual Studio et facilite les bases de données croisées interrogation à partir d’entité Framework ou d’autres environnements ORM. Figure 1 illustre un scénario où une application cloud existante (qui utilise la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)) s’appuie sur un niveau de données plus grande échelle, et une requête élastique est utilisée pour les rapports de bases de données croisées.

**Figure 1** Requête de base de données élastique utilisée sur couche de données plus grande échelle

![Requête de base de données élastique utilisée sur couche de données plus grande échelle][1]

Scénarios de client de requête élastique sont caractérisées par les topologies suivantes :

* **Partition vertical – requêtes de bases de données croisées** (Topologie 1) : les données sont réparties verticalement entre un certain nombre de bases de données dans une couche de données. En règle générale, différents jeux de tables se trouvent sur différentes bases de données. Cela signifie que le schéma est différent sur différentes bases de données. Par exemple, toutes les tables d’inventaire sont dans une base de données alors que toutes les tables de comptabilité sont dans une seconde base de données. Scénarios d’utilisation courants avec cette topologie nécessitent une pour exécuter des requêtes dans ou pour recevoir des rapports de tableaux dans plusieurs bases de données.
* **Partition horizontale – ont** (Topologie 2) : données sont réparties horizontalement pour distribuer des lignes dans une mise à l’échelle des données niveau. Avec cette approche, le schéma est identique sur toutes les bases de données participants. Cette approche est également appelée « ont ». Ont peut être effectuée et gérées (1) la base de données élastique à l’aide des outils des bibliothèques ou automatique (2)-ont. Une requête élastique est utilisée pour interroger ou recevoir des rapports de nombreux milieu des fragments.

> [AZURE.NOTE] Requête de base de données élastique convient le mieux pour les scénarios de création de rapports occasionnels où la plupart du traitement peut être effectuée sur la couche données. Pour les charges de travail Création de rapports ou scénarios entrepôt de données avec des requêtes plus complexes, également envisagez d’utiliser [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).


## <a name="elastic-database-query-topologies"></a>Élastiques topologies de requête de base de données

### <a name="topology-1-vertical-partitioning--cross-database-queries"></a>Topologie 1 : Partition verticale – requêtes entre bases de données

Pour commencer à coder, voir [prise en main de requête de bases de données croisées (partition verticale)](sql-database-elastic-query-getting-started-vertical.md).

Une requête élastique peut être utilisée pour rendre les données situées dans une base de données SQLDB aux autres bases de données SQLDB. Ceci permet de requêtes à partir d’une base de données pour faire référence aux tables dans n’importe quel autre SQLDB base de données distante. La première étape consiste à définir une source de données externes pour chaque base de données distante. La source de données externe est définie dans la base de données locale à partir de laquelle vous voulez accéder aux tables situé dans la base de données distante. Aucune modification n’est nécessaire sur la base de données distante. Pour verticales partition scénarios classiques où différentes bases de données ont des schémas différents, élastiques requêtes peuvent être utilisées pour implémenter des scénarios d’utilisation courants telles que l’accès aux données de référence et interrogation de base de données croisées.

**Données de référence**: topologie 1 est utilisé pour la gestion des données de référence. Dans l’illustration ci-dessous, les deux tables (T1 et T2) avec les données de référence sont conservées sur une base de données dédiée. À l’aide d’une requête élastique, vous pouvez désormais accéder aux tables T1 et T2 à distance à partir d’autres bases de données, comme indiqué dans l’illustration. Utiliser la topologie 1 si les tables de références sont des requêtes de petite taille ou distants dans la table de référence ont prédicats sélectives.

**Figure 2** Vertical partition : à l’aide de données de référence de requête à la requête élastique

![Vertical partition : à l’aide de données de référence de requête à la requête élastique][3]

**Interroger des bases de données croisées**: élastique des exemples d’utilisation activer nécessitant interrogation entre plusieurs bases de données SQLDB requêtes. Figure 3 montre quatre différentes bases de données : CRM, le stock, RH et produits. Requêtes effectuées dans une base de données doivent également accéder à une ou toutes les autres bases de données. Utiliser une requête élastique, vous pouvez configurer votre base de données pour ce cas en exécutant quelques instructions DDL simples sur chacun des quatre bases de données. Après cette configuration ponctuelle, accès à une table distante est aussi simple que faisant référence à une table locale à partir de vos requêtes T-SQL ou vos outils de décisionnel. Cette approche est recommandée si les requêtes à distance ne retournent pas de résultats volumineux.

**Figure 3** Vertical partition - utilisant élastique requête à la requête sur différentes bases de données

![Vertical partition - utilisant élastique requête à la requête sur différentes bases de données][4]

### <a name="topology-2-horizontal-partitioning--sharding"></a>Topologie 2 : Partition horizontale – ont

À l’aide de la requête élastique pour effectuer des tâches création de rapports sur sharded, c'est-à-dire, horizontalement partitionnées, couche données nécessite une [carte d’éclater élastique de base de données](sql-database-elastic-scale-shard-map-management.md) pour représenter les bases de données de la couche de données. En règle générale, uniquement une carte unique partagé est utilisée dans ce scénario et une base de données dédié avec des fonctions de requête élastique sert de point d’entrée pour les requêtes de création de rapports. Uniquement cette base de données dédiée doit avoir accès au mappage partagé. Figure 4 illustre cette topologie et sa configuration avec la carte de base de données et Éclater requête élastique. Les bases de données dans la couche données peuvent être de toute version de base de données SQL Azure ou edition. Pour plus d’informations sur la bibliothèque de client de base de données élastique et création de cartes éclater, voir [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md).

**Figure 4** Horizontal partition : à l’aide de la requête élastique pour les rapports sur les niveaux de données sharded

![Horizontal partition : à l’aide de la requête élastique pour les rapports sur les niveaux de données sharded][5]

> [AZURE.NOTE] La base de données de requête de base de données élastique dédiée doit être une base de données SQL DB v12. Il n’existe aucune restriction sur au milieu des fragments eux-mêmes.

Pour commencer à coder, voir [mise en route d’une requête élastique pour la partition horizontale (ont)](sql-database-elastic-query-getting-started.md).

## <a name="implementing-elastic-database-queries"></a>Implémentation des requêtes de base de données élastique

Les étapes pour implémenter élastique requête pour les scénarios partition horizontaux et verticaux sont décrits dans les sections suivantes. Ils également faire référence à une documentation plus détaillée pour les différents scénarios partition.

### <a name="vertical-partitioning---cross-database-queries"></a>Partition verticale - requêtes entre bases de données

Les étapes suivantes configurer les requêtes de base de données élastique pour les scénarios partition verticales qui nécessitent un accès à un tableau situé sous bases de données distantes SQLDB possédant le même schéma :

*    [Créer une clé de masque](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Créer de base de données inclus dans l’étendue d’informations d’identification](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    [SOURCE de données externe CREATE/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource de type **SGBDR**
*    [TABLE externe CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Après avoir exécuté les instructions DDL, vous pouvez accéder au tableau à distance « mytable » comme s’il s’agissait d’une table locale. Base de données SQL Azure automatiquement ouvre une connexion à la base de données distante, traite votre requête sur la base de données distante et renvoie le résultat.
Vous trouverez plus d’informations sur les étapes nécessaires pour le scénario partition vertical dans [une requête élastique pour partition verticale](sql-database-elastic-query-vertical-partitioning.md).  

### <a name="horizontal-partitioning---sharding"></a>Partition horizontale - ont

Les étapes suivantes configurer les requêtes de base de données élastique pour horizontales partition des scénarios qui nécessitent un accès à un ensemble de tableau qui sont trouvent sur (en général) plusieurs SQLDB bases de données distantes :

*    [Créer une clé de masque](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
*    [Créer de base de données inclus dans l’étendue d’informations d’identification](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
*    Créer une [carte éclater](sql-database-elastic-scale-shard-map-management.md) représentant votre couche de données à l’aide de la bibliothèque de client élastique de base de données.   
*    [SOURCE de données externe CREATE/DROP](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource de type **SHARD_MAP_MANAGER**
*    [TABLE externe CREATE/DROP](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Une fois que vous avez effectué ces étapes, vous pouvez accéder au tableau partitionné horizontalement « mytable » comme s’il s’agissait d’une table locale. Base de données SQL Azure automatiquement ouvre plusieurs connexions parallèles pour les bases de données distantes où les tables sont stockées physiquement, il traite les demandes sur les bases de données distantes et renvoie le résultat.
Vous trouverez plus d’informations sur les étapes nécessaires pour le scénario partition horizontal dans [une requête élastique pour la partition horizontale](sql-database-elastic-query-horizontal-partitioning.md).

## <a name="t-sql-querying"></a>Requêtes T-SQL
Une fois que vous avez défini vos tables externes et vos sources de données externes, vous pouvez utiliser des chaînes de connexion SQL Server standard pour vous connecter aux bases de données dans lequel vous avez défini vos tables externes. Vous pouvez ensuite exécuter des instructions T-SQL sur vos tables externes sur cette connexion avec les limitations décrites ci-dessous. Vous trouverez plus d’informations et exemples de requêtes T-SQL dans les rubriques documentation pour [partition horizontale](sql-database-elastic-query-horizontal-partitioning.md) et [verticale partition](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Connectivité des outils
Vous pouvez utiliser des chaînes de connexion SQL Server standard pour vous connecter vos applications et outils d’intégration BI ou de données aux bases de données qui ont des tables externes. Assurez-vous que SQL Server est pris en charge en tant que source de données pour votre outil. Une fois connecté, reportez-vous à la base de données de requête élastique et les tables dans cette base de données externes comme vous le feriez avec n’importe quel autre base de données de SQL Server vous vous connectez à votre outil.

> [AZURE.IMPORTANT] Authentification à l’aide d’Azure Active Directory avec les requêtes élastiques n’est pas actuellement pris en charge.

## <a name="cost"></a>Coût

Requête élastique est inclus dans le coût des bases de données de base de données SQL Azure. Notez que topologies où se trouvent vos bases de données distantes dans un centre de données autre que le point de terminaison requête élastique sont prises en charge, mais sortie de données à partir de bases de données distantes sont facturés régulières [taux Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Limitations d’aperçu
* Votre première requête élastique en cours d’exécution peut prendre quelques minutes sur le niveau de performances Standard. Ce moment est nécessaire au chargement de la fonctionnalité de requête élastique ; améliorent les performances de chargement avec des niveaux de performances plus élevés.
* L’écriture de script de sources de données externes ou des tables externes à partir de SSMS ou SSDT n’est pas encore pris en charge.
* Importer/exporter pour SQL DB ne reconnaît pas encore tables externes et des sources de données externes. Si vous avez besoin d’utiliser importer/exporter, déposez ces objets avant d’exporter et recréez-les après l’importation.
* Requête de base de données élastique prend actuellement en charge uniquement les accès en lecture seule à des tables externes. Vous pouvez, toutefois utiliser toutes les fonctionnalités T-SQL sur la base de données dans lequel la table externe est définie. Cela peut être utile pour, par exemple, conserver les résultats temporaires à l’aide, par exemple, sélectionnez < liste_de_colonnes > dans < local_table >, ou pour définir des procédures stockées sur la base de données de requête élastique qui font référence à des tables externes.
* À l’exception de nvarchar (max), types de métier ne sont pas pris en charge dans les définitions de table externe. Pour résoudre ce problème, vous pouvez créer une vue sur la base de données distante qui effectue un cast du type métier dans nvarchar (max), définissez votre table externe sur la vue au lieu de la table de base et puis convertissez-le dans le type métier d’origine dans vos requêtes.
* Statistiques de colonne sur les tables externes ne sont actuellement pas pris en charge. Les statistiques de tables sont prises en charge, mais doivent être créés manuellement.

## <a name="feedback"></a>Commentaires
Veuillez partager des commentaires sur votre expérience avec les requêtes élastiques avec nous Disqus ci-dessous, les forums MSDN, ou sur Stackoverflow. Nous sommes intéressés par tous les types de commentaires sur le service (défauts, les bords, des intervalles fonctionnalité).

## <a name="more-information"></a>Plus d’informations

Vous pouvez trouver plus d’informations sur les bases de données croisées interroger et les scénarios partition verticales dans les documents suivants :

* [Vue d’ensemble partition verticale et de bases de données croisées interroger](sql-database-elastic-query-vertical-partitioning.md)
* Essayez notre didacticiel pour que complet d’un exemple de travail en cours d’exécution en minutes : [prise en main de requête de bases de données croisées (partition verticale)](sql-database-elastic-query-getting-started-vertical.md).


Plus d’informations sur des scénarios partition et ont horizontales sont disponibles ici :

* [Vue d’ensemble de partition et ont horizontale](sql-database-elastic-query-horizontal-partitioning.md)
* Essayez notre didacticiel pour que complet d’un exemple de travail en cours d’exécution en minutes : [mise en route d’une requête élastique pour la partition horizontale (ont)](sql-database-elastic-query-getting-started.md).


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
