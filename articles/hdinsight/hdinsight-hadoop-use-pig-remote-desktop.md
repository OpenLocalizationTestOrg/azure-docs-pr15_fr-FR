<properties
   pageTitle="Utiliser Hadoop cochon avec Bureau à distance dans HDInsight | Microsoft Azure"
   description="Découvrez comment utiliser la commande cochon d’exécuter des instructions cochon Latin à partir d’une connexion Bureau à distance sur un cluster Hadoop fonctionnant sous Windows dans HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Exécuter des travaux cochon à partir d’une connexion Bureau à distance

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Ce document fournit une procédure pas à pas pour l’utilisation de la commande cochon d’exécuter des instructions cochon Latin à partir d’une connexion Bureau à distance à un cluster HDInsight fonctionnant sous Windows. Cochon Latin permet de créer des applications MapReduce en décrivant des transformations de données, au lieu de mapper et réduire les fonctions.

Dans ce document, découvrez comment

##<a id="prereq"></a>Conditions préalables

Pour effectuer les étapes décrites dans cet article, vous devez les éléments suivants.

* Un cluster fonctionnant sous Windows HDInsight (Hadoop sur HDInsight)

* Un ordinateur client exécutant Windows 10, Windows 8 ou Windows 7

##<a id="connect"></a>Connexion Bureau à distance

Activer le Bureau à distance pour le cluster HDInsight, puis vous y connecter en suivant les instructions à [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

##<a id="pig"></a>Utilisez la commande cochon

2. Une fois que vous avez une connexion Bureau à distance, commencer la **ligne de commande Hadoop** à l’aide de l’icône sur le bureau.

2. Utilisez ce qui suit pour démarrer la commande cochon :

        %pig_home%\bin\pig

    Vous obtenez un `grunt>` invite de commandes.

3. Entrez l’instruction suivante :

        LOGS = LOAD 'wasbs:///example/data/sample.log';

    Cette commande charge le contenu du fichier exemple.log dans les fichiers journaux. Vous pouvez afficher le contenu du fichier à l’aide de la commande suivante :

        DUMP LOGS;

4. Transformer les données en appliquant une expression régulière pour extraire uniquement le niveau de journalisation de chaque enregistrement :

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Vous pouvez utiliser le **vidage** pour afficher les données après la transformation. Dans ce cas, `DUMP LEVELS;`.

5. Continuez d’appliquer des transformations en utilisant les instructions suivantes. Utiliser `DUMP` pour afficher le résultat de la transformation après chaque étape.

    <table>
    <tr>
    <th>Instruction</th><th>Fonction</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = niveaux de filtrage par LOGLEVEL n’est pas null ;</td><td>Supprime les lignes qui contiennent une valeur null pour le niveau de journalisation et stocke les résultats dans FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS de groupe par LOGLEVEL ;</td><td>Regroupe les lignes par niveau de journalisation et stocke les résultats dans GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FRÉQUENCE = foreach GROUPEDLEVELS générer un groupe en tant que LOGLEVEL, NB (FILTEREDLEVELS. LOGLEVEL) comme compter ;</td><td>Crée un nouveau jeu de données qui contient chaque journal unique valeur niveau et le nombre de fois qu’il se produit. Ces informations sont stockées dans fréquence</td>
    </tr>
    <tr>
    <td>RÉSULTAT = ordre fréquence en nombre desc ;</td><td>Commandes les niveaux du journal par nombre (tri décroissant) et stocke dans le résultat</td>
    </tr>
    </table>

6. Vous pouvez également enregistrer les résultats d’une transformation à l’aide de la `STORE` instruction. Par exemple, la commande suivante enregistre la `RESULT` dans le répertoire **/example/data/pigout** dans le conteneur de stockage par défaut pour votre cluster :

        STORE RESULT into 'wasbs:///example/data/pigout'

    > [AZURE.NOTE] Les données sont stockées dans le répertoire spécifié dans les fichiers nommés **composant nnnnn**. Si le répertoire existe déjà, vous recevrez un message d’erreur.

7. Pour quitter l’invite grunt, entrez l’instruction suivante.

        QUIT;

###<a name="pig-latin-batch-files"></a>Fichiers de commandes cochon Latin

Vous pouvez également utiliser la commande cochon pour exécuter Latin cochon se trouvant dans un fichier.

3. Après avoir quitté l’invite grunt, ouvrez **le bloc-notes** et créez un nouveau fichier nommé **pigbatch.pig** dans le répertoire **% PIG_HOME %** .

4. Tapez ou collez les lignes suivantes dans le fichier **pigbatch.pig** et enregistrez-le :

        LOGS = LOAD 'wasbs:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;

5. Utiliser les éléments suivants pour exécuter le fichier **pigbatch.pig** à l’aide de la commande cochon.

        pig %PIG_HOME%\pigbatch.pig

    Lorsque la tâche est terminée, vous devriez voir le résultat suivant, doit être la même que lorsque vous avez utilisé `DUMP RESULT;` lors des étapes précédentes :

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="summary"></a>Résumé

Comme vous pouvez le voir, la commande cochon permet de façon interactive exécuter des opérations MapReduce, ou exécuter des travaux cochon Latin qui est stockées dans un fichier de commandes.

##<a id="nextsteps"></a>Étapes suivantes

Pour obtenir des informations générales sur cochon dans HDInsight :

* [Utiliser cochon avec Hadoop sur HDInsight](hdinsight-use-pig.md)

Pour plus d’informations sur les autres méthodes, vous pouvez travailler avec Hadoop sur HDInsight :

* [Utiliser Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

* [Utiliser MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)
