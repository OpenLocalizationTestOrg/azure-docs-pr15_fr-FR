<properties 
    pageTitle="Prise en main des outils de base de données élastique" 
    description="Principes fondamentaux de fonctionnalité d’outils de base de données élastique de base de données SQL Azure, y compris facile pour exécuter l’exemple d’application." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor="CarlRabeler"/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="get-started-with-elastic-database-tools"></a>Prise en main des outils de base de données élastique

Ce document vous présente l’expérience de développement en exécutant l’exemple d’application. L’exemple crée une application sharded simple et présente les principales fonctionnalités des outils de base de données élastique. Cet exemple illustre les fonctions de la [bibliothèque de client de base de données élastique](sql-database-elastic-database-client-library.md)

Pour installer la bibliothèque, accédez à [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Notez que la bibliothèque est installée avec l’application d’exemple ci-dessous.

## <a name="prerequisites"></a>Conditions préalables

1. Visual Studio 2012 ou une version ultérieure avec c# est requis. Télécharger une version gratuite à [Téléchargements de Visual Studio](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
2. NuGet 2.7 ou version ultérieure. Pour obtenir la dernière version, voir [Installer NuGet](http://docs.nuget.org/docs/start-here/installing-nuget)

## <a name="download-and-run-the-sample-app"></a>Téléchargez et exécutez l’application exemple

Le **élastique base de données avec SQL Azure, mise en route** application exemple illustre les aspects les plus importantes de l’expérience de développement pour les applications sharded à l’aide des outils de base de données élastique SQL Azure. Il se concentre sur les exemples d’utilisation pour [Éclater mapper gestion](sql-database-elastic-scale-shard-map-management.md), [routage dépendant des données](sql-database-elastic-scale-data-dependent-routing.md) et [l’interrogation éclater multiples](sql-database-elastic-scale-multishard-querying.md). Pour télécharger et exécuter l’exemple, procédez comme suit : 

1. Ouvrez Visual Studio et sélectionnez **fichier -> Nouveau -> projet**.
2. Dans la boîte de dialogue, cliquez sur **en ligne**.

    ![Nouveau projet > en ligne][2]
3. Cliquez ensuite sur **c#** sous **exemples**.

    ![Cliquez sur Visual C#][3]
4. Dans la zone Rechercher, tapez **db élastique** pour rechercher l’échantillon. Le titre **Élastique outils DB pour SQL Azure - mise en route** apparaît.

    ![Zone de recherche][1]
 
5. Sélectionnez l’échantillon, choisissez un nom et un emplacement pour le nouveau projet, puis appuyez sur **OK** pour créer le projet.
6. Ouvrez le fichier **app.config** dans la solution pour l’exemple de projet et suivez les instructions dans le fichier pour ajouter le nom de votre serveur de base de données SQL Azure et vos informations de connexion (nom d’utilisateur et mot de passe).
7. Créer et exécuter l’application. Lorsque vous êtes invité, patientez Visual Studio restaurer les packages NuGet de la solution. Cela sera Téléchargez la dernière version de la bibliothèque de client élastique de base de données à partir de NuGet.
8. Jouer avec les différentes options pour en savoir plus sur les fonctions de bibliothèque client. Notez les étapes de que l’application vous permet d’accéder dans la console de sortie et n’hésitez pas à Explorer le code derrière les scènes.

    ![avancement][4]

Félicitations : vous avez correctement créé et exécuté votre première application sharded à l’aide des outils de base de données élastique sur base de données SQL Azure. Consultez rapide au milieu des fragments que l’échantillon créé en vous connectant avec Visual Studio ou SQL Server Management Studio sur votre serveur de base de données Azure. Vous remarquerez nouvelles bases de données exemple partagé et une base de données du Gestionnaire de carte éclater l’échantillon a créé.

> [AZURE.IMPORTANT] Il est recommandé d’utiliser toujours la dernière version de Management Studio doit pour rester synchronisé avec les mises à jour Microsoft Azure et base de données SQL. [Mettre à jour de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


### <a name="key-pieces-of-the-code-sample"></a>L’exemple de code essentielles

1. **Milieu des fragments gestion et cartes éclater**: le code montre comment travailler avec milieu des fragments, des plages, et dans les mappages de fichiers **ShardMapManagerSample.cs**. Vous pouvez trouver plus d’informations sur cette rubrique ici : [Gestion de carte partagé](http://go.microsoft.com/?linkid=9862595).  
2. **Routage dépendant des données**: routage des transactions vers la droite éclater est présentée dans **DataDependentRoutingSample.cs**. Pour plus d’informations, voir [Routage dépendant des données](http://go.microsoft.com/?linkid=9862596). 
3. **Requête sur plusieurs milieu des fragments**: interrogation sur milieu des fragments est illustrée dans le fichier **MultiShardQuerySample.cs**. Pour plus d’informations, voir [Partagé à plusieurs requêtes](http://go.microsoft.com/?linkid=9862597).
4. **Ajout vide milieu des fragments**: l’ajout itératif de nouveau milieu des fragments vides est exécutée par le code dans un fichier **AddNewShardsSample.cs**. Détails de cette rubrique sont décrites ici : [Gestion de carte partagé](http://go.microsoft.com/?linkid=9862595).

### <a name="other-elastic-scale-operations"></a>Autres opérations d’échelle élastique

1. **Fractionnement d’une éclater existant**: la possibilité de fractionner milieu des fragments est fournie via l' **outil de fusion de fractionnement**. Vous pouvez trouver plus d’informations sur cet outil ici : [Présentation de l’outil de fusion et fractionner](sql-database-elastic-scale-overview-split-and-merge.md).
2. **Fusion de milieu des fragments existants**: fusions éclater sont également effectuées à l’aide de l' **outil de fusion de fractionnement**. Pour plus d’informations, reportez-vous à : [Présentation de l’outil de fusion et fractionner](sql-database-elastic-scale-overview-split-and-merge.md).   


## <a name="cost"></a>Coût

Les outils de base de données élastique sont gratuites. Outils de base de données élastique n’impose des frais supplémentaires sur le coût d’enrichir le Azure. 

Par exemple, l’exemple d’application crée des nouvelles bases de données. Le coût varie selon l’édition de base de données de base de données SQL Azure choisie et l’utilisation de votre application Azure.

Informations de tarification voir [Détails sur tarification pour la base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les outils de base de données élastique, voir :

* [Outils de base de données élastique Explorateur de documents](https://azure.microsoft.com/documentation/learning-paths/sql-database-elastic-scale/) 
-    Exemples de code : 
    -    [DB élastique avec SQL Azure - mise en route](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE)
    -    [DB élastique avec SQL Azure - intégrant entité Framework](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
    -    [Éclater élasticité sur Centre de scripts](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
-    Blog : [échelle élastique annonce](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
-    Canal 9 : [échelle élastique aperçu vidéo](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
-    Forum de discussion : [forum de la base de données SQL Azure](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
-    Mesurer les performances : [compteurs pour le Gestionnaire de carte partagé](sql-database-elastic-database-client-library.md)


<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
 
