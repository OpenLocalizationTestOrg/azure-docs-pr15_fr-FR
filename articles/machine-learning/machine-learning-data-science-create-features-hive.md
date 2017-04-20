<properties
    pageTitle="Créer des fonctions pour les données dans un cluster Hadoop à l’aide de requêtes Hive | Microsoft Azure"
    description="Exemples de requêtes Hive qui génèrent des fonctionnalités dans les données stockées dans un cluster Azure HDInsight Hadoop."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/19/2016"
    ms.author="hangzh;bradsev" />


#<a name="create-features-for-data-in-an-hadoop-cluster-using-hive-queries"></a>Créer des fonctions pour les données dans un cluster Hadoop à l’aide de requêtes Hive

Ce document montre comment créer des fonctions pour les données stockées dans un cluster Azure HDInsight Hadoop à l’aide de requêtes Hive. Ces requêtes Hive utilisent incorporé ruche utilisateur fonctions définies par le (UDF), les scripts dont vous sont proposés.

Opérations nécessaires pour créer des fonctions peuvent être quantité importante de mémoire. Les performances des requêtes Hive devient critique dans ce cas et peuvent être améliorée par certains paramètres de réglage. Le réglage de ces paramètres est décrite dans la section finale.

Exemples de requêtes qui sont présentées sont spécifiques aux [Données de voyage NYC Taxi](http://chriswhong.com/open-data/foil_nyc_taxi/) scénarios sont également disponibles dans [référentiel Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Ces requêtes déjà ont schéma de données spécifiée et êtes prêts à être soumis à exécuter. Dans la section finale, les paramètres que les utilisateurs peuvent régler afin que les performances des requêtes Hive peuvent être améliorées sont également présentés.

[AZURE.INCLUDE [cap-create-features-data-selector](../../includes/cap-create-features-selector.md)]Cette **menu** fournit des liens vers des rubriques qui décrivent comment créer des fonctions pour les données dans différents environnements. Cette tâche est une étape de l' [Équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="prerequisites"></a>Conditions préalables
Cet article suppose que vous avez :

* Créer un compte de stockage Azure. Si vous avez besoin d’instructions, voir [créer un compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account)
* Mise en service un cluster Hadoop personnalisée avec le service HDInsight.  Si vous avez besoin d’instructions, voir [Personnaliser Azure HDInsight Hadoop Clusters pour Analytique avancée](machine-learning-data-science-customize-hadoop-cluster.md).
* Les données ont été téléchargées vers tables Hive dans Azure HDInsight Hadoop clusters. Si ce n’est pas le cas, appliquez la [créer et charger les données aux tables Hive](machine-learning-data-science-move-hive-tables.md) pour télécharger des données aux tables Hive tout d’abord.
* Accès à distance activés pour le cluster. Si vous avez besoin d’instructions, voir [le nœud de tête de Hadoop Cluster l’accès](machine-learning-data-science-customize-hadoop-cluster.md#headnode).


##<a name="hive-featureengineering"></a>Génération de fonctionnalité

Plusieurs exemples de l’une des manières dans lequel les fonctionnalités peuvent générer à l’aide de requêtes Hive sont décrites dans cette section. Une fois que vous avez généré des fonctionnalités supplémentaires, vous pouvez les ajouter en tant que colonnes à la table existante ou créer une table avec les autres fonctionnalités de clé primaire, qui peut être joints avec la table d’origine. Voici les exemples présentés :

1. [Fréquence en fonction de génération de fonctionnalité](#hive-frequencyfeature)
2. [Risques de Variables par catégorie de Classification binaire](#hive-riskfeature)
3. [Extraire des fonctionnalités de Datetime Field](#hive-datefeatures)
4. [Extraire les fonctionnalités d’un champ de texte](#hive-textfeatures)
5. [Calculer la distance entre les coordonnées GPS](#hive-gpsdistance)

###<a name="hive-frequencyfeature"></a>Fréquence en fonction de génération de fonctionnalité

Il est souvent utile calculer la fréquence des niveaux d’une variable par catégorie, ou la fréquence de certaines combinaisons de niveaux à partir de plusieurs variables par catégorie. Les utilisateurs peuvent permet de calculer ces fréquence le script suivant :

        select
            a.<column_name1>, a.<column_name2>, a.sub_count/sum(a.sub_count) over () as frequency
        from
        (
            select
                <column_name1>,<column_name2>, count(*) as sub_count
            from <databasename>.<tablename> group by <column_name1>, <column_name2>
        )a
        order by frequency desc;


###<a name="hive-riskfeature"></a>Risques de Variables par catégorie de Classification binaire

Lors de la classification binaire, nous avons besoin convertir des variables par catégorie non numérique en fonctionnalités numériques lorsque les modèles utilisés uniquement tirer fonctionnalités numériques. Pour cela en remplaçant chaque niveau non numérique par un risque numérique. Dans cette section, nous affichons certaines requêtes Hive génériques qui permettent de calculer les valeurs de risque (journal chances) d’une variable par catégorie.


        set smooth_param1=1;
        set smooth_param2=20;
        select
            <column_name1>,<column_name2>,
            ln((sum_target+${hiveconf:smooth_param1})/(record_count-sum_target+${hiveconf:smooth_param2}-${hiveconf:smooth_param1})) as risk
        from
            (
            select
                <column_nam1>, <column_name2>, sum(binary_target) as sum_target, sum(1) as record_count
            from
                (
                select
                    <column_name1>, <column_name2>, if(target_column>0,1,0) as binary_target
                from <databasename>.<tablename>
                )a
            group by <column_name1>, <column_name2>
            )b

Dans cet exemple, variables `smooth_param1` et `smooth_param2` sont définis pour atténuer les valeurs risque calculées à partir des données. Risques ont une plage comprise entre -Inf et Inf. Risques > égal à 0 indique que la probabilité que la cible est égale à 1 est supérieure à 0,5.

Après le risque tableau est calculée, les utilisateurs peuvent attribuer des valeurs de risque à une table en joignant avec la table risque. La requête de jonction Hive a été fournie dans la section précédente.

###<a name="hive-datefeatures"></a>Extraire des fonctionnalités de Datetime Fields

Hive est fourni avec un ensemble d’UDF pour le traitement des champs de date/heure. Dans Hive, le format de date/heure par défaut est « AAAA-MM-JJ 00:00:00 » (« 1970-01-01 12:21:32 » par exemple). Dans cette section, nous affichons exemples d’extraire le jour du mois, le mois à partir d’un champ date/heure et d’autres exemples qui convertir une chaîne de date/heure dans un format autre que le format par défaut pour une chaîne de date/heure par défaut mettre en forme.

        select day(<datetime field>), month(<datetime field>)
        from <databasename>.<tablename>;

Cette requête Hive suppose que le *& #60 ; champ date/heure >* se trouve dans le format de date/heure par défaut.

Si un champ date/heure n’est pas dans le format par défaut, vous devez convertir le champ date/heure en horodatage Unix tout d’abord, puis convertir l’horodatage Unix chaîne datetime qui se trouve dans le format par défaut. Lorsque la valeur datetime est par défaut mise en forme, les utilisateurs peuvent appliquer la valeur datetime incorporé UDF pour extraire les fonctionnalités.

        select from_unixtime(unix_timestamp(<datetime field>,'<pattern of the datetime field>'))
        from <databasename>.<tablename>;

Dans cette requête, si la *& #60 ; champ date/heure >* contient le modèle comme *26/03/2015 12:04:39*, la *' & #60 ; motif du champ date/heure >'* doit être `'MM/dd/yyyy HH:mm:ss'`. Pour tester, les utilisateurs peuvent exécuter

        select from_unixtime(unix_timestamp('05/15/2015 09:32:10','MM/dd/yyyy HH:mm:ss'))
        from hivesampletable limit 1;

*Hivesampletable* dans cette requête est préinstallé sur tous les clusters Azure HDInsight Hadoop par défaut lorsque les groupes sont mis en service.


###<a name="hive-textfeatures"></a>Extraire des fonctionnalités des champs de texte

Lorsque la table Hive comporte un champ de texte qui contient une chaîne de mots sont délimités par des espaces, la requête suivante extrait la longueur de la chaîne et le nombre de mots dans la chaîne.

        select length(<text field>) as str_len, size(split(<text field>,' ')) as word_num
        from <databasename>.<tablename>;

###<a name="hive-gpsdistance"></a>Calculer la distance entre les jeux de coordonnées GPS

La requête qui figure dans cette section peut être appliquée directement aux données NYC Taxi voyage. L’objectif de cette requête consiste à montrer comment appliquer des fonctions mathématiques incorporé dans Hive pour générer des fonctionnalités.

Les champs qui sont utilisées dans cette requête sont les coordonnées GPS des emplacements de collecte et cette chute, nommés *collecte\_longitude*, *collecte\_latitude*, *cette chute\_longitude*, et *cette chute\_latitude*. Les requêtes qui calculent la distance entre les coordonnées collecte et cette chute directe sont :

        set R=3959;
        set pi=radians(180);
        select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude,
            ${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
            *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
            *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
            /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
            +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
            pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
        from nyctaxi.trip
        where pickup_longitude between -90 and 0
        and pickup_latitude between 30 and 90
        and dropoff_longitude between -90 and 0
        and dropoff_latitude between 30 and 90
        limit 10;

Les équations mathématiques qui permettent de calculer la distance entre deux coordonnées GPS vous pouvez trouver sur le site de <a href="http://www.movable-type.co.uk/scripts/latlong.html" target="_blank">Type mobile Scripts</a> , créé par Peter Lapisu. Dans son Javascript, la fonction `toRad()` est simplement *lat_or_lon*pi/180*, qui convertit des degrés en radians. Ici, *lat_or_lon * est la latitude ou la longitude. Dans la mesure où Hive n’offre pas de la fonction `atan2`, mais fournit la fonction `atan`, la `atan2` fonction est implémentée par `atan` fonction dans la requête Hive ci-dessus à l’aide de la définition fournie dans <a href="http://en.wikipedia.org/wiki/Atan2" target="_blank">Wikipédia</a>.

![Créer l’espace de travail](./media/machine-learning-data-science-create-features-hive/atan2new.png)

Une liste complète des Hive UDF incorporés se trouve dans la section **Fonctions intégrées** <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-MathematicalFunctions" target="_blank">Apache ruche wiki</a>).  

## <a name="tuning"></a>Informations complémentaires : régler les paramètres de la ruche pour améliorer la vitesse de requête

Les paramètres par défaut de cluster Hive n’est peut-être pas adaptées pour les requêtes Hive et les données de traitement des requêtes. Dans cette section, nous aborderons certains paramètres utilisateurs pouvant optimiser les améliorer les performances des requêtes Hive. Les utilisateurs ont besoin d’ajouter le paramètre réglage des requêtes avant les requêtes de traitement de données.

1. **Espace de segment Java**: pour les requêtes impliquant participation à grands volumes de données ou le traitement des enregistrements longues, **segment espace insuffisant** figure parmi les erreurs courantes. Cela peut être ajustée en définissant des paramètres *mapreduce.map.java.opts* et *mapreduce.task.io.sort.mb* pour les valeurs souhaitées. Voici un exemple :

        set mapreduce.map.java.opts=-Xmx4096m;
        set mapreduce.task.io.sort.mb=-Xmx1024m;


    Ce paramètre affecte 4 Go de mémoire à l’espace de segment Java et également simplifie le tri plus efficaces en attribuant plus de mémoire pour qu’elle. Il est conseillé de lire avec ces affectations si n’importe quelle tâche Échec des erreurs se produisent liés à un espace segment.

2. **Taille du bloc DFS** : ce paramètre définit la plus petite unité de données qui stocke le système de fichiers. Par exemple, si la taille du bloc DFS est 128 Mo, puis toutes les données de taille inférieure à et jusqu'à 128 Mo sont stockée dans un seul bloc, lors de données qui sont supérieure à 128 Mo alloué blocs supplémentaires. En choisissant une taille très petit bloc provoque, grandes transparents dans Hadoop car le nœud name comporte traiter les demandes plus grand nombre pour rechercher le bloc pertinent concernant le fichier. Un paramètre recommandé lorsque relatifs à gigaoctets (ou plus) les données sont :

        set dfs.block.size=128m;

3. **Optimisation participer à une opération dans Hive** : tandis que les opérations de jointure dans le cadre de mappage/réduction sont généralement effectuées lors de la phase de réduire, parfois, payantes peuvent être obtenus en planification jointures lors de la phase de carte (également appelée « mapjoins »). Pour diriger Hive pour effectuer cette action chaque fois que possible, nous pouvons définir :

        set hive.auto.convert.join=true;

4. **Spécifier le nombre de mappeurs à Hive** : Hadoop tandis que permet à l’utilisateur définir le nombre de REDUCTEURS, le nombre de mappeurs est généralement ne pas être définies par l’utilisateur. Une astuce qui permet qu'une certaine de contrôle sur ce numéro consiste à choisir des variables Hadoop, *mapred.min.split.size* et *mapred.max.split.size* en tant que la taille de chaque tâche carte est déterminée par :

        num_maps = max(mapred.min.split.size, min(mapred.max.split.size, dfs.block.size))

    En règle générale, la valeur par défaut de *mapred.min.split.size* est égal à 0, celle de *mapred.max.split.size* est **Long.MAX** et celle de *dfs.block.size* 64 Mo. Comme nous pouvons voir, compte tenu de la taille des données, ces paramètres par « paramètre » de réglage les permet d’ajuster le nombre de mappeurs utilisé.

5. Quelques autres autres **options avancées** pour l’optimisation des performances Hive sont présentés ci-dessous. Ces vous permettent de définir la mémoire attribuée pour mapper et réduire les tâches et peuvent être utiles dans peaufinage performances. Gardez à l’esprit que le *mapreduce.reduce.memory.mb* ne peut pas être supérieur à la taille de la mémoire physique de chaque nœud de travail dans le cluster Hadoop.

        set mapreduce.map.memory.mb = 2048;
        set mapreduce.reduce.memory.mb=6144;
        set mapreduce.reduce.java.opts=-Xmx8192m;
        set mapred.reduce.tasks=128;
        set mapred.tasktracker.reduce.tasks.maximum=128;
