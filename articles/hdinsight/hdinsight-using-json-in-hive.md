<properties
   pageTitle="Analyser et processus JSON des documents avec Hive dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser des documents JSON et les analyser à l’aide de Hive dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="rashimg"
   manager="mwinkle"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/22/2015"
   ms.author="rashimg"/>


# <a name="process-and-analyze-json-documents-using-hive-in-hdinsight"></a>Traiter et analyser des documents JSON à l’aide de Hive dans HDInsight

Découvrez comment traiter et analyser les fichiers JSON à l’aide de Hive dans HDInsight. Le document JSON suivant sera utilisé dans le didacticiel

    {
        "StudentId": "trgfg-5454-fdfdg-4346",
        "Grade": 7,
        "StudentDetails": [
            {
                "FirstName": "Peggy",
                "LastName": "Williams",
                "YearJoined": 2012
            }
        ],
        "StudentClassCollection": [
            {
                "ClassId": "89084343",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "High",
                "Score": 93,
                "PerformedActivity": false
            },
            {
                "ClassId": "78547522",
                "ClassParticipation": "NotSatisfied",
                "ClassParticipationRank": "None",
                "Score": 74,
                "PerformedActivity": false
            },
            {
                "ClassId": "78675563",
                "ClassParticipation": "Satisfied",
                "ClassParticipationRank": "Low",
                "Score": 83,
                "PerformedActivity": true
            }
        ]
    }

Le fichier se trouve à wasbs://processjson@hditutorialdata.blob.core.windows.net/. Pour plus d’informations sur l’utilisation de stockage d’objets Blob Azure avec HDInsight, voir [utiliser HADOOP compatible Azure Blob storage avec Hadoop dans HDInsight](hdinsight-hadoop-use-blob-storage.md). Si vous voulez, vous pouvez copier le fichier vers le conteneur par défaut de votre cluster.

Dans ce didacticiel, vous allez utiliser la console Hive.  Pour obtenir des instructions d’ouverture de la console Hive, voir [Utiliser la ruche avec Hadoop sur HDInsight avec Bureau à distance](hdinsight-hadoop-use-hive-remote-desktop.md).

##<a name="flatten-json-documents"></a>Fusionner les documents JSON

Les méthodes répertoriées dans la section suivante nécessitent le document JSON dans une seule ligne. Vous devez donc fusionner le document JSON à une chaîne. Si votre document JSON est déjà fusionné, vous pouvez ignorer cette étape et passer directement à la section suivante sur les données JSON analyse.

    DROP TABLE IF EXISTS StudentsRaw;
    CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

    DROP TABLE IF EXISTS StudentsOneLine;
    CREATE EXTERNAL TABLE StudentsOneLine
    (
      json_body string
    )
    STORED AS TEXTFILE LOCATION '/json/students';

    INSERT OVERWRITE TABLE StudentsOneLine
    SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
          FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
          GROUP BY INPUT__FILE__NAME;

    SELECT * FROM StudentsOneLine

Le fichier JSON brut se trouve à **wasbs://processjson@hditutorialdata.blob.core.windows.net/**. La table Hive *StudentsRaw* pointe vers le document JSON non plat brut.

La table Hive *StudentsOneLine* stockera les données dans le système de fichiers HDInsight par défaut sous le chemin d’accès */json/étudiants /* .

L’instruction INSERT renseigner le tableau StudentOneLine avec les données JSON plane.

L’instruction SELECT renvoient uniquement 1 ligne.

Voici le résultat de l’instruction SELECT :

![Mise à plat du document JSON.][image-hdi-hivejson-flatten]

##<a name="analyze-json-documents-in-hive"></a>Analyser les documents JSON dans Hive

Hive fournit trois mécanismes différents pour exécuter des requêtes sur des documents JSON :

- Utilisez la commande obtenir\_JSON\_objet UDF (fonctions définies par l’utilisateur)
- utiliser le fichier UDF JSON_TUPLE
- utiliser SerDe personnalisé
- écrire que vous êtes propriétaire UDF à l’aide de Python ou autres langues. Consultez [cet article] [ hdinsight-python] sur l’exécution de votre propre code Python avec Hive.

### <a name="use-the-getjsonobject-udf"></a>Utilisez la commande obtenir\_JSON_OBJECT UDF
Hive fournit qu'un UDF intégré appelé [obtenir objet json](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) qui peut effectuer JSON interroger pendant l’exécution. Cette méthode prend deux arguments : le nom de la table et le nom de la méthode qui comporte le document JSON plat et le champ JSON qui doit être analysé. Examinons un exemple pour voir comment fonctionne cette UDF.

Obtenir le prénom et le nom de chaque étudiant

    SELECT
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
      GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
    FROM StudentsOneLine;

Voici la sortie lors de l’exécution de cette requête dans la fenêtre de la console.

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Il existe quelques limitations de la UDF get-json_object.

- Étant donné que chaque champ de la requête nécessite une ré-analyse la requête, il affecte les performances.
- OBTENIR\_JSON_OBJECT() renvoie la représentation sous forme de chaîne d’un tableau. Pour convertir un tableau Hive, vous devez utiliser des expressions régulières pour remplacer les crochets ' [ » et «] », puis appelez également fractionner pour obtenir le tableau.


C’est pourquoi le wiki Hive recommande l’utilisation de json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Utiliser le fichier UDF JSON_TUPLE

Un autre UDF fourni par Hive est appelée [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple) qui fonctionne mieux que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Cette méthode prend un ensemble de clés et une chaîne JSON et renvoie un uplet de valeurs à l’aide d’une fonction. La requête suivante renvoie l’id de l’étudiant et la qualité du document JSON :

    SELECT q1.StudentId, q1.Grade
      FROM StudentsOneLine jt
      LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
        AS StudentId, Grade;

Le résultat de ce script dans la console Hive :

![json_tuple UDF][image-hdi-hivejson-jsontuple]

JSON\_TUPLE utilise la syntaxe de la [vue latérale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) dans Hive qui permet de json\_tuple pour créer une table virtuelle en appliquant la fonction UDT pour chaque ligne de la table d’origine.  JSONs complexes deviennent trop encombrant en raison de l’utilisation répétée de vue latérale. En outre, JSON_TUPLE ne peuvent pas gérer JSONs imbriquées.


###<a name="use-custom-serde"></a>Utiliser SerDe personnalisé

SerDe est le meilleur choix pour l’analyse des documents JSON imbriqués, il vous permet de définir le schéma JSON, et utiliser le schéma pour analyser les documents. Dans ce didacticiel, vous allez utiliser parmi le plus populaire SerDe qui a été développé par [rcongiu](https://github.com/rcongiu).

**Pour utiliser le SerDe personnalisé :**

1. Installation [du Kit de développement SE Java 7u55 JDK 1.7.0_55](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u55-oth-JPR). Choisir la version de Windows X64 du JDK si vous vous apprêtez à utiliser le déploiement Windows de HDInsight

    >[AZURE.WARNING] 1.8 JDK ne fonctionne pas avec cette SerDe.

    Une fois l’installation terminée, ajoutez une nouvelle variable d’environnement utilisateur :

    1. À partir de l’écran Windows, ouvrez **l’affichage des paramètres système avancés** .
    2. Cliquez sur **Variables d’environnement**.  
    3. Ajouter qu'une nouvelle variable d’environnement **JAVA_HOME** pointe vers **C:\Program Files\Java\jdk1.7.0_55** ou à l’endroit où votre JDK est installé.

    ![Configuration de la configuration correcte des valeurs pour JDK][image-hdi-hivejson-jdk]

2. Installer [Maven 3.3.1](http://mirror.olnevhost.net/pub/apache/maven/maven-3/3.3.1/binaries/apache-maven-3.3.1-bin.zip)

    Ajouter le dossier bin à votre chemin d’accès en accédant au contrôle Panel--> modifier les Variables système pour les variables de Environment de votre compte. La capture d’écran ci-dessous vous montre comment effectuer cette opération.

    ![La configuration Maven][image-hdi-hivejson-maven]

3. Dupliquer le projet à partir de [Hive-JSON-SerDe](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) github site. Vous pouvez le faire en cliquant sur le bouton « Télécharger le code postal » comme le montre la capture d’écran ci-dessous.

    ![Clonant le projet][image-hdi-hivejson-serde]

4 : accédez au dossier dans lequel vous avez téléchargé ce package et type « mvn ». Cette action doit créer les fichiers jar nécessaires que vous pouvez copier ensuite au cluster.

5 : accédez au dossier cible sous le dossier racine dans lequel vous avez téléchargé le package. Téléchargez le fichier json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar au serveur de nœud de votre cluster. J’ai généralement placées sous le dossier binaire hive : C:\apps\dist\hive-0.13.0.2.1.11.0-2316\bin ou un élément similaire.

6 : dans l’invite hive, tapez « ajouter jar /path/to/json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar ». Étant donné que dans mon cas, le fichier jar se trouve dans le dossier C:\apps\dist\hive-0.13.x\bin, puis-je ajouter directement le bocal avec le nom comme indiqué ci-dessous :

    add jar json-serde-1.1.9.9-Hive13-jar-with-dependencies.jar;

    ![Adding JAR to your project][image-hdi-hivejson-addjar]

À présent, vous êtes prêt à utiliser le SerDe pour exécuter des requêtes sur le document JSON.

L’expression suivante crée une table avec un schéma défini

    DROP TABLE json_table;
    CREATE EXTERNAL TABLE json_table (
      StudentId string,
      Grade int,
      StudentDetails array<struct<
          FirstName:string,
          LastName:string,
          YearJoined:int
          >
      >,
      StudentClassCollection array<struct<
          ClassId:string,
          ClassParticipation:string,
          ClassParticipationRank:string,
          Score:int,
          PerformedActivity:boolean
          >
      >
    ) ROW FORMAT SERDE 'org.openx.data.jsonserde.JsonSerDe'
    LOCATION '/json/students';

Pour répertorier le prénom et le nom de l’étudiant

    SELECT StudentDetails.FirstName, StudentDetails.LastName FROM json_table;

Voici le résultat de la console Hive.

![Requête SerDe 1][image-hdi-hivejson-serde_query1]

Pour calculer la somme des scores du document JSON

    SELECT SUM(scores)
    FROM json_table jt
      lateral view explode(jt.StudentClassCollection.Score) collection as scores;

La requête ci-dessus utilise [Éclater vue latérale](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) UDF pour développer le tableau des scores afin qu’ils peuvent être additionnées.

Voici le résultat de la console Hive.

![Requête SerDe 2][image-hdi-hivejson-serde_query2]

Pour trouver les sujets un étudiant donné a remporté plus de 80 points sélectionnez  
      JT. StudentClassCollection.ClassId de json_table jt vue latérale éclater (JT. Collection StudentClassCollection.Score) en tant que résultat où score général > 80 ;

La requête ci-dessus renvoie une matrice Hive Contrairement à obtenir\_json\_objet qui retourne une chaîne.

![Requête SerDe 3][image-hdi-hivejson-serde_query3]

Si vous voulez skil JSON incorrect, puis comme indiqué dans la [page wiki](https://github.com/sheetaldolas/Hive-JSON-Serde/tree/master) de cette SerDe vous pouvez effectuer cette opération en tapant le code ci-dessous :  

    ALTER TABLE json_table SET SERDEPROPERTIES ( "ignore.malformed.json" = "true");




##<a name="summary"></a>Résumé
En conclusion, le type d’opérateur JSON dans Hive que vous choisissez dépend de votre scénario. Si vous avez un document JSON simple et vous n’avez qu’un seul champ pour rechercher sur – vous pouvez choisir d’utiliser l’obtenir UDF ruche\_json\_objet. Si vous avez plusieurs touches pour rechercher vous pouvez utiliser json_tuple. Si vous avez un document imbriqué, vous devez utiliser le SerDe JSON.

Pour d’autres articles connexes, voir

- [Utiliser des Hive et HiveQL avec Hadoop dans HDInsight pour analyser un exemple de fichier log4j Apache](hdinsight-use-hive.md)
- [Analyser des données de vol retard à l’aide Hive dans HDInsight](hdinsight-analyze-flight-delay-data.md)
- [Analyser des données Twitter avec Hive dans HDInsight](hdinsight-analyze-twitter-data.md)
- [Exécuter une tâche Hadoop à l’aide de DocumentDB et HDInsight](../documentdb/documentdb-run-hadoop-with-hdinsight.md)

[hdinsight-python]: hdinsight-python.md

[image-hdi-hivejson-flatten]: ./media/hdinsight-using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/hdinsight-using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/hdinsight-using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png
