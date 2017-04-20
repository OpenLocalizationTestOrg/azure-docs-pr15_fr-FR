<properties
    pageTitle="Quand utiliser NoSQL vs SQL | Microsoft Azure"
    description="Comparer les avantages de l’utilisation des solutions non relationnelles NoSQL par rapport aux solutions SQL. Découvrez si un des services Microsoft Azure NoSQL ou SQL Server best s’adapte à votre scénario."
    keywords="NoSQL vs sql, quand utiliser NoSQL, sql vs nosql"
    services="documentdb"
    documentationCenter=""
    authors="mimig1"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="documentdb"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="06/24/2016"
    ms.author="mimig"/>

# <a name="nosql-vs-sql"></a>NoSQL vs SQL

SQL Server et les bases de données relationnelles (SGBDR) ont été les bases de données à atteindre pour plus de 20 ans. Toutefois, qu’il soit nécessaire pour traiter une augmentation des volumes et des types de données à un rythme rapide accrue a modifié la nature des besoins de stockage de données pour les développeurs d’applications. Pour activer ce scénario, les bases de données NoSQL activer le stockage de données hétérogènes et non structurées à l’échelle ont acquise dans popularité. 

NoSQL est une catégorie de bases de données très différents à partir de bases de données SQL. NoSQL est souvent utilisé pour faire référence à des systèmes de gestion des données qui sont « Pas SQL » ou une approche de gestion des données incluant « ne pas SQL ». Il existe un certain nombre de technologies d’assistance dans la catégorie NoSQL, y compris les document bases de données, valeur clé stores, stores famille de colonne et graph, qui sont les plus consultés avec des jeux, mise en réseau, et les applications IoT.

![Diagramme de présentation NoSQL vs SQL montrant des modèles de données et les scénarios courants](./media/documentdb-nosql-vs-sql/nosql-vs-sql-overview.png)

L’objectif de cet article est pour vous aider à en savoir plus sur les différences entre NoSQL et SQL et vous fournir une introduction pour les offres NoSQL et SQL de Microsoft.  

## <a name="when-to-use-nosql"></a>Quand utiliser NoSQL ?

Imaginons que vous créez un nouveau site social engagement. Les utilisateurs peuvent créer des billets et leur ajouter des images, de vidéos et de musique. D’autres utilisateurs peuvent commenter les publications et offrent des points (J’aime) pour évaluer les publications. La page d’accueil ont un flux de publications que les utilisateurs peuvent partager et interagir avec. 

Comment vous stockez ces données ? Si vous avez l’habitude avec SQL, vous pouvez commencer à dessiner ressembler à ceci :

![NoSQL vs SQL de diagramme montrant le modèle de données relationnelles pour un site social engagement](./media/documentdb-nosql-vs-sql/nosql-vs-sql-social.png)

Jusque, tout va bien, mais maintenant réfléchissez à la structure d’un billet unique et comment l’afficher. Si vous voulez afficher le billet et les images associées, audio, vidéo, commentaires, points et informations utilisateur sur un site Web ou une application, vous devez exécuter une requête avec des jointures table huit simplement pour extraire le contenu. Imaginons un flux de billets dynamiquement charger qui apparaissent sur l’écran et vous pouvez facilement prévoir qu’il s’afin d’exiger des milliers de requêtes et de jointures pour effectuer cette tâche.

Maintenant que vous pouvez utiliser une solution relationnelle comme SQL Server pour stocker les données - mais il existe une autre option, une option NoSQL qui simplifie l’approche. En transformant le billet dans un document JSON l’exemple suivant et le stockage dans DocumentDB, un service de base de données Azure NoSQL document, vous pouvez améliorer les performances et récupérer le billet entier avec une requête et aucune jointure. Il est plus simple et plus simple et plus performant à l’origine.

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

En outre, ces données peuvent être partitionnées par id billet permettant d’évoluer naturellement et tirer parti de caractéristiques d’échelle NoSQL des données. Systèmes NoSQL permettent également les développeurs Desserrez la cohérence et de proposer des applications hautement disponibles.  Enfin, cette solution ne nécessite pas les développeurs à définir, gérer et conserver le schéma dans la couche de données permettant d’itération rapide.

Vous pouvez ensuite créer cette solution à l’aide d’autres services Azure :

- [Recherche Azure](https://azure.microsoft.com/services/search/) peuvent servir via l’application web pour permettre aux utilisateurs rechercher des billets.
- [Services d’application Azure](https://azure.microsoft.com/services/app-service/) peuvent être utilisés pour héberger les applications et processus en arrière-plan.
- [Stockage d’objets Blob Azure](https://azure.microsoft.com/services/storage/) peuvent servir à stocker des profils utilisateur complet y compris les images.
- [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/) peut servir à stocker des volumes importants de données telles que les informations de connexion et les données d’analytique de l’utilisation.
- [Apprentissage automatique Azure](https://azure.microsoft.com/services/machine-learning/) peuvent servir à générer des connaissances et intelligence qui peut fournir des commentaires au processus et fournir le contenu approprié aux utilisateurs appropriés.

Ce site social engagement est seul un scénario dans lequel une base de données NoSQL est le modèle de données approprié pour la tâche. Si vous êtes intéressé lecture en savoir plus sur ce scénario et comment modéliser vos données pour DocumentDB dans les applications de médias sociaux, voir [accédant mise en réseau avec DocumentDB](documentdb-social-media-apps.md). 

## <a name="nosql-vs-sql-comparison"></a>Comparaison de NoSQL vs SQL

Le tableau suivant compare les principales différences entre NoSQL et SQL. 

![NoSQL vs SQL de diagramme montrant quand utiliser NoSQL et quand utiliser SQL. Comparaison de NoSQL vs SQL](./media/documentdb-nosql-vs-sql/nosql-vs-sql-comparison.png)

Si une base de données NoSQL meilleures mieux à vos besoins, passez à la section suivante pour en savoir plus sur les services NoSQL disponibles à partir d’Azure. Dans le cas contraire, si une base de données SQL meilleures adapté à vos besoins, passez à le [Quelles sont les offres Microsoft SQL ?](#what-are-the-microsoft-sql-offerings)

## <a name="what-are-the-microsoft-azure-nosql-offerings"></a>Quelles sont les offres de Microsoft Azure NoSQL ?

Azure comprend quatre entièrement gérés NoSQL services suivants : 

- [DocumentDB Azure](https://azure.microsoft.com/services/documentdb/)
- [Stockage de Table Azure](https://azure.microsoft.com/services/storage/)
- [Azure HBase dans le cadre de HDInsight](https://azure.microsoft.com/services/hdinsight/)
- [Cache Redis Azure](https://azure.microsoft.com/services/cache/)

Le graphique de comparaison suivant mappe les différences clés pour chaque service. Celui décrit plus précisément les besoins de votre application ? 

![NoSQL vs SQL de diagramme montrant quand utiliser les offres NoSQL à partir de Microsoft Azure, y compris les DocumentDB, le stockage de Table HBase dans le cadre de HDInsight et Redis Cache](./media/documentdb-nosql-vs-sql/nosql-vs-sql-documentdb-storage-hbase-hdinsight-redis-cache.png)

Si un ou plusieurs de ces services peuvent répondre aux besoins de votre application, en apprendre davantage avec les ressources suivantes : 

- [Rubriques d’apprentissage DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) et [exemples d’utilisation DocumentDB](documentdb-use-cases.md)
- [Prise en main stockage de table Azure](../storage/storage-dotnet-how-to-use-tables.md)
- [Qu’est HBase dans HDInsight](../hdinsight/hdinsight-hbase-overview.md)
- [Redis Cache rubriques d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/redis-cache/)

Pour plus d’informations d’évaluation gratuites puis passez aux [étapes suivantes](#next-steps) .

## <a name="what-are-the-microsoft-sql-offerings"></a>Quelles sont les offres Microsoft SQL ?

Microsoft comporte cinq offres SQL : 

- [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [SQL Server sur Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)
- [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
- [Magasin de données SQL Azure (Preview)](https://azure.microsoft.com/services/sql-data-warehouse/)
- [Système de plateforme Analytique (solution en local)](https://www.microsoft.com/en-us/server-cloud/products/analytics-platform-system/)

Si vous êtes intéressé dans SQL Server sur un ordinateur virtuel ou d’une base de données SQL, puis lisez [Choisissez un option de SQL Server cloud : base de données SQL Azure (PaaS) ou SQL Server sur Azure machines virtuelles (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) pour en savoir plus sur les différences entre les deux.

Si SQL semble être la meilleure option, puis accédez à [SQL Server](https://www.microsoft.com/server-cloud/products/) pour plus d’informations sur nos produits Microsoft SQL et services qu’à proposer.

Puis passez aux [étapes suivantes](#next-steps) gratuitement des liens d’évaluation et d’évaluation.

## <a name="next-steps"></a>Étapes suivantes

Nous vous invitons à en savoir plus sur nos produits SQL et NoSQL par essayer gratuitement. 

- Pour tous les services Azure, vous pouvez vous inscrire une [version d’évaluation d’un mois gratuite](https://azure.microsoft.com/pricing/free-trial/) et recevoir 200 $ dépenser sur n’importe lequel des services Azure.
    - [DocumentDB Azure](https://azure.microsoft.com/services/documentdb/)
    - [Azure HBase dans le cadre de HDInsight](https://azure.microsoft.com/services/hdinsight/)
    - [Cache Redis Azure](https://azure.microsoft.com/services/cache/)
    - [Magasin de données SQL Azure (Preview)](https://azure.microsoft.com/services/sql-data-warehouse/)
    - [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
    - [Stockage de Table Azure](https://azure.microsoft.com/services/storage/)

- Vous pouvez tourner une [version d’évaluation de SQL Server 2016 sur une machine virtuelle](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016ctp33evaluationwindowsserver2012r2/) ou télécharger une [version d’évaluation de SQL Server](https://www.microsoft.com/en-us/evalcenter/evaluate-sql-server-2016).
    - [SQL Server](https://www.microsoft.com/server-cloud/products/sql-server-2016/)
    - [SQL Server sur Machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)

