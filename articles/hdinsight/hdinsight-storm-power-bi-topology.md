<properties
 pageTitle="Utiliser vague d’Apache avec Power BI | Microsoft Azure"
 description="Créer un rapport Power BI à l’aide de données à partir d’une topologie c# s’exécutant sur un cluster Apache vague dans HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
    tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="10/27/2016"
 ms.author="larryfr"/>

# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Permet de visualiser des données à partir d’une topologie Apache vague Power BI

Power BI permet d’afficher visuellement les données sous forme de rapports. Utiliser les modèles Visual Studio pour vague d’HDInsight, vous pouvez utiliser facilement stocker des données à partir d’une topologie en cours d’exécution sur une vague Apache sur HDInsight cluster dans SQL Azure et puis visualiser les données à l’aide de Power BI.

Dans ce document, vous allez apprendre à utiliser Power BI pour créer un rapport à partir des données générées par une topologie vague Apache et stockées dans la base de données SQL Azure.

> [AZURE.NOTE] Alors que les étapes décrites dans ce document dépendent d’un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à cluster une HDInsight fonctionnant sous Windows ou Linux. Clusters basés sur Linux créé uniquement après la prise en charge de 28/10/2016 SCP.NET topologies.
>
> Pour utiliser une topologie c# avec un cluster Linux, vous devez mettre à jour le package Microsoft.SCP.Net.SDK NuGet utilisé par votre projet vers la version 0.10.0.6 ou une version ultérieure. La version du package doit également correspondre à la version principale de vague d’installé sur HDInsight. Par exemple, vague d’HDInsight versions 3.3 et 3.4 utiliser vague version 0.10.x, tandis que HDInsight 3.5 utilise vague de 1.0.x.
> 
> Topologies c# clusters basés sur Linux sur doivent utiliser .NET 4.5 et Mono permet d’exécuter sur le cluster HDInsight. La plupart des éléments fonctionnent toutefois vous devez vérifier le document [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/) d’incompatibilité potentielle.
>
> Pour une version Java de ce projet, qui fonctionne également sur un cluster basée sur Windows ou Linux, voir [traiter les événements de Azure événement Hubs avec vague d’HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Conditions préalables

- Un abonnement Azure. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un utilisateur Azure Active Directory avec accès [Power BI](https://powerbi.com)

* Visual Studio (une des versions suivantes)

    * Visual Studio 2012 avec la [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=39305)

    * Visual Studio 2013 avec la [mise à jour 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Communauté](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)

    * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)

* Les outils de HDInsight pour Visual Studio : voir [commencer à utiliser les outils de HDInsight pour Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md) pour plus d’informations sur les informations d’installation.

## <a name="how-it-works"></a>Mode de fonctionnement

Cet exemple contient une topologie de c# vague aléatoirement génère des données du journal Internet Information Services (IIS). Ces données sont ensuite écrite dans une base de données SQL, puis à partir de là, il est utilisé pour générer des rapports dans Power BI.

Voici une liste des fichiers que les principales fonctionnalités de cet exemple de mise en œuvre.

* **SqlAzureBolt.cs**: enregistre les informations de produit dans la topologie vague à base de données SQL.

* **IISLogsTable.sql**: permet de générer la base de données les données sont stockées dans les instructions Transact-SQL.

> [AZURE.WARNING] Vous devez créer la table de base de données SQL avant le démarrage de la topologie de sur votre cluster HDInsight.

## <a name="download-the-example"></a>Télécharger l’exemple

Télécharger l' [exemple HDInsight c# vague de Power BI](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Pour la télécharger, soit branche/cloner à l’aide de [git](http://git-scm.com/), ou cliquez sur le lien **Télécharger** pour télécharger un fichier ZIP de l’archivage.

## <a name="create-a-database"></a>Créer une base de données

1. Suivez les étapes dans le document de [base de données SQL didacticiel](../sql-database/sql-database-get-started.md) pour créer une nouvelle base de données SQL.

2. Connectez-vous à la base de données en suivant les étapes décrites dans le document de [se connecter à une base de données SQL avec Visual Studio](../sql-database/sql-database-connect-query.md) pour vous connecter à la base de données.

4. Cliquez avec le bouton droit sur la base de données dans l’Explorateur d’objets, créez une __Nouvelle requête__. Coller le contenu du fichier __IISLogsTable.sql__ inclus dans le projet téléchargé dans la fenêtre de requête, puis utilisez Ctrl + Maj + E pour exécuter la requête. Vous devez recevoir un message réussi l’ou les commandes.

    Une fois cette opération terminée, il sera une nouvelle table nommée __IISLOGS__ dans la base de données.

## <a name="configure-the-sample"></a>Configurer l’échantillon

1. À partir du [portail Azure](https://portal.azure.com), sélectionnez votre base de données SQL. Dans la section __Essentials__ de la cuillère de base de données SQL, sélectionnez __Afficher les chaînes de connexion de base de données__. Dans la liste qui s’affiche, copiez les informations __ADO.NET (authentification SQL)__ .

1. Ouvrez l’exemple dans Visual Studio. À partir **L’Explorateur de solutions**, ouvrez le fichier **App.config** et recherchez l’entrée suivante :

        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
    
    Remplacez la valeur __## TOBEFILLED ##__ par la chaîne de connexion de base de données copiée à l’étape précédente. Remplacer __{votre\_nom d’utilisateur}__ et __{votre\_mot de passe}__ avec le nom d’utilisateur et mot de passe pour la base de données.

2. Enregistrez et fermez les fichiers.

## <a name="deploy-the-sample"></a>Déploiement de l’échantillon

1. Dans **L’Explorateur de solutions**, droit sur le projet **StormToSQL** et sélectionnez **Envoyer à vague d’HDInsight**. Sélectionnez le cluster HDInsight à partir de la boîte de dialogue **Cluster vague de** liste déroulante.

    > [AZURE.NOTE] Il peut prendre quelques secondes pour la liste déroulante **Vague de Cluster** à remplir avec des noms de serveur.
    >
    > Si vous y êtes invité, entrez les informations d’identification pour votre abonnement Azure. Si vous avez plusieurs abonnements, connectez-vous à celui qui contient votre vague de sur cluster HDInsight.

2. Lorsque la topologie a été envoyée, les Topologies vague de pour le cluster doit apparaître. Sélectionnez l’entrée SqlAzureWriterTopology dans la liste pour afficher des informations sur la topologie en cours d’exécution.

    ![Les topologies, avec la topologie sélectionnée](./media/hdinsight-storm-power-bi-topology/topologyview.png)

    Vous pouvez utiliser cet affichage pour afficher les informations sur la topologie, ou double-cliquez sur entrées (par exemple, SqlAzureBolt) pour afficher des informations spécifiques à un composant de la topologie.

3. Une fois la topologie s’est exécutée pendant quelques minutes, retournez dans la fenêtre de requête SQL utilisée pour créer la base de données. Remplacez les instructions existantes avec les éléments suivants.

        select * from iislogs;
    
    Utiliser Ctrl + Maj + E pour exécuter la requête et vous devant recevoir des résultats semblables à ce qui suit.
    
        1   2016-05-27 17:57:14.797 255.255.255.255 /bar    GET 200
        2   2016-05-27 17:57:14.843 127.0.0.1   /spam/eggs  POST    500
        3   2016-05-27 17:57:14.850 123.123.123.123 /eggs   DELETE  200
        4   2016-05-27 17:57:14.853 127.0.0.1   /foo    POST    404
        5   2016-05-27 17:57:14.853 10.9.8.7    /bar    GET 200
        6   2016-05-27 17:57:14.857 192.168.1.1 /spam   DELETE  200

    Il s’agit de données qui a été écrite de la topologie vague.

## <a name="create-a-report"></a>Créer un rapport

1. Se connecter à la [base de données SQL Azure connecteur](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) Power BI.

2. Dans __les bases de données__, sélectionnez __Insérer__.

3. Sélectionnez la __Base de données SQL Azure__, puis __se connecter__.

4. Entrez les informations pour vous connecter à votre base de données SQL Azure. Cela peut s’avérer en visitant le [portail Azure](https://portal.azure.com) et en sélectionnant votre base de données SQL.

    > [AZURE.NOTE] Vous pouvez également définir l’intervalle d’actualisation et des filtres personnalisés à l’aide __d’Activer les Options avancées__ de la boîte de dialogue connexion.

5. Une fois que vous êtes déjà connecté, vous verrez un nouveau groupe de données portant le même nom en tant que vous connecté à la base de données. Sélectionnez le jeu de données pour commencer la création d’un rapport.

3. À partir des __champs__, développez l’entrée __IISLOGS__ . Activez la case à cocher pour __URISTEM__. Cela créera un nouveau rapport qui répertorie le radical URI (/ foo, / barre, etc.) enregistré dans la base de données.

    ![Création d’un état](./media/hdinsight-storm-power-bi-topology/createreport.png)

5. Ensuite, faites glisser __méthode__ au rapport. Le rapport est mise à jour pour répertorier les radical et la méthode HTTP correspondante utilisée pour la requête HTTP.

    ![ajouter les données de méthode](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

4. À partir de la colonne __visualisations__ , sélectionnez l’icône de __champs__ , puis la flèche vers le bas en regard de la __méthode__ dans la section __valeurs__ . Dans la liste qui s’affiche, sélectionnez __nombre__. Cette opération modifie le rapport à un nombre de fois combien ont accédé à un URI spécifique de la liste.

    ![Conversion en un nombre des méthodes](./media/hdinsight-storm-power-bi-topology/count.png)

6. Ensuite, sélectionnez le __graphique en histogrammes empilé__ pour modifier la façon dont les informations sont affichées.

    ![Modification dans un graphique empilé](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

7. Une fois que le rapport comment vous le souhaitez, utilisez l’entrée __Enregistrer__ dans le menu pour entrer un nom et enregistrez le rapport.

## <a name="stop-the-topology"></a>Arrêter la topologie

La topologie continuent à exécuter jusqu'à ce que vous l’arrêter ou supprimez la vague de sur cluster HDInsight. Procédez comme suit pour arrêter la topologie.

1. Dans Visual Studio, revenez à la visionneuse de topologie et sélectionnez la topologie.

2. Sélectionnez le bouton **Arrêter** pour arrêter la topologie.

    ![Supprimer le bouton de la topologie de synthèse](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à envoyer des données à partir d’une topologie vague à la base de données SQL, puis visualiser les données à l’aide de Power BI. Pour plus d’informations sur la façon de travailler avec d’autres technologies Azure à l’aide de vague de sur HDInsight, voir les rubriques suivantes :

* [Exemples de topologies pour vague d’HDInsight](hdinsight-storm-example-topology.md)
