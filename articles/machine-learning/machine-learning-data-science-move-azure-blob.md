<properties
    pageTitle="Déplacer des données vers et depuis le stockage Blob Azure | Microsoft Azure"
    description="Déplacer des données vers et depuis le stockage Blob Azure"
    services="machine-learning,storage"
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
    ms.date="09/14/2016"
    ms.author="bradsev;sachouks" />

# <a name="move-data-to-and-from-azure-blob-storage"></a>Déplacer des données vers et depuis le stockage Blob Azure

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

Conseils sur les technologies utilisées pour déplacer des données à portée de main et/ou depuis le stockage Blob Azure sont liées ici :

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]
 
Quelle méthode est préférable dépend de votre scénario. L’article [scénarios d’analytique avancée dans l’apprentissage automatique Azure](machine-learning-data-science-plan-sample-scenarios.md) permet de déterminer les ressources que nécessaires pour une variété de flux de travail scientifique données utilisées dans le processus analytique avancées.

> [AZURE.NOTE] Pour une présentation complète de stockage d’objets blob Azure, reportez-vous à [Concepts de base Blob Azure](../storage/storage-dotnet-how-to-use-blobs.md) et au [Service d’objets Blob Azure](https://msdn.microsoft.com/library/azure/dd179376.aspx).

Comme alternative, vous pouvez utiliser [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) à : 

- créer et planifier un pipeline qui télécharge des données à partir du stockage blob Azure, 
- passer à un service web apprentissage automatique Azure publié, 
- recevoir les résultats prédictive analytique, et 
- Télécharger les résultats au stockage. 

Pour plus d’informations, voir [créer pipelines prédictives à l’aide de Azure Data Factory et apprentissage automatique Azure](../data-factory/data-factory-azure-ml-batch-execution-activity.md).

## <a name="prerequisites"></a>Conditions préalables

Ce document suppose que vous avez un abonnement Azure, un compte de stockage et la clé de stockage correspondante pour ce compte. Avant de télécharger/télécharger des données, vous devez connaître votre clé de compte et nom de compte stockage Azure.

- Pour configurer un abonnement Azure, consultez la [version d’évaluation gratuite d’un mois](https://azure.microsoft.com/pricing/free-trial/).
- Pour obtenir des instructions sur la création d’un compte de stockage et pour obtenir des informations de compte et clées, voir [comptes de stockage sur Azure](../storage/storage-create-storage-account.md).
