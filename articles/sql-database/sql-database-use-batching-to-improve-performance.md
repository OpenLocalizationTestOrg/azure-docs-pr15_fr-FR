 <properties
    pageTitle="Comment utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL Azure"
    description="La rubrique fournit des preuves que le traitement par lots des opérations de base de données imroves considérablement la vitesse et extensibilité élevées de vos applications de base de données SQL Azure. Bien que ces techniques de traitement par lots fonctionnent pour les bases de données SQL Server, l’objectif de l’article se trouve sur Azure."
    services="sql-database"
    documentationCenter="na"
    authors="annemill"
    manager="jhubbard"
    editor="" />


<tags
    ms.service="sql-database"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="data-management"
    ms.date="07/12/2016"
    ms.author="annemill" />

# <a name="how-to-use-batching-to-improve-sql-database-application-performance"></a>Comment utiliser le traitement par lots pour améliorer les performances de l’application de base de données SQL

Opérations de base de données SQL Azure le traitement par lots sensiblement améliore les performances et extensibilité élevées de vos applications. Afin de mieux comprendre les avantages, la première partie de cet article traite des résultats de test qui comparent les demandes séquentielles et par lots à une base de données SQL. Le reste de l’article montre les techniques, les scénarios et les considérations pour vous aider à utiliser le traitement par lots correctement dans vos applications Azure.

**Auteurs**: Jason Roth, Silvano Coriani, Trent Swanson (pleine échelle 180 Inc)

**Relecteurs**: Conor Cunningham, Michael Thomassy

## <a name="why-is-batching-important-for-sql-database"></a>Pourquoi est le traitement par lots importantes pour la base de données SQL ?
Le traitement par lots appels vers un service distant est une stratégie connue pour améliorer les performances et extensibilité élevées. Il constituent traitement des coûts fixes pour les interactions avec un service distant, tels que sérialisation et désérialisation transfert réseau. Le conditionnement combien de transactions distincte dans un seul lot réduit ces coûts.

Dans ce document, nous voulons examiner diverses bases de données SQL le traitement par lots stratégies et scénarios. Bien que ces stratégies sont également importants pour les applications en local qui utilisent SQL Server, il existe plusieurs raisons pour mettre en surbrillance l’utilisation de traitement par lots de base de données SQL :

- Il est potentiellement plus grande latence du réseau de l’accès à la base de données SQL, surtout si vous accédez à base de données SQL en dehors du centre de données Microsoft Azure même.
- Les caractéristiques partagés de base de données SQL signifie que l’efficacité de la corrélation de calque données access à l’extensibilité du globale de la base de données. Base de données SQL doit empêcher tout client/utilisateur unique de monopoliser les ressources de base de données au détriment des autres clients. En réponse à l’utilisation de plus de quotas prédéfinis, base de données SQL peut réduire le débit ou répondre grâce à la limitation des exceptions. L’efficacité, telles que le traitement par lots, permettre d’effectuer plus de travail sur base de données SQL avant d’atteindre ces limites. 
- Le traitement par lots est également utile pour architectures qui utilisent plusieurs bases de données (ont). L’efficacité de vos interactions avec chaque unité de base de données est toujours un facteur clé dans votre extensibilité élevées globale. 

Un des avantages de l’utilisation de base de données SQL est que vous n’êtes pas obligé de gérer les serveurs hébergeant la base de données. Toutefois, cette infrastructure gérée signifie également que vous deviez envisager différemment optimisations de base de données. Vous pouvez rechercher n’est plus pour améliorer l’infrastructure de matériel ou de réseau de base de données. Microsoft Azure contrôles ces environnements. La zone principale que vous pouvez contrôler est comment votre application interagit avec la base de données SQL. Le traitement par lots est un de ces optimisations. 

La première partie du papier examine les différentes techniques de traitement par lots pour les applications .NET qui utilisent la base de données SQL. Les deux dernières sections couvrent le traitement par lots des instructions et des scénarios.

## <a name="batching-strategies"></a>Stratégies de traitement par lots

### <a name="note-about-timing-results-in-this-topic"></a>Remarque sur les résultats de l’horloge dans cette rubrique
>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation mais sont conçus pour afficher les **performances relative**. Le minutage est basé sur une moyenne au moins 10 des séries de tests. Les opérations sont insertions dans une table vide. Ces tests ont été mesurées pre-V12, et elles ne correspondent pas nécessairement débit des données que vous pouvez rencontrer dans une base de données V12 l’utilisation de la nouvelle [niveaux de service](sql-database-service-tiers.md). L’avantage relatif de la technique le traitement par lots doit être similaire.

### <a name="transactions"></a>Transactions
Il semble étrange pour commencer à consulter le traitement par lots en expliquant transactions. Mais l’utilisation des transactions côté client est un effet de traitement par lots côté serveur légère qui améliore les performances. Et transactions peuvent être ajoutées avec uniquement quelques lignes de code, afin qu’ils fournissent un moyen rapide d’améliorer les performances des opérations séquentielles.

Examinons le code c# suivant qui contient une séquence d’insertion et mettre à jour les opérations sur une table simple.

    List<string> dbOperations = new List<string>();
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 1");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 2");
    dbOperations.Add("update MyTable set mytext = 'updated text' where id = 3");
    dbOperations.Add("insert MyTable values ('new value',1)");
    dbOperations.Add("insert MyTable values ('new value',2)");
    dbOperations.Add("insert MyTable values ('new value',3)");

Le code suivant ADO.NET exécute dans l’ordre de ces opérations.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
    
        foreach(string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn);
            cmd.ExecuteNonQuery();                   
        }
    }

La meilleure façon d’optimiser ce code consiste à implémenter une forme de côté client le traitement par lots de ces appels. Mais il existe un moyen simple pour améliorer les performances de ce code par habillage simplement la séquence d’appels dans une transaction. Voici le même code qui utilise une transaction.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        conn.Open();
        SqlTransaction transaction = conn.BeginTransaction();
    
        foreach (string commandString in dbOperations)
        {
            SqlCommand cmd = new SqlCommand(commandString, conn, transaction);
            cmd.ExecuteNonQuery();
        }
    
        transaction.Commit();
    }

Transactions sont réellement utilisées dans ces deux exemples. Dans le premier exemple, chaque appel individuel est une transaction implicite. Dans le second exemple, une transaction explicite à la ligne tous les appels. Par la documentation de [l’écriture anticipée journal des transactions](https://msdn.microsoft.com/library/ms186259.aspx), les enregistrements du journal sont vidés sur le disque lorsque la transaction valide. Donc en incluant d’autres appels dans une transaction, l’écriture dans le journal des transactions peut retarder jusqu'à ce que la transaction est validée. En fait, vous activez le traitement par lots pour l’écriture sur journal des transactions du serveur.

Le tableau suivant montre quelques résultats des tests ad hoc. Les tests effectués les insertions séquentielles mêmes avec et sans transactions. Pour plus de perspective, le premier jeu des tests exécuté à distance à partir d’un ordinateur portable à la base de données Microsoft Azure. Le second jeu des tests exécuté à partir d’un service cloud et la base de données que les deux résident dans le centre de données Microsoft Azure même (Ouest é.u.). Le tableau suivant indique la durée en millisecondes d’insertions séquentielles avec et sans transactions.

**Local vers Azure**:

| Opérations | Aucune Transaction (ms) | Transaction (ms) |
|---|---|---|
| 1 | 130 | 402 |
| 10 | 1208 | 1226 |
| 100 | 12662 | 10395 |
| 1000 | 128852 | 102917 |


**Azure à Azure (même centre de données)**:

| Opérations | Aucune Transaction (ms) | Transaction (ms) |
|---|---|---|
| 1 | 21 | 26 |
| 10 | 220 | 56 |
| 100 | 2145 | 341 |
| 1000 | 21479 | 2756 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

Basé sur les résultats des tests précédents, une seule opération à la ligne dans une transaction diminue réellement les performances. Mais si vous augmentez le nombre d’opérations au sein d’une seule opération, l’amélioration des performances devient plus marquée. La différence de performances est également plus visible lorsque toutes les opérations se trouvent dans le centre de données Microsoft Azure. La latence accrue de l’utilisation de la base de données SQL en dehors du centre de données Microsoft Azure masque en partie le gain de performances de l’utilisation des transactions.

Bien que l’utilisation des transactions peut améliorer les performances, continuent à [observer les meilleures pratiques pour les transactions et connexions](https://msdn.microsoft.com/library/ms187484.aspx). Conserver la transaction autant que possible, puis fermez la connexion de base de données quand le travail est terminé. L’à l’aide d’instruction dans l’exemple précédent permet de s’assurer que la connexion est fermée lorsque le bloc de code suivantes se termine.

L’exemple précédent montre que vous pouvez ajouter une transaction locale à n’importe quel code ADO.NET avec deux lignes. Transactions offrent un moyen rapide d’améliorer les performances de code qui vous paraît insertion séquentielle, mettre à jour et supprimer des opérations. Toutefois, pour de meilleures performances, pensez à modifier le code à la suite de tirer parti de traitement par lots côté client, telles que les paramètres table.

Pour plus d’informations sur les transactions dans ADO.NET, voir [Transactions locales dans ADO.NET](https://msdn.microsoft.com/library/vstudio/2k2hy99x.aspx).

### <a name="table-valued-parameters"></a>Paramètres table
Paramètres table prend en charge les types de table définie par l’utilisateur en tant que paramètres dans les instructions Transact-SQL, des procédures stockées et des fonctions. Cette technique de traitement par lots côté client permet d’envoyer plusieurs lignes de données dans le paramètre de table. Pour utiliser les paramètres table, commencez par définir un type de table. L’instruction Transact-SQL suivante crée un type de tableau nommé **MyTableType**.

    CREATE TYPE MyTableType AS TABLE 
    ( mytext TEXT,
      num INT );
 

Dans le code, vous créez une **table de données** avec les mêmes noms exactes et types du type de table. Passer de cette **table de données** dans un paramètre dans une requête de texte ou appel de procédure stockée. L’exemple suivant illustre cette technique :

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        DataTable table = new DataTable();
        // Add columns and rows. The following is a simple example.
        table.Columns.Add("mytext", typeof(string));
        table.Columns.Add("num", typeof(int));    
        for (var i = 0; i < 10; i++)
        {
            table.Rows.Add(DateTime.Now.ToString(), DateTime.Now.Millisecond);
        }
    
        SqlCommand cmd = new SqlCommand(
            "INSERT INTO MyTable(mytext, num) SELECT mytext, num FROM @TestTvp",
            connection);
                    
        cmd.Parameters.Add(
            new SqlParameter()
            {
                ParameterName = "@TestTvp",
                SqlDbType = SqlDbType.Structured,
                TypeName = "MyTableType",
                Value = table,
            });
    
        cmd.ExecuteNonQuery();
    }

Dans l’exemple précédent, l’objet **SqlCommand** insère les lignes à partir d’un paramètre table, **@TestTvp**. L’objet de **table de données** créé précédemment est affectée à ce paramètre avec la méthode **SqlCommand.Parameters.Add** . Le traitement par lots les insertions en un seul appel sensiblement améliore les performances sur insertions séquentielles.

Pour améliorer davantage l’exemple précédent, utilisez une procédure stockée au lieu d’une commande en mode texte. La commande Transact-SQL suivante crée une procédure stockée qui prend le paramètre de table **SimpleTestTableType** .

    CREATE PROCEDURE [dbo].[sp_InsertRows] 
    @TestTvp as MyTableType READONLY
    AS
    BEGIN
    INSERT INTO MyTable(mytext, num) 
    SELECT mytext, num FROM @TestTvp
    END
    GO

Modifier la déclaration de l’objet **SqlCommand** dans l’exemple précédent à ce qui suit.

    SqlCommand cmd = new SqlCommand("sp_InsertRows", connection);
    cmd.CommandType = CommandType.StoredProcedure;

Dans la plupart des cas, les paramètres table ont équivalentes ou meilleures performances que d’autres techniques de traitement par lots. Paramètres table sont souvent préférables, car ils ne sont plus flexibles que les autres options. Par exemple, d’autres techniques, telles que copie en bloc SQL, autorisent uniquement l’insertion de nouvelles lignes. Mais avec les paramètres de table, vous pouvez utiliser une logique dans la procédure stockée pour déterminer les lignes sont mises à jour et celles qui sont insère. Le type de table peut également être modifié pour contenir une colonne « Opération » qui indique si la ligne spécifiée doit être insérée, mise à jour ou supprimée.

Le tableau suivant montre les résultats des tests ad hoc pour l’utilisation de paramètres table en millisecondes.

| Opérations | Local vers Azure (ms)  | Centre de données même Azure (ms) |
|---|---|---|
| 1 | 124 | 32 |
| 10 | 131 | 25 |
| 100 | 338 | 51 |
| 1000 | 2615 | 382 |
| 10000 | 23830 | 3586 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

L’amélioration des performances le traitement par lots est immédiatement visible. Dans le test séquentiel précédent, opérations 1000 a 129 secondes en dehors du centre de données et 21 secondes de dans le centre de données. Mais avec les paramètres de table, opérations 1000 2,6 secondes uniquement à l’extérieur du centre de données et 0,4 secondes dans le centre de données.

Pour plus d’informations sur les paramètres table, voir [Paramètres de la table](https://msdn.microsoft.com/library/bb510489.aspx).

### <a name="sql-bulk-copy"></a>Copie en bloc SQL
Copie en bloc SQL une autre méthode consiste à insérer de grandes quantités de données dans une base de données cible. .NET applications peuvent utiliser la classe **SqlBulkCopy** pour effectuer les opérations d’insertion en bloc. **SqlBulkCopy** est similaire à l’outil de ligne de commande, **Bcp.exe**ou l’instruction Transact-SQL, **Insertion en bloc**. L’exemple suivant montre comment copier en bloc les lignes dans la **table de données**, table, dans la table de destination dans SQL Server, MyTable source.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        using (SqlBulkCopy bulkCopy = new SqlBulkCopy(connection))
        {
            bulkCopy.DestinationTableName = "MyTable";
            bulkCopy.ColumnMappings.Add("mytext", "mytext");
            bulkCopy.ColumnMappings.Add("num", "num");
            bulkCopy.WriteToServer(table);
        }
    }

Il existe certains cas où copier en bloc est préférée paramètres table. Consultez le tableau de comparaison des paramètres table par rapport aux opérations Insérer en bloc dans la rubrique [Paramètres de la table](https://msdn.microsoft.com/library/bb510489.aspx).

Les résultats de test ad hoc suivants montrent les performances de traitement par lots avec **SqlBulkCopy** en millisecondes.

| Opérations | Local vers Azure (ms)  | Centre de données même Azure (ms) |
|---|---|---|
| 1 | 433 | 57 |
| 10 | 441 | 32 |
| 100 | 636 | 53 |
| 1000 | 2535 | 341 |
| 10000 | 21605 | 2737 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

De réduire la taille lot, utilisez les paramètres table surpassé la classe **SqlBulkCopy** . Toutefois, **SqlBulkCopy** effectuée 12-31 % plus rapide que paramètres table pour les tests de 1 000 et 10 000 lignes. Comme paramètres table, **SqlBulkCopy** est une bonne option pour les insertions groupées, en particulier les performances des opérations non regroupés par rapport.

Pour plus d’informations sur la copie en bloc dans ADO.NET, voir [Opérations de copie en bloc dans SQL Server](https://msdn.microsoft.com/library/7ek5da1a.aspx).

### <a name="multiple-row-parameterized-insert-statements"></a>Instructions paramétrées insérer plusieurs lignes
Une autre solution de petits lots consiste à construire une instruction INSERT paramétrée volumineuse qui insère plusieurs lignes. L’exemple suivant illustre cette technique.

    using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
    {
        connection.Open();
    
        string insertCommand = "INSERT INTO [MyTable] ( mytext, num ) " +
            "VALUES (@p1, @p2), (@p3, @p4), (@p5, @p6), (@p7, @p8), (@p9, @p10)";
    
        SqlCommand cmd = new SqlCommand(insertCommand, connection);
    
        for (int i = 1; i <= 10; i += 2)
        {
            cmd.Parameters.Add(new SqlParameter("@p" + i.ToString(), "test"));
            cmd.Parameters.Add(new SqlParameter("@p" + (i+1).ToString(), i));
        }
    
        cmd.ExecuteNonQuery();
    }
 

Cet exemple est destiné à afficher le concept de base. Un scénario plus plausible serait parcourir les entités requises pour créer la chaîne de requête et des paramètres de la commande simultanément. Vous êtes limité à un total 2100 des paramètres de requête, afin que cela permet de limiter le nombre total de lignes qui peut être traité de cette manière.

Les résultats de test ad hoc suivants montrent les performances de ce type d’instruction insert en millisecondes.

| Opérations | Paramètres de table (ms) | Seule instruction INSERT (ms) |
|---|---|---|
| 1 | 32 | 20 |
| 10 | 30 | 25 |
| 100 | 33 | 51 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

Cette approche peut être légèrement plus rapide pour les lots qui sont moins de 100 lignes. Bien que l’amélioration est petite, cette technique est une autre option peut-être fonctionner correctement dans votre scénario d’application spécifique.

### <a name="dataadapter"></a>DataAdapter
La classe **DataAdapter** vous permet de modifier un objet de **jeu de données** , puis soumettez les modifications comme insérer, mettre à jour et supprimer des opérations. Si vous utilisez le **DataAdapter** de cette manière, il est important de noter que les appels séparés sont effectuées pour chaque opération distincte. Pour améliorer les performances, utilisez la propriété **UpdateBatchSize** et le nombre d’opérations qui doivent être regroupés par lots à la fois. Pour plus d’informations, voir [Exécution lot opérations à l’aide de DataAdapters](https://msdn.microsoft.com/library/aadf8fk2.aspx).

### <a name="entity-framework"></a>Infrastructure d’entités
Entité Framework ne prend pas actuellement en charge le traitement par lots. Différents développeurs de la Communauté ont tenté de présenter des solutions de contournement, tels qu’Ignorer la méthode **SaveChanges** . Mais les solutions sont généralement complexe et personnalisé à l’application et le modèle de données. Le projet codeplex entité Framework a actuellement une page de discussion sur cette demande de fonctionnalité. Pour afficher cette discussion, voir [Création des Notes de réunion – 2 août 2012](http://entityframework.codeplex.com/wikipage?title=Design%20Meeting%20Notes%20-%20August%202%2c%202012).

### <a name="xml"></a>XML
Pour plus de précision, nous sentiment qu’il est important de parler XML comme une stratégie de traitement par lots. Toutefois, l’utilisation de la technologie XML a aucune avantages par rapport aux autres méthodes et plusieurs inconvénients. L’approche est similaire à paramètres table, mais un fichier XML ou une chaîne est passé à une procédure stockée au lieu d’une table définie par l’utilisateur. La procédure stockée analyse les commandes dans la procédure stockée.

Il existe plusieurs inconvénients de cette approche :

- Travailler avec XML peut être fastidieuse et source d’erreurs.
- L’analyse XML sur la base de données peut être intensive du processeur.
- Dans la plupart des cas, cette méthode est plus lente que paramètres table.

Pour ces raisons, l’utilisation de la technologie XML requêtes par lots n’est pas recommandée.

## <a name="batching-considerations"></a>Considérations sur le traitement par lots
Les sections suivantes fournissent plus de conseils pour l’utilisation de traitement par lots dans les applications de base de données SQL.

### <a name="tradeoffs"></a>Compromis
Selon votre architecture, le traitement par lots implique un compromis entre les performances et la résilience. Par exemple, considérez le scénario où votre rôle affiche inopinément la section vers le bas. Si vous perdez une ligne de données, l’impact est inférieure à l’impact de perdre un lot de grande taille de lignes non soumises. Il existe un risque supérieur lorsque la mémoire tampon de lignes avant de les envoyer à la base de données dans une fenêtre de temps spécifié.

En raison de ce compromis, évaluez le type d’opérations de ce lot vous. Traitement par lots plus agressif (lots plus grande et fenêtres de temps plus longues) avec des données sont moins critiques.

### <a name="batch-size"></a>Taille du lot
Dans nos tests, est survenu généralement aucun avantage à transmettre des lots de grande taille en plusieurs segments. Ce compartimentage souvent résulte en fait baisse des performances de l’envoi d’un seul lot de grande taille. Par exemple, considérez un scénario où vous souhaitez insérer des lignes de 1000. Le tableau suivant indique la durée d’utiliser des paramètres table pour insérer des lignes de 1000 lorsqu’elle est divisée en lots plus petits.

| Taille du lot | Itérations | Paramètres de table (ms) |
| -------- | --- | --- |
| 1000 | 1 | 347 |
| 500 | 2 | 355 |
| 100 | 10 | 465 |
| 50 | 20 | 630 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

Vous pouvez voir que les performances de 1000 lignes consiste à les soumettre tous en même temps. Dans d’autres tests (non illustrés ici) est survenu un gain de performances petite afin de scinder un lot de 10000 ligne en deux lots de 5 000. Mais le schéma de table pour ces tests est relativement simple, afin que vous effectuez des tests sur vos données spécifiques et les tailles lot pour vérifier ces conclusions.

Un autre facteur à prendre en considération est que si le lot total devient trop volumineux, base de données SQL peut limiter et refuser valider le lot. Pour de meilleurs résultats, testez votre scénario spécifique pour déterminer s’il existe une taille de lot idéale. Rendre la taille du lot configurable en cours d’exécution pour activer l’ajustement rapide basé sur les performances ou des erreurs.

Pour finir, comparer la taille du lot avec les risques associés le traitement par lots. Si est erreurs transitoires ou le rôle échoue, vous pouvez les conséquences d’une nouvelle tentative de l’opération ou de perdre les données dans le lot.

### <a name="parallel-processing"></a>Traitement en parallèle
Que se passe-t-il si vous a pris l’approche de réduction de la taille du lot mais plusieurs threads permet d’exécuter le travail ? Là encore, nos tests ont montré que plusieurs lots plus petits multithreads généralement exécutées pire qu’un seul lot beaucoup plus important. Le test suivant tente d’insérer des lignes de 1000 dans un ou plusieurs lots en parallèle. Ce test indique comment plus lots sonneries réellement baisse des performances.

| Taille du lot [itérations] | Deux threads (ms) | Quatre threads (ms) | Six threads (ms) |
| -------- | --- | --- | --- |
| 1000 [1] | 277 | 315 | 266 |
| 500 [2] | 548 | 278 | 256 |
| 250 [4] | 405 | 329 | 265 |
| 100 [10] | 488 | 439 | 391 |

>[AZURE.NOTE] Résultats ne sont pas des tests d’évaluation. Voir la [Remarque à propos des résultats de l’horloge dans cette rubrique](#note-about-timing-results-in-this-topic).

Il existe pour la dégradation des performances en raison de parallélisme pour plusieurs raisons :

- Il existe plusieurs appels réseau simultanées au lieu d’un.
- Plusieurs opérations sur une seule table peuvent entraîner de conflit et bloquer.
- Il existe transparents associés multithread.
- La dépense d’ouvrant plusieurs connexions compense l’avantage de traitement en parallèle.

Si vous ciblez différentes tables ou des bases de données, il est possible afficher des performances gain grâce à cette stratégie. Ont de base de données ou fédérations serait un scénario pour cette approche. Ont utilise plusieurs bases de données et achemine les données différentes pour chaque base de données. Si chaque lot petite est destiné à une autre base de données, puis en effectuant les opérations en parallèle peut être plus efficace. Toutefois, le gain de performances n’est pas suffisamment significatif à utiliser comme base pour une décision à utiliser ont de base de données dans votre solution.

Dans certains modèles, l’exécution des lots plus petits parallèle peut entraîner débit amélioré des requêtes dans un système en charge. Dans ce cas, même s’il est plus rapide de traiter un seul lot plus grand, traitement de plusieurs lots en parallèle peut être plus efficace.

Si vous n’utilisez pas d’exécution en parallèle, vous pouvez contrôler le nombre maximal de threads de travail. Un plus petit nombre peut entraîner moins en conflit et une durée d’exécution plus rapide. Envisagez également la charge supplémentaire placé sur la base de données cible à la fois dans des connexions et des transactions.

### <a name="related-performance-factors"></a>Facteurs de performances connexes
Orientation par défaut sur les performances de base de données affecte également le traitement par lots. Par exemple, insérez les performances sont réduites pour les tables qui ont une grande clé primaire ou nombreux index ordonnés.

Si les paramètres table utilisent une procédure stockée, vous pouvez utiliser la commande **SET NOCOUNT ON** au début de la procédure. Cette instruction supprime le retour du nombre de lignes concernées dans la procédure. Toutefois, au cours de nos tests, l’utilisation de **SET NOCOUNT ON** n’a aucun effet soit baisse des performances. La procédure d’essai stockée a été simple avec une seule **Insérer** commande à partir du paramètre évaluée dans une table. Il est possible que les procédures stockées plus complexes pourraient bénéficier de cette déclaration. Mais ne pensez pas qu’ajout **SET NOCOUNT ON** à votre procédure stockée automatiquement améliore les performances. Pour mieux comprendre l’effet, testez votre procédure stockée avec et sans l’instruction **SET NOCOUNT ON** .

## <a name="batching-scenarios"></a>Le traitement par lots de scénarios
Les sections suivantes décrivent comment utiliser des paramètres table dans trois scénarios d’application. Le premier scénario montre comment la mémoire tampon et le traitement par lots peuvent fonctionner ensemble. Le second scénario améliore les performances en effectuant des opérations de maître / détail dans un appel de procédure stockée unique. Le dernier scénario montre comment utiliser des paramètres table dans une opération « UPSERT ».

### <a name="buffering"></a>Mise en mémoire tampon
Bien qu’il existe certains scénarios qui sont candidats évidente pour le traitement par lots, il existe de nombreux scénarios qui pourraient tirer parti de traitement par lots d’en retard de traitement. Toutefois, le retard de traitement comporte également un risque plus élevé que les données sont perdues en cas d’erreur inattendue. Il est important de comprendre ce risque et à prendre en compte les conséquences.

Par exemple, considérez une application web qui effectue le suivi de l’historique de navigation de chaque utilisateur. À chaque demande de page, l’application peut appeler une base de données pour enregistrer le mode page de l’utilisateur. Mais une augmentation des performances et extensibilité élevées possible par la mémoire tampon d’activités de navigation leurs et puis envoyer ces données à la base de données dans des lots. Vous pouvez déclencher la mise à jour de base de données en temps écoulé et/ou la taille de la mémoire tampon. Par exemple, une règle peut spécifier que le lot doit être traité après 20 secondes ou lorsque la mémoire tampon atteint 1000 éléments.

L’exemple suivant utilise [Les Extensions réactive - réception](https://msdn.microsoft.com/data/gg577609) de traiter les événements dans la mémoire tampon déclenchés par une classe surveillance. Lorsque la mémoire tampon est ou une durée arrive à expiration, le lot de données de l’utilisateur est envoyé à la base de données avec un paramètre de table.

La classe NavHistoryData suivante modèles de détails de navigation de l’utilisateur. Il contient des informations de base telles que l’identificateur d’utilisateur, l’URL d’accès et le temps d’accès.

    public class NavHistoryData
    {
        public NavHistoryData(int userId, string url, DateTime accessTime)
        { UserId = userId; URL = url; AccessTime = accessTime; }
        public int UserId { get; set; }
        public string URL { get; set; }
        public DateTime AccessTime { get; set; }
    }

La classe NavHistoryDataMonitor est responsable de la mise en mémoire tampon les données de navigation utilisateur à la base de données. Il contient une méthode, RecordUserNavigationEntry, qui répond en déclenchant un événement **OnAdded** . Le code suivant illustre la logique constructeur qui utilise réception pour créer une collection de sites visible en fonction de l’événement. Il puis permet de s’abonner à cette collection visible avec la méthode de mémoire tampon. La surcharge indique que la mémoire tampon doit être envoyé toutes les 20 secondes ou 1000 entrées.

    public NavHistoryDataMonitor()
    {
        var observableData =
            Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
        observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
    }

Le Gestionnaire de tous les éléments dans la mémoire tampon convertit en un type évaluée dans une table, puis passe ce type à une procédure stockée qui traite le lot. Le code suivant illustre la définition complète pour la NavHistoryDataEventArgs et les classes NavHistoryDataMonitor.

    public class NavHistoryDataEventArgs : System.EventArgs
    {
        public NavHistoryDataEventArgs(NavHistoryData data) { Data = data; }
        public NavHistoryData Data { get; set; }
    }
    
    public class NavHistoryDataMonitor
    {
        public event EventHandler<NavHistoryDataEventArgs> OnAdded;
    
        public NavHistoryDataMonitor()
        {
            var observableData =
                Observable.FromEventPattern<NavHistoryDataEventArgs>(this, "OnAdded");
    
            observableData.Buffer(TimeSpan.FromSeconds(20), 1000).Subscribe(Handler);           
        }
    
        public void RecordUserNavigationEntry(NavHistoryData data)
        {    
            if (OnAdded != null)
                OnAdded(this, new NavHistoryDataEventArgs(data));
        }
    
        protected void Handler(IList<EventPattern<NavHistoryDataEventArgs>> items)
        {
            DataTable navHistoryBatch = new DataTable("NavigationHistoryBatch");
            navHistoryBatch.Columns.Add("UserId", typeof(int));
            navHistoryBatch.Columns.Add("URL", typeof(string));
            navHistoryBatch.Columns.Add("AccessTime", typeof(DateTime));
            foreach (EventPattern<NavHistoryDataEventArgs> item in items)
            {
                NavHistoryData data = item.EventArgs.Data;
                navHistoryBatch.Rows.Add(data.UserId, data.URL, data.AccessTime);
            }
    
            using (SqlConnection connection = new SqlConnection(CloudConfigurationManager.GetSetting("Sql.ConnectionString")))
            {
                connection.Open();
    
                SqlCommand cmd = new SqlCommand("sp_RecordUserNavigation", connection);
                cmd.CommandType = CommandType.StoredProcedure;
    
                cmd.Parameters.Add(
                    new SqlParameter()
                    {
                        ParameterName = "@NavHistoryBatch",
                        SqlDbType = SqlDbType.Structured,
                        TypeName = "NavigationHistoryTableType",
                        Value = navHistoryBatch,
                    });
    
                cmd.ExecuteNonQuery();
            }
        }
    }

Pour utiliser cette classe mise en mémoire tampon, l’application crée un objet NavHistoryDataMonitor statique. Chaque fois qu’un utilisateur accède à une page, l’application appelle la méthode NavHistoryDataMonitor.RecordUserNavigationEntry. La logique de mise en mémoire tampon continue pour prendre en charge d’envoyer ces entrées à la base de données dans des lots.

### <a name="master-detail"></a>Maître / détail
Paramètres table sont utiles pour les scénarios insertion simples. Toutefois, il peut être plus difficile à insertions lot impliquant plusieurs tables. Le scénario « maître/détail » constitue un bon exemple. La table principale identifie l’entité principale. Une ou plusieurs tables de détail stockent davantage de données sur l’entité. Dans ce scénario, des relations de clé étrangères mettent en œuvre la relation de détails à une entité maître unique. Envisagez d’une version simplifiée d’une table PurchaseOrder et sa table OrderDetail associée. Le Transact-SQL suivante crée la table PurchaseOrder avec quatre colonnes : OrderID, OrderDate, CustomerID et l’état.

    CREATE TABLE [dbo].[PurchaseOrder](
    [OrderID] [int] IDENTITY(1,1) NOT NULL,
    [OrderDate] [datetime] NOT NULL,
    [CustomerID] [int] NOT NULL,
    [Status] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrder] 
    PRIMARY KEY CLUSTERED ( [OrderID] ASC ))

Chaque commande contenait une ou plusieurs des achats de produits. Ces informations sont incluses dans la table PurchaseOrderDetail. Le Transact-SQL suivante crée la table PurchaseOrderDetail avec cinq colonnes : OrderID, OrderDetailID, ProductID, UnitPrice et OrderQty.

    CREATE TABLE [dbo].[PurchaseOrderDetail](
    [OrderID] [int] NOT NULL,
    [OrderDetailID] [int] IDENTITY(1,1) NOT NULL,
    [ProductID] [int] NOT NULL,
    [UnitPrice] [money] NULL,
    [OrderQty] [smallint] NULL,
     CONSTRAINT [PrimaryKey_PurchaseOrderDetail] PRIMARY KEY CLUSTERED 
    ( [OrderID] ASC, [OrderDetailID] ASC ))

La colonne OrderID de la table PurchaseOrderDetail doit faire référence à une commande à partir de la table PurchaseOrder. La définition d’une clé étrangère suivante applique cette contrainte.

    ALTER TABLE [dbo].[PurchaseOrderDetail]  WITH CHECK ADD 
    CONSTRAINT [FK_OrderID_PurchaseOrder] FOREIGN KEY([OrderID])
    REFERENCES [dbo].[PurchaseOrder] ([OrderID])

Pour pouvoir utiliser les paramètres table, vous devez disposer un type de table définie par l’utilisateur pour chaque table cible.

    CREATE TYPE PurchaseOrderTableType AS TABLE 
    ( OrderID INT,
      OrderDate DATETIME,
      CustomerID INT,
      Status NVARCHAR(50) );
    GO
    
    CREATE TYPE PurchaseOrderDetailTableType AS TABLE 
    ( OrderID INT,
      ProductID INT,
      UnitPrice MONEY,
      OrderQty SMALLINT );
    GO

Définissez ensuite une procédure stockée qui accepte les tables de ces types. Cette procédure permet à une application pour un ensemble de commandes et des détails de la commande en un seul appel en lot localement. Transact-SQL suivant fournit la déclaration de la procédure stockée complète pour que cet exemple de commande achat.

    CREATE PROCEDURE sp_InsertOrdersBatch (
    @orders as PurchaseOrderTableType READONLY,
    @details as PurchaseOrderDetailTableType READONLY )
    AS
    SET NOCOUNT ON;
    
    -- Table that connects the order identifiers in the @orders
    -- table with the actual order identifiers in the PurchaseOrder table
    DECLARE @IdentityLink AS TABLE ( 
    SubmittedKey int, 
    ActualKey int, 
    RowNumber int identity(1,1)
    );
     
          -- Add new orders to the PurchaseOrder table, storing the actual
    -- order identifiers in the @IdentityLink table   
    INSERT INTO PurchaseOrder ([OrderDate], [CustomerID], [Status])
    OUTPUT inserted.OrderID INTO @IdentityLink (ActualKey)
    SELECT [OrderDate], [CustomerID], [Status] FROM @orders ORDER BY OrderID;
    
    -- Match the passed-in order identifiers with the actual identifiers
    -- and complete the @IdentityLink table for use with inserting the details
    WITH OrderedRows As (
    SELECT OrderID, ROW_NUMBER () OVER (ORDER BY OrderID) As RowNumber 
    FROM @orders
    )
    UPDATE @IdentityLink SET SubmittedKey = M.OrderID
    FROM @IdentityLink L JOIN OrderedRows M ON L.RowNumber = M.RowNumber;
    
    -- Insert the order details into the PurchaseOrderDetail table, 
          -- using the actual order identifiers of the master table, PurchaseOrder
    INSERT INTO PurchaseOrderDetail (
    [OrderID],
    [ProductID],
    [UnitPrice],
    [OrderQty] )
    SELECT L.ActualKey, D.ProductID, D.UnitPrice, D.OrderQty
    FROM @details D
    JOIN @IdentityLink L ON L.SubmittedKey = D.OrderID;
    GO

Dans cet exemple, défini localement @IdentityLink table stocke les valeurs OrderID réels à partir des lignes nouvellement insérées. Ces identificateurs de commande sont différents des valeurs OrderID temporaires de la @orders et @details paramètres table. Pour cette raison, la @IdentityLink table s’y connecte les valeurs OrderID la @orders paramètre aux valeurs OrderID réels pour les nouvelles lignes dans la table PurchaseOrder. Une fois cette étape, la @IdentityLink tableau peut faciliter l’insertion de détails de la commande avec la colonne OrderID réelle qui résout la contrainte de clé étrangère.

Cette procédure stockée peut être utilisée à partir du code ou à partir d’autres appels Transact-SQL. Consultez la section Paramètres table de ce livre blanc pour obtenir un exemple de code. Le Transact-SQL suivante montre comment appeler la sp_InsertOrdersBatch.

    declare @orders as PurchaseOrderTableType
    declare @details as PurchaseOrderDetailTableType
    
    INSERT @orders 
    ([OrderID], [OrderDate], [CustomerID], [Status])
    VALUES(1, '1/1/2013', 1125, 'Complete'),
    (2, '1/13/2013', 348, 'Processing'),
    (3, '1/12/2013', 2504, 'Shipped')
    
    INSERT @details
    ([OrderID], [ProductID], [UnitPrice], [OrderQty])
    VALUES(1, 10, $11.50, 1),
    (1, 12, $1.58, 1),
    (2, 23, $2.57, 2),
    (3, 4, $10.00, 1)
    
    exec sp_InsertOrdersBatch @orders, @details

Cette solution permet de chaque lot à utiliser un jeu de valeurs OrderID qui commencent à 1. Ces valeurs OrderID temporaires décrivent les relations dans le lot, mais les valeurs réelles OrderID sont déterminées au moment de l’opération d’insertion. Vous pouvez exécuter à plusieurs reprises les mêmes instructions dans l’exemple précédent et générer des commandes uniques dans la base de données. Pour cette raison, vous pouvez ajouter plus logique de base de données ou de code qui empêche les commandes en double lors de l’utilisation de cette technique de traitement par lots.

Cet exemple montre que des opérations de base de données encore plus complexes, tels que les opérations maître / détail, peuvent être regroupés par lots à l’aide des paramètres table.

### <a name="upsert"></a>UPSERT
Un autre scénario le traitement par lots implique simultanément mise à jour de l’insertion de nouvelles lignes et de lignes existantes. Cette opération est parfois appelée une opération « UPSERT » (mise à jour + INSER). Au lieu d’effectuer des appels séparés pour insérer et mettre à jour, l’instruction de fusion est adaptée à cette tâche. L’instruction de fusion et peut effectuer les deux insertion et mettre à jour les opérations dans un seul appel.

Valeurs tabulaires paramètres peuvent être utilisés avec l’instruction de fusion pour effectuer des mises à jour et insertions. Par exemple, supposons une table employés simplifiée qui contient les colonnes suivantes : EmployeeID, prénom, nom, SocialSecurityNumber :

    CREATE TABLE [dbo].[Employee](
    [EmployeeID] [int] IDENTITY(1,1) NOT NULL,
    [FirstName] [nvarchar](50) NOT NULL,
    [LastName] [nvarchar](50) NOT NULL,
    [SocialSecurityNumber] [nvarchar](50) NOT NULL,
     CONSTRAINT [PrimaryKey_Employee] PRIMARY KEY CLUSTERED 
    ([EmployeeID] ASC ))
 
Dans cet exemple, vous pouvez utiliser le fait que la SocialSecurityNumber est unique pour effectuer une fusion de plusieurs employés. Commencez par créer le type de table définie par l’utilisateur :

    CREATE TYPE EmployeeTableType AS TABLE 
    ( Employee_ID INT,
      FirstName NVARCHAR(50),
      LastName NVARCHAR(50),
      SocialSecurityNumber NVARCHAR(50) );
    GO

Ensuite, créez une procédure stockée ou écrire du code qui utilise l’instruction de fusion pour effectuer la mise à jour et l’insérer. L’exemple suivant utilise l’instruction fusion sur un paramètre table, @employees, de type EmployeeTableType. Le contenu de la @employees table ne sont pas indiqués ici.

    MERGE Employee AS target
    USING (SELECT [FirstName], [LastName], [SocialSecurityNumber] FROM @employees) 
    AS source ([FirstName], [LastName], [SocialSecurityNumber])
    ON (target.[SocialSecurityNumber] = source.[SocialSecurityNumber])
    WHEN MATCHED THEN 
    UPDATE SET
    target.FirstName = source.FirstName, 
    target.LastName = source.LastName
    WHEN NOT MATCHED THEN
       INSERT ([FirstName], [LastName], [SocialSecurityNumber])
       VALUES (source.[FirstName], source.[LastName], source.[SocialSecurityNumber]);

Pour plus d’informations, consultez la documentation et exemples pour l’instruction de fusion. Bien que le même travail peut être effectué en plusieurs étapes stockées appel de procédure avec séparez insertion et opérations de mise à jour, l’instruction de fusion est plus efficace. Code de base de données peut également construire appels Transact-SQL qui utilisent la déclaration de fusion directement, sans exiger des deux appels de base de données pour insérer et mettre à jour.

## <a name="recommendation-summary"></a>Recommandation résumée

La liste suivante fournit un récapitulatif des recommandations le traitement par lots mentionnés dans cette rubrique :

- Utilisez la mémoire tampon et le traitement par lots pour améliorer les performances et extensibilité élevées des applications de base de données SQL.
- Comprendre les compromis entre le traitement par lots/mise en mémoire tampon et la résilience. Lors d’une défaillance de rôle, les risques de perdre un lot non transformé de données critiques peuvent emporter sur les performances accrues offertes le traitement par lots.
- Essayez de conserver toutes les appels vers la base de données dans un centre de données unique pour réduire la latence.
- Si vous choisissez une technique le traitement par lots, paramètres table offrent les meilleures performances et flexibilité.
- Pour de meilleures performances d’insertion, suivez ces instructions générales, mais votre scénario de test :
    - Pour les lignes < 100, utilisez une commande d’insertion paramétrée unique.
    - Pour les lignes < 1000, utilisez les paramètres table.
    - Pour > = 1000 lignes, utilisez SqlBulkCopy.
- Pour mettre à jour et supprimer des opérations, utiliser des paramètres table avec une logique de procédure stockée qui détermine le bon fonctionnement sur chaque ligne dans le paramètre de table.
- Instructions de taille de lot :
    - Utilisez la plus grande taille des lots représentatives pour votre application et les besoins de l’entreprise.
    - Équilibrer le gain de performances des lots de grande taille avec les risques d’échecs temporaires ou grave. Quelle est la conséquence de tentatives ou perte de données dans le lot ? 
    - Testez la plus grande taille du lot pour vérifier que base de données SQL rejeter pas.
    - Créer les paramètres de configuration qui contrôle le traitement par lots, tels que la taille du lot ou la fenêtre Délai mise en mémoire tampon. Ces paramètres garantissent la flexibilité. Vous pouvez modifier le comportement de traitement de production sans redéployez le service cloud.
- Évitez d’exécution en parallèle des lots qui fonctionnent sur une seule table dans une base de données. Si vous choisissez de ne divisez un seul lot entre plusieurs threads de travail, des tests pour déterminer le nombre idéal de threads. Après un seuil hauteur, davantage de threads sera réduire les performances plutôt que qu’elle.
- Envisagez de mise en mémoire tampon sur taille et l’heure comme un moyen d’implémentation le traitement par lots pour d’autres scénarios.

## <a name="next-steps"></a>Étapes suivantes

Cet article se concentre sur la conception de base de données et de codage techniques liées au traitement par lots peuvent améliorer vos performances de l’application et extensibilité élevées. Mais il s’agit d’une facteur dans votre stratégie globale. Pour les autres façons d’améliorer les performances et extensibilité élevées, voir [conseils pour les performances de base de données SQL Azure des bases de données unique](sql-database-performance-guidance.md) et [Considérations prix et de performances d’un pool élastique de base de données](sql-database-elastic-pool-guidance.md).
