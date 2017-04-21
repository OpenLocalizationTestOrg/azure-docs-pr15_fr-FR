L’unité de Transaction de base de données (DTU) est l’unité de mesure dans une base de données SQL qui représente la puissance relative des bases de données selon une mesure réelles : la transaction de base de données. Nous avons un ensemble d’opérations qui sont standard pour une transaction en ligne traitement de requête (OLTP) et puis mesurée combien de transactions pu être effectuée par seconde sous entièrement chargé conditions (c’est la version courte, vous pouvez lire les détails de catégorie dans [vue d’ensemble de référence](../articles/sql-database/sql-database-benchmark-overview.md)). 

Par exemple, une base de données P11 Premium avec DTUs 1750 fournit 350 x DTU plus grande puissance à une base de données simple avec 5 DTUs de traitement. 

![Introduction à la base de données SQL : unique DTUs de base de données par niveau et niveau.](./media/sql-database-understanding-dtus/single_db_dtus.png)

>[AZURE.NOTE] Si vous faites migrer une base de données SQL Server existante, vous pouvez utiliser un outil tiers, [La calculatrice de DTU de base de données SQL Azure](http://dtucalculator.azurewebsites.net/), pour obtenir une estimation du niveau de performance et de votre base de données peut nécessiter dans la base de données SQL Azure de niveau de service.

### <a name="dtu-vs-edtu"></a>DTU et eDTU

La DTU des bases de données unique se traduit par l’eDTU des bases de données élastiques directement. Par exemple, une base de données dans un pool de base de la base de données élastique propose 5 eDTUs. C’est la même performance qu’une seule base de données simple. La différence est que la base de données élastique ne consommer n’importe quel eDTUs à partir du pool jusqu'à ce qu’il y a. 

![Introduction à la base de données SQL : élastiques pools par niveau.](./media/sql-database-understanding-dtus/sqldb_elastic_pools.png)

Aide d’un exemple simple. Effectuez un pool de base de la base de données élastique avec 1000 DTUs et déposez 800 bases de données qu’il contient. Dans la mesure où 200 uniquement les bases de données 800 sont utilisés à tout moment dans le temps (5 DTU X 200 = 1000), vous n’atteignez capacité du pool et les performances de base de données ne se dégrader. Cet exemple est simplifié pour plus de clarté. Le calcul réel est un peu plus longue. Le portail effectue les opérations mathématiques à votre place et appelle une recommandation basée sur l’utilisation de la base de données historiques. Voir [Considérations prix et de performances d’un pool de base de données élastique](../articles/sql-database/sql-database-elastic-pool-guidance.md) pour mieux comprendre les recommandations, ou pour effectuer ces opérations mathématiques vous-même. 
