<properties
    pageTitle="Mise en service de l’ordinateur Linux données scientifique virtuel | Microsoft Azure"
    description="Configurez et créez une Machine virtuelle Linux données scientifique sur Azure à suivre analytique et d’apprentissage de l’ordinateur."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun"  />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2016"
    ms.author="bradsev" />

# <a name="provision-the-linux-data-science-virtual-machine"></a>Mise en service de la Machine virtuelle de Science données Linux

La Machine virtuelle Linux données scientifique est une machine virtuelle Azure qui est fourni avec un ensemble d’outils préinstallés. Ces outils couramment utilisés pour effectuant analytique de données et apprentissage de l’ordinateur. Les composants de logiciels clés inclus sont :

- Microsoft R Server Édition développeur
- Distribution de Python anaconda (versions 2.7 et 3.5), y compris des bibliothèques d’analyse des données les plus consultés
- JupyterHub - un serveur de bloc-notes Jupyter multi-utilisateur R, Python, noyaux Julia de prise en charge
- Explorateur de stockage Azure
- Interface de ligne Azure (commande) pour la gestion des ressources Azure
- Base de données PostgresSQL
- Outils d’apprentissage automatique
    - [Kit de ressources de réseau informatique (CNTK)](https://github.com/Microsoft/CNTK): une profondeur Kit de ressources de logiciel de Microsoft Research d’apprentissage.
    - [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit): un ordinateur rapide système prenant en charge des techniques telles qu’allreduce en ligne, hachage, réductions, learning2search et active, de formation et d’apprentissage interactif.
    - [XGBoost](https://xgboost.readthedocs.org/en/latest/): un outil fournissant implémentation arborescence augmentée rapide et précise.
    - [Vibrer](http://rattle.togaware.com/) (la R analytique outil pour en savoir plus facilement) : un outil qui vous paraît prise en main analytique de données et machine R simple, avec des données via une interface utilisateur d’apprentissage et de modélisation avec génération de code automatique R.
- Kit de développement Azure dans Java, Python, node.js, Ruby, PHP
- Utilisent des bibliothèques dans R et Python pour dans apprentissage automatique Azure et d’autres services Azure
- Outils de développement et les éditeurs (Eclipse, Emacs, gedit, vi)

Effectuant scientifique de données implique une itération dans une séquence des tâches :

1. Recherche, le chargement et prétraitement des données
2. Création et test des modèles
3. Déployer les modèles de consommation dans des applications intelligentes

Scientifiques données utilisent divers outils pour effectuer ces tâches. Il peut être beaucoup de temps pour rechercher les versions appropriées du logiciel et puis pour télécharger, compiler et installez ces versions.

La Machine virtuelle Linux données scientifique peut simplifier cette sensiblement. Utilisez-le pour démarrer rapidement votre projet analytique. Il vous permet de travailler sur des tâches dans plusieurs langues, y compris R, Python, SQL, Java et C++. Éclipse fournit une interface IDE pour développer et tester votre code soit facile à utiliser. Le Kit de développement Azure inclus dans la machine virtuelle vous permet de créer des applications à l’aide de divers services sur Linux pour la plateforme de cloud Microsoft. En outre, vous avez accès à d’autres langues, tels que Ruby, Perl, PHP et node.js également préinstallés.

Il n’existe aucun frais logiciel de cette image données scientifique machine virtuelle. Vous payez uniquement les frais de l’utilisation matériel Azure sont évalués en fonction de la taille de la machine virtuelle que vous devez configurer avec l’image de la machine virtuelle. Vous trouverez plus d’informations sur les frais de calcul sur la [machine virtuelle répertoriant page sur Azure Marketplace ](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/).


## <a name="prerequisites"></a>Conditions préalables

Avant de pouvoir créer une Machine virtuelle Linux données scientifique, vous devez disposer des éléments suivants :

- **Abonnement un Azure**: pour obtenir une, voir [Azure obtenir la version d’évaluation gratuite](https://azure.microsoft.com/free/).
- **Compte de stockage un Azure**: pour créer une, voir [créer un compte de stockage Azure](storage-create-storage-account.md#create-a-storage-account). Par ailleurs, le compte de stockage peut être créé dans le cadre du processus de création de la machine virtuelle, si vous ne souhaitez pas utiliser un compte existant.


## <a name="create-your-linux-data-science-virtual-machine"></a>Créer votre ordinateur Linux données scientifique virtuel

Voici les étapes pour créer une instance de la Machine virtuelle Linux données scientifique :

1.  Accédez à la machine virtuelle liste sur le [portail Azure](https://portal.azure.com/#create/microsoft-ads.linux-data-science-vmlinuxdsvm).
2.   Cliquez sur **créer** (en bas) pour faire apparaître l’Assistant. ![configurer des données-scientifique-machine virtuelle](./media/machine-learning-data-science-linux-dsvm-intro/configure-linux-data-science-virtual-machine.png)
3.   Les sections suivantes fournissent les entrées pour chacune des étapes de l’Assistant (énuméré située à droite de l’illustration précédente) utilisées pour créer la Machine virtuelle Microsoft données scientifique. Voici les entrées nécessaires à la configuration de chacune de ces étapes :

    un. **Concepts de base**:

  - **Nom**: nom de votre serveur scientifique de données que vous créez.
  - **Nom d’utilisateur**: connexion premier compte identifiant.
  - **Mot de passe**: premier mot de passe du compte (vous pouvez utiliser clé publique SSH au lieu de mot de passe).
  - **Abonnement**: Si vous avez plusieurs abonnements, sélectionnez celui sur lequel l’ordinateur doit être créé et facturés. Vous devez disposer des privilèges de création de ressources pour cet abonnement.
  - **Groupe de ressources**: vous pouvez créer un autre ou utiliser un groupe existant.
  - **Emplacement**: sélectionnez le centre de données qui convient le mieux. En règle générale, il est le centre de données qui comporte la plupart de vos données, ou le plus proche de votre emplacement physique pour accéder au réseau plus rapide.

    b. **Taille**:

  - Sélectionnez un des types de serveurs qui répond à vos exigences fonctionnelles et les contraintes de coût. Sélectionner **Afficher tout** pour afficher davantage d’options de taille de la mémoire virtuelle.

    c. **Paramètres**:

  - **Type de disque**: choisissez **Premium** si vous préférez un lecteur Flash (SSD). Dans le cas contraire, sélectionnez **Standard**.
  - **Compte de stockage**: vous pouvez créer un nouveau compte de stockage Azure dans votre abonnement, ou utiliser une existante au même endroit choisi les **Concepts de base** à l’étape de l’Assistant.
  - **Autres paramètres**: dans la plupart des cas, vous utilisez uniquement les valeurs par défaut. Pour prendre en compte les valeurs de ceux définis par défaut, pointez sur le lien de l’aide sur les champs spécifiques d’information.

    d. **Résumé**:

  - Vérifiez que toutes les informations entrées sont correctes.

    e. **Acheter**:

  - Pour commencer la mise en service, cliquez sur **acheter**. Un lien est fourni avec les termes de la transaction. La machine virtuelle n’a pas les frais supplémentaires au-delà du cluster pour la taille du serveur que vous avez choisi à l’étape de **taille** .

La mise en service doit prendre environ 10 à 20 minutes. L’état de la mise en service est affiché dans le portail Azure.

## <a name="how-to-access-the-linux-data-science-virtual-machine"></a>Comment accéder à la Machine virtuelle Linux données scientifique

Après la création de la machine virtuelle, vous pouvez vous connecter à celui-ci à l’aide de SSH. Utilisez les informations d’identification de compte que vous avez créé dans la section **Concepts de base** de l’étape 3 de l’interface de shell du texte. Sous Windows, vous pouvez télécharger un outil client SSH tel que [Putty](http://www.putty.org). Si vous préférez un bureau graphique (système de Windows X), vous pouvez utiliser X11 transfert sur Putty ou installer le client X2Go.

>[AZURE.NOTE] Le client X2Go effectué sensiblement supérieure à celle de X11 transfert dans test. Nous recommandons d’utiliser le client X2Go pour une interface de bureau graphique.


## <a name="installing-and-configuring-x2go-client"></a>Installation et configuration client X2Go

La VM Linux est déjà généré avec X2Go serveur et vous êtes prêt à accepter des connexions client. Pour vous connecter à l’application de bureau graphique Linux VM, procédez comme suit sur votre client :

1. Téléchargez et installez le client X2Go pour votre plateforme client à partir de [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Exécuter le client X2Go, puis sélectionnez **Nouvelle Session**. Il ouvre une fenêtre de configuration avec plusieurs onglets. Entrez les paramètres de configuration suivants :
    * **Onglet session**:
        - **Hôte**: le nom d’hôte ou l’adresse IP de votre organisation scientifique de données Linux machine virtuelle.
        - **Connexion**: nom d’utilisateur sur le VM Linux.
        - **Voie SSH**: laissez à 22, la valeur par défaut.
        - **Type de session**: définissez la valeur sur XFCE. Actuellement la VM Linux prend uniquement en charge bureau XFCE.
    * **Onglet média**: vous pouvez désactiver son prise en charge et le client l’impression si vous n’avez pas besoin de les utiliser.
    * **Dossiers partagés**: Si vous souhaitez répertoires à partir de vos ordinateurs clients chargés sur la VM Linux, ajoutez les répertoires de l’ordinateur client que vous voulez partager avec la machine virtuelle sur cet onglet.

Une fois que vous êtes connecté à la machine virtuelle en utilisant le client SSH ou le bureau graphique XFCE via le client X2Go, vous êtes prêt à commencer à utiliser les outils qui sont installés et configurés sur l’ordinateur virtuel. Sur XFCE, vous pouvez voir raccourcis du menu applications et les icônes du bureau pour la plupart des outils.


## <a name="tools-installed-on-the-linux-data-science-virtual-machine"></a>Outils installés sur la Machine virtuelle Linux données scientifique

### <a name="microsoft-r-open"></a>Ouvrir Microsoft R
R figure parmi les langues les plus populaires pour l’analyse de données et d’apprentissage automatique. Si vous voulez utiliser R pour votre analytique, la machine virtuelle a Microsoft R ouvrir Diffusée avec Math Kernel Library (MKL). La MKL optimise opérations mathématiques courantes dans les algorithmes analytiques. MRO est 100 % compatible avec CRAN R, et une des bibliothèques R publiés dans CRAN peut être installée sur le MRO. Vous pouvez modifier vos programmes R dans un des éditeurs par défaut, comme vi, Emacs ou gedit. Vous pouvez également télécharger et utiliser les autres IDE, tel que [RStudio](http://www.rstudio.com). Votre convenance, un script simple (installRStudio.sh) est fourni dans le répertoire **/dsvm/tools** qui installe RStudio. Si vous utilisez l’éditeur Emacs, notez que les Emacs package ESS (Emacs Speaks statistiques), qui simplifie utiliser R fichiers au sein de l’éditeur Emacs, a été préinstallé.

Pour lancer R, vous tapez **R** dans le shell. Cela vous permet d’accéder à un environnement interactif. Pour développer votre programme R, vous utilisez généralement un éditeur tel que Emacs ou vi ou gedit et puis exécutez les scripts dans R. Si vous installez RStudio, vous disposez d’un environnement IDE graphique complète pour développer votre programme R.

Vous trouverez également un script R pour vous permet d’installer les [packages haut 20 R](http://www.kdnuggets.com/2015/06/top-20-r-packages.html) si vous voulez. Ce script peut être exécuté une fois que vous êtes dans l’interface interactif R, qui peut être entré (comme indiqué) en tapant **R** dans le shell.  

### <a name="python"></a>Python
Pour le développement à l’aide de Python, distribution Python Anaconda 2.7 et 3.5 a été installée. Cette distribution contient les Python base ainsi que d’environ 300 les packages analytique mathématiques, d’ingénierie et données les plus populaires. Vous pouvez utiliser les éditeurs de texte par défaut. En outre, vous pouvez utiliser Spyder, un IDE Python qui est fourni avec répartitions Python Anaconda. Spyder a besoin d’un bureau graphique ou X11 transfert. Un raccourci vers Spyder est fourni dans l’application de bureau graphique.

Étant donné que nous avons Python 2.7 et 3.5, vous devez activer spécifiquement la version Python souhaitée que vous voulez utiliser dans la session active. Le processus d’activation définit la variable de chemin d’accès à la version souhaitée de Python.

Pour activer les Python 2.7, exécutez la commande suivante à partir du shell :

    source /anaconda/bin/activate root

Python 2.7 est installé en */anaconda/bin*.

Pour activer les Python 3.5, exécutez la commande suivante à partir du shell :

    source /anaconda/bin/activate py35


Python 3.5 est installé en */anaconda/envs/py35/bin*.

Pour appeler une session interactive Python, tapez **python** dans l’environnement. Si vous êtes une interface graphique ou avez X11 jeu de transfert vers le haut, vous pouvez taper **spyder** pour lancer l’IDE Python.

### <a name="jupyter-notebook"></a>Bloc-notes Jupyter

La distribution Anaconda est également fourni avec un bloc-notes Jupyter, un environnement à partager le code et analyse. Le bloc-notes Jupyter est accessible via JupyterHub. Vous connecter à l’aide de votre nom d’utilisateur Linux local et le mot de passe.

Le serveur de bloc-notes Jupyter a été configuré préalablement avec Python 2, 3 Python et noyaux R. Il existe une icône de bureau nommée « Bloc-notes Jupyter » pour lancer le navigateur pour accéder au serveur de bloc-notes. Si vous exécutez la machine virtuelle par le biais d’un client SSH ou X2Go, vous pouvez également consulter [https://localhost:8000 /](https://localhost:8000/) pour accéder au serveur de bloc-notes Jupyter.

>[AZURE.NOTE] Continuez si vous obtenez des avertissements de certificat.

Vous pouvez accéder au serveur de bloc-notes Jupyter depuis n’importe quel hôte. Tapez simplement *https://\<nom VM DNS ou l’adresse IP\>: 8000 /*

>[AZURE.NOTE] Port 8000 est ouvert dans le pare-feu par défaut lors de la machine virtuelle est mis en service.

Nous avons empaqueté blocs-notes exemple--Python en un seul, le R. Vous pouvez voir le lien vers les exemples dans la page d’accueil de bloc-notes après que vous être authentifié au bloc-notes Jupyter en utilisant votre nom d’utilisateur Linux local et le mot de passe. Vous pouvez créer un bloc-notes en sélectionnant **Nouveau**, puis le noyau linguistique approprié. Si vous ne voyez pas le bouton **Nouveau** , cliquez sur l’icône **Jupyter** dans le coin supérieur gauche pour accéder à la page d’accueil du serveur bloc-notes.


### <a name="ides-and-editors"></a>IDEs et les éditeurs

Vous avez le choix de plusieurs éditeurs de code. Cela inclut vi/VIM, Emacs, gEdit et Eclipse. gEdit Eclipse éditeurs graphiques et sont besoin d’être connecté à un bureau graphique les utiliser. Ces éditeurs ont bureau et application pour lancer les raccourcis du menu.

**VIM** et **Emacs** sont des éditeurs basées sur texte. Sous Emacs, nous avons installé un package de composant additionnel appelé Emacs Speaks statistiques (ESS) qui facilite l’utilisation des R au sein de l’éditeur Emacs. Plus d’informations, consultez [ESS](http://ess.r-project.org/).

**Eclipse** est une source ouverte, IDE extensible qui prend en charge plusieurs langues. L’édition de développeurs Java est l’instance installée sur l’ordinateur virtuel. Plug-ins sont disponibles pour plusieurs langues courantes pouvant être installé pour étendre l’environnement Eclipse. Nous avons également un plug-in installé dans Eclipse appelé **Azure le Kit de ressources pour Eclipse**. Il permet de créer, développer, tester et déployer des applications Azure à l’aide de l’environnement de développement Eclipse qui prend en charge des langues telles que Java. Vous trouverez également un **Azure SDK pour Java** qui permet d’accéder à différents services Azure à partir d’un environnement Java. Vous trouverez plus d’informations sur le Kit de ressources de Azure pour Eclipse au [Jeu d’outils Azure pour Eclipse](../azure-toolkit-for-eclipse.md).

**LaTex** est installé via le package texlive ainsi que Emacs [auctex](https://www.gnu.org/software/auctex/manual/auctex/auctex.html) package complémentaire, qui simplifie la création de vos documents LaTex Emacs.  

### <a name="databases"></a>Bases de données

#### <a name="postgres"></a>Postgres
La base de données source ouverte **Postgres** est disponible sur la machine virtuelle, avec les services en cours d’exécution et initdb déjà terminé. Vous avez besoin créer des bases de données et les utilisateurs. Pour plus d’informations, voir la [documentation Postgres](https://www.postgresql.org/docs/).  

####  <a name="graphical-sql-client"></a>Client SQL graphique
**Non SQL**, un client graphique SQL, a été fourni pour se connecter à des bases de données (par exemple, Microsoft SQL Server, Postgres et MySQL) et exécuter des requêtes SQL. Vous pouvez l’exécuter à partir d’une session de bureau graphique (en utilisant le client X2Go, par exemple). Pour appeler SQL non, vous pouvez lancer à partir de l’icône sur le bureau ou exécutez la commande suivante sur le shell.

    /usr/local/squirrel-sql-3.7/squirrel-sql.sh

Avant la première utilisation, configurez votre stratégiques et les alias de base de données. Les pilotes JDBC se trouvent :

*/usr/share/Java/jdbcdrivers*

Pour plus d’informations, voir [SQL non](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

#### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Outils de ligne de commande pour accéder à Microsoft SQL Server

Le package du pilote ODBC pour SQL Server est également fourni avec deux outils de ligne de commande :

**bcp**: la plus grande partie d’utilitaire bcp copie des données entre une instance de Microsoft SQL Server et un fichier de données dans un format spécifié par l’utilisateur. L’utilitaire bcp peut être utilisé pour importer un grand nombre de nouvelles lignes dans des tables SQL Server, ou pour exporter les données se déconnecter de tables dans les fichiers de données. Pour importer des données dans un tableau, vous devez utiliser un fichier de format créé pour cette table ou comprendre la structure de la table et les types de données valides pour ses colonnes.

Pour plus d’informations, consultez l’article [connexion avec bcp](https://msdn.microsoft.com/library/hh568446.aspx).

**SQLCMD**: vous pouvez entrer des instructions Transact-SQL avec l’utilitaire sqlcmd, ainsi que les procédures système et fichiers à l’invite de script. Cet utilitaire utilise ODBC pour exécuter des lots Transact-SQL.

Pour plus d’informations, consultez l’article [connexion avec sqlcmd](https://msdn.microsoft.com/library/hh568447.aspx).

>[AZURE.NOTE] Il existe certaines différences dans cet utilitaire entre les plateformes Linux et Windows. Consultez la documentation pour plus d’informations.


#### <a name="database-access-libraries"></a>Bibliothèques d’accès aux base de données

Les bibliothèques sont disponibles dans R et Python aux bases de données access.

- Dans R, le package **RODBC** ou **dplyr** vous permet de la requête ou exécuter des instructions SQL sur le serveur de base de données.
- Dans Python, la bibliothèque **pyodbc** offre un accès de base de données avec ODBC en tant que la couche sous-jacente.  

Pour accéder à **Postgres**:

- À partir de r : Utilisez le package **RPostgreSQL**.
- À partir de Python : Utiliser la bibliothèque **psycopg2** .


### <a name="azure-tools"></a>Outils d’Azure
Les outils Azure suivants sont installés sur l’ordinateur virtuel :

- **Interface de ligne de commande Azure**: l’infrastructure du langage commun Azure vous permet de créer et gérer des ressources Azure via des commandes shell. Pour appeler les outils Azure, tapez simplement **aide azure**. Pour plus d’informations, consultez la [page de la documentation Azure infrastructure du langage commun](../virtual-machines-command-line-tools.md).
- **Explorateur de stockage de Microsoft Azure**: Microsoft Azure stockage Explorer est un outil graphique qui est utilisé pour parcourir les objets que vous avez stockés dans votre compte de stockage Azure et pour charger et télécharger des données vers et depuis des objets BLOB Azure. Vous pouvez accéder Explorateur de stockage à partir de l’icône de raccourci sur le bureau. Vous pouvez l’appeler à partir d’une invite de shell en tapant **StorageExplorer**. Vous devez être connecté à partir d’un client X2Go ou que vous X11 jeu de transfert vers le haut.
- **Bibliothèques Azure**: Voici quelques-unes des bibliothèques préinstallés.

 - **Python**: bibliothèques liés à la Azure dans Python installées sont **azure**, **azureml**, **pydocumentdb**et **pyodbc**. Avec les trois premières bibliothèques, vous pouvez accéder services stockage Azure et apprentissage automatique Azure Azure DocumentDB (une base de données NoSQL sur Azure). La bibliothèque quatrième, pyodbc (ainsi que le pilote ODBC Microsoft pour SQL Server), autorise l’accès à SQL Server, base de données SQL Azure et SQL Azure Data Warehouse à partir de Python en utilisant une interface ODBC. Entrez **liste pip** pour afficher toutes les bibliothèques répertoriées. Veillez à exécuter cette commande en version2.7 Python et 3,5 environnements.
 - **R**: les bibliothèques liés à la Azure dans R installées sont **AzureML** et **RODBC**.
 - **Java**: la liste des bibliothèques Java Azure sont accessibles dans le répertoire **/dsvm/sdk/AzureSDKJava** sur l’ordinateur virtuel. Les bibliothèques clés sont Azure Gestion du stockage et des API DocumentDB pilotes et JDBC pour SQL Server.  

Vous pouvez accéder au [portail Azure](https://portal.azure.com) à partir du navigateur Firefox préinstallé. Dans le portail Azure, vous pouvez créer, gérer et analyser les ressources Azure.

### <a name="azure-machine-learning"></a>Apprentissage automatique Azure

Azure apprentissage automatique est un service cloud entièrement gérée qui vous permet de générer, déployer et partager des solutions prédictive analytique. Vous construire vos expériences et des modèles à partir d’Azure Machine apprentissage Studio. Il est accessible à partir d’un navigateur web sur l’ordinateur données scientifique virtuel, visitez le [Microsoft Azure Machine Learning](https://studio.azureml.net).

Une fois que vous êtes connecté à Azure Machine d’apprentissage Studio, vous avez accès à une zone de dessin essais qui vous pouvez de créer un flux logique pour l’apprentissage algorithmes de l’ordinateur. Également, vous avez accès à un bloc-notes Jupyter hébergé sur apprentissage automatique Azure et que vous pouvez travailler en toute transparence avec les expériences dans Machine apprentissage Studio. Mettre l’apprentissage modèles que vous avez créée par les à la ligne dans une interface de service web de l’ordinateur. Ceci permet aux clients écrites dans n’importe quelle langue appeler des prévisions à partir de l’apprentissage des modèles de l’ordinateur. Pour plus d’informations, voir la [documentation d’apprentissage automatique](https://azure.microsoft.com/documentation/services/machine-learning/).

Vous pouvez également créer vos modèles dans R ou Python sur l’ordinateur virtuel, puis le déployer en production sur apprentissage automatique Azure. Nous avons installé bibliothèques dans R (**AzureML**) et Python (**azureml**) pour activer cette fonctionnalité.

Pour savoir comment déployer des modèles dans R et Python dans apprentissage automatique Azure, voir [dix pouvant être exécutées sur la science données Machine virtuelle](machine-learning-data-science-vm-do-ten-things.md) (en particulier, la section « créer des modèles à l’aide de R ou Python et effectuent les Azure apprentissage automatique »).

>[AZURE.NOTE] Ces instructions ont été écrites pour la version Windows de la machine virtuelle scientifique de données. Mais les informations fournies il sur le déploiement de modèles à Azure Machine formation s’applique à la VM Linux.

### <a name="machine-learning-tools"></a>Outils d’apprentissage automatique

La machine virtuelle est fourni avec quelques d’apprentissage automatique outils et des algorithmes qui ont été préalablement compilés et préinstallés localement. Parmi lesquels :

* **CNTK** (Calcul réseau Kit de ressources de Microsoft Research) : une profondeur Kit de ressources de formation.
* **Vowpal Wabbit**: un algorithme de formation en ligne rapide.
* **xgboost**: un outil qui fournit des algorithmes d’arborescence optimisée, augmentée.
* **Python**: Python Anaconda est fourni avec algorithmes d’apprentissage machine avec des bibliothèques comme Découvrez Scikit. Vous pouvez installer d’autres bibliothèques à l’aide de la `pip install` commande.
* **R**: une riche bibliothèque de fonctions d’apprentissage automatique est disponible pour R. Certaines des bibliothèques qui sont préinstallés sont lm, glm, randomForest, rpart. Autres bibliothèques peuvent être installées en exécutant :

        install.packages(<lib name>)

Voici quelques informations supplémentaires sur les outils d’apprentissage automatique trois premières dans la liste.

#### <a name="cntk"></a>CNTK
Il s’agit d’une source ouverte, approfondie Kit de ressources de formation. Il est un outil de ligne de commande (cntk) et se trouve déjà dans le chemin d’accès.

Pour exécuter un exemple de base, exécutez les commandes suivantes dans le shell :

    # Copy samples to your home directory and execute cntk
    cp -r /dsvm/tools/CNTK-2016-02-08-Linux-64bit-CPU-Only/Examples/Other/Simple2d cntkdemo
    cd cntkdemo/Data
    cntk configFile=../Config/Simple.cntk

La sortie de modèle est dans *~/cntkdemo/Output/Models*.

Pour plus d’informations, consultez la section CNTK de [GitHub](https://github.com/Microsoft/CNTK)et le [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).


#### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit est une machine système qui utilise des techniques telles qu’allreduce en ligne, hachage, réductions, learning2search et actif, la formation et d’apprentissage interactif.

Pour exécuter l’outil sur un exemple très simple, procédez comme suit :

    cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
    cd vwdemo
    vw house_dataset

Il existe des démonstrations des autres, plus grandes dans ce répertoire. Pour plus d’informations sur VW, consultez [Cette section de GitHub](https://github.com/JohnLangford/vowpal_wabbit)et le [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

#### <a name="xgboost"></a>xgboost
Il s’agit d’une bibliothèque qui est conçue et optimisée pour les algorithmes augmentée (arborescence). L’objectif de cette bibliothèque est distribuer les limites calcul d’ordinateurs sur les variées nécessaires pour fournir à grande échelle arborescence augmentation qui est format SVG, portable et précis.

Il est fourni sous forme d’une ligne de commande, ainsi que d’une bibliothèque de R.

Pour utiliser cette bibliothèque dans R, vous pouvez démarrer une session R interactive (juste en tapant **R** dans shell) et charger la bibliothèque.

Voici un exemple simple, que vous pouvez exécuter invite R :

    library(xgboost)

    data(agaricus.train, package='xgboost')
    data(agaricus.test, package='xgboost')
    train <- agaricus.train
    test <- agaricus.test
    bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                    eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
    pred <- predict(bst, test$data)

Pour exécuter la ligne de commande xgboost, voici les commandes à exécuter dans le shell :

    cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
    cd xgboostdemo
    xgboost mushroom.conf


Un fichier .model écrit dans le répertoire spécifié. Vous trouverez des informations sur cet exemple démo [sur GitHub](https://github.com/dmlc/xgboost/tree/master/demo/binary_classification).

Pour plus d’informations sur xgboost, consultez la [page de la documentation xgboost](https://xgboost.readthedocs.org/en/latest/)et son [référentiel Github](https://github.com/dmlc/xgboost).

#### <a name="rattle"></a>Hochet
Hochet ( **R** **A**nalytical **T**outil **T**o **L**gagner asily **E**) utilise des données via une interface utilisateur et modélisation. Il présente les statistiques et visuelles résumés des données, données transformations qui peuvent être facilement modélisation, crée des modèles qui fonctionne sans surveillance et contrôlés à partir des données, présente les performances des modèles sous forme de graphique, et jeux de données de nouveaux scores. Il génère également du code R, réplication les opérations dans l’interface utilisateur qui peuvent être exécutées directement dans R ou utilisées comme point de départ pour une analyse approfondie.

Pour exécuter Hochet, vous devez être dans une graphique connexion session du bureau. Dans le terminal, tapez ```R``` entrer l’environnement R. À l’invite R, entrez les commandes suivantes :

    library(rattle)
    rattle()

Maintenant une interface graphique s’ouvre avec un ensemble d’onglets. Voici les étapes de démarrage rapide dans Hochet nécessaire pour utiliser un jeu de données d’exemple météo et créez un modèle. Dans certains les actions suivantes, vous êtes invité à installer et charger certains packages R requis qui ne sont pas déjà sur le système automatiquement.

>[AZURE.NOTE] Si vous n’avez pas accès à installer le package dans le répertoire système (par défaut), vous pouvez recevoir un message dans la fenêtre de console R pour installer les packages à votre bibliothèque personnelle. Répondre à *y* si vous voyez ces invites.

1. Cliquez sur **exécuter**.
2. Une boîte de dialogue s’affiche, vous demandant si vous souhaitez utiliser le jeu de données d’exemple météo. Cliquez sur **Oui** pour charger l’exemple.
3. Cliquez sur l’onglet **modèle** .
4. Cliquez sur **exécuter** pour créer un arbre de décision.
5. Cliquez sur **dessiner** pour afficher l’arbre de décision.
6. Cliquez sur le bouton d’option **forêt** , puis cliquez sur **exécuter** pour créer une forêt aléatoires.
7. Cliquez sur l’onglet **évaluer** .
8. Cliquez sur le bouton d’option **risque** , puis cliquez sur **exécuter** pour afficher deux tracés de performances risque (Cumulative).
9. Cliquez sur l’onglet **journal** pour afficher le code de générer R pour les opérations précédentes.
(En raison d’un bogue dans la version actuelle de Hochet, vous devez insérer un *#* caractère devant *exporter ce journal...* dans le texte du journal.)
10. Cliquez sur le bouton **Exporter** pour enregistrer le fichier de script R nommé *weather_script. R* vers le dossier de base.

Vous pouvez quitter Hochet et R. Vous pouvez désormais modifier le script R généré ou utilisez-le tel qu’il doit exécuter à tout moment pour répéter tout ce qui a été fait dans l’interface utilisateur vibrer. En particulier pour les débutants en R, il s’agit d’un moyen simple pour effectuer des analyses et machine apprentissage dans une interface graphique simple, lors de la génération automatiquement code dans R pour modifier et/ou plus rapidement.


## <a name="next-steps"></a>Étapes suivantes
Voici comment vous pouvez continuer votre apprentissage et l’exploration :

* La procédure pas à pas [scientifique de données sur la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-walkthrough.md) vous montre comment effectuer plusieurs tâches scientifique des données communes avec la virtuelle scientifique de données Linux sa mise en service ici. 
* Explorer les divers outils scientifique de données sur la science données machine virtuelle par essayer les outils décrits dans cet article. Vous pouvez également exécuter *dsvm-plus-informations* sur l’environnement au sein de la machine virtuelle pour une présentation de base et les pointeurs pour plus d’informations sur les outils sont installés sur l’ordinateur virtuel.  
* Apprenez à créer des solutions analytiques de bout en bout systématiquement en utilisant le [Processus de scientifique des données d’équipe](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).
* Visitez la [Galerie d’Analytique Cortana](http://gallery.cortanaanalytics.com) machine données et l’apprentissage analytique exemples qui utilisent la gamme Analytique Cortana.
