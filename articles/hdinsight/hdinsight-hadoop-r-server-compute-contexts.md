<properties
   pageTitle="Calculer les options de contexte pour R Server sur HDInsight (preview) | Microsoft Azure"
   description="Découvrez les options de contexte cluster différents disponibles pour les utilisateurs avec serveur R sur HDInsight (preview)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="jhubbard"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="10/18/2016"
   ms.author="jeffstok"
/>

# <a name="compute-context-options-for-r-server-on-hdinsight-preview"></a>Calculer les options de contexte pour R Server sur HDInsight (preview)

Microsoft R Server sur Azure HDInsight (preview) fournit les dernières fonctionnalités pour analytique basée sur R. Il utilise des données qui sont stockées dans HADOOP dans un conteneur dans votre compte de stockage de(../storage/storage-introduction.md "stockage d’objets Blob Azure") [Blob Azure]ou le système de fichiers Linux local. Dans la mesure où Server R est basé sur Ouvrir la source R, les applications R que vous créez peuvent exploiter aucun des modules 8000 + R ouvrir la source. Ils peuvent également exploiter les procédures de de [DETARTREUR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Révolution Analytique ScaleR"), groupes de package de Microsoft données volumineuses analytique inclus avec serveur R.  

Le nœud de bord d’un cluster Premium fournit un endroit pratique pour vous connecter au cluster et exécuter vos scripts R. Avec un nœud de bord, vous avez la possibilité de l’exécution de fonctions distribuées parallélisées de DETARTREUR sur les cœurs du serveur edge nœud. Vous avez également la possibilité pour les exécuter sur les nœuds du cluster à l’aide Hadoop carte réduire de DETARTREUR ou explosion calculer contextes.

## <a name="compute-contexts-for-an-edge-node"></a>Calculer des contextes pour un nœud de bord

En règle générale, un script R qui est exécuté sur le nœud de bord dans R Server s’exécute dans le relais R sur ce nœud. L’exception est ces étapes nécessitant une fonction ScaleR. Les appels ScaleR s’exécutent dans un environnement de cluster est déterminé par la définition du contexte cluster ScaleR.  Lorsque vous exécutez votre script R à partir d’un nœud de bord, les valeurs possibles du contexte cluster sont local séquentiel (« local »), parallèle local (« localpar »), carte réduire et explosion.

Les options « locales » et « localpar » diffèrent uniquement dans l’exécution des appels rxExec. Ils sont tous deux exécutent d’autres appels de la fonction de la réception d’une manière parallèle sur cœurs disponibles sauf indication contraire à l’aide de l’option numCoresToUse ScaleR, par exemple, rxOptions(numCoresToUse=6). Le texte suivant récapitule les différentes options de contexte cluster

| Calculer contexte  | Comment définir                      | Contexte d’exécution                                                                     |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| Local séquentiel | rxSetComputeContext('local')    | L’exécution de parallélisée sur les cœurs du serveur edge nœud, à l’exception des rxExec appelle qui sont exécuté en série |
| Parallèle local   | rxSetComputeContext('localpar') | Exécution de parallélisée sur les cœurs du serveur edge nœud                                 |
| Explosion            | RxSpark()                       | Mises en parallèle exécution distribuée via explosion sur les nœuds du cluster HDI      |
| Carte réduire       | RxHadoopMR()                    | Mises en parallèle exécution distribuée via carte réduire entre les nœuds du cluster HDI |


En supposant que vous voulez que l’exécution parallélisée aux fins de performances, il existe trois options sont disponibles. L’option que vous choisissez dépend de la nature de votre travail analytique et la taille et l’emplacement de vos données.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Conseils pour le choix d’un contexte de calcul

Il n’existe actuellement aucune formule qui vous indique qui calculent contexte à utiliser. Toutefois, il existe quelques principes fondamentaux qui peuvent vous aider à faire le bon choix, ou au moins vous aider à affiner vos choix avant d’exécuter un test d’évaluation. Ces principes sont les suivantes :

1.  Le système de fichiers Linux local est plus rapide que HADOOP.
2.  Analyses répétées sont plus rapides si les données sont locales et s’il s’agit de XDF.
3.  Il est préférable de diffusion en continu de petites quantités de données à partir d’une source de données texte ; Si la quantité de données est plus grande, convertissez-le au XDF avant l’analyse.
4.  La charge de copie ou de diffusion en continu les données vers le nœud de bord pour l’analyse devient impossible à gérer de grandes quantités de données.
5.  Explosion est plus carte réduire pour l’analyse dans Hadoop rapide.

Étant donné ces principes, certaines règles générales permettant de sélectionner un contexte cluster sont :

### <a name="local"></a>Local

- Si la quantité de données à analyser est petite et ne nécessite pas les analyses répétées, puis flux directement dans la routine d’analyse et « local » ou « localpar ».
- Si la quantité de données à analyser est petites et moyennes et exige l’analyse répétée, copiez-le dans le système de fichiers local, l’importer dans XDF et analyser via « local » ou « localpar ».

### <a name="hadoop-spark"></a>Hadoop explosion

- Si la quantité de données à analyser est importante, puis l’importer dans XDF dans HDFS (à moins que le stockage est un problème) et analyser via 'à 'explosion.

### <a name="hadoop-map-reduce"></a>Carte Hadoop réduire

- Utiliser uniquement si vous rencontrez un problème insurmontable avec l’option utiliser le contexte de cluster explosion dans la mesure où généralement qu’il soit plus lente.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Aide en ligne sur rxSetComputeContext

Pour plus d’informations et des exemples de DETARTREUR cluster contextes, voir l’inline à l’aide dans R sur la méthode rxSetComputeContext, par exemple :

    > ?rxSetComputeContext

Vous pouvez également consulter la « ScaleR Distributed Computing Guide » qui est disponible à partir de la bibliothèque du(https://msdn.microsoft.com/library/mt674634.aspx "Serveur R sur MSDN") [R Server MSDN].


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris comment créer un nouveau cluster HDInsight incluant serveur R. Vous avez également appris les notions de base de l’utilisation de la console de R à partir d’une session SSH. Désormais, vous pouvez lire les articles suivants pour découvrir d’autres façons de travailler avec les serveur R sur HDInsight :

- [Vue d’ensemble du serveur R pour Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Prise en main serveur R pour Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Ajouter RStudio serveur à HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Azure options de stockage pour serveur R HDInsight Premium](hdinsight-hadoop-r-server-storage.md)
