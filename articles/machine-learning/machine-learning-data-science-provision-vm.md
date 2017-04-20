<properties 
    pageTitle="Mise en service de l’ordinateur Microsoft Data scientifique virtuel | Microsoft Azure" 
    description="Configurez et créez une Machine virtuelle de Science données sur Azure à suivre analytique et d’apprentissage de l’ordinateur." 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/07/2016" 
    ms.author="bradsev" />


# <a name="provision-the-microsoft-data-science-virtual-machine"></a>Mise en service de la Machine virtuelle de scientifique des données à Microsoft

La Machine virtuelle Microsoft données scientifique est une image Azure machine virtuelle () préinstallés et configurés avec plusieurs outils populaires couramment utilisés pour analytique de données et d’apprentissage automatique. Les outils inclus sont :

- Microsoft R Server Édition développeur
- Distribution de Python anaconda
- Bloc-notes Jupyter (avec R, noyaux Python)
- Édition de communauté Visual Studio
- Ordinateur de bureau Power BI
- SQL Server 2016 édition développeur
- Outils d’apprentissage automatique
    - [Kit de ressources de réseau informatique (CNTK)](https://github.com/Microsoft/CNTK): une profondeur Kit de ressources de logiciel de Microsoft Research d’apprentissage.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un ordinateur rapide système prenant en charge des techniques telles qu’allreduce en ligne, hachage, réductions, learning2search et active, de formation et d’apprentissage interactif.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): un outil fournissant implémentation arborescence augmentée rapide et précise.
    - [Vibrer](http://rattle.togaware.com/) (la R analytique outil pour en savoir plus facilement) : un outil qui vous paraît prise en main analytique de données et machine R simple, avec des données via une interface utilisateur d’apprentissage et de modélisation avec génération de code automatique R.
    - [mxnet](https://github.com/dmlc/mxnet): un cadre de formation approfondie conçu pour l’efficacité et flexibilité
- Utilisent des bibliothèques dans R et Python pour dans apprentissage automatique Azure et d’autres services Azure
- GIT notamment Git Bash pour fonctionner avec référentiels de code source, y compris GitHub, Visual Studio Team Services
- Ports Windows de plusieurs Linux de ligne de commande utilitaires populaires (y compris awk, sed, perl, grep, rechercher, wget, ourlée etc.) accessible via l’invite de commandes. 


Effectuant scientifique de données implique une itération dans une séquence des tâches : données de recherche, de chargement et prétraitement, de création et test des modèles et les modèles de consommation dans des applications intelligentes de déploiement. Scientifiques données utilisent une série d’outils pour effectuer ces tâches. Il peut être beaucoup de temps à rechercher les versions appropriées du logiciel, puis téléchargez et installez-les. La Machine virtuelle Microsoft données scientifique peut en faciliter à l’aide d’une image prête à l’emploi qui peut être configurée sur Azure et tous les outils populaires plusieurs préinstallés et configurés. 

La Machine virtuelle Microsoft données scientifique jump-starts votre projet analytique. Il vous permet de travailler sur des tâches dans plusieurs langues, y compris R, Python, SQL et c#. Visual Studio fournit une interface IDE pour développer et tester votre code soit facile à utiliser. Le Kit de développement Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services plateformes de cloud de Microsoft. 

Il n’existe aucun frais logiciel de cette image données scientifique machine virtuelle. Vous payez uniquement pour les frais de l’utilisation Azure qui dépend de la taille de la machine virtuelle que vous mise en service. Vous trouverez plus d’informations sur les frais de cluster dans la section de détails de tarification dans la page [données scientifique Virtual Machine](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) . 


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir créer une Machine virtuelle Microsoft données scientifique, vous devez disposer des éléments suivants :

- **Abonnement un Azure**: pour obtenir une, voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

*   **Compte de stockage un Azure**: pour créer une, voir [créer un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Par ailleurs, le compte de stockage peut être créé dans le cadre du processus de création de la machine virtuelle si vous ne souhaitez pas utiliser un compte existant.


## <a name="create-your-microsoft-data-science-virtual-machine"></a>Créer votre ordinateur Microsoft Data scientifique virtuel

Voici les étapes pour créer une instance de la Machine virtuelle Microsoft données scientifique :

1.  Accédez à la machine virtuelle votre annonce [portail Azure](https://portal.azure.com/#create/microsoft-ads.standard-data-science-vmstandard-data-science-vm).
2.   Sélectionnez le bouton **créer** dans la partie inférieure à prendre en un Assistant. ![configurer des données-scientifique-machine virtuelle](./media/machine-learning-data-science-provision-vm/configure-data-science-virtual-machine.png)
3.   L’Assistant permet de créer la Machine virtuelle Microsoft données scientifique requiert des **entrées** pour chacun des **cinq étapes** énumérées située à droite de cette illustration. Voici les entrées nécessaires à la configuration de chacune de ces étapes :
    
    1.   **Concepts de base**
        1.   **Nom**: nom de votre serveur scientifique de données que vous créez.
        2.   **Nom d’utilisateur**: id de connexion de compte d’administrateur.
        3.   **Mot de passe**: mot de passe du compte d’administrateur.
        4.   **Abonnement**: Si vous avez plusieurs abonnements, sélectionnez celui sur lequel l’ordinateur doit être créé et facturés.
        5.   **Groupe de ressources**: vous pouvez créer un autre ou utiliser un groupe existant.
        6.   **Emplacement**: sélectionnez le centre de données qui convient le mieux. En règle générale, il est le centre de données qui comporte la plupart de vos données ou le plus proche de votre emplacement physique pour accéder au réseau plus rapide.
         
    2.   **Taille**: sélectionnez un des types de serveurs qui répond à vos exigences fonctionnelles et les contraintes de coût. Vous pouvez obtenir davantage d’options de taille de la mémoire virtuelle en sélectionnant « Afficher tout ».
    
    3.   **Paramètres**:
        1.   **Type de disque**: Premium choisir si vous préférez un lecteur SSD (SSD), sinon choisir « Standard ».
        2.   **Compte de stockage**: vous pouvez créer un nouveau compte de stockage Azure dans votre abonnement ou utiliser une existante au même *emplacement* choisi les **Concepts de base** à l’étape de l’Assistant.
        3.   **Autres paramètres**: en règle générale, vous utilisez uniquement les valeurs par défaut. Vous pouvez pointer sur le lien de l’aide sur les champs spécifiques d’information au cas où vous souhaiteriez envisagez d’utiliser les valeurs par défaut.

    4.   **Résumé**: Vérifiez que toutes les informations entrées sont correctes.
    5.   **Acheter**: cliquez sur **acheter** pour démarrer la mise en service. Un lien est fourni avec les termes de la transaction. La machine virtuelle n’a pas les frais supplémentaires au-delà du cluster pour la taille du serveur que vous avez choisi à l’étape de **taille** . 


>[AZURE.NOTE] La mise en service doit prendre environ 10 à 20 minutes. L’état de la mise en service est affiché dans le portail Azure.

## <a name="how-to-access-the-microsoft-data-science-virtual-machine"></a>Comment accéder à la Machine virtuelle Microsoft données scientifique

Une fois que la machine virtuelle est créée, vous pouvez Bureau à distance à l’aide des informations d’identification du compte d’administrateur que vous avez configuré dans la section précédente **Concepts de base** . 

Une fois que votre ordinateur virtuel est créé et sa mise en service, vous êtes prêt à commencer à utiliser les outils qui sont installés et configurés sur celui-ci. Il existe des vignettes du menu Démarrer et les icônes du bureau pour la plupart des outils. 

## <a name="how-to-create-a-strong-password-on-the-jupyter-notebook-server"></a>Comment créer un mot de passe sur le serveur de bloc-notes Jupyter 

Pour créer votre propre mot de passe fort pour le serveur de bloc-notes Jupyter installé sur l’ordinateur, exécutez la commande suivante à partir d’une invite de commande sur la Machine virtuelle de données scientifique.

    c:\anaconda\python.exe -c "import IPython;print IPython.lib.passwd()"

Choisissez un mot de passe lorsque vous y êtes invité.

Vous voyez le hachage de mot de passe au format « sha1:xxxxxx » dans le résultat. Copiez ce hachage de mot de passe et remplacez le hachage existant qui se trouve dans votre fichier de configuration de la bloc-notes situé sous : **C:\ProgramData\jupyter\jupyter_notebook_config.py** avec un nom de paramètre ***c.NotebookApp.password***.

Remplacez uniquement la partie (xxxxxx) de la valeur de hachage existante qui se trouve entre guillemets. Les guillemets et la ***sha1 :*** préfixe pour la valeur du paramètre les deux doivent être conservées.

Enfin, vous devez arrêter et redémarrer le serveur Jupyter, qui est en cours d’exécution sur l’ordinateur virtuel comme une tâche planifiée windows appelée **Start_IPython_Notebook**. Si votre nouveau mot de passe n’est pas accepté après le redémarrage de cette tâche, essayez d’arrêt de tous les processus python en cours d’exécution dans le Gestionnaire des tâches, puis redémarrez la tâche planifiée. Vous pouvez également cliquer, essayez de redémarrer l’ordinateur virtuel.

## <a name="tools-installed-on-the-microsoft-data-science-virtual-machine"></a>Outils installés sur la Machine virtuelle Microsoft données scientifique

### <a name="microsoft-r-server-developer-edition"></a>Microsoft R Server Édition développeur
Si vous souhaitez utiliser R pour votre analytique, la machine virtuelle a edition développeur Microsoft R Server installé. Microsoft R Server est une plateforme analytique entreprise déployer largement selon R est pris en charge, scalable et sécurisée. Prise en charge des statistiques de données volumineux, modélisation prédictive et apprentissage capacités de l’ordinateur, R serveur prend en charge la gamme complète d’analytique – exploration, d’analyse, de visualisation et de modélisation. En utilisant et étendre libres R, Microsoft R Server est entièrement compatible avec les scripts R, les fonctions et les packages CRAN, pour analyser des données à l’échelle de l’entreprise. Il traite également les limitations en mémoire d’ouvrir Source R en ajoutant parallèle et segmenté traitement des données. Cela vous permet d’exécuter analytique sur données beaucoup plus grande que tout ce qui tient dans la mémoire principale.  Visual Studio Community Edition inclus sur l’ordinateur virtuel contient les outils R pour extension Visual Studio qui fournit un IDE complète pour travailler avec R. Vous pouvez également télécharger et utiliser les autres IDE également comme [RStudio](http://www.rstudio.com). 

### <a name="python"></a>Python
Pour le développement à l’aide de Python, distribution Python Anaconda 2.7 et 3.5 a été installée. Cette distribution contient les Python base ainsi que d’environ 300 les packages analytique mathématiques, d’ingénierie et données les plus populaires. Vous pouvez utiliser les outils de Python pour commandes visuel les Studio (PTVS) qui est installé au sein de l’édition Visual Studio 2015 Communauté ou un de l’IDE fourni avec Anaconda comme inactif ou Spyder. Vous pouvez lancer une d'entre elles en effectuant une recherche dans la barre de recherche (**Win** + clé**S** ).

>[AZURE.NOTE] Pour pointer sur les outils Python pour Visual Studio en Anaconda Python 2.7 et 3.5, vous devez créer des environnements personnalisés pour chaque version. Pour définir ces chemins d’accès de l’environnement de l’édition de communauté Visual Studio 2015, accédez à **Outils** -> **Outils Python** -> **Environnements Python** , puis cliquez sur **+ personnalisé**. 

Anaconda Python 2.7 est installé sous C:\Anaconda et Anaconda Python 3.5 est installé sous c:\Anaconda\envs\py35. Pour plus d’informations, voir [documentation PTVS](https://github.com/Microsoft/PTVS/wiki/Selecting-and-Installing-Python-Interpreters#hey-i-already-have-an-interpreter-on-my-machine-but-ptvs-doesnt-seem-to-know-about-it) . 

### <a name="jupyter-notebook"></a>Bloc-notes Jupyter
Distribution anaconda est également fourni avec un bloc-notes Jupyter, un environnement à partager le code et analyse. Un serveur de bloc-notes Jupyter a été configuré préalablement avec Python 2, 3 Python et noyaux R. Il existe une icône de bureau nommée « bloc-notes Jupyter pour lancer le navigateur pour accéder au serveur de bloc-notes. Si vous exécutez la machine virtuelle via le Bureau à distance, vous pouvez également consulter [https://localhost:9999 /](https://localhost:9999/) pour accéder au serveur de bloc-notes Jupyter lorsque connecté à la machine virtuelle.
 
>[AZURE.NOTE] Continuez si vous obtenez des avertissements de certificat. 

Nous avons empaqueté blocs-notes d’exemple dans Python et R. Les blocs-notes Jupyter montrent comment utiliser Microsoft R Server, SQL Server 2016 R Services (analytique dans base de données), Python et d’autres technologies Azure une fois que vous vous connectez à Jupyter. Vous pouvez voir le lien vers les exemples dans la page d’accueil de bloc-notes après que vous être authentifié au bloc-notes Jupyter à l’aide du mot de passe que vous avez créé dans une étape précédente. 

### <a name="visual-studio-2015-community-edition"></a>Visual Studio 2015 Community edition
Visual Studio Community edition installée sur l’ordinateur virtuel. Il est une version gratuite de l’IDE populaires de Microsoft que vous pouvez utiliser à des fins d’évaluation et des petites équipes. Vous pouvez consulter les termes de licence [ici](https://www.visualstudio.com/support/legal/mt171547).  Ouvrir Visual Studio en double-cliquant sur l’icône du bureau ou dans le menu **Démarrer** . Vous pouvez également rechercher des programmes avec des **Win** + **S** et saisissez « Visual Studio ». Une fois qu’il vous pouvez créer des projets dans les langages comme c#, Python, R, node.js. Plug-ins sont également installés qui le rendent pratique de travailler avec les services, tels que catalogue de données Azure Azure HDInsight (Hadoop, commandé) et la Lake de données Azure Azure. 

>[AZURE.NOTE] Vous pouvez obtenir un message indiquant que votre période d’évaluation a expiré. Entrez vos informations d’identification de compte Microsoft ou créer un nouveau compte gratuit pour accéder à l’édition de communauté Visual Studio. 

### <a name="sql-server-2016-developer-edition"></a>Édition du développeur SQL Server 2016
Une version développeur de SQL Server 2016 avec les Services R pour exécuter analytique dans base de données est fournie sur la machine virtuelle. R Services fournissent une plateforme pour développer et déployer des applications intelligentes. Vous pouvez utiliser le langage de R riche et puissant et les nombreux packages à partir de la Communauté pour créer des modèles et générer des prédictions pour vos données SQL Server. Vous pouvez conserver analytique proche des données car R Services (dans database) intégrer la langue R avec SQL Server. Cela supprime les coûts et les risques associés au transfert de données.

>[AZURE.NOTE] L’édition développeur SQL Server 2016 peut uniquement être utilisée à des fins de test et de développement. Vous avez besoin d’une licence pour exécuter en production. 

Vous pouvez accéder au serveur SQL en lançant **SQL Server Management Studio**. Le nom de votre machine virtuelle est rempli en tant que le nom du serveur. Utiliser l’authentification Windows lorsque connecté en tant qu’administrateur sous Windows. Une fois que vous êtes dans SQL Server Management Studio vous pouvez créer d’autres utilisateurs, créer des bases de données, importer des données et exécuter des requêtes SQL. 

Pour activer analytique dans base de données à l’aide de Microsoft R, exécutez la commande suivante qu’une fois que l’action dans SQL Server management studio après la connexion en tant que l’administrateur du serveur. 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
        
        (Please replace the %COMPUTERNAME% with your VM name)


### <a name="azure"></a>Azure 
Plusieurs outils Azure sont installés sur l’ordinateur virtuel :

- Il existe un raccourci clavier pour accéder à la documentation SDK Azure. 
- **AzCopy**: permet de déplacer des données vers et depuis votre compte Microsoft Azure stockage. Pour afficher l’utilisation, tapez **Azcopy** à l’invite pour afficher l’utilisation. 
- **Explorateur de stockage de Microsoft Azure**: permet de parcourir les objets que vous avez stockés dans vos données de compte de stockage Azure et de transfert vers et depuis le stockage Azure. Vous pouvez tapez **Explorateur de stockage** dans Rechercher ou rechercher dans le menu Démarrer de Windows pour accéder à cet outil. 
- **Adlcopy**: permet de déplacer des données vers Azure données Lake. Pour afficher l’utilisation, tapez **adlcopy** dans une invite de commande. 
- **dtui**: permet de déplacer des données vers et depuis DocumentDB Azure, une base de données NoSQL sur le nuage. Tapez **dtui** sur invite de commandes. 
- **Passerelle de gestion des données de Microsoft**: permet de déplacer des données entre des sources de données en local et nuage. Il est utilisé dans les outils tels que Azure Data Factory. 
- **Microsoft Azure Powershell**: un outil permettant de gérer vos ressources Azure dans la session Powershell langage de script est également installé sur votre ordinateur virtuel. 

###<a name="power-bi"></a>Power BI

Pour vous aider à créer des tableaux de bord et les visualisations excellente, le **Power BI Desktop** a été installé. Utilisez cet outil pour extraire des données de différentes sources, à vos tableaux de bord et les rapports d’auteur et les publier dans le cloud. Pour plus d’informations, consultez le site [Power BI](http://powerbi.microsoft.com) . Vous pouvez trouver bureau Power BI dans le menu Démarrer. 

>[AZURE.NOTE] Vous avez besoin d’un compte Office 365 pour accéder à Power BI. 

## <a name="additional-microsoft-development-tools"></a>Autres outils de développement Microsoft
[**Programme d’installation de Microsoft Web plateforme**](https://www.microsoft.com/web/downloads/platform.aspx) peut servir à découvrir et télécharger d’autres outils de développement Microsoft. Vous trouverez également un raccourci vers l’outil fourni sur le bureau Machine virtuelle de Microsoft données scientifique.  

## <a name="important-directories-on-the-vm"></a>Répertoires importants sur l’ordinateur virtuel

| Élément                          | Répertoire |
| ------------------------------| ---------------- |
|Configurations de serveur Jupyter bloc-notes | C:\ProgramData\jupyter |
|Répertoire de base Jupyter bloc-notes exemples| c:\dsvm\notebooks|
|Autres exemples | c:\dsvm\samples|
| Anaconda (par défaut : Python 2.7) | c:\Anaconda |
| Environnement de Python 3.5 anaconda | c:\Anaconda\envs\py35|
|Répertoire d’instance R Server autonome (instance R par défaut) | C:\Program Files\Microsoft SQL Server\130\R_SERVER |
| Répertoire d’instance R serveur de base de données | C:\Program Files\Microsoft SQL Server\MSSQL13. MSSQLSERVER\R_SERVICES |
| Divers outils | c:\dsvm\tools|

>[AZURE.NOTE] Instances de la données scientifique Machine virtuelle Microsoft créée avant 1.5.0 (avant le 3 septembre 2016) utilisé une structure de répertoire légèrement différente supérieur au nombre spécifié dans le tableau ci-dessus. 

## <a name="next-steps"></a>Étapes suivantes
Voici quelques étapes suivantes pour continuer votre apprentissage et l’exploration. 

* Explorer les divers outils scientifique de données sur la science données machine virtuelle en cliquant sur le menu Démarrer et en cochant les outils figurant dans le menu.
* Accédez à **C:\Program Files\Microsoft SQL Server\130\R_SERVER\library\RevoScaleR\demoScripts** pour exemples à l’aide de la bibliothèque RevoScaleR dans R qui prend en charge analytique de données à l’échelle de l’entreprise.  
* Consultez l’article : [10 pouvant être exécutées sur la science données Machine virtuelle](http://aka.ms/dsvmtenthings)
* Apprenez à créer des solutions analytiques de bout en bout systématiquement en utilisant le [Processus de scientifique des données d’équipe](https://azure.microsoft.com/documentation/learning-paths/data-science-process/).
* Visitez la [Galerie d’Intelligence Cortana](http://gallery.cortanaintelligence.com) machine données et l’apprentissage analytique exemples qui utilisent la gamme Intelligence Cortana. Nous vous fournissons également une icône dans le menu **Démarrer** , sur le bureau de la machine virtuelle à cette galerie.

