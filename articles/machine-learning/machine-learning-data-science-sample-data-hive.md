<properties
    pageTitle="Exemples de données dans les tables Azure HDInsight ruche | Microsoft Azure"
    description="Vers le bas échantillonnage des données dans des tableaux ruche Azure HDInsight (Hadopop)"
    services="machine-learning,hdinsight"
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

# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Exemples de données dans les tables Azure HDInsight ruche

Dans cet article, nous décrivent comment les données stockées dans des tables Azure HDInsight ruche à l’aide de requêtes Hive échantillons vers le bas. Aborde trois méthodes d’échantillonnage couramment utilisés :

* Sondage uniforme
* Sondage en groupes
* Échantillonnages stratifié

**Pourquoi des exemples de vos données ?**
Si le jeu de données que vous souhaitez analyser est volumineux, il est généralement judicieux de sélectionner les données pour réduire la taille de plus petite taille, mais représentatives et plus facile à gérer échantillons vers le bas. Cela facilite la compréhension des données, exploration et ingénierie de fonctionnalité. Son rôle dans le processus scientifique des données d’équipe consiste à activer prototypage rapide de la machine modèles d’apprentissage et de fonctions de traitement des données.

Le **menu** en dessous des liens vers des rubriques qui décrivent l’échantillon de données de différents environnements de stockage.

[AZURE.INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Cette tâche d’échantillonnage est une étape de l' [Équipe données scientifique processus (TDSP)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).


## <a name="how-to-submit-hive-queries"></a>Comment envoyer les requêtes Hive
Requêtes Hive peuvent être soumises à partir de la console de ligne de commande Hadoop sur le nœud principal du cluster Hadoop. Pour cela, connectez-vous au nœud de tête du cluster Hadoop, ouvrez la console de ligne de commande Hadoop et soumettre les requêtes Hive à partir de là. Pour obtenir des instructions sur l’envoi de requêtes Hive dans la console de ligne de commande Hadoop, Découvrez [comment envoyer les requêtes ruche](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="uniform"></a>Sondage uniforme
Sondage uniforme signifie que chaque ligne dans le jeu de données dispose d’autant de chance d’échantillonnage. Cela peut être implémentée en ajoutant un ALEA () champ supplémentaire à l’ensemble de données dans la requête interne « select » et dans la requête « select » externe cette condition sur ce champ aléatoire.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Ici, `<sample rate, 0-1>` spécifie la proportion d’enregistrements que les utilisateurs souhaitent exemple.

## <a name="group"></a>Sondage en groupes

Lorsque échantillonnage de données par catégorie, vous souhaiterez inclure ou exclure toutes les instances d’une valeur particulière d’une variable par catégorie. Il s’agit qu’entend-on par « sondage par groupe ».
Par exemple, si vous avez une variable par catégorie « État », qui contient les valeurs NY, MA, autorité de certification, NJ, PA, etc., vous enregistrements du même état toujours être ensemble, échantillonnage ou non.

Voici un exemple de requête qui échantillons par groupe :

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="stratified"></a>Échantillonnages stratifié

Sondage est moyen par rapport à une variable par catégorie lorsque échantillons obtenus ont des valeurs de que par catégorie qui se trouvent dans le rapport comme dans la population parent à partir duquel les exemples ont été obtenus. L’exemple même en tant que ci-dessus, supposons que vos données comportent des populations sous-adresse par les États, par exemple NJ ayant 100 observations, NY 60 observations ou WA a 300 observations. Si vous spécifiez le taux d’échantillonnage stratifié à 0,5, puis l’échantillon obtenue doit avoir environ 50, 30 et 150 observations de NJ, NY et WA respectivement.

Voici un exemple de requête :

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
            rank() over (partition by state order by rand()) as state_rank
        from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Pour plus d’informations sur les méthodes d’échantillonnage plus avancées qui sont disponibles dans Hive, voir [LanguageManual échantillonnages](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).
