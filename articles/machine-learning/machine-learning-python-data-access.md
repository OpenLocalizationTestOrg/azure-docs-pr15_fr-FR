<properties 
    pageTitle="Accéder aux groupes de données avec une bibliothèque client Machine apprentissage Python | Microsoft Azure" 
    description="Installer et utiliser la bibliothèque de client Python pour accéder et gérer les données d’apprentissage automatique Azure en toute sécurité à partir d’un environnement Python local." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Jeux de données Access avec Python à l’aide de la bibliothèque de client Azure Machine apprentissage Python 

L’aperçu d’une bibliothèque de client Microsoft Azure Machine apprentissage Python pouvez activer un accès sécurisé pour les groupes de données de votre apprentissage automatique Azure à partir d’un environnement Python local et permet la création et la gestion des groupes de données dans un espace de travail.

Cette rubrique fournit des instructions sur la façon :

* installer la bibliothèque de client Machine apprentissage Python 
* accéder et télécharger des jeux de données, y compris les instructions sur la façon d’obtenir l’autorisation pour accéder aux jeux de données Azure Machine apprentissage à partir de votre environnement Python local
*  accéder aux jeux de données intermédiaires à partir d’expériences
*  utiliser la bibliothèque de client Python pour énumérer les jeux de données, accéder aux métadonnées, lire le contenu d’un jeu de données, créer de nouveaux jeux de données et mettre à jour les jeux de données existante

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Conditions préalables

La bibliothèque cliente Python a été testée sous les environnements suivants :

 - Windows, Mac et Linux
 - Python 2.7, 3.3 et 3.4

Il a une dépendance sur les packages suivants :

 - demandes
 - Python dateutil
 - pandas

Nous vous recommandons d’à l’aide d’une distribution Python tels que [Anaconda](http://continuum.io/downloads#all) ou [toile](https://store.enthought.com/downloads/)qui accompagnent Python, IPython et les trois packages énumérés installée. IPython n’est pas strictement requis, mais il est un excellent environnement permettant de manipuler et de visualisation des données de façon interactive.


###<a name="installation"></a>L’installation de la bibliothèque de client Azure Machine apprentissage Python

La bibliothèque de client Azure Machine apprentissage Python également doit être installée pour effectuer les tâches décrites dans cette rubrique. Il est disponible à partir de l' [Index de Package Python](https://pypi.python.org/pypi/azureml). Pour l’installer dans votre environnement Python, exécutez la commande suivante à partir de votre environnement Python local :

    pip install azureml

Par ailleurs, vous pouvez télécharger et installer à partir des sources sur [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Si vous avez git installé sur votre ordinateur, vous pouvez utiliser pip pour installer directement à partir du référentiel git :

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Extraits de Code Studio permet d’accéder aux groupes de données

La bibliothèque cliente Python permet d’accéder par programme à vos groupes de données existante à partir d’expériences qui ont été exécutés.

À partir de l’interface web Studio, vous pouvez générer des extraits de code qui incluent toutes les informations nécessaires pour télécharger et désérialiser jeux de données sous forme d’objets Pandas DataFrame sur votre ordinateur de l’emplacement.

### <a name="security"></a>Sécurité pour accéder aux données

Extraits de code fournis par Studio pour les utiliser avec la bibliothèque de client Python qui incluent votre id de l’espace de travail et l’autorisation jeton. Ces permettent d’accéder à votre espace de travail et doivent être protégées, comme un mot de passe.

Pour des raisons de sécurité, la fonctionnalité d’extrait de code est uniquement disponible pour les utilisateurs qui ont leur rôle définir en tant que **propriétaire** de l’espace de travail. Votre rôle est affiché dans Azure Machine apprentissage Studio dans la page **utilisateurs** sous **paramètres**.

![Sécurité][security]

Si votre rôle n’est pas définie en tant que **propriétaire**, vous pouvez soit demander à la nouvelle en tant que propriétaire ou demandez au propriétaire de l’espace de travail afin d’améliorer l’extrait de code.

Pour obtenir le jeton d’autorisation, vous pouvez effectuer une des opérations suivantes :



- Demandez à un jeton d’un propriétaire. Accès aux propriétaires jetons d’autorisation à partir de la page Paramètres de son espace de travail dans Studio. Sélectionnez **paramètres** dans le volet gauche, puis cliquez sur **Autorisation jetons** pour afficher les jetons principales et secondaires.  Bien que les jetons d’autorisation secondaire ou principal peuvent être utilisées dans l’extrait de code, il est recommandé que les propriétaires de partagent uniquement les jetons secondaire d’autorisation.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Demandez à être promue au rôle du propriétaire.  Pour ce faire, un propriétaire actuel de l’espace de travail doit d’abord vous supprimer de l’espace de travail puis vous Réinviter lui en tant que propriétaire.

Une fois que les développeurs ont obtenu l’id de l’espace de travail et l’autorisation jeton, ils sont en mesure d’accéder à l’espace de travail à l’aide de l’extrait de code quelle que soit leur rôle.

Jetons d’autorisation sont gérés dans la page **D’autorisations jetons** sous **paramètres**. Vous pouvez le restaurer, mais cette procédure retire accès au jeton précédent.

### <a name="accessingDatasets"></a>Jeux de données Access à partir d’une application locale Python

1. Dans Studio d’apprentissage Machine, cliquez sur **jeux de données** dans la barre de navigation gauche.

2. Sélectionnez le jeu de données que vous voulez accéder. Vous pouvez sélectionner un des jeux de données à partir de la liste **Mes jeux de données** ou à partir de la liste des **exemples** .

3. Dans la barre d’outils en bas, cliquez sur **Générer un Code d’accès aux données**. Si les données sont dans un format compatible avec la bibliothèque de client Python, ce bouton est désactivé.

    ![Jeux de données][datasets]

4. Sélectionnez l’extrait de code dans la fenêtre qui s’affiche et le copie dans le Presse-papiers.

    ![Code d’accès][dataset-access-code]

5. Collez le code dans le bloc-notes de votre application Python local.

    ![Bloc-notes][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Jeux de données intermédiaires Access à partir des expériences d’apprentissage automatique

Après qu’une expérience est exécutée dans Studio apprentissage Machine, il est possible d’accéder aux jeux de données intermédiaires des nœuds de sortie des modules. Jeux de données intermédiaires est des données qui a été créées et utilisées pour étapes intermédiaires lorsqu’un outil de modèle a été exécuté.

Jeux de données intermédiaires sont accessibles dans la mesure où le format de données est compatible avec la bibliothèque de client Python.

Les formats suivants sont pris en charge (constantes pour ces se trouvent dans la `azureml.DataTypeIds` classe) :

 - Texte brut
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Vous pouvez déterminer le format en pointant sur un nœud de sortie de module. Il s’affiche ainsi que le nom du nœud, dans une info-bulle.

Certains des modules, tels que le [fractionnement] [ split] module, de sortie dans un format nommé `Dataset`, qui n’est pas pris en charge par la bibliothèque cliente Python.

![Format de jeu de données][dataset-format]

Vous devez utiliser un module de conversion, par exemple [convertir au format CSV][convert-to-csv], afin d’obtenir une sortie dans un format pris en charge.

![Format GenericCSV][csv-format]

Les étapes suivantes montrent un exemple qui crée une expérience, exécute et accède à ce jeu de données.

1. Créer une nouvelle expérience.

2. Insérer un module **gros recensement revenus binaire Classification dataset** .

3. Insérer un [fractionnement] [ split] module et vous connecter son entrée à la sortie de module dataset.

4. Insérer un [convertir au format CSV] [ convert-to-csv] module et se connecter son entrée à l’un du [fractionnement] [ split] module sorties.

5. Enregistrer l’expérience, exécutez-le et attendre qu’elle se termine en cours d’exécution.

6. Cliquez sur le nœud de sortie sur [convertir au format CSV] [ convert-to-csv] module.

7. Lorsque le menu contextuel s’affiche, sélectionnez **Générer un Code d’accès aux données**.

    ![Menu contextuel][experiment]

8. Sélectionnez l’extrait de code et copiez-le dans votre Presse-papiers à partir de la fenêtre qui s’affiche.

    ![Code d’accès][intermediate-dataset-access-code]

9. Collez le code dans votre bloc-notes.

    ![Bloc-notes][ipython-intermediate-dataset]

10. Vous pouvez visualiser les données à l’aide de matplotlib. Cela permet d’afficher dans un histogramme pour la colonne Durée de vie :

    ![Histogramme][ipython-histogram]


##<a name="clientApis"></a>Utiliser la bibliothèque de client Machine apprentissage Python pour accéder, lire, créer et gérer des groupes de données

### <a name="workspace"></a>Espace de travail

L’espace de travail est le point d’entrée pour la bibliothèque cliente Python. Fournir la `Workspace` classe avec votre id de l’espace de travail et l’autorisation des jetons pour créer une instance :

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Énumérer les jeux de données

Pour énumérer tous les groupes de données dans un espace de travail donné :

    for ds in ws.datasets:
        print(ds.name)

Énumérer les simplement les datasets créés par l’utilisateur :

    for ds in ws.user_datasets:
        print(ds.name)

Énumérer les simplement les jeux de données d’exemple :

    for ds in ws.example_datasets:
        print(ds.name)

Vous pouvez accéder à un jeu de données par nom (qui respecte la casse) :

    ds = ws.datasets['my dataset name']

Ou vous pouvez y accéder par index :

    ds = ws.datasets[0]


### <a name="metadata"></a>Métadonnées

Jeux de données ont des métadonnées, en plus du contenu. (Jeux de données intermédiaires est une exception à cette règle et n’ont pas toutes les métadonnées.)

Certaines valeurs de métadonnées affectés par l’utilisateur au moment de la création :

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

D’autres personnes sont affectées par ML Azure des valeurs :

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Voir la `SourceDataset` cours pour plus d’informations sur les métadonnées disponibles.


### <a name="read-contents"></a>Lire le contenu

Extraits de code fournis par Machine apprentissage Studio automatiquement téléchargement et désérialiser le jeu de données à un objet Pandas DataFrame. Pour cela, avec la `to_dataframe` méthode :

    frame = ds.to_dataframe()

Si vous préférez télécharger les données brutes et effectuer la désérialisation vous-même, qui est une option. Pour le moment, il s’agit de la seule option disponible pour les formats tels que « ARFF », la bibliothèque cliente Python ne peut pas désérialiser.

Pour lire le contenu sous forme de texte :

    text_data = ds.read_as_text()

Pour lire le contenu en tant que fichier binaire :

    binary_data = ds.read_as_binary()

Vous pouvez également ouvrir un flux de données au contenu :

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Créer un nouveau groupe de données

La bibliothèque cliente Python vous permet de télécharger des jeux de données à partir de votre programme Python. Ces jeux de données est ensuite disponibles à utiliser dans votre espace de travail.

Si vous avez vos données dans un DataFrame Pandas, utilisez le code suivant :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Si vos données sont déjà étées, vous pouvez utiliser :

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

La bibliothèque cliente Python ne peut pas sérialiser un DataFrame Pandas aux formats suivants (constantes pour ces se trouvent dans la `azureml.DataTypeIds` classe) :

 - Texte brut
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Mettre à jour un dataset existant

Si vous essayez de télécharger un nouveau groupe de données avec un nom qui correspond à un dataset existant, vous devez obtenir une erreur de conflit.

Pour mettre à jour un dataset existant, vous devez tout d’abord obtenir une référence au dataset existant :

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Utilisez ensuite `update_from_dataframe` pour sérialiser et remplacez le contenu du dataset sur Azure :

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Si vous voulez sérialiser les données dans un format différent, spécifiez une valeur pour l’option `data_type_id` paramètre.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Vous pouvez éventuellement définir une nouvelle description en spécifiant une valeur pour le `description` paramètre.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Vous pouvez éventuellement définir un nouveau nom en spécifiant une valeur pour le `name` paramètre. Dès lors, vous allez récupérer le jeu de données en utilisant uniquement le nouveau nom. Le code suivant met à jour les données, nom et une description.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

La `data_type_id`, `name` et `description` paramètres sont facultatifs et par défaut à leur valeur précédente. La `dataframe` paramètre est toujours requis.

Si vos données sont déjà étées, utilisez `update_from_raw_data` au lieu de `update_from_dataframe`. Si vous passez simplement `raw_data` au lieu de `dataframe`, elle fonctionne de façon similaire.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
