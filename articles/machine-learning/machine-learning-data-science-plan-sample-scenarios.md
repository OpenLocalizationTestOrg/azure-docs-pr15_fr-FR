<properties
    pageTitle="Scénarios de l’Analytique avancée processus et technologie dans l’apprentissage automatique Azure | Microsoft Azure"
    description="Sélectionnez les scénarios appropriés pour ce faire avancées prédictive analytique avec le processus d’équipe données scientifique."
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
    ms.date="09/19/2016" 
    ms.author="bradsev" />


# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénarios d’analytique avancée dans l’apprentissage automatique Azure

Cet article décrit la diversité des exemples de sources de données et les scénarios cible qui peuvent être gérés par l' [Équipe données scientifique processus (TDSP)](data-science-process-overview.md). Le TDSP fournit une approche systématique permettant aux équipes de collaborer sur la création d’applications intelligentes. Les scénarios présentés ici illustrent les options disponibles dans le flux de travail du traitement des données qui dépendent les caractéristiques de données, les emplacements de source et les référentiels cible dans Azure.

L' **arbre de décision** de sélection les exemples de scénarios qui est approprié pour vos données et l’objectif est présenté à la section précédente.

>[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


Chacune des sections ci-dessous présente un exemple de scénario. Pour chaque scénario, une science données possibles ou analytique avancées Azure ressources de support et de flux sont répertoriées.

>[AZURE.NOTE]**Pour tous les scénarios suivants, vous devez :**
><br/>
>* [Créer un compte de stockage](../storage/storage-create-storage-account.md)
><br/>
>* [Créer un espace de travail d’apprentissage automatique Azure](machine-learning-create-workspace.md)


## <a name="smalllocal"></a>Scénario \#1 : petites et moyenne dataset sous forme de tableau dans des fichiers locaux

![Petites et moyennes fichiers locaux][1]

#### <a name="additional-azure-resources-none"></a>Ressources Azure supplémentaires : aucun

1.  Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

2.  Télécharger un jeu de données.

3.  Créer un flux expérience d’apprentissage automatique Azure commençant par jeu téléchargé.

## <a name="smalllocalprocess"></a>Scénario \#2 : petites et moyenne dataset de fichiers locaux nécessitant un traitement

![Petites et moyennes fichiers locaux avec traitement][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure IPython bloc-notes en cours d’exécution.

2.  Télécharger des données dans un conteneur de stockage Azure.

3.  Prétraitement et effacer les données dans le bloc-notes IPython, accès aux données depuis le conteneur de stockage Azure.

4.  Transformer des données en nettoyé, sous forme tabulaire.

5.  Enregistrer les données transformées dans des objets BLOB Azure.

6.  Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

7.  Lire les données à partir d’objets BLOB Azure à l’aide de l' [Importation de données] [ import-data] module.

8. Créer un flux expérience d’apprentissage automatique Azure commençant par jeu ingérés.

## <a name="largelocal"></a>Scénario \#3 : ensemble de données volumineux de fichiers locaux, ciblage des objets BLOB Azure

![Fichiers volumineux locales][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure IPython bloc-notes en cours d’exécution.

2.  Télécharger des données dans un conteneur de stockage Azure.

3.  Prétraitement et effacer les données dans le bloc-notes IPython, accès aux données d’objets BLOB Azure.

4.  Transformer des données en nettoyé, sous forme tabulaire, si nécessaire.

5.  Explorer les données et créer des fonctions selon vos besoins.

6.  Extraire un échantillon de données de petites et moyennes.

7.  Enregistrer les exemples de données dans des objets BLOB Azure.

8. Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

9. Lire les données à partir d’objets BLOB Azure à l’aide de l' [Importation de données] [ import-data] module.

10. Créer des flux d’expérience d’apprentissage automatique Azure commençant par jeu ingérés.


## <a name="smalllocaltodb"></a>Scénario \#4 : petites et moyenne dataset de fichiers locaux, SQL Server dans une Virtal Machine Azure le ciblage du

![Petites et moyennes fichiers locaux DB SQL dans Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (SQL Server / serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure SQL Server + IPython bloc-notes en cours d’exécution.

2.  Télécharger des données dans un conteneur de stockage Azure.

3.  Prétraitement et effacer les données dans un conteneur de stockage Azure à l’aide de IPython bloc-notes.

4.  Transformer des données en nettoyé, sous forme tabulaire, si nécessaire.

5.  Enregistrer les données dans des fichiers de machine virtuelle-local (IPython bloc-notes s’exécute sur machine virtuelle, lecteurs locaux font référence aux lecteurs machine virtuelle).

6.  Charger les données à base de données SQL Server s’exécutant sur une machine virtuelle Azure.

    Option \#1 : utilisant SQL Server Management Studio.

    - Connexion à SQL Server machine virtuelle
    - Exécutez SQL Server Management Studio.
    - Créer des tables de base de données et cibles.
    - Utilisez une de la plus grande partie importer méthodes pour charger les données à partir de fichiers de machine virtuelle-local.

    Option \#2 : à l’aide de IPython bloc-notes – pas recommandé pour les jeux de données moyens et plus grandes<!-- -->    
    - Chaîne de connexion ODBC permet d’accéder à SQL Server sur machine virtuelle.
    - Créer des tables de base de données et cibles.
    - Utilisez une de la plus grande partie importer méthodes pour charger les données à partir de fichiers de machine virtuelle-local.

7.  Explorer des données, créer des fonctions selon vos besoins. Notez que les fonctionnalités ne souhaitez pas être réalisée dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

8. Choisir une taille d’échantillon de données, si nécessaire, et/ou vous le souhaitez.

9. Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

10. Lire les données directement à partir de SQL Server à l’aide de l' [Importation de données] [ import-data] module. Collez la requête nécessaire qui extrait les champs et crée les fonctions d’exemples de données si nécessaire directement dans les [Importer des données] [ import-data] requête.

11. Créer des flux d’expérience d’apprentissage automatique Azure commençant par jeu ingérés.

## <a name="largelocaltodb"></a>Scénario \#5 : ensemble de données volumineux dans des fichiers locaux, cibler SQL Server dans la machine virtuelle Azure

![Fichiers volumineux locales DB SQL dans Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (SQL Server / serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure qui exécute SQL Server et IPython bloc-notes serveur.

2.  Télécharger des données dans un conteneur de stockage Azure.

3.  (Facultatif) Prétraitement et effacer les données.

    un.  Prétraitement et effacer les données dans le bloc-notes IPython, accès aux données d’objets BLOB Azure.

    b.  Transformer des données en nettoyé, sous forme tabulaire, si nécessaire.

    c.  Enregistrer les données dans des fichiers de machine virtuelle-local (IPython bloc-notes s’exécute sur machine virtuelle, lecteurs locaux font référence aux lecteurs machine virtuelle).

4.  Charger les données à base de données SQL Server s’exécutant sur une machine virtuelle Azure.

    un.  Connexion à SQL Server machine virtuelle.

    b.  Si les données n'enregistrées pas déjà, téléchargez les fichiers de données à partir de conteneur de stockage Azure au dossier machine virtuelle local.

    c.  Exécutez SQL Server Management Studio.

    d.  Créer des tables de base de données et cibles.

    e.  Utilisez une de la plus grande partie méthodes pour charger les données d’importation.

    f.  Si tableau jointures sont nécessaires, créez des index pour accélérer les jointures.

     > [AZURE.NOTE] Pour accélérer le chargement de tailles de grande quantité de données, il est recommandé que vous créez des tables partitionnées et les données en parallèle d’importation en bloc. Pour plus d’informations, voir [Importation des données en parallèle à des Tables SQL partition](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorer des données, créer des fonctions selon vos besoins. Notez que les fonctionnalités ne souhaitez pas être réalisée dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

6.  Choisir une taille d’échantillon de données, si nécessaire, et/ou vous le souhaitez.

7.  Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

8. Lire les données directement à partir de SQL Server à l’aide de l' [Importation de données] [ import-data] module. Collez la requête nécessaire qui extrait les champs et crée les fonctions d’exemples de données si nécessaire directement dans les [Importer des données] [ import-data] requête.

9. Flux expérience apprentissage automatique Azure simple en commençant par dataset téléchargé

## <a name="largedbtodb"></a>Scénario \#6 : ensemble de données volumineux dans un serveur SQL de base de données local, un ciblage de SQL Server dans une Machine virtuelle Azure

![Grande SQL DB local à DB SQL dans Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (SQL Server / serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure qui exécute SQL Server et IPython bloc-notes serveur.

2.  Utilisez une des données exportez des méthodes pour exporter les données à partir de SQL Server aux fichiers image.

    > [AZURE.NOTE] Si vous décidez de déplacer toutes les données à partir de la base de données local, une autre méthode (plus rapide) pour déplacer la base de données complète vers l’instance SQL Server dans Azure. Ignorer les étapes suivantes pour exporter des données, créer la base de données et charger/importer des données à la base de données cible et suivez l’autre méthode.

3.  Télécharger des fichiers de vidage au conteneur de stockage Azure.

4.  Charger les données à une base de données SQL Server en cours d’exécution sur une Machine virtuelle Azure.

    un.  Connectez-vous à la machine virtuelle SQL Server.

    b.  Télécharger des fichiers de données à partir d’un conteneur de stockage Azure dans le dossier machine virtuelle local.

    c.  Exécutez SQL Server Management Studio.

    d.  Créer des tables de base de données et cibles.

    e.  Utilisez une de la plus grande partie méthodes pour charger les données d’importation.

    f.  Si tableau jointures sont nécessaires, créez des index pour accélérer les jointures.

    > [AZURE.NOTE] Pour accélérer le chargement de tailles de grande quantité de données, créez des tables partitionnées et en bloc importer les données en parallèle. Pour plus d’informations, voir [Importation des données en parallèle à des Tables SQL partition](machine-learning-data-science-parallel-load-sql-partitioned-tables.md).

5.  Explorer des données, créer des fonctions selon vos besoins. Notez que les fonctionnalités ne souhaitez pas être réalisée dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

6.  Choisir une taille d’échantillon de données, si nécessaire, et/ou vous le souhaitez.

7.  Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

8. Lire les données directement à partir de SQL Server à l’aide de l' [Importation de données] [ import-data] module. Collez la requête nécessaire qui extrait les champs et crée les fonctions d’exemples de données si nécessaire directement dans les [Importer des données] [ import-data] requête.

9. Flux d’expérience apprentissage automatique Azure simple en commençant par dataset téléchargé.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Autre méthode pour copier une base de données complète d’un serveur SQL local à la base de données SQL Azure

![Détacher la base de données locale et joindre à la base de données SQL dans Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Ressources Azure supplémentaires : Machine virtuelle Azure (SQL Server / serveur IPython bloc-notes)

Pour répliquer la base de données SQL Server entière dans votre machine virtuelle de SQL Server, vous devez copier une base de données à partir d’un seul emplacement/serveur vers une autre, en supposant que la base de données peut être déconnecté temporairement. Pour cela, dans l’Explorateur d’objets SQL Server Management Studio, ou en utilisant les commandes Transact-SQL équivalentes.

1. Détacher la base de données à l’emplacement source. Pour plus d’informations, voir [détacher une base de données](https://technet.microsoft.com/library/ms191491(v=sql.110).aspx).
2. Dans la fenêtre de l’Explorateur Windows ou l’invite de commandes Windows, copiez le fichier de base de données détaché ou fichiers et fichier journal ou à l’emplacement cible sur la machine de virtuelle SQL Server dans Azure.
3. Joindre les fichiers copiés à l’instance SQL Server cible. Pour plus d’informations, voir [joindre une base de données](https://technet.microsoft.com/library/ms190209(v=sql.110).aspx).

[Déplacer une base de données à l’aide de détacher et de joindre (Transact-SQL)](https://technet.microsoft.com/library/ms187858(v=sql.110).aspx)

## <a name="largedbtohive"></a>Scénario \#7 : données volumineuses dans des fichiers locaux, cibler Hive de base de données dans les clusters Azure HDInsight Hadoop

![Données volumineuses dans cible local Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Ressources Azure supplémentaires : Azure HDInsight Hadoop Cluster et Machine virtuelle Azure (serveur IPython bloc-notes)

1.  Créer une Machine virtuelle Azure server IPython bloc-notes en cours d’exécution.

2.  Créer un cluster Azure HDInsight Hadoop.

3.  (Facultatif) Prétraitement et effacer les données.

    un.  Prétraitement et effacer les données dans le bloc-notes IPython, accès aux données d’objets BLOB Azure.

    b.  Transformer des données en nettoyé, sous forme tabulaire, si nécessaire.

    c.  Enregistrer les données dans des fichiers de machine virtuelle-local (IPython bloc-notes s’exécute sur machine virtuelle, lecteurs locaux font référence aux lecteurs machine virtuelle).

4.  Télécharger des données dans le conteneur par défaut du cluster Hadoop sélectionné à l’étape 2.

5.  Charger les données à base de données Hive Azure HDInsight Hadoop cluster.

    un.  Se connecter au nœud de tête du cluster Hadoop

    b.  Ouvrez la ligne de commande Hadoop.

    c.  Entrez le répertoire racine Hive par commande `cd %hive_home%\bin` en ligne de commande Hadoop.

    d.  Exécuter les requêtes Hive pour créer une base de données et des tables et charger les données depuis le stockage blob Hive tables.

    > [AZURE.NOTE] Si les données sont volumineux, les utilisateurs peuvent créer la table Hive avec partitions. Ensuite, les utilisateurs peuvent utiliser un `for` boucle dans Hadoop ligne de commande sur le nœud principal pour charger les données dans la table Hive partitionnée par partition.

6.  Explorer les données et créer des fonctions de ligne de commande de Hadoop le cas échéant. Notez que les fonctionnalités ne souhaitez pas être réalisée dans les tables de base de données. Notez seulement la requête nécessaire pour les créer.

    un.  Se connecter au nœud de tête du cluster Hadoop

    b.  Ouvrez la ligne de commande Hadoop.

    c.  Entrez le répertoire racine Hive par commande `cd %hive_home%\bin` en ligne de commande Hadoop.

    d.  Exécuter les requêtes Hive en ligne de commande Hadoop sur le nœud principal du cluster Hadoop pour Explorer les données et créer des fonctions selon vos besoins.

7.  Si nécessaire, et/ou vous le souhaitez, exemples de données pour l’ajuster à Azure Machine apprentissage Studio.

8.  Se connecter à [Machine Azure d’apprentissage Studio](https://studio.azureml.net/).

9. Lire les données directement à partir de la `Hive Queries` à l’aide de l' [Importation de données] [ import-data] module. Collez la requête nécessaire qui extrait les champs et crée les fonctions d’exemples de données si nécessaire directement dans les [Importer des données] [ import-data] requête.

10. Flux d’expérience apprentissage automatique Azure simple en commençant par dataset téléchargé.

## <a name="decisiontree"></a>Arbre de décision de sélection de scénario
------------------------

Le diagramme suivant récapitule les scénarios décrits ci-dessus et avancées Analytique processus et technologie choisies que vous prenez sur chacun des scénarios détaillés. Notez que traitement des données, d’exploration, ingénierie de fonctionnalité et échantillonnages peuvent prendre placer dans une ou plusieurs méthode/environnement--à la source, intermédiaire, et/ou environnements cible et peut être entreprise itératif selon vos besoins. Le diagramme sert une illustration de certaines des flux possibles uniquement et ne fournit pas énumération exhaustive.

![Exemples de scénarios procédure pas à pas de processus DS][8]

### <a name="advanced-analytics-in-action-examples"></a>Advanced Analytique en action exemples

De bout en bout apprentissage automatique Azure procédures pas à pas qui utilisent l’avancées Analytique processus et technologie à l’aide des jeux de données publics, voir :


* [Processus scientifique des données d’équipe en action : à l’aide de SQL Server](machine-learning-data-science-process-sql-walkthrough.md).
* [Processus scientifique des données d’équipe en action : utilisation des clusters HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md).


[1]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/machine-learning-data-science-plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
