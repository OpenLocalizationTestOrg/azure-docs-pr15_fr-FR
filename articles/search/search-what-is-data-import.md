<properties
    pageTitle="Téléchargement des données dans Azure recherche | Microsoft Azure | Service de recherche cloud hébergé"
    description="Découvrez comment télécharger des données dans un index de recherche Azure."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager="jhubbard"
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="ashmaka"/>

# <a name="upload-data-to-azure-search"></a>Télécharger des données à la recherche d’Azure
> [AZURE.SELECTOR]
- [Vue d’ensemble](search-what-is-data-import.md)
- [.NET](search-import-data-dotnet.md)
- [RESTE](search-import-data-rest-api.md)


## <a name="data-upload-models-in-azure-search"></a>Modèles d’Azure recherche du téléchargement des données
Il existe deux manières de remplir votre index de recherche Azure avec vos données. La première option est reçue manuellement vos données dans l’index à l’aide de la recherche Azure [API REST](search-import-data-rest-api.md) ou le [Kit de développement .NET](search-import-data-dotnet.md). La deuxième option consiste à [pointez sur une source de données pris en charge](search-indexer-overview.md) pour votre index de recherche Azure et laisser Azure recherche extraire automatiquement vos données dans le service de recherche.

Ce guide couvre uniquement des instructions sur l’utilisation du modèle push de téléchargement de données (qui est pris en charge uniquement dans [l’API REST](search-import-data-rest-api.md) et le [Kit de développement .NET](search-import-data-dotnet.md)), mais vous pouvez toujours plus d’informations sur le modèle d’extraction ci-dessous.

### <a name="push-data-to-an-index"></a>Transmission des données pour un index

Cette approche fait référence à l’envoi par programme vos données à Azure recherche pour le rendre disponible pour la recherche. Pour les applications présentant des exigences de latence très faible (par exemple, si vous avez besoin d’effectuer une recherche opérations pour être synchronisé avec les bases de données stock dynamique), le modèle push est la seule option disponible.

Vous pouvez utiliser [l’API REST](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou le [Kit de développement .NET](search-import-data-dotnet.md) à push des données pour un index. Il n’existe actuellement aucune prise en charge de l’outil de diffusion de données via le portail.

Cette approche est plus flexible que le modèle d’extraction, car vous pouvez télécharger des documents individuellement ou en lots (jusqu'à 1000 par lot ou 16 Mo, quelle que soit la limite vient en premier). Le modèle push vous permet également de télécharger des documents à la recherche Azure quelle que soit l’endroit où vos données se trouvent.

### <a name="pull-data-into-an-index"></a>Extraire des données dans un index

Le modèle d’extraction analyse une source de données pris en charge et télécharge automatiquement les données dans l’index de recherche Azure vous pour vous. Grâce au suivi des modifications et les suppressions à des documents existants en plus de reconnaissance d’un nouveau document, indexeurs supprimer la nécessité de gérer activement les données dans votre index.

Dans Rechercher Azure, cette fonctionnalité est implémentée par *indexeurs*, actuellement disponibles pour le [stockage d’objets Blob (preview)](search-howto-indexing-azure-blob-storage.md), [DocumentDB](http://aka.ms/documentdb-search-indexer), [base de données SQL Azure et SQL Server sur machines virtuelles Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md).

La fonctionnalité indexeur est indiquée dans le [Portail Azure](search-import-data-portal.md) également comme dans l' [API REST](https://msdn.microsoft.com/library/azure/dn946891.aspx).
