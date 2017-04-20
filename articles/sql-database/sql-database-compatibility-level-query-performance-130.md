<properties
    pageTitle="Le niveau de compatibilité, comment évaluer | Microsoft Azure"
    description="Étapes et des outils permettant de savoir à quel niveau de compatibilité adapté à votre base de données sur base de données SQL Azure ou Microsoft SQL Server"
    services="sql-database"
    documentationCenter=""
    authors="alainlissoir"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.devlang="NA"
    ms.tgt_pltfrm="NA"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="alainl"/>


# <a name="improved-query-performance-with-compatibility-level-130-in-azure-sql-database"></a>Amélioration des performances de requête avec compatibilité 130 niveau dans la base de données SQL Azure


Base de données SQL Azure ne fonctionne en toute transparence des milliers de bases de données à plusieurs niveaux de compatibilité, conservation et de garantir la compatibilité descendante vers la version correspondante de Microsoft SQL Server pour tous ses clients !

Par conséquent, rien n’empêche les utilisateurs de modifier les bases de données existantes vers le dernier niveau de compatibilité bénéficient à partir du nouveau optimiseur de requêtes et les fonctionnalités du processeur requête. En tant que rappel de l’historique, l’alignement des versions SQL niveaux de compatibilité par défaut sont les suivantes :

- 100 : dans SQL Server 2008 et SQL Azure 11 de base de données.
- 110 : dans SQL Server 2012 et SQL Azure 11 de base de données.
- 120 : dans SQL Server 2014 et SQL Azure V12 de base de données.
- 130 : dans SQL Server 2016 et SQL Azure V12 de base de données.


> [AZURE.IMPORTANT] Démarrage dans **mid juin 2016**, dans la base de données SQL Azure, le niveau de compatibilité par défaut sera 130 au lieu de 120 pour les bases de données **nouvellement créé** .
> 
> Bases de données créées avant 2016 mid juin seront *pas* affectées et conservent leur niveau de compatibilité actuel (110, 100 ou 120). Bases de données migrer à partir de la version de base de données SQL Azure que 11 à V12 n’ont pas de leur niveau de compatibilité modifié des options.


Dans cet article, nous découvrir les avantages de niveau de compatibilité 130 et comment tirer parti de ces avantages. Nous résoudre les éventuels effets secondaires sur les performances des requêtes pour les applications SQL existantes.


## <a name="about-compatibility-level-130"></a>Sur le niveau de compatibilité 130


Tout d’abord, si vous voulez connaître le niveau de compatibilité actuel de votre base de données, exécutez l’instruction Transact-SQL suivante.


```
SELECT compatibility_level
    FROM sys.databases
    WHERE name = '<YOUR DATABASE_NAME>’;
```


Avant ce changement à un niveau 130 arrive des bases de données **nouvellement** créé, nous allons examiner quels cette modification voilà par le biais des exemples de requête très simple et voir comment tout le monde peut en bénéficier.

Traitement des requêtes dans les bases de données relationnelles peut être très complexe et peut entraîner un nombre important de l’informatique et mathématiques à comprendre les choix de conception intrinsèque à et les comportements. Dans ce document, le contenu a été simplifié intentionnellement pour vous assurer que tout le monde avec certaines techniques générales minimale peut comprendre l’impact de la modification du niveau de compatibilité et déterminer comment il peut bénéficier des applications.

Examinons les rapide à ce que le niveau de compatibilité 130 apporte à la table.  Vous pouvez trouver plus d’informations sur [Modifier le niveau de compatibilité de base de données (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx), mais voici un court résumé :

- L’opération d’insertion d’une instruction Insert-select peut être multi-thread ou peut avoir une offre en parallèle, tandis que vous avant cette opération était thread unique.
- Mémoire optimisée table et les requêtes de variables table disposez désormais des plans en parallèle, tandis que vous avant que cette opération a été également thread unique.
- Statistiques de la mémoire optimisée table peuvent désormais être échantillonnées et sont mis à jour automatique. Voir [What's New dans le moteur de base de données : OLTP en mémoire](https://msdn.microsoft.com/library/bb510411.aspx#InMemory) pour plus d’informations.
- Modification du Mode de ligne en mode de traitement par lots v/s avec index de colonne magasin
  - Trie sur une table avec un index de colonne Store est à présent en mode de traitement par lots.
  - Agrégats Windowing fonctionnent maintenant en mode de traitement par lots tels que des instructions TSQL décalage/prospect.
  - Requêtes sur les tables de colonne Store avec plusieurs clauses distinctes fonctionnent en mode de traitement par lots.
  - Requêtes en cours d’exécution sous DOP = 1 ou plan en série également exécuter en Mode de traitement par lots.
- Enfin, réellement provenant des améliorations Estimation de cardinalité avec le niveau de compatibilité 120, mais pour ceux qui en cours d’exécution à un niveau de compatibilité inférieur (c'est-à-dire 100 ou 110), la déplacer à la compatibilité niveau 130 apportera également ces améliorations et ceux-ci peuvent également tirer les performances des requêtes de vos applications.


## <a name="practicing-compatibility-level-130"></a>Exercez 130 le niveau de compatibilité


Première, nous allons apprendre certaines tables, les index et les données aléatoires créées pour essayer de ces nouvelles fonctionnalités. Les exemples de script TSQL peuvent être exécutés sous SQL Server 2016, ou base de données SQL Azure. Toutefois, lorsque vous créez une base de données SQL Azure, vérifiez que vous sélectionnez au moins une base de données P2, car vous devez au moins deux cœurs pour autoriser multi-thread et par conséquent bénéficier de ces fonctionnalités.


```
-- Create a Premium P2 Database in Azure SQL Database

CREATE DATABASE MyTestDB
    (EDITION=’Premium’, SERVICE_OBJECTIVE=’P2′);
GO

-- Create 2 tables with a column store index on
-- the second one (only available on Premium databases)

CREATE TABLE T_source
    (Color varchar(10), c1 bigint, c2 bigint);

CREATE TABLE T_target
    (c1 bigint, c2 bigint);

CREATE CLUSTERED COLUMNSTORE INDEX CCI ON T_target;
GO

-- Insert few rows.

INSERT T_source VALUES
    (‘Blue’, RAND() * 100000, RAND() * 100000),
    (‘Yellow’, RAND() * 100000, RAND() * 100000),
    (‘Red’, RAND() * 100000, RAND() * 100000),
    (‘Green’, RAND() * 100000, RAND() * 100000),
    (‘Black’, RAND() * 100000, RAND() * 100000);

GO 200

INSERT T_source SELECT * FROM T_source;

GO 10
```


À présent, examinons à certaines des fonctionnalités de traitement des requêtes bientôt avec le niveau de compatibilité 130.


## <a name="parallel-insert"></a>INSERTION en parallèle


L’exécution des instructions TSQL ci-dessous exécute l’opération d’insertion sous le niveau de compatibilité 120 et 130, lequel peut être exécuté respectivement l’opération d’insertion dans un seul modèle thread (120) et dans un modèle multi-thread (130).


```
-- Parallel INSERT … SELECT … in heap or CCI
-- is available under 130 only

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO 

-- The INSERT part is in serial

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130
GO

-- The INSERT part is in parallel

INSERT t_target WITH (tablock)
    SELECT C1, COUNT(C2) * 10 * RAND()
        FROM T_source
        GROUP BY C1
    OPTION (RECOMPILE);

SET STATISTICS XML OFF;
```


En demandant réelle le plan de requête, examinez sa représentation graphique ou son contenu XML, vous pouvez déterminer quel Estimation de cardinalité fonction se trouve à lire. Nous prenons le plans côte à côte dans la figure 1, nous pouvons voir clairement que l’exécution de colonne Store insérer accède à partir de série de 120 à parallèle dans 130. Notez également que la modification de l’icône itérateur dans le plan 130 avec deux flèches en parallèle, illustrant le fait que maintenant l’exécution itérateur est bien parallèle. Si vous avez les opérations d’insertion grandes pour terminer, l’exécution en parallèle, liée au nombre de base que vous avez à votre disposition pour la base de données sera plus performante ; jusqu'à une fois 100 plus vite selon votre situation !


*Figure 1 : Opération d’insertion change de série en parallèle avec compatibilité niveau 130.*


![Figure 1](./media/sql-database-compatibility-level-query-performance-130/figure-1.jpg)


## <a name="serial-batch-mode"></a>Mode de traitement par lots en série


De même, déplacement vers le niveau de compatibilité 130 lors du traitement des lignes de données active en mode de traitement par lots. Tout d’abord, lot mode opérations sont disponibles uniquement lorsque vous avez un index de magasin de colonne en place. Ensuite, un lot représente généralement environ 900 lignes et utilise une logique de code optimisé pour l’UC plusieurs cœurs, débit mémoire et directement s’appuie sur les données compressées du Store colonne dès que possible. Dans ces conditions, SQL Server 2016 peut traiter environ 900 lignes en une seule fois, au lieu de 1 ligne dans le temps, et par conséquent, les frais généraux global de l’opération sont maintenant partagé par l’ensemble du lot, réduire le coût par ligne général. Ce montant partagé d’opérations combinées avec la compression store colonne fondamentalement réduit la latence impliqués dans une opération de mode lot SELECT. Vous pouvez trouver plus d’informations sur le magasin de colonne et de commandes en mode en [Columnstore index Guide](https://msdn.microsoft.com/library/gg492088.aspx).


```
-- Serial batch mode execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT (C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO 

– The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


En tant que visibles en-dessous, en observant la requête plans côte à côte sur la figure 2, nous pouvons voir que le mode de traitement a changé avec le niveau de compatibilité, et par conséquent, lors de l’exécution des requêtes dans les deux le niveau de compatibilité totalement différent, nous pouvons voir que la plupart du temps de traitement est passé en mode de ligne (86 %) par rapport au mode de traitement par lots (14 %), où 2 lots ont été traitées. Augmenter le jeu de données, l’avantage augmente.


*Figure 2 : Sélectionnez opération de série en mode lot au niveau de compatibilité 130.*


![Figure 2](./media/sql-database-compatibility-level-query-performance-130/figure-2.jpg)


## <a name="batch-mode-on-sort-execution"></a>Mode de traitement par lots sur l’exécution de tri


Semblable à ce qui précède, mais appliqué à une opération de tri, la transition à partir du mode ligne (niveau de compatibilité 120) en mode de traitement par lots (niveau de compatibilité 130) améliore les performances de l’opération de tri pour les mêmes raisons.


```
-- Batch mode on sort execution

SET STATISTICS XML ON;

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 120;
GO

-- The scan and aggregate are in row mode

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

-- The scan and aggregate are in batch mode,
-- and force MAXDOP to 1 to show that batch mode
-- also now works in serial mode.

SELECT C1, COUNT(C2)
    FROM T_target
    GROUP BY C1
    ORDER BY C1
    OPTION (MAXDOP 1, RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Côte à côte visible sur figure 3, nous voyons que l’opération de tri en mode de ligne indique 81 % du coût, tandis que le mode de traitement par lots représente uniquement % de 19 du coût (respectivement 81 et 56 % dans l’ordre de tri).


*Figure 3 : Opération de tri change de ligne en mode de traitement par lots avec le niveau de compatibilité 130.*


![Figure 3](./media/sql-database-compatibility-level-query-performance-130/figure-3.png)


Bien évidemment, ces exemples contiennent uniquement des milliers de lignes, c'est-à-dire rien lorsque vous examinez les données disponibles dans la plupart des serveurs SQL ces jours. Simplement project ces sur des millions de lignes au lieu de cela, et cela peut se traduire en quelques minutes d’exécution neutralisé quotidiennement en attente de la nature de votre charge de travail.


## <a name="cardinality-estimation-ce-improvements"></a>Améliorations apportées cardinalité Estimation (CE)


Introduite avec SQL Server 2014, une base de données en cours d’exécution à un niveau de compatibilité 120 ou au-dessus fera utiliser de l’Estimation de cardinalité nouvelles fonctionnalités. Estimation de cardinalité est principalement la logique utilisée pour déterminer comment SQL server exécute une requête basée sur sa valeur estimée. L’estimation est calculée à l’aide de saisie à partir des statistiques associés aux objets impliqués dans cette requête. Pratiquement, à un niveau élevé, Estimation de cardinalité fonctions est ligne Nb estimations ainsi que des informations sur la distribution des valeurs, nombre de valeurs distinctes, et les nombres en double contenues dans les tables et les objets référencées dans la requête. Ces estimations prise incorrect, peut entraîner inutiles e/s disque en raison des demandes de mémoire insuffisante (c'est-à-dire TempDB projections) ou à une sélection de l’exécution d’un plan en série sur l’exécution d’une plan en parallèle, citer. Conclusion, estimations incorrectes peuvent entraîner une dégradation des performances globales de l’exécution des requêtes. Sur l’autre côté, une meilleure estimation, estimations plus précises, permet d’accéder à une meilleure exécutions de la requête !

Comme mentionné précédemment, estimations et optimisations de requête de quelque chose de complexe, mais si vous voulez en savoir plus sur les plans de requête et estimateur cardinalité, vous pouvez faire référence au document à [l’Optimisation de vos Plans de requête avec l’estimateur de cardinalité 2014 de SQL Server](https://msdn.microsoft.com/library/dn673537.aspx) pour obtenir de plus amples.


## <a name="which-cardinality-estimation-do-you-currently-use"></a>Quels Estimation de cardinalité utilisez-vous actuellement ?


Pour déterminer sous quel Estimation de cardinalité vos requêtes en cours d’exécution, nous allons utiliser uniquement les exemples de requête ci-dessous. Notez que cet exemple première s’exécutera sous le niveau de compatibilité 110, ce qui implique l’utilisation des fonctions Estimation de cardinalité anciennes.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 110;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Une fois que l’exécution est terminée, cliquez sur le lien XML et consulter les propriétés de la première itérateur comme illustré ci-dessous. Notez le nom de la propriété appelé CardinalityEstimationModelVersion actuellement définie sur 70. Il n’est pas signifie que le niveau de compatibilité de base de données est défini sur la version de SQL Server 7.0 (elle est définie sur 110 comme visible dans les instructions TSQL ci-dessus), mais la valeur 70 représente simplement la fonctionnalité Estimation de cardinalité héritée disponible depuis SQL Server 7.0, laquelle n’était aucun révisions majeures jusque SQL Server 2014 (ce qui est fourni avec un niveau de compatibilité de 120).


*Figure 4 : La CardinalityEstimationModelVersion est définie sur 70 lors de l’utilisation d’un niveau de compatibilité de 110 ou en dessous.*


![Figure 4](./media/sql-database-compatibility-level-query-performance-130/figure-4.png)


Par ailleurs, vous pouvez modifier le niveau de compatibilité à 130 et désactiver l’utilisation de la nouvelle fonction Estimation de cardinalité à l’aide de la LEGACY_CARDINALITY_ESTIMATION défini sur activé avec [ALTER de base de données inclus dans l’étendue de CONFIGURATION](https://msdn.microsoft.com/library/mt629158.aspx). Cela sera exactement identique à l’aide de 110 à partir d’une fonction de cardinalité Estimation point de vue, lors de l’utilisation de la requête dernière niveau de compatibilité de traitement. Ainsi, vous pouvez bénéficier de la nouvelle requête traitement des fonctionnalités bientôt avec le niveau de compatibilité dernière (c'est-à-dire mode lot), mais continuer à utiliser l’ancienne fonctionnalité Estimation de cardinalité si nécessaire.


```
-- Old CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


Simplement déplacer vers le niveau de compatibilité 120 ou 130 permet la nouvelle fonctionnalité de cardinalité Estimation. Dans ce cas, la valeur par défaut CardinalityEstimationModelVersion sera définie en conséquence 120 ou 130 comme visibles en-dessous.


```
-- New CE

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT [c1]
    FROM [dbo].[T_target]
    WHERE [c1] > 20000;
GO

SET STATISTICS XML OFF;
```


*Figure 5 : La CardinalityEstimationModelVersion est définie sur 130 lors de l’utilisation d’un niveau de compatibilité de 130.*


![Figure 5](./media/sql-database-compatibility-level-query-performance-130/figure-5.jpg)


## <a name="witnessing-the-cardinality-estimation-differences"></a>Contrôleur les différences Estimation de cardinalité


À présent, nous allons exécuter un peu plus complexe qui impliquent une jointure interne avec une clause WHERE avec certaines prédicats de la requête, puis examinons l’estimation Nb ligne à partir de la fonction Estimation de cardinalité ancienne tout d’abord.


```
-- Old CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = ON;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


L’exécution de cette requête efficacement renvoie 200,704 lignes, tandis que l’estimation de ligne avec la fonctionnalité de cardinalité Estimation ancienne revendications 194,284 lignes. Bien évidemment, comme indiqué précédemment, ces résultats de l’inventaire ligne également dépendra la fréquence à laquelle vous avez exécuté exemples précédents, qui remplit les tables d’exemple boucle à chaque exécution. Bien évidemment, les prédicats dans votre requête également aura une incidence sur l’estimation réelle en dehors de la forme de table, données de contenu, et comment faire correspondre ces données entre eux.


*Figure 6 : L’estimation Nb ligne est 194,284 ou 6 000 lignes désactiver à partir des 200,704 lignes prévus.*


![Figure 6](./media/sql-database-compatibility-level-query-performance-130/figure-6.jpg)


Dans la même manière, nous allons maintenant s’exécutent la même requête avec la nouvelle fonctionnalité de cardinalité Estimation.


```
-- New CE row estimate with INNER JOIN and WHERE clause

ALTER DATABASE MyTestDB
    SET COMPATIBILITY_LEVEL = 130;
GO

ALTER DATABASE
    SCOPED CONFIGURATION
    SET LEGACY_CARDINALITY_ESTIMATION = OFF;
GO

SET STATISTICS XML ON;

SELECT T.[c2]
    FROM
                   [dbo].[T_source] S
        INNER JOIN [dbo].[T_target] T  ON T.c1=S.c1
    WHERE
        S.[Color] = ‘Red’  AND
        S.[c2] > 2000  AND
        T.[c2] > 2000
    OPTION (RECOMPILE);
GO

SET STATISTICS XML OFF;
```


Examiner les ci-dessous, nous voyons maintenant que l’estimation de ligne est 202,877, ou beaucoup plus étroite et supérieures à celles de l’ancien Estimation de cardinalité.

*Figure 7 : L’estimation Nb ligne est désormais 202,877, au lieu de 194,284.*


![Figure 7](./media/sql-database-compatibility-level-query-performance-130/figure-7.jpg)


En réalité, le jeu de résultats est 200,704 lignes (mais tout dépend de la fréquence à laquelle vous n’avez exécuté les requêtes des exemples précédents, mais plus important, car le TSQL utilise l’instruction ALEA (), les valeurs réelles renvoyés peuvent varier l’exécution à une autre). Par conséquent, dans cet exemple, l’Estimation de cardinalité nouveau est le meilleur à évaluer le nombre de lignes, car 202,877 est beaucoup plus proche 200,704, que 194,284 ! Enfin, si vous modifiez la clause WHERE prédicats à l’égalité (au lieu de « > » par exemple), cela pourrait rendre les estimations entre l’ancienne et nouvelle fonction cardinalité encore plus différente, selon correspond au nombre que vous pouvez accéder.

Bien évidemment, dans ce cas, est ~ 6000 lignes éteint à partir du nombre réel ne représente pas une grande quantité de données dans certains cas. À présent, la fonction transpose cette option pour des millions de lignes sur plusieurs tables et requêtes plus complexes et il arrive l’estimation peut être désactivée par des millions de lignes et par conséquent, les risques en matière de prise en charge le plan d’exécution incorrecte ou demander des demandes de mémoire insuffisante et causer TempDB projections et pour en e/s, sont beaucoup plus élevé.

Si vous avez la possibilité, exercices pratiques cette comparaison avec vos requêtes et les jeux de données, plus courantes et voyez par vous-même en quantité certaines des estimations anciennes et nouvelle sont concernés, tandis que certains peuvent simplement devenir plus hors de la réalité, ou certaines autres simplement simplement rapprocher à la ligne réelle compte réellement retourné dans les jeux de résultats. Toutes ces sections dépend de la forme de vos requêtes, les caractéristiques de base de données SQL Azure, la nature et la taille de vos groupes de données et les statistiques disponibles les concernant. Si vous venez de créer votre instance de base de données SQL Azure, l’optimiseur de requêtes devrez créer sa connaissance à partir de zéro au lieu de réutiliser les statistiques de la requête s’exécute précédent. Par conséquent, les estimations sont très contextuels et presque spécifiques à chaque situation serveur et d’application. Il est important à prendre en compte !


## <a name="some-considerations-to-take-into-account"></a>Certains éléments à prendre en compte


Bien que la plupart des charges de travail susceptible de bénéficier du niveau de compatibilité 130, avant d’adopter le niveau de compatibilité pour votre environnement de production, vous avez fondamentalement 3 options :

1. Vous déplacez vers le niveau de compatibilité 130 et voir comment effectuent des actions. Au cas où vous constatez certaines régression simplement définir au niveau de la compatibilité à son niveau d’origine ou conserver 130 et inverse uniquement l’Estimation de cardinalité retour au mode hérité (comme expliqué ci-dessus, cela seul peut résoudre le problème).
2. Vous testez vos applications existantes sous la charge de production similaires, optimiser les et validez les performances avant de passer en production. En cas de problèmes, même que celle décrite ci-dessus, vous pouvez toujours revenir au niveau de compatibilité d’origine, ou simplement inverser l’Estimation de cardinalité revenir au mode hérité.
3. Comme une option finale et la façon la plus récente de répondre à ces questions, consiste à tirer parti de la banque de requête. C’est option recommandée aujourd'hui ! Pour faciliter l’analyse de vos requêtes sous compatibilité niveau 120 ou en dessous et 130, nous ne pouvons pas vous invitons suffisamment à utiliser banque de requête. Magasin de requête est disponible avec la dernière version de V12 de base de données SQL Azure, et il est conçu pour vous aider à la résolution des problèmes de performances de requête. Considérez le magasin de requête comme un enregistreur de données de vol pour votre base de données collecter et présenter des informations historiques détaillées sur toutes les requêtes. Cela simplifie considérablement post-mortem performances en réduisant le temps pour diagnostiquer et résoudre les problèmes. Vous trouverez davantage d’informations sur [requête Store : un enregistreur de données de vol pour votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/).


Au niveau élevé, si vous disposez d’un ensemble de bases de données en cours d’exécution au niveau de compatibilité 120 ou en dessous et envisagez de déplacer certaines d'entre elles à 130 ou parce que votre charge de travail automatiquement mise en service de nouvelles bases de données qui seront bientôt être définie par défaut à 130, envisagez les suivantes :

- Avant de modifier pour le nouveau niveau de compatibilité en production, activer le magasin de requête. Vous pouvez faire référence à [Modifier le Mode de compatibilité de base de données et utilisez le magasin de requête](https://msdn.microsoft.com/library/bb895281.aspx) pour plus d’informations.
- Ensuite, testez critiques toutes les charges de travail à l’aide de données représentatives et les requêtes d’un environnement de type production et comparer les performances a rencontré et comme signalé par banque de requête. Si vous rencontrez certains régression, vous pouvez identifier les requêtes régressés avec le magasin de requête et utiliser le plan de forcer l’option à partir de requête Store (plan ou épingler). Dans ce cas, vous définitivement Restez au niveau de compatibilité 130 et utilisez le plan de requête ancien comme suggéré par le magasin de requête.
- Si vous voulez tirer parti des nouvelles fonctionnalités et des fonctions de base de données SQL Azure (qui exécute SQL Server 2016), mais sont sensibles aux modifications apportées par le niveau de compatibilité 130, en dernier recours, vous pouvez envisager de forcer le niveau de compatibilité en retour vers le niveau adapté à votre charge de travail à l’aide d’une instruction ALTER DATABASE. Mais tout d’abord, n’oubliez pas que le plan de requête Store l’épinglage d’option est la meilleure option car n’utilisant ne pas 130 fondamentalement restent au niveau de la fonctionnalité d’une ancienne version de SQL Server.
- Si vous avez partagées applications couvrant plusieurs bases de données, il peut être nécessaire mettre à jour de la logique de mise en service de vos bases de données afin de garantir un niveau de compatibilité cohérente sur toutes les bases de données ; sont ceux qui anciens et nouveaux généré. Performances de charge de travail de votre application pourraient être sensibles au fait que certaines bases de données en cours d’exécution en niveaux de compatibilité, et par conséquent, cohérence au niveau de compatibilité entre les bases de données peut être nécessaire pour fournir la même expérience à vos clients tous les tous les niveaux. Notez qu’il n’est pas un formulaire d’autorisation, elle dépend vraiment comment votre application est affectée par le niveau de compatibilité.
- Enfin, en ce qui concerne l’Estimation de cardinalité et comme la modification du niveau de compatibilité, avant de passer en production, il est recommandé pour tester votre charge de travail de production dans les conditions de nouveau pour déterminer si votre application avantages des améliorations Estimation de cardinalité.


## <a name="conclusion"></a>Conclusion


L’utilisation de base de données SQL Azure pour bénéficier de toutes les améliorations de SQL Server 2016 peut améliorer clairement votre exécutions de la requête. Comme-est ! Bien entendu, comme une nouvelle fonctionnalité, une évaluation appropriée doit être effectuée pour déterminer les conditions exactes dans lesquelles votre charge de travail de base de données fonctionne mieux. L’expérience montre que la plupart des charges de travail sont amenés au moins exécuter en toute transparence sous le niveau de compatibilité 130, tout en tirant parti de traitement des fonctions et Estimation de cardinalité nouvelle requête. Qui dit, dans la pratique, il existe toujours quelques exceptions effectuant échéance NOMPROPRE diligence est une évaluation importantes pour déterminer combien vous pouvez bénéficier de ces améliorations. Et là encore, le magasin de requête peut être très utile dans ce travail !

À mesure que SQL Azure évolue, vous pouvez vous attendre un niveau de compatibilité 140 à l’avenir. Heure est le cas, nous allons commencer expliquer ce que ce niveau de compatibilité future 140 s’affiche, comme nous brièvement mentionnés ici niveau compatibilité 130 est en bénéficiant aujourd'hui.

Pour l’instant, nous allons pas oublier, à partir de juin 2016, base de données SQL Azure remplacera le niveau de compatibilité par défaut de 120 par 130 des bases de données nouvellement créés. N’oubliez pas !


## <a name="references"></a>Références


- [Quelles sont les nouveautés dans le moteur de base de données](https://msdn.microsoft.com/library/bb510411.aspx#InMemory)

- [Blog : Requête Store : un enregistreur de données de vol pour votre base de données, par Borko Novakovic, 2016 8 juin](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database/)

- [MODIFIER le niveau de compatibilité de base de données (Transact-SQL)](https://msdn.microsoft.com/library/bb510680.aspx)

- [MODIFIER LA BASE DE DONNÉES DE CONFIGURATION D’ÉTENDUES](https://msdn.microsoft.com/library/mt629158.aspx)

- [Niveau de compatibilité 130 pour V12 de base de données SQL Azure](https://azure.microsoft.com/updates/compatibility-level-130-for-azure-sql-database-v12/)

- [Optimisation de votre requête Plans avec SQL Server 2014 cardinalité estimateur](https://msdn.microsoft.com/library/dn673537.aspx)

- [Guide d’index ColumnStore](https://msdn.microsoft.com/library/gg492088.aspx)

- [Blog : Meilleures performances des requêtes avec le niveau de compatibilité 130 dans la base de données SQL Azure, par Alain Lissoir, 2016 6 mai](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/)



<!--
Improved Query Performance with Compatibility Level 130 in Azure SQL Database

May 6, 2016 by Alain Lissoir (AlainL), on GitHub 'alainlissoir'.

https://blogs.msdn.microsoft.com/sqlserverstorageengine/2016/05/06/improved-query-performance-with-compatibility-level-130-in-azure-sql-database/

..... Now, above.
....................
..... Soon, below?

CAPS / MSDN ideally, but instead on ACom:
.. # Assess effects of latest compatibility level on query performance, how to

sql-database-compatibility-level-query-performance-130.md

genemi = MightyPen , 2016-05-20  Friday  17:00pm
-->
