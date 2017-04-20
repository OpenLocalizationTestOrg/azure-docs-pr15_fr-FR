<properties
    pageTitle="Utiliser le langage c# avec Hive et cochon sur Hadoop dans HDInsight | Microsoft Azure"
    description="Découvrez comment utiliser c# fonctions utilisateur (UDF) avec Hive et cochon diffusion en continu dans Azure HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="larryfr"/>


#<a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Utiliser des fonctions définies par l’utilisateur c# avec Hive et cochon en continu sur Hadoop dans HDInsight

Hive et cochon sont parfaits pour utiliser des données dans Azure HDInsight, mais vous pouvez parfois besoin une langue plus général. Hive et cochon vous permettent d’appeler le code externes par le biais de fonctions définies par l’utilisateur (UDF) ou de diffusion en continu.

Dans ce document, découvrez comment utiliser c# avec Hive et cochon.

##<a name="prerequisites"></a>Conditions préalables

* Windows 7 ou version ultérieure.

* Visual Studio avec les versions suivantes :

    * Visual Studio 2012 Professional/Premium/intégrale avec la [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 Communauté/Professionnel/Premium/intégrale avec la [mise à jour 4](https://www.microsoft.com/download/details.aspx?id=44921)

    * Visual Studio 2015

* Hadoop sur cluster HDInsight - voir [mise en service un cluster HDInsight](hdinsight-provision-clusters.md) pour obtenir la procédure pour créer un cluster

* Hadoop Tools pour Visual Studio. Voir [prise en main de l’utilisation HDInsight Hadoop Tools pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour obtenir la procédure d’installation et configuration des outils.

##<a name="net-on-hdinsight"></a>.NET sur HDInsight

Le common language runtime .NET (CLR) et les cadres sont installés par défaut sur les clusters HDInsight fonctionnant sous Windows. Cela vous permet d’utiliser des applications c# avec Hive et cochon streaming (données sont passées entre Hive/cochon et l’application c# via stdin/stdout).

> [AZURE.NOTE] Il n’existe actuellement aucune prise en charge pour l’exécution .NET Framework UDF sur clusters HDInsight basé sur Linux. 

##<a name="net-and-streaming"></a>.NET et la diffusion en continu

Diffusion en continu implique Hive et cochon transmission des données à une application externe sur stdout et la réception les résultats via stdin. Pour les applications c#, il est plus facile si vous `Console.ReadLine()` et `Console.WriteLine()`.

Dans la mesure où Hive et cochon ont besoin d’appeler l’application en cours d’exécution, le modèle **Application Console** doit être utilisé pour vos projets c#.

##<a name="hive-and-c35"></a>Hive et C & #35 ;

###<a name="create-the-c-project"></a>Créez le projet c#

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application Console**et nommez le nouveau projet **HiveCSharp**.

2. Remplacez le contenu de **Program.cs** avec les éléments suivants :

        using System;
        using System.Security.Cryptography;
        using System.Text;
        using System.Threading.Tasks;

        namespace HiveCSharp
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Parse the string, trimming line feeds
                        // and splitting fields at tabs
                        line = line.TrimEnd('\n');
                        string[] field = line.Split('\t');
                        string phoneLabel = field[1] + ' ' + field[2];
                        // Emit new data to stdout, delimited by tabs
                        Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                    }
                }
                /// <summary>
                /// Returns an MD5 hash for the given string
                /// </summary>
                /// <param name="input">string value</param>
                /// <returns>an MD5 hash</returns>
                static string GetMD5Hash(string input)
                {
                    // Step 1, calculate MD5 hash from input
                    MD5 md5 = System.Security.Cryptography.MD5.Create();
                    byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                    byte[] hash = md5.ComputeHash(inputBytes);

                    // Step 2, convert byte array to hex string
                    StringBuilder sb = new StringBuilder();
                    for (int i = 0; i < hash.Length; i++)
                    {
                        sb.Append(hash[i].ToString("x2"));
                    }
                    return sb.ToString();
                }
            }
        }

3. Générez le projet.

###<a name="upload-to-storage"></a>Télécharger vers le stockage

1. Dans Visual Studio, ouvrez **l’Explorateur de serveur**.

3. Développez **Azure**et puis **HDInsight**.

4. Si vous y êtes invité, entrez vos informations d’identification de l’abonnement Azure, puis cliquez sur **Se connecter**.

5. Développez le cluster HDInsight que vous souhaitez déployer cette application, puis puis **Compte de stockage par défaut**.

    ![Explorateur de serveurs affichant le compte de stockage pour le cluster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

6. Double-cliquez sur **Conteneur par défaut** pour le cluster. Ceci ouvre une nouvelle fenêtre qui affiche le contenu du conteneur par défaut.

7. Cliquez sur l’icône de téléchargement et puis accédez au dossier **bin\debug** pour le projet **HiveCSharp** . Enfin, sélectionnez le fichier **HiveCSharp.exe** et cliquez sur **Ok**.

    ![icône de téléchargement](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

8. Une fois le téléchargement terminé, vous ne pourrez pas utiliser l’application d’une requête Hive.

###<a name="hive-query"></a>Requête Hive

1. Dans Visual Studio, ouvrez **l’Explorateur de serveur**.

2. Développez **Azure**et puis **HDInsight**.

5. Le cluster que vous avez déployée de l’application **HiveCSharp** d’avec le bouton droit et sélectionnez **écrire une requête ruche**.

6. Procédez comme suit pour la requête Hive :

        add file wasbs:///HiveCSharp.exe;

        SELECT TRANSFORM (clientid, devicemake, devicemodel)
        USING 'HiveCSharp.exe' AS
        (clientid string, phoneLabel string, phoneHash string)
        FROM hivesampletable
        ORDER BY clientid LIMIT 50;

    Cela sélectionne la `clientid`, `devicemake`, et `devicemodel` des champs de `hivesampletable`et passe les champs à l’application HiveCSharp.exe. La requête attend l’application à retourner trois champs, qui sont stockés en tant que `clientid`, `phoneLabel`, et `phoneHash`. La requête s’attend également à trouver HiveCSharp.exe à la racine du conteneur de stockage par défaut (`add file wasbs:///HiveCSharp.exe`).

5. Cliquez sur **Envoyer** pour envoyer la tâche pour le cluster HDInsight. La fenêtre **Récapitulatif des ruche** s’ouvre.

6. Cliquez sur **Actualiser** pour actualiser la synthèse jusqu'à ce que **l’État du travail** devient **terminé**. Pour afficher le résultat de la tâche, cliquez sur **Sortie des tâches**.

##<a name="pig-and-c35"></a>Cochon et C & #35 ;

###<a name="create-the-c-project"></a>Créez le projet c#

1. Ouvrez Visual Studio et créez une solution. Pour le type de projet, sélectionnez **Application Console**et nommez le nouveau projet **PigUDF**.

2. Remplacez le contenu du fichier **Program.cs** avec les éléments suivants :

        using System;

        namespace PigUDF
        {
            class Program
            {
                static void Main(string[] args)
                {
                    string line;
                    // Read stdin in a loop
                    while ((line = Console.ReadLine()) != null)
                    {
                        // Fix formatting on lines that begin with an exception
                        if(line.StartsWith("java.lang.Exception"))
                        {
                            // Trim the error info off the beginning and add a note to the end of the line
                            line = line.Remove(0, 21) + " - java.lang.Exception";
                        }
                        // Split the fields apart at tab characters
                        string[] field = line.Split('\t');
                        // Put fields back together for writing
                        Console.WriteLine(String.Join("\t",field));
                    }
                }
            }
        }

    Cette application analysera les lignes envoyées à partir de cochon et reformater les lignes qui commencent par `java.lang.Exception`.

3. Enregistrez **Program.cs**, puis générer le projet.

###<a name="upload-the-application"></a>Télécharger l’application

1. Cochon streaming prévoit que l’application soit local sur le système de fichiers cluster. Activer le Bureau à distance pour le cluster HDInsight et puis vous y connecter en suivant les instructions à [se connecter à clusters HDInsight à l’aide de RDP](hdinsight-administer-use-management-portal.md#rdp).

2. Une fois connecté, copiez **PigUDF.exe** à partir du répertoire **bin/débogage** pour le projet PigUDF sur votre ordinateur local et collez-le dans le répertoire **% PIG_HOME %** sur le cluster.

###<a name="use-the-application-from-pig-latin"></a>Utiliser l’application à partir de cochon Latin

1. À partir de la session Bureau à distance, démarrez la ligne de commande Hadoop à l’aide de l’icône de **ligne de commande Hadoop** sur le bureau.

2. Pour commencer la ligne de commande cochon, utilisez ce qui suit :

        cd %PIG_HOME%
        bin\pig

    Vous obtenez un `grunt>` invite de commandes.

3. Entrez les informations suivantes pour exécuter une tâche de cochon simple à l’aide de l’application .NET Framework :

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    La `DEFINE` instruction crée un alias de `streamer` pour les applications pigudf.exe, et `SHIP` répartit entre les nœuds du cluster. Plus tard, `streamer` est utilisée avec la `STREAM` opérateur pour traiter les lignes uniques contenues dans les journaux et retourner les données sous forme d’une série de colonnes.

> [AZURE.NOTE] Le nom de l’application qui est utilisé pour la diffusion en continu doit être entouré la \` (backtick) caractère quand un alias, et ' (guillemet simple) lorsqu’il est utilisé avec `SHIP`.

3. Après avoir entré la dernière ligne, le travail doit commencer. Finalement il renverra sortie semblable à ce qui suit :

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

##<a name="summary"></a>Résumé

Dans ce document, vous avez appris à utiliser une application .NET Framework à partir de Hive et cochon sur HDInsight. Si vous voulez apprendre à utiliser Python avec Hive et cochon, voir [Utiliser les Python avec Hive et cochon dans HDInsight](hdinsight-python.md).

D’autres manières d’utiliser cochon et Hive et pour découvrir comment utiliser MapReduce, voir les rubriques suivantes :

* [Utiliser Hive avec HDInsight](hdinsight-use-hive.md)

* [Utiliser cochon avec HDInsight](hdinsight-use-pig.md)

* [Utiliser MapReduce avec HDInsight](hdinsight-use-mapreduce.md)
