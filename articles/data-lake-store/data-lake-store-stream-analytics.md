<properties
   pageTitle="Flux de données à partir de flux de données Analytique dans données Lake magasin | Azure"
   description="Utiliser Azure flux Analytique pour répartir des données dans le magasin de Lake données Azure"
   services="data-lake-store,stream-analytics" 
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/07/2016"
   ms.author="nitinme"/>

# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Données de flux de données à partir d’Azure stockage Blob dans banque Lake de données à l’aide d’Azure flux Analytique

Dans cet article, vous allez apprendre à utiliser Azure données Lake Store comme résultat pour une tâche Azure flux Analytique. Cet article montre un scénario simple qui lit les données à partir d’un blob Azure stockage (entrée) et écrit les données dans les données Lake Store (résultat).

>[AZURE.NOTE] Pour l’instant, la création et la configuration des sorties de données Lake Store pour flux Analytique est pris en charge uniquement dans le [Portail classique Azure](https://manage.windowsazure.com). Par conséquent, certaines parties de ce didacticiel utilisera le portail classique Azure.

## <a name="prerequisites"></a>Conditions préalables

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- **Azure un abonnement**. Voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/).

- **Activer votre abonnement Azure** pour les données Lake Store Public Preview. Consultez les [instructions](data-lake-store-get-started-portal.md#signup).

- **Compte de stockage azure**. Vous allez utiliser un conteneur blob depuis ce compte pour entrer des données pour une tâche de flux de données Analytique. Pour ce didacticiel, supposons que vous créez un compte de stockage nommé **datalakestoreasa** et un conteneur dans le compte appelé **datalakestoreasacontainer**. Une fois que vous avez créé le conteneur, télécharger un fichier de données d’exemple vers celui-ci. Vous pouvez obtenir un exemple de fichier de données à partir du [Référentiel de Git Azure données Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Vous pouvez utiliser différents clients, tels que l' [Explorateur de stockage Azure](http://storageexplorer.com/), pour télécharger des données dans un conteneur blob.

    >[AZURE.NOTE] Si vous créez le compte à partir du portail Azure, vérifiez que vous créez avec le modèle de déploiement **classique** . Cela garantit que le compte de stockage sont accessibles à partir du portail classique Azure, car c’est ce que nous permet de créer une tâche de flux de données Analytique. Pour obtenir des instructions sur la création d’un compte de stockage à partir du portail Azure à l’aide du déploiement classique, voir [créer un compte de stockage Azure](../storage/storage-create-storage-account/#create-a-storage-account).
    >
    > Ou bien, vous pouvez créer un compte de stockage à partir du portail classique Azure.

- **Compte azure données Lake Store**. Suivez les instructions sur la [prise en main Azure données Lake Store à l’aide du portail Azure](data-lake-store-get-started-portal.md).  


## <a name="create-a-stream-analytics-job"></a>Créer un flux de travail d’Analytique

Vous commencez par créer une tâche de flux de données Analytique contenant une source d’entrée et une destination de sortie. Pour ce didacticiel, la source est un conteneur blob Azure et la destination est données Lake Store.

1. Ouvrez une session sur le [portail classique Azure](https://manage.windowsazure.com).

2. Dans le coin inférieur gauche de l’écran, cliquez sur **Nouveau** **Data Services**, **Flux Analytique**, **Création rapide**. Fournir les valeurs comme indiqué ci-dessous, puis sur **Créer un flux de travail Analytique**.

    ![Créer un flux de travail d’Analytique] (./media/data-lake-store-stream-analytics/create.job.png "Créer une tâche de flux de données Analytique")

## <a name="create-a-blob-input-for-the-job"></a>Créer une entrée d’objets Blob pour la tâche

1. Ouvrez la page de la tâche de flux de données Analytique et cliquez sur l’onglet **entrées** , puis cliquez sur **Ajouter une entrée** pour lancer l’Assistant.

2. Dans la page **Ajouter une entrée à votre travail** , sélectionnez le **flux de données**, puis cliquez sur la flèche droite.

    ![Ajouter une entrée à votre travail] (./media/data-lake-store-stream-analytics/create.input.1.png "Ajouter une entrée à votre travail")

3. Dans la page **Ajouter un flux de données à votre travail** , sélectionnez **stockage Blob**, puis cliquez sur la flèche droite.

    ![Ajouter un flux de données à la tâche] (./media/data-lake-store-stream-analytics/create.input.2.png "Ajouter un flux de données à la tâche")

4. Dans la page **paramètres du stockage Blob** , fournissent des détails pour le stockage blob que vous utiliserez comme source de données d’entrée.

    ![Fournir des paramètres de stockage blob] (./media/data-lake-store-stream-analytics/create.input.3.png "Fournir des paramètres de stockage blob")

    * **Entrée un alias d’entrée**. Il s’agit d’un nom unique que vous fournissez pour le travail d’entrée.
    * **Sélectionnez un compte de stockage**. Assurez-vous que le compte de stockage se trouve dans la région même en tant que la tâche de flux de données Analytique ou vous entraînera des frais supplémentaires de déplacement des données entre les régions.
    * **Fournir un conteneur de stockage**. Vous pouvez choisir créer un nouveau conteneur ou sélectionnez un conteneur existant.

    Cliquez sur la flèche droite.

5. Dans la page **paramètres de sérialisation** , définissez le format de sérialisation **CSV**, délimiteur d' **onglet**, codage **UTF8**, puis sur la coche.

    ![Fournissez les paramètres de sérialisation] (./media/data-lake-store-stream-analytics/create.input.4.png "Fournissez les paramètres de sérialisation")

6. Une fois que vous avez terminé avec l’Assistant, l’entrée blob sera ajoutée sous l’onglet **entrées** et la colonne de **diagnostic** doit figurer **OK**. Vous pouvez également explicitement tester la connexion à l’entrée en utilisant le bouton **Tester la connexion** dans la partie inférieure.

## <a name="create-a-data-lake-store-output-for-the-job"></a>Créer un données Lake stocker les résultats de la tâche

1. Ouvrez la page de la tâche de flux de données Analytique et cliquez sur l’onglet **sorties** , puis cliquez sur **Ajouter un résultat** pour lancer l’Assistant.

2. Dans la page **Ajouter une sortie à votre travail** , sélectionnez **Données Lake Store**, puis cliquez sur la flèche droite.

    ![Ajouter une sortie à votre travail] (./media/data-lake-store-stream-analytics/create.output.1.png "Ajouter une sortie à votre travail")

3. Dans la page de **connexion autoriser** , si vous avez déjà créé un compte de données Lake Store, cliquez sur **Autoriser maintenant**. Dans le cas contraire, cliquez sur **s’inscrire** pour créer un nouveau compte. Dans ce didacticiel, supposons que vous possédez déjà un compte de données Lake Store créé (comme indiqué dans les conditions préalables). Vous sera automatiquement autorisé à l’aide des informations d’identification avec laquelle vous vous connectez au portail classique Azure.

    ![Autorisez les données Lake Store] (./media/data-lake-store-stream-analytics/create.output.2.png "Autorisez les données Lake Store")

4. Dans la page **Paramètres du magasin de données Lake** , entrez les informations comme indiqué dans la capture d’écran ci-dessous.

    ![Paramètres du magasin Lake spécifier des données] (./media/data-lake-store-stream-analytics/create.output.3.png "Paramètres du magasin Lake spécifier des données")

    * **Entrée un alias de sortie**. Il s’agit d’un nom unique que vous fournissez pour la sortie des tâches.
    * **Spécifier un compte de données Lake Store**. Vous devez avez déjà créé, comme indiqué dans les conditions préalables.
    * **Spécifier un motif de préfixe chemin d’accès**. Cela est nécessaire pour identifier les fichiers de sortie sont écrits sur données Lake Store par la tâche de flux de données Analytique. Étant donné que les titres des sorties rédigés par la tâche se trouvent dans un format de GUID, y compris un préfixe pour aider à identifier la sortie écrite. Si vous souhaitez inclure la date et l’heure dans le cadre du préfixe veillez à inclure `{date}/{time}` dans le modèle de préfixe. Si vous incluez cela, les champs **Date **et **Heure Format** sont activés et vous pouvez sélectionner le format de choix.

    Cliquez sur la flèche droite.

5. Dans la page **paramètres de sérialisation** , définissez le format de sérialisation **CSV**, délimiteur d' **onglet**, codage **UTF8**, puis sur la coche.

    ![Spécifier le format de sortie] (./media/data-lake-store-stream-analytics/create.output.4.png "Spécifier le format de sortie")

6. Une fois que vous avez terminé avec l’Assistant, le résultat de la banque d’informations données Lake sera ajouté sous l’onglet **sorties** et la colonne de **diagnostic** doit figurer **OK**. Vous pouvez également explicitement tester la connexion à la sortie en utilisant le bouton **Tester la connexion** dans la partie inférieure.

## <a name="run-the-stream-analytics-job"></a>Exécuter la tâche de flux de données Analytique

Pour exécuter une tâche de flux de données Analytique, vous devez exécuter une requête à partir de l’onglet requête. Pour ce didacticiel, vous pouvez exécuter l’exemple de requête en remplaçant les espaces réservés par la tâche d’entrée et de sortie alias, comme le montre la capture d’écran ci-dessous.

![Exécuter la requête] (./media/data-lake-store-stream-analytics/run.query.png "Exécuter la requête")

Cliquez sur **Enregistrer** à partir du bas de l’écran, puis cliquez sur **Démarrer**. Dans la boîte de dialogue Sélectionnez **Heure personnalisée**et puis sélectionnez une date dans le passé, telles que **1/1/2016**. Activez la case à cocher pour démarrer le travail. Il peut prendre jusqu'à quelques minutes pour démarrer la tâche.

![Définir la durée de la tâche] (./media/data-lake-store-stream-analytics/run.query.2.png "Définir la durée de la tâche")

Après le démarrage de la tâche, cliquez sur l’onglet **Moniteur** pour voir comment les données a été traitées.

![Travail de moniteur] (./media/data-lake-store-stream-analytics/run.query.3.png "Travail de moniteur")

Enfin, vous pouvez utiliser le [Portail Azure](https://portal.azure.com) pour ouvrir votre compte données Lake Store et vérifiez si les données ont été correctement écrites sur le compte.

![Sortie de vérifier] (./media/data-lake-store-stream-analytics/run.query.4.png "Sortie de vérifier")

Dans le volet Explorateur de données, notez que la sortie est écrite dans un dossier comme indiqué dans le magasin de Lake données de sortie (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Voir aussi

* [Créer un cluster HDInsight pour utiliser des données Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
