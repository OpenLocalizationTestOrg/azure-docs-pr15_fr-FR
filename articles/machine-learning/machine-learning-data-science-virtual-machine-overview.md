<properties
    pageTitle="Qu’est une Machine virtuelle de Science données ? | Microsoft Azure"
    description="Découvrez les scénarios clés, fonctionnalités et comment se familiariser avec les données scientifique Machines virtuelles, un environnement et le Kit de ressources de prêt pour analytique."
    keywords="outils d’organisation scientifique de données, données scientifique virtuelle machine, tools pour scientifique de données, scientifique de données linux"
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
    ms.date="10/17/2016"
    ms.author="bradsev" />


# <a name="introduction-to-the-cloud-based-data-science-virtual-machine-for-linux-and-windows"></a>Introduction à la cloud données scientifique Machine virtuelle pour Linux et Windows

L’ordinateur données scientifique virtuel est une image de machine virtuelle personnalisée sur Azure cloud de Microsoft conçue spécifiquement pour effectuant scientifique de données. Il a nombre scientifique données courants et autres outils préinstallés et préconfigurés pour relancer la création d’applications intelligentes pour analytique avancées. Il est disponible sur Windows Server 2012 ou Linux basée sur OpenLogic 7.2 CentOS versions. 

Cette rubrique explique ce que vous pouvez faire avec la machine virtuelle scientifique de données présente certaines des scénarios clés pour l’utilisation de la machine virtuelle, détaille les principales fonctionnalités disponibles dans les versions Windows et Linux et fournit des instructions sur la façon de commencer à les utiliser.


## <a name="what-can-i-do-with-the-data-science-virtual-machine"></a>Que puis-je faire avec les données scientifique la Machine virtuelle ?

L’objectif de la Machine virtuelle de données scientifique est de proposer professionnels de données à tous les rôles et les niveaux de compétences avec un environnement scientifique données sans frictions. Cette machine virtuelle enregistre beaucoup de temps que vous le feriez dépensez si vous aviez transférées un environnement comparable à votre guise. En revanche, démarrer votre projet scientifique données immédiatement dans une instance de machine virtuelle nouvellement créée. 

La machine virtuelle scientifique de données est conçue et configurée pour l’utilisation de des scénarios d’utilisation large. Vous pouvez ajuster votre environnement vers le haut ou vers le bas selon les besoins votre projet. Vous êtes en mesure d’utiliser la langue par défaut pour programmer des tâches de données scientifique. Vous pouvez installer d’autres outils et personnaliser le système pour exactement à vos besoins.
 
## <a name="key-scenarios"></a>Scénarios clés
Cette section suggère quelques scénarios clés pour lesquels la machine virtuelle scientifique données peuvent être déployée.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Bureau analytique préconfigurés dans le cloud

La machine virtuelle scientifique données fournit une configuration de base pour les équipes de science données cherchant à remplacer leur bureau local par un bureau cloud gérées. Cette référence garantit que tous les scientifiques de données d’une équipe ont une configuration cohérente avec laquelle vous voulez vérifier expériences et encourage la collaboration. Il permet également de réduire les coûts en réduisant la charge sysadmin et en enregistrant le temps nécessaire pour évaluer, installer et mettre à jour les différents packages logiciels nécessaires pour faire analytique avancées.  

### <a name="data-science-training-and-education"></a>Données scientifique formation

Formateurs d’entreprise et les enseignants qui enseigner scientifique données classes en règle générale, fournissent une image de machine virtuelle pour vous assurer que leurs étudiants ont une configuration cohérente et que les exemples fonctionnent mis. La machine virtuelle scientifique données crée un environnement à la demande avec une configuration cohérente qui facilite les défis incompatibilité entre et prise en charge. Cas où ces environnements doivent être générés fréquemment, en particulier pour les cours de formation plus courts, bénéficient considérablement.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Capacité élastique à la demande pour les projets à grande échelle

Données scientifique hackathons/concours ou modélisation de données de grande taille et de l’exploration nécessitent répartie capacité matérielle, généralement de courte durée. La machine virtuelle scientifique données peut vous aider à répliquer l’environnement scientifique données rapidement à la demande, sur mise à l’échelle des serveurs qui autorisent expériences nécessitant des ressources informatiques puissante à exécuter.

### <a name="short-term-experimentation-and-evaluation"></a>Évaluation et essais à court terme

La machine virtuelle scientifique de données peut être utilisé pour évaluer ou Découvrez les outils tels que Microsoft R Server, SQL Server, outils de Visual Studio, Jupyter, d’apprentissage pour annoncer / ML outils et nouveaux outils populaires dans la Communauté avec un minimum de configuration effort. Étant donné que la machine virtuelle scientifique de données peut être configurée rapidement, elle peut être appliquée dans d’autres scénarios d’utilisation à court terme, tels que la réplication expériences publiés, l’exécution des démonstrations, des procédures suivantes dans sessions en ligne ou les didacticiels de conférence.


## <a name="whats-included-in-the-data-science-vm"></a>Ce qui est inclus dans la machine virtuelle des données ?

La Machine virtuelle de Science données comporte de nombreux outils de science populaires données déjà installé et configuré. Il inclut également des outils qui permettent d’utiliser des divers produits de données et analytique Azure. Vous pouvez Explorer et créer des modèles prédictives sous jeux de données à grande échelle à l’aide de Microsoft R Server ou SQL Server 2016. Un hôte d’autres outils à partir de la Communauté open source et de Microsoft sont également blocs-notes et inclus, ainsi que les exemples de code. Le tableau suivant détaille et compare les principaux composants inclus dans les éditions Windows et Linux de la Machine virtuelle de données scientifique.


|**Édition de Windows** | **Linux Edition** |
|----------------|---------------|
|Microsoft R Server Édition développeur | Microsoft R Server Édition développeur |
|Anaconda Python 2.7, 3.5 | Anaconda Python 2.7, 3.5 |
|Serveur Jupyter bloc-notes (R, Python) | JupyterHub : Blocs-notes Jupyter multi-utilisateurs (R, Python, Ariane) |
|SQL Server 2016 Developer Edition : Fichiers de base de données analytique avec les services R | Postgres, non SQL (outil de base de données), pilotes SQL Server et ligne de commande (bcp, sqlcmd) |
|Édition de communauté Visual Studio 2015 (IDE) </br> -Azure HDInsight (Hadoop), données Lake, outils de données SQL Server </br> -R Node.js et Python tools pour Visual Studio |IDEs et les éditeurs </br> -Eclipse avec plug-in Azure Kit de ressources </br> -Emacs (avec ESS, auctex) gedit |
|Ordinateur de bureau Power BI | -- |
|Outils d’apprentissage automatique </br> -Intégration avec apprentissage automatique Azure </br> -CNTK (formation approfondie/AI) </br> -Xgboost (populaires outil ML dans concours scientifique de données) </br> -Vowpal Wabbit (étudiant rapidement en ligne) </br> -Dansais (visuelle des données de démarrage rapide et outil analytique) </br> -Mxnet (formation approfondie/AI) | Outils d’apprentissage automatique </br> -Intégration d’apprentissage automatique Azure </br> -CNTK (formation approfondie/AI) </br> -Xgboost (populaires outil ML dans concours scientifique de données) </br> -Vowpal Wabbit (étudiant rapidement en ligne) </br> -Dansais (visuelle des données de démarrage rapide et outil analytique)  |
| Kits d’accéder aux Azure et Cortana Intelligence Suite de services | Kits d’accéder aux Azure et Cortana Intelligence Suite de services |
| Outils de déplacement de données et de gestion des ressources Azure et Big Data : Explorateur de stockage Azure, infrastructure du langage commun, PowerShell, AdlCopy (Lake des données Azure), AzCopy, dtui (pour DocumentDB), passerelle de gestion des données de Microsoft | Outils de déplacement de données et de gestion des ressources Azure et Big Data : Explorateur de stockage Azure, infrastructure du langage commun |
| GIT, plug-in Visual Studio Team Services | GIT |
| Port Windows les plus populaires utilitaires de ligne de commande de Linux/Unix accessibles via GitBash/invite de commandes | -- |



## <a name="how-to-get-started-with-the-windows-data-science-vm"></a>Comment se familiariser avec la virtuelle Windows données scientifique

- Créer une instance de la machine virtuelle sur Windows en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/standard-data-science-vm/) , cliquez sur le bouton **créer un Machine virtuelle** vert.
- Connectez-vous à la machine virtuelle à partir de votre Bureau à distance en utilisant les informations d’identification que vous avez spécifié lors de la création de la machine virtuelle.
- Pour découvrir et lancer les outils disponibles, cliquez sur le menu **Démarrer** .


## <a name="get-started-with-the-linux-data-science-vm"></a>Prise en main la machine virtuelle scientifique de données Linux

- Pour créer une instance de la machine virtuelle sur Linux (basées sur OpenLogic CentOS) en accédant à [cette page](https://azure.microsoft.com/marketplace/partners/microsoft-ads/linux-data-science-vm/) , cliquez sur le bouton **créer un Machine virtuelle** .
- Connectez-vous à la machine virtuelle à partir d’un client SSH, tel que Putty ou SSH commande, en utilisant les informations d’identification que vous avez spécifié lors de la création de la machine virtuelle.
- Dans l’invite de commandes, entrez dsvm-plus-info.
- Pour un graphique Bureau, télécharger le client X2Go pour votre plateforme client [ici](http://wiki.x2go.org/doku.php/doc:installation:x2goclient) et suivez les instructions dans le document Linux données scientifique machine virtuelle [mise en service de la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-intro.md#installing-and-configuring-x2go-client).


## <a name="next-steps"></a>Étapes suivantes

### <a name="for-the-windows-data-science-vm"></a>Pour Windows données Science machine virtuelle

- Pour plus d’informations sur l’exécution des outils disponibles dans la version de Windows, voir [mise en service de la Machine virtuelle Microsoft données Science](machine-learning-data-science-provision-vm.md) et
-  Pour plus d’informations sur la façon d’effectuer diverses tâches nécessaires à votre projet scientifique de données sur la machine virtuelle Windows, voir [dix pouvant être exécutées sur la science données Machine virtuelle](machine-learning-data-science-vm-do-ten-things.md).

### <a name="for-the-linux-data-science-vm"></a>Pour le données Linux scientifique machine virtuelle

- Pour plus d’informations sur l’exécution des outils disponibles dans la version Linux, voir [mise en service de la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-intro.md).
- Pour une procédure pas à pas qui vous montre comment effectuer plusieurs tâches scientifique données courantes avec la VM Linux, voir [organisation scientifique de données sur la Machine virtuelle Linux données scientifique](machine-learning-data-science-linux-dsvm-walkthrough.md).
