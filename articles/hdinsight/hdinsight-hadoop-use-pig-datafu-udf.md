<properties
pageTitle="Utiliser DataFu avec cochon sur HDInsight"
description="DataFu est un ensemble de bibliothèques pour une utilisation avec Hadoop. Découvrez comment vous pouvez utiliser DataFu avec cochon sur votre cluster HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="jhubbard"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="08/23/2016"
ms.author="larryfr"/>

#<a name="use-datafu-with-pig-on-hdinsight"></a>Utiliser DataFu avec cochon sur HDInsight

DataFu est un ensemble de bibliothèques Open Source pour une utilisation avec Hadoop. Dans ce document, vous allez apprendre comment utiliser DataFu sur votre cluster HDInsight et comment utiliser les fonctions de définies par l’utilisateur DataFu (UDF) avec cochon.

##<a name="prerequisites"></a>Conditions préalables

* Un abonnement Azure.

* Un cluster Azure HDInsight (Linux ou sur Windows)

* Une connaissance élémentaire [à l’aide de cochon sur HDInsight](hdinsight-use-pig.md)

##<a name="install-datafu-on-linux-based-hdinsight"></a>Installer DataFu sur HDInsight basé sur Linux

> [AZURE.NOTE] DataFu est installé version clusters basés sur Linux 3.3 et versions ultérieures et clusters fonctionnant sous Windows. Il n’est pas antérieure à 3.3 installé sur clusters basés sur Linux.
>
> Si vous utilisez une version cluster basé sur Linux 3.3 ou une version ultérieure, ou un cluster fonctionnant sous Windows, vous pouvez ignorer cette section.

DataFu peut être téléchargé et installé à partir du référentiel Maven. Procédez comme suit pour ajouter DataFu à votre cluster HDInsight :

1. Se connecter à votre cluster basé sur Linux HDInsight à l’aide de SSH. Pour plus d’informations sur l’utilisation de SSH avec HDInsight, voir un des documents suivants :

    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight de Linux, OS X et Unix](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utiliser le protocole SSH avec basé sur Linux Hadoop sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-unix.md)
    
2. Utilisez la commande suivante pour télécharger le fichier jar DataFu à l’aide de l’utilitaire wget, ou copiez et collez le lien dans votre navigateur pour commencer le téléchargement.

        wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar

3. Ensuite, téléchargez le fichier vers le stockage par défaut pour votre cluster HDInsight. Cela rend le fichier disponible pour tous les nœuds du cluster, et le fichier restera dans l’espace de stockage même si vous supprimez puis recréez le cluster.

        hdfs dfs -put datafu-1.2.0.jar /example/jars
    
    > [AZURE.NOTE] L’exemple ci-dessus enregistre le fichier jar dans `wasbs:///example/jars` étant donné que ce répertoire existe déjà sur le stockage de cluster. Vous pouvez utiliser n’importe quel emplacement que vous voulez de stockage de cluster HDInsight.

##<a name="use-datafu-with-pig"></a>Utiliser DataFu avec cochon

Les étapes décrites dans cette section part du principe que vous êtes habitué à utiliser cochon sur HDInsight et fournissez uniquement les instructions cochon Latin, pas les étapes sur la façon de les utiliser avec le cluster. Pour plus d’informations sur l’utilisation de cochon avec HDInsight, voir [Utiliser cochon avec HDInsight](hdinsight-use-pig.md).

> [AZURE.IMPORTANT] Lorsque vous utilisez DataFu de cochon sur un cluster HDInsight basé sur Linux, vous devez tout d’abord enregistrer le fichier jar à l’aide de l’instruction cochon Latin suivante :
>
> ```register wasbs:///example/jars/datafu-1.2.0.jar```
>
> DataFu est enregistré par défaut sur les clusters HDInsight fonctionnant sous Windows.

Vous définirez généralement un alias pour les fonctions de DataFu. Par exemple :

    DEFINE SHA datafu.pig.hash.SHA();
    
Ceci permet de définir un alias nommé `SHA` pour la fonction de hachage de ça. Vous pouvez ensuite utiliser ce dans un script Latin cochon pour générer un hachage pour les données d’entrée. Par exemple, celle-ci remplace les noms dans les données d’entrée avec une valeur de hachage :

    raw = LOAD '/data/raw/' USING PigStorage(',') AS  
        (name:chararray, 
        int1:int, 
        int2:int,
        int3:int); 
    mask = FOREACH raw GENERATE SHA(name), int1, int2, int3; 
    DUMP mask;

Si elle est utilisée avec les données d’entrée suivantes :

    Lana Zemljaric,5,9,1
    Qiong Zhong,9,3,6
    Sandor Harsanyi,0,7,3
    Roko Petkovic,2,6,2
    Tibor Rozsa,8,0,0
    Lea Hrastovsek,6,3,6
    Regina Toth,2,1,2
    Eva Makay,8,9,2
    Shi Liao,4,6,0
    Tjasa Zemljaric,0,2,5
    
Il génère le résultat suivant :

    (c1a743b0f34d349cfc2ce00ef98369bdc3dba1565fec92b4159a9cd5de186347,5,9,1)
    (713d030d621ab69aa3737c8ea37a2c7c724a01cd0657a370e103d8cdecac6f99,9,3,6)
    (7a5f5abdd281f68168199319d98a1a662535f988d1443b3a3c497010937bac89,0,7,3)
    (a94818e93807e12079c4b35f8f3c8c8ef8e8acd1954e7f0476bc1a3a86fc96a9,2,6,2)
    (894ead4f48af91df7e088241218a23157bede7c52115272e417e95c046d48902,8,0,0)
    (6f99f163af3448fda672087db306f363e27a98a9e49c1f274a0860e303f8aec4,6,3,6)
    (a03de92a28be3c6a984c7a153fa9ed81c0413f76a9401955b5f7e04a5dd0ab9f,2,1,2)
    (6ceab977c8fb48d9ad0dc413e6bc646cabd89f22e7ab97a6b0133f3d225c6013,8,9,2)
    (fa9c436469096ff1bd297e182831f460501b826272ae97e921f5f6e3f54747e8,4,6,0)
    (bc22db7c238b86c37af79a62c78f61a304b35143f6087eb99c34040325865654,0,2,5)

##<a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur DataFu ou cochon, voir les documents suivants :

* [Guide de cochon DataFu Apache](http://datafu.incubator.apache.org/docs/datafu/guide.html).

* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)
