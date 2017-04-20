<properties 
   pageTitle="Vue d’ensemble de Microsoft Azure données Lake Analytique | Azure" 
   description="Données Lake Analytique est un service de calcul Azure Big Data qui vous permet d’utiliser des données pour votre entreprise à l’aide de l’analyse des acquise de vos données dans le cloud, quelle que soit l’endroit où il est et quelle que soit sa taille. Données Lake Analytique permet cela dans le plus simple, de manière plus scalable et plus économique possible. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="overview-of-microsoft-azure-data-lake-analytics"></a>Vue d’ensemble de Microsoft Azure données Lake Analytique

## <a name="what-is-azure-data-lake-analytics"></a>Qu’est Azure données Lake Analytique ?

Azure données Lake Analytique est un nouveau service, intégré pour analytique de données volumineux plus facilement. Ce service vous pouvez vous concentrer sur écriture, en cours d’exécution et gestion des tâches, plutôt que d’exploitation infrastructure distribuée. Au lieu de déploiement, la configuration et le matériel réglage, vous écrivez des requêtes pour transformer vos données et extraire excellente. Le service d’analytique peut gérer les travaux de n’importe quelle échelle instantanément en définissant simplement la numérotation pour la quantité power que vous avez besoin. Vous payez uniquement pour votre travail lorsqu’elle s’exécute rendant économique. Le service analytique prend en charge Azure Active Directory vous permettant de gérer simplement accès et les rôles, intégrées à votre système d’identité local. Il inclut également U-SQL, une langue qui réunit les avantages de SQL grâce à la puissance expressif du code utilisateur. Format SVG distribué runtime de U-SQL vous permet analyser efficacement les données dans le magasin et sur les serveurs SQL Azure, base de données SQL Azure et SQL Azure Data Warehouse.


## <a name="key-capabilities"></a>Fonctionnalités clés

- **Mise à l’échelle dynamiques** 

    Données Lake Analytique est conçue spécifiquement pour l’échelle de cloud et les performances.  Il dynamiquement dispositions ressources et vous permet d’effectuer analytique sur to ou même exaoctets de données. Lorsque la tâche est terminée, il apprêtez sans ressources automatiquement, et vous payez uniquement la puissance de traitement utilisée. Comme vous augmentez ou diminuez la taille des données stockées ou la quantité de cluster utilisé, vous n’êtes pas obligé de la réécriture de code. Cette vous pouvez vous concentrer sur votre logique métier uniquement et non sur comment traiter et stocker de grands volumes de données. 

- **Développer plus rapidement, déboguer et optimiser plus efficacement à l’aide des outils familiers**

    Données Lake Analytique a une forte intégration avec Visual Studio, afin que vous pouvez utiliser des outils familiers à exécuter, déboguer et régler votre code. Visualisations de vos projets U-SQL vous permettent de voir comment votre code s’exécute à l’échelle, afin que vous pouvez facilement identifier performances engorgements et optimiser les coûts. 

- **U-SQL : simples et familiers, puissante et extensible**

    Données Lake Analytique inclut U-SQL, un langage de requête qui s’étend la nature familière, simple déclarative de SQL grâce à la puissance expressif de c#. Le langage SQL-U est basé sur le même runtime distribué qui se met les systèmes de données volumineux au sein de Microsoft. Des millions de développeurs SQL et .NET peuvent désormais traiter et analyser toutes leurs données ayant les compétences qu’il possède déjà.

- **Intégration transparente avec vos investissements informatiques**

    Données Lake Analytique permet vos investissements informatiques existants d’identité, gestion, sécurité et data warehouse. Cela simplifie la gouvernance des données et facilite l’étendre vos applications de données en cours. Données Lake Analytique est intégré à Active Directory pour les autorisations et gestion des utilisateurs et est fourni avec intégrés de surveillance et d’audit.

- **Économique et rentable**

    Données Lake Analytique est une solution économique pour l’exécution de charges de travail de données volumineux. Vous payez par projet lorsque les données sont traitées. Aucun matériel, licences ou contrats de support spécifiques au service sont requis. Le système s’ajuste automatiquement vers le haut ou vers le bas que la tâche démarre et se termine, ce qui signifie que vous payez jamais pour plus d’informations que vous avez besoin. 

- **Fonctionne avec toutes vos données Azure**

    Données Lake Analytique pouvez travailler avec un nombre de sources de données Azure : stockage d’objets Blob Azure, base de données SQL Azure et bien entendu données Lake Analytique est spécialement optimisé pour travailler avec Azure données Lake Store - assurant le niveau le plus élevé de performances, débit et parallélisation pour vous charges de données volumineux.

## <a name="see-also"></a>Voir aussi

- Prise en main
    - [Prise en main données Lake Analytique à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
    - [Prise en main données Lake Analytique à l’aide de PowerShell Azure](data-lake-analytics-get-started-powershell.md)
    - [Prise en main données Lake Analytique à l’aide du Kit de développement .NET Azure](data-lake-analytics-get-started-net-sdk.md)
    - [Développer des scripts U-SQL à l’aide de Lake Data Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
    - [Prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
    
- Développement et U-SQL
    - [Prise en main langue données Lake Analytique U-SQL Azure](data-lake-analytics-u-sql-get-started.md)
    - [Utiliser les fonctions de fenêtre U-SQL pour les tâches de Azure données Lake Analytique](data-lake-analytics-use-window-functions.md)
    - [Développer des opérateurs U-SQL sont définis par l’utilisateur pour les tâches de données Lake Analytique](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- Gestion des
    - [Gérer les Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md)
    - [Gérer les Azure données Lake Analytique à l’aide de PowerShell Azure](data-lake-analytics-manage-use-powershell.md)
    - [Surveiller et résoudre les problèmes de travaux Azure données Lake Analytique à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [Accès aux journaux de Diagnostics pour Azure données Lake Analytique](data-lake-analytics-diagnostic-logs.md)

- Didacticiel de bout en bout
    - [Utiliser les didacticiels interactifs Azure données Lake Analytique](data-lake-analytics-use-interactive-tutorials.md)
    - [Analyser les journaux de site Web à l’aide d’Azure données Lake Analytique](data-lake-analytics-analyze-weblogs.md)

- Dites-nous ce que vous pensez
    - [Commentaires sur notre file d’attente documentation](data-lake-analytics-documentation-backlog.md)
    - [Envoyez une demande de fonctionnalité](http://aka.ms/adlafeedback)
    - [Obtenir de l’aide dans les forums](http://aka.ms/adlaforums)


