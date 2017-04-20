<properties
   pageTitle="Charger les données à partir de SQL Server dans Azure SQL données Warehouse (SSIS) | Microsoft Azure"
   description="Vous montre comment créer un package SQL Server Integration Services (SSIS) pour déplacer des données d’un large éventail de sources de données vers SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/08/2016"
   ms.author="lodipalm;sonyama;barbkess"/>

# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-ssis"></a>Charger les données à partir de SQL Server dans Azure SQL données Warehouse (SSIS)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)


Créer un package SQL Server Integration Services (SSIS) pour charger les données à partir de SQL Server dans SQL Azure Data Warehouse. Vous pouvez éventuellement restructurer, transformer et nettoyer les données qui traversent le flux de données SSIS.

Dans ce didacticiel, vous allez :

- Créer un nouveau projet Integration Services dans Visual Studio.
- Se connecter aux sources de données, notamment SQL Server (en tant que source) et SQL Data Warehouse (comme destination).
- Créer un package SSIS qui charge les données provenant de la source dans la destination.
- Exécuter le package SSIS pour charger les données.

Ce didacticiel utilise SQL Server comme source de données. SQL Server peut s’exécuter en local ou sur une machine virtuelle Azure.

## <a name="basic-concepts"></a>Concepts de base

Le package est l’unité de travail dans SSIS. Lots associés sont regroupées dans les projets. Vous créez des projets et les packs de conception dans Visual Studio avec SQL Server Data Tools. Le processus de conception est un processus visuel dans lequel vous faites glisser et déposez des composants à partir de la boîte à outils vers l’aire de conception, relier et définissez leurs propriétés. Une fois que vous avez terminé votre package, vous pouvez éventuellement le déployer à SQL Server pour la gestion complète, la surveillance et la sécurité.

## <a name="options-for-loading-data-with-ssis"></a>Options de chargement des données avec SSIS

SQL Server Integration Services (SSIS) est un ensemble d’outils flexible qui fournit de nombreuses options pour la connexion et charger les données, Data Warehouse SQL.

1. Utiliser une Destination NET ADO pour vous connecter à SQL Data Warehouse. Ce didacticiel utilise une Destination NET ADO, car il comporte les options de configuration moins.
2. Utiliser une Destination OLE DB pour vous connecter à SQL Data Warehouse. Cette option peut fournir des performances légèrement supérieures à celles de la Destination NET ADO.
3. La tâche de télécharger Blob Azure permet d’organiser les données dans le stockage Blob Azure. Puis utilisez la tâche SSIS Execute SQL pour lancer un script Polybase qui charge les données dans Data Warehouse SQL. Cette option offre les meilleures performances des trois options répertoriés ici. Pour obtenir la tâche télécharger des objets Blob Azure, téléchargez le [Microsoft SQL Server 2016 Integration Services Feature Pack pour Azure][]. Pour en savoir plus sur Polybase, voir [PolyBase Guide][].

## <a name="before-you-start"></a>Avant de commencer

Pour lancer ce didacticiel, vous devez :

1. **SQL Server Integration Services (SSIS)**. SSIS est un composant de SQL Server et requiert une version d’évaluation ou une version sous licence de SQL Server. Pour obtenir une version d’évaluation de SQL Server 2016 Preview, voir [SQL Server évaluations][].
2. **Visual Studio**. Pour obtenir l’édition de communauté 2015 gratuite de Visual Studio, voir [Visual Studio Communauté][].
3. **SQL Server Data Tools pour Visual Studio (SSDT)**. Pour obtenir SQL Server Data Tools pour Visual Studio 2015, voir [Télécharger SQL Server Data Tools (SSDT)][].
4. **Exemples de données**. Ce didacticiel utilise des exemples de données stockées dans SQL Server dans la base de données exemple AdventureWorks comme source de données à charger dans SQL Data Warehouse. Pour obtenir la base de données exemple AdventureWorks, voir [Bases de données AdventureWorks 2014][].
5. **Autorisations et base de données A SQL Data Warehouse**. Ce didacticiel se connecte à une instance SQL Data Warehouse et charge les données. Vous devez disposer des autorisations pour créer une table et charger les données.
6. **Une règle de pare-feu**. Vous devez créer une règle de pare-feu sur SQL Data Warehouse avec l’adresse IP de votre ordinateur local avant que vous pouvez télécharger des données sur le Data Warehouse SQL.

## <a name="step-1-create-a-new-integration-services-project"></a>Étape 1 : Créer un nouveau projet Integration Services

1. Lancez Visual Studio 2015.
2. Dans le menu **fichier** , sélectionnez Nouveau **| Projet**.
3. Accédez à la **installé | Modèles | À la décision | Services d’intégration** types de projets.
4. Sélectionnez **projet Integration Services**. Indiquez les valeurs pour **nom** et un **emplacement**, puis sur **OK**.

Visual Studio s’ouvre et crée un nouveau projet Integration Services (SSIS). Puis Visual Studio ouvre le concepteur pour le package SSIS nouveau unique (Package.dtsx) dans le projet. Vous voyez les zones d’écran suivantes :

- Sur la gauche, la **boîte à outils** des composants SSIS.
- Au milieu, l’aire de conception, avec plusieurs onglets. Vous utilisez généralement au moins du **Flux de contrôle** et les onglets **De flux de données** .
- Sur la droite, l' **Explorateur de solutions** et les volets de **Propriétés** .

    ![][01]

## <a name="step-2-create-the-basic-data-flow"></a>Étape 2 : Créer le flux de base de données

1. Faites glisser une tâche de flux de données à partir de la boîte à outils vers le centre de l’aire de conception (sous l’onglet **Flux de contrôle** ).

    ![][02]

2. Double-cliquez sur la tâche de flux de données pour basculer vers l’onglet flux de données.
3. Dans la liste d’autres Sources dans la boîte à outils, faites glisser une Source ADO.NET vers l’aire de conception. Avec la carte source toujours sélectionnée, modifiez son nom pour la **source de SQL Server** dans le volet **Propriétés** .
4. Dans la liste Destinations des autres dans la boîte à outils, faites glisser une Destination ADO.NET vers l’aire de conception sous la Source ADO.NET. Avec la carte de destination toujours sélectionnée, modifiez son nom à **destination DW SQL** dans le volet **Propriétés** .

    ![][09]

## <a name="step-3-configure-the-source-adapter"></a>Étape 3 : Configurer la carte source

1. Double-cliquez sur la carte source pour ouvrir l' **Éditeur de code Source ADO.NET**.

    ![][03]

2. Sous l’onglet **Gestionnaire de connexions** de l' **Éditeur de code Source ADO.NET**, cliquez sur le bouton **Nouveau** en regard de la liste du **Gestionnaire de connexions ADO.NET** pour ouvrir la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** et créer des paramètres de connexion pour la base de données SQL Server à partir de laquelle ce didacticiel charge les données.

    ![][04]

3. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexions** et créer une nouvelle connexion de données.

    ![][05]

4. Dans la boîte de dialogue **Gestionnaire de connexions** , effectuer les opérations suivantes.

    1. **Fournisseur**, sélectionnez le fournisseur de données SqlClient.
    2. **Nom du serveur**, entrez le nom de SQL Server.
    3. Dans la section **Ouvrir une session sur le serveur** , sélectionnez ou entrez des informations d’authentification.
    4. Dans la section **se connecter à une base de données** , sélectionnez la base de données exemple AdventureWorks.
    5. Cliquez sur **tester la connexion**.
    
        ![][06]
    
    6. Dans la boîte de dialogue qui communique les résultats du test de connexion, cliquez sur **OK** pour retourner dans la boîte de dialogue **Gestionnaire de connexions** .
    7. Dans la boîte de dialogue **Gestionnaire de connexions** , cliquez sur **OK** pour retourner dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** .
 
5. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur **OK** pour retourner dans l' **Éditeur de code Source ADO.NET**.
6. Dans l' **Éditeur de code Source ADO.NET**, dans la liste **nom de la table ou la vue** , sélectionnez la table **Sales.SalesOrderDetail** .

    ![][07]

7. Cliquez sur **Aperçu** pour voir les 200 premières lignes de données dans la table source dans la boîte de dialogue **Aperçu des résultats de requête** .

    ![][08]

8. Dans la boîte de dialogue **Aperçu des résultats de requête** , cliquez sur **Fermer** pour revenir à l' **Éditeur de code Source ADO.NET**.
9. Dans l' **Éditeur de code Source ADO.NET**, cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-4-connect-the-source-adapter-to-the-destination-adapter"></a>Étape 4 : Se connecter à la carte source à la carte de destination

1. Sélectionnez la carte source dans l’aire de conception.
2. Cliquez sur la flèche bleue qui s’étend de la carte source et faites-le glisser vers l’éditeur de destination jusqu'à ce qu’elle se mette en place.

    ![][10]

    Dans un package SSIS classique, vous utilisez un nombre d’autres composants de la boîte à outils SSIS en séparant la source et la destination pour restructurer, transformer et nettoyer vos données qui traversent le flux de données SSIS. Pour conserver la plus simple possible de cet exemple, nous allons reliant la source directement à la destination.

## <a name="step-5-configure-the-destination-adapter"></a>Étape 5 : Configurer la carte de destination

1. Double-cliquez sur la carte de destination pour ouvrir l' **Éditeur de Destination ADO.NET**.

    ![][11]

2. Sous l’onglet **Gestionnaire de connexions** de l' **Éditeur de Destination ADO.NET**, cliquez sur le bouton **Nouveau** en regard de la liste du **Gestionnaire de connexions** pour ouvrir la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** et créer des paramètres de connexion pour la base de données SQL Azure Data Warehouse dans laquelle ce didacticiel charge les données.
3. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur le bouton **Nouveau** pour ouvrir la boîte de dialogue **Gestionnaire de connexions** et créer une nouvelle connexion de données.
4. Dans la boîte de dialogue **Gestionnaire de connexions** , effectuer les opérations suivantes.
    1. **Fournisseur**, sélectionnez le fournisseur de données SqlClient.
    2. **Nom du serveur**, entrez le nom de Data Warehouse SQL.
    3. Dans la section **Ouvrir une session sur le serveur** , sélectionnez **l’authentification utiliser SQL Server** , entrez les informations d’authentification.
    4. Dans la section **se connecter à une base de données** , sélectionnez une base de données SQL Data Warehouse existante.
    5. Cliquez sur **tester la connexion**.
    6. Dans la boîte de dialogue qui communique les résultats du test de connexion, cliquez sur **OK** pour retourner dans la boîte de dialogue **Gestionnaire de connexions** .
    7. Dans la boîte de dialogue **Gestionnaire de connexions** , cliquez sur **OK** pour retourner dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** .
5. Dans la boîte de dialogue **Configurer le Gestionnaire de connexion ADO.NET** , cliquez sur **OK** pour retourner dans l' **Éditeur de Destination ADO.NET**.
6. Dans l' **Éditeur de Destination ADO.NET**, cliquez sur **Nouveau** en regard de la liste **utiliser une table ou une vue** pour ouvrir la boîte de dialogue **Créer un tableau** pour créer une nouvelle table de destination avec une liste de colonnes qui correspond à la table source.

    ![][12a]

7. Dans la boîte de dialogue **Créer un tableau** , procédez comme suit.

    1. Modifier le nom de la table de destination **SalesOrderDetail**.
    2. Supprimer la colonne **rowguid** . Le type de données **uniqueidentifier** n’est pas pris en charge dans Data Warehouse SQL.
    3. Modifier le type de données de la colonne **LineTotal** à **argent**. Le type de données **décimal** n’est pas pris en charge dans Data Warehouse SQL. Pour plus d’informations sur les types de données pris en charge, voir [Créer un tableau (entrepôt de données SQL Azure, Parallel Data Warehouse)][].
    
        ![][12b]
    
    4. Cliquez sur **OK** pour créer une table et revenir à l' **Éditeur de Destination ADO.NET**.

8. Dans l' **Éditeur de Destination ADO.NET**, sélectionnez l’onglet **mappages** pour voir comment les colonnes de la source sont mappés aux colonnes dans la destination.

    ![][13]

9. Cliquez sur **OK** pour terminer la configuration de la source de données.

## <a name="step-6-run-the-package-to-load-the-data"></a>Étape 6 : Exécuter le package pour charger les données

Exécuter le package en cliquant sur le bouton **Démarrer** dans la barre d’outils ou en sélectionnant l’une des options de **s’exécuter** dans le menu **Déboguer** .

Lorsque le package commence à exécuter, vous voyez engrenages jaunes pour indiquer l’activité, ainsi que le nombre de lignes traitées jusqu'à présent.

![][14]

Lorsque le package est terminée, vous voyez coche verte pour indiquer la réussite ainsi que le nombre total de lignes de données chargées à partir de la source à la destination.

![][15]

Félicitations ! Vous avez utilisé avec succès SQL Server Integration Services pour charger les données dans le magasin de données SQL Azure.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le flux de données SSIS. Commencez ici : [Flux de données][].
- Découvrez comment déboguer et résoudre les problèmes de vos directement packages dans l’environnement de conception. Commencez ici : [Outils de résolution des problèmes pour le développement de packages][].
- Découvrez comment déployer vos projets SSIS et packages vers Integration Services Server ou vers un autre emplacement de stockage. Commencez ici : [déploiement de projets et des Packages][].

<!-- Image references -->
[01]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-designer-01.png
[02]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ssis-data-flow-task-02.png
[03]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-03.png
[04]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-manager-04.png
[05]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-connection-05.png
[06]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/test-connection-06.png
[07]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-source-07.png
[08]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/preview-data-08.png
[09]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/source-destination-09.png
[10]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/connect-source-destination-10.png
[11]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/ado-net-destination-11.png
[12a]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-before-12a.png
[12b]: ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/destination-query-after-12b.png
[13]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/column-mapping-13.png
[14]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-running-14.png
[15]:  ./media/sql-data-warehouse-load-from-sql-server-with-integration-services/package-success-15.png

<!-- Article references -->

<!-- MSDN references -->
[Guide de PolyBase]: https://msdn.microsoft.com/library/mt143171.aspx
[Télécharger SQL Server Data Tools (SSDT)]: https://msdn.microsoft.com/library/mt204009.aspx
[Création de TABLE (Data Warehouse SQL Azure, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Flux de données]: https://msdn.microsoft.com/library/ms140080.aspx
[Outils de résolution des problèmes pour le développement de packages]: https://msdn.microsoft.com/library/ms137625.aspx
[Déploiement des projets et des Packages]: https://msdn.microsoft.com/library/hh213290.aspx

<!--Other Web references-->
[Services d’intégration Microsoft SQL Server 2016 Feature Pack pour Azure]: http://go.microsoft.com/fwlink/?LinkID=626967
[SQL Server évaluations]: https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016
[Communauté Visual Studio]: https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx
[Bases de données AdventureWorks 2014]: https://msftdbprodsamples.codeplex.com/releases/view/125550
