<properties
   pageTitle="Développer des scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio | Azure"
   description="Découvrez comment installer Lake Data Tools pour Visual Studio, le développement et les scripts de test U-SQL. "
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="tutorial-get-started-with-azure-data-lake-analytics-u-sql-language"></a>Didacticiel : Prise en main langue données Lake Analytique U-SQL Azure

U-SQL est une langue qui réunit les avantages de SQL avec la puissance expressif de votre propre code pour traiter toutes les données à n’importe quelle échelle. Fonctionnalité de requête distribuée scalable de U-SQL vous permet d’analyser efficacement les données dans le magasin et entre banques relationnelles comme base de données SQL Azure.  Il vous permet de processus de données non structurées en appliquant schéma sur lire, insérer une logique personnalisée et UDF et inclut l’extensibilité pour permettre à grains fins optimiser contrôler comment exécuter à l’échelle. Pour en savoir plus sur la philosophie de conception derrière U-SQL, reportez-vous à ce [billet de blog Visual Studio](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Il existe certaines différences à partir de SQL ANSI ou T-SQL. Par exemple, ses mots-clés telles que SELECT doivent être en majuscules.

Il s’agit de langue système et expression de type à l’intérieur des clauses select, où se trouvent prédicats etc. dans c#.
Cela signifie que les types de données sont les types c# et les types de données utilisent la sémantique c# NULL et les opérations de comparaison à l’intérieur d’un prédicat suivent syntaxe c# (par exemple, un == « foo »).  Cela signifie aussi, que les valeurs sont des objets .NET complètes, ce qui vous permet d’utiliser facilement n’importe quelle méthode pour agir sur l’objet (par exemple, « f o o o ». Split(' ')).

Pour plus d’informations, voir [Informations de référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

###<a name="prerequisites"></a>Conditions préalables

Vous devez effectuer [didacticiel : développer les scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).

Dans le didacticiel, vous avez exécuté une tâche de données Lake Analytique avec le script U SQL suivant :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();

Ce script n’a pas toutes les étapes de transformation. Il lit à partir du fichier source appelé **SearchLog.tsv**, il schematizes et renvoie l’ensemble de lignes dans un fichier nommé **SearchLog-premier-u-sql.csv**.

Notez le point d’interrogation en regard du type de données du champ de durée. Cela signifie que le champ durée peut être null.

Certains mots clés dans le script et concepts :

- **Variables du jeu de lignes**: chaque expression de requête produisant un ensemble de lignes peut être affectée à une variable. U-SQL suit le modèle d’appellation variable T-SQL, par exemple, **@searchlog** dans le script.
    Remarque l’affectation ne force pas l’exécution. Il simplement de noms l’expression et vous donne la possibilité de voir des expressions plus complexes.
- **Extraire** vous donne la possibilité de définir un schéma lors de la lecture. Le schéma est spécifié par un nom de colonne et paire nom type c# par colonne. Il utilise un dite **extraction**, par exemple, pour extraire des fichiers tsv **Extractors.Tsv()** . Vous pouvez développer extracteurs personnalisés.
- **Sortie** prend un ensemble de lignes et les sérialise. La Outputters.Csv() de sortie un fichier séparé dans l’emplacement spécifié. Vous pouvez également développer Outputters personnalisés.
- Notez les chemins de deux accès chemins d’accès relatifs. Vous pouvez également utiliser les chemins d’accès absolus.  Par exemple

        adl://<ADLStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Vous devez utiliser le chemin d’accès absolu pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe pour les fichiers stockés dans un compte de stockage Azure lié est la suivante :

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

    >[AZURE.NOTE] Conteneur Blob Azure disposant d’autorisations d’accès conteneurs public ou des objets BLOB publics ne sont actuellement pas pris en charge.

## <a name="use-scalar-variables"></a>Utiliser des variables scalaires

Vous pouvez utiliser des variables scalaires ainsi que pour simplifier la maintenance de votre script. Le script U-SQL précédente peut également être écrites comme suit :

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Transformer des ensembles de lignes

Permet de **Sélectionner** pour transformer les ensembles de lignes :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

La clause WHERE utilise [c# expression booléenne](https://msdn.microsoft.com/library/6a71f45d.aspx). Vous pouvez utiliser le langage d’expression c# pour effectuer vos propres expressions et fonctions. Vous pouvez même effectuer un filtrage plus complexes en combinant les avec conjonctions logiques (et) et disjunctions (ORs).

Le script suivant utilise la méthode DateTime.Parse() et conjonction.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datatime.csv"
        USING Outputters.Csv();

Notez que la deuxième requête fonctionne sur le résultat de l’ensemble de lignes première et donc le résultat est constituée de deux filtres. Vous pouvez également réutiliser un nom de la variable et les noms sont limitées lexicalement.

## <a name="aggregate-rowsets"></a>Jeux de lignes agrégation

U-SQL vous propose les familiers **ORDER BY**, **GROUP BY** et les agrégations.

La requête suivante recherche la durée totale par région et puis renvoie la partie supérieure à 5 durées dans l’ordre.

Jeux de lignes U-SQL ne pas conserve leur ordre de la requête suivante. Par conséquent, pour trier une sortie, vous devez ajouter ORDER BY à la déclaration de sortie, comme indiqué ci-dessous :

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();
    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Clause SQL-U ORDER BY doit être combinés avec la clause récupérer dans une expression SELECT.

Clause HAVING U-SQL peut servir à restreindre la sortie aux groupes qui répondent à la condition HAVING :

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

## <a name="join-data"></a>Joindre des données

U-SQL fournit des opérateurs de jointure courantes telles que INNER JOIN, jointure externe gauche/droite/plein et semi-transparent jointure, pour joindre non seulement les tables, mais les ensembles de lignes (même ceux produits à partir de fichiers).

Le script suivant joint la searchlog avec un journal d’impression de publication et permet des publications associées à la chaîne de requête pour une date donnée.

    @adlog =
        EXTRACT UserId int,
                Ad string,
                Clicked int
        FROM "/Samples/Data/AdsLog.tsv"
        USING Extractors.Tsv();

    @join =
        SELECT a.Ad, s.Query, s.Start AS Date
        FROM @adlog AS a JOIN <insert your DB name>.dbo.SearchLog1 AS s
                        ON a.UserId == s.UserId
        WHERE a.Clicked == 1;

    OUTPUT @join   
        TO "/output/Searchlog-join.csv"
        USING Outputters.Csv();


U-SQL prend uniquement en charge la syntaxe de la jointure compatible ANSI : prédicat Rowset1 jointure Rowset2 activé. La syntaxe de l’ancien d’à partir de Rowset1, Rowset2 où prédicat n’est pas pris en charge.
Le prédicat dans une jointure est une jointure l’égalité et sans l’expression. Si vous souhaitez utiliser une expression, ajoutez-le à la clause select d’un jeu de lignes précédent. Si vous voulez effectuer une autre comparaison, vous pouvez le déplacer dans la clause WHERE.


## <a name="create-databases-table-valued-functions-views-and-tables"></a>Créer des bases de données, des fonctions table, des vues et des tables

U-SQL vous permet d’utiliser des données dans le contexte d’une base de données et le schéma. Si vous n’êtes pas obligé toujours lire ou écrire sur des fichiers.

Chaque script U-SQL s’exécute avec une base de données par défaut (maître) et un schéma par défaut (DBO) comme son contexte par défaut. Vous pouvez créer votre propre base de données et/ou le schéma. Pour modifier le contexte, utilisez l’instruction **USE** pour modifier le contexte.


### <a name="create-a-table-valued-function-tvf"></a>Créer une fonction de table (table)

Dans le script U-SQL précédent, vous répétées avec extrait de lecture à partir du même fichier source. Fonction table U-SQL vous permet d’encapsuler les données pour une utilisation ultérieure.   

Le script suivant crée une table appelée *Searchlog()* dans la base de données par défaut et le schéma :

    DROP FUNCTION IF EXISTS Searchlog;

    CREATE FUNCTION Searchlog()
    RETURNS @searchlog TABLE
    (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
    )
    AS BEGIN
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();
    RETURN;
    END;

Le script suivant vous montre comment utiliser la fonction table définie dans le script précédent :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM Searchlog() AS S
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/SerachLog-use-tvf.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-views"></a>Créer des affichages

Si vous disposez d’une expression de requête que vous voulez extraire et ne souhaitez pas paramétrer, vous pouvez créer une vue au lieu d’une fonction table.

Le script suivant crée une vue appelée *SearchlogView* dans la base de données par défaut et le schéma :

    DROP VIEW IF EXISTS SearchlogView;

    CREATE VIEW SearchlogView AS  
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

Le script suivant montre comment utiliser l’affichage défini :

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchlogView
    GROUP BY Region
    HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-use-view.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

### <a name="create-tables"></a>Créer des tables

Semblable à une base de données relationnelles, U-SQL vous permet de créer un tableau avec un schéma prédéfini ou créer une table et déduire le schéma de la requête qui remplit la table (également appelé CREATE TABLE AS SELECT ou SACT).

Le script suivant créer une base de données et deux tables :

    DROP DATABASE IF EXISTS SearchLogDb;
    CREATE DATABASE SeachLogDb
    USE DATABASE SearchLogDb;

    DROP TABLE IF EXISTS SearchLog1;
    DROP TABLE IF EXISTS SearchLog2;

    CREATE TABLE SearchLog1 (
                UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string,

                INDEX sl_idx CLUSTERED (UserId ASC)
                    PARTITIONED BY HASH (UserId)
    );

    INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

    CREATE TABLE SearchLog2(
        INDEX sl_idx CLUSTERED (UserId ASC)
                PARTITIONED BY HASH (UserId)
    ) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here


### <a name="query-tables"></a>Tables de requête

Vous pouvez interroger les tables (créés dans le script précédent) de la même façon en tant que requête vous sur les fichiers de données. Au lieu de créer un jeu de lignes à l’aide d’extraction, vous maintenant pouvez simplement faire référence au nom de table.

Le script de transformation que vous avez déjà utilisé a été modifié pour lire à partir des tables :

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM SearchLogDb.dbo.SearchLog2
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @res
        TO "/output/Searchlog-query-table.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Notez qu’actuellement impossible d’exécuter une instruction SELECT sur une table dans le même script que le script dans lequel vous créez cette table.


##<a name="conclusion"></a>Conclusion

Ce qui est décrit dans le didacticiel est uniquement une petite partie d’U-SQL. En raison de l’étendue de ce didacticiel, elle ne peut pas couvre tous les éléments, tels que :

- Utiliser CROSS APPLY décompresser des parties de cartes, des tableaux et des chaînes dans des lignes.
- Faire fonctionner partitionnées jeux de données (jeux de fichiers et les tables partitionnées).
- Développer des opérateurs définis par l’utilisateur telles qu’extracteurs, outputters, processeurs et les agrégations de définies par l’utilisateur en c#.
- Utilisez les fonctions multifenêtre U-SQL.
- Gérer le code SQL-U avec des procédures stockées, des fonctions table et des affichages.
- Exécuter du code personnalisé arbitraire sur vos nœuds de traitement.
- Se connecter aux bases de données SQL Azure et aux requêtes federate entre eux et vos données U-SQL et Azure données Lake.

## <a name="see-also"></a>Voir aussi

- [Vue d’ensemble de Microsoft Azure données Lake Analytique](data-lake-analytics-overview.md)
- [Développer des scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [À l’aide de fonctions de fenêtre U-SQL pour les travaux Azure données Lake Analytique](data-lake-analytics-use-window-functions.md)
- [Surveiller et résoudre les problèmes de travaux Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="let-us-know-what-you-think"></a>Dites-nous ce que vous pensez

- [Envoyez une demande de fonctionnalité](http://aka.ms/adlafeedback)
- [Obtenir de l’aide dans les forums](http://aka.ms/adlaforums)
- [Fournir des commentaires sur U-SQL](http://aka.ms/usqldiscuss)
