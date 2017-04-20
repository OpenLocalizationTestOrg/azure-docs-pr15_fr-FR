<properties 
    pageTitle="Scientifique de données sur la Machine virtuelle Linux données scientifique | Microsoft Azure" 
    description="Comment effectuer plusieurs tâches communes données scientifique avec la virtuelle Linux données scientifique." 
    services="machine-learning"
    documentationCenter="" 
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
    ms.author="bradsev;paulsh" />


# <a name="data-science-on-the-linux-data-science-virtual-machine"></a>Scientifique de données sur la Machine virtuelle Linux données scientifique

Cette procédure vous montre comment effectuer plusieurs tâches communes données scientifique avec la virtuelle Linux données scientifique. La Machine virtuelle de scientifique des données de Linux (DSVM) est une image de machine virtuelle disponible sur Azure est préinstallé avec un ensemble d’outils fréquemment utilisées pour analytique de données et d’apprentissage automatique. Les composants logiciels clés sont répertoriés dans la rubrique [mise en service de la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-intro.md) . L’image de la machine virtuelle facilite la prise en main effectuant scientifique de données en quelques minutes, sans avoir à installer et configurer individuellement chacun des outils. Vous pouvez facilement évoluer la machine virtuelle, si nécessaire et l’arrêter lorsqu’elles pas en cours d’utilisation. Cette ressource est donc élastique et rentables. 

Les tâches scientifique données illustré dans cette procédure pas à pas suivre les étapes décrites dans le [Processus de scientifique des données d’équipe](https://azure.microsoft.com/documentation/learning-paths/data-science-process/). Ce processus fournit une approche systématique scientifique de données qui permet aux équipes de scientifiques de données pour collaborer efficacement sur le cycle de vie de développement d’applications intelligentes. Le processus scientifique données fournit également un cadre itératif pour scientifique de données qui peut être suivi par une personne.

Nous analyser le dataset [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) dans cette procédure. Il s’agit d’un ensemble de messages électroniques qui sont marqués comme courrier indésirable ou jambon (ce qui signifie qu’ils ne sont pas le courrier indésirable), et contient également des statistiques sur le contenu des e-mails. Les statistiques inclus sont présentées dans l’autre, mais une section. 


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir utiliser une Machine virtuelle Linux données scientifique, vous devez disposer des éléments suivants :

- Un **abonnement Azure**. Si vous n’en avez pas déjà, voir [créer votre compte Azure gratuit aujourd'hui](https://azure.microsoft.com/free/).
- Une [**science de données Linux machine virtuelle**](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm). Pour plus d’informations sur la mise en service de cet ordinateur virtuel, voir [mise en service de la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-intro.md). 
- [X2Go](http://wiki.x2go.org/doku.php) installé sur votre ordinateur et ouvert une session de XFCE. Pour plus d’informations sur l’installation et configuration d’un **client X2Go**, consultez [installation et configuration X2Go client](machine-learning-data-science-linux-dsvm-intro.md#Installing-and-configuring-X2Go-client). 
- Un **compte AzureML**. Si vous n’avez pas encore, s’inscrire à un nouveau à [page d’accueil AzureML](https://studio.azureml.net/). Il existe un niveau de l’utilisation gratuite pour vous aider à commencer.


## <a name="download-the-spambase-dataset"></a>Télécharger le dataset spambase

Le dataset [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) est un ensemble de données qui contient uniquement 4601 exemples relativement réduit. Il s’agit d’une taille pratique à utiliser lors de la démonstration que les principales fonctionnalités de la machine virtuelle scientifique données telle qu’elle conserve les besoins en ressources limités.

>[AZURE.NOTE] Cette procédure pas à pas a été créé sous une D2 Tabloïd v2 données scientifique Machine virtuelle Linux. Cette taille DSVM est capable de gérer les procédures décrites dans cette procédure pas à pas.

Si vous avez besoin d’espace de stockage, vous pouvez créer des disques supplémentaires et associez-les à votre machine virtuelle. Ces disques utilisent stockage Azure permanent, afin que leurs données sont conservées même si le serveur est configuré en raison de redimensionnement ou est arrêté. Pour ajouter un disque et joindre à votre machine virtuelle, suivez les instructions dans [Ajouter un disque pour une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-add-disk.md). Ces étapes utilisent l’Interface de ligne d’Azure (Azure commande), qui est déjà installé sur le DSVM. Si ces procédures peuvent être effectuées entièrement à partir de la machine virtuelle elle-même. Une autre option pour augmenter le stockage consiste à utiliser les [fichiers Azure](../storage/storage-how-to-use-files-linux.md).

Pour télécharger les données, ouvrez une fenêtre de terminal et exécutez la commande suivante :

    wget http://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data

Le fichier téléchargé n’a pas une ligne d’en-tête, donc nous allons créer un autre fichier qui a d’en-tête. Exécutez la commande suivante pour créer un fichier avec les en-têtes appropriés :

    echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers

Puis effectuera les deux fichiers avec la commande :

    cat spambase.data >> headers
    mv headers spambaseHeaders.data

Le jeu de données comporte plusieurs types de statistiques sur tous les messages électroniques : 

- Colonnes comme ***word\_fréquence\_WORD*** indiquent le pourcentage de mots dans le message électronique qui correspondent à *WORD*. Par exemple, si *word\_fréquence\_rendre* correspond à 1, puis 1 % de tous les mots dans le message a été *apporter*. 
- Colonnes comme ***car\_fréquence\_car*** indiquent le pourcentage de tous les caractères dans le message électronique qui ont été *car*. 
- ***majuscule\_exécuter\_longueur\_plus longue*** correspond à la longueur de la plus longue d’une séquence de lettres majuscules. 
- ***majuscule\_exécuter\_longueur\_moyenne*** est la durée moyenne de toutes les séquences de majuscules. 
- ***majuscule\_exécuter\_longueur\_total*** est la longueur totale de toutes les séquences de majuscules. 
- ***le courrier indésirable*** indique si le message a été considéré comme du courrier indésirable ou non (1 = le courrier indésirable, 0 ne = pas du courrier indésirable).


## <a name="explore-the-dataset-with-microsoft-r-open"></a>Explorer le dataset avec Microsoft R ouvert

Nous allons examiner les données et effectuer certaines base machine d’apprentissage avec R. La machine virtuelle des données est fourni avec [Microsoft R ouvrir](https://mran.revolutionanalytics.com/open/) préinstallés. Les bibliothèques multithread mathématiques dans cette version de R offrent des performances supérieures à celles de différentes versions de thread unique. Ouvrir Microsoft R fournit également reproductibilité en utilisant un instantané du référentiel de package CRAN.

Pour obtenir des copies des exemples de code utilisés dans cette procédure pas à pas, cloner le référentiel **Science du données apprentissage Azure Machine** à l’aide de git, qui est préinstallé sur l’ordinateur virtuel. À partir de la ligne de commande git, exécutez :

    git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git

Ouvrir une fenêtre terminal et commencer une nouvelle session R avec la console interactive R.

>[AZURE.NOTE] Vous pouvez également utiliser RStudio pour les procédures suivantes. Pour installer RStudio, exécutez la commande suivante dans un terminal :`./Desktop/DSVM\ tools/installRStudio.sh`

Pour importer les données et configurer l’environnement, exécutez :

    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

Pour afficher le résumé des statistiques sur chaque colonne :

    summary(data)

Pour un autre affichage des données :

    str(data)

Ceci affiche le type de chaque variable et la première peu de valeurs dans le groupe de données. 

La colonne de *courrier indésirable* a été lu sous forme d’entier, mais il est en réalité un par catégorie variable (ou facteurs). Pour définir son type :

    data$spam <- as.factor(data$spam)

Pour effectuer une analyse exploratoire, utilisez le package [ggplot2](http://ggplot2.org/) , une bibliothèque de graphique populaires pour R est déjà installé sur l’ordinateur virtuel. Remarque, à partir des données de synthèse affichées plus tôt, que nous avons résumé des statistiques sur la fréquence du caractère point d’exclamation. Nous allons tracer ces fréquence ici avec les commandes suivantes :

    library(ggplot2)
    ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Étant donné que la barre de zéro est inclinaison le tracé, nous allons vous débarrasser de celle-ci :

    email_with_exclamation = data[data$char_freq_exclamation > 0, ]
    ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Il existe une densité supérieure à 1 semble intéressant non ordinaire. Nous allons étudier simplement ces données :

    ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)

Puis la fractionner en ham vs de courrier indésirable :

    ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
    geom_density(lty=3) +
    geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
    xlab("spam") +
    ggtitle("Distribution of spam \nby frequency of !") +
    labs(fill="spam", y="Density")

Ces exemples devraient vous permettre de faire tracés similaires des autres colonnes pour Explorer les données contenues dans les.


## <a name="train-and-test-an-ml-model"></a>Former et tester un modèle ML

Maintenant nous allons former quelques modèles d’apprentissage machine pour classer les messages électroniques dans le groupe de données comme contenant une plage spécifique ou ham. Nous former un modèle d’arbre de décision et un modèle de forêt aléatoires dans cette section, puis vérifiez la précision de leurs prévisions. 

>[AZURE.NOTE] Le package rpart (récursive partition et arborescences de régression) utilisé dans le code suivant est déjà installé sur l’ordinateur virtuel scientifique de données.


Tout d’abord, nous allons fractionner le jeu de données en jeux de formation et de test :

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

Puis créer un arbre de décision pour classer les messages électroniques.

    require(rpart)
    model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
    plot(model.rpart)
    text(model.rpart)

Voici le résultat :

![1](./media/machine-learning-data-science-linux-dsvm-walkthrough/decision-tree.png)

Pour déterminer la qualité d’exécution sur l’ensemble d’apprentissage, utilisez le code suivant :

    trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
    t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Pour déterminer quelle mesure elle effectue de l’ensemble de test :

    testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy

Essayer également un modèle de forêt aléatoires. Forêts aléatoires former une multitude d’arborescences de décision et une classe qui est le mode des classifications à partir de toutes les arborescences de décision de sortie. Ils offrent un ordinateur plus puissant approche d’apprentissage comme elles corriger pour la tendance d’un modèle d’arbre de décision à overfit un jeu de données de formation. 

    require(randomForest)
    trainVars <- setdiff(colnames(data), 'spam')
    model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

    trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
    table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

    testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
    t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
    accuracy <- sum(diag(t))/sum(t)
    accuracy


## <a name="deploy-a-model-to-azure-ml"></a>Déployer un modèle à ML Azure

[Machine Azure Studio d’apprentissage](https://studio.azureml.net/) (AzureML) est un service cloud qui facilite la créer et déployer des modèles de prédictive analytique. Un des fonctionnalités piles de AzureML est sa capacité à publier n’importe quelle fonction R comme un service web. Le package AzureML R, déploiement facile de faire directement à partir de notre session R sur la DSVM. 

Pour déployer le code d’arbre de décision de la section précédente, vous devez vous connecter à Azure Machine apprentissage Studio. Vous avez besoin de votre ID d’espace de travail et un jeton d’autorisation à sigh dans. Pour rechercher ces valeurs et initialisation les variables AzureML avec eux :

Sélectionnez **paramètres** dans le menu de gauche. Notez votre **ID de l’espace de travail**. ![2](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-id.png)

Sélectionnez **L’autorisation jetons** dans le menu généraux et notez votre **Jeton principal d’autorisation**. ![3](./media/machine-learning-data-science-linux-dsvm-walkthrough/workspace-token.png)

Charger le package **AzureML** , puis définissez les valeurs des variables de votre ID de jeton et d’espace de travail dans votre session R sur la DSVM :


    require(AzureML)
    wsAuth = "<authorization-token>"
    wsID = "<workspace-id>"


Nous allons simplifier le modèle pour faciliter la mise en œuvre cette démonstration. Sélectionnez les trois variables dans l’arbre de décision plus proche de la racine et créer un organigramme nouveau en utilisant uniquement les trois variables :

    colNames <- c("char_freq_dollar", "word_freq_remove", "word_freq_hp", "spam")
    smallTrainSet <- trainSet[, colNames]
    smallTestSet <- testSet[, colNames]
    model.rpart <- rpart(spam ~ ., method = "class", data = smallTrainSet)

Nous avons besoin d’une fonction de prévision qui utilise les fonctionnalités comme entrée et retourne les valeurs prévues :

    predictSpam <- function(char_freq_dollar, word_freq_remove, word_freq_hp) {
        predictDF <- predict(model.rpart, data.frame("char_freq_dollar" = char_freq_dollar,
        "word_freq_remove" = word_freq_remove, "word_freq_hp" = word_freq_hp))
        return(colnames(predictDF)[apply(predictDF, 1, which.max)])
    }

Publier la fonction predictSpam sur AzureML à l’aide de la fonction **publishWebService** : 

    spamWebService <- publishWebService("predictSpam",
        "spamWebService",
        list("char_freq_dollar"="float", "word_freq_remove"="float","word_freq_hp"="float"),
        list("spam"="int"),
        wsID, wsAuth)

Cette fonction vous permet d’accéder la fonction **predictSpam** , crée un service web appelé **spamWebService** avec défini entrées et les sorties et renvoie des informations sur le nouveau point de terminaison.

Afficher les détails du service web publiés, y compris son point de terminaison API et accéder aux clés avec la commande :

    spamWebService[[2]]

Pour tester la première à la rubrique 10 lignes du test :

    consumeDataframe(spamWebService$endpoints[[1]]$PrimaryKey, spamWebService$endpoints[[1]]$ApiLocation, smallTestSet[1:10, 1:3])


## <a name="use-other-tools-available"></a>Utilisez les autres outils disponibles

Les sections suivantes montrent comment utiliser certains des outils installés sur l’ordinateur Linux données scientifique virtuel. Voici la liste des outils abordés :

- XGBoost
- Python
- Jupyterhub
- Hochet
- PostgreSQL et non SQL
- Data Warehouse de SQL Server


## <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) est un outil qui fournit une implémentation arborescence augmentée rapide et précise.

    require(xgboost)
    data <- read.csv("spambaseHeaders.data")
    set.seed(123)

    rnd <- runif(dim(data)[1])
    trainSet = subset(data, rnd <= 0.7)
    testSet = subset(data, rnd > 0.7)

    bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

    pred <- predict(bst, data.matrix(testSet[, 0:57]))
    accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
    print(paste("test accuracy = ", accuracy))

XGBoost peut également appeler python ou une ligne de commande.

## <a name="python"></a>Python

Pour le développement à l’aide de Python, les répartitions Python Anaconda 2.7 et 3.5 ont été installées dans le DSVM. 

>[AZURE.NOTE] La distribution Anaconda inclut [Condas](http://conda.pydata.org/docs/index.html), qui peuvent servir à créer des environnements personnalisés pour Python qui ont des versions différentes et/ou packages installés qu’ils.

Nous allons lu dans certaines du dataset spambase et classer les messages électroniques avec prise en charge des machines de vecteur dans scikit-en savoir plus :

    import pandas
    from sklearn import svm    
    data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
    X = data.ix[:, 0:57]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour réaliser des prévisions :

    clf.predict(X.ix[0:20, :])

Pour afficher la publication d’un point de terminaison AzureML, nous allons rendre un modèle plus simple trois variables comme nous l’avons fait quand nous publiée précédemment le modèle R. 

    X = data.ix[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
    y = data.ix[:, 57]
    clf = svm.SVC()
    clf.fit(X, y)

Pour publier le modèle dans AzureML :

    # Publish the model.
    workspace_id = "<workspace-id>"
    workspace_token = "<workspace-token>"
    from azureml import services
    @services.publish(workspace_id, workspace_token)
    @services.types(char_freq_dollar = float, word_freq_remove = float, word_freq_hp = float)
    @services.returns(int) # 0 or 1
    def predictSpam(char_freq_dollar, word_freq_remove, word_freq_hp):
        inputArray = [char_freq_dollar, word_freq_remove, word_freq_hp]
        return clf.predict(inputArray)

    # Get some info about the resulting model.
    predictSpam.service.url
    predictSpam.service.api_key

    # Call the model
    predictSpam.service(1, 1, 1)

>[AZURE.NOTE] Ceci est disponible uniquement pour les python 2.7 et n’est pas encore pris en charge sur 3.5. Exécuter avec **/anaconda/bin/python2.7**.


## <a name="jupyterhub"></a>Jupyterhub

La distribution Anaconda dans la DSVM est fourni avec un bloc-notes Jupyter, un environnement de disponibilité sur plusieurs plateformes pour partager des analyse et du code Python, R ou Julia. Le bloc-notes Jupyter est accessible via JupyterHub. Vous êtes connecté à l’aide de votre nom d’utilisateur Linux local et le mot de passe à ***https://\<nom VM DNS ou l’adresse IP\>: 8000 /***. Tous les fichiers de configuration pour JupyterHub sont trouvent dans le répertoire **/etc/jupyterhub**.

Plusieurs blocs-notes exemple sont déjà installés sur l’ordinateur virtuel :

- Voir [IntroToJupyterPython.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb) pour un bloc-notes Python exemple.
- Voir [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) pour un bloc-notes **R** exemple.
- Voir [IrisClassifierPyMLWebService](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IrisClassifierPyMLWebService.ipynb) pour un autre bloc-notes **Python** exemple.

>[AZURE.NOTE] La langue Julia est également disponible à partir de la ligne de commande sur l’ordinateur Linux données scientifique virtuel.


## <a name="rattle"></a>Hochet

[Vibrer](https://cran.r-project.org/web/packages/rattle/index.html) (la R analytique outil pour en savoir plus facilement) est un outil R graphique d’exploration de données. Il possède une interface intuitive qui facilite la charger, Explorer et transformer les données et créer et évaluer des modèles.  L’article [vibrer : graphique exploration de données A pour R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) fournit une procédure pas à pas qui montre ses fonctionnalités.

Installez et démarrez Hochet avec les commandes suivantes :

    if(!require("rattle")) install.packages("rattle")
    require(rattle)
    rattle()

>[AZURE.NOTE] L’installation n’est pas nécessaire sur le DSVM. Mais Hochet peut vous inviter à installer des packages supplémentaires lors du chargement.

Hochet utilise une interface basée sur un onglet. La plupart des onglets correspondent aux étapes dans le [Processus de scientifique des données](https://azure.microsoft.com/documentation/learning-paths/data-science-process/), comme le chargement des données ou d’exploration. Le processus scientifique données se déroule de gauche à droite parmi les onglets. Mais le dernier onglet contient un journal des commandes R exécuter en Hochet. 


Pour charger et configurer le jeu de données :

- Pour charger le fichier, cliquez sur l’onglet **données** , puis 
- Choisissez le sélecteur en regard du **nom de fichier** , puis **spambaseHeaders.data**. 
- Pour charger le fichier. Cliquez sur **exécuter** dans la ligne supérieure des boutons. Vous devez voir un résumé de chaque colonne, y compris son type de données identifié, qu’il s’agisse d’une entrée, une cible ou tout autre type de variable et le nombre de valeurs uniques.
- Hochet a identifié correctement la colonne **spam** comme cible. Sélectionnez la colonne du courrier indésirable, puis définissez le **Type de données cible** à **Categoric**.

Pour Explorer les données : 

- Sélectionnez l’onglet **Explorer** . 
- Cliquez sur **synthèse**, puis **Execute**, pour afficher des informations sur les types de variables et des statistiques de synthèse. 
- Pour afficher d’autres types de statistiques relatives à chaque variable, sélectionnez autres options, comme la **Description** ou **Concepts de base**.

L’onglet **Explorer** permet également vous permet de générer de nombreux tracés génèrent. Pour tracer un histogramme des données :


- Sélectionnez **la distribution**.
- Consultation de **histogramme** **word_freq_remove** et **word_freq_you**.
- Sélectionnez **exécuter**. Vous devriez voir les deux tracés densité dans une fenêtre graphique unique, où il est évident que le mot « vous » s’affiche beaucoup plus fréquemment dans des messages électroniques que « supprimer ».

Les corrélation tracés sont également intéressantes. Pour créer une :


- Choisissez **corrélation** comme **Type**, puis 
- Sélectionnez **exécuter**. 
- Hochet vous avertit qu’il recommande un maximum de 40 variables. Sélectionnez **Oui** pour afficher le tracé. 

Il existe certaines corrélations intéressantes poser : « technologies » est étroitement lié « HP » et « ateliers », par exemple. Il est également étroitement lié à « 650 », l’indicatif régional des donateurs dataset étant 650.

Les valeurs numériques pour les corrélations entre les mots sont disponibles dans la fenêtre d’exploration. Il est intéressant de note, par exemple, que « technologies » est en corrélation négative avec « votre » et « argent ».

Hochet pouvez transformer le jeu de données pour gérer les problèmes courants. Par exemple, il vous permet de mettre à l’échelle fonctionnalités, imputer les valeurs manquantes, gérer aberrantes et supprimer des variables ou des observations et données manquantes. Hochet peut également identifier des règles d’association entre observations et/ou variables. Ces onglets sortent du cadre de cette procédure pas à pas d’introduction.

Hochet peut effectuer également analyse cluster. Nous allons exclure certaines fonctionnalités pour faciliter la lecture de la sortie. Sous l’onglet **données** , sélectionnez **Ignorer** en regard de chacune des variables à l’exception de ces dix éléments :

- word_freq_hp
- word_freq_technology
- word_freq_george
- word_freq_remove
- word_freq_your
- word_freq_dollar
- word_freq_money
- capital_run_length_longest
- word_freq_business
- courrier indésirable

Puis accédez à l’onglet **Cluster** , choisissez **KMeans**, puis définissez le *nombre de clusters* à 4. Puis **exécuter**. Les résultats sont affichés dans la fenêtre de sortie. Un cluster a haute fréquence de « Georges » et « hp » et est probablement une adresse de messagerie légitime.

Pour créer un modèle d’apprentissage décision simple arborescence machine : 

- Cliquez sur l’onglet **modèle** , 
- Choisissez **arborescence** comme **Type**. 
- Sélectionnez **exécuter** pour afficher l’arborescence sous forme de texte dans la fenêtre de sortie. 
- Sélectionnez le bouton **dessiner** pour afficher une version graphique. Il est très similaire à l’arborescence que nous obtenues précédemment à l’aide de *rpart*.

Un des fonctionnalités piles de Hochet est sa capacité à exécuter plusieurs méthodes d’apprentissage automatique et les évaluer rapidement. Voici la procédure à suivre :

- Sélectionnez **tout** pour le **Type**. 
- Sélectionnez **exécuter**. 
- Une fois terminé, vous pouvez cliquez sur tout **Type**, comme **SVM**et afficher les résultats. 
- Vous pouvez également comparer les performances des modèles de la validation définie à l’aide de l’onglet **évaluer** . Par exemple, la sélection **d’Une matrice d’erreur** vous montre la matrice toute confusion, erreur globale et erreur de classe moyenne pour chaque modèle sur l’ensemble de validation. 
- Vous pouvez également tracer courbes ROC, effectuer une analyse de sensibilité et effectuer d’autres types d’évaluations de modèle.

Une fois que vous avez terminé de créer des modèles, sélectionnez l’onglet **journal** pour afficher le code R exécuter en Hochet pendant la session. Vous pouvez sélectionner le bouton **Exporter** pour l’enregistrer. 

>[AZURE.NOTE] Il existe un bogue dans la version actuelle de Hochet. Pour modifier le script ou utilisez-le pour répéter les étapes plus tard, vous devez insérer un caractère # devant *exporter ce journal...* dans le texte du journal. 


## <a name="postgresql--squirrel-sql"></a>PostgreSQL et non SQL

Le DSVM est fourni avec PostgreSQL installé. PostgreSQL est une base de données relationnel sophistiquée, open source. Cette section montre comment charger notre dataset le courrier indésirable dans PostgreSQL et puis de la requête.

Avant de pouvoir charger les données, vous devez autoriser l’authentification par mot de passe à partir de l’hôte local. À l’invite :

    sudo gedit /var/lib/pgsql/data/pg_hba.conf

Dans la partie inférieure du fichier de configuration sont plusieurs lignes de détaillent les connexions autorisées :

    # "local" is for Unix domain socket connections only
    local   all             all                                     trust
    # IPv4 local connections:
    host    all             all             127.0.0.1/32            ident
    # IPv6 local connections:
    host    all             all             ::1/128                 ident

Modifiez la ligne « Connexions locales IPv4 » pour utiliser md5 au lieu d’identification, afin que nous puissions connectez-vous en utilisant un nom d’utilisateur et mot de passe :

    # IPv4 local connections:
    host    all             all             127.0.0.1/32            md5

Et redémarrez le service postgres :

    sudo systemctl restart postgresql

Pour lancer psql, un terminal guide interactif pour PostgreSQL, en tant que l’utilisateur postgres intégrés, exécutez la commande suivante à partir d’une invite de commandes :

    sudo -u postgres psql

Créer un nouveau compte d’utilisateur, utilisez le même nom d’utilisateur comme compte Linux que vous êtes connecté tant qu’et lui attribuer un mot de passe :

    CREATE USER <username> WITH CREATEDB;
    CREATE DATABASE <username>;
    ALTER USER <username> password '<password>';
    \quit

Puis se connecter à psql en tant qu’utilisateur votre :

    psql

Et importer les données dans une base de données :

    CREATE DATABASE spam;
    \c spam
    CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
    \copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
    \quit

À présent, nous allons explorer les données et exécuter des requêtes à l’aide **Non SQL**, un outil graphique qui vous permet d’interagissent avec les bases de données via un pilote JDBC.

Pour commencer, lancez SQL non dans le menu Applications. Pour configurer le pilote :

- Sélectionnez **Windows**, puis **mode pilotes**. 
- Avec le bouton droit sur **PostgreSQL** , puis sélectionnez **Modifier le pilote**. 
- Sélectionnez le **chemin d’accès de classe supplémentaires**, puis sur **Ajouter**. 
- Entrez ***/usr/share/java/jdbcdrivers/postgresql-9.4.1208.jre6.jar*** pour le **nom de fichier** et 
- Cliquez sur **Ouvrir**.
- Choisissez les pilotes de la liste, puis sélectionnez **org.postgresql.Driver** dans **Nom du cours**, puis sélectionnez **OK**.

Pour configurer la connexion au serveur local :
 
- Sélectionnez **Windows**, puis **Afficher alias.** 
- Choisissez le **+** bouton pour créer un nouvel alias. 
- Nom de *base de données du courrier indésirable*, choisissez **PostgreSQL** dans le menu déroulant **pilote** .
- Définir l’URL à *jdbc:postgresql://localhost/spam*. 
- Entrez votre *nom d’utilisateur* et *mot de passe*. 
- Cliquez sur **OK**. 
- Pour ouvrir la fenêtre de **connexion** , double-cliquez sur l’alias de ***base de données du courrier indésirable*** . 
- Sélectionnez **se connecter**.

Pour exécuter des requêtes :

- Sélectionnez l’onglet **SQL** .
- Entrez une requête simple telle que `SELECT * from data;` dans la zone de texte de requête dans la partie supérieure de l’onglet SQL. 
- Appuyez sur **Ctrl + Entrée** pour l’exécuter. Par défaut non SQL renvoie les 100 premières lignes de votre requête. 

Il existe de nombreuses requêtes plus, que vous pouvez exécuter pour explorer ces données. Par exemple, la fréquence de word *rendre* différence entre le courrier indésirable et ham ?

    SELECT avg(word_freq_make), spam from data group by spam;

Ou quelles sont les caractéristiques de la messagerie électronique qui contiennent fréquemment *3d*?

    SELECT * from data order by word_freq_3d desc;

La plupart des messages électroniques qui ont une occurrence élevée de *3d* sont apparemment spam, il peut être une fonction utile pour la création d’un modèle de prévision pour classer les messages électroniques.

Si vous souhaitez effectuer apprentissage automatique avec des données stockées dans une base de données PostgreSQL, envisagez d’utiliser [MADlib](http://madlib.incubator.apache.org/).

## <a name="sql-server-data-warehouse"></a>Data Warehouse de SQL Server

Magasin de données SQL Azure est une base de données sur le nuage, horizontale capable de traiter les volumes importants de données, relationnelles et non relationnelles. Pour plus d’informations, voir [Nouveautés Azure SQL Data Warehouse ?](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Pour vous connecter au data warehouse et créer une table, exécutez la commande suivante à partir d’une invite de commande :

    sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I

Puis à l’invite sqlcmd :

    CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
    GO

Copier des données avec bcp :

    bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"

>[AZURE.NOTE] Les fins de ligne dans le fichier téléchargé sont style Windows, mais bcp attend style UNIX, afin que nous avons besoin de savoir que bcp avec l’indicateur - r.

Et de requête de sqlcmd :

    select top 10 spam, char_freq_dollar from spam;
    GO

Vous pouvez également interroger avec SQL non. Suivez les étapes similaires pour PostgreSQL, à l’aide du pilote Microsoft MSSQL Server JDBC qui se trouve dans ***/usr/share/java/jdbcdrivers/sqljdbc42.jar***.

## <a name="next-steps"></a>Étapes suivantes

Pour une vue d’ensemble des rubriques qui vous guident dans les tâches qui constituent le processus de données scientifique dans Azure, voir [Processus scientifique des données d’équipe](http://aka.ms/datascienceprocess).

Pour une description des autres procédures de bout en bout qui illustrent les étapes décrites dans le processus d’équipe données scientifique pour des scénarios spécifiques, consultez [procédures pas à pas de processus scientifique des données d’équipe](data-science-process-walkthroughs.md). Les procédures illustrent également combiner des outils cloud et en local et services dans un flux de travail ou pipeline pour créer une application intelligente.

