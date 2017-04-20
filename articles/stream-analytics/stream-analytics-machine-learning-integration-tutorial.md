<properties
    pageTitle="Analyse opinion à l’aide de Azure flux Analytique et apprentissage automatique Azure | Microsoft Azure"
    description="Comment utiliser une fonction définie par l’utilisateur et l’apprentissage automatique dans une tâche de flux de données Analytique"
    keywords=""
    documentationCenter=""
    services="stream-analytics"
    authors="jeffstokes72"
    manager="jhubbard"
    editor="cgronlun"
/>


<tags 
    ms.service="stream-analytics" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="10/04/2016" 
    ms.author="jeffstok"
/>

# <a name="sentiment-analysis-by-using-azure-stream-analytics-and-azure-machine-learning"></a>Analyse opinion à l’aide de Azure flux Analytique et apprentissage automatique Azure #

Cet article est conçu pour vous aider à configurer rapidement une tâche Azure flux Analytique simple, avec l’intégration d’apprentissage automatique Azure. Nous utiliser un modèle d’apprentissage automatique analytique opinion de la galerie de Intelligence Cortana pour analyser des données texte en continu et déterminer le score opinion en temps réel. Les informations dans cet article peuvent vous aider à comprendre les scénarios tels qu’analytique opinion en temps réel en continu les données Twitter, analyser les enregistrements des clients conversations avec personnel de support et évaluer les commentaires dans les forums, des blogs et des vidéos, en plus de nombreux autres en temps réel, prédictives score scénarios.

Cet article offre un exemple de fichier CSV avec du texte en tant qu’entrée dans le stockage Blob Azure indiqué dans l’image suivante. Le travail s’applique le modèle analytique opinion dans une fonction définie par l’utilisateur (UDF) dans les exemples de données de texte à partir du magasin d’objets blob. Le résultat final est placé dans le magasin d’objets blob même dans un autre fichier CSV. 

![Flux Analytique Machine apprentissage](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

L’image suivante illustre cette configuration. Dans un scénario plus plausible, vous pouvez remplacer stockage Blob avec la diffusion en continu de données Twitter à partir d’une entrée Azure événement Hubs. En outre, vous pouvez créer une visualisation en temps réel [Microsoft Power BI](https://powerbi.microsoft.com/) de l’agrégation opinion.    

![Flux Analytique Machine apprentissage](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Conditions préalables

Les conditions préalables pour effectuer les tâches qui sont présentés dans cet article sont les suivantes :

-   Un abonnement Azure actif.
-   Un fichier CSV contenant des données qu’il contient. Vous pouvez télécharger le fichier illustré Figure 1 à partir de [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample Data/sampleinput.csv), ou vous pouvez créer votre propre fichier. Pour cet article, nous part du principe que vous utilisez celui qui est fourni gratuitement sur GitHub.

À un niveau élevé, pour effectuer les tâches décrites dans cet article, vous devez procédez comme suit :

1.  Téléchargez le fichier d’entrée CSV sur stockage d’objets Blob Azure.
2.  Ajouter un modèle analytique opinion de la galerie de Intelligence Cortana dans votre espace de travail d’apprentissage automatique Azure.
3.  Déployer ce modèle comme un service web dans l’espace de travail d’apprentissage automatique.
4.  Créer une tâche de flux Analytique qui appelle ce service web comme une fonction, afin de déterminer opinion pour la saisie de texte.
5.  Démarrer la tâche de flux de données Analytique et observez la sortie.

## <a name="upload-the-csv-input-file-to-blob-storage"></a>Télécharger le fichier d’entrée CSV à Blob storage

Pour cette étape, vous pouvez utiliser n’importe quel fichier CSV, tel que celui déjà spécifié en tant que disponible au téléchargement sur GitHub. Vous pouvez utiliser [l’Explorateur de stockage Azure](http://storageexplorer.com/) ou Visual Studio à télécharger le fichier, ou vous pouvez utiliser un code personnalisé. Nous utilisons exemples basés sur Visual Studio.

1.  Dans Visual Studio, cliquez sur **Azure** > **stockage** > **Stockage externe joindre**. Entrez un **nom de compte** et une **clé de compte**.  

    ![Flux Analytique Machine Learning, Explorateur de serveurs](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-server-explorer.png)  

2.  Pour augmenter le stockage que vous avez joint à l’étape 1 et cliquez sur **Créer un conteneur Blob**, puis entrez un nom logique. Après avoir créé le conteneur, ouvrez-le pour afficher son contenu. (Il sera vide à ce stade).  

    ![Flux d’apprentissage automatique Analytique, créer des objets blob](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-create-blob.png)  

3.  Pour télécharger le fichier CSV, cliquez sur **Télécharger Blob**, puis cliquez sur **fichier à partir du disque local**.  

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Ajouter le modèle analytique opinion de la galerie de Intelligence Cortana

1.  Télécharger le [modèle d’analytique opinion prédictive](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) depuis la galerie de Intelligence Cortana.  
2.  Dans Studio d’apprentissage Machine, cliquez sur **Ouvrir dans Studio**.  

    ![Flux d’apprentissage automatique d’Analytique, ouvrir Machine apprentissage Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3.  Se connecter accéder à l’espace de travail. Sélectionnez l’emplacement qui correspond le mieux à votre propre emplacement.
4.  Cliquez sur **exécuter** dans la partie inférieure de la page.  
5.  Une fois le processus s’exécute correctement, cliquez sur **Déployer un Service Web**.
6.  Le modèle d’analytique opinion est prêt à utiliser. Pour valider, cliquez sur le bouton de **Test** et fournir l’entrée de texte, telles que « J’aime Microsoft ». Le test doit renvoyer un résultat semblable à ce qui suit :

`'Predictive Mini Twitter sentiment analysis Experiment' test returned ["4","0.715057671070099"]...`  

![Apprentissage automatique de flux Analytique, les données d’analyse](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-analysis-data.png)  

Dans la colonne **applications** , cliquez sur le lien pour **Excel 2010 ou un classeur antérieur** obtenir votre clé d’API et l’URL que vous aurez besoin ultérieurement pour configurer la tâche de flux de données Analytique. (Cette étape est nécessaire uniquement pour utiliser un modèle d’apprentissage automatique à partir d’un autre espace de travail compte Azure. Cet article suppose que c’est le cas, pour résoudre ce scénario.)  

Notez l’URL et accès clé de service web à partir du fichier Excel téléchargé, comme indiqué ci-dessous :  

![Apprentissage automatique de flux Analytique, coup de œil](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  

## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Créer une tâche de flux de données Analytique qui utilise le modèle d’apprentissage automatique

1.  Accédez au [portail Azure](https://manage.windowsazure.com).  
2.  Cliquez sur **Nouveau** > **Services Business Data** > **flux Analytique** > **Création rapide**. Entrez un nom pour votre projet dans le **Nom de la tâche**, entrez la région appropriée pour le travail dans **la région**et puis sélectionnez le compte dans le **Compte de stockage de surveillance régionaux**.    
3.  Une fois que le travail est créé, sous l’onglet **entrées** , cliquez sur **Ajouter une entrée**.  

    ![Flux d’apprentissage automatique Analytique, ajouter entrée d’apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-input-screen.png)  

4.  Sur la première page de l’Assistant **Ajouter une entrée** , cliquez sur **flux de données**, puis cliquez sur **suivant**. Dans la page suivante, cliquez sur le **Stockage d’objets Blob** comme entrée, puis cliquez sur **suivant**.  
5.  Dans la page **Paramètres du stockage Blob** de l’Assistant, indiquez le nom de conteneur de compte de stockage blob défini précédemment lorsque vous téléchargez les données. Cliquez sur **suivant**. Pour le **Format de sérialisation événement**, cliquez sur **CSV**. Acceptez les valeurs par défaut pour le reste de la page **paramètres de sérialisation** . Cliquez sur **OK**.  
6.  Sous l’onglet **sorties** , cliquez sur **Ajouter un résultat**.  

    ![Flux d’apprentissage automatique Analytique, ajoutez sortie](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-output-screen.png)  

7.  Cliquez sur le **Stockage d’objets Blob**, puis entrez les mêmes paramètres, à l’exception du conteneur. La valeur pour **l’entrée** a été configurée pour lire à partir du conteneur nommé « test » où le fichier **CSV** a été téléchargé. Pour la **sortie**, entrez « RésultatTest ». Noms de conteneurs doivent être différents. Vérifier l’existence de ce conteneur.     
8.  Cliquez sur **suivant** pour configurer des **paramètres de sérialisation**de sortie. Comme avec **Saisie**, cliquez sur **CSV**, puis cliquez sur le bouton **OK** .
9.  Sous l’onglet **fonctions** , cliquez sur **Ajouter une fonction d’apprentissage automatique**.  

    ![Flux d’apprentissage automatique Analytique, ajouter une fonction d’apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-add-ml-function.png)  

10. Dans la page **Paramètres du Service Web apprentissage Machine** , recherchez l’espace de travail apprentissage automatique, le service web et point de terminaison par défaut. Pour cet article, appliquez les paramètres manuellement pour se familiariser avec la configuration d’un service web pour un espace de travail, tant que vous connaissez pas l’URL et que vous disposez de la clé de l’API. Entrez le point de terminaison **URL** et la **clé de l’API**. Cliquez sur **OK**.    

    ![Apprentissage automatique de flux Analytique, service web apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-web-service.png)    

11. Sous l’onglet **requête** , modifiez la requête comme suit :    

 ```
    WITH subquery AS (  
        SELECT text, sentiment(text) as result from input  
    )  
 
    Select text, result.[Scored Labels]  
    Into output  
    From subquery  
 ```    
12. Cliquez sur **Enregistrer** pour enregistrer la requête.

## <a name="start-the-stream-analytics-job-and-observe-the-output"></a>Démarrer la tâche de flux de données Analytique et observez la sortie

1.  Cliquez sur **Démarrer** en bas de la page de travail.
2.  Dans la **Boîte de dialogue requête démarrer**, cliquez sur **Heure personnalisée**, puis sélectionnez puis de temps avant lorsque vous téléchargez la CSV dans stockage d’objets Blob. Cliquez sur **OK**.  

    ![Flux Analytique Machine Learning, heure personnalisée](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-custom-time.png)  

3.  Atteindre le stockage Blob à l’aide de l’outil que vous permet de télécharger le fichier CSV, par exemple, Visual Studio.
4.  Quelques minutes après que le travail est démarré, le conteneur de sortie est créé et un fichier CSV est téléchargé vers celui-ci.  
5.  Ouvrez le fichier dans l’éditeur CSV par défaut. Doit ressembler à ce qui suit doit s’afficher :  

    ![Afficher les flux Analytique Machine Learning, CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  

## <a name="conclusion"></a>Conclusion

Cet article explique comment créer une tâche de flux de données Analytique qui lit les données de texte en continu et applique analytique opinion aux données en temps réel. Vous pouvez effectuer tout cela sans avoir à vous soucier de la complexité de la création d’un modèle analytique opinion. Il s’agit d’un des avantages de la Suite Intelligence Cortana.

Vous pouvez également afficher les mesures de liés à la fonction d’apprentissage automatique Azure. Pour ce faire, cliquez sur l’onglet **Moniteur** . Trois indicateurs liés à la fonction sont affichées.  

- **Fonction demandes** indique le nombre de requêtes envoyées à un service web apprentissage automatique.  
- **Fonction événements** indique le nombre d’événements dans la demande. Par défaut, chaque demande à un service web apprentissage automatique contient jusqu'à 1 000 événements.  
- **Échec de la fonction demandes** indique le nombre d’échecs de requêtes au service web apprentissage automatique.  

    ![Apprentissage automatique de flux Analytique, affichage du moniteur apprentissage automatique](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-ml-monitor-view.png)  
